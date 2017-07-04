---
layout: notebook
title: ""
---
# odm2api demo with Little Bear SQLite sample DB
Largely from https://github.com/ODM2/ODM2PythonAPI/blob/master/Examples/Sample.py   
- 4/25/2016. Started testing with the new `odm2` conda channel, based on the new `0.5.0-alpha` odm2api release. See my `odm2api_odm2channel` env. Ran into problems b/c the SQLite database needed to be updated to have a `SamplingFeature.FeatureGeometryWKT` field; so I added and populated it manually with `SQLite Manager`.
- 2/7/2016. Tested successfully with `sfgeometry_em_1` branch, with my overhauls. Using `odm2api_dev` env.
- 2/1 - 1/31. Errors with SamplingFeatures code, with latest odm2api from master (on env `odm2api_jan31test`). *The code also fails the same way with the `odm2api` env, but it does still run fine with the `odm2api_jan21` env! I'm investigating the differences between those two envs.*
- 1/22-20,9/2016.

Author: Emilio Mayorga

### Create a connection to the ODM2 database

<div class="prompt input_prompt">
In&nbsp;[1]:
</div>

```python
import os
from odm2api.ODMconnection import dbconnection


odm2db_fpth = os.path.join('data', 'ODM2.sqlite')
session_factory = dbconnection.createConnection('sqlite', odm2db_fpth, 2.0)
```

### Read the database

<div class="prompt input_prompt">
In&nbsp;[2]:
</div>

```python
from odm2api.ODM2.services.readService import ReadODM2


read = ReadODM2(session_factory)
```

### Run some basic sample queries

<div class="prompt input_prompt">
In&nbsp;[3]:
</div>

```python
allVars = read.getVariables()

for x in allVars:
    print('{}: {}'.format(x.VariableCode, x.VariableNameCV))
```
<div class="output_area"><div class="prompt"></div>
<pre>
    USU36: Temperature

</pre>
</div>
### Read some metadata from the database: people and affiliation

<div class="prompt input_prompt">
In&nbsp;[4]:
</div>

```python
allPeople = read.getPeople()

if allPeople:
    for x in allPeople:
        print('{} {}'.format(x.PersonFirstName, x.PersonLastName))

allaff = read.getAffiliations()

if allaff:
    for x in allaff:
        print('{}: {}'.format(x.PersonObj.PersonFirstName, x.OrganizationID))
```
<div class="output_area"><div class="prompt"></div>
<pre>
    Jeff Horsburgh
    Jeff: 1

</pre>
</div>
## SamplingFeatures tests

### Get all of the SamplingFeatures from the database that are Sites

<div class="prompt input_prompt">
In&nbsp;[5]:
</div>

```python
try:
    siteFeatures = read.getSamplingFeatures(type='Site')
    numSites = len(siteFeatures)
    for x in siteFeatures:
        print(': '.format(x.SamplingFeatureCode, x.SamplingFeatureName))
except Exception as e:
    print('Unable to demo getSamplingFeatures(type="Site")\n{}'.format(e))
```
<div class="output_area"><div class="prompt"></div>
<pre>
    : 

</pre>
</div>
<div class="prompt input_prompt">
In&nbsp;[6]:
</div>

```python
read.getSamplingFeatures()
```




    [<Sites({'SamplingFeatureDescription': None, '_sa_instance_state': <sqlalchemy.orm.state.InstanceState object at 0x7f9746f86990>, 'SamplingFeatureGeotypeCV': u'Point', 'SamplingFeatureName': u'Little Bear River at Mendon Road near Mendon, Utah', 'ElevationDatumCV': u'NGVD29', 'Elevation_m': 1345.0, 'SamplingFeatureUUID': u'938e328c-0e38-11e6-819c-f45c8999816f', 'SamplingFeatureTypeCV': u'Site', 'SamplingFeatureCode': u'USU-LBR-Mendon', 'SamplingFeatureID': 1, 'FeatureGeometryWKT': u'POINT (-111.946402 41.718473)'})>]



