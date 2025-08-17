import os
import json
import datetime
from collections import defaultdict

DATA_FILE = "daily_planner.json"

def load_data():
    if os.path.exists(DATA_FILE):
        with open(DATA_FILE, "r", encoding="utf-8") as f:
            return json.load(f)
    return {"tasks": [], "habits": {}, "log": []}

def save_data(data):
    with open(DATA_FILE, "w", encoding="utf-8") as f:
        json.dump(data, f, indent=4)

def add_task(data):
    title = input("Task title: ")
    deadline = input("Deadline (YYYY-MM-DD, leave empty if none): ")
    priority = input("Priority (High/Medium/Low): ")
    data["tasks"].append({
        "title": title,
        "deadline": deadline if deadline else None,
        "priority": priority,
        "done": False
    })
    save_data(data)
    print("✅ Task added successfully!")

def mark_task_done(data):
    if not data["tasks"]:
        print("📭 No tasks yet.")
        return
    for i, task in enumerate(data["tasks"], start=1):
        status = "✅" if task["done"] else "❌"
        print(f"{i}. {status} {task['title']} — Priority: {task['priority']}")
    try:
        choice = int(input("Enter task number to mark as done: "))
        data["tasks"][choice-1]["done"] = True
        save_data(data)
        print("🎯 Task marked as completed!")
    except:
        print("⚠️ Invalid choice.")

def add_habit(data):
    habit = input("Enter a new habit: ")
    if habit in data["habits"]:
        print("⚠️ Habit already exists.")
    else:
        data["habits"][habit] = {}
        save_data(data)
        print(f"✅ Habit '{habit}' added!")

def mark_habit_done(data):
    if not data["habits"]:
        print("📭 No habits yet.")
        return
    today = str(datetime.date.today())
    for i, habit in enumerate(data["habits"].keys(), start=1):
        print(f"{i}. {habit}")
    try:
        choice = int(input("Enter habit number: "))
        habit_name = list(data["habits"].keys())[choice-1]
        data["habits"][habit_name][today] = True
        save_data(data)
        print(f"🎯 Marked '{habit_name}' as done today!")
    except:
        print("⚠️ Invalid choice.")

def view_summary(data):
    print("\n📋 Tasks:")
    for task in data["tasks"]:
        status = "✅" if task["done"] else "❌"
        print(f"{status} {task['title']} — Priority: {task['priority']} — Deadline: {task['deadline']}")

    print("\n📊 Habits (last 7 days):")
    last_7 = [str(datetime.date.today() - datetime.timedelta(days=i)) for i in range(6, -1, -1)]
    for habit, records in data["habits"].items():
        print(f"\n{habit}:")
        for day in last_7:
            status = "✅" if records.get(day) else "❌"
            print(f"{day}: {status}")

def daily_plan(data):
    print("\n==== YOUR DAILY PLAN ====")
    print("🌅🌅 Morning: Meditation, Healthy Breakfast, Check Tasks")
    print("🌞🌞 Afternoon: Work on shop/business, Learning English, Crypto projects")
    print("🌙 Evening: Play with your son, Exercise, Relax")
    print("🌌 Night: Review the day, Prepare tomorrow")
    print("=========================")

def menu():
    data = load_data()
    while True:
        print("\n==== SMART DAILY PLANNER ====")
        print("1. Add a task")
        print("2. Mark task as done")
        print("3. Add a habit")
        print("4. Mark habit as done today")
        print("5. View summary")
        print("6. Show daily plan")
        print("7. Exit")
        choice = input("Choose an option: ")
        
        if choice == "1":
            add_task(data)
        elif choice == "2":
            mark_task_done(data)
        elif choice == "3":
            add_habit(data)
        elif choice == "4":
            mark_habit_done(data)
        elif choice == "5":
            view_summary(data)
        elif choice == "6":
            daily_plan(data)
        elif choice == "7":
            break
        else:
            print("⚠️ Invalid choice.")

menu()
