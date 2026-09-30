import random

print("╔══════════════════════════════╗")
print("║      🎮 УГАДАЙ ЧИСЛО 🎮      ║")
print("╚══════════════════════════════╝")

while True:
    print("\nВыбери уровень сложности:")
    print("1 — 🟢 Легко    (1–50, 10 попыток)")
    print("2 — 🟡 Средне   (1–100, 7 попыток)")
    print("3 — 🔴 Сложно   (1–500, 10 попыток)")

    try:
        level = int(input("\n👉 Твой выбор: "))

        if level == 1:
            max_number = 50
            max_attempts = 10
        elif level == 2:
            max_number = 100
            max_attempts = 7
        elif level == 3:
            max_number = 500
            max_attempts = 10
        else:
            print("❌ Выбери 1, 2 или 3!")
            continue

        secret_number = random.randint(1, max_number)
        attempts = 0

        print(f"\n🎯 Я загадал число от 1 до {max_number}.")
        print(f"У тебя есть {max_attempts} попыток!")

        while attempts < max_attempts:
            try:
                guess = int(input("\n🔢 Введи число: "))

                if guess < 1 or guess > max_number:
                    print(f"⚠️ Введи число от 1 до {max_number}!")
                    continue

                attempts += 1
                remaining = max_attempts - attempts

                if guess < secret_number:
                    print("📈 Моё число БОЛЬШЕ!")

                elif guess > secret_number:
                    print("📉 Моё число МЕНЬШЕ!")

                else:
                    # Чем меньше попыток — тем больше очков
                    score = (max_attempts - attempts + 1) * 100

                    print("\n🎉🎉🎉 ПОБЕДА! 🎉🎉🎉")
                    print(f"🏆 Ты угадал число: {secret_number}")
                    print(f"🔢 Использовано попыток: {attempts}")
                    print(f"⭐ Твои очки: {score}")

                    break

                if remaining > 0:
                    print(f"❤️ Осталось попыток: {remaining}")

                    # Дополнительная подсказка
                    difference = abs(secret_number - guess)

                    if difference <= 5:
                        print("🔥 Очень близко!")
                    elif difference <= 15:
                        print("🙂 Уже близко!")
                    else:
                        print("❄️ Пока далеко!")

            except ValueError:
                print("❌ Нужно ввести целое число!")

        else:
            print("\n💀 Попытки закончились!")
            print(f"😈 Я загадал число: {secret_number}")

        # Игра заново
        again = input("\n🔄 Сыграть ещё раз? (да/нет): ").lower()

        if again not in ("да", "д", "yes", "y"):
            print("\n👋 Спасибо за игру!")
            print("До встречи! 🎮")
            break

    except ValueError:
        print("❌ Введи номер уровня: 1, 2 или 3.")
