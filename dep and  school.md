# 23/10/2024
- We Created `Department` and `School` app and linked with `Student` , `Teacher` model which is in myapp.
## Department
'''
from django.db import models

class Department(models.Model):
    department_name = models.CharField(max_length=100)
    # Unique ID for each department, automatically incremented
    department_id = models.AutoField(primary_key=True)
    #HOD, related to the Teacher model
    hod = models.ForeignKey('myapp.Teacher', on_delete=models.DO_NOTHING,null=True,blank=True)
    # Reference to the school that this department belongs to
    school_id = models.ForeignKey('School.School', on_delete=models.DO_NOTHING,null=True,blank=True)
    def __str__(self):
        return f"{self.department_name} (Department ID: {self.department_id}), HOD: {self.hod}, School ID: {self.school_id}"


'''

## School
```
from datetime import timezone
from django.db import models
from django.utils import timezone

class School(models.Model):
    school_name = models.CharField(max_length=100)
    # Unique ID for each school
    school_id = models.AutoField(primary_key=True,)
    location = models.CharField(max_length=100)
    created_on = models.DateTimeField(default=timezone.now, editable=False)
    updated_on = models.DateTimeField(auto_now=True)


```
- And also created `Views.py`, `url.py`, `serealizer.py`, `admin.py` related to these app.