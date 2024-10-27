# 22/10/2024
## Script demo.py
We used demo.py to Synchronizes student `department_id` and `school_id` with the teacher's data.

- **Fetches all students**.
- For each student:
  - Retrieves their associated teacher.
  - Updates the student's `department_id` and `school_id` if applicable.
- Handles errors and prints messages for any issues encountered.

- Ideal for **data migration** or **cleanup tasks**.
- Ensures **data consistency** in the database.

This script automates updates across related models efficiently.

```
from Department.models import Department 
from myapp.models import Teacher, Student

def run():
    # Fetch all students data
    students = Student.objects.all()

    # Loop through each student and update their department_id and school_id based on the teacher's information
    for student in students:
        try:
            # Get the teacher associated with the student
            teacher = student.employee_id  # Access the teacher directly through the foreign key
            # if teacher:
            if teacher and teacher.department_id:
                # Update the student's department_id to the teacher's department_id
                student.department_id = teacher.department_id
                if teacher.department_id.school_id:
                     # Update school_id based on teacher's department
                    student.school_id = teacher.department_id.school_id 
                else:
                    print(f'Teacher {teacher.name} does not have a school assigned.')
                    continue
                student.save()  # Save the updated student instance
                print(f'Successfully updated student {student.roll_no}: '
                      f'Department to {teacher.department_id.department_name}, '
                      f'School to {student.school_id.school_name}.')
            else:
                print(f'Student {student.roll_no} has no associated teacher.')
        except Exception as e:
            print(f'Error updating student {student.roll_no}: {e}')

if __name__ == '__main__':
    run()
```