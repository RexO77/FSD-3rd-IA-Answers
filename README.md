### Module 4

1. **Design and develop code snippet for registering student and displaying a list of students. When a student is selected, display more details about student using Django ListView and DetailView.**

**Code Snippet:**
```python
# models.py
from django.db import models

class Student(models.Model):
    name = models.CharField(max_length=100)
    age = models.IntegerField()
    email = models.EmailField()

# views.py
from django.views.generic import ListView, DetailView
from .models import Student

class StudentListView(ListView):
    model = Student
    template_name = 'students/student_list.html'

class StudentDetailView(DetailView):
    model = Student
    template_name = 'students/student_detail.html'

# urls.py
from django.urls import path
from .views import StudentListView, StudentDetailView

urlpatterns = [
    path('students/', StudentListView.as_view(), name='student-list'),
    path('students/<int:pk>/', StudentDetailView.as_view(), name='student-detail'),
]

# student_list.html
{% for student in object_list %}
    <p><a href="{% url 'student-detail' student.pk %}">{{ student.name }}</a></p>
{% endfor %}

# student_detail.html
<p>Name: {{ object.name }}</p>
<p>Age: {{ object.age }}</p>
<p>Email: {{ object.email }}</p>
```

2. **What is a generic view? List the purpose of generic views. Design and develop the code snippet for generic view.**

**Generic View:**
A generic view is a class-based view in Django that provides a way to handle common web development tasks like displaying a list of objects, handling forms, and displaying detailed object information.

**Purpose of Generic Views:**
- Simplify common patterns
- Reduce code repetition
- Enhance reusability and maintainability

**Code Snippet for Generic View:**
```python
# views.py
from django.views.generic import ListView
from .models import Student

class StudentListView(ListView):
    model = Student
    template_name = 'students/student_list.html'

# urls.py
from django.urls import path
from .views import StudentListView

urlpatterns = [
    path('students/', StudentListView.as_view(), name='student-list'),
]

# student_list.html
{% for student in object_list %}
    <p>{{ student.name }}</p>
{% endfor %}
```

3. **What is a MIME type? List different MIME types. Design and develop the code snippet for using MIME type.**

**MIME Type:**
MIME (Multipurpose Internet Mail Extensions) type is a standard that indicates the nature and format of a document, file, or assortment of bytes.

**Different MIME Types:**
- `text/html`: HTML document
- `text/plain`: Plain text
- `application/json`: JSON data
- `application/pdf`: PDF document
- `image/jpeg`: JPEG image

**Code Snippet for Using MIME Type:**
```python
# views.py
from django.http import HttpResponse

def pdf_view(request):
    with open('path/to/file.pdf', 'rb') as pdf:
        response = HttpResponse(pdf.read(), content_type='application/pdf')
        response['Content-Disposition'] = 'inline;filename=file.pdf'
        return response
```

4. **Design and develop code snippet to demonstrate the usage of queryset, template-object-name, extra-context.**

**Code Snippet:**
```python
# views.py
from django.views.generic import ListView
from .models import Student

class StudentListView(ListView):
    model = Student
    template_name = 'students/student_list.html'
    context_object_name = 'students'
    extra_context = {'title': 'Student List'}

# student_list.html
<h1>{{ title }}</h1>
{% for student in students %}
    <p>{{ student.name }}</p>
{% endfor %}
```

5. **Design and develop code snippet to demonstrate the concept of complex filtering with wrapper functions.**

**Code Snippet:**
```python
# views.py
from django.shortcuts import render
from .models import Student

def student_list_view(request):
    students = Student.objects.filter(age__gte=18)
    return render(request, 'students/student_list.html', {'students': students})

# student_list.html
{% for student in students %}
    <p>{{ student.name }}</p>
{% endfor %}
```

6. **Design and develop a program to display the latest five blogs using a simple feed.**

