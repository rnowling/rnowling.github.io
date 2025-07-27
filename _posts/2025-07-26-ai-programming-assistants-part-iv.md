---
layout: post
title:  "AI-Assisted Software Development with LLMs: Part IV"
date:   2025-07-26 10:13:19
categories: "software-engineering"
tags: ["education", "ai", "llm", "cs-education"]
---

(This post is a continuation of a series I started previously.  See parts I, II, and III
[here](/software-engineering/2025/07/23/ai-programming-assistants-part-i.html), 
[here](/software-engineering/2025/07/24/ai-programming-assistants-part-ii.html), and
[here](/software-engineering/2025/07/23/ai-programming-assistants-part-iii.html).)

I wanted to reach a reasonable point at which to stop developing the game.  I wanted to
leave students with something intriguing and complete enough that it captures their interest
and sucks them in but with a wide open space of possible directions to extend.  I settled on
the following changes and additions:

## Change Battery Recharge Amount
When playing the game, I found it was too hard to stay alive if the batteries only increased
the energy by a small amount.  I decided to go to the extreme and have batteries restore
the character's energy to its maximum level.

I accomplished this by modifying my previous prompt and using the "redo" button to re-run
the model.  The modified prompt is as so:

> Add objects called batteries to the game. The battery objects should be displayed using the character "b". The game should start with 20 battery objects. The batteries should should be randomly distributed across the map inside the rooms. When the character's position matches the position of a battery object, the character's energy should be increased to the maximum amount (100) and the battery object should be removed from the game.

And we see the impact of the change in this screenshot:

![](/images/roguelike/v5_redo_terminal.png)


## Add Parts to Fix Itself
I decided that the character's energy was draining so quickly because it an electrical short.  I had the idea
of adding parts that the player could collect so that the character can fix itself.  I achieved this with the
following prompt:

> Add objects called parts to the game. The battery objects should be displayed using the character "p". The game should start with 5 parts objects. The parts should should be randomly distributed across the map inside the rooms. When the character's position matches the position of a part, the character picks up the part, and the part should be removed from the game. When the character has collected all 5 parts, the rate at which the character's energy drains should be decreased from 1 unit per move to 1 unit every 10 moves. In addition, display the message "Congratulations! You used the parts to fix your electrical short. Your energy level is no longer going to drain so quickly."

Here is a screenshot showing the message when all of the parts have been collected:

![](/images/roguelike/v6_terminal.png)

## Adding an Introductory Story
The last change gave me an idea for a story.  I figured a story would make the game more enjoyable and
provide a way to inform the user of their first objective.  I had Gemini add a splash screen and generate
the text for a story with this prompt:

> Display a splash screen when the program starts that tells the player the background story for the game. The story should include the following elements:
>
> * The character is a robot
> * The robot woke up in an unknown place, not knowing how it got there
> * The robot notices that it has an electrical short. It's energy is draining quickly.
> * The robot needs to find parts to fix itself with.
> * Thankfully, the robot seems to be in a workshop with ample parts and batteries.
>
> After the user presses enter, the program should start the game.

The introductory screen with the generated story text can be seen in this screenshot:

![](/images/roguelike/v7_terminal.png)

## Conclusion
As a refresher, here's the story so far:  I'm using Google Gemini 2.5 Pro to implement
a roguelike, text-based, role playing game.  One of my goals is to improve my proficiency with
using AI assistants for programming.  Practically, this means learning how to describe my
requirements, write prompts that direct the AI appropriately to achieve the desired results,
and evaluate the resulting code, both in terms of correctness and quality (readability, performance,
abstractions for easy of extension, etc.). Of course, it also means learning how to handle cases
when the AI doesn't do what I want.

My second goal is to evaluate this project as a potential assignment to introduce
students to programming with AI assistants.  I am imagining an assignment where which offers
substantially scaffolding initially such as giving the students specific prompts, directions for tasks
such as entering the prompts into Gemini, copying code to the terminal to run it, attaching files
to use as starting points, and redoing / re-running steps when the output is incorrect.
Once students have the basics down, I want students to continue developing the game. This will
give students lots of opportunities to be creative and hopefully expose them to occasional
"gotchas" that they will learn to overcome to improve their proficiency.

