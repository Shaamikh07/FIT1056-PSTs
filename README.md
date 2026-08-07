"""
MSMS.py - Music School Management System
This script is a prototype that uses in-memory data structures (lists and objects) 
to manage student registrations and teacher specialities.
"""

# FRAGMENT 1: DATA MODELS & STORAGE (1_1)


class Student:
    """
    Represents a student in the system.
    Encapsulates student details and a list of their current enrollments.
    """
    def __init__(self, student_id, name):
        # Unique identifier assigned by the system
        self.id = student_id
        # Full name of the student
        self.name = name
        # A list to store instruments the student is learning (allows multiple)
        self.enrolled_in = []

class Teacher:
    """
    Represents a teacher in the system.
    Stores their ID, name, and their primary musical speciality.
    """
    def __init__(self, teacher_id, name, speciality):
        self.id = teacher_id
        self.name = name
        self.speciality = speciality

# --- In-Memory Databases ---
# These lists act as our temporary database during the program execution.
student_db = []
teacher_db = []

# Global counters used to generate unique IDs automatically for new entries.
next_student_id = 1
next_teacher_id = 1



# FRAGMENT 2: CORE HELPER FUNCTIONS (1_2)


def add_teacher(name, speciality):
    """
    Instantiates a new Teacher and saves it to the global teacher list.
    Uses 'global' keyword to modify the ID counter across function calls.
    """
    global next_teacher_id
    new_teacher = Teacher(next_teacher_id, name, speciality)
    teacher_db.append(new_teacher)
    next_teacher_id += 1
    print(f"Core System: Teacher '{name}' added successfully.")

def list_students():
    """Outputs all student records currently stored in the system memory."""
    print("\n--- Current Students ---")
    if not student_db:
        print("No students found in memory.")
    for s in student_db:
        # Prints ID, Name, and the list of instruments
        print(f"ID: {s.id} | Name: {s.name} | Courses: {s.enrolled_in}")

def list_teachers():
    """Outputs all teacher records currently stored in the system memory."""
    print("\n--- Current Teachers ---")
    if not teacher_db:
        print("No teachers found in memory.")
    for t in teacher_db:
        print(f"ID: {t.id} | Name: {t.name} | Speciality: {t.speciality}")

def find_students(term):
    """
    Search student names using a case-insensitive search term.
    Demonstrates list filtering logic.
    """
    print(f"\nSearching students for: '{term}'...")
    # List comprehension to find matches; term.lower() makes it case-insensitive
    matches = [s for s in student_db if term.lower() in s.name.lower()]
    
    if not matches:
        print("No matching students found.")
    else:
        for s in matches:
            print(f"Match: ID {s.id} - {s.name}")

def find_teachers(term):
    """
    Search teacher names OR specialities for a keyword.
    Uses 'or' logic to check multiple attributes of the object.
    """
    print(f"\nSearching teachers for: '{term}'...")
    matches = [t for t in teacher_db if term.lower() in t.name.lower() or term.lower() in t.speciality.lower()]
    
    if not matches:
        print("No matching teachers found.")
    else:
        for t in matches:
            print(f"Match: ID {t.id} - {t.name} ({t.speciality})")



# FRAGMENT 3: FRONT DESK FUNCTIONS (1_3)


def find_student_by_id(student_id):
    """
    Utility function to retrieve a specific student object by their ID.
    Returns None if no match is found.
    """
    for student in student_db:
        if student.id == student_id:
            return student
    return None

def front_desk_register(name, instrument):
    """
    A workflow function that combines student creation with initial enrollment.
    This abstraction simplifies the process for the end-user.
    """
    global next_student_id
    new_student = Student(next_student_id, name)
    student_db.append(new_student)
    next_student_id += 1
    
    # Automatically enrol the student in their first course immediately
    front_desk_enrol(new_student.id, instrument)
    print(f"Front Desk: {name} is now registered.")

def front_desk_enrol(student_id, instrument):
    """
    Updates a student's enrollment list based on a provided ID.
    Validates existence of student before updating.
    """
    student = find_student_by_id(student_id)
    if student:
        # Add the instrument name to the student's inner list
        student.enrolled_in.append(instrument)
        print(f"Front Desk: Enrolled Student ID {student_id} in '{instrument}'.")
    else:
        print(f"Front Desk Error: ID {student_id} not found in system.")

def front_desk_lookup(term):
    """
    Centralized lookup function that queries both databases simultaneously.
    """
    print(f"\n[Global Lookup for: {term}]")
    find_students(term)
    find_teachers(term)


# FRAGMENT 4: INTERACTIVE INTERFACE (MAIN)


if __name__ == "__main__":
    # The 'while True' loop keeps the program running until the user chooses to exit
    while True:
        print("\n--- MSMS PROTOTYPE MENU ---")
        print("1. Register Student")
        print("2. Add Teacher")
        print("3. Enrol Existing Student")
        print("4. Display All Records")
        print("5. Search Database")
        print("6. Exit")
        
        # Taking input from user (Slide 39)
        choice = input("\nSelect Option: ")
        
        if choice == '1':
            name = input("Student Name: ")
            inst = input("Primary Instrument: ")
            front_desk_register(name, inst)
            
        elif choice == '2':
            name = input("Teacher Name: ")
            spec = input("Teacher Speciality: ")
            add_teacher(name, spec)
            
        elif choice == '3':
            # Conversion (Casting) is required because input() returns a string (Slide 40)
            try:
                sid = int(input("Enter Student ID: "))
                inst = input("Enter Instrument/Course Name: ")
                front_desk_enrol(sid, inst)
            except ValueError:
                # Error handling for invalid numeric input
                print("Invalid input: Please enter a numeric ID.")
                
        elif choice == '4':
            list_teachers()
            list_students()
            
        elif choice == '5':
            term = input("Search term: ")
            front_desk_lookup(term)
            
        elif choice == '6':
            print("System shutting down. No data will be saved (Amnesia Stage).")
            break
        else:
            print("Error: Invalid selection. Choose 1-6.")