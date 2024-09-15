# Basic Structure of a Visual Novel in Pygame

## Introduction

A visual novel is a video game genre that focuses on narrative and interaction through player decisions. In Pygame, a Python library for game development, you can create visual novels efficiently. This documentation details the basic structure and provides examples to help you build a visual novel using Pygame.

## Basic Structure of a Visual Novel

A visual novel generally consists of the following parts:

- Introduction Screen
- Dialog and Text Management
- Image and Background Management
- Choice Options
- Saving and Loading System

### Introduction Screen with Animation

Instead of a static screen, you can add a simple animation to make the introduction more attractive.

Example:
```python
import pygame
import sys
import time

pygame.init()

# Display configuration
screen = pygame.display.set_mode((800, 600))
pygame.display.set_caption("Título de la Novela Visual")

# Colors and font
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
font = pygame.font.Font(None, 74)

def show_intro_screen():
    logo = pygame.image.load('logo.png')  # Make sure you have a logo
    logo_rect = logo.get_rect(center=(400, 300))
    
    clock = pygame.time.Clock()
    start_time = time.time()
    duration = 5  # Animation duration in seconds

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_RETURN:  # Press Enter to start
                    return

        elapsed_time = time.time() - start_time
        if elapsed_time < duration:
            screen.fill(WHITE)
            alpha = int(255 * (elapsed_time / duration))
            logo.set_alpha(alpha)
            screen.blit(logo, logo_rect)
        else:
            return

        pygame.display.flip()
        clock.tick(60)

show_intro_screen()
```

### Advanced Dialog and Text System

Example:
```python
import pygame
import json
import sys
import time

# IPygame initialization
pygame.init()

# Display configuration
screen = pygame.display.set_mode((800, 600))
pygame.display.set_caption("Visual Novel Title")

# Colors and fonts
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
dialogue_font = pygame.font.Font(None, 36)

def load_dialogues(filename='dialogues.json'):
    with open(filename, 'r') as file:
        return json.load(file)

def show_text(dialogues, scene_name, text_speed=0.05):
    screen.fill(WHITE)
    scene_dialogues = dialogues.get(scene_name, [])
    y_offset = 50

    for dialogue in scene_dialogues:
        character = dialogue.get('character', 'Unknown')
        text = dialogue.get('text', '')

        # Show character name
        character_surface = dialogue_font.render(f"{character}:", True, BLACK)
        screen.blit(character_surface, (50, y_offset))
        y_offset += 40
        
        # Show text with writing effect
        temp_text = ""
        for char in text:
            temp_text += char
            screen.fill(WHITE)
            screen.blit(character_surface, (50, y_offset - 40))
            text_surface = dialogue_font.render(temp_text, True, BLACK)
            screen.blit(text_surface, (50, y_offset))
            pygame.display.flip()
            time.sleep(text_speed)

        y_offset += 40

        # Wait until the player presses a key.
        waiting = True
        while waiting:
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    pygame.quit()
                    sys.exit()
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_SPACE:  # Advance with the space bar
                        waiting = False

def main():
    dialogues = load_dialogues()

    # Show the first scene
    show_text(dialogues, 'scene1')

    # Show the second scene
    show_text(dialogues, 'scene2')

if __name__ == "__main__":
    main()
```

### JSON File Structure
First, create a JSON file containing your dialogs. This file can be structured as follows:

dialogues.json
Example:
```json
{
    "scene1": [
        {
            "character": "Starring",
            "text": "Hello, welcome to the visual novel."
        },
        {
            "character": "Narrator",
            "text": "This is where your adventure begins."
        }
    ],
    "scene2": [
        {
            "character": "Starring",
            "text": "What do you want to do today?"
        },
        {
            "character": "Option 1",
            "text": "Go to the store."
        },
        {
            "character": "Option 2",
            "text": "Go to the park"
        }
    ]
}
```

### Dynamic Image and Background Management

You can manage multiple backgrounds and characters, as well as dynamically change depending on the story.

Example:
```python
def show_background(image_path):
    background = pygame.image.load(image_path)
    screen.blit(background, (0, 0))
    pygame.display.flip()

def show_character(image_path, position, scale_factor=1.0):
    character = pygame.image.load(image_path)
    if scale_factor != 1.0:
        new_size = (int(character.get_width() * scale_factor), int(character.get_height() * scale_factor))
        character = pygame.transform.scale(character, new_size)
    screen.blit(character, position)
    pygame.display.flip()

def update_scene(background_path, character_paths):
    show_background(background_path)
    for character, pos, scale in character_paths:
        show_character(character, pos, scale)

# Example of use
update_scene('background1.png', [('character1.png', (100, 400), 1.0), ('character2.png', (300, 400), 0.8)])
```

### Election System with Effects

Choice options can have visual and sound effects to enhance the experience.

Example:
```python
import pygame

def show_choices(choices):
    screen.fill(WHITE)
    choice_font = pygame.font.Font(None, 36)
    y_offset = 400
    choice_rects = []
    
    for idx, choice in enumerate(choices):
        choice_text = choice_font.render(f"{idx + 1}. {choice}", True, BLACK)
        choice_rect = choice_text.get_rect(topleft=(50, y_offset))
        screen.blit(choice_text, choice_rect.topleft)
        choice_rects.append(choice_rect)
        y_offset += 40

    pygame.display.flip()
    
    # Election sound effect
    selection_sound = pygame.mixer.Sound('select.wav') # You can use any type of audio file
    
    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key in (pygame.K_1, pygame.K_2):  # Assuming there are 2 options
                    selection_sound.play()
                    return int(event.key) - pygame.K_1

choices = ["Go to the store", "Go to the park"]
chosen_option = show_choices(choices)
print(f"You chose the option {chosen_option + 1}")
```

### Complete Saving and Loading System

Implement a save system that can handle multiple game states and data.

Example:
```python
import pickle

def save_game(state, filename='savegame.pkl'):
    with open(filename, 'wb') as f:
        pickle.dump(state, f)

def load_game(filename='savegame.pkl'):
    try:
        with open(filename, 'rb') as f:
            return pickle.load(f)
    except FileNotFoundError:
        return None

game_state = {
    'scene': 'intro',
    'dialogue': 'Welcome',
    'choices_made': [1],
    'character_positions': {'protagonist': (100, 400)},
    'background': 'background1.png'
}
save_game(game_state)

loaded_state = load_game()
if loaded_state:
    print("Loaded state:")
    print(f"Scene: {loaded_state['scene']}")
    print(f"Dialogue: {loaded_state['dialogue']}")
    print(f"Choices made: {loaded_state['choices_made']}")
    print(f"Character positions: {loaded_state['character_positions']}")
    print(f"Background: {loaded_state['background']}")
```

# Remember

Remember that these codes found in this documentation are examples and for them to work perfectly they have to be more complex systems.