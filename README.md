Django CRUD (Create Read Update Delete) Example
Prerequisites before using Django-
Applications need to install- Python and python virtual environment
1. Download and Install python
2, Create virtual environment(pip should be installed(check version- pip --version))
Install either virtualenvwrapper or virtualenv(for more info goto- link)
install virtual environment- pip install virtualenv
Create virtual environment- virtualenv <virtual-env>
Now activate virtual env - cd <virtual-env>/Scripts/activate
3. Install Django using command -
pip install django
Check django version command-
	python -m django --version


To create a Django application that performs CRUD operations, follow the following steps.
1. Create a Project
$ django-admin startproject crudexample  
2. Create an App
$ python3 manage.py startapp employee  
3. Project Structure
Initially, our project looks like this:
crudexample
	__init__.py
	settings.py
	urls.py
	wsgi.py
employee
	migrations
		__init__.py
	__init__.py
	urls.py
	admin.py
	apps.py
	models.py
	tests.py
	views.py
manage.py

4. Database Setup
Create a database djangodb in mysql, and configure into the settings.py file of django project. See the example.
// settings.py
DATABASES = {  
    'default': {  
        'ENGINE': 'django.db.backends.mysql',  
        'NAME': 'djangodb',  
        'USER':'root',  
        'PASSWORD':'mysql',  
        'HOST':'localhost',  
        'PORT':'3306'  
    }  
}  
5. Create a Model
Put the following code into models.py file.
// models.py
from django.db import models  
class Employee(models.Model):  
    eid = models.CharField(max_length=20)  
    ename = models.CharField(max_length=100)  
    eemail = models.EmailField()  
    econtact = models.CharField(max_length=15)  
    class Meta:  
        db_table = "employee"  
6. Create a ModelForm
// forms.py
from django import forms  
from employee.models import Employee  
class EmployeeForm(forms.ModelForm):  
    class Meta:  
        model = Employee  
        fields = "__all__"  
7. Create View Functions
// views.py
from django.shortcuts import render, redirect  
from employee.forms import EmployeeForm  
from employee.models import Employee  

Create your views here.  
def emp(request):  
    if request.method == "POST":  
        form = EmployeeForm(request.POST)  
        if form.is_valid():  
            try:  
                form.save()  
                return redirect('/show')  
            except:  
                pass  
    else:  
        form = EmployeeForm()  
    return render(request,'index.html',{'form':form})  
def show(request):  
    employees = Employee.objects.all()  
    return render(request,"show.html",{'employees':employees})  
def edit(request, id):  
    employee = Employee.objects.get(id=id)  
    return render(request,'edit.html', {'employee':employee})  
def update(request, id):  
    employee = Employee.objects.get(id=id)  
    form = EmployeeForm(request.POST, instance = employee)  
    if form.is_valid():  
        form.save()  
        return redirect("/show")  
    return render(request, 'edit.html', {'employee': employee})  
def destroy(request, id):  
    employee = Employee.objects.get(id=id)  
    employee.delete()  
    return redirect("/show")  

8. Provide Routing
Provide URL patterns to map with views function.
// urls.py
from django.contrib import admin  
from django.urls import path  
from employee import views  
urlpatterns = [  
    path('admin/', admin.site.urls),
      path('', include('employee.urls')),
]  

8.1 Creating routing for employee urls
// employee/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.show),
    path('emp', views.emp),
    path('show', views.show),
    path('edit/<int:id>', views.edit),
    path('update/<int:id>', views.update),
    path('delete/<int:id>', views.destroy),
]

9. Organize Templates
Create a templates folder inside the employee app and create three (index, edit, show) html files inside the directory. files are-
// index.html
// show.html
//edit.html

10. Now create css, create a static/css folder in main directory parallel to manage.py and create a file style.css in it.
and now tell djnago about your css file, run command-
python manage.py collectstatic
(it will create a folder assets in main directory with your css file in it. You should run this command always after making changes in your css, js or images.)


11. Project Structure
Crudexample->
	__init__.py
	settings.py
	urls.py
	wsgi.py
employee->
	migrations->
		__init__.py
	__init__.py
	urls.py
	admin.py
	apps.py
	models.py
	tests.py
	views.py
	static->
		css->
			style.css
	templates->
		index.html
		show.html
		edit.html
	manage.py
	
12. Create Migrations
Create migrations for the created model employee, use the following command.
$ python3 manage.py makemigrations  
After migrations, execute one more command to reflect the migration into the database. But before it, mention name of app (employee) in INSTALLED_APPS of settings.py file.
// settings.py
INSTALLED_APPS = [  
    'django.contrib.admin',  
    'django.contrib.auth',  
    'django.contrib.contenttypes',  
    'django.contrib.sessions',  
    'django.contrib.messages',  
    'django.contrib.staticfiles',  
    'employee'  
]  

Run the command to migrate the migrations.
$ python3 manage.py migrate  

Now, our application has successfully connected and created tables in database. It creates 10 default tables for handling project (session, authentication etc) and one table of our model that we created.
See list of tables created after migrate command.

Run Server
To run server use the following command.
$ python3 manage.py runserver  

Access to the Browser
Access the application by entering localhost:8000/, it will show all the available employee records.
Initially, there is no record. So, it shows no record message. 
Well, we have successfully created a CRUD application using Django.
This complete project can be downloaded here.( https://github.com/diwamishra21/Django-crud-application)

13. Accessing admin panel, for that you have to create a super user using command-
python manage.py createsuperuser
(than follow instructions)


Debugging-

1. MySQL connection Error-
Solution-
django.core.exceptions.ImproperlyConfigured: Error loading MySQLdb module.
Did you install mysqlclient?
Ans-
pip install pymysql
Then, edit the __init__.py file in your project origin dir(the same as settings.py)
add:
import pymysql
pymysql.install_as_MySQLdb()

2. 'staticfiles' is not a registered tag library
Solution-
It's due to upgrading to Django3.0, use as mentioned above.
use-
{% load static %}