I'm starting with a web-based chat since that seems less overwhelming than using something like
the Cursor IDE or Anthropic's Claude Code CLI tool.   Thankfully, these sort of simple texted-based
games don't require any dependencies to implement and their implementation is small enough
(at least initially) to be written in a single file.

So far, everything has been going quite well.  Gemini has done what I asked without any significant
issues.  It hasn't seemed to have trouble implementing the desired functionality or modifying the existing
code from previous steps.  The code it generates is relatively clean and well commented.  All of this
has enabled a very smooth experience learning the basics of programming-by-prompt.  Eventually, though,
I need to figure out how to go beyond Gemini's capabilities so I can figure out how to handle cases
when things don't go right the first time.  Additionally, I'm starting to wonder if I can provide
test cases or code to Gemini to help it generate a correct implementation.

## Appendix

Here's the final script:

```python
import os
import random

def display_splash_screen():
    """Clears the screen and displays the game's backstory."""
    os.system('cls' if os.name == 'nt' else 'clear')

    story = """

    >>> BOOTING...SYSTEMS...ONLINE.
    >>> SENSORS...ACTIVE.

    You awaken with a jolt, your optical sensors slowly focusing on the
    dimly lit room around you. This is not your designated charging bay.
    You are a maintenance robot, but you have no memory of how you got here.

    > WARNING: CRITICAL POWER FAILURE DETECTED.

    An **electrical short** is causing your **energy** to drain at an alarming rate.
    You must find a way to fix it, or you will shut down permanently.

    Scanning the area, you realize you are in some kind of derelict workshop.
    It's a mess, but you detect salvageable **parts (p)** and spare **batteries (b)**
    scattered throughout the wreckage.

    Your new directive is clear: Find the parts. Fix the short. Survive.

    """

    print(story)
    input("Press Enter to begin...")

# --- Game Class for Rectangular Rooms ---
class Rect:
    def __init__(self, x, y, w, h):
        self.x1 = x
        self.y1 = y
        self.x2 = x + w
        self.y2 = y + h

    def center(self):
        """Returns the center coordinate of the room."""
        center_x = (self.x1 + self.x2) // 2
        center_y = (self.y1 + self.y2) // 2
        return (center_x, center_y)

    def intersects(self, other):
        """Returns true if this rectangle intersects with another one."""
        return (self.x1 <= other.x2 and self.x2 >= other.x1 and
                self.y1 <= other.y2 and self.y2 >= other.y1)

# --- Dungeon Generation ---
def generate_dungeon(map_width, map_height, max_rooms, room_min_size, room_max_size, num_batteries, num_parts):
    """Generates the map, player position, and object locations."""
    game_map = [['#' for _ in range(map_width)] for _ in range(map_height)]
    rooms = []
    player_start_pos = (0, 0)

    for _ in range(max_rooms):
        w = random.randint(room_min_size, room_max_size)
        h = random.randint(room_min_size, room_max_size)
        x = random.randint(1, map_width - w - 2)
        y = random.randint(1, map_height - h - 2)
        new_room = Rect(x, y, w, h)

        if any(new_room.intersects(other_room) for other_room in rooms):
            continue

        for ry in range(new_room.y1, new_room.y2):
            for rx in range(new_room.x1, new_room.x2):
                game_map[ry][rx] = '.'

        (new_x, new_y) = new_room.center()

        if not rooms:
            player_start_pos = (new_x, new_y)
        else:
            (prev_x, prev_y) = rooms[-1].center()
            if random.randint(0, 1) == 1:
                for hx in range(min(prev_x, new_x), max(prev_x, new_x) + 1):
                    game_map[prev_y][hx] = '.'
                for vy in range(min(prev_y, new_y), max(prev_y, new_y) + 1):
                    game_map[vy][new_x] = '.'
            else:
                for vy in range(min(prev_y, new_y), max(prev_y, new_y) + 1):
                    game_map[vy][prev_x] = '.'
                for hx in range(min(prev_x, new_x), max(prev_x, new_x) + 1):
                    game_map[new_y][hx] = '.'

        rooms.append(new_room)

    # --- Place Objects ---
    objects = []
    # Place batteries
    batteries_placed = 0
    while batteries_placed < num_batteries:
        room = random.choice(rooms)
        pos = (random.randint(room.x1, room.x2 - 1), random.randint(room.y1, room.y2 - 1))
        if pos != player_start_pos and not any(obj['pos'] == pos for obj in objects):
            objects.append({'pos': pos, 'char': 'b', 'type': 'battery'})
            batteries_placed += 1

    # Place parts
    parts_placed = 0
    while parts_placed < num_parts:
        room = random.choice(rooms)
        pos = (random.randint(room.x1, room.x2 - 1), random.randint(room.y1, room.y2 - 1))
        if pos != player_start_pos and not any(obj['pos'] == pos for obj in objects):
            objects.append({'pos': pos, 'char': 'p', 'type': 'part'})
            parts_placed += 1

    return game_map, player_start_pos, objects

# --- Game Settings ---
MAP_WIDTH = 100
MAP_HEIGHT = 40
VIEW_WIDTH = 80
VIEW_HEIGHT = 25
NUM_BATTERIES = 20
NUM_PARTS = 5
MAX_ROOMS = 15
ROOM_MIN_SIZE = 6
ROOM_MAX_SIZE = 10

# --- Start of Script ---
display_splash_screen()

# --- Initialize Game ---
game_map, (player_x, player_y), objects = generate_dungeon(
    MAP_WIDTH, MAP_HEIGHT, MAX_ROOMS, ROOM_MIN_SIZE, ROOM_MAX_SIZE, NUM_BATTERIES, NUM_PARTS
)
MAX_ENERGY = 100
player_energy = MAX_ENERGY
parts_collected = 0
short_fixed = False
turn_count = 0
game_message = ""

# --- Main Game Loop ---
while True:
    # --- Drawing Logic ---
    os.system('cls' if os.name == 'nt' else 'clear')
    camera_x = max(0, min(player_x - (VIEW_WIDTH // 2), MAP_WIDTH - VIEW_WIDTH))
    camera_y = max(0, min(player_y - (VIEW_HEIGHT // 2), MAP_HEIGHT - VIEW_HEIGHT))

    print("--- Rogue Robot ---")
    status_line = f"Position: ({player_x}, {player_y}) | Energy: {player_energy}/{MAX_ENERGY} | Parts: {parts_collected}/{NUM_PARTS}"
    print(status_line)

    object_map = {obj['pos']: obj['char'] for obj in objects}

    for screen_y in range(VIEW_HEIGHT):
        row = []
        for screen_x in range(VIEW_WIDTH):
            map_x, map_y = camera_x + screen_x, camera_y + screen_y
            pos = (map_x, map_y)

            if pos == (player_x, player_y):
                row.append('@')
            elif pos in object_map:
                row.append(object_map[pos])
            else:
                row.append(game_map[map_y][map_x])
        print(''.join(row))

    if game_message:
        print(f"\n{game_message}")
        game_message = ""

    # --- Game Over Check ---
    if player_energy <= 0:
        print("\nENERGY DEPLETED. SHUTTING DOWN... 💀")
        break

    # --- Handle Player Input ---
    prompt = "\nYour move (w/a/s/d) or 'q' to quit: "
    move = input(prompt).lower()

    if move == 'q':
        break

    if move in ['w', 'a', 's', 'd']:
        turn_count += 1
        # --- Energy Drain ---
        if not short_fixed:
            player_energy -= 1
        elif turn_count % 10 == 0:
            player_energy -= 1

        next_x, next_y = player_x, player_y
        if move == 'w': next_y -= 1
        elif move == 's': next_y += 1
        elif move == 'a': next_x -= 1
        elif move == 'd': next_x += 1

        # --- Collision & Interaction ---
        if game_map[next_y][next_x] != '#':
            player_x, player_y = next_x, next_y

            for i, obj in enumerate(objects):
                if (player_x, player_y) == obj['pos']:
                    if obj['type'] == 'battery':
                        player_energy = MAX_ENERGY
                        game_message = "Battery absorbed. Energy levels maximized."
                    elif obj['type'] == 'part':
                        parts_collected += 1
                        game_message = "Component acquired."
                        if parts_collected == NUM_PARTS:
                            short_fixed = True
                            game_message = "All components acquired. You repair the electrical short, stabilizing your power consumption."

                    del objects[i]
                    break

# --- Runs after the loop breaks ---
print("\n...SYSTEMS OFFLINE.\n")
```
