# Importerar matematik till pygame
import math
# Importerar random funktionen till pygame
import random

# Importerar pygame
import pygame
# Importerar mixer funktionen/ljud funktionen till pygame
from pygame import mixer

# Initialisera pygame
pygame.init()

# Skapar en spelruta som är 1540 x 805
screen = pygame.display.set_mode((1540, 805))

# Laddar in backgrundsbilden till spelskärmen
background = pygame.image.load('background1.jpg')

# Laddar in bakgrundsmusiken som jag laddat ner
mixer.music.load('bakgrundsmusik.mp3')
mixer.music.play(-1)

# Skapar ett namn och inför en bild till spelet
pygame.display.set_caption("Kick The Boss")
icon = pygame.image.load('shooting1.png')
pygame.display.set_icon(icon)

# Laddar in pistolens bild i spelet och lägger pistolen i dess X samt Y koordinater när man startar upp spelet
pistolImg = pygame.image.load('shooting1.png')
pistolX = 268
pistolY = 178
pistolX_change = 0
pistolY_change = 0

# Laddar in bossen, bossen spawnas helt random mellan dess Y och X koordinater samt gör så att bossen bara kan gå upp
# och ner, inte höger och vänster
bossImg = pygame.image.load('healthy1.png')
bossX = random.randint(1155, 1284)
bossY = random.randint(0, 544)
bossX_change = 0
bossY_change = 2.5

# Skottets funktion
# Ready = Skotten är redo men syntes ej på skärmen
# Fire = Skottet är avlossat
bulletImg = pygame.image.load('bullet1.png')
bulletX = 268
bulletY = 178
bulletX_change = 8
bulletY_change = 0
bullet_state = "ready"

# Score / Poäng
# Här lägger jag till poäng funktionen, och laddar in ett teckensnitt samt skapade x och y koordinaterna för score
# där jag vill att det ska synas
score_value = 0
font = pygame.font.Font('freesansbold.ttf', 50)
textX = 10
textY = 10


# Jag tolkar teckensnittet och framställde den på skärmen när man spelar
# Jag tolkade även 3 saker, poäng, true och färgen till texten (RGB- RÖD - GRÖN - BLÅ)
def show_score(x, y):
    score = font.render("Score: " + str(score_value), True, (0, 0, 255))
    screen.blit(score, (x, y))


# Definierar pistolen och laddar upp den på skärmen
def pistol(x, y):
    screen.blit(pistolImg, (x, y))


# Definierar bossen och laddar upp den på skärmen
def boss(x, y):
    screen.blit(bossImg, (x, y))


# Definierar hur skottet funkar, är skottets tillstånd redo och skjuts så börjar skottet skjuta 80 pixlar från pistolens
# x-koordinat och 25 pixlar från pistolens y-koordinat
def fire_bullet(x, y):
    global bullet_state
    bullet_state = "fire"
    screen.blit(bulletImg, (x + 80, y + 25))


# Definierar rottecknet till spelet som hjälper till vid avståndsformeln
def isCollision(bossX, bossY, skottX, skottY):
    distance = math.sqrt(math.pow(bossX - skottX, 2) + math.pow(bossY - skottY, 2))
    if distance < 27:
        return True
    else:
        return False