<div class="prompt input_prompt">
In&nbsp;[7]:
</div>

```python
read.getSamplingFeatures(codes=['USU-LBR-Mendon'])
```




    [<Sites({'SamplingFeatureDescription': None, '_sa_instance_state': <sqlalchemy.orm.state.InstanceState object at 0x7f9746f86990>, 'SamplingFeatureGeotypeCV': u'Point', 'SamplingFeatureName': u'Little Bear River at Mendon Road near Mendon, Utah', 'ElevationDatumCV': u'NGVD29', 'Elevation_m': 1345.0, 'SamplingFeatureUUID': u'938e328c-0e38-11e6-819c-f45c8999816f', 'SamplingFeatureTypeCV': u'Site', 'SamplingFeatureCode': u'USU-LBR-Mendon', 'SamplingFeatureID': 1, 'FeatureGeometryWKT': u'POINT (-111.946402 41.718473)'})>]



### Now get the SamplingFeature object for a SamplingFeature code

<div class="prompt input_prompt">
In&nbsp;[8]:
</div>

```python
sf_lst = read.getSamplingFeatures(codes=['USU-LBR-Mendon'])

vars(sf_lst[0])
```




    {'ElevationDatumCV': u'NGVD29',
     'Elevation_m': 1345.0,
     'FeatureGeometryWKT': u'POINT (-111.946402 41.718473)',
     'SamplingFeatureCode': u'USU-LBR-Mendon',
     'SamplingFeatureDescription': None,
     'SamplingFeatureGeotypeCV': u'Point',
     'SamplingFeatureID': 1,
     'SamplingFeatureName': u'Little Bear River at Mendon Road near Mendon, Utah',
     'SamplingFeatureTypeCV': u'Site',
     'SamplingFeatureUUID': u'938e328c-0e38-11e6-819c-f45c8999816f',
     '_sa_instance_state': <sqlalchemy.orm.state.InstanceState at 0x7f9746f86990>}



<div class="prompt input_prompt">
In&nbsp;[9]:
</div>

```python
sf = sf_lst[0]

sf
```




    <Sites({'SamplingFeatureDescription': None, '_sa_instance_state': <sqlalchemy.orm.state.InstanceState object at 0x7f9746f86990>, 'SamplingFeatureGeotypeCV': u'Point', 'SamplingFeatureName': u'Little Bear River at Mendon Road near Mendon, Utah', 'ElevationDatumCV': u'NGVD29', 'Elevation_m': 1345.0, 'SamplingFeatureUUID': u'938e328c-0e38-11e6-819c-f45c8999816f', 'SamplingFeatureTypeCV': u'Site', 'SamplingFeatureCode': u'USU-LBR-Mendon', 'SamplingFeatureID': 1, 'FeatureGeometryWKT': u'POINT (-111.946402 41.718473)'})>



<div class="prompt input_prompt">
In&nbsp;[10]:
</div>

```python
print(type(sf))

print(type(sf.FeatureGeometryWKT), sf.FeatureGeometryWKT)
```
<div class="output_area"><div class="prompt"></div>
<pre>
    <class 'odm2api.ODM2.models.Sites'>
    (<type 'unicode'>, u'POINT (-111.946402 41.718473)')

</pre>
</div>
<div class="prompt input_prompt">
In&nbsp;[11]:
</div>

```python
table = """
<!DOCTYPE html>
<html>
<head>
<style>
table {{
    width:100%;
}}
table, th, td {{
    border: 1px solid black;
    border-collapse: collapse;
}}
th, td {{
    padding: 5px;
    text-align: left;
}}
table#t01 tr:nth-child(odd) {{
    background-color: #eee;
}}
table#t01 tr:nth-child(even) {{
   background-color:#fff;
}}
</style>
</head>
<body>

<table id="t01">
  <tr>
    <td>Code</td>
    <td>{}</td>
  </tr>
  <tr>
    <td>TypeCV</td>
    <td>{}</td>
  </tr>
  <tr>
    <td>Name</td>
    <td>{}</td>
  </tr>
</table>
</body>
</html>
""".format
```

