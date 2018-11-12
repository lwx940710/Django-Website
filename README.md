# Django Website

## Study Notes

  * ##### Start a new project
    ###### In Terminal
    ```bash
      django-admin startproject projectname
    ```
  * ##### Start the server
    ###### In Terminal
    ```bash
      python manage.py runserver
    ```
  * ##### Create a new app
    ###### In Terminal
    ```bash
      python manage.py startapp appname
    ```
  * ##### Add the app to the root urls.py
    ###### In root urls.py
    ```python
      from django.urls import include
      # add to urlpatterns
      url('appname/', include(appname.urls)),
    ```
    ###### In app urls.py
    ```python
      from django.contrib import admin
      from django.urls import path
      # add to urlpatterns
      path('', views.index, name='index')    # '' for index page
    ```   
  * ##### Create models
    ###### In app models.py
    ```python
    class Model1(models.model):
        column1 = models.DataType(max_length=250)
        column2 = models.DataType(max_length=250)
        
        def __str__(self): # used to show actual content when asked by Model1.objects.all() in shell
          return self.column1 + '  ' + self.column2
        
    class Model2(models.model):
        column3 = models.ForeighKey(Model1, on_delete=models.CASADE) # CASADE used for composition relationship
    ```
  * ##### Activate models
    ###### In app apps.py
    ```python
      # add to INSTALLED_APPS
      'appname',
    ```
    ###### In Terminal
    ```bash
      python manage.py makemigration appname
      python manage.py migrate
    ```
    ```bash
      # to check
      python manage.py sqlmigrate appname idnumber
    ```
  * ##### Database API
    ```bash
      python manage.py shell
      
      # import
      from appname.models import Model1, Model2
      
      # to check content
      Model1.objects.all()
      
      # create an object
      a = Model1(column1="someinfo", column2="someinfo")
      a.save()
      # another way to create an object
      b = Model1()
      b.column1 = "someinfo"
      b.column2 = "someinfo"
      b.save()
      
      # to check the content of a
      a.column1
      a.column2
      a.id # primary key, can also be a.pk
      
      # filter
      Model1.objects.filter(column1="someinfo")
      Model1.objects.filter(id=1) # =pk
      Model1.objects.filter(column1__startswith='somechar')
    ```  
  * ##### Admin
    ###### Create admin account
    ```bash
      python manage.py createsuperuser
    ```
    ######
    ###### To display models on admin site
    ```python
      # In app admin.py
      from .models import Model1
      admin.site.register(Model1)
    ```
  * ##### Display data detail
    ```python
      # in app urls.py
      path('<int:model1_id>', views.detail, name='detail'),
      # in app views.py
      def detail(request, model1_id):
          return HttpResponse("some info in html")
    ```
      
