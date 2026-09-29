import random

print("🎮 Игра «Угадай число»")
print("Я загадал число от 1 до 100.")

secret_number = random.randint(1, 100)
attempts = 0

while True:
    try:
        guess = int(input("Введи свою догадку: "))
        attempts += 1

        if guess < secret_number:
            print("📈 Моё число больше!")
        elif guess > secret_number:
            print("📉 Моё число меньше!")
        else:
            print(f"🎉 Поздравляю! Ты угадал число {secret_number}!")
            print(f"Количество попыток: {attempts}")
            break

    except ValueError:
        print("❌ Пожалуйста, введи целое число.")
  После этого кода будем дорабатывать данный код намного лучше,
  буде также код по другой игре однако незнаю на сколько выйдет он хороший
  