<div class="prompt input_prompt">
In&nbsp;[12]:
</div>

```python
import folium

lon, lat = sf.Longitude, sf.Latitude

m = folium.Map(location=[lat, lon], zoom_start=16)

icon = folium.Icon(color='orange', icon='info-sign', prefix='glyphicon')

width, height = 310, 130
html = table(sf.SamplingFeatureCode, sf.SamplingFeatureTypeCV, sf.SamplingFeatureName)
iframe = folium.IFrame(html, width=width, height=height)
popup = folium.Popup(iframe)

folium.Marker(location=[lat, lon], icon=icon, popup=popup).add_to(m)

m
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="data:text/html;charset=utf-8;base64,PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL3VucGtnLmNvbS9sZWFmbGV0QDEuMC4xL2Rpc3QvbGVhZmxldC5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9hamF4Lmdvb2dsZWFwaXMuY29tL2FqYXgvbGlicy9qcXVlcnkvMS4xMS4xL2pxdWVyeS5taW4uanMiPjwvc2NyaXB0PgogICAgPHNjcmlwdCBzcmM9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vYm9vdHN0cmFwLzMuMi4wL2pzL2Jvb3RzdHJhcC5taW4uanMiPjwvc2NyaXB0PgogICAgPHNjcmlwdCBzcmM9Imh0dHBzOi8vY2RuanMuY2xvdWRmbGFyZS5jb20vYWpheC9saWJzL0xlYWZsZXQuYXdlc29tZS1tYXJrZXJzLzIuMC4yL2xlYWZsZXQuYXdlc29tZS1tYXJrZXJzLmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9sZWFmbGV0Lm1hcmtlcmNsdXN0ZXIvMS4wLjAvbGVhZmxldC5tYXJrZXJjbHVzdGVyLXNyYy5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvbGVhZmxldC5tYXJrZXJjbHVzdGVyLzEuMC4wL2xlYWZsZXQubWFya2VyY2x1c3Rlci5qcyI+PC9zY3JpcHQ+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vdW5wa2cuY29tL2xlYWZsZXRAMS4wLjEvZGlzdC9sZWFmbGV0LmNzcyIgLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC5taW4uY3NzIiAvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLXRoZW1lLm1pbi5jc3MiIC8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIgLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuY3NzIiAvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9sZWFmbGV0Lm1hcmtlcmNsdXN0ZXIvMS4wLjAvTWFya2VyQ2x1c3Rlci5EZWZhdWx0LmNzcyIgLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvbGVhZmxldC5tYXJrZXJjbHVzdGVyLzEuMC4wL01hcmtlckNsdXN0ZXIuY3NzIiAvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL3Jhd2dpdC5jb20vcHl0aG9uLXZpc3VhbGl6YXRpb24vZm9saXVtL21hc3Rlci9mb2xpdW0vdGVtcGxhdGVzL2xlYWZsZXQuYXdlc29tZS5yb3RhdGUuY3NzIiAvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfMzQwMjFmZDhjNWNiNGU4ZTkwY2RiZWVlNGRmYzc3NGEgewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwXzM0MDIxZmQ4YzVjYjRlOGU5MGNkYmVlZTRkZmM3NzRhIiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIHNvdXRoV2VzdCA9IEwubGF0TG5nKC05MCwgLTE4MCk7CiAgICAgICAgICAgICAgICB2YXIgbm9ydGhFYXN0ID0gTC5sYXRMbmcoOTAsIDE4MCk7CiAgICAgICAgICAgICAgICB2YXIgYm91bmRzID0gTC5sYXRMbmdCb3VuZHMoc291dGhXZXN0LCBub3J0aEVhc3QpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIHZhciBtYXBfMzQwMjFmZDhjNWNiNGU4ZTkwY2RiZWVlNGRmYzc3NGEgPSBMLm1hcCgKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICdtYXBfMzQwMjFmZDhjNWNiNGU4ZTkwY2RiZWVlNGRmYzc3NGEnLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAge2NlbnRlcjogWzQxLjcxODQ3MywtMTExLjk0NjQwMl0sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB6b29tOiAxNiwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIG1heEJvdW5kczogYm91bmRzLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgbGF5ZXJzOiBbXSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHdvcmxkQ29weUp1bXA6IGZhbHNlLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgY3JzOiBMLkNSUy5FUFNHMzg1NwogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHRpbGVfbGF5ZXJfMWNjOTc3ODgzOWJlNDc5OTlmOWRhMjgwMDUzZDE0NjIgPSBMLnRpbGVMYXllcigKICAgICAgICAgICAgICAgICdodHRwczovL3tzfS50aWxlLm9wZW5zdHJlZXRtYXAub3JnL3t6fS97eH0ve3l9LnBuZycsCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgbWF4Wm9vbTogMTgsCiAgICAgICAgICAgICAgICAgICAgbWluWm9vbTogMSwKICAgICAgICAgICAgICAgICAgICBjb250aW51b3VzV29ybGQ6IGZhbHNlLAogICAgICAgICAgICAgICAgICAgIG5vV3JhcDogZmFsc2UsCiAgICAgICAgICAgICAgICAgICAgYXR0cmlidXRpb246ICdEYXRhIGJ5IDxhIGhyZWY9Imh0dHA6Ly9vcGVuc3RyZWV0bWFwLm9yZyI+T3BlblN0cmVldE1hcDwvYT4sIHVuZGVyIDxhIGhyZWY9Imh0dHA6Ly93d3cub3BlbnN0cmVldG1hcC5vcmcvY29weXJpZ2h0Ij5PRGJMPC9hPi4nLAogICAgICAgICAgICAgICAgICAgIGRldGVjdFJldGluYTogZmFsc2UKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zNDAyMWZkOGM1Y2I0ZThlOTBjZGJlZWU0ZGZjNzc0YSk7CgogICAgICAgIAogICAgCgogICAgICAgICAgICB2YXIgbWFya2VyX2Q4MmUzNmQzNmU0OTQ1YzVhMTAzYTc4MjM5MWQ5YTczID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuNzE4NDczLC0xMTEuOTQ2NDAyXSwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBpY29uOiBuZXcgTC5JY29uLkRlZmF1bHQoKQogICAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgICkKICAgICAgICAgICAgICAgIC5hZGRUbyhtYXBfMzQwMjFmZDhjNWNiNGU4ZTkwY2RiZWVlNGRmYzc3NGEpOwogICAgICAgICAgICAKICAgIAoKICAgICAgICAgICAgICAgIHZhciBpY29uX2FmNjQ5Y2ZiMjczYzQwMTc4MDRiNWM2YThkMGViYjhmID0gTC5Bd2Vzb21lTWFya2Vycy5pY29uKHsKICAgICAgICAgICAgICAgICAgICBpY29uOiAnaW5mby1zaWduJywKICAgICAgICAgICAgICAgICAgICBpY29uQ29sb3I6ICd3aGl0ZScsCiAgICAgICAgICAgICAgICAgICAgbWFya2VyQ29sb3I6ICdvcmFuZ2UnLAogICAgICAgICAgICAgICAgICAgIHByZWZpeDogJ2dseXBoaWNvbicsCiAgICAgICAgICAgICAgICAgICAgZXh0cmFDbGFzc2VzOiAnZmEtcm90YXRlLTAnCiAgICAgICAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgICAgICBtYXJrZXJfZDgyZTM2ZDM2ZTQ5NDVjNWExMDNhNzgyMzkxZDlhNzMuc2V0SWNvbihpY29uX2FmNjQ5Y2ZiMjczYzQwMTc4MDRiNWM2YThkMGViYjhmKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2E3NTIzNWU2ODIwYTQ2OTQ5MWM4MTc2MWY1ZDY0Mjc0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBpX2ZyYW1lXzg1Y2U5NjRkN2E2NDQ4YzViZTFmYWQ1YWIzZjk1NTZhID0gJCgnPGlmcmFtZSBzcmM9ImRhdGE6dGV4dC9odG1sO2NoYXJzZXQ9dXRmLTg7YmFzZTY0LENpQWdJQ0FLUENGRVQwTlVXVkJGSUdoMGJXdytDanhvZEcxc1BnbzhhR1ZoWkQ0S1BITjBlV3hsUGdwMFlXSnNaU0I3Q2lBZ0lDQjNhV1IwYURveE1EQWxPd3A5Q25SaFlteGxMQ0IwYUN3Z2RHUWdld29nSUNBZ1ltOXlaR1Z5T2lBeGNIZ2djMjlzYVdRZ1lteGhZMnM3Q2lBZ0lDQmliM0prWlhJdFkyOXNiR0Z3YzJVNklHTnZiR3hoY0hObE93cDlDblJvTENCMFpDQjdDaUFnSUNCd1lXUmthVzVuT2lBMWNIZzdDaUFnSUNCMFpYaDBMV0ZzYVdkdU9pQnNaV1owT3dwOUNuUmhZbXhsSTNRd01TQjBjanB1ZEdndFkyaHBiR1FvYjJSa0tTQjdDaUFnSUNCaVlXTnJaM0p2ZFc1a0xXTnZiRzl5T2lBalpXVmxPd3A5Q25SaFlteGxJM1F3TVNCMGNqcHVkR2d0WTJocGJHUW9aWFpsYmlrZ2V3b2dJQ0JpWVdOclozSnZkVzVrTFdOdmJHOXlPaU5tWm1ZN0NuMEtQQzl6ZEhsc1pUNEtQQzlvWldGa1BnbzhZbTlrZVQ0S0NqeDBZV0pzWlNCcFpEMGlkREF4SWo0S0lDQThkSEkrQ2lBZ0lDQThkR1ErUTI5a1pUd3ZkR1ErQ2lBZ0lDQThkR1ErVlZOVkxVeENVaTFOWlc1a2IyNDhMM1JrUGdvZ0lEd3ZkSEkrQ2lBZ1BIUnlQZ29nSUNBZ1BIUmtQbFI1Y0dWRFZqd3ZkR1ErQ2lBZ0lDQThkR1ErVTJsMFpUd3ZkR1ErQ2lBZ1BDOTBjajRLSUNBOGRISStDaUFnSUNBOGRHUStUbUZ0WlR3dmRHUStDaUFnSUNBOGRHUStUR2wwZEd4bElFSmxZWElnVW1sMlpYSWdZWFFnVFdWdVpHOXVJRkp2WVdRZ2JtVmhjaUJOWlc1a2IyNHNJRlYwWVdnOEwzUmtQZ29nSUR3dmRISStDand2ZEdGaWJHVStDand2WW05a2VUNEtQQzlvZEcxc1BnPT0iIHdpZHRoPSIzMTAiIHN0eWxlPSJib3JkZXI6bm9uZSAhaW1wb3J0YW50OyIgaGVpZ2h0PSIxMzAiPjwvaWZyYW1lPicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYTc1MjM1ZTY4MjBhNDY5NDkxYzgxNzYxZjVkNjQyNzQuc2V0Q29udGVudChpX2ZyYW1lXzg1Y2U5NjRkN2E2NDQ4YzViZTFmYWQ1YWIzZjk1NTZhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBtYXJrZXJfZDgyZTM2ZDM2ZTQ5NDVjNWExMDNhNzgyMzkxZDlhNzMuYmluZFBvcHVwKHBvcHVwX2E3NTIzNWU2ODIwYTQ2OTQ5MWM4MTc2MWY1ZDY0Mjc0KTsKCiAgICAgICAgICAgIAogICAgICAgIAo8L3NjcmlwdD4=" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



## Back to the rest of the demo

<div class="prompt input_prompt">
In&nbsp;[13]:
</div>

```python
read.getResults()
```




    [<TimeSeriesResults({'ResultUUID': u'938fec1c-0e38-11e6-98c2-f45c8999816f', 'ValidDateTimeUTCOffset': None, '_sa_instance_state': <sqlalchemy.orm.state.InstanceState object at 0x7f9746c42d10>, 'VariableID': 1, 'ProcessingLevelID': 1, 'StatusCV': u'Unknown', 'ValueCount': 24206, 'ValidDateTime': None, 'UnitsID': 96, 'FeatureActionID': 1, 'ResultDateTimeUTCOffset': -7, 'TaxonomicClassifierID': None, 'ResultTypeCV': u'Time series coverage', 'SampledMediumCV': u'Surface Water', 'ResultDateTime': datetime.datetime(2016, 4, 29, 12, 31, 23, 801947), 'ResultID': 1})>]



<div class="prompt input_prompt">
In&nbsp;[14]:
</div>

```python
firstResult = read.getResults()[0]
firstResult.FeatureActionObj.ActionObj
```




    <Actions({'_sa_instance_state': <sqlalchemy.orm.state.InstanceState object at 0x7f9746bc9490>, 'MethodID': 1, 'ActionDescription': u'An observation action that generated a time series result.', 'ActionFileLink': None, 'EndDateTime': datetime.datetime(2009, 1, 16, 12, 30), 'BeginDateTimeUTCOffset': -7, 'EndDateTimeUTCOffset': -7, 'ActionTypeCV': u'Observation', 'ActionID': 1, 'BeginDateTime': datetime.datetime(2007, 8, 16, 16, 30)})>



### Foreign Key Example

Drill down and get objects linked by foreign keys

<div class="prompt input_prompt">
In&nbsp;[15]:
</div>

```python
try:
    # Call getResults, but return only the first result.
    firstResult = read.getResults()[0]
    action_firstResult = firstResult.FeatureActionObj.ActionObj
    print('The FeatureAction object for the Result is: {}'.format(firstResult.FeatureActionObj))
    print('The Action object for the Result is: {}'.format(action_firstResult))
    print(
        '\nThe following are some of the attributes for the Action that created the Result: \n' +
        'ActionTypeCV: ' + action_firstResult.ActionTypeCV + '\n' +
        'ActionDescription: ' + action_firstResult.ActionDescription + '\n' +
        'BeginDateTime: ' + str(action_firstResult.BeginDateTime) + '\n' +
        'EndDateTime: ' + str(action_firstResult.EndDateTime) + '\n' +
        'MethodName: ' + action_firstResult.MethodObj.MethodName + '\n' +
        'MethodDescription: ' + action_firstResult.MethodObj.MethodDescription
    )
except Exception as e:
    print('Unable to demo Foreign Key Example: {}'.format(e))
```
<div class="output_area"><div class="prompt"></div>
<pre>
    The FeatureAction object for the Result is: <FeatureActions({'FeatureActionID': 1, 'SamplingFeatureID': 1, '_sa_instance_state': <sqlalchemy.orm.state.InstanceState object at 0x7f9746bc6190>, 'ActionID': 1})>
    The Action object for the Result is: <Actions({'_sa_instance_state': <sqlalchemy.orm.state.InstanceState object at 0x7f9746bc9490>, 'MethodID': 1, 'ActionDescription': u'An observation action that generated a time series result.', 'ActionFileLink': None, 'EndDateTime': datetime.datetime(2009, 1, 16, 12, 30), 'BeginDateTimeUTCOffset': -7, 'EndDateTimeUTCOffset': -7, 'ActionTypeCV': u'Observation', 'ActionID': 1, 'BeginDateTime': datetime.datetime(2007, 8, 16, 16, 30)})>
    
    The following are some of the attributes for the Action that created the Result: 
    ActionTypeCV: Observation
    ActionDescription: An observation action that generated a time series result.
    BeginDateTime: 2007-08-16 16:30:00
    EndDateTime: 2009-01-16 12:30:00
    MethodName: Quality Control Level 1 Data Series created from raw QC Level 0 data using ODM Tools.
    MethodDescription: Quality Control Level 1 Data Series created from raw QC Level 0 data using ODM Tools.

</pre>
</div>
### Example of Retrieving Attributes of a Time Series Result using a ResultID

<div class="prompt input_prompt">
In&nbsp;[16]:
</div>

```python
tsResult = read.getResults(ids=[1])[0]

