# Django Cheat Sheet

## Install

- pip install django

## Start Project

- django-admin startproject <name_of_project> <project_location>

### Creates files, including

- _settings.py_ contains the boilerplate generated by Django and settings for your project.
- _urls.py_ contains the setup for the URL endpoints
- _manage.py_ is the command line utility for administrative tasks

## Run the development server

- python manage.py runserver

## Perform database migrations

- python manage.py migrate

## Start a new web application

- python manage.py startapp <name_of_app>

### In settings.py to set up app

- Under INSTALLED_APPS add the name of the app in quotes

## Create Model

1. Edit models.py
2. `python manage.py createmigrations`
3. `python manage.py migrate`
4. Register model in admin.py

```python
from django.db import models
from django.contrib.auth import get_user_model

class Thing(models.Model):
    name = models.CharField(max_length=256)
    rating = models.IntegerField(default=0)
    reviewer = models.ForeignKey(get_user_model(), on_delete=models.CASCADE)

    def __str__(self):
        return self.name
```

## Work on Views

### Detail & List Views

In `views.py:`

```python
from django.views.generic import ListView, DetailView
from .models import <ModelName>

class ThingsListView(ListView):
    template_name = "thing_list.html"
    model = Thing
    context_object_name = "things"

class ThingsDetailView(DetailView):
    template_name = "thing_detail.html"
    model = Thing
```

Create `urls.py` in the app folder.

```python
from .views import ThingListView, ThingDetailView

urlpatterns = [
    path('', ThingListView.as_view(), name='thing_list'),
    path('<int:pk>/', ThingDetailView.as_view(), name='thing_detail')
]
```

## Register the app `urls.py` file in the project `urls.py` file

## Register the model in `admin.py`:

```python
from django.contrib import admin
from .models import Thing

admin.onsite.register(Thing)
```

Register templates:

In `settings.py`:
under `TEMPLATES` add
    `"DIRS": [BASE_DIR/'templates'],`

## Adding CRUD Operations

### Create a `CREATE` view:

In `views.py`:

```python
from django.views.generic import CreateView

class ThingCreateView(CreateView):
    template_name = "thing_create.html"
    model = Thing
    fields = ["name", "reviewer"]
```

### Go to `urls.py`

```python
import ThingCreateView

urlpatterns = [
    path('create/', ThingCreateView.as_view(), name='thing_create')
]
```

### Create the template

```html
{% extends 'base.html' %}

{% block content %}
    <form method="POST">
        {% csrf_token %}
        {{ form }}
        <input class="bg-green-200" type="submit" value="Create Thing">
    </form>
{% endblock content %}
```

### In `models.py`

```python
from django.urls import reverse
```

In the Things class,

```python
def get_absolute_url(self):
    return reverse('thing_detail', args=str[(self.id)])
```

Note: Crispy Forms offers handy styling for forms!

## Delete

In `views.py`:

```python
import DeleteView
from django.urls import reverse.lazy

class ThingDeleteView(DeleteView):
    template_name = 'thing_delete.html'
    model = Thing
    success_url = reverse_lazy("thing_list")
```

In `urls.py`:

```python
import ThingDeleteView

path('<int:pk>/delete', ThingDeleteView.as_view(), name='thing_delete')
```

Create `thing_delete.html`

```html
{% extends 'base.html' %}

{% block content %}
    <h1>Delete Thing</h1>
    <form method="POST">
        {% csrf_token %}
        <p>Do you really want to delete {{ thing.name }}?</p>
        <input class="text-red-500" type="submit" value="Delete {{ thing.name }}">
    </form>
{% endblock content %}
```

## Update

```python
template_name = "thing_update.html"
model = Thing
fields = "__all__"
```



