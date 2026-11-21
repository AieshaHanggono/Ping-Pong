
from pygame import *

class GameSprite(sprite.Sprite):
    def __init__(self, player_image, player_x, player_y, player_speed, size_x, size_y):
        super().__init__()
        self.image = transform.scale(image.load(player_image), (size_x, size_y))
        self.speed = player_speed
        self.rect = self.image.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y

    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))

class Player(GameSprite):
    def update_l(self):
        keys = key.get_pressed()
        if keys[K_w] and self.rect.y > 5:
            self.rect.y -= self.speed
        if keys[K_s] and self.rect.y < win_height - 80:
            self.rect.y += self.speed

    def update_r(self):
        keys = key.get_pressed()
        if keys[K_UP] and self.rect.y > 5:
            self.rect.y -= self.speed
        if keys[K_DOWN] and self.rect.y < win_height - 80:
            self.rect.y += self.speed

back = (241, 230, 100)
win_width = 600
win_height = 500

window = display.set_mode((win_width, win_height))
window.fill(back)

game = True
finish = False
clock = time.Clock()
FPS = 60

# TIMER
start_time = time.get_ticks()

racket1 = Player('line.png', 30, 200, 4, 50, 150)
racket2 = Player('line.png', 520, 200, 4, 50, 150)
ball = GameSprite('ball.png', 200, 200, 4, 50, 50)

font.init()
font = font.Font(None, 35)
lose1 = font.render('Player 1 Lose!', True, (180, 0, 0))
lose2 = font.render('Player 2 Lose!', True, (180, 0, 0))

score_p1 = font.render('Player 1 : 0', True, (0, 0, 0))
score_p2 = font.render('Player 2 : 0', True, (0, 0, 0))

speed_x = 3
speed_y = 3
score1 = 0
score2 = 0

ball_hit = False

while game:
    for e in event.get():
        if e.type == QUIT:
            game = False

    if finish != True:
        window.fill(back)

        current_time = (time.get_ticks() - start_time) // 1000
        minutes = current_time // 60
        seconds = current_time % 60

        timer_text = font.render(
            f'{minutes:02}:{seconds:02}',
            True,
            (0, 0, 0)
        )

        window.blit(timer_text, (win_width // 2 - 25, 20))

        racket1.update_l()
        racket2.update_r()

        ball.rect.x += speed_x
        ball.rect.y += speed_y

        if sprite.collide_rect(racket1, ball) and speed_x < 0:
            speed_x *= -1
            score2 += 1

        elif sprite.collide_rect(racket2, ball) and speed_x > 0:
            speed_x *= -1
            score1 += 1

        score_p1 = font.render('Player 1 : ' + str(score1), True, (0, 0, 0))
        score_p2 = font.render('Player 2 : ' + str(score2), True, (0, 0, 0))

        if ball.rect.y > win_height - 50 or ball.rect.y < 0:
            speed_y *= -1

        if ball.rect.x < 0:
            finish = True
            window.blit(lose1, (200, 200))
            game_over = True

        if ball.rect.x > win_width:
            finish = True
            window.blit(lose2, (200, 200))
            game_over = True

        racket1.reset()
        racket2.reset()
        ball.reset()

        window.blit(score_p1, (20, 20))
        window.blit(score_p2, (420, 20))

    display.update()
    clock.tick(FPS)