type(tsResult), vars(tsResult)
```




    (odm2api.ODM2.models.TimeSeriesResults,
     {'FeatureActionID': 1,
      'FeatureActionObj': <FeatureActions({'FeatureActionID': 1, 'SamplingFeatureID': 1, '_sa_instance_state': <sqlalchemy.orm.state.InstanceState object at 0x7f9746bc6190>, 'ActionID': 1})>,
      'ProcessingLevelID': 1,
      'ResultDateTime': datetime.datetime(2016, 4, 29, 12, 31, 23, 801947),
      'ResultDateTimeUTCOffset': -7,
      'ResultID': 1,
      'ResultTypeCV': u'Time series coverage',
      'ResultUUID': u'938fec1c-0e38-11e6-98c2-f45c8999816f',
      'SampledMediumCV': u'Surface Water',
      'StatusCV': u'Unknown',
      'TaxonomicClassifierID': None,
      'UnitsID': 96,
      'ValidDateTime': None,
      'ValidDateTimeUTCOffset': None,
      'ValueCount': 24206,
      'VariableID': 1,
      '_sa_instance_state': <sqlalchemy.orm.state.InstanceState at 0x7f9746c42d10>})



**Why are `ProcessingLevelObj`, `VariableObj` and `UnitsObj` objects not shown in the above `vars()` listing!?** They **are** actually available, as demonstrated in much of the code below.

<div class="prompt input_prompt">
In&nbsp;[17]:
</div>

```python
try:
    tsResult = read.getResults(ids=[1])[0]
    # Get the site information by drilling down.
    sf_tsResult = tsResult.FeatureActionObj.SamplingFeatureObj
    print('Some of the attributes for the TimeSeriesResult retrieved using getResults(ids=[]): \n' +
          'ResultTypeCV: ' + tsResult.ResultTypeCV + '\n' +
          # Get the ProcessingLevel from the TimeSeriesResult's ProcessingLevel object.
          'ProcessingLevel: ' + tsResult.ProcessingLevelObj.Definition + '\n' +
          'SampledMedium: ' + tsResult.SampledMediumCV + '\n' +
          # Get the variable information from the TimeSeriesResult's Variable object.
          'Variable: ' + tsResult.VariableObj.VariableCode + ': ' + tsResult.VariableObj.VariableNameCV + '\n' +
          'AggregationStatistic: ' + tsResult.AggregationStatisticCV + '\n' +
          # Get the site information by drilling down.
          'Elevation_m: ' + str(sf_tsResult.Elevation_m) + '\n' +
          'SamplingFeature: ' + sf_tsResult.SamplingFeatureCode + ' - ' +
          sf_tsResult.SamplingFeatureName)
