# ping-pong
from pygame import *
backgroud = (200, 255, 255)
win_w = 600
win_h = 500
font.init()
font1 = font.SysFont('Arial', 40)
font_win = font.SysFont('Arial', 72)
class GameSprite(sprite.Sprite):
    def __init__(self, player_image, player_x, player_y, player_speed, w, h):
        super().__init__()
        self.image = transform.scale(image.load(player_image), (w, h))
        self.speed = player_speed
        self.rect = self.image.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y
    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))
class Paddle(GameSprite):
    def __init__(self, image, x, y, speed, up_key, down_key, w=20, h=100):
        super().__init__(image, x, y, speed, w, h)
        self.up_key = up_key
        self.down_key = down_key
    def update(self):
        keys_pressed = key.get_pressed()
        if keys_pressed[self.up_key] and self.rect.y > 0:
            self.rect.y -= self.speed
        if keys_pressed[self.down_key] and self.rect.y < win_h - self.rect.height:
            self.rect.y += self.speed
        self.reset()
class Ball(GameSprite):
    def __init__(self, image, x, y, speed, w, h):
        super().__init__(image, x, y, speed, w, h)
        self.dx = 4
        self.dy = 3
    def update(self):
        self.rect.x += self.dx
        self.rect.y += self.dy
        if self.rect.y <= 0 or self.rect.y >= win_h - self.rect.height:
            self.dy *= -1
        if self.rect.colliderect(player1.rect) and self.dx < 0:
            self.dx *= -1
        if self.rect.colliderect(player2.rect) and self.dx > 0:
            self.dx *= -1
        self.reset()
    def reset_position(self):
        self.rect.x = 290
        self.rect.y = 240
        self.dx *= -1  
window = display.set_mode((win_w, win_h))
window.fill(backgroud)
clock = time.Clock()
FPS = 60
game = True
finish = False
score1 = 0   
score2 = 0   
WIN_SCORE = 5
player1 = Paddle('paddle_.png', 10, 200, 6, K_UP, K_DOWN)
player2 = Paddle('paddle_.png', win_w - 30, 200, 6, K_w, K_s)
ball = Ball('tennis_ball.png', 290, 240, 4, 50, 50)
while game:
    for e in event.get():
        if e.type == QUIT:
            game = False
        if e.type == KEYDOWN and finish and e.key == K_r:
            score1 = 0
            score2 = 0
            finish = False
            player1.rect.y = 200
            player2.rect.y = 200
            ball.reset_position()
    window.fill(backgroud)
    if not finish:
        player1.update()
        player2.update()
        ball.update()
        if ball.rect.x < -ball.rect.width:
            score2 += 1
            ball.reset_position()
        if ball.rect.x > win_w:
            score1 += 1
            ball.reset_position()
        if score1 >= WIN_SCORE or score2 >= WIN_SCORE:
            finish = True
        score_text = font1.render(f'{score1} : {score2}', True, (0, 0, 100))
        window.blit(score_text, (win_w // 2 - score_text.get_width() // 2, 10))
    else:
        if score1 >= WIN_SCORE:
            win_text = font_win.render('ИГРОК 1 ПОБЕДИЛ!', True, (0, 150, 0))
        else:
            win_text = font_win.render('ИГРОК 2 ПОБЕДИЛ!', True, (150, 0, 0))
        window.blit(win_text, (win_w // 2 - win_text.get_width() // 2, 200))
        hint = font1.render('Нажмите R для новой игры', True, (0, 0, 0))
        window.blit(hint, (win_w // 2 - hint.get_width() // 2, 300))
        score_text = font1.render(f'{score1} : {score2}', True, (0, 0, 100))
        window.blit(score_text, (win_w // 2 - score_text.get_width() // 2, 10))
    display.update()
    clock.tick(FPS)