**Code Snippet:**
```python
# views.py
from django.contrib.syndication.views import Feed
from .models import Blog

class LatestEntriesFeed(Feed):
    title = "Latest Blogs"
    link = "/blogs/"
    description = "Updates on the latest blogs."

    def items(self):
        return Blog.objects.order_by('-created')[:5]

    def item_title(self, item):
        return item.title

    def item_description(self, item):
        return item.body

# urls.py
from django.urls import path
from .views import LatestEntriesFeed

urlpatterns = [
    path('feed/', LatestEntriesFeed(), name='blog-feed'),
]
```

7. **What is a sitemap framework. Develop an XML file for sitemap framework.**

**Sitemap Framework:**
The sitemap framework in Django is used to generate XML sitemaps which help search engines index website content efficiently.

**XML File for Sitemap Framework:**
```python
# views.py
from django.contrib.sitemaps import Sitemap
from django.urls import reverse
from .models import Blog

class BlogSitemap(Sitemap):
    changefreq = "daily"
    priority = 0.8

    def items(self):
        return Blog.objects.all()

    def location(self, item):
        return reverse('blog-detail', args=[item.pk])

# urls.py
from django.contrib.sitemaps.views import sitemap
from .sitemaps import BlogSitemap

sitemaps = {
    'blogs': BlogSitemap,
}

urlpatterns = [
    path('sitemap.xml', sitemap, {'sitemaps': sitemaps}, name='django.contrib.sitemaps.views.sitemap'),
]
```

8. **Design and develop a code snippet for GenericSitemap.**

**Code Snippet:**
```python
# views.py
from django.contrib.sitemaps import GenericSitemap
from .models import Blog

info_dict = {
    'queryset': Blog.objects.all(),
    'date_field': 'created',
}

# urls.py
from django.contrib.sitemaps.views import sitemap
from .views import info_dict

sitemaps = {
    'blogs': GenericSitemap(info_dict, priority=0.6),
}

urlpatterns = [
    path('sitemap.xml', sitemap, {'sitemaps': sitemaps}, name='django.contrib.sitemaps.views.sitemap'),
]
```

9. **Design and develop programs for getting and setting cookie.**

**Code Snippet:**
```python
# views.py
from django.http import HttpResponse

def set_cookie_view(request):
    response = HttpResponse("Setting a cookie")
    response.set_cookie('user', 'John Doe')
    return response

def get_cookie_view(request):
    user = request.COOKIES.get('user')
    return HttpResponse(f'User is {user}')
```

10. **Design and develop programs to use sessions in views.**

**Code Snippet:**
```python
# views.py
from django.shortcuts import render
from django.http import HttpResponse

def set_session_view(request):
    request.session['user'] = 'John Doe'
    return HttpResponse("Session set")

def get_session_view(request):
    user = request.session.get('user')
    return HttpResponse(f'User is {user}')
```

11. **Briefly explain how authentication and authorization system works in Django.**

**Explanation:**
Django's authentication system handles user accounts, groups, permissions, and cookie-based user sessions. Authentication verifies user credentials, while authorization determines user permissions for accessing resources. Django provides built-in views and forms for login, logout, and password management.

---

### Module 5

1. **What is overlaid function. List features of Web 2.0**

**Overlaid Function:**
An overlaid function refers to the use of multiple technologies or functionalities on top of each other to create a composite application, especially seen in Web 2.0 applications.

**Features of Web 2.0:**
- User-generated content
- Rich user experiences
- Dynamic content
- Scalability
- APIs for third-party integration

2. **List the technologies on which Ajax is overlaid. Briefly explain JavaScript and CSS.**

**Technologies on which Ajax is overlaid:**
- HTML/XHTML
- CSS
- JavaScript
- DOM
- XML/JSON
- XMLHttpRequest

**JavaScript:**
JavaScript is a high-level, interpreted programming language that enables interactive web pages and dynamic content.

**CSS:**
Cascading Style Sheets (CSS) is a style sheet language used for describing the presentation of a document written in HTML or XML.

3. **Briefly explain HTML/XHTML, XML, JSON, DOM**

