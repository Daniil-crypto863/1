```python
import random
import json
import os
import time


# ============================================================
# 🎮 УГАДАЙ ЧИСЛО — ULTIMATE EDITION 2.0
# ============================================================

SAVE_FILE = "player_data.json"


# ============================================================
# 🎨 ЦВЕТА
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

def default_data():
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
        "hints": 2,
        "extra_attempts": 0
    }


def load_data():
    if not os.path.exists(SAVE_FILE):
        return default_data()

    try:
        with open(SAVE_FILE, "r", encoding="utf-8") as file:
            data = json.load(file)

        # Если в старом сохранении нет новых параметров
        default = default_data()

        for key, value in default.items():
            if key not in data:
                data[key] = value

        return data

    except (json.JSONDecodeError, OSError):
        print(RED + "⚠️ Не удалось загрузить сохранение. Создан новый профиль." + RESET)
        return default_data()


def save_data():
    try:
        with open(SAVE_FILE, "w", encoding="utf-8") as file:
            json.dump(player, file, indent=4, ensure_ascii=False)
    except OSError:
        print(RED + "❌ Ошибка сохранения!" + RESET)


player = load_data()


# ============================================================
# ⭐ XP И УРОВНИ
# ============================================================

def add_xp(amount):
    player["xp"] += amount

    while player["xp"] >= player["level"] * 100:
        needed_xp = player["level"] * 100

        player["xp"] -= needed_xp
        player["level"] += 1
        player["coins"] += 50

        print()
        print(GREEN + "╔══════════════════════════════╗" + RESET)
        print(GREEN + "║      🎉 НОВЫЙ УРОВЕНЬ!      ║" + RESET)
        print(GREEN + "╚══════════════════════════════╝" + RESET)
        print(f"⭐ Теперь твой уровень: {player['level']}")
        print("💰 Бонус: +50 монет!")


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
    print(f"🔥 Текущая серия: {player['win_streak']}")
    print(f"🏆 Лучшая серия: {player['best_streak']}")
    print(f"🎮 Всего игр: {player['games']}")
    print(f"🔮 Подсказок: {player['hints']}")
    print(f"❤️ Доп. попыток: {player['extra_attempts']}")

    if player["games"] > 0:
        win_rate = player["wins"] / player["games"] * 100
        print(f"📊 Процент побед: {win_rate:.1f}%")
    else:
        print("📊 Процент побед: 0%")

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
        print(f"💰 Монеты: {player['coins']}")
        print()
        print("1. 🔮 Подсказка — 30 монет")
        print("2. ❤️ Дополнительная попытка — 50 монет")
        print("3. 💎 100 XP — 80 монет")
        print("4. 🚪 Выйти")

        choice = input("\n👉 Выбери товар: ").strip()

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
                player["extra_attempts"] += 1
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
            print(RED + "❌ Выбери пункт от 1 до 4." + RESET)

        save_data()


# ============================================================
# 🎯 РЕЖИМЫ
# ============================================================

def choose_mode():

    print()
    print(BLUE + "╔════════════ 🎯 РЕЖИМЫ ════════════╗" + RESET)
    print("1. 🟢 Легко      — 1-50   | 10 попыток")
    print("2. 🟡 Средне     — 1-100  | 7 попыток")
    print("3. 🔴 Сложно     — 1-500  | 10 попыток")
    print("4. 💀 Безумие    — 1-1000 | 8 попыток")
    print("5. 🎲 Хаос       — случайный диапазон")

    while True:
        choice = input("\n👉 Выбери режим: ").strip()

        if choice == "1":
            return "Легко", 50, 10

        elif choice == "2":
            return "Средне", 100, 7

        elif choice == "3":
            return "Сложно", 500, 10

        elif choice == "4":
            return "Безумие", 1000, 8

        elif choice == "5":
            maximum = random.randint(100, 5000)
            attempts = random.randint(5, 12)
            return "Хаос", maximum, attempts

        else:
            print(RED + "❌ Выбери число от 1 до 5." + RESET)


# ============================================================
# 🔮 ПОДСКАЗКИ
# ============================================================

def use_hint(secret_number, maximum):

    if player["hints"] <= 0:
        print(RED + "❌ У тебя нет подсказок!" + RESET)
        return

    player["hints"] -= 1

    hint_type = random.randint(1, 5)

    if hint_type == 1:
        if secret_number % 2 == 0:
            print("🔮 Число ЧЁТНОЕ.")
        else:
            print("🔮 Число НЕЧЁТНОЕ.")

    elif hint_type == 2:
        middle = maximum // 2

        if secret_number <= middle:
            print("🔮 Число находится в ПЕРВОЙ половине.")
        else:
            print("🔮 Число находится во ВТОРОЙ половине.")

    elif hint_type == 3:
        if secret_number % 5 == 0:
            print("🔮 Число делится на 5.")
        else:
            print("🔮 Число НЕ делится на 5.")

    elif hint_type == 4:
        if secret_number % 10 == 0:
            print("🔮 Число заканчивается на 0.")
        elif secret_number % 5 == 0:
            print("🔮 Число заканчивается на 5.")
        else:
            print("🔮 Число не заканчивается на 0 или 5.")

    else:
        # Подсказка с диапазоном
        low = max(1, secret_number - maximum // 10)
        high = min(maximum, secret_number + maximum // 10)

        print(f"🔮 Число находится примерно между {low} и {high}.")

    print(f"🔮 Осталось подсказок: {player['hints']}")


# ============================================================
# 🌡️ ГОРЯЧО / ХОЛОДНО
# ============================================================

def temperature_hint(secret_number, guess, maximum):

    difference = abs(secret_number - guess)

    if difference <= 3:
        print(RED + "🔥 ОЧЕНЬ ГОРЯЧО!" + RESET)

    elif difference <= 10:
        print(YELLOW + "🔥 Горячо!" + RESET)

    elif difference <= maximum * 0.25:
        print("🙂 Тепло.")

    else:
        print(CYAN + "❄️ Холодно..." + RESET)


# ============================================================
# 🎁 НАГРАДА
# ============================================================

def calculate_reward(maximum, attempts, max_attempts):

    score = max(10, (max_attempts - attempts + 1) * 20)

    if maximum >= 1000:
        score *= 3

    elif maximum >= 500:
        score *= 2

    coins = max(1, score // 5)

    return score, coins


# ============================================================
# 🎮 ИГРА
# ============================================================

def play_game():

    mode, maximum, max_attempts = choose_mode()

    secret_number = random.randint(1, maximum)

    # Используем купленную попытку
    if player["extra_attempts"] > 0:
        max_attempts += player["extra_attempts"]

        print(
            GREEN
            + f"❤️ Использовано дополнительных попыток: "
              f"{player['extra_attempts']}"
            + RESET
        )

        player["extra_attempts"] = 0

    attempts = 0

    print()
    print(GREEN + f"🎮 Режим: {mode}" + RESET)
    print(f"🎯 Диапазон: 1–{maximum}")
    print(f"❤️ Попыток: {max_attempts}")

    while attempts < max_attempts:

        print()
        print(f"❤️ Осталось попыток: {max_attempts - attempts}")
        print(f"🔮 Подсказок: {player['hints']}")

        user_input = input(
            "🔢 Введи число или 'hint': "
        ).strip().lower()

        if user_input == "hint":
            use_hint(secret_number, maximum)
            continue

        try:
            guess = int(user_input)

        except ValueError:
            print(RED + "❌ Введи целое число!" + RESET)
            continue

        if guess < 1 or guess > maximum:
            print(
                RED
                + f"❌ Число должно быть от 1 до {maximum}!"
                + RESET
            )
            continue

        attempts += 1

        # Победа
        if guess == secret_number:

            print()
            print(GREEN + "╔════════════════════════════════╗" + RESET)
            print(GREEN + "║        🎉 ПОБЕДА! 🎉          ║" + RESET)
            print(GREEN + "╚════════════════════════════════╝" + RESET)

            print(f"🎯 Число: {secret_number}")
            print(f"🔢 Попыток: {attempts}")

            score, coins = calculate_reward(
                maximum,
                attempts,
                max_attempts
            )

            print(f"⭐ XP: +{score}")
            print(f"💰 Монеты: +{coins}")

            player["coins"] += coins
            player["wins"] += 1
            player["games"] += 1
            player["win_streak"] += 1

            if player["win_streak"] > player["best_streak"]:
                player["best_streak"] = player["win_streak"]
                print(YELLOW + "🔥 НОВЫЙ РЕКОРД СЕРИИ!" + RESET)

            if (
                player["best_attempts"] is None
                or attempts < player["best_attempts"]
            ):
                player["best_attempts"] = attempts
                print(YELLOW + "🏆 НОВЫЙ РЕКОРД ПОПЫТОК!" + RESET)

            add_xp(score)
            save_data()

            return

        # Подсказка больше / меньше
        if guess < secret_number:
            print(BLUE + "📈 Моё число БОЛЬШЕ!" + RESET)
        else:
            print(YELLOW + "📉 Моё число МЕНЬШЕ!" + RESET)

        temperature_hint(
            secret_number,
            guess,
            maximum
        )

    # Проигрыш
    print()
    print(RED + "╔════════════════════════════════╗" + RESET)
    print(RED + "║       💀 ТЫ ПРОИГРАЛ 💀       ║" + RESET)
    print(RED + "╚════════════════════════════════╝" + RESET)

    print(f"😈 Загаданное число: {secret_number}")

    player["losses"] += 1
    player["games"] += 1
    player["win_streak"] = 0

    save_data()


# ============================================================
# 📜 ПРАВИЛА
# ============================================================

def rules():

    print()
    print(CYAN + "╔════════════ 📜 ПРАВИЛА ════════════╗" + RESET)

    print("""
🎯 Цель:
Угадать загаданное компьютером число.

📈 Если твоё число меньше — компьютер сообщит:
   «Моё число БОЛЬШЕ».

📉 Если твоё число больше — компьютер сообщит:
   «Моё число МЕНЬШЕ».

🔥 Чем ближе ты к числу, тем горячее подсказка.

🔮 Командой "hint" можно использовать подсказку.

💰 За победу ты получаешь монеты.

⭐ За победу ты получаешь XP.

🆙 XP повышает уровень.

🔥 Победы подряд увеличивают серию.

🛒 Монеты можно потратить в магазине.

💾 Прогресс автоматически сохраняется.
""")

    input("Нажми Enter, чтобы вернуться...")


# ============================================================
# 🧹 ОЧИСТКА ЭКРАНА
# ============================================================

def clear_screen():

    os.system("cls" if os.name == "nt" else "clear")


# ============================================================
# 🚀 ГЛАВНОЕ МЕНЮ
# ============================================================

def main():

    while True:

        clear_screen()
        title()

        print(f"👤 Уровень: {player['level']}")
        print(f"💰 Монеты: {player['coins']}")
        print(f"🔥 Серия: {player['win_streak']}")
        print(f"⭐ XP: {player['xp']}/{player['level'] * 100}")

        print()
        print("1. 🎮 Играть")
        print("2. 👤 Профиль")
        print("3. 🛒 Магазин")
        print("4. 📜 Правила")
        print("5. 🚪 Выход")

        choice = input("\n👉 Выбери действие: ").strip()

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
            save_data()

            print()
            print(GREEN + "💾 Игра сохранена!" + RESET)
            print("👋 Спасибо за игру!")
            break

        else:
            print(RED + "❌ Такой команды нет!" + RESET)
            time.sleep(1)


# ============================================================
# ▶️ ЗАПУСК
# ============================================================

if __name__ == "__main__":
    main()
```
