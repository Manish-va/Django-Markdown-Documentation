# 21/10/2024
- We Created `Student` and `Teacher` model in myapp.
And also created `Views.py`, `url.py`, `serealizer.py`, `admin.py` related to the model.

```
from datetime import timezone
from django.db import models
from myapp.logic2 import teacher_performance
from django.utils import timezone
class Student(models.Model):
    name = models.CharField(max_length=50)
    # Unique ID for each student
    roll_no = models.AutoField(primary_key=True)
    # Marks in different subjects
    chemistry = models.IntegerField(default=0)
    physics = models.IntegerField(default=0)
    maths = models.IntegerField(default=0)
    total = models.IntegerField(default=0, editable=False)
    percentage = models.FloatField(default=0.0, editable=False)
    # Reference to the teacher this student is linked to
    employee_id = models.ForeignKey(Teacher, on_delete=models.DO_NOTHING,null=True, blank=True)
    # Reference to the department this student belongs to
    department_id = models.ForeignKey('Department.Department', on_delete=models.DO_NOTHING, null=True,blank=True)
    created_on = models.DateTimeField(default=timezone.now, editable=False)
    updated_on = models.DateTimeField(auto_now=True)

    def save(self, *args, **kwargs):
        # Calculate total marks and percentage before saving
        self.total = self.chemistry + self.physics + self.maths
        total_subjects = 3
        self.percentage = (self.total / (total_subjects * 50)) * 100 if self.total else 0
        
        # Save the student instance
        super().save(*args, **kwargs)  

        # Update the teacher's performance if the teacher is assigned
        if self.employee_id:
            self.employee_id.update_performance()  

    def __str__(self):
        return f"{self.name} (Roll No: {self.roll_no})"
    
```
## Teacher
```
from datetime import timezone
from django.db import models
from myapp.logic2 import teacher_performance
from django.utils import timezone
class Teacher(models.Model):
    name = models.CharField(max_length=50)
    # Unique ID for each teacher, automatically incremented
    employee_id = models.AutoField(primary_key=True)
    # Performance score of the teacher
    performance = models.FloatField(blank=True, editable=False, null=True)
    # Reference to the department this teacher belongs to
    department_id = models.ForeignKey('Department.Department', on_delete=models.DO_NOTHING, null=True,blank=True)
    # Timestamp for when the teacher was created
    created_on = models.DateTimeField(default=timezone.now, editable=False)
    # Timestamp for when the teacher was last updated
    updated_on = models.DateTimeField(auto_now=True)

    def save(self, *args, **kwargs):
        # Save the teacher first
        super().save(*args, **kwargs)  
        # Update performance only if the teacher is being created or updated
        self.update_performance()  

    def update_performance(self):
        # Get the students associated with this teacher
        student_data = Student.objects.filter(employee_id=self)
        if student_data.exists():
            # Calculate the performance based on students
            performance_data = teacher_performance(student_data)
            self.performance = performance_data['performance_percentage']
            # Save only the performance field to avoid recursion
            # self.save(update_fields=['performance'])

            # Update the performance field in the database
            Teacher.objects.filter(employee_id=self.employee_id).update(performance=self.performance)

    def __str__(self):
        performance_display = f"{self.performance:.1f}" if self.performance is not None else 0.0
        return f"{self.name} (Employee ID: {self.employee_id}, Performance: {performance_display})"


```