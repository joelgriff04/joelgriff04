import Pygame
import math
from pygame import mixer


#initializer
pygame.mixer.init()
pygame.font.init()
#display w/h
wScreen = 1200
hScreen = 500

#display
win = pygame.display.set_mode((wScreen, hScreen))

#colors
white = (0,0,0)
black = (255,255,255)

#images
golf_flag = pygame.image.load('golf-hole.png').convert_alpha()
golf_flag_scale = pygame.transform.scale(golf_flag, (100,100))
golf_flagX = 800
golf_flagY = 360

#first level bacgkround 
level_1 = pygame.image.load('first_level.png').convert_alpha()
level_1_scale = pygame.transform.scale(level_1, (1200,500))
level_1_rect = level_1_scale.get_rect()



#sound effect
golf_hit = pygame.mixer.Sound('golf_swing.wav')

#creating the ball
class ball(object):
    def __init__(self, x,y,radius, color):
        self.x = x
        self.y = y
        self.radius = radius
        self.color = color

#drawing ball on screen
    def draw(self, win):
        pygame.draw.circle(win, (0,0,0) , (self.x,self.y), self.radius)
        pygame.draw.circle(win, self.color, (self.x,self.y), self.radius-1)

    def ball_motion(startx, starty, power, angle, time):
        velocityX = math.cos(angle) * power 
        velocityY = math.sin(angle) * power

        distX = velocityX * time
        distY = (velocityY * time) + ((-4.9 * (time)**2)/2)

        Value_X = round(distX + startx)
        Value_Y = round(starty - distY)

        return (Value_X, Value_Y)


    
def redrawWindow():
    win.blit(text, (100,100))
    win.blit(level_1_scale, (level_1_rect))
    golfball.draw(win)
    pygame.draw.line(win, (0,0,0), line[0], line[1])
    win.blit(golf_flag_scale, (golf_flagX, golf_flagY))
    pygame.display.update()

def find_angle(pos):
    ballX = golfball.x
    ballY = golfball.y
    try:
        angle = math.atan((ballY - pos[1]) / (ballX - pos[0]))
    except:
        angle = math.pi / 2

    if pos[1] < ballY and pos[0] > ballX:
        angle = abs(angle)
    elif pos[1] < ballY and pos[0] < ballX:
        angle = math.pi - angle
    elif pos[1] > ballY and pos[0] < ballX:
        angle = math.pi + abs(angle)
    elif pos[1] > ballY and pos[0] > ballX:
        angle = (math.pi * 2) - angle

    return angle



golfball = ball(250,494, 5, (255,255,255))

#global
x = 0
y = 0
time = 0
power = 0
angle = 0
shoot = False
stroke_score = 0


font = pygame.font.Font(None, 30)
text = font.render("Stroke: "+str(stroke_score), False, white)

run = True
while run:
    if shoot:
        if golfball.y < 500 - golfball.radius:
            time += 0.02
            po = ball.ball_motion(x, y, power, angle, time)
            golfball.x = po[0]
            golfball.y = po[1]
        else:
            shoot = False
            golfball.y = 494
            
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            run = False
        if event.type == pygame.MOUSEBUTTONDOWN:
            if shoot == False:
                shoot = True
                stroke_score +=1
                golf_hit.play()
                x = golfball.x
                y = golfball.y
                time = 0
                power_fl = math.sqrt((line[1][1] - line[0][1])**2 + (line[1][0]-line[0][0])**2)/8
                power = round(power_fl)
                angle = find_angle(pos)
                print(angle)
                print(power)
                
                
    pos = pygame.mouse.get_pos()
    line = [(golfball.x, golfball.y), pos]
    redrawWindow()

pygame.quit()
