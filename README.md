# shooter
from pygame import *
from random import randint
from time import time as timer
win_width = 700
win_height = 500
window = display.set_mode((win_width, win_height))
display.set_caption("Лабиринт")
background = transform.scale(image.load("galaxy.jpg"), (win_width, win_height)) 
mixer.init()
mixer.music.load('space.ogg')
mixer.music.play()
fr=mixer.Sound('fire.ogg')
font.init()
font2=font.SysFont('Arial',36)
win = font2.render('YOU WIN!', True, (255, 215, 0))
lose = font2.render('YOU LOSE!', True, (180, 0, 0))
score=0
lost=0
class GameSprite(sprite.Sprite):
    def init(self, player_image, player_x, player_y,size_x,size_y, player_speed):
        super().init()
        self.image = transform.scale(image.load(player_image), (65, 65))
        self.speed = player_speed
        self.rect = self.image.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y
    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))
class Pla(GameSprite):
    def update(self):
        keys = key.get_pressed()
        if keys[K_LEFT] and self.rect.x > 5:
            self.rect.x -= self.speed
        if keys[K_RIGHT] and self.rect.x < win_width - 80:
            self.rect.x += self.speed
        if keys[K_UP] and self.rect.y > 0:
           self.rect.y -= self.speed
        if keys[K_DOWN] and self.rect.y < win_height - 80:
            self.rect.y += self.speed
    def fire(self):
        ff=Bullet('bullet.png',self.rect.centerx,self.rect.top,5,10,-15)
        bulets.add(ff)
        
class Ene(GameSprite):
    def update(self):
        self.rect.y+=self.speed
        global lost
        if self.rect.y>win_height:
            self.rect.x=randint(80,win_width-80)
            self.rect.y=0
            lost=lost + 1
class Bullet(GameSprite):
    def update(self):
        self.rect.y+=self.speed
        if self.rect.y<0:
            self.kill()

player=Pla('rocket.png', 5, win_height - 100, 80,100,10)
monsterj=sprite.Group()
for i in range(10):
    monster=Ene('ufo.png',randint(80,win_width-80),-40,80,50,randint(1,2))
    monsterj.add(monster)
tarelkas=sprite.Group()
for i in range(2):
    tarelka=Ene('asteroid.png',randint(80,win_width-80),-40,80,50,randint(2,4))
    tarelkas.add(tarelka)    
bulets=sprite.Group()




game = True
finish = False
rel_time=False
num_fire=0
clock = time.Clock()
FPS = 60
while game:
    for e in event.get():
        if e.type == QUIT:
            game = False
        elif e.type== KEYDOWN:
            if e.key==K_SPACE:
                if num_fire<5 and rel_time==False:
                    num_fire=num_fire+1
                    fr.play()
                    player.fire()
                if num_fire>=5 and rel_time==False:
                    u=timer()
                    rel_time=True


                 
    if finish != True:
        window.blit(background,(0, 0))
        t=font2.render('Счёт:'+str(score),1,(255,0,255))
        window.blit(t,(10,20))
        t1=font2.render('Пропущено:'+str(lost),1,(255,0,255))
        window.blit(t1,(10,50))
        player.update()
        player.reset()
        monsterj.update()
        monsterj.draw(window)
        bulets.update()
        bulets.draw(window)
        tarelkas.update()
        tarelkas.draw(window)
    if rel_time==True:
        o=timer()
        if o-u<3:
            p=font2.render('Wait,reload....',1,(150,0,0))
            window.blit(p,(260,460))
        else:
            num_fire=0
            rel_time=False



        





    if sprite.spritecollide(player,monsterj,False):
        finish=True
        window.blit(lose,(250,250))
    if sprite.spritecollide(player,tarelkas,False):
        finish=True
        window.blit(lose,(250,250))        

    if sprite.groupcollide(monsterj,bulets,True,True):
        score+=1
        monster.rect.x=50
        monster.rect.y=0
        
    if lost > 15:
        finish=True
        window.blit(lose,(250,250)) 
    if score==5:
        finish=True
        window.blit(win,(250,250))        

    display.update()
    clock.tick(FPS)
