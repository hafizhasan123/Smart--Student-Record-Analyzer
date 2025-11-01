# Smart--Student-Record-Analyzer
# ===============================================
# SMART STUDENT RECORD ANALYZER
# Developed by: Hasan Rajput
# Hackathon Project
# ===============================================

# Required Concepts: Functions + File Handling only
# No OOP, No external libraries

# ---------- Helper Functions ----------

def validate_input(prompt, type_func, condition=lambda x: True, error_msg="Invalid input!"):
    """General validation function"""
    while True:
        try:
            value = type_func(input(prompt))
            if condition(value):
                return value
            else:
                print(error_msg)
        except:
            print(error_msg)


def read_students():
    """Read all student records from file and return as list"""
    try:
        with open("students.txt", "r") as f:
            lines = f.readlines()
        students = []
        for line in lines:
            parts = line.strip().split(",")
            if len(parts) == 5:
                students.append({
                    "id": int(parts[0]),
                    "name": parts[1],
                    "age": int(parts[2]),
                    "grade": parts[3],
                    "marks": int(parts[4])
                })
        return students
    except FileNotFoundError:
        return []


def write_students(students):
    """Write all student data back to the file"""
    with open("students.txt", "w") as f:
        for s in students:
            f.write(f"{s['id']},{s['name']},{s['age']},{s['grade']},{s['marks']}\n")


# ---------- Core Functionalities ----------

def add_student():
    print("\n--- Add Student ---")
    students = read_students()

    # ID validation (unique)
    while True:
        sid = validate_input("Enter ID (integer): ", int, lambda x: x > 0, "ID must be a positive integer!")
        if any(s["id"] == sid for s in students):
            print("❌ ID already exists! Try another.")
        else:
            break

    # Name validation
    while True:
        name = input("Enter Name: ").strip()
        if not name or name.isnumeric():
            print("❌ Invalid name! Try again.")
        else:
            break

    age = validate_input("Enter Age: ", int, lambda x: x > 0, "Age must be greater than 0.")
    grade = input("Enter Grade (A–F): ").strip().upper()
    while grade not in ["A", "B", "C", "D", "E", "F"]:
        grade = input("❌ Invalid Grade! Enter A–F: ").strip().upper()

    marks = validate_input("Enter Marks (0–100): ", int, lambda x: 0 <= x <= 100, "Marks must be 0–100.")

    # Save data
    with open("students.txt", "a") as f:
        f.write(f"{sid},{name},{age},{grade},{marks}\n")
    print("✅ Student added successfully!\n")


def view_students():
    print("\n--- All Student Records ---")
    students = read_students()
    if not students:
        print("No records found!")
        return
    for s in students:
        print(f"ID: {s['id']} | Name: {s['name']} | Age: {s['age']} | Grade: {s['grade']} | Marks: {s['marks']}")
    print(f"\nTotal Students: {len(students)}\n")


def search_student():
    print("\n--- Search Student ---")
    students = read_students()
    key = input("Enter ID or Name to search: ").strip().lower()
    found = False
    for s in students:
        if key == str(s["id"]).lower() or key in s["name"].lower():
            print(f"Found: ID={s['id']}, Name={s['name']}, Age={s['age']}, Grade={s['grade']}, Marks={s['marks']}")
            found = True
    if not found:
        print("❌ No matching record found!")


def update_student():
    print("\n--- Update Student ---")
    students = read_students()
    sid = validate_input("Enter ID to update: ", int)
    for s in students:
        if s["id"] == sid:
            print(f"Editing record for {s['name']}")
            s["name"] = input(f"New Name ({s['name']}): ") or s["name"]
            s["age"] = validate_input(f"New Age ({s['age']}): ", int, lambda x: x > 0, "Invalid age!")
            s["grade"] = input(f"New Grade ({s['grade']}): ").upper() or s["grade"]
            s["marks"] = validate_input(f"New Marks ({s['marks']}): ", int, lambda x: 0 <= x <= 100, "Invalid marks!")
            write_students(students)
            print("✅ Record updated successfully!")
            return
    print("❌ Student ID not found!")


def delete_student():
    print("\n--- Delete Student ---")
    students = read_students()
    sid = validate_input("Enter ID to delete: ", int)
    for s in students:
        if s["id"] == sid:
            confirm = input(f"Are you sure to delete {s['name']}? (y/n): ").lower()
            if confirm == "y":
                students.remove(s)
                write_students(students)
                print("✅ Record deleted successfully!")
            return
    print("❌ Student not found!")


def analyze_data():
    print("\n--- Data Analysis ---")
    students = read_students()
    if not students:
        print("No data to analyze.")
        return

    marks_list = [s["marks"] for s in students]
    avg = sum(marks_list) / len(marks_list)
    top = max(students, key=lambda x: x["marks"])
    low = min(students, key=lambda x: x["marks"])
    below_avg = [s for s in students if s["marks"] < avg]

    print(f"Average Marks: {avg:.2f}")
    print(f"Top Performer: {top['name']} ({top['marks']})")
    print(f"Students Below Average: {len(below_avg)}")
    print(f"Highest Marks: {low['marks']} | Lowest Marks: {low['marks']}")


# ---------- Main Menu ----------

def main():
    while True:
        print("""
====== Smart Student Record Analyzer ======
1. Add Student
2. View All Students
3. Search Student
4. Update Student
5. Delete Student
6. Analyze Data
7. Exit
""")
        choice = input("Enter your choice (1–7): ")
        if choice == "1":
            add_student()
        elif choice == "2":
            view_students()
        elif choice == "3":
            search_student()
        elif choice == "4":
            update_student()
        elif choice == "5":
            delete_student()
        elif choice == "6":
            analyze_data()
        elif choice == "7":
            print("Thank you for using Smart Student Record Analyzer!")
            break
        else:
            print("❌ Invalid choice! Try again.")


# Run the program
if __name__ == "__main__":
    main() Smart Student Management System For AI and Data Science Hackathon
