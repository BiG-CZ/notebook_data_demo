---
layout: notebook
title: ""
---
# Testing WOFpy LBR sample DB
Emilio Mayorga. Run on my conda environment `uwapl_em_mc_1aui`.  
3/5,4/2017. Test Don's Amazon cloud deployment

<div class="prompt input_prompt">
In&nbsp;[1]:
</div>

```python
%matplotlib inline

import pytz
import matplotlib.pyplot as plt
import pandas as pd

import ulmo
from ulmo.util import convert_datetime
```
<div class="warning" style="border:thin solid red">
    /home/filipe/miniconda3/envs/BiG-CZ/lib/python2.7/site-
packages/ulmo/twc/kbdi/core.py:20: FutureWarning: pandas.tslib is deprecated and
will be removed in a future version.
    You can access Timestamp as pandas.Timestamp
      CSV_SWITCHOVER = pandas.tslib.Timestamp('2016-10-01')

</div>
## CUAHSI WaterOneFlow: ulmo, SOAP endpoint, and other general info

<div class="prompt input_prompt">
In&nbsp;[2]:
</div>

```python
print(ulmo.cuahsi.wof.__doc__)
```
<div class="output_area"><div class="prompt"></div>
<pre>
    
        ulmo.cuahsi.wof
        ~~~~~~~~~~~~~~~
    
        `CUAHSI WaterOneFlow`_ web services
    
        .. _CUAHSI WaterOneFlow: http://his.cuahsi.org/wofws.html
    

</pre>
</div>
<div class="prompt input_prompt">
In&nbsp;[3]:
</div>

```python
print([obj for obj in dir(ulmo.cuahsi.wof) if not obj.startswith('__')])
```
<div class="output_area"><div class="prompt"></div>
<pre>
    ['absolute_import', 'core', 'get_site_info', 'get_sites', 'get_values', 'get_variable_info']

</pre>
</div>
<div class="prompt input_prompt">
In&nbsp;[4]:
</div>

```python
# WaterML/WOF WSDL endpoints
wsdlurl = 'http://54.186.36.247:8080/mysqlodm2timeseries/soap/cuahsi_1_0/.wsdl'  # WOF 1.0

# 'network code'
networkcd = 'mysqlodm2timeseries'
```

## Get site information

one of two sites in the LBR sample DB

<div class="prompt input_prompt">
In&nbsp;[5]:
</div>

```python
sitecd = 'USU-LBR-Mendon'
```

<div class="prompt input_prompt">
In&nbsp;[6]:
</div>

```python
siteinfo = ulmo.cuahsi.wof.get_site_info(wsdlurl, networkcd+':'+sitecd)
```

<div class="prompt input_prompt">
In&nbsp;[7]:
</div>

```python
type(siteinfo), siteinfo.keys()
```




    (dict,
     ['code', 'name', 'series', 'notes', 'network', 'location', 'timezone_info'])



<div class="prompt input_prompt">
In&nbsp;[8]:
</div>

```python
siteinfo['network'], siteinfo['code'], siteinfo['name']
```




    ('mysqlodm2timeseries',
     'USU-LBR-Mendon',
     'Little Bear River at Mendon Road near Mendon, Utah')



<div class="prompt input_prompt">
In&nbsp;[9]:
</div>

```python
print(siteinfo['location'])
```
<div class="output_area"><div class="prompt"></div>
<pre>
    {'latitude': '41.718473', 'srs': 'EPSG:CUAHSI:4269', 'longitude': '-111.946402'}

</pre>
</div>
<div class="prompt input_prompt">
In&nbsp;[10]:
</div>

```python
type(siteinfo['series']), len(siteinfo['series']), siteinfo['series'].keys()
```




    (dict,
     18,
     ['mysqlodm2timeseries:USU7',
      'mysqlodm2timeseries:USU6',
      'mysqlodm2timeseries:USU5',
      'mysqlodm2timeseries:USU4',
      'mysqlodm2timeseries:USU3',
      'mysqlodm2timeseries:USU48',
      'mysqlodm2timeseries:USU47',
      'mysqlodm2timeseries:USU9',
      'mysqlodm2timeseries:USU8',
      'mysqlodm2timeseries:USU13',
      'mysqlodm2timeseries:USU10',
      'mysqlodm2timeseries:USU35',
      'mysqlodm2timeseries:USU34',
      'mysqlodm2timeseries:USU37',
      'mysqlodm2timeseries:USU36',
      'mysqlodm2timeseries:USU44',
      'mysqlodm2timeseries:USU33',
      'mysqlodm2timeseries:USU32'])



<div class="prompt input_prompt">
In&nbsp;[11]:
</div>

```python
siteinfo['series']['mysqlodm2timeseries:USU33'].keys()
```




    ['{http://www.cuahsi.org/water_ml/1.0/}variable_time_interval',
     '{http://www.cuahsi.org/water_ml/1.0/}_source',
     '{http://www.cuahsi.org/water_ml/1.0/}_quality_control_level',
     'variable',
     '{http://www.cuahsi.org/water_ml/1.0/}_method',
     '{http://www.cuahsi.org/water_ml/1.0/}value_count']



