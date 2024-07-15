# Django

## Project Architecture

## Project Structure
Use [Coockicutter](https://github.com/KUWAITNET/kn-django-cookiecutter) for project layout

## Code Organization for app
```
* __init__.py

* admin.py

* context_processors.py

* feeds.py

* forms.py

* managers.py

* middleware.py

* models.py

* receivers.py

* signals.py

* apis

    * __init__.py

    * views.py

    * serializers.py

* templates/app_name/

* templatetags/

    * __init__.py

    * app_name.py

* tests.py or tests/

* urls.py

* views.py
```
What lives in each of these files should be self-explanatory.

## Models
!!! note "Note: Follow Django’s defined conventions for model code."

### Fat models
A common pattern in MVC-style programming is to build thick/fat models and thin controllers. For Django this translates to building models with lots of small methods attached to them and views which use those methods to keep their logic as minimal as possible. There are lots of benefits to this approach.

1. DRY: Rather than repeating the same logic in multiple views, it is defined once on the model.

2. Testable: Breaking up logic into small methods on the model makes your code easier to unit test.

3. Readable: By giving your methods friendly names, you can abstract ugly logic into something that is easily readable and understandable.

For a good example of a fat model in Django, look at the [definition of django.contrib.auth.models.User](https://github.com/django/django/blob/ff6ee5f06c2850f098863d4a747069e10727293e/django/contrib/auth/models.py#L225-404).

## Managers and QuerySets
When models.py grows too large, a common remedy is to move any custom model managers and Queryset to managers.py module.

## Forms/serializers
* Validate All Incoming Data With Django Forms or serializers

* Use the POST Method as much as possible

* Every HTML form that alters data must submit its data via the POST method

* Posting Data via AJAX

* Add Errors to Forms With Form.add_error()
``` py
from django import forms

class ReviewForm(forms.Form):
  def clean(self):
    cleaned_data = super(ReviewForm, self).clean()
    flavor = cleaned_data.get('flavor')
    age = cleaned_data.get('age')
    if flavor == 'coffee' and age < 3:
      # Record errors that will be displayed later.
      msg = 'Coffee Ice Cream is not for Babies.'
      self.add_error('flavor', msg)
      self.add_error('age', msg)

    # Always return the full collection of cleaned data.
    return cleaned_data
```

## Views
* Try to Keep Business Logic Out of Views

* Don’t Use locals() as Views Context

    Do not do this.

    ``` py
    def store_display(request, store_id):
        store = get_object_or_404(Store, id=store_id)
        date = timezone.now()
        return render(request, 'report.html', locals())
    ```

    Do this.

    ``` py
    def store_display(request, store_id):
        store = get_object_or_404(Store, id=store_id)
        date = timezone.now()

        context = {
            'store': store,
            'now': date
        }
        return render(request, 'report.html', context)
    ```

* Less view code is better.

* Never repeat code in views.

* Views should handle presentation logic. Try to keep business logic in models when possible, 
or in forms if you must.

* Keep your views simple.

* Keep your mixins simpler.

## Templates
* Flat Is Better Than Nested

* Limit Processing in Templates

**Don’t do this: conditional filtering in templates**

``` html
<ul>
  {% for voucher in voucher_list %}
    {% if 'greenfeld' in voucher.name.lower %}
      <li>{{ voucher.name }}</li>
    {% endif %}
  {% endfor %}
</ul>
```

* Prefer underscores over dashes in template names, block names, and other names in templates. Most Django users seem to follow this convention. Why? Well, because underscores are allowed in names of Python objects but dashes are forbidden.

* Rely on clear, intuitive names for blocks. {% block javascript %} is good.

* Include the name of the block tag in the endblock. Never write just {% endblock %}, include the whole {% endblock javascript %}.

* Templates called by other templates are prefixed with ‘_’. This applies to templates called via {% include %} or custom template tags. It does not apply to templates inheritance controls such as {% extends %} or {% block %}.

* Use Implicit and Named Explicit Context Objects Properly

* Use URL Names Instead of Hardcoded Paths

## Tests
* Each Test Method Tests One Thing

* For Views, When Possible Use the Request Factory

``` py
class SavoryIceCreamTest(TestCase):
  def setUp(self):
    # Every test needs access to the request factory.
    self.factory = RequestFactory()

  def test_cheese_flavors(self):
    request = self.factory.get('/cheesy/broccoli/')
    request.user = AnonymousUser()
    # Annotate the request object with a session
    request = add_middleware_to_request(request, SessionMiddleware)
    request.session.save()
    # process and test the request
    response = cheese_flavors(request)
    self.assertContains(response, 'bleah!')
```

* Don’t Write Tests That Have to Be Tested

* Things That Should Be Tested

    * Views: Viewing of data, changing of data, and custom class-based view methods

    * Models: Creating/updating/deleting of models, model methods, model manager methods.

    * Forms: Form methods, clean() methods, and custom fields.

    * Validators: Write multiple test methods against each custom validator you write.

    * Signals: Signals can cause grief especially if you lack tests on them.

    * Filters: Filters are essentially just functions accepting one or two arguments, writing tests for them should be easy

    * Template Tags: Since template tags can do anything and can even accept template context, writing tests often becomes much more challenging. This means you really need to test them, since otherwise you may run into edge cases.

* Use Mock

``` py
@mock.patch.object(requests, 'get')
def test_request_failure_ssl(self, get):
  """Test if we can handle SSL problems elegantly."""
  get.side_effect = requests.exception.SSLError()
  with self.assertRaises(CantListFlavors):
    list_flavors_sorted()
```

## How to develop an API?
* Use Consistent API Module Naming

* Code for a Project Should Be Neatly Organized

* Code for an App Should Remain in the App

* Try to Keep Business Logic Out of API Views

* Version Your API. eg. /api/v1/users, /api/v2/users

* Document APIs with example

## Queuing
* Treat Tasks Like Views

* Tasks Aren’t Free

* Remember that the memory and resources to process a task have to come from somewhere. Overly resource-heavy tasks might be hidden, but they can still cause site problems.

* Only Pass JSON-Serializable Values to Task Functions

* Write Tasks as Idempotent Whenever Possible

* Don’t Keep Important Data in Your Queue

* Monitor the Backlog

* Periodically Clear Out Dead Tasks

* Use the Queue’s Error Handling