**HTML/XHTML:**
HTML (HyperText Markup Language) and XHTML (Extensible HyperText Markup Language) are markup languages for creating web pages.

**XML:**
XML (eXtensible Markup Language) is a markup language used for storing and transporting data.

**JSON:**
JSON (JavaScript Object Notation) is a lightweight data interchange format, easy for humans to read and write and for machines to parse and generate.

**DOM:**
DOM (Document Object Model) is a programming interface for HTML and XML documents that represents the page structure as a tree of objects.

4. **What is XMLHttpRequest? List and briefly explain the properties and methods of XMLHttpRequest.**

**XMLHttpRequest:**
XMLHttpRequest is an API in the form of an object that is used to transfer data between a web browser and a web server. It is commonly used in AJAX programming.

**Properties of XMLHttpRequest:**
- `onreadystatechange`: An event handler for an event that fires at every state change.
- `readyState`: The state of the request (0-4, where 4 means the request is complete).
- `responseText`: The response data as a string.
- `responseXML`: The response data as an XML document.
- `status`: The HTTP status code of the response.
- `statusText`: The HTTP status text of the response.

**Methods of XMLHttpRequest:**
- `open(method, url, async, user, password)`: Initializes the request.
- `send(body)`: Sends the request.
- `abort()`: Aborts the request.
- `setRequestHeader(header, value)`: Sets the value of an HTTP request header.
- `getResponseHeader(header)`: Returns the string containing the text of a specified header.

5. **Develop code snippets to demonstrate the usage of XMLHttpRequest in JavaScript code.**

**Code Snippet:**
```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', 'https://api.example.com/data', true);
xhr.onreadystatechange = function() {
    if (xhr.readyState === 4 && xhr.status === 200) {
        console.log(xhr.responseText);
    }
};
xhr.send();
```

6. **List and demonstrate the usage of jQuery selectors, with examples.**

**jQuery Selectors:**
- `$("#id")`: Selects an element by ID.
- `$(".class")`: Selects elements by class.
- `$("element")`: Selects elements by tag name.
- `$("element.class")`: Selects elements by tag name and class.
- `$("element:first")`: Selects the first element.

**Examples:**
```javascript
$(document).ready(function(){
    $("#myId").css("color", "red");
    $(".myClass").hide();
    $("p").click(function(){
        $(this).hide();
    });
    $("div.myClass").fadeIn();
    $("p:first").css("font-size", "20px");
});
```

7. **Develop a program to demonstrate ajax calls using jQuery.**

**Code Snippet:**
```javascript
$(document).ready(function(){
    $("#loadData").click(function(){
        $.ajax({
            url: "https://api.example.com/data",
            method: "GET",
            success: function(result){
                $("#dataContainer").html(result);
            },
            error: function(error){
                console.log("Error:", error);
            }
        });
    });
});
```

8. **With respect to jQuery ajax explain data, dataType, dataFilter, success, error.**

**Explanation:**
- `data`: Data to be sent to the server with the request.
- `dataType`: The type of data expected back from the server (e.g., "json", "xml", "html").
- `dataFilter`: A function to modify the raw response data before it is passed to the success callback.
- `success`: A function to be called if the request succeeds.
- `error`: A function to be called if the request fails.

9. **Design and develop a jQuery program for autocomplete.**

**Code Snippet:**
```html
<!-- HTML -->
<input id="search" type="text" placeholder="Search...">

<!-- jQuery UI Autocomplete -->
<script>
$(function() {
    var availableTags = [
        "ActionScript",
        "AppleScript",
        "Asp",
        "BASIC",
        "C",
        "C++",
        "Clojure",
        "COBOL",
        "ColdFusion",
        "Erlang",
        "Fortran",
        "Groovy",
        "Haskell",
        "Java",
        "JavaScript",
        "Lisp",
        "Perl",
        "PHP",
        "Python",
        "Ruby",
        "Scala",
        "Scheme"
    ];
    $("#search").autocomplete({
        source: availableTags
    });
});
</script>
```

