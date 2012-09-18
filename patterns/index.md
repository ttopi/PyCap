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




[pd]: http://pandas.pydata.org/