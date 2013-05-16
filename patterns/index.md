---
layout: default
---

# Pandas Integration

[Pandas][pd] is a high-performance data analysis library for Python. With just a little prodding, we can create a `pandas.DataFrame` from a `redcap.Project`, indexed by the unique field:

{% highlight python %}
from StringIO import StringIO
import pandas as pd
from redcap import Project

project = Project(URL, API)
data_csv = project.export_records(format='csv')
csv_buffer = StringIO(data_csv)

df = pd.read_csv(csv_buffer, index_col=project.def_field)

{% endhighlight %}

As of PyCap 0.6, you can more easily make `pandas.DataFrame` objects:

{% highlight python %}

project = Project(URL, API)
data_df = project.export_records(format='df')
metadata_df = project.export_metadata(format='df')

{% endhighlight%}

As of PyCap 0.6.1, you can pass `df_kwargs` a dictionary to alter the construction of
the DataFrame. This dict is fed into the [pandas.read_csv][csv] method.

## Exporting Data From Longitudinal Projects

If you're exporting records from longitudinal projects as a `DataFrame`, (as of `0.8.1`) `PyCap` will by default create a hierarchical index from the unique field and `redcap_event_name`. This should allow for easier slicing/dicing.

## Importing DataFrames

The `pandas.DataFrame` makes it really easy to apply functions across columns or records. If you export a `DataFrame` and compute new columns, it's a pain to convert it back to a list of `dict` to import back into the `Project`. As of `0.8.1`, you can pass a `DataFrame` to `Project.import_records`!

{% highlight python %}
from my_module import awesome_function

df = project.export_records(format='df')
df['computed_field'] = df.apply(awesome_function, axis=1)
response = project.import_records(df)

{% endhighlight %}

[pd]: http://pandas.pydata.org/
[csv]: http://pandas.pydata.org/pandas-docs/stable/generated/pandas.io.parsers.read_csv.html#pandas.io.parsers.read_csv