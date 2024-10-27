# 17/10/2024
# Implementing CRUD Operations for a Student Model
**Create a Virtual Environment :** Before installing Django, create a virtual environment to isolate your project's dependencies.
```
python -m venv myenv
```
## Activate the Virtual Environment :
**On Windows:**
```
myenv\Scripts\activate
```
## Set Up Your Django Project

**Install Django and Django Rest Framework:**

```
   pip install django djangorestframework
```
**Create a New Django Project :**

```
django-admin startproject myproject
cd myproject
```
**Create a New App :**
```
python manage.py startapp myapp
```
**Add the App to Installed Apps :** Open settings.py and add 'myapp' and 'rest_framework' to INSTALLED_APPS:

```
INSTALLED_APPS = [
    ...
    'rest_framework',
    'myapp',
]
```
## Common Field Types
**CharField:** Short text strings.<br>
**TextField:** Large text strings.<br>
**IntegerField:** Integer values.<br>
**FloatField:** Floating-point numbers.<br>
**BooleanField:** True/False values.<br>
**DateField:** Date values.<br>
**DateTimeField:** Date and time values.<br>
**EmailField:** Email addresses.<br>
**URLField:** URLs.<br>
**ImageField:** Images (requires Pillow).<br>
**ForeignKey:** Many-to-one relationships.<br>
**ManyToManyField:** Many-to-many relationships.<br>
**OneToOneField:** One-to-one relationships.<br>
### Field Options
**null:** Allows NULL values.<br>
**blank:** Allows empty values in forms.<br>
**default:** Sets a default value.<br>
**unique:** Ensures unique values.<br>
**choices:** Limits field values to specific options.

## Create the Student Model
**Define the Model :** In myapp/models.py<br> **create a Student model:**

```
from django.db import models

class Student(models.Model):
    roll_no = models.CharField(max_length=10, unique=True)
    name = models.CharField(max_length=100)
    age = models.IntegerField()

    def __str__(self):
        return self.name
```

**Run Migrations:**
```
python manage.py makemigrations
python manage.py migrate
```
## Create a Serializer
Create a Serializer: In myapp/serializers.py, create a serializer for the Student model

```
from rest_framework import serializers
from .models import Student

class StudentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Student
        fields = '__all__'
```
## Create CRUD Views
**Create Views:** In myapp/views.py <br>implement CRUD operations using APIView<br><br>
**GET:** Retrieve all students or a specific student
```
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Student
from .serializers import StudentSerializer
class StudentAPIView(APIView):
    # GET: Retrieve all students or a specific student
    def get(self, request, roll_no=None):
        if roll_no:
            try:
                student = Student.objects.get(roll_no=roll_no)
                serializer = StudentSerializer(student)
                return Response(serializer.data)
            except Student.DoesNotExist:
                return Response({"error": "Student not found!"}, status=status.HTTP_404_NOT_FOUND)
        else:
            students = Student.objects.all()
            serializer = StudentSerializer(students, many=True)
            return Response(serializer.data)
```
**POST:** Create a new student
```
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Student
from .serializers import StudentSerializer
class StudentAPIView(APIView):
    def post(self, request):
        serializer = StudentSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```
**PUT:** Update an entire student record
```
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Student
from .serializers import StudentSerializer
class StudentAPIView(APIView):
    def put(self, request, roll_no):
        try:
            student = Student.objects.get(roll_no=roll_no)
        except Student.DoesNotExist:
            return Response({"error": "Student not found!"}, status=status.HTTP_404_NOT_FOUND)

        serializer = StudentSerializer(student, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```
**PATCH:** Update specific fields of a student record
```
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Student
from .serializers import StudentSerializer
class StudentAPIView(APIView):
    def patch(self, request, roll_no):
        try:
            student = Student.objects.get(roll_no=roll_no)
        except Student.DoesNotExist:
            return Response({"error": "Student not found!"}, status=status.HTTP_404_NOT_FOUND)

        serializer = StudentSerializer(student, data=request.data, partial=True)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

**DELETE:** Remove a student record
```
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Student
from .serializers import StudentSerializer
class StudentAPIView(APIView):
    def delete(self, request, roll_no):
        try:
            student = Student.objects.get(roll_no=roll_no)
            student.delete()
            return Response(status=status.HTTP_204_NO_CONTENT)
        except Student.DoesNotExist:
            return Response({"error": "Student not found!"}, status=status.HTTP_404_NOT_FOUND)
```
## Set Up URLs
**Create URLs for the API:** In myapp/urls.py<br>set up your URL routes

```
from django.urls import path
from .views import StudentAPIView

urlpatterns = [
    path('students/', StudentAPIView.as_view()),  # For all students (GET, POST)
    path('students/<str:roll_no>/', StudentAPIView.as_view()),  # For specific student (GET, PUT, PATCH, DELETE)
]
```
**Include App URLs in Project:** In myproject/urls.py<br>include the app's URLs

```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('myapp.urls')),
]
```
## Test the API
Run the Development Server
```
python manage.py runserver
```
**Test the Endpoints:** Using a tool  Postman to test the API endpoints

**GET all students:** GET http://127.0.0.1:8000/api/students/ <br>
**GET a specific student:** GET http://127.0.0.1:8000/api/students/<roll_no>/   <br>
**POST(add) a new student:** POST http://127.0.0.1:8000/api/students/   <br>
**PUT(update) a student:** PUT http://127.0.0.1:8000/api/students/<roll_no>/  <br>
**PATCH update specific fields:** PATCH http://127.0.0.1:8000/api/students/<roll_no>/ <br>
**DELETE a student:** DELETE http://127.0.0.1:8000/api/students/<roll_no>/
