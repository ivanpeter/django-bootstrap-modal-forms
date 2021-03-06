
============================
Django Bootstrap Modal Forms
============================

A jQuery plugin for creating AJAX driven Django forms in Bootstrap modal.

Installation
============

1. Install django-bootstrap-modal-forms::

    $ pip install django-bootstrap-modal-forms

2. Add "bootstrap_modal_forms" to your INSTALLED_APPS setting::

    INSTALLED_APPS = [
        ...
        'bootstrap_modal_forms',
        ...
    ]

3. Include Bootstrap, jQuery and jquery.bootstrap.modal.forms.js on every page where you would like to set up the AJAX driven Django forms in Bootstrap modal.

IMPORTANT: Adjust Bootstrap and jQuery file paths to match yours, but include jquery.bootstrap.modal.forms.js exactly as in code bellow. 

.. code-block:: html+django

    <head>
        ...
        <link rel="stylesheet" href="{% static "assets/css/bootstrap.css" %}">
    </head>

    <body>
        ...
        <script src="{% static "assets/js/bootstrap.js" %}"></script>
        <script src="{% static "assets/js/jquery.js" %}"></script>
        <script src="{% static "js/jquery.bootstrap.modal.forms.js" %}"></script>
    </body>

Usage
=====

1. Form
*******

Define either Django Form or ModelForm. django-bootstrap-modal-forms works with both.

.. code-block:: python

    forms.py
    
    from django import forms
    from .models import Test

    class TestForm(forms.ModelForm):
        class Meta:
            model = Test
            fields = ['test_field_1', 'test_field_2', ]

2. Form's html
**************

.. code-block:: html

    test/create_test.html
    
    <form method="post" action="{% url 'test:create_test' %}">
      {% csrf_token %}
      
     <div class="modal-header">
        <h5 class="modal-title">Create a new test</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>

      <div class="modal-body">
        {% for field in form %}
          <div class="form-group{% if field.errors %} invalid{% endif %}">
            <label for="{{ field.id_for_label }}">{{ field.label }}</label>
            {% field %}
            {% for error in field.errors %}
              <p class="help-block">{{ error }}</p>
            {% endfor %}
          </div>
        {% endfor %}
      </div>

      <div class="modal-footer">
        <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
        <button type="submit" class="btn btn-primary" formnovalidate="formnovalidate">Create</button>
      </div>
    
    </form>

- Define form's html and save it as Django template.
- Bootstrap 4 modal elements are used in this example.
- Form should POST to url defined in #4.
- Add "invalid" class or custom errorClass to the elements that wrap the fields.
- "invalid" class acts as a flag for the fields having errors after the form has been POSTed.

3. Class-based view
*******************

Define a class-based view that processes the form defined in #1 and uses the template defined in #2.

.. code-block:: python

    views.py
    
    from django.shortcuts import render
    from django.views.generic.edit import CreateView
    
    from .forms import TestForm
    
    class TestFormView(CreateView):
        template_name = 'test/create_test.html'
        form_class = TestForm
        success_url = '/'

4. URL for the view
*******************

Define URL for the view in #3.

.. code-block:: python

    from django.urls import path
    
    from . import views
    
    app_name = 'test'
    urlpatterns = [
        path('', views.index, name='index'),
        path('test/create_test/', views.TestFormView.as_view(), name='create_test')
    ]

5. Bootstrap modal and trigger element
**************************************

Define the Bootstrap modal window and trigger element.

.. code-block:: html

    test/index.html

    <div class="modal fade" tabindex="-1" role="dialog" id="modal">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
        
        </div>
      </div>
    </div>
    
    <button type="button" class="btn btn-primary" id="createTest">
      <span class="fa fa-plus fa-sm"></span>
      New Test
    </button>

- Same modal window can be used for multiple modalForms in single template (see #6). 
- Form's html from #2 is loaded within ``<div class="modal-content"></div>``.
- Trigger element (in this example button) selected with id selector is used for instantiation of modalForm (see #6).
- Any element can be trigger element as long as modalForm is bound to it.

6. modalForm
************

Add script to the template from #5 and bind the modalForm to the trigger element. Set URL defined in #3 as formURL property of modalForm.

If you want to create **more modalForms in single template using the same modal window** from #5, repeat steps #1 to #4, create new trigger element as in #5 and bind the new modalForm with unique URL to it.

.. code-block:: html

    test/index.html

    <script type="text/javascript">
    $(document).ready(function() {
        
        $("#createTest").modalForm({
            formURL: "/test/create_test/"
        });
    
    });
    </script>

Options
=======

IMPORTANT: All the code in the examples above uses default options. If you customize any option adjust the code of the examples accordingly.

modalID
  Sets the custom id of the modal. ``Default: "#modal"``

modalContent
  Sets the custom class of the element to which the form's html is appended. ``Default: ".modal-content"``

modalForm
  Sets the custom form selector. ``Default: ".modal-content form"``

formURL
  Sets the unique id of the modal. ``Default: null``

errorClass
  Sets the custom errorClass for the form fields. ``Default: ".invalid"``


How it works
============

1. Click event on trigger element opens modal with ``modalID``
2. Form at ``formURL`` is appended to the element with ``modalContent`` class
3. On submit the form is POSTed via AJAX request to ``formURL``

- If the form is invalid it's updated with the errors
- If the form is valid the view redirects to defined ``success_url`` (see #3)

Contribute
==========

This is an Open Source project and any contribution is appriciated.

License
=======

This project is licensed under the MIT License.

