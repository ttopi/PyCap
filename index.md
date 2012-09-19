---
title: "PyCap: Python Interface to Redcap"
layout: default
---

PyCap (*Python and REDCap*) is a simple library for communicating with [REDCap][r] projects from Python applications.  It mimics the REDCap API as much as possible and is designed to be very easy to use.

## Getting started

To begin, you'll need access to a REDCap server, a project within that server, and an API key for said project which can be obtained from your REDCap administrator.

{% highlight python %}
from redcap import Project
api_url = 'https://redcap.example.edu/api/'
api_key = 'SomeSuperSecretAPIKeyThatNobodyElseShouldHave'
project = Project(api_url, api_key)
{% endhighlight %}

Some helpful API calls occur during project creation:

{% highlight python %}
# Primary field, REDCap calls this the unique field
print project.def_field
id
# A list of raw field names...
print project.field_names
('id', 'age_at_testing', 'dob', 'test1_ss', ...)
# ...and their labels
print project.field_labels
('Identifier', 'Age at Day of Testing', 'Date of Birth', 'Test 1 Standard Score',...)

# List of forms
print project.forms
('demographics', 'test_1', ...)

# If this is a longitudinal database...
print project.events, project.arm_nums, project.arm_names
('event1', 'event2',...), ('arm1', 'arm2',...), ('Visit1', 'Visit2',...)
{% endhighlight %}

The two most useful functions of the `project` object are `.export_records` and `.import_records`:

{% highlight python %}
from my_module import modify

data = project.export_records()
# data is a list of dicts
new_data = modify(data)
project.import_records(new_data)

{% endhighlight%}

For more complete examples, please see the [docs][d].

[d]: {{site.baseurl}}docs/

## Installation

Install with `pip` (or `easy_install`):

    $ pip install PyCap

Or download the package from github (see buttons below) and:

    $ cd < downloads >
    $ < unzip/untar >
    $ python setup.py install

Or to use the most up-to-date codebase, which hopefully isn't broken:

    $ git clone git://github.com/sburns/PyCap.git
    $ cd PyCap
    $ python setup.py install

    OR

    $ pip install -e git+git@github.com:sburns/PyCap.git@master#eqq=PyCap

[r]: http://project-redcap.org