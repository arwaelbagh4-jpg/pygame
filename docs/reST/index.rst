import pygame
from settings import SCREEN_WIDTH, SCREEN_HEIGHT, COLORS
from core.ui import Button

class TitleScene:
    def __init__(self, game):
        self.game = game
        self.font = pygame.font.SysFont("arial", 32)
        self.title_font = pygame.font.SysFont("arial", 64, bold=True)

        self.buttons = [
            Button((SCREEN_WIDTH//2 - 120, SCREEN_HEIGHT//2 - 80, 240, 60), "AVATAR",
                   self.font, self.choose_avatar),
            Button((SCREEN_WIDTH//2 - 120, SCREEN_HEIGHT//2, 240, 60), "LEVEL",
                   self.font, self.choose_level),
            Button((SCREEN_WIDTH//2 - 120, SCREEN_HEIGHT//2 + 80, 240, 60), "PLAY",
                   self.font, self.start_game)
        ]

        self.avatar = None
        self.level = None

    def on_enter(self):
        self.game.play_bgm("title")

    def on_exit(self): pass

    def choose_avatar(self):
        from scenes.avatar_scene import AvatarScene
        self.game.manager.push(AvatarScene(self.game, self))

    def choose_level(self):
        from scenes.level_scene import LevelScene
        self.game.manager.push(LevelScene(self.game, self))

    def start_game(self):
        if self.avatar and self.level:
            from scenes.world_scene import WorldScene
            self.game.manager.replace(WorldScene(self.game, self.avatar, self.level))

    def handle_event(self, event):
        for b in self.buttons:
            b.handle_event(event)

    def update(self, dt): pass

    def draw(self, surface):
        surface.fill(COLORS["bg"])
        title = self.title_font.render("ILLIT Adventure", True, COLORS["ink"])
        surface.blit(title, title.get_rect(center=(SCREEN_WIDTH//2, SCREEN_HEIGHT//3)))
        for b in self.buttons:
            b.draw(surface)
import pygame
from settings import SCREEN_WIDTH, SCREEN_HEIGHT, COLORS
from core.ui import DialogueBox

class AvatarScene:
    def __init__(self, game, parent_scene):
        self.game = game
        self.parent = parent_scene
        self.font = pygame.font.SysFont("arial", 28)
        self.dialogue = DialogueBox(pygame.Rect(40, SCREEN_HEIGHT - 140, SCREEN_WIDTH - 80, 100), self.font)
        self.dialogue.set_lines([
            "Hi, I'm Yuna!",
            "Welcome to ILLIT Adventure.",
            "Let’s explore together!"
        ])
        self.show_dialogue = True

    def on_enter(self): pass
    def on_exit(self): pass

    def handle_event(self, event):
        if event.type == pygame.KEYDOWN and event.key in (pygame.K_SPACE, pygame.K_RETURN):
            self.dialogue.advance()
            if self.dialogue.index == len(self.dialogue.lines) - 1:
                self.parent.avatar = "Yuna"
                self.game.manager.pop()  # return to TitleScene

    def update(self, dt): pass

    def draw(self, surface):
        surface.fill(COLORS["bg"])
        self.dialogue.draw(surface)
import pygame from settings import SCREEN_WIDTH, SCREEN_HEIGHT, COLORS class LevelScene: def __init__(self, game, parent_scene): self.game = game self.parent = parent_scene self.font = pygame.font.SysFont("arial", 28) self.input_text = "" def on_enter(self): pass def on_exit(self): pass def handle_event(self, event): if event.type == pygame.KEYDOWN: if event.key == pygame.K_RETURN: self.parent.level = self.input_text self.game.manager.pop() elif event.key == pygame.K_BACKSPACE: self.input_text = self.input_text[:-1] else: self.input_text += event.unicode def update(self, dt): pass def draw(self, surface): surface.fill(COLORS["bg"]) label = self.font.render("Enter your level:", True, COLORS["ink"]) surface.blit(label, (SCREEN_WIDTH//2 - 100, SCREEN_HEIGHT//2 - 40)) text = self.font.render(self.input_text, True, COLORS["accent"]) surface.blit(text, (SCREEN_WIDTH//2 - 100, SCREEN_HEIGHT//2))
