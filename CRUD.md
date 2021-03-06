## CRUD OPEARATIONS
CRUD stands for Create, Read, Update & Delete. These are the four basic operations which are executed on Database Models. We are developing a web app which is capable of performing these operations.


![](https://github.com/lavanya-Mercy/Crud/blob/master/Crud_imgs/curddd.jpg) 

**Create**   – create or add new entries in a table in the database. <br>
**Retrieve** – read, retrieve, search, or view existing entries as a list(List View) or retrieve a particular entry in detail.<br>
**Update**   – update or edit existing entries in a table in the database. <br>
**Delete**   – delete, deactivate, or remove existing entries in a table in the database. <br>

###### Example:
Majority of applications on the internet are CRUD applications. For example – Facebook uses CRUD operations to save your data on their database. You can change your profile picture that means perform the update operation. Of course, you can see the data in-app or browser which is read operation. Also, you can delete your Facebook account which is delete operation.

### Making CRUD application
##### 1. Create a new project by executing the following command <br>
 	 ```django-admin startproject ProjectName```
##### 2. Creating new App 
Move to the directory of manage.py file and make a new app <br>
  ```python manage.py startapp crud``` 
  
Don’t forget to add your crud app to the Installed app. Append crudApp/settings.py as follows <br>
**`settings.py`** 

```python
INSTALLED_APPS = [  
      'django.contrib.admin',  
      'django.contrib.auth',  
      'django.contrib.contenttypes',  
      'django.contrib.sessions',  
      'django.contrib.messages',  
      'django.contrib.staticfiles',  
      'crud',  
     ] 
```

  
#####  3. Making Model Forms in app
A Form class describes a form and determines how it works and appears.In a similar way that a model class’s fields map to database fields, a form class’s fields map to HTML form <input> elements.

**`forms.py`**
    
```python
from django.forms import ModelForm
from crud.models import Register
class registrationform(ModelForm):
	class Meta:
	   model = Register
	   fields = '__all__'
```
We need to import Django forms first (from django import forms) and our Register model (from .models import Register). Next, we have class Meta, where we tell Django which model should be used to create this form (model = Register). Finally, we can say which field(s) should end up in our form. In this scenario if we want only few fields then metion them in a list formate.


##### 4. Registering Model in django Admin
Here we are editing admin.py existing in app folder. Import the model you want to register in the admin.<br>
**`admin.py`**
```python
from django.contrib import admin
from crud.models import Register

admin.site.register(Register)
```
##### 5. Sync with Database
* To implement all of this, run these commands in the command line

* Makemigrations : It is used to create a migration file that contains code for the tabled schema of a model. <br>
	```python manage.py makemigrations```

* Migrate : It creates table according to the schema defined in the migration file. <br>
	```python manage.py migrate```

##### 6. Making Views Function for Django crud app
* The view functions are our actual CRUD operations in Django. Now, we are editing views.py in app folder

###### *Read Opearation* : 
This function is performing Read Operation. In this function, we simply retrieve all the objects in the crud table .

**`views.py`**
```python
def details(request):
	data = Register.objects.all()
	return render(request,'crud/details.html',{'data':data})
```
> **_NOTE:_** Querysets is used to retrieve data from Tables is Model_className.objects.all()

###### *Update operation* : 
This function is performing Update Operation. If the object exists it will return the form filled with the object’s information in it. The user can change the form again. In this case, there will be no creation of new registration but the editing of the existing object.

**`viwes.py`**
```python
	def edit(request,id):
		data = Register.objects.get(id=id)
		if request.method =='POST':
			form = registrationform(request.POST,instance=data)
			if form.is_valid():
				form.save()
				return redirect('/crud/details')
		form = registrationform(instance=data)
		return render(request,'crud/edit.html',{'form':form,'data':data})
```
> **_NOTE:_** Querysets is used to update data is Model_className.objects.get(id=1)

###### *Delete Operation* : 

Delete Function is the last function of the CRUD application. We are again using the same object method as with Update function. We are passing the request and id to delete.

**`viwes.py`**
```python
def delete(request,id):
	ob = Register.objects.get(id=id)
	if request.method == 'POST':
		ob.delete()
		return redirect('/crud/details')
	return render(request,'crud/msg.html',{'info':ob})
```
> **_NOTE:_** Querysets is used to delete data from Tables is Model_className.objects.delete(id=1)

##### 7. Enabling the Templates

> **_NOTE:_** Here we are using Bootstrap 4

This html page shows the details of users. You want to update the specific user details it redirect to update page and also if we want to delete the user details with user confirmation.

**`details.html`**
```
{% load static %}
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
  	<meta name="viewport" content="width=device-width, initial-scale=1">
	<title>Details Page</title>
	<link rel="stylesheet" type="text/css" href="{% static 'css/bootstrap.min.css' %}">
	<script type="text/javascript" src="{% static 'js/bootstarp.min.js' %}"></script>
</head>
<body>
	<div class="container">
		<div class="row justify-content-left">
			<div class="col-sm-8">
				<div class="card">
					<div class="card-header">
					User Details
					</div>
				<div class="card-body">
			<table class="table table-striped responsive ">
				{% for row in data %}
					<tr>
					<td>{{row.id}}</td>
					<td>{{row.firstName}}</td>
					<td>{{row.lastName}}</td>
					<td>{{row.emailId}}</td>
					<td><a class='btn btn-info'href="{%url 'edit' row.id %}">Update</a></td>
					<td><a class='btn btn-info'href="{%url 'delete' row.id %}">Delete</a></td>
					</tr>
				{% endfor %}
			</table>
		     </div>
		</div>
	    </div>
      </div>
</div>
</body>
</html>
```

This page shows the given id details.You can now change the information accordingly and update. This will change the data in the database.

**`edit/id.html`**
```
<!DOCTYPE html>
<html>
<head>
    <title>Updation</title>
	<meta charset="utf-8">
	<meta name="viewport" content="width=device-width, initial-scale=1">
	<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css">
	<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js"></script>
</head>
<body>
	<div class="container">
		<div class="row justify-content-left">
			<div class="card">
			<div class="card-header bg-info">Updation Page</div>
			<div class="card-body">
				<form method="POST">
					{% csrf_token %}
					<table>
						{{ form.as_p }}
					</table>
					<div class="card-footer">
					<input class="btn btn-info" type="submit" name="Update" value="Submit">
					</div>
				</form>
			</div>
		</div>
		</div>
	</div>
</body>
</html>
```

This page for confirmation message, when you click the delete button in a app object, the object will be deleted from the database. and redirect to details page again and if the user click on cancel just redirect back to details page.

**`msg.html`**
```
{% load static %}
<!DOCTYPE html>
<html>
<head>
	<title>User Form</title>
	<link rel="stylesheet" type="text/css" href="{% static 'css/bootstrap.min.css' %}">
	<script type="text/javascript" src="{% static 'js/bootstarp.min.js' %}"></script>
</head>
<body>
	<div class="container">
		<div class="row justify-content-left">
			<div class="card">
				<div  class="card-body">
					<form action="{% url 'delete' info.id %}" method="POST">
		{% csrf_token %}
	<h2>Are you sure to delete {{ info.firstName }}</h2>
	<input class='btn btn-warning' type="submit" name="submit" value="Delete">
	<a href="{% url 'details' %}" class="btn btn-info">Cancel</a>	
</form>
				</div>
			</div>
		</div>
	</div>
</body>
</html>

```


> **_NOTE:_** if id doesn't exit in the database in shows an error


##### 8. Configuring URLs
To present data we need to write a ‘views’ which is mapped to a URL pattern. Editing urls.py allows URL patterns to be defined.

In the main url file we have to include our apps url file .For that we have to add the url.py file path
**`urls.py`**
```python
from django.contrib import admin
from django.urls import path,include
urlpatterns = [
    path('admin/', admin.site.urls),
    path('crud/',include('crud.urls')),

]
```
Now in our app we have to create a urls.py file,and in that file we have to add the html files path.

**`urls.py`**
```python
from django.contrib import admin
from django.urls import path
from crud import views
urlpatterns = [
	path('details/',views.details,name='details'),
	path('edit/<int:id>',views.edit,name='edit'),
	path('delete/<int:id>',views.delete,name='delete'),

]
```

##### 9. Running Server and Testing 

To test the website, start your server by
``` python manage.py runserver ```

Open web page with localhost and required URL given in your project  urls.py file.

``` http://127.0.0.1:8000/crud/details/ ```

![](https://github.com/lavanya-Mercy/Crud/blob/master/Crud_imgs/details.PNG)

``` http://127.0.0.1:8000/crud/edit/6 ```

![](https://github.com/lavanya-Mercy/Crud/blob/master/updatee.PNG)

``` http://127.0.0.1:8000/crud/delete/2 ```


![](https://github.com/lavanya-Mercy/Crud/blob/master/Crud_imgs/del.PNG)



