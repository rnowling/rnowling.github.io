---
layout: post
title:  "AI-Assisted Software Development with LLMs: Part III"
date:   2025-07-25 10:13:19
categories: "software-engineering"
tags: ["education", "ai", "llm", "cs-education"]
---

(This post is a continuation of a series I started previously.  See parts I and II
[here](/software-engineering/2025/07/23/ai-programming-assistants-part-i.html) and 
[here](/software-engineering/2025/07/24/ai-programming-assistants-part-ii.html).)

At the end of the last blog post, I had a good start on a simple text-based
role-playing game.  The player could move a character around an empty
rectangular map but not beyond the map boundaries.

Of course, this isn't a very interesting game so far.  In this blog post,
I'm going to start to add some more interesting game elements.  I'll
have Gemini add rooms and hallways, objects the character can pick up, and
give the player an "energy" attribute that goes down over time.

## Creating a More Sophisticated Map
Starting with the last version of the script from [part II](/software-engineering/2025/07/24/ai-programming-assistants-part-ii.html),
I prompted Gemini to add rooms and hallways to the map:

> Please divide the map into rooms. Each room should have 1 or 2 entryways. The rooms should be connected by hallways.

Gemini happily performed the task -- even going beyond what I expected.  I was hoping that Gemini would recognize the need for
a collision detection system (which it did), but I didn't expect Gemini to implement procedural generation for the maps.
Every time you run the game, you get a different map.  Pretty neat! Here's a screenshot from my testing.

![](/images/roguelike/v3_terminal.png)


## Adding a Depleting Energy Level
Exploring the map is fun but there isn't much of a challenge.  Games usually have you find keys to unlock doors, solve puzzles,
fight hoardes of enemies, etc.  I decided that the character will be a robot with a short that is causing its battery to drain
quickly.  The robot is trying to collect the parts needed to repair itself before it runs out of battery.  I asked Gemini to
implement the first part of this, the depleting energy level:

> Add a quantitative attribute to the player's character called energy. It's minimum value is 0, while it's maximum value is 100. When the game starts, the character's energy should be initialized to 100. After every turn, the energy should go down by 1 point. If the character's energy reaches 0, the game is over, and the character has lost. 

And of course, quickly died with no means to restore my energy level.  But at least Gemini was thoughtful enough to include a cute
message indicating when I lost the game.

![](/images/roguelike/v4_terminal.png)

## Batteries for Restoring Energy
To make it at least feasible for the robot to repair itself, I decided to add battery objects that the character could pick
up and would recharge their internal battery.  I used the following prompt:

> Add objects called batteries to the game. The battery objects should be displayed using the character "b". The game should start with 20 battery objects. The batteries should should be randomly distributed across the map inside the rooms. When the character's position matches the position of a battery object, the character's energy should be increased by 10 points and the battery object should be removed from the game. 

Gemini understood what I was asking and happily implemented it.  My character is still dying without a means to repair itself
but at least it lives a little longer.

![](/images/roguelike/v5_terminal.png)

## Reflections and Next Steps
I continue to be very impressed with Gemini so far.  My first prompt (adding rooms and hallways) was pretty basic.  Gemini was able to
infer that the map would need walls and that collision detection will be needed so that character can't walk through them.  I didn't think
Gemini would get that right the first time -- I thought I would have to expand the prompt with more details.  Secondly, I didn't expect
Gemini to implement a process for procedurally generating the maps -- I assumed it would create a single map and store it as data in
the program.

Gemini performed similar feats for the two other prompts.  Although I made the prompts more descriptive, there were details that I forgot
to mention that Gemini ended up inferring.  For example, when the player loses, the game prints a message.  I didn't tell Gemini to do
that, but it is logical that I would expect the game to do that.  Gemini inferred the intention and implemented it.

I have two more elements to add to the game.  I want to add part objects that the character can pick up.  If the character gets all of
the parts, it can repair itself and its energy level will hold steady.  Secondly, I want to add an introductory screen with a story
that gives background and context for the game.  Expect to see the results in my next blog post.

I plan to finish the blog post with thoughts on how I would turn these into an assignment for students.  After running the initial
prompts, the students should be more comfortable coming up with their own.  The assignment will need to detail how students can
checkpoint the game so they can backtrack if things go poorly.  Armed with that knowledge, students should have a wide space of
ways to expand the game and hopefully develop some skills and confidence in programming with an AI assistant.

## Appendix
Here's the code for the script after the changes described above were made:

