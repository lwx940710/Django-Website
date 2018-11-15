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
      # In app urls.py
      path('<int:model1_id>', views.detail, name='detail'),
      # In app views.py
      def detail(request, model1_id):
          return HttpResponse("some info in html")
    ```
  * ##### Connect to database
    ```python
      # In app views.py
      from .models import Model1
      
      def index(request):
          all_models = Model1.objects.all()
          html = ''
          for model in all_models:
              url = '/appname/' + str(model.pk) + '/'
              html += '<a href="' + url + '">' + model.title + '</a><br>'
          return HttpResponse(html)
    ```
   * ##### Template to separate html from python
     ###### Create template(Default)
     1. In app folder, create a new directory "templates"
     2. In "templates", create a new directory "appname"
     3. In "appname", create html5 templates
     ###### Import template
     ```python
       # In app views.py
       from django.template import loader
     
       def index(request)
           all_models = Model1.objects.all()
           template = loader.get_template('appname/index.html')
           context = {
               'all_models': all_models,
           }
           return HttpResponse(template.render(context, request))
     ```
     ```html
       # In index.html
       {% if all_albums %}
           <ul>
               {% for model in all_models %}
               <li><a href="/appname/{{ model.id }}/">{{ model.title }}</a><li>
               {% endfor %}
           <ul>
       {% else %}
           <h3>No items</h3>
       {% endif %}
     ```
   * ##### Render template shortcut
     ```python
       # In app views.py
       # Instead of importing template
       from django.shortcuts import render
       
       def index(request):
           all_models = Model1.objects.all()
           context = {
               'all_models': all_models,
           }
           return render(request, 'appname/index.html', context) # render() will automatically create valid HttpResponse
     ```
   * ##### Raise a 404 error
     ```python
       # In app views.py
       from django.http import Http404
       
       def detail(request, model_id):
           try:
               model = Model1.objects.get(pk=model_id)
           except Model.DoesNotExist:
               raise Http404("Item does not exist")
           return render(request, 'appname/detail.html', {'model': model}) # {'model': model} can be used when it's the only entry in context
     ```
   * ##### Add Model2 to database
     ###### Create Model2
     ```python
       # In app models.py
       class Model2(models.Model):
           m1 = models.ForeignKey(Model1, on_delete=models.CASCADE)
           title = models. CharField(max_length=10)
           column2 = models. fieldtype(max_length=100)
           
           def __str__(self):
               return self.title + ' - ' + str(self.id)
       
       # In app admin.py
       from .models import Model2
       
       admin.site.register(Model2)
     ```
     ```bash
       # If the model structure is changed
       python manage.py makemigration appname
       python manage.py migrate
     ```
     ```bash
       # To add data
       python manage.py shell
       from appname.models import Model1, Model2
       
       model1 = Model1.objects.get(pk=1)
       model2 = Model()
       model2.m1 = model1
       ...
     ```
     ###### Get related object set
     ```bash
       python manage.py shell
       from appname.models import Model1, Model2
       
       model1 = Model1.objects.get(pk=1)
       model1.m1_set.all()
       model1.m1_set.count() # return the number of items related to model1 in Model2
       
       # Another way to add data - create()
       album1.song_set.create() # specify every field besides m1
     ```
   * ##### Create detail template
     ```html
       <body>
           <h1>{{ model1.column1 }}</h1>
           <h2>{{ model1.column2 }}</h2>
           <ul>
               {% for model in model1.m1_set.all %}
                   <li>{{ model.title }} - {{ model.column2 }}</li>
               {% endfor %}
           </ul>
       </body>
     ```
   
     
       
       
       