<div class="prompt input_prompt">
In&nbsp;[12]:
</div>

```python
siteinfo['series']['mysqlodm2timeseries:USU33']
```




    {'variable': {'code': 'USU33',
      'data_type': 'Average',
      'general_category': 'Water Quality',
      'id': '33',
      'name': 'Oxygen, dissolved percent of saturation',
      'no_data_value': '-9999.0000000000',
      'sample_medium': 'Unknown',
      'time': {},
      'units': {'abbreviation': '%',
       'code': '1',
       'name': 'percent',
       'type': 'Dimensionless'},
      'value_type': 'Unknown',
      'vocabulary': 'mysqlodm2timeseries'},
     '{http://www.cuahsi.org/water_ml/1.0/}_method': {'method_description': 'Dissolved oxygen measured using a Hydrolab MS5 Water Quality Multiprobe.',
      'method_id': '19',
      'method_link': 'http://www.hydrolab.com'},
     '{http://www.cuahsi.org/water_ml/1.0/}_quality_control_level': {'quality_control_level': '0',
      'quality_control_level_id': '0'},
     '{http://www.cuahsi.org/water_ml/1.0/}_source': {'organization': 'Utah State University Utah Water Research Laboratory',
      'source_description': 'Continuous water quality monitoring by Utah State University as part of the USDA CEAP Grant',
      'source_id': '1',
      'source_link': 'http://www.bearriverinfo.org'},
     '{http://www.cuahsi.org/water_ml/1.0/}value_count': {'value_count': '1440'},
     '{http://www.cuahsi.org/water_ml/1.0/}variable_time_interval': {'begin_date_time': '2007-09-01T00:00:00',
      'end_date_time': '2007-09-30T23:30:00',
      'variable_time_interval_type': 'TimeIntervalType'}}



## Get Values

<div class="prompt input_prompt">
In&nbsp;[13]:
</div>

```python
def site_series_values_to_df(series_values, variable_name):
    # Create a clean timeseries list of (dt, val) tuples
    tsdt_tuplst = [
        (convert_datetime(valdict['datetime']).replace(tzinfo=pytz.utc),
         float(valdict['value'])) for valdict in series_values['values']
    ]

    dt, val = zip(*tsdt_tuplst)
    ts_df = pd.DataFrame({'time': dt, variable_name: val})
    ts_df.set_index('time', inplace=True)
    ts_df.sort_index(ascending=True, inplace=True)
    return ts_df
```

<div class="prompt input_prompt">
In&nbsp;[14]:
</div>

```python
print(
    ulmo.cuahsi.wof.get_values.__doc__.replace('<', '').replace('>', '')
)
```
<div class="output_area"><div class="prompt"></div>
<pre>
    
        Retrieves site values from a WaterOneFlow service using a GetValues request.
    
        Parameters
        ----------
        wsdl_url : str
            URL of a service's web service definition language (WSDL) description.
            All WaterOneFlow services publish a WSDL description and this url is the
            entry point to the service.
        site_code : str
            Site code of the site you'd like to get values for. Site codes MUST
            contain the network and be of the form network:site_code, as is
            required by WaterOneFlow.
        variable_code : str
            Variable code of the variable you'd like to get values for. Variable
            codes MUST contain the network and be of the form
            vocabulary:variable_code, as is required by WaterOneFlow.
        start : ``None`` or datetime (see :ref:`dates-and-times`)
            Start of a date range for a query. If both start and end parameters are
            omitted, the entire time series available will be returned.
        end : ``None`` or datetime (see :ref:`dates-and-times`)
            End of a date range for a query. If both start and end parameters are
            omitted, the entire time series available will be returned.
        suds_cache: ``None`` or tuple
            SOAP local cache duration for WSDL description and client object.
            Pass a cache duration tuple like ('days', 3) to set a custom duration.
            Duration may be in months, weeks, days, hours, or seconds.
            If unspecified, the default duration (1 day) will be used.
            Use ``None`` to turn off caching.
    
        Returns
        -------
        site_values : dict
            a python dict containing values
        

</pre>
</div>
`'odm2timeseries:USU33'` is 'Oxygen, dissolved percent of saturation'

<div class="prompt input_prompt">
In&nbsp;[15]:
</div>

```python
variablecd = 'USU33'

site_values = ulmo.cuahsi.wof.get_values(wsdlurl, networkcd+':'+sitecd, networkcd+':'+variablecd)
```

<div class="prompt input_prompt">
In&nbsp;[16]:
</div>

```python
site_values.keys()
```




    ['sources', 'quality_control_levels', 'values', 'methods', 'variable', 'site']



<div class="prompt input_prompt">
In&nbsp;[17]:
</div>

