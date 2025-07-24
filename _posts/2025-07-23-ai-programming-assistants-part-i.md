---
layout: post
title:  "AI-Assisted Software Development with LLMs: Part I"
date:   2025-07-23 15:13:19
categories: "software-engineering"
tags: ["education", "ai", "llm", "cs-education"]
---

AI-assisted software development seems to be taking the world by storm.  One of my goals as an educator is to ensure that
my classes (and our curriculum as a whole) is preparing students so that they are well-positioned to succeed in
interviews, internships, and ultimately, in full-time positions.  So... I need to figure out how to use
AI programming assistants, evaluate their capabilities, incorporate their use into my classes, and
scaffold the learning process for students.

I'm facing a mental stumbling block, though.  At this point, I'm so attuned to thinking directly in code that expressing
requests in natural language is jarring, uncomfortable, and a significant hinderance.  The difference is analagous to
switching from using a primarily imperative programming paradigm (which describes *how* to do something) to a
declarative programming paradigm (which describes *what* to do and lets the system figure out the *how*).  More concretely,
it's akin to the difference between using for loops and dictionaries and SQL to calculate aggregated values.  Suddenly,
you no don't know what you can and can't do and you don't have a toolbox full of well-understand tools you can apply. We
see this all of the time when students are introduced to SQL or Prolog -- it takes time for them to stop translating
imperative solutions into SQL and start thinking natively in SQL.

I decided to start with collecting concrete tasks I can perform with LLMs.  My goal is to understand both what tasks they
can perform and how to write prompts to that reliably direct them to perform those tasks.  A discussion with a colleague
led me to this initial taxonomy of tasks:

1. Read and interpret code
   1. Generate documentation from code
   1. Explain how a piece of code works
1. Or, create and modify code (including tests and build systems)
   1. Write or modify code from a description of functional requirements
   1. Fix a piece of code to resolve an error message

## A Target: Simple Games
I realized that simple games are a great place to start learning how to use an AI assistant.  The rules of many games
can be described in English by anyone who has played the game.  Simple games can be implemented in as little as a single
source file with no external dependencies.  Games can be tested by playing them.  And lastly, game design is incredibly
open-ended, providing an opportunity to be creative.

I decided on one key restriction: I am not allowed to refer to specific games (e.g., Snake, Hangman, etc.) in my prompts.
Otherwise, it would be trivial to write a prompt.  "Please implement a basic version of the game Snake in Python." I
wouldn't have to describe the rules, mechanics, or functional requirements.  I won't grow much that way.

## Creating A Word-Guessing Game
I gave Google Gemini 2.5 Pro the following prompt:


> Please generate code for a text-based game in Python. On start, the game should
  randomly pick a word from a dictionary. The word’s letters will initially be
  hidden by printing a star for each letter. The user will attempt to guess letters
  that might be in the word. On each guess, the user will enter a letter. If the letter is in the word, the stars at all
  matching positions will be replaced with the letter. The user wins when they have correctly guessed all of the letters.
  Users are allowed three incorrect guesses. If they guess incorrectly three times before the word is revealed, they lose.


Gemini identified the type of game I was asking it to create, which was a nice validation of my description:

> Of course. Here is the Python code for a text-based word guessing game.

  This script uses the random module to select a word from a predefined list. It then prompts the user to guess letters
  until they either reveal the word or make three incorrect guesses.


and provided the following generated code:


```python
import random

def play_word_game():
    """
    This function runs a text-based word guessing game.
    A random word is chosen, and the player guesses letters
    to reveal it before running out of attempts.
    """
    # --- Game Setup ---
    word_dictionary = [
        "python", "developer", "programming", "algorithm", "computer", 
        "keyboard", "software", "database", "variable", "function"
    ]
    secret_word = random.choice(word_dictionary)
    word_length = len(secret_word)
    
    # Represents the hidden word shown to the user (e.g., '*******')
    display_word = ['*'] * word_length
    
    incorrect_guesses = 0
    max_incorrect_guesses = 3
    guessed_letters = []

    print("--- Welcome to the Word Guessing Game! ---")
    print("Try to guess the word, one letter at a time.")
    print("You can make up to 3 incorrect guesses.")

    # --- Main Game Loop ---
    # The loop continues as long as the user has guesses left AND the word has not been fully revealed
    while incorrect_guesses < max_incorrect_guesses and '*' in display_word:
        print("\n" + " ".join(display_word))
        print(f"Incorrect guesses remaining: {max_incorrect_guesses - incorrect_guesses}")
        
        # Get user input
        guess = input("Guess a letter: ").lower()

        # --- Input Validation ---
        if len(guess) != 1 or not guess.isalpha():
            print("⚠️ Invalid input. Please enter a single letter.")
            continue
        
        if guess in guessed_letters:
            print("You have already guessed that letter. Please try a different one.")
            continue
        
        # Add the valid new guess to the list of guessed letters
        guessed_letters.append(guess)

        # --- Process the Guess ---
        if guess in secret_word:
            print(f"✅ Good guess! '{guess}' is in the word.")
            # Update the display_word to show the correctly guessed letter
            for i in range(word_length):
                if secret_word[i] == guess:
                    display_word[i] = guess
        else:
            incorrect_guesses += 1
            print(f"❌ Sorry, '{guess}' is not in the word.")

    # --- End of Game ---
    print("\n--- Game Over ---")
    if '*' not in display_word:
        print(f"🎉 Congratulations! You guessed the word: {secret_word}")
    else:
        print(f"😔 You lost. The secret word was: {secret_word}")


# This line ensures the game starts when the script is run
if __name__ == "__main__":
    play_word_game()
```

I played the game a few times to make sure that the generated code works as expected.

## Modifying the Word-Guessing Game
Next, I wanted to experiment with modifying existing code. Per my original request, the game only allowed the user to
make 3 incorrect guesses.  The implementation has a fixed set of words and they are on the longer side.  This makes it
rather difficult to win. I decided to have Gemini change the number of incorrect guesses allowed. Rather than adding a
follow-up prompt in the original session, I decided to create a new session and upload the original file.

The "+" symbol on the Gemini prompt window displays a menu with several options.  One of them is the ability to select
and upload files.  I used this functionality to attach a file containing the Python script above.  I provided the
following prompt:

```
I've attached a Python script that implements a word-guessing game.  The game only allows 3 incorrect guesses.
The words are long, however, so this makes the game more difficult to win than I want.  Please modify the program to
allow 5 incorrect guesses and explain what you changed.
```

Gemini provided a nice description of the specific change in its response:

```
Of course. I've updated the script to allow for 5 incorrect guesses.

The change was made on line 20. I modified the max_incorrect_guesses variable, which controls the number of wrong
guesses a player can make before the game ends. By changing its value from 3 to 5, the player now has more opportunities
to guess the word.
```

I used diff to verify what was changed:

```
21c21
<     max_incorrect_guesses = 3
---
>     max_incorrect_guesses = 5
26c26
<     print("You can make up to 3 incorrect guesses.")
---
>     print("You can make up to 5 incorrect guesses.")
```

The changes that Gemini made correctly updated the functionality to do what I described. However, Gemini's description
was incorrect in two ways.  First, the `max_incorrect_guesses` variable is defined on line 21, not 20. Secondly, the
description does not mention the secondary change on line 26. The second change is required for correctness but also a
result of a bad programming practice in the original version: repeating a constant value in the code instead of
referencing a variable containing the value.

## What's Next?
Future posts in this series will feature more complex games and a wider selection of concrete tasks accomplishable
with Gemini.
