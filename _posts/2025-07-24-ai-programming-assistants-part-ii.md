---
layout: post
title:  "AI-Assisted Software Development with LLMs: Part II"
date:   2025-07-23 15:13:19
categories: "software-engineering"
tags: ["education", "ai", "llm", "cs-education"]
---

(This post is a continuation of a series I started previously.  See
["AI-Assisted Software Development with LLMs: Part I"](/software-engineering/2025/07/23/ai-programming-assistants-part-i.html)
for the first post.)

Last time, I created and modified a simple word-guessing game.  Google Gemini 2.5 Pro had no trouble creating
a functional game or modifying the number of guesses.

I wanted to move to something more complex and more open-ended so I could be more creative.  I decided to
create a text-based, [roguelike](https://en.wikipedia.org/wiki/Roguelike), role-playing game similar to
[nethack](https://www.nethack.org/).  Of course, I will start off with something much simpler and try to
evolve it towards something more ornate over time.

## Moving a Character in a Box
I decided to start with having the AI create a program that allows a character to move in a box on the screen.
This creates simple input, graphics, and physics systems.  I gave Gemini the following prompt:

> I want to create a text-based role-playing game. Please generate a Python program that generates a map
> with borders displayed on the screen with stars and the user’s character displayed with an "@" symbol.
> The user should be able to move the character up, down, left, or right one space at a time by entering the
> letters w, s, a, and d. The character should not be able to cross the map’s borders. The map should be no
> larger than 80 by 25 characters. 

Gemini responded, indicating understanding of my request:

> Of course! Here is a complete Python program that generates a playable, text-based map for a role-playing game. 🗺️
> 
> This program creates a bordered map, places a character (@) on it, and allows you to move the character using
> the 'w', 's', 'a', and 'd' keys. The character is prevented from moving outside the map's boundaries.

and even provided some instructions on how to run the script and how it worked:

> 🎮 How to Run the Game
>
>    Save the Code: Save the code above into a file named rpg_map.py.
>
>    Open a Terminal: Open a command prompt (on Windows) or a terminal (on macOS/Linux).
>
>    Navigate to the File: Use the cd command to navigate to the directory where you saved rpg_map.py.
>
>    Run the Script: Type python rpg_map.py and press Enter.
>
> The game will start, and you can move your character by typing w, s, a, or d and pressing Enter
> after each command.
>
> ⚙️ How It Works
>
>    Game Loop: The while True: statement creates an infinite loop that keeps the game running.
>
>    Screen Clearing: os.system('cls' if os.name == 'nt' else 'clear') clears the terminal screen before
>    each frame is drawn. This creates the illusion of animation.
>
>    Map Drawing: The code uses nested for loops to iterate through every coordinate of the map grid. It
>    prints a * for the border, a @ for the player's position, and a space ' ' for everything else.
>
>    Player Movement: The program asks for user input with input(). Based on the input (w, s, a, or d), it
>    adjusts the player_x or player_y coordinates.
>
>    Boundary Checking: Before updating the player's position, an if statement checks if the new position
>    would be inside the border walls. For example, if player_y > 1: ensures the player can't move into the
>    top border at y=0.

and the following script:

```python
import os

# --- Game Settings ---
MAP_WIDTH = 40
MAP_HEIGHT = 20

# --- Player's Initial State ---
# Place the player in the center of the map
player_x = MAP_WIDTH // 2
player_y = MAP_HEIGHT // 2

# --- Main Game Loop ---
while True:
    # Clear the terminal screen. 'cls' is for Windows, 'clear' for macOS/Linux.
    os.system('cls' if os.name == 'nt' else 'clear')

    print("--- Text RPG Map ---")
    print("Move your character (@) with 'w','a','s','d' and press Enter.")
    
    # --- Draw the Map ---
    # Iterate over each row (y-coordinate)
    for y in range(MAP_HEIGHT):
        # Iterate over each column (x-coordinate) for the current row
        for x in range(MAP_WIDTH):
            # Check if the current coordinate is a border
            if y == 0 or y == MAP_HEIGHT - 1 or x == 0 or x == MAP_WIDTH - 1:
                print('*', end='')
            # Check if the current coordinate is the player's position
            elif x == player_x and y == player_y:
                print('@', end='')
            # Otherwise, it's an empty space
            else:
                print(' ', end='')
        # Print a newline character at the end of each row
        print()

    # --- Handle Player Input ---
    move = input("Your move: ").lower()

    # --- Update Player Position ---
    # Move Up
    if move == 'w':
        # Check for collision with the top border
        if player_y > 1:
            player_y -= 1
    # Move Down
    elif move == 's':
        # Check for collision with the bottom border
        if player_y < MAP_HEIGHT - 2:
            player_y += 1
    # Move Left
    elif move == 'a':
        # Check for collision with the left border
        if player_x > 1:
            player_x -= 1
    # Move Right
    elif move == 'd':
        # Check for collision with the right border
        if player_x < MAP_WIDTH - 2:
            player_x += 1
```

I tested the program and it worked perfectly:

![](/images/roguelike/v1_terminal.png)

When I entered one of the four letters followed by return, the character moved in appropriate direction.
When the character was next to a wall, the character wasn't allowed to go further.

## Expanding the Size of the Map
These types of games often involve maps that are much larger than just what can be displayed at one time.
I asked Gemini to expand the map.  I was delighted to see it implement an abstraction between the map
and the display system.  Here's the prompt:

> Please change the game logic so that the map can be larger than 80 by 25. The game should only show a
> 80 by 25 region of the map on the console with the user’s character in the center. When the user moves,
> the displayed region of the map should be updated. 

Here's the response:

> Of course! Here is the updated Python program.
> 
> This version implements a "camera" system. The world map is now much larger than the console window, and
> the view stays centered on your character as you move. This creates a scrolling map effect. 🗺️

and the modified script:

```python
import os

# --- Game Settings ---
# World map dimensions (can be very large)
MAP_WIDTH = 200
MAP_HEIGHT = 60

# Viewport dimensions (the visible part of the map, max 80x25)
VIEW_WIDTH = 80
VIEW_HEIGHT = 25

# --- Player's Initial State ---
# Place the player in the center of the large world map
player_x = MAP_WIDTH // 2
player_y = MAP_HEIGHT // 2

# --- Main Game Loop ---
while True:
    # --- Camera Logic ---
    # Calculate the camera's top-left corner to try and center the player.
    # The camera position is the top-left coordinate of the viewport on the world map.
    camera_x = player_x - (VIEW_WIDTH // 2)
    camera_y = player_y - (VIEW_HEIGHT // 2)

    # Clamp the camera to the map boundaries to prevent it from showing
    # areas outside the world map.
    camera_x = max(0, min(camera_x, MAP_WIDTH - VIEW_WIDTH))
    camera_y = max(0, min(camera_y, MAP_HEIGHT - VIEW_HEIGHT))

    # --- Drawing Logic ---
    # Clear the terminal screen
    os.system('cls' if os.name == 'nt' else 'clear')

    print("--- Scrolling RPG Map ---")
    print(f"Player Position: ({player_x}, {player_y}) | World Size: {MAP_WIDTH}x{MAP_HEIGHT}")
    
    # Draw the visible part of the map (the viewport)
    # Iterate over each row of the viewport (on screen)
    for screen_y in range(VIEW_HEIGHT):
        # Iterate over each column of the viewport (on screen)
        for screen_x in range(VIEW_WIDTH):
            # Calculate the corresponding coordinate on the large world map
            map_x = camera_x + screen_x
            map_y = camera_y + screen_y

            # Now, decide what character to draw at this screen position
            # Check if the calculated map coordinate is the player's position
            if map_x == player_x and map_y == player_y:
                print('@', end='')
            # Check if the map coordinate is a world map border
            elif map_y == 0 or map_y == MAP_HEIGHT - 1 or map_x == 0 or map_x == MAP_WIDTH - 1:
                print('*', end='')
            # Otherwise, it's an empty space
            else:
                print(' ', end='')
        print() # Newline after drawing the row

    # --- Handle Player Input & Movement ---
    # Note: Boundary checks are now against the large MAP dimensions
    move = input("Your move (w/a/s/d): ").lower()

    if move == 'w':
        if player_y > 1:
            player_y -= 1
    elif move == 's':
        if player_y < MAP_HEIGHT - 2:
            player_y += 1
    elif move == 'a':
        if player_x > 1:
            player_x -= 1
    elif move == 'd':
        if player_x < MAP_WIDTH - 2:
            player_x += 1
```

and a very nice explanation of how it works:

> ⚙️ How It Works: Key Changes
> 
> This version introduces a few new concepts to handle the large, scrolling map.
> 
> World Map vs. Viewport
>
>    World Map: Defined by MAP_WIDTH and MAP_HEIGHT (e.g., 200x60). This is the entire game world. The player's (x,y) coordinates exist on this map.
>
>    Viewport: Defined by VIEW_WIDTH and VIEW_HEIGHT (e.g., 80x25). This is the small, visible rectangle of the map that gets drawn to your console.
>
> The Camera 📸
>
> The "camera" isn't a visible object. It's a pair of coordinates (camera_x, camera_y) that represents the top-left corner of the viewport on the large world map.
>
>    Centering: In each loop, the code first calculates where the camera should be to put the player in the center of the screen: camera_x = player_x - (VIEW_WIDTH // 2).
>
>    Clamping: If the player gets close to the edge of the world map, the camera would normally move past the border. The max() and min() functions "clamp" the camera's position, ensuring it never shows area outside the MAP_WIDTH and MAP_HEIGHT. This is why your character moves from the center to the edge of the screen when you approach a world border.
>
>Coordinate Translation
>
>The drawing loop iterates through the viewport's screen coordinates (screen_x, screen_y). For each point on the screen, it calculates the corresponding point on the large world map using the camera's position:
>
>    map_x = camera_x + screen_x
>
>    map_y = camera_y + screen_y
>
> It then uses these map_x and map_y coordinates to decide whether to draw the player (@), a border (*), or an empty space.

I ran the program to test and it worked as expected.  Wonderful!  Here's a screenshot:

![](/images/roguelike/v1_terminal.png)

## Adding a Quit Option
After playing with the game a bit, I realized that it gets annoying that there isn't a quit option.  I decided
to have Gemini add one:

> Please modify the program so that if the user can quit the game by entering the character 'q'. 

and the final version of the script for today:

```python
import os

# --- Game Settings ---
# World map dimensions (can be very large)
MAP_WIDTH = 200
MAP_HEIGHT = 60

# Viewport dimensions (the visible part of the map, max 80x25)
VIEW_WIDTH = 80
VIEW_HEIGHT = 25

# --- Player's Initial State ---
# Place the player in the center of the large world map
player_x = MAP_WIDTH // 2
player_y = MAP_HEIGHT // 2

# --- Main Game Loop ---
while True:
    # --- Camera Logic ---
    # Calculate the camera's top-left corner to try and center the player.
    camera_x = player_x - (VIEW_WIDTH // 2)
    camera_y = player_y - (VIEW_HEIGHT // 2)

    # Clamp the camera to the map boundaries
    camera_x = max(0, min(camera_x, MAP_WIDTH - VIEW_WIDTH))
    camera_y = max(0, min(camera_y, MAP_HEIGHT - VIEW_HEIGHT))

    # --- Drawing Logic ---
    # Clear the terminal screen
    os.system('cls' if os.name == 'nt' else 'clear')

    print("--- Scrolling RPG Map ---")
    print(f"Player Position: ({player_x}, {player_y}) | World Size: {MAP_WIDTH}x{MAP_HEIGHT}")
    
    # Draw the visible part of the map (the viewport)
    for screen_y in range(VIEW_HEIGHT):
        row = []
        for screen_x in range(VIEW_WIDTH):
            map_x = camera_x + screen_x
            map_y = camera_y + screen_y

            if map_x == player_x and map_y == player_y:
                row.append('@')
            elif map_y == 0 or map_y == MAP_HEIGHT - 1 or map_x == 0 or map_x == MAP_WIDTH - 1:
                row.append('*')
            else:
                row.append(' ')
        print(''.join(row))

    # --- Handle Player Input & Movement ---
    move = input("Your move (w/a/s/d) or 'q' to quit: ").lower()

    # Check if the player wants to quit
    if move == 'q':
        break # Exit the while loop

    # Update player position for movement
    if move == 'w':
        if player_y > 1:
            player_y -= 1
    elif move == 's':
        if player_y < MAP_HEIGHT - 2:
            player_y += 1
    elif move == 'a':
        if player_x > 1:
            player_x -= 1
    elif move == 'd':
        if player_x < MAP_WIDTH - 2:
            player_x += 1

# --- Runs after the loop breaks ---
print("\nThanks for playing! Goodbye. 👋\n")
```

## Reflection and Next Steps
I was very happy to see what Gemini could do.  We're starting to build a game with some sophisticated logic
but Gemini seems more than up to the task.

Eventually, I'll hit a point where my prompts are insufficient.  Right now, I'm putting a lot of effort into
writing them.  I'm realizing that I need to be more direct and use simpler sentences.  (In other words, I'm
learning to appreciate how Ernest Hemingway wrote.)  Over time, I will likely be able to generate prompts
with less effort as I get used to the writing style and develop an understanding of how and what I need
to specify to get successful results.

Similarly, my success rate has been 100% so far.  That's not realistic or practical.  There will be cases
cases where the prompt is insufficient or the request is too complicated for Gemini to execute.  I look forward
to learning how to modify the prompts and use the "redo" functionality.

So far, the explanations of the approaches are pretty decent and easy to follow.  That said, it is clear that
the developer needs a foundational understanding of programming to follow them.  Over time, I hope to get a
better sense of where a user is in their journey and how these tools can be used as learning assistants,
not just junior developers.

In the meantime, I need to figure out what I want the goals of my game to be.  Should the character fight
through hoards of enemies?  Should it be a scavenger hunt game where the character has to find items? Should I
add a crafting system?  So many possibilities to explore... and so many limits to push.
