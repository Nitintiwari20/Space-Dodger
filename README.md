import turtle
import random
import time

# Screen setup
wn = turtle.Screen()
wn.title("Space Dodger")
wn.bgcolor("black")
wn.setup(width=800, height=600)
wn.tracer(0)

# Score
score = 0
high_score = 0
speed = 15

# Player
player = turtle.Turtle()
player.shape("triangle")
player.color("cyan")
player.penup()
player.goto(0, -250)
player.setheading(90)

# Bullet
bullet = turtle.Turtle()
bullet.shape("square")
bullet.color("yellow")
bullet.shapesize(stretch_wid=0.3, stretch_len=1)
bullet.penup()
bullet.hideturtle()
bullet_speed = 30

bullet_state = "ready"

# Enemies
enemies = []
for _ in range(6):
    enemy = turtle.Turtle()
    enemy.shape("circle")
    enemy.color("red")
    enemy.penup()
    x = random.randint(-350, 350)
    y = random.randint(100, 250)
    enemy.goto(x, y)
    enemies.append(enemy)

# Scoreboard
pen = turtle.Turtle()
pen.speed(0)
pen.color("white")
pen.penup()
pen.hideturtle()
pen.goto(-380, 260)
pen.write(f"Score: {score}  High Score: {high_score}", font=("Arial", 16, "bold"))

# Functions

def move_left():
    x = player.xcor()
    x -= speed
    if x < -370:
        x = -370
    player.setx(x)


def move_right():
    x = player.xcor()
    x += speed
    if x > 370:
        x = 370
    player.setx(x)


def fire_bullet():
    global bullet_state
    if bullet_state == "ready":
        bullet_state = "fire"
        bullet.goto(player.xcor(), player.ycor() + 10)
        bullet.showturtle()


def is_collision(t1, t2):
    distance = t1.distance(t2)
    return distance < 20


# Keyboard bindings
wn.listen()
wn.onkeypress(move_left, "Left")
wn.onkeypress(move_right, "Right")
wn.onkeypress(fire_bullet, "space")

# Game loop
while True:
    wn.update()

    # Move enemies
    for enemy in enemies:
        y = enemy.ycor()
        y -= 2
        enemy.sety(y)

        # Respawn enemy
        if enemy.ycor() < -300:
            x = random.randint(-350, 350)
            y = random.randint(200, 300)
            enemy.goto(x, y)

        # Collision with player
        if is_collision(player, enemy):
            player.goto(0, -250)
            time.sleep(1)
            score = 0
            pen.clear()
            pen.write(f"Score: {score}  High Score: {high_score}", font=("Arial", 16, "bold"))
            enemy.goto(random.randint(-350, 350), random.randint(200, 300))

        # Collision with bullet
        if is_collision(bullet, enemy):
            bullet.hideturtle()
            bullet_state = "ready"
            bullet.goto(0, -400)

            enemy.goto(random.randint(-350, 350), random.randint(200, 300))

            score += 10
            if score > high_score:
                high_score = score

            pen.clear()
            pen.write(f"Score: {score}  High Score: {high_score}", font=("Arial", 16, "bold"))

    # Move bullet
    if bullet_state == "fire":
        y = bullet.ycor()
        y += bullet_speed
        bullet.sety(y)

    # Reset bullet
    if bullet.ycor() > 300:
        bullet.hideturtle()
        bullet_state = "ready"
        
wn.mainloop()

