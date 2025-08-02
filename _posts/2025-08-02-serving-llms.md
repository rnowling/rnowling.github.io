---
layout: post
title:  "Serving LLMs"
date:   2025-07-29 10:13:19
categories: "software-engineering"
tags: ["education", "ai", "llm", "cs-education", "ampere"]
---

In a recent series of posts, I've been documenting explorations with AI coding agents. In parts [II](/software-engineering/2025/07/24/ai-programming-assistants-part-ii.html), -
[IV](/software-engineering/2025/07/26/ai-programming-assistants-part-iv.html), I looked at prompting
Google Gemini Pro to create a roguelike RPG game.  In [part V](/software-engineering/2025/07/27/ai-programming-assistants-part-v.html),
I ran the prompts through Microsoft Copilot, OpenAI's ChatGPT, and Anthropic's Claude.  In the
[most recent post](/software-engineering/2025/08/02/ai-programming-assistants-part-vi.html), I looked at using Claude Code
to perform software maintenance tasks on some [bioinformatics software](https://github.com/nowling-lab/asaph) I developed.

In this post, I'm going to look at the serving side of LLMs. My plan is to develop a course focused on software development
with LLMs.  I plan to cover both AI-assisted programming but also developing software that uses LLMs to implement features.
I'll need to be able to support 24 - 30 students making parallel requests to LLMs and possibly need to make different models
available in parallel.

The pricing models of cloud LLM services are not structurally conducive for use in a class.  While providers offer free student
plans, the plans are tied to individual accounts with a maximum amount of free credit for a limited amount of time.  If a
student signs up for the service on their own, they may have used up both before taking the class.  Additionally, universities
tend to prefer one-time expenditures rather than recurring expenses. In other words, it's not easy to get resources for the class.

[Milwaukee School of Engineering](https://www.msoe.edu/) is fortunate to have substantial on-premise hardware resources thanks
to vendors like Nvidia and Ampere and private donors. Our GPU cluster [Rosie](https://www.msoe.edu/about-msoe/news/details/rosie-msoe-s-supercomputer-gets-major-update/)
has a handful of Nvidia DGX nodes with state-of-the-art Nvidia GPUs as well as a larger number of nodes with more modest GPUs.
We also have an Ampere-powered server that was recently upgraded to 256 cores thanks to a generous donation from Ampere. We use
that server to host virtual machines for students in our [CSC 6605 ML Production Systems](catalog.msoe.edu/preview_course.php?catoid=43&coid=46068&print)
(described in [this paper](https://ieeexplore.ieee.org/abstract/document/10609876)) course.

I've been considering solutions for the course I'm developing.  Our Nvidia DGX nodes are generally best suited for running the
largest LLMs (e.g., ~70 billion parameters) and training models.  We don't want to tie those systems up for the class. Thankfully,
we can get away with using smaller models (e.g., < 10 billion paramaters) for the class.  I've been testing LLM serving options
on my [System76 Thelio Astra](https://system76.com/desktops/thelio-astra-a1.1-n1/configure) with a 128-core Ampere Alta Max and
512 GB of RAM.

I'll admit that I had a little bit of a rough entry wrapping my head around some of the deployment operations.  A couple of
Ampere engineers ([Jan Gryzbek](https://github.com/jan-grzybek-ampere) and [Daniel Kupniki](https://github.com/dkupnicki)) were generous
with their time in answering my questions to help me get started. It turns out that Ampere provides a version of [llama.cpp](https://github.com/AmpereComputingAI/llama.cpp)
with the included llama-server that is optimized for the Ampere architecture and [collections of models](https://huggingface.co/AmpereComputing)
on HuggingFace that are prepared using new quantization methods Q4_K_4 and Q8R16 that particularly beneficial for the Ampere architecture.

Here, I'm going to document some practical things I've learned.

## Llama-server
The llama-server binary runs a REST service for remote access to LLMs and handles loading model weights and performing model computations.
It has an overwhelming number of command-line options, many of which require some knowledge to use.  I ended up running the server
like is:

```bash
./llama-server --host 0.0.0.0 --model ~/llama-models/llama-3.2-3b-instruct-Q8R16.gguf --flash-attn --ctx-size 0 --threads 16
```

* `host`: By default, llama-server listens on 127.0.0.1 and localhost.  To access it over the network, you can either specify
  a specific public IP or the special `0.0.0.0` for listening on all IPs.
* `model`: Somewhat self-explanatory.  It specifies the path to the model file.
* `flash-attn`: Enable the [flash attention](https://arxiv.org/abs/2205.14135) mechanism to reduce computation time and memory usage.
* `ctx-size`: The context size of the model determines the amount of input the model can process. The default context size for llama-server
  is 4k but many models offer longer context windows like 128k. You can either set the context size specifically for your model
  (e.g., 4096 or 131072) or have llama-server use the context size specified in the model with the special value 0.
* `threads`: Also somewhat self-explanatory.  Like many problems in parallelization, communication overhead and waiting can negate
  the benefits of using more threads.  For example, I got better generation performance with llama-3.2:3b-instruct with 16 threads
  than 128 threads.  (Although it is quite fun to see all 128 cores being used.  :) )

## Llama-api-python
The Python [llama-api-client](https://github.com/meta-llama/llama-api-python) provides an API for generating the REST calls.
I used it to implement a script to run the series of prompts for creating and modifying the roguelike game from previous posts
(see below). The API isn't particularly well documented (is it documented at all?), so I spent a little time splunking through
the code.  A couple notes:

* `api_key`: For a basic llama-server instance the API key can be anything.  I used the string `"none"`.
* `base_url`: A URL string in the format `http://host:port/`.  Defaults to assuming the server is running on 127.0.0.1
* `timeout`: Instance of `httpx.Timeout` used to specify the timeouts until a connection is established and
  and for a response to a REST request.  The default is 60 seconds.  I found, however, that prompts with quite a bit
  of context from previous interactions can take a few minutes to respond.  I increased the timeout to 10 minutes
  just to be safe.

## Conversation History
One of my initial questions going into this was how to LLMs process continue interactions if they are stateless.
The solution was easier than I anticipated.  The REST API formats messages to and from the LLM as JSON objects.
The `role` field can be values like `user` (you), `assistant` (the LLM), and `tool` (an external tool). The `content`
field contains the prompt or response as text. A message sent to the LLM might look like this:

```json
{
	"content" : "why is the sky blue?",
	"role" : "user"
}
```

while a response might look like this:

```json
{
	"content" : "I don't know.",
	"role" : "assistant"
}
```

To continue a conversation, pass the sequence of messages sent back and forth with the newest prompt at the end.
(I found the [chat with history example](https://github.com/ollama/ollama-python/blob/main/examples/chat-with-history.py)
from the ollama-python library helpful for figuring this out.)

It should be noted that the generation time grows as the conversation becomes longer. I've read here and there about
a strategy in which you summarize chat histories periodically and send the summaries instead of the original messages.
This can reduce processing time and costs. I need to look into that further in the future.

## Reflections
I'm really happy to see this progress. I'm slowly starting to understand the practical details. As an academic, I'm used
to reading papers about the algorithms but that is rarely sufficient to use these tools in practice. (Hmm... sounds like
there is need for a class on this. :) ).  Fortunately, most if it isn't difficult conceptually -- just not well documented.
Once I started wrapping my head around some of the patterns, I understood them relatively quickly. That said, I greatly
appreciate the help from Jan and Daniel -- their help allowed me to make much faster progress.

On the serving side, I need to better understand how batching works.  In theory, batching should allow the LLM server
to handle a batch of prompts at once to increase overall throughput.  I'm not sure if that means multiple prompts
in a single REST request or from independent REST requests.  I need to figure out the details to configure llama-server
and set up a client to test it to simulate what it might look like with a class fo 24-30 students accessing the service
at the same time.

## Appendix
Here is the Python script I wrote for the roguelike game prompts:

```python
import argparse

import httpx
from llama_api_client import LlamaAPIClient

PROMPTS = [
    """I want to create a text-based role-playing game. Please generate a Python program that generates a map with borders displayed on the screen with stars and the user’s character displayed with an "@" symbol. The user should be able to move the character up, down, left, or right one space at a time by entering the letters w, s, a, and d. The user can enter the letter q to quit.  The character should not be able to cross the map’s borders. The map should be no larger than 80 by 25 characters.""",
    """Please change the game logic so that the map can be larger than 80 by 25. The game should only show a 80 by 25 region of the map on the console with the user’s character in the center. When the user moves, the displayed region of the map should be updated.""",
    """Please divide the map into rooms. Each room should have 1 or 2 entryways. The rooms should be connected by hallways.""",
    """Add a quantitative attribute to the player’s character called energy. It’s minimum value is 0, while it’s maximum value is 100. When the game starts, the character’s energy should be initialized to 100. After every turn, the energy should go down by 1 point. If the character’s energy reaches 0, the game is over, and the character has lost.""",
    """Add objects called batteries to the game. The battery objects should be displayed using the character "b". The game should start with 20 battery objects. The batteries should should be randomly distributed across the map inside the rooms. When the character’s position matches the position of a battery object, the character’s energy should be increased to the maximum amount (100) and the battery object should be removed from the game.""",
    """Add objects called parts to the game. The battery objects should be displayed using the character "p". The game should start with 5 parts objects. The parts should should be randomly distributed across the map inside the rooms. When the character’s position matches the position of a part, the character picks up the part, and the part should be removed from the game. When the character has collected all 5 parts, the rate at which the character’s energy drains should be decreased from 1 unit per move to 1 unit every 10 moves. In addition, display the message "Congratulations! You used the parts to fix your electrical short. Your energy level is no longer going to drain so quickly." """,
    """Display a splash screen when the program starts that tells the player the background story for the game. The story should include the following elements:

        * The character is a robot
        * The robot woke up in an unknown place, not knowing how it got there
        * The robot notices that it has an electrical short. It’s energy is draining quickly.
        * The robot needs to find parts to fix itself with.
        * Thankfully, the robot seems to be in a workshop with ample parts and batteries.

    After the user presses enter, the program should start the game."""]

def run_query(client, messages):
    response = client.chat.completions.create(
        model=args.model,
        messages=messages)

    print(response.choices[0]["message"]["content"])
    print()

    return response.choices[0]["message"]

def parseargs():
    parser = argparse.ArgumentParser()

    parser.add_argument("--model",
                        type=str,
                        required=True,
                        choices=["llama-3.1-8b-instruct", "llama-3.2-3b-instruct"])

    parser.add_argument("--host",
                        type=str,
                        required=True)

    parser.add_argument("--port",
                        type=int,
                        default=8080)

    parser.add_argument("--timeout",
                        type=int,
                        default=600)

    return parser.parse_args()

if __name__ == "__main__":
    args = parseargs()

    host = f"http://{args.host}:{args.port}"
    timeout = httpx.Timeout(timeout=args.timeout, connect=5.0)
    client = LlamaAPIClient(base_url=host, api_key="none", timeout=timeout)

    messages = []
    for prompt in PROMPTS:
        messages.append({
            "role" : "user",
            "content" : prompt
        })

        print("prompt:", prompt)
        print()

        response_msg = run_query(client, messages)

        messages.append(response_msg)
```
