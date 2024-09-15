# Game State Management in a Visual Novel using Pygame

## Introduction

Game state management is crucial in a visual novel to control the flow of the story, handle different game modes, and ensure smooth transitions between scenes. In Pygame, managing the state involves setting up a system to track and switch between various states of the game, such as the main menu, story scenes, and settings.

## Key Concepts

- Game State: Represents the current condition or mode of the game (e.g., Main Menu, In-Game, Settings).

- State Manager: A system or class responsible for handling state transitions and maintaining the current state.

- State Transitions: The process of moving from one state to another based on user inputs or game events.

## Implementation Overview

The game state management system can be implemented using a combination of Python classes and Pygame functions. Here’s a step-by-step guide:

### Define Game States

Create a set of constants or an enumeration to represent different game states.

```python
# game_states.py
MAIN_MENU = 0
STORY = 1
SETTINGS = 2
GAME_OVER = 3
```

### Create a State Manager

The State Manager class is responsible for holding the current state and handling state transitions.

```python
# state_manager.py
import pygame
from game_states import MAIN_MENU, STORY, SETTINGS, GAME_OVER

class StateManager:
    def __init__(self):
        self.current_state = MAIN_MENU

    def set_state(self, new_state):
        self.current_state = new_state

    def get_state(self):
        return self.current_state
```

### Define State Classes

Each state can be represented by a class that handles its own logic and rendering.

```python
# main_menu.py
import pygame

class MainMenu:
    def __init__(self, screen):
        self.screen = screen
        self.font = pygame.font.Font(None, 74)
        self.title = self.font.render('Main Menu', True, (255, 255, 255))

    def update(self):
        # Handle input and update logic here
        pass

    def render(self):
        self.screen.fill((0, 0, 0))  # Black background
        self.screen.blit(self.title, (100, 100))  # Render title
```

```python
# story.py
import pygame

class Story:
    def __init__(self, screen):
        self.screen = screen
        self.font = pygame.font.Font(None, 36)
        self.text = self.font.render('Story Scene', True, (255, 255, 255))

    def update(self):
        # Handle input and update logic here
        pass

    def render(self):
        self.screen.fill((0, 0, 0))  # Black background
        self.screen.blit(self.text, (50, 50))  # Render text
```

###  Integrate State Manager with Main Game Loop

Modify the main game loop to use the State Manager and handle state transitions.

```python
# main.py
import pygame
from state_manager import StateManager
from main_menu import MainMenu
from story import Story
from game_states import MAIN_MENU, STORY

def main():
    pygame.init()
    screen = pygame.display.set_mode((800, 600))
    clock = pygame.time.Clock()

    state_manager = StateManager()
    states = {
        MAIN_MENU: MainMenu(screen),
        STORY: Story(screen),
    }

    running = True
    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False

        current_state = state_manager.get_state()
        states[current_state].update()
        states[current_state].render()

        pygame.display.flip()
        clock.tick(30)  # Frame rate

    pygame.quit()

if __name__ == "__main__":
    main()
```

## Example Use Cases

### Switching from Main Menu to Story

You can handle transitions between states based on user inputs.

```python
# main_menu.py (updated)
def update(self):
    for event in pygame.event.get():
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_RETURN:  # Press Enter to start the story
                state_manager.set_state(STORY)
```

### Adding More States

You can expand the system by adding new states like Settings or Game Over.

```python
# settings.py
import pygame

class Settings:
    def __init__(self, screen):
        self.screen = screen
        self.font = pygame.font.Font(None, 36)
        self.text = self.font.render('Settings', True, (255, 255, 255))

    def update(self):
        # Handle input and update logic here
        pass

    def render(self):
        self.screen.fill((0, 0, 0))  # Black background
        self.screen.blit(self.text, (50, 50))  # Render text
```

Update ``main.py`` to include the new state:

```python
# main.py (updated)
from settings import Settings
from game_states import SETTINGS

states = {
    MAIN_MENU: MainMenu(screen),
    STORY: Story(screen),
    SETTINGS: Settings(screen),
}
```