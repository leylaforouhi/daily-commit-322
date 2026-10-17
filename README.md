import json
from pathlib import Path

DATA_FILE = Path("habits.json")


def load_habits():
    if not DATA_FILE.exists():
        return {}

    try:
        return json.loads(DATA_FILE.read_text(encoding="utf-8"))
    except (json.JSONDecodeError, OSError):
        return {}


def save_habits(habits):
    DATA_FILE.write_text(
        json.dumps(habits, indent=2, ensure_ascii=False),
        encoding="utf-8",
    )


def add_habit(habits, name):
    name = name.strip()

    if not name:
        raise ValueError("Habit name cannot be empty")

    habits.setdefault(name, [])
    save_habits(habits)


def mark_completed(habits, name, date):
    if name not in habits:
        raise KeyError(f"Habit not found: {name}")

    if date not in habits[name]:
        habits[name].append(date)

    save_habits(habits)


def completion_rate(habits, name, total_days):
    if name not in habits:
        raise KeyError(f"Habit not found: {name}")

    if total_days <= 0:
        raise ValueError("Total days must be positive")

    completed = min(len(set(habits[name])), total_days)
    return round(completed / total_days * 100, 2)


if __name__ == "__main__":
    habits = load_habits()

    add_habit(habits, "Read for 20 minutes")
    mark_completed(habits, "Read for 20 minutes", "2026-10-17")

    rate = completion_rate(habits, "Read for 20 minutes", 7)
    print(f"Weekly completion rate: {rate}%")
