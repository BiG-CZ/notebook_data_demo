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
wsdlurl = 'http://52.40.105.124:8080/odm2timeseries/soap/cuahsi_1_0/.wsdl'  # WOF 1.0

# 'network code'
networkcd = 'odm2timeseries'
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




    ('odm2timeseries',
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
     ['odm2timeseries:USU44',
      'odm2timeseries:USU8',
      'odm2timeseries:USU9',
      'odm2timeseries:USU6',
      'odm2timeseries:USU7',
      'odm2timeseries:USU4',
      'odm2timeseries:USU5',
      'odm2timeseries:USU3',
      'odm2timeseries:USU10',
      'odm2timeseries:USU47',
      'odm2timeseries:USU13',
      'odm2timeseries:USU48',
      'odm2timeseries:USU36',
      'odm2timeseries:USU37',
      'odm2timeseries:USU34',
      'odm2timeseries:USU35',
      'odm2timeseries:USU32',
      'odm2timeseries:USU33'])



<div class="prompt input_prompt">
In&nbsp;[11]:
</div>

```python
siteinfo['series']['odm2timeseries:USU33'].keys()
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
siteinfo['series']['odm2timeseries:USU33']
```




    {'variable': {'code': 'USU33',
      'data_type': 'Unknown',
      'general_category': 'Unknown',
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
      'vocabulary': 'odm2timeseries'},
     '{http://www.cuahsi.org/water_ml/1.0/}_method': {'method_description': 'Dissolved oxygen measured using a Hydrolab MS5 Water Quality Multiprobe.',
      'method_id': '19',
      'method_link': 'http://www.hydrolab.com'},
     '{http://www.cuahsi.org/water_ml/1.0/}_quality_control_level': {'quality_control_level': '0',
      'quality_control_level_id': '0'},
     '{http://www.cuahsi.org/water_ml/1.0/}_source': {},
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




    ['variable', 'quality_control_levels', 'values', 'site', 'methods']



<div class="prompt input_prompt">
In&nbsp;[17]:
</div>

```python
sitevariable = site_values['variable']
sitevariable
```




    {'code': 'USU33',
     'data_type': 'Unknown',
     'general_category': 'Unknown',
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
     'vocabulary': 'odm2timeseries'}



`site_values['values']` is a list of individual time series values (timestamp and data value)

<div class="prompt input_prompt">
In&nbsp;[18]:
</div>

```python
type(site_values['values']), site_values['values'][0].keys()
```




    (list,
     ['method_id', 'value', 'datetime', 'censor_code', 'quality_control_level'])



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
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
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
l = ax.set_ylabel(varlabel + ', ' + sitevariable['units']['abbreviation'])
```


![png](data:image/png;base64,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
)

<br>
Right click and choose Save link as... to
[download](https://raw.githubusercontent.com/BiG-CZ/notebook_data_demo/master/notebooks/2017-07-04-WOFpy_ulmo.ipynb)
this notebook, or click [here](https://beta.mybinder.org/v2/gh/BiG-CZ/notebook_data_demo/master?filepath=notebooks/2017-07-04-WOFpy_ulmo.ipynb) to run a live instance of this notebook.