# Här börjar spelets loop
running = True
# Under tiden spelet är på så händer detta:
while running:

    # RGB - Röd, Grön, Blå
    screen.fill((0, 0, 0))
    # laddar in bakgrunden till spelet
    screen.blit(background, (0, 0))
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        # Om tangenttryckningen trycks ned kontrollera om den är höger eller vänster
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_a:
                pistolX_change = -3
            if event.key == pygame.K_d:
                pistolX_change = 3
            if event.key == pygame.K_SPACE:
                if bullet_state == "ready":
                    # När skottet är redo och skjuts så kommer ett skottljud
                    bullet_Sound = mixer.Sound('bulletsound.wav.mp3')
                    bullet_Sound.play()
                    # Får den aktuella x-koordinaten för pistolen
                    bulletY = pistolY
                    fire_bullet(bulletX, bulletY)
        # Om tangenten är uppe/släppt så ska hastigheten bli 0, dvs pistolens rörelse ska stanna
        if event.type == pygame.KEYUP:
            if event.key == pygame.K_a or event.key == pygame.K_d:
                pistolX_change = 0

        # Om tangenttryckningen trycks, kontrollera om den är upp eller ner
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_w:
                pistolY_change = -3
            if event.key == pygame.K_s:
                pistolY_change = 3
        # Om tangenten är uppe/släppt så ska hastigheten bli 0, dvs pistolens rörelse ska stanna
        if event.type == pygame.KEYUP:
            if event.key == pygame.K_w or event.key == pygame.K_s:
                pistolY_change = 0

    # X = X + -0.1 -> 5 = 5 - 0.1
    # X = X + 0.1

    # Kollar så att pistolenX inte går ut genom mappen
    pistolX += pistolX_change
    # Får pistolen ett negativt X värde så ska pistolen teleporteras till 0 igen
    if pistolX <= 0:
        pistolX = 0
    # Får pistolen ett X värde på över 1000 så ska pistolen teleporteras till 1000 igen
    elif pistolX >= 1000:
        pistolX = 1000

    # Kollar så att pistolenY inte går ut genom mappen
    pistolY += pistolY_change
    # Får pistolen ett negativt Y värde så ska pistolen teleporteras till 0 igen
    if pistolY <= 0:
        pistolY = 0
    # Får pistolen ett Y värde på över 672 så ska pistolen teleporteras till 672 igen
    elif pistolY >= 672:
        pistolY = 672

    # Bossens rörelse
    bossY += bossY_change
    # Om bossen får en negativ koordinat, alltså mindre än 0 så ändras bossen till ett positivt koordinat för att den
    # ska stanna i skärmen
    if bossY <= 0:
        bossY_change = 1
    # Om bossen får en koordinat som är över 544 på y-axeln så ska bossens rörelse gå -1 så att den går tillbaka upp
    elif bossY >= 544:
        bossY_change = -1

    # Y = Y + -0.1 -> 5 = 5 - 0.1
    # Y = Y + 0.1
    
    # Skottets rörelse
    # Om skottets koordinat blir mer än 1540 på x-axeln så ska den ändras tillbaka till 1540, och då ska skottets
    # tillstånd ändras tillbaka till redo för att kunna skjuta igen
    if bulletX >= 1540:
        bulletX = 1540
        bullet_state = "ready"

    # När skottets är skjutet så ska skottets rörelse anpassas med x-axeln, dvs den ska ökas i x-led
    if bullet_state == "fire":
        fire_bullet(bulletX, bulletY)
        bulletX += bulletX_change

    # Om skottets tillstånd är redo då ska skottets koordinat ändras tillbaka till pistolens koordinat, dvs
    # skottets x-koordinat ska vara på pistolens x-koordinat, likaså med y-koordinaten flr skottet och pistolen.
    if bullet_state == "ready":
        bulletX = pistolX
        bulletY = pistolY

    # Kollision / Sammanstötning
    # Varje gång man träffar bossens huvud eller hals så ska poängen öka med 1
    collision = isCollision(bossX, bossY, bulletX, bulletY)
    if collision:
        # När man träffar bossen ska ljud höras
        hitmarker_Sound = mixer.Sound('hitmarker_2.mp3')
        hitmarker_Sound.play()
        skottY = 480
        bullet_state = "ready"
        # Träffar man bossen ska poängen ökas med 1
        score_value += 1
        # När man träffar bossen ska bossen spawnas på nytt på random ställen som är okej och som är nämnt under
        bossX = random.randint(1155, 1284)
        bossY = random.randint(0, 544)

    pistol(pistolX, pistolY)
    boss(bossX, bossY)
    show_score(textX, textY)
    # Uppdaterar skärmen/spelet hela tiden så att allt kommer på skärmen
    pygame.display.update()