```python
sitevariable = site_values['variable']
sitevariable
```




    {'code': 'USU33',
     'data_type': 'Average',
     'general_category': 'Water Quality',
     'id': '33',
     'name': 'Oxygen, dissolved percent of saturation',
     'no_data_value': '-9999.0000000000',
     'sample_medium': 'Unknown',
     'time': {'interval': '30',
      'units': {'abbreviation': 'min', 'name': 'minute', 'type': 'Time'}},
     'units': {'abbreviation': '%',
      'code': '1',
      'name': 'percent',
      'type': 'Dimensionless'},
     'value_type': 'Unknown',
     'vocabulary': 'mysqlodm2timeseries'}



`site_values['values']` is a list of individual time series values (timestamp and data value)

<div class="prompt input_prompt">
In&nbsp;[18]:
</div>

```python
type(site_values['values']), site_values['values'][0].keys()
```




    (list,
     ['method_id',
      'censor_code',
      'quality_control_level',
      'source_id',
      'value',
      'datetime'])



Start and end timestamps (local time with time offset vs utc; iso8601 format)

<div class="prompt input_prompt">
In&nbsp;[19]:
</div>

```python
site_values['values'][0]['datetime'], site_values['values'][-1]['datetime']
```




    ('2007-09-01T00:00:00', '2007-09-30T23:30:00')



Set a nice, user-friendly variable name string.

<div class="prompt input_prompt">
In&nbsp;[20]:
</div>

```python
variable_name = '%s (%s)' % (sitevariable['name'], sitevariable['value_type'])
variable_name
```




    'Oxygen, dissolved percent of saturation (Unknown)'



<div class="prompt input_prompt">
In&nbsp;[21]:
</div>

```python
dtstr_last = site_values['values'][-1]['datetime']
convert_datetime(dtstr_last).replace(tzinfo=pytz.utc)
```
<div class="warning" style="border:thin solid red">
    /home/filipe/miniconda3/envs/BiG-CZ/lib/python2.7/site-
packages/ipykernel/__main__.py:2: FutureWarning: to_datetime is deprecated. Use
self.to_pydatetime()
      from ipykernel import kernelapp as app

</div>



    datetime.datetime(2007, 9, 30, 23, 30, tzinfo=<UTC>)



Hmm, this failed:
```python
convert_datetime(dtstr_last).astimezone(pytz.utc)
ValueError: astimezone() cannot be applied to a naive datetime
```

<div class="prompt input_prompt">
In&nbsp;[22]:
</div>

```python
ts_df = site_series_values_to_df(site_values, variable_name)
ts_df.tail()
```
<div class="warning" style="border:thin solid red">
    /home/filipe/miniconda3/envs/BiG-CZ/lib/python2.7/site-
packages/ipykernel/__main__.py:5: FutureWarning: to_datetime is deprecated. Use
self.to_pydatetime()

</div>



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Oxygen, dissolved percent of saturation (Unknown)</th>
    </tr>
    <tr>
      <th>time</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2007-09-30 21:30:00+00:00</th>
      <td>94.99999</td>
    </tr>
    <tr>
      <th>2007-09-30 22:00:00+00:00</th>
      <td>94.18334</td>
    </tr>
    <tr>
      <th>2007-09-30 22:30:00+00:00</th>
      <td>93.28333</td>
    </tr>
    <tr>
      <th>2007-09-30 23:00:00+00:00</th>
      <td>92.41666</td>
    </tr>
    <tr>
      <th>2007-09-30 23:30:00+00:00</th>
      <td>91.58334</td>
    </tr>
  </tbody>
</table>
</div>



<div class="prompt input_prompt">
In&nbsp;[23]:
</div>

```python
type(ts_df), ts_df.columns, ts_df.index.dtype, ts_df.index.min(), ts_df.index.max()
```




    (pandas.core.frame.DataFrame,
     Index([u'Oxygen, dissolved percent of saturation (Unknown)'], dtype='object'),
     datetime64[ns, UTC],
     Timestamp('2007-09-01 00:00:00+0000', tz='UTC'),
     Timestamp('2007-09-30 23:30:00+0000', tz='UTC'))



<div class="prompt input_prompt">
In&nbsp;[24]:
</div>

```python
fig, ax = plt.subplots(figsize=(10, 4))
varlabel = ts_df.columns[0]
ts_df[varlabel].plot(style='-', ax=ax)
ax.set_ylabel(varlabel + ', ' + sitevariable['units']['abbreviation']);
```


![png](data:image/png;base64,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
AABJRU5ErkJggg==
)

<br>
Right click and choose Save link as... to
[download](https://raw.githubusercontent.com/BiG-CZ/notebook_data_demo/master/notebooks/2017-07-04-WOFpy_ulmo.ipynb)
this notebook, or click [here](https://beta.mybinder.org/v2/gh/BiG-CZ/notebook_data_demo/master?filepath=notebooks/2017-07-04-WOFpy_ulmo.ipynb) to run a live instance of this notebook.