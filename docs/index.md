---
layout: default
---

# Connecting to Projects

The main class of PyCap is `redcap.Project`. It must be instantiated with the API URL of your REDCap site and a API token.

{% highlight python %}
from redcap import Project
URL = 'https://redcap.example.com/api/'
API_KEY = 'ExampleKey'

project = Project(URL, API_KEY)

{% endhighlight %}

Note that you will have one API key per redcap project to which you have access. To communicate between projects, you would create multiple `redcap.Project` instances.

API Keys are effectively your username and password for a particular project.
If you have read/write access to a project (which is most likely),
anyone with your redcap URL (public knowledge) and your API key has read/write access.

Since REDCap projects are often used to store Personal Health Information (PHI), it is of the **utmost importance** to:

1. **Never share your API key**
2. **Delete the key (through the web interface) after you're done using it **

## Project Attributes

When creating a `Project` object, PyCap goes ahead and makes some useful API calls and creates these attributes:

- `def_field`: What REDCap refers to as the _unique key_
- `forms`: A tuple of form names within the project
- `field_names`: A tuple of the _raw_ fields
- `field_labels`: A tuple of the field's labels
- `events`: Unique event names (longitudinal projects)
- `arm_nums`: Unique arm numbers (longitudinal projects)
- `arm_names`: Unique arm names (longitudinal projects)

For non-longitudinal projects, `events`, `arm_nums`, and `arm_names` are empty tuples.

## Metadata

Every `Project` object has an attribute named `metadata`. This is a list of `dicts` with the following keys (in no particular order):

- `field_name`: The raw field name
- `field_type`: The field type (text, radio, mult choice, etc.)
- `field_note`: Any notes for this field
- `required_field`: Whether the field is required
- `custom_alignment`: (Web-only) Determines how the field looks visually
- `matrix_group_name`: The matrix name this field belongs to
- `field_label`: The field label
- `section_header`: Under which section in the form page this field belongs
- `text_validation_min`: Minimum value for validation
- `branching_logic`: Any branching logic that REDCap uses to show or hide fields
- `select_choices_or_calculations`: For radio fields, the choices
- `question_number`:
- `form_name`: Form under which this field exists
- `text_validation_type_or_show_slider_number`: Validation type
- `identifier`:
- `text_validation_max`: Maximum value for validation

You can export the metadata on your own using the `.export_metadata` method on
`Project` objects.

# Exporting Data

Exporting data is very easy with PyCap.

{% highlight python %}
# we have a previously-made project
data = project.export_records()
{% endhighlight%}

`data` is a list of `dicts` with the raw field names as keys.

We can request slices to reduce the size of the transmitted data, which can be useful for large projects.

{% highlight python %}

# Known record identifiers
ids_of_interest = ['1', '2', '3']
subset = project.export_records(records=ids_of_interest)
# Contains all fields, but only three records

# Known fields of interest
fields_of_interest = ['age', 'test_score1', 'test_score2']
subset = project.export_records(fields=fields_of_interest)
# All records, but only three columns

# Only want the first two forms
forms = project.forms[:2]
subset = project.export_records(forms=forms)
# All records, all fields within the first two forms
{% endhighlight %}

Note, no matter which fields or forms are requested, the `project.def_field` key will always be in the returned `dicts`.

Finally, you can tweak the how the data is indexed or formatted.

{% highlight python %}

# Same data, but keys will the field labels
data = project.export_records(raw_or_label='label')

# You can also get the data in different formats
csv_data = project.export_records(format='csv') # or format='xml'

# As of PyCap 0.6, you can create a pandas.DataFrame
data_frame = project.export_records(format='df')
# As of 0.6.1, df_kwargs is passed to the pandas.read_csv DataFrame constructor
other_df = project.export_records(format='df', df_kwargs={'index_col': project.field_names[1]})
{% endhighlight %}

Previously, PyCap enforced a strict intersection between the passed `fields` and `project.field_names` but that requirement was dropped in PyCap v0.5.

{% highlight python %}
non_fields = ['foo', 'bar', 'bat']
response = project.export_records(fields=non_fields)
# response will contain dicts with only the def_field
{% endhighlight %}

# Importing Data

PyCap aims to make importing as easy as exporting.

{% highlight python %}

# toy
def increment_score(record):
    record['score'] += 5

data = project.export_records(fields=['score'])
map(increment_score, data)
response = project.import_records(data)
# response['count'] is the number of records successfully updated
{% endhighlight %}

Imported data must be a list of `dicts`. While data can be exported in csv and xml formats, PyCap can only import an in-memory data structure.
