cd /path/to/your/project
git init
touch .gitignore
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*.so

# Virtual environment
venv/
env/

# Pygame assets (optional, if you store them separately)
assets/

# Editor-specific settings (optional)
.idea/
.vscode/
git add .
git commit -m "Initial commit of battle game with power-ups"
git remote add origin https://github.com/your-username/your-repository.git
git push -u origin master
battle_game/
├── assets/
│   ├── powerup.png
│   └── ...
├── game/
│   ├── __init__.py
│   ├── game.py
│   └── player.py
├── main.py
├── .gitignore
└── README.md
import pygame
from game.game import Game

def main():
    pygame.init()
    game = Game()
    game.run()

if __name__ == "__main__":
    main()
import pygame
import random
from game.player import Player

# Constants
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
FPS = 30
POWERUP_SPAWN_TIME = 200  # Every 200 frames, a new power-up spawns

class Game:
    def __init__(self):
        self.screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
        self.clock = pygame.time.Clock()
        self.players = [Player("Player 1", RED, (50, SCREEN_HEIGHT // 2 - 25)), 
                        Player("Player 2", BLUE, (SCREEN_WIDTH - 100, SCREEN_HEIGHT // 2 - 25))]
        self.powerups = []
        self.frame_count = 0

    def draw_game(self):
        self.screen.fill(WHITE)
        for player in self.players:
            player.draw(self.screen)
            for bullet in player.bullets:
                pygame.draw.rect(self.screen, player.color, (bullet[0], bullet[1], 10, 5))

        # Draw power-ups
        for powerup in self.powerups:
            pygame.draw.rect(self.screen, powerup['color'], (powerup['x'], powerup['y'], 30, 30))

        pygame.display.flip()

    def spawn_powerup(self):
        powerup_x = random.randint(50, SCREEN_WIDTH - 50)
        powerup_y = random.randint(50, SCREEN_HEIGHT - 50)
        powerup_type = random.choice(['speed', 'strength', 'health'])
        color = GREEN if powerup_type == 'speed' else YELLOW if powerup_type == 'strength' else PURPLE
        self.powerups.append({'x': powerup_x, 'y': powerup_y, 'type': powerup_type, 'color': color})

    def run(self):
        running = True
        while running:
            self.frame_count += 1
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    running = False

            # Update and handle player actions
            for player in self.players:
                player.update()

            # Spawn power-ups
            if self.frame_count % POWERUP_SPAWN_TIME == 0:
                self.spawn_powerup()

            # Draw everything
            self.draw_game()

            self.clock.tick(FPS)

        pygame.quit()
import pygame

# Player settings
BASE_SPEED = 5
MAX_HEALTH = 10

class Player:
    def __init__(self, name, color, position):
        self.name = name
        self.color = color
        self.x, self.y = position
        self.health = MAX_HEALTH
        self.bullets = []
        self.speed = BASE_SPEED
        self.strength = 1

    def draw(self, screen):
        pygame.draw.rect(screen, self.color, (self.x, self.y, 50, 50))

    def update(self):
        keys = pygame.key.get_pressed()
        # Controls for Player 1
        if self.name == "Player 1":
            if keys[pygame.K_w] and self.y > 0:
                self.y -= self.speed
            if keys[pygame.K_s] and self.y < 600 - 50:
                self.y += self.speed
            if keys[pygame.K_a] and self.x > 0:
                self.x -= self.speed
            if keys[pygame.K_d] and self.x < 800 // 2 - 50:
                self.x += self.speed
            if keys[pygame.K_SPACE]:
                self.bullets.append([self.x + 50, self.y + 25])

        # Controls for Player 2
        elif self.name == "Player 2":
            if keys[pygame.K_UP] and self.y > 0:
                self.y -= self.speed
            if keys[pygame.K_DOWN] and self.y < 600 - 50:
                self.y += self.speed
            if keys[pygame.K_LEFT] and self.x > 800 // 2:
                self.x -= self.speed
            if keys[pygame.K_RIGHT] and self.x < 800 - 50:
                self.x += self.speed
            if keys[pygame.K_RSHIFT]:
                self.bullets.append([self.x - 10, self.y + 25])

# Battle Game

A simple 2D battle game made with Pygame where two players can move around, shoot bullets, and collect power-ups.

## How to Run

1. Install Pygame:
   ```bash
   pip install pygame
python main.py