except Exception as e:
    print('Unable to demo Example of retrieving Attributes of a time Series Result: {}'.format(e))
```
<div class="output_area"><div class="prompt"></div>
<pre>
    Some of the attributes for the TimeSeriesResult retrieved using getResults(ids=[]): 
    ResultTypeCV: Time series coverage
    ProcessingLevel: Quality controlled data
    SampledMedium: Surface Water
    Variable: USU36: Temperature
    AggregationStatistic: Average
    Elevation_m: 1345.0
    SamplingFeature: USU-LBR-Mendon - Little Bear River at Mendon Road near Mendon, Utah

</pre>
</div>
### Example of Retrieving Time Series Result Values, then plotting them

<div class="prompt input_prompt">
In&nbsp;[18]:
</div>

```python
tsValues = read.getResultValues(resultids=[1])  # Get the values for a particular TimeSeriesResult.

tsValues.set_index('valuedatetime', inplace=True)

tsValues.head()  # Return type is a pandas dataframe.
```




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
      <th>valueid</th>
      <th>resultid</th>
      <th>datavalue</th>
      <th>valuedatetimeutcoffset</th>
      <th>censorcodecv</th>
      <th>qualitycodecv</th>
      <th>timeaggregationinterval</th>
      <th>timeaggregationintervalunitsid</th>
    </tr>
    <tr>
      <th>valuedatetime</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2009-01-04 18:00:00</th>
      <td>23641</td>
      <td>1</td>
      <td>-0.268333</td>
      <td>-7</td>
      <td>nc</td>
      <td>Unknown</td>
      <td>30.0</td>
      <td>102</td>
    </tr>
    <tr>
      <th>2009-01-04 06:00:00</th>
      <td>23617</td>
      <td>1</td>
      <td>-0.266667</td>
      <td>-7</td>
      <td>nc</td>
      <td>Unknown</td>
      <td>30.0</td>
      <td>102</td>
    </tr>
    <tr>
      <th>2008-12-18 01:00:00</th>
      <td>22922</td>
      <td>1</td>
      <td>-0.263333</td>
      <td>-7</td>
      <td>nc</td>
      <td>Unknown</td>
      <td>30.0</td>
      <td>102</td>
    </tr>
    <tr>
      <th>2009-01-04 05:30:00</th>
      <td>23616</td>
      <td>1</td>
      <td>-0.263333</td>
      <td>-7</td>
      <td>nc</td>
      <td>Unknown</td>
      <td>30.0</td>
      <td>102</td>
    </tr>
    <tr>
      <th>2009-01-04 06:30:00</th>
      <td>23618</td>
      <td>1</td>
      <td>-0.263333</td>
      <td>-7</td>
      <td>nc</td>
      <td>Unknown</td>
      <td>30.0</td>
      <td>102</td>
    </tr>
  </tbody>
</table>
</div>



<div class="prompt input_prompt">
In&nbsp;[19]:
</div>

```python
%matplotlib inline
import matplotlib.pyplot as plt
from matplotlib import dates

fig, ax = plt.subplots(figsize=(11, 2.25))
tsValues['datavalue'].plot(ax=ax)

ax.set_ylabel('{} ({})'.format(
    tsResult.VariableObj.VariableNameCV,
    tsResult.UnitsObj.UnitsAbbreviation))
ax.set_xlabel('')
ax.xaxis.set_minor_locator(dates.MonthLocator())
ax.xaxis.set_minor_formatter(dates.DateFormatter('%b'))
ax.xaxis.set_major_locator(dates.YearLocator())
ax.xaxis.set_major_formatter(dates.DateFormatter('\n%Y'))
ax.grid(which='major', axis='y')
ax.grid(which='minor', axis='x')
```


![png](data:image/png;base64,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
6acyAAAAAElFTkSuQmCC
)

<br>
Right click and choose Save link as... to
[download](https://raw.githubusercontent.com/BiG-CZ/notebook_data_demo/master/notebooks/2017-06-24-odm2api_sample_fromsqlite.ipynb)
this notebook, or click [here](https://beta.mybinder.org/v2/gh/BiG-CZ/notebook_data_demo/master?filepath=notebooks/2017-06-24-odm2api_sample_fromsqlite.ipynb) to run a live instance of this notebook.