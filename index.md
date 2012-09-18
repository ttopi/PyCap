---
title: "PyCap: Python Interface to Redcap"
layout: default
---

PyCap (*Python and REDCap*) is a simple library for communicating with [REDCap][r] projects from Python applications.  It mimics the REDCap API as much as possible and is designed to be very easy to use.

## Getting started

To begin, you'll need access to a REDCap server, a project within that server, and an API key for this project, which can be obtained from your REDCap administrator.

{% highlight python %}
from redcap import Project

api_url = 'https://redcap.vanderbilt.edu/api/'
api_key = 'SomeSuperSecretAPIKeyThatNobodyElseShouldHave'
project = Project(api_url, api_key)

{% endhighlight %}

Some helpful API calls occurs during project creation:

{% highlight python %}
# Primary field, REDCap calls this the unique field
project.def_field

# A list of raw field names...
project.field_names
# ...and their labels
project.field_labels

# List of forms
project.forms

# If this is a longitudinal database...
project.events, project.arm_nums, project.arm_names

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

Or clone the repo and install:

    $ git clone git://github.com/sburns/PyCap.git
    $ cd PyCap
    $ python setup.py install


[r]: http://project-redcap.org