```python
import json
import os
import random
import time


# ============================================================
# 🎮 УГАДАЙ ЧИСЛО — ULTIMATE EDITION 3.0
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
# 🧰 ВСПОМОГАТЕЛЬНЫЕ ФУНКЦИИ
# ============================================================

def clear_screen():
    """Очищает экран."""
    os.system("cls" if os.name == "nt" else "clear")


def pause(message="\nНажми Enter, чтобы продолжить..."):
    input(message)


def print_box(text, color=CYAN):
    """Красивый вывод сообщения."""
    border = "═" * (len(text) + 4)

    print(color + f"╔{border}╗" + RESET)
    print(color + f"║  {text}  ║" + RESET)
    print(color + f"╚{border}╝" + RESET)


# ============================================================
# 💾 ДАННЫЕ ИГРОКА
# ============================================================

def default_data():
    return {
        "coins": 100,
        "xp": 0,
        "level": 1,

        "wins": 0,
        "losses": 0,
        "games": 0,

        "best_attempts": None,

        "win_streak": 0,
        "best_streak": 0,

        "hints": 2,
        "extra_attempts": 0,

        "total_xp": 0,
        "total_coins": 0
    }


def load_data():
    """Загружает профиль игрока."""
    if not os.path.exists(SAVE_FILE):
        return default_data()

    try:
        with open(SAVE_FILE, "r", encoding="utf-8") as file:
            data = json.load(file)

        default = default_data()

        # Добавляем отсутствующие параметры
        for key, value in default.items():
            if key not in data:
                data[key] = value

        return data

    except (json.JSONDecodeError, OSError, TypeError):
        print(RED + "⚠️ Ошибка загрузки сохранения!" + RESET)
        print("Создаём новый профиль...")
        time.sleep(1)

        return default_data()


def save_data():
    """Сохраняет профиль игрока."""
    try:
        with open(SAVE_FILE, "w", encoding="utf-8") as file:
            json.dump(
                player,
                file,
                indent=4,
                ensure_ascii=False
            )

    except OSError:
        print(RED + "❌ Не удалось сохранить игру!" + RESET)


player = load_data()


# ============================================================
# ⭐ XP И УРОВНИ
# ============================================================

def xp_needed():
    """Количество XP для следующего уровня."""
    return player["level"] * 100


def add_xp(amount):
    """Добавляет XP и проверяет повышение уровня."""
    player["xp"] += amount
    player["total_xp"] += amount

    while player["xp"] >= xp_needed():

        required = xp_needed()

        player["xp"] -= required
        player["level"] += 1

        # Бонус за уровень
        player["coins"] += 50
        player["total_coins"] += 50

        print()

        print_box(
            f"🎉 НОВЫЙ УРОВЕНЬ: {player['level']}!",
            GREEN
        )

        print("⭐ Получено: +50 монет!")


# ============================================================
# 🎮 ЗАГОЛОВОК
# ============================================================

def title():
    print(CYAN)
    print("╔════════════════════════════════════════════╗")
    print("║          🎮 УГАДАЙ ЧИСЛО 🎮              ║")
    print("║             ULTIMATE EDITION              ║")
    print("║                   v3.0                    ║")
    print("╚════════════════════════════════════════════╝")
    print(RESET)


# ============================================================
# 👤 ПРОФИЛЬ
# ============================================================

def profile():
    clear_screen()
    title()

    print(MAGENTA + "╔════════════ 👤 ПРОФИЛЬ ════════════╗" + RESET)

    print(f"⭐ Уровень: {player['level']}")
    print(f"✨ XP: {player['xp']}/{xp_needed()}")
    print(f"💰 Монеты: {player['coins']}")

    print()

    print(f"🎯 Побед: {player['wins']}")
    print(f"💀 Поражений: {player['losses']}")
    print(f"🎮 Всего игр: {player['games']}")

    print()

    print(f"🔥 Текущая серия: {player['win_streak']}")
    print(f"🏆 Лучшая серия: {player['best_streak']}")

    print()

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

    print()

    print(f"📈 Всего получено XP: {player['total_xp']}")
    print(f"💰 Всего заработано монет: {player['total_coins']}")

    print(MAGENTA + "╚════════════════════════════════════╝" + RESET)

    pause()


# ============================================================
# 🛒 МАГАЗИН
# ============================================================

def shop():
    while True:
        clear_screen()
        title()

        print(YELLOW + "╔════════════ 🛒 МАГАЗИН ════════════╗" + RESET)
        print(f"💰 Твои монеты: {player['coins']}")
        print()

        print("1. 🔮 Подсказка — 30 монет")
        print("2. ❤️ Дополнительная попытка — 50 монет")
        print("3. ⭐ 100 XP — 80 монет")
        print("4. 🎁 Большой пакет — 250 монет")
        print("5. 🚪 Выйти")

        choice = input("\n👉 Выбери товар: ").strip()

        # Подсказка
        if choice == "1":

            if player["coins"] >= 30:
                player["coins"] -= 30
                player["hints"] += 1

                print(GREEN + "✅ Подсказка куплена!" + RESET)

            else:
                print(RED + "❌ Недостаточно монет!" + RESET)

        # Попытка
        elif choice == "2":

            if player["coins"] >= 50:
                player["coins"] -= 50
                player["extra_attempts"] += 1

                print(
                    GREEN +
                    "✅ Дополнительная попытка куплена!"
                    + RESET
                )

            else:
                print(RED + "❌ Недостаточно монет!" + RESET)

        # XP
        elif choice == "3":

            if player["coins"] >= 80:
                player["coins"] -= 80

                add_xp(100)

                print(GREEN + "✅ Получено 100 XP!" + RESET)

            else:
                print(RED + "❌ Недостаточно монет!" + RESET)

        # Большой пакет
        elif choice == "4":

            if player["coins"] >= 250:
                player["coins"] -= 250

                player["hints"] += 3
                player["extra_attempts"] += 2
                add_xp(150)

                print(GREEN + "🎁 Большой пакет получен!" + RESET)
                print("🔮 +3 подсказки")
                print("❤️ +2 попытки")
                print("⭐ +150 XP")

            else:
                print(RED + "❌ Недостаточно монет!" + RESET)

        elif choice == "5":
            break

        else:
            print(RED + "❌ Выбери пункт от 1 до 5!" + RESET)

        save_data()
        time.sleep(1)


# ============================================================
# 🎯 РЕЖИМЫ
# ============================================================

def choose_mode():

    print()
    print(BLUE + "╔════════════ 🎯 РЕЖИМЫ ════════════╗" + RESET)

    print("1. 🟢 Легко       — 1-50    | 10 попыток")
    print("2. 🟡 Средне      — 1-100   | 7 попыток")
    print("3. 🔴 Сложно      — 1-500   | 10 попыток")
    print("4. 💀 Безумие     — 1-1000  | 8 попыток")
    print("5. 🎲 Хаос        — случайный диапазон")
    print("6. 👑 Легенда     — 1-10000 | 10 попыток")

    while True:

        choice = input("\n👉 Выбери режим: ").strip()

        if choice == "1":
            return "Легко", 50, 10

        elif choice == "2":
            return "Средно", 100, 7

        elif choice == "3":
            return "Сложно", 500, 10

        elif choice == "4":
            return "Безумие", 1000, 8

        elif choice == "5":
            maximum = random.randint(100, 5000)
            attempts = random.randint(5, 12)

            return "Хаос", maximum, attempts

        elif choice == "6":
            return "Легенда", 10000, 10

        else:
            print(RED + "❌ Выбери число от 1 до 6!" + RESET)


# ============================================================
# 🔮 ПОДСКАЗКИ
# ============================================================

def use_hint(secret_number, maximum):

    if player["hints"] <= 0:
        print(RED + "❌ У тебя нет подсказок!" + RESET)
        return

    player["hints"] -= 1

    hint_type = random.randint(1, 6)

    print()

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

    elif hint_type == 5:

        if secret_number > maximum * 0.75:
            print("🔮 Число находится в верхней четверти.")

        elif secret_number > maximum * 0.5:
            print("🔮 Число находится между половиной и 75%.")

        elif secret_number > maximum * 0.25:
            print("🔮 Число находится между 25% и половиной.")

        else:
            print("🔮 Число находится в нижней четверти.")

    else:

        radius = max(5, maximum // 10)

        low = max(1, secret_number - radius)
        high = min(maximum, secret_number + radius)

        print(
            f"🔮 Число находится примерно между "
            f"{low} и {high}."
        )

    print(f"🔮 Осталось подсказок: {player['hints']}")


# ============================================================
# 🌡️ ГОРЯЧО / ХОЛОДНО
# ============================================================

def temperature_hint(secret_number, guess, maximum):

    difference = abs(secret_number - guess)

    percent = difference / maximum

    if difference <= 3:
        print(RED + "🔥 ОЧЕНЬ ГОРЯЧО!" + RESET)

    elif percent <= 0.05:
        print(YELLOW + "🔥 Горячо!" + RESET)

    elif percent <= 0.15:
        print("🙂 Тепло.")

    elif percent <= 0.30:
        print("😐 Прохладно.")

    else:
        print(CYAN + "❄️ Холодно..." + RESET)


# ============================================================
# 🎁 НАГРАДА
# ============================================================

def calculate_reward(maximum, attempts, max_attempts):

    # Чем меньше попыток — тем больше XP
    score = max(
        10,
        (max_attempts - attempts + 1) * 20
    )

    # Сложные режимы дают больше награды
    if maximum >= 10000:
        score *= 5

    elif maximum >= 1000:
        score *= 3

    elif maximum >= 500:
        score *= 2

    coins = max(5, score // 5)

    # Бонус за победную серию
    streak_bonus = min(player["win_streak"] * 5, 50)

    coins += streak_bonus

    return score, coins


# ============================================================
# 🎮 ИГРА
# ============================================================

def play_game():

    clear_screen()
    title()

    mode, maximum, max_attempts = choose_mode()

    secret_number = random.randint(1, maximum)

    # Дополнительные попытки
    bonus_attempts = player["extra_attempts"]

    if bonus_attempts > 0:

        max_attempts += bonus_attempts
        player["extra_attempts"] = 0

        print(
            GREEN +
            f"\n❤️ Получено дополнительных попыток: "
            f"+{bonus_attempts}"
            + RESET
        )

    attempts = 0
    used_numbers = set()

    print()
    print(GREEN + f"🎮 Режим: {mode}" + RESET)
    print(f"🎯 Диапазон: 1–{maximum}")
    print(f"❤️ Попыток: {max_attempts}")

    print()
    print("💡 Команды:")
    print("   hint  — подсказка")
    print("   menu  — выйти в меню")
    print("   quit  — выйти из игры")

    while attempts < max_attempts:

        print()
        print(
            f"❤️ Осталось попыток: "
            f"{max_attempts - attempts}"
        )

        user_input = input(
            "🔢 Введи число: "
        ).strip().lower()

        # -------------------------
        # Команды
        # -------------------------

        if user_input == "hint":
            use_hint(secret_number, maximum)
            continue

        if user_input == "menu":
            print("↩️ Возвращаемся в меню...")
            time.sleep(1)
            return

        if user_input == "quit":
            print("👋 Выход из игры...")
            save_data()
            raise SystemExit

        # -------------------------
        # Проверка числа
        # -------------------------

        try:
            guess = int(user_input)

        except ValueError:
            print(
                RED +
                "❌ Введи целое число!"
                + RESET
            )
            continue

        # -------------------------
        # Проверка диапазона
        # -------------------------

        if guess < 1 or guess > maximum:

            print(
                RED +
                f"❌ Число должно быть "
                f"от 1 до {maximum}!"
                + RESET
            )

            continue

        # -------------------------
        # Проверка повторения
        # -------------------------

        if guess in used_numbers:

            print(
                YELLOW +
                "⚠️ Ты уже называл это число!"
                + RESET
            )

            continue

        used_numbers.add(guess)
        attempts += 1

        # -------------------------
        # ПОБЕДА
        # -------------------------

        if guess == secret_number:

            print()

            print_box(
                "🎉 ПОБЕДА! 🎉",
                GREEN
            )

            print(f"🎯 Загаданное число: {secret_number}")
            print(f"🔢 Попыток: {attempts}")

            score, coins = calculate_reward(
                maximum,
                attempts,
                max_attempts
            )

            player["coins"] += coins
            player["total_coins"] += coins

            player["wins"] += 1
            player["games"] += 1

            player["win_streak"] += 1

            print()
            print(f"⭐ XP: +{score}")
            print(f"💰 Монеты: +{coins}")

            # Рекорд серии
            if player["win_streak"] > player["best_streak"]:

                player["best_streak"] = player["win_streak"]

                print(
                    YELLOW +
                    "🔥 НОВЫЙ РЕКОРД СЕРИИ!"
                    + RESET
                )

            # Рекорд попыток
            if (
                player["best_attempts"] is None
                or attempts < player["best_attempts"]
            ):

                player["best_attempts"] = attempts

                print(
                    YELLOW +
                    "🏆 НОВЫЙ РЕКОРД ПОПЫТОК!"
                    + RESET
                )

            add_xp(score)

            save_data()

            pause()

            return

        # -------------------------
        # ПОДСКАЗКА БОЛЬШЕ / МЕНЬШЕ
        # -------------------------

        if guess < secret_number:

            print(
                BLUE +
                "📈 Моё число БОЛЬШЕ!"
                + RESET
            )

        else:

            print(
                YELLOW +
                "📉 Моё число МЕНЬШЕ!"
                + RESET
            )

        # -------------------------
        # ГОРЯЧО / ХОЛОДНО
        # -------------------------

        temperature_hint(
            secret_number,
            guess,
            maximum
        )

    # ========================================================
    # 💀 ПРОИГРЫШ
    # ========================================================

    print()

    print_box(
        "💀 ТЫ ПРОИГРАЛ 💀",
        RED
    )

    print(f"😈 Загаданное число: {secret_number}")

    player["losses"] += 1
    player["games"] += 1
    player["win_streak"] = 0

    save_data()

    pause()


# ============================================================
# 📜 ПРАВИЛА
# ============================================================

def rules():

    clear_screen()
    title()

    print_box("📜 ПРАВИЛА", CYAN)

    print("""
🎯 ЦЕЛЬ
Угадать число, которое загадал компьютер.

📈 ЕСЛИ ЧИСЛО МЕНЬШЕ
Компьютер сообщит:
«Моё число БОЛЬШЕ».

📉 ЕСЛИ ЧИСЛО БОЛЬШЕ
Компьютер сообщит:
«Моё число МЕНЬШЕ».

🔥 ГОРЯЧО / ХОЛОДНО
Чем ближе твоя попытка к загаданному числу,
тем горячее подсказка.

🔮 ПОДСКАЗКИ
Во время игры введи:
hint

🛒 МАГАЗИН
За монеты можно покупать:
• подсказки
• дополнительные попытки
• XP
• специальные наборы

⭐ XP
XP повышает уровень.

💰 МОНЕТЫ
Монеты можно получать за победы
и повышение уровня.

🔥 СЕРИЯ
Несколько побед подряд увеличивают серию.

💾 СОХРАНЕНИЕ
Прогресс автоматически сохраняется.

🎮 КОМАНДЫ
hint  — подсказка
menu  — выйти в меню
quit  — выйти из игры
""")

    pause()


# ============================================================
# 🧹 СБРОС ПРОФИЛЯ
# ============================================================

def reset_profile():

    print()

    answer = input(
        RED +
        "⚠️ Ты точно хочешь удалить весь прогресс? "
        "(да/нет): "
        + RESET
    ).strip().lower()

    if answer in ("да", "д", "yes", "y"):

        global player

        player = default_data()
        save_data()

        print(
            GREEN +
            "✅ Профиль сброшен!"
            + RESET
        )

    else:

        print("↩️ Сброс отменён.")

    time.sleep(1)


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
        print(f"⭐ XP: {player['xp']}/{xp_needed()}")

        print()

        print("1. 🎮 Играть")
        print("2. 👤 Профиль")
        print("3. 🛒 Магазин")
        print("4. 📜 Правила")
        print("5. 🗑️ Сбросить прогресс")
        print("6. 🚪 Выход")

        choice = input(
            "\n👉 Выбери действие: "
        ).strip()

        if choice == "1":
            play_game()

        elif choice == "2":
            profile()

        elif choice == "3":
            shop()

        elif choice == "4":
            rules()

        elif choice == "5":
            reset_profile()

        elif choice == "6":

            save_data()

            print()

            print_box(
                "💾 Игра сохранена!",
                GREEN
            )

            print("👋 Спасибо за игру!")

            break

        else:

            print(
                RED +
                "❌ Такой команды нет!"
                + RESET
            )

            time.sleep(1)


# ============================================================
# ▶️ ЗАПУСК
# ============================================================

if __name__ == "__main__":
    main()
```

# ============================================================

if __name__ == "__main__":
    main()
```




скоро будет добавлен апгрейд кода пока ещё думаю что добавить возможно будет ещё пару улучшений но код идёт к концу

