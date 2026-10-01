```python
import random
import json
import os
import time

# ============================================================
# 🎮 УГАДАЙ ЧИСЛО — ULTIMATE EDITION
# ============================================================

SAVE_FILE = "player_data.json"


# ============================================================
# 🎨 ЦВЕТА ТЕРМИНАЛА
# ============================================================

RESET = "\033[0m"
RED = "\033[91m"
GREEN = "\033[92m"
YELLOW = "\033[93m"
BLUE = "\033[94m"
MAGENTA = "\033[95m"
CYAN = "\033[96m"
WHITE = "\033[97m"


# ============================================================
# 💾 СОХРАНЕНИЕ
# ============================================================

def load_data():
    if os.path.exists(SAVE_FILE):
        try:
            with open(SAVE_FILE, "r", encoding="utf-8") as file:
                return json.load(file)
        except:
            pass

    return {
        "coins": 100,
        "xp": 0,
        "level": 1,
        "wins": 0,
        "losses": 0,
        "best_attempts": None,
        "win_streak": 0,
        "best_streak": 0,
        "games": 0,
        "hints": 2
    }


def save_data(data):
    with open(SAVE_FILE, "w", encoding="utf-8") as file:
        json.dump(data, file, indent=4, ensure_ascii=False)


player = load_data()


# ============================================================
# ⭐ СИСТЕМА УРОВНЕЙ
# ============================================================

def add_xp(amount):
    player["xp"] += amount

    needed_xp = player["level"] * 100

    if player["xp"] >= needed_xp:
        player["xp"] -= needed_xp
        player["level"] += 1

        print()
        print(GREEN + "🎉 НОВЫЙ УРОВЕНЬ!" + RESET)
        print(f"⭐ Теперь твой уровень: {player['level']}")
        print("💰 Бонус: +50 монет!")

        player["coins"] += 50


# ============================================================
# 🖥️ ЗАГОЛОВОК
# ============================================================

def title():
    print(CYAN)
    print("╔════════════════════════════════════════════╗")
    print("║          🎮 УГАДАЙ ЧИСЛО 🎮              ║")
    print("║             ULTIMATE EDITION              ║")
    print("╚════════════════════════════════════════════╝")
    print(RESET)


# ============================================================
# 👤 ПРОФИЛЬ
# ============================================================

def profile():
    print()
    print(MAGENTA + "╔════════════ 👤 ПРОФИЛЬ ════════════╗" + RESET)
    print(f"⭐ Уровень: {player['level']}")
    print(f"✨ XP: {player['xp']}/{player['level'] * 100}")
    print(f"💰 Монеты: {player['coins']}")
    print(f"🎯 Побед: {player['wins']}")
    print(f"💀 Поражений: {player['losses']}")
    print(f"🔥 Лучшая серия: {player['best_streak']}")
    print(f"🎮 Всего игр: {player['games']}")

    if player["best_attempts"] is None:
        print("🏆 Рекорд: пока нет")
    else:
        print(f"🏆 Рекорд: {player['best_attempts']} попыток")

    print(MAGENTA + "╚════════════════════════════════════╝" + RESET)


# ============================================================
# 🛒 МАГАЗИН
# ============================================================

def shop():
    while True:
        print()
        print(YELLOW + "╔════════════ 🛒 МАГАЗИН ════════════╗" + RESET)
        print(f"💰 Твои монеты: {player['coins']}")
        print()
        print("1. 🔮 Купить подсказку — 30 монет")
        print("2. ❤️ Купить дополнительную попытку — 50 монет")
        print("3. 💎 Купить 100 XP — 80 монет")
        print("4. 🚪 Выйти")

        choice = input("\n👉 Выбери товар: ")

        if choice == "1":
            if player["coins"] >= 30:
                player["coins"] -= 30
                player["hints"] += 1
                print(GREEN + "✅ Подсказка куплена!" + RESET)
            else:
                print(RED + "❌ Недостаточно монет!" + RESET)

        elif choice == "2":
            if player["coins"] >= 50:
                player["coins"] -= 50
                player["extra_attempt"] = player.get("extra_attempt", 0) + 1
                print(GREEN + "✅ Дополнительная попытка куплена!" + RESET)
            else:
                print(RED + "❌ Недостаточно монет!" + RESET)

        elif choice == "3":
            if player["coins"] >= 80:
                player["coins"] -= 80
                add_xp(100)
                print(GREEN + "✅ Получено 100 XP!" + RESET)
            else:
                print(RED + "❌ Недостаточно монет!" + RESET)

        elif choice == "4":
            break

        else:
            print("❌ Неверный выбор.")

        save_data(player)


# ============================================================
# 🎯 ВЫБОР РЕЖИМА
# ============================================================

def choose_mode():
    print()
    print(BLUE + "╔════════════ 🎯 РЕЖИМЫ ════════════╗" + RESET)
    print("1. 🟢 Легко")
    print("2. 🟡 Средне")
    print("3. 🔴 Сложно")
    print("4. 💀 Безумие")
    print("5. 🎲 Хаос")

    while True:
        choice = input("\n👉 Выбери режим: ")

        if choice == "1":
            return "Легко", 50, 10

        if choice == "2":
            return "Средне", 100, 7

        if choice == "3":
            return "Сложно", 500, 10

        if choice == "4":
            return "Безумие", 1000, 8

        if choice == "5":
            maximum = random.randint(100, 5000)
            attempts = random.randint(5, 12)
            return "Хаос", maximum, attempts

        print("❌ Выбери число от 1 до 5.")


# ============================================================
# 🔮 ПОДСКАЗКА
# ============================================================

def use_hint(secret_number, maximum):
    if player["hints"] <= 0:
        print(RED + "❌ У тебя нет подсказок!" + RESET)
        return

    player["hints"] -= 1

    hint_type = random.randint(1, 4)

    if hint_type == 1:
        if secret_number % 2 == 0:
            print("🔮 Подсказка: число ЧЁТНОЕ.")
        else:
            print("🔮 Подсказка: число НЕЧЁТНОЕ.")

    elif hint_type == 2:
        if secret_number <= maximum // 2:
            print("🔮 Подсказка: число находится в ПЕРВОЙ половине диапазона.")
        else:
            print("🔮 Подсказка: число находится во ВТОРОЙ половине диапазона.")

    elif hint_type == 3:
        if secret_number % 5 == 0:
            print("🔮 Подсказка: число делится на 5.")
        else:
            print("🔮 Подсказка: число НЕ делится на 5.")

    else:
        number = random.randint(1, maximum)
        print(f"🔮 Магическая подсказка: попробуй число около {number}.")

    print(f"🔮 Осталось подсказок: {player['hints']}")


# ============================================================
# 🎮 ОСНОВНАЯ ИГРА
# ============================================================

def play_game():

    mode, maximum, max_attempts = choose_mode()

    secret_number = random.randint(1, maximum)

    extra = player.get("extra_attempt", 0)

    if extra > 0:
        max_attempts += extra
        player["extra_attempt"] = 0

    attempts = 0

    print()
    print(GREEN + f"🎮 Режим: {mode}" + RESET)
    print(f"🎯 Диапазон: 1–{maximum}")
    print(f"❤️ Попыток: {max_attempts}")

    while attempts < max_attempts:

        print()
        print(f"❤️ Осталось попыток: {max_attempts - attempts}")
        print(f"🔮 Подсказок: {player['hints']}")

        user_input = input("🔢 Введи число или напиши 'hint': ")

        if user_input.lower() == "hint":
            use_hint(secret_number, maximum)
            continue

        try:
            guess = int(user_input)

        except ValueError:
            print(RED + "❌ Введи целое число!" + RESET)
            continue

        if guess < 1 or guess > maximum:
            print(RED + f"❌ Число должно быть от 1 до {maximum}!" + RESET)
            continue

        attempts += 1

        # 🎯 Угадал
        if guess == secret_number:

            print()
            print(GREEN + "╔════════════════════════════════╗" + RESET)
            print(GREEN + "║        🎉 ПОБЕДА! 🎉          ║" + RESET)
            print(GREEN + "╚════════════════════════════════╝" + RESET)

            print(f"🎯 Загаданное число: {secret_number}")
            print(f"🔢 Попыток: {attempts}")

            # Очки
            score = max(10, (max_attempts - attempts + 1) * 20)

            # Бонус за сложность
            if maximum >= 1000:
                score *= 3
            elif maximum >= 500:
                score *= 2

            coins = score // 5

            print(f"⭐ Получено XP: {score}")
            print(f"💰 Получено монет: {coins}")

            player["coins"] += coins
            player["wins"] += 1
            player["games"] += 1
            player["win_streak"] += 1

            if player["win_streak"] > player["best_streak"]:
                player["best_streak"] = player["win_streak"]

            if (
                player["best_attempts"] is None
                or attempts < player["best_attempts"]
            ):
                player["best_attempts"] = attempts
                print(YELLOW + "🏆 НОВЫЙ РЕКОРД!" + RESET)

            add_xp(score)

            save_data(player)

            return

        # 📈 Число больше
        if guess < secret_number:
            print(BLUE + "📈 Моё число БОЛЬШЕ!" + RESET)

        # 📉 Число меньше
        else:
            print(YELLOW + "📉 Моё число МЕНЬШЕ!" + RESET)

        # 🔥 Расстояние до ответа
        difference = abs(secret_number - guess)

        if difference <= 3:
            print(RED + "🔥 ОЧЕНЬ ГОРЯЧО!" + RESET)

        elif difference <= 10:
            print(YELLOW + "🔥 Горячо!" + RESET)

        elif difference <= maximum * 0.25:
            print("🙂 Тепло.")

        else:
            print(CYAN + "❄️ Холодно..." + RESET)

    # 💀 Проигрыш

    print()
    print(RED + "╔════════════════════════════════╗" + RESET)
    print(RED + "║       💀 ТЫ ПРОИГРАЛ 💀       ║" + RESET)
    print(RED + "╚════════════════════════════════╝" + RESET)

    print(f"😈 Загаданное число было: {secret_number}")

    player["losses"] += 1
    player["games"] += 1
    player["win_streak"] = 0

    save_data(player)


# ============================================================
# 📜 ПРАВИЛА
# ============================================================

def rules():
    print()
    print(CYAN + "╔════════════ 📜 ПРАВИЛА ════════════╗" + RESET)

    print("""
🎯 Твоя задача — угадать загаданное число.

📈 Если твоё число меньше — игра сообщит об этом.
📉 Если твоё число больше — игра сообщит об этом.

🔥 Чем ближе число, тем горячее подсказка.

🔮 Можно использовать специальные подсказки.
💰 За победы ты получаешь монеты.
⭐ За победы ты получаешь XP.
🆙 XP позволяет повышать уровень.
🛒 Монеты можно тратить в магазине.

🔥 Побеждай несколько раз подряд и увеличивай серию!

💾 Прогресс автоматически сохраняется.
""")

    input("Нажми Enter, чтобы вернуться...")


# ============================================================
# 🚀 ГЛАВНОЕ МЕНЮ
# ============================================================

def main():

    while True:

        title()

        print(f"👤 Уровень: {player['level']}")
        print(f"💰 Монеты: {player['coins']}")
        print(f"🔥 Серия побед: {player['win_streak']}")

        print()
        print("1. 🎮 Играть")
        print("2. 👤 Профиль")
        print("3. 🛒 Магазин")
        print("4. 📜 Правила")
        print("5. 🚪 Выход")

        choice = input("\n👉 Выбери действие: ")

        if choice == "1":
            play_game()

        elif choice == "2":
            profile()
            input("\nНажми Enter...")

        elif choice == "3":
            shop()

        elif choice == "4":
            rules()

        elif choice == "5":
            save_data(player)
            print()
            print(GREEN + "💾 Игра сохранена!" + RESET)
            print("👋 Спасибо за игру!")
            break

        else:
            print(RED + "❌ Такой команды нет!" + RESET)


# ============================================================
# ▶️ ЗАПУСК
# ============================================================

if __name__ == "__main__":
    main()
```

### 🚀 Что теперь есть

**🎮 Геймплей**

* 5 режимов сложности
* случайный диапазон в режиме «Хаос»
* ограничение попыток
* система «горячо/холодно»
* случайные подсказки
* дополнительные попытки

**👤 Прокачка**

* уровень игрока
* XP
* повышение уровня
* бонус за новый уровень
* серия побед
* лучший результат
* статистика игр

**💰 Экономика**

* монеты
* магазин
* покупка подсказок
* покупка дополнительных попыток
* покупка XP

**💾 Сохранение**

* прогресс сохраняется в `player_data.json`
* после закрытия программы уровень, монеты, рекорды и статистика не пропадут.

**🏆 Система рекордов**

* лучший результат по количеству попыток
* лучшая серия побед
* XP за победы
* дополнительные награды за сложные режимы


Положи `game.py` и `player_data.json` **в одну папку**. Причём `player_data.json` создавать вручную не нужно — программа сама создаст его при первом сохранении.

Если запускаешь в **Google Colab**, код тоже можно использовать, но файл сохранения будет находиться внутри среды Colab и может исчезнуть после сброса среды.