```python
import os
import random

# --- Game Class for Rectangular Rooms ---
class Rect:
    def __init__(self, x, y, w, h):
        self.x1 = x
        self.y1 = y
        self.x2 = x + w
        self.y2 = y + h

    def center(self):
        return (self.x1 + self.x2) // 2, (self.y1 + self.y2) // 2

    def intersects(self, other):
        return (self.x1 <= other.x2 and self.x2 >= other.x1 and
                self.y1 <= other.y2 and self.y2 >= other.y1)

# --- Dungeon Generation ---
def generate_dungeon(map_width, map_height, max_rooms, min_room_size, max_room_size, num_batteries):
    """Generates a new dungeon map, returning the map, player position, and battery locations."""
    game_map = [['#' for _ in range(map_width)] for _ in range(map_height)]
    rooms = []
    player_start_pos = (0, 0)

    for _ in range(max_rooms):
        w = random.randint(min_room_size, max_room_size)
        h = random.randint(min_room_size, max_room_size)
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
                for hx in range(min(prev_x, new_x), max(prev_x, new_x) + 1): game_map[prev_y][hx] = '.'
                for vy in range(min(prev_y, new_y), max(prev_y, new_y) + 1): game_map[vy][new_x] = '.'
            else:
                for vy in range(min(prev_y, new_y), max(prev_y, new_y) + 1): game_map[vy][prev_x] = '.'
                for hx in range(min(prev_x, new_x), max(prev_x, new_x) + 1): game_map[new_y][hx] = '.'
        rooms.append(new_room)

    # --- Place Batteries ---
    batteries = []
    placed_locations = {player_start_pos}
    while len(batteries) < num_batteries:
        room = random.choice(rooms)
        bx = random.randint(room.x1, room.x2 - 1)
        by = random.randint(room.y1, room.y2 - 1)
        if (bx, by) not in placed_locations:
            placed_locations.add((bx, by))
            batteries.append((bx, by))

    return game_map, player_start_pos, batteries

# --- Game Settings ---
MAP_WIDTH, MAP_HEIGHT = 100, 40
VIEW_WIDTH, VIEW_HEIGHT = 80, 25
MAX_ROOMS, MIN_ROOM_SIZE, MAX_ROOM_SIZE = 15, 6, 10
NUM_BATTERIES = 20

# --- Initialize Game ---
game_map, (player_x, player_y), batteries = generate_dungeon(
    MAP_WIDTH, MAP_HEIGHT, MAX_ROOMS, MIN_ROOM_SIZE, MAX_ROOM_SIZE, NUM_BATTERIES
)
MAX_ENERGY = 100
player_energy = MAX_ENERGY
status_message = ""

# --- Main Game Loop ---
while True:
    # --- Drawing Logic ---
    os.system('cls' if os.name == 'nt' else 'clear')
    camera_x = max(0, min(player_x - (VIEW_WIDTH // 2), MAP_WIDTH - VIEW_WIDTH))
    camera_y = max(0, min(player_y - (VIEW_HEIGHT // 2), MAP_HEIGHT - VIEW_HEIGHT))

    print("--- Roguelike Dungeon ---")
    status_line = f"Position: ({player_x}, {player_y}) | Energy: {player_energy}/{MAX_ENERGY}"
    print(status_line)
    if status_message:
        print(status_message)

    for screen_y in range(VIEW_HEIGHT):
        row = []
        for screen_x in range(VIEW_WIDTH):
            map_x, map_y = camera_x + screen_x, camera_y + screen_y
            if map_x == player_x and map_y == player_y:
                row.append('@')
            elif (map_x, map_y) in batteries:
                row.append('b')
            else:
                row.append(game_map[map_y][map_x])
        print(''.join(row))

    # --- Game Over & Input ---
    if player_energy <= 0:
        print("\nYou ran out of energy! Game Over. 💀")
        break
    
    status_message = "" # Clear message after displaying it
    move = input("Your move (w/a/s/d) or 'q' to quit: ").lower()

    if move == 'q': break
    
    if move in ['w', 'a', 's', 'd']:
        player_energy -= 1
        next_x, next_y = player_x, player_y
        if move == 'w': next_y -= 1
        elif move == 's': next_y += 1
        elif move == 'a': next_x -= 1
        elif move == 'd': next_x += 1

        if game_map[next_y][next_x] != '#':
            player_x, player_y = next_x, next_y
            # --- Check for Battery Pickup ---
            if (player_x, player_y) in batteries:
                player_energy = min(MAX_ENERGY, player_energy + 10)
                batteries.remove((player_x, player_y))
                status_message = "You found a battery! +10 energy. 🔋"

print("\nThanks for playing! Goodbye. 👋\n")
```
