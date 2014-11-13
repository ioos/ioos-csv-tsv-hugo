+++
draft = false
title = "IOOS CSV / TSV Encoding"
subtitle = "IOOS Convention for observation data encoding in CSV/TSV"
type = "post"
date = 2014-08-04T08:10:23Z
weight = "1"
+++

_This document describes the conventions used by the Integrated Ocean Observing System (IOOS) program to encode observation data as plain text Comma-Separated Values (CSV) or Tab-Separated Values (TSV)._
<!--more-->

_CSV means that the data are expressed as a sequence of multiple data values or metadata attributes separated by commas on a single line of text. Each line of text represents a single point in time and space. Multiple lines are used for additional times or locations. CSV data can be thought of as an array of rows (one per line) and columns (one per value in each line)._

_TSV means that the data are expressed as a sequence of multiple data values or metadata attributes separated by tabs (ASCII character 0x09) on a single line of text. Each line of text represents a single point in time and space. Multiple lines are used for additional times or locations. TSV data can be thought of as an array of rows (one per line) and columns (one per value in each line)._

_This document applies to the following **Data Value** types: **Scalar**, **Vector**, **Multivalue**, and **Spectral**. This document applies to the following **Sampling Feature** types: **Point**, **Vertical Profile**, **Horizontal Profile**, **2D Trajectory**, **3D Trajectory**, and **Collection**._

>_**NOTES:**_  
>  _1. This document does not address conventions for reporting multiple phenomena from different sensors from a single station in a single response. This remains an area to be discussed._ 
>  _2. This document does not discuss other sampling feature types including regular grids, irregular grids, unstructured grids, or volumetric data. IOOS does not encode such data as CSV or TSV — instead, the binary NetCDF format with CF conventions is used._

_The described conventions are intended to be applied by the IOOS Sensor Observation Service (SOS) instances, but could be used to transmit and store CSV- or TSV-encoded data from other sources as well._ 
<br />

# Revision History #
| Version | Description | Date |
|------------|-----------------|---------|
|1.0.0 |Original versions of separate CSV and TSV encoding specifications.	|2010-06-09 |
|1.0.0b |Both CSV and TSV specifications: minor editing; no change to specifications.	|2010-07-20 |
|1.0.0c |Both CSV and TSV specifications: update sample URLs to use production server instead of test server.	|2010-12-20 |
|1.0.1 |CSV specification: update Section 2.2 to use the MIME type 'text/csv' instead of 'text/csv;header=true' to reflect common practice and the examples in the document.	|2011-01-14 |
|1.1.0 |First version of the combined CSV and TSV encoding specification; minor editing to align the documents and encoding examples in the document.	|2012-01-16 | 
<br>

# General Encoding Conventions #

## Basic Structure and Conformance with RFC 4180 ##

IOOS CSV responses shall use the basic structure defined in IETF RFC 4180, "Common Format and MIME Type for Comma-Separated Values (CSV) Files" (Shafranovich, 2005), including:
 -  a line break (CR/LF) between each line;
 -  a comma between each value;
 -  two commas in a row for a missing value;
 -  no comma after the last value, unless the last value is missing in which case the preceding comma is retained;
 -  space characters within each field are significant; do not add a space after each comma;
 -  double quotes around values that contain commas, spaces, line breaks or double quotes (escaped as ""). Optionally, double quotes may be added around other values.
 
IOOS TSV responses shall use the basic structure similar to the CSV responses:
 -  a line break (CR/LF) between each line;
 -  a TAB between each value;
 -  no TABs permitted in any value;
 -  two TABs in a row to designate a missing value;
 -  no TAB after the last value, unless the last value is missing in which case the preceding TAB is retained; 
 -  all characters within a value are significant (including spaces, commas, and quotes).

<br>

## MIME Types ##

The MIME type of the response shall be indicated by the originating server using the HTTP Content-Type header.

>**NOTE:**
>The NDBC SOS server is presently using the HTTP Content-Disposition header to specify a filename and open a dialog box. IOOS is considering conventions to make the resulting filename more reflective of the phenomenon, procedure and time of the data inside.

The MIME type of an IOOS CSV response shall be as defined in RFC 4180 (the optional parameter _**header=present**_ suggested by RFC 4180 is not used. IOOS CSV responses shall contain column headers as described in [Header row and initial columns](#Header_row_and_initial_columns) below:
```
     text/csv
```

IOOS TSV files shall use the MIME type registered with the Internet Assigned Numbers Authority [IANA-2010, "Tab-Separated Values"](http://www.iana.org/assignments/media-types/text/tab-separated-values):
```
     text/tab-separated-values
```

<a name="Compression"></a> 
## Compression ##

IOOS servers shall offer uncompressed CSV or TSV. Though not required, servers may offer compressed CSV or TSV as well (using gzip, ZIP, etc.). If the CSV or TSV result is compressed the originating server shall indicate this fact using the HTTP Content-Encoding header. Clients should use the HTTP Accept-Encoding mechanism to request compression if desired, being prepared to handle either compressed or uncompressed responses.

## Number and order of values ##

Every line in a given CSV or TSV response shall have the same number and order of values, with missing values indicated by an empty field (two commas or tabs respectively) or by agreed-upon terms to indicate missing information (e.g., **missing** or **N/A** or **NULL**) in some cases.

<a name="Header_row_and_initial_columns"></a> 
## Header row and initial columns ##

The content of this section implies that all data values in a given row are from the same station, sensor, location and time.

### CSV Encoding ###

The first line of every IOOS CSV response shall comprise a list of column headers that provide names for the values in the following rows. If a value has a unit, it shall be specified in parentheses after the column name. This line is known as the **header row**. All other lines are referred to as **data rows**.

The first several fields of the **header row** shall be, in this order:
```
station_id, sensor_id, latitude (degree), longitude (degree), date_time, depth (m)
```

The initial fields of each data row shall provide values for these quantities as follows:

_**station_id / sensor_id**_

>URNs as defined in the [IOOS Conventions for Observing Asset Identifiers] (http://ioos.github.io/conventions-for-observing-asset-identifiers).

_**latitude / longitude**_

>Latitude and longitude in decimal degrees.

_**date_time**_

>Date and time in ISO 8601 format with punctuation (normally yyyy-mm-ddThh:mm:ssZ, with variations for non-specific times, e.g. climatological average of temperature in August regardless of year).

_**depth**_

>Depth in meters, positive below the surface of the water.

<br>


### TSV Encoding ###

The first line of every IOOS TSV response shall comprise a list of column headers that provide names for the values in the following rows separated by a single tab. If a value has a unit, it shall be specified in square brackets [] at the end of the column name (no text is permitted after the closing bracket). This line is known as the **header row.** All other lines are referred to as **data rows**.

The first several fields of the **header row** shall be, in this order:
```
station_id:METAVAR:TEXT:61   sensor_id:METAVAR:TEXT:61   latitude [degree]   longitude [degree]   time_ISO8601   depth [m]
```

The initial fields of each data row shall provide values for these quantities as follows:

_**station_id:METAVAR:TEXT:61 / sensor_id:METAVAR:TEXT:61**_

>represent the **station_id** and **sensor_id** with URNs as defined in the [IOOS Conventions for Observing Asset Identifiers](http://ioos.github.io/conventions-for-observing-asset-identifiers).
     
>**NOTE:**
> These fields are named to provide consistency with ODV tools.

_**latitude / longitude**_

>Latitude and longitude in decimal degrees relative to World Geodetic System 1984 (WGS84) coordinate reference system (CRS ID = **urn:ogc:def:crs:epsg::4326**).

_**time_ISO8601**_

>Date and time in ISO 8601 format with punctuation (normally **yyyy-mm-ddThh:mm:ssZ**, with variations for non-specific times, e.g. climatological average of temperature in August regardless of     year).

_**depth**_

>Depth in meters, positive below the surface of the water.

## Phenomenon-specific columns ##

As discussed in [Conventions for Specific Observed Properties](#Conventions_for_Specific_Observed_Properties) below, the remaining fields will depend on the phenomenon, data value type, and sampling feature types. In order to ensure compatibility of CSV and TSV encodings from different servers, the ordering of some mandatory fields for each will be specified in this document for various phenomena. Data providers may offer additional fields after the mandatory fields; their order is not specified here but a name shall be provided for every column in the CSV or TSV response.

## Use of CF Names ##

In the header row, names of data values shall use the [Climate and Forecast (CF) Standard Names](http://cf-pcmdi.llnl.gov/documents/cf-standard-names/standard-name-table/12/cf-standard-name-table.html) where possible. The units of the quantity are not required to be the same as the "canonical" CF unit as long as there is a well-known conversion formula from the specified units to the canonical units. Example: The canonical unit of Temperature in CF is Kelvin, but data may be reported in Celsius.

## Time-dependent metadata ##

Some types of data may include time-dependent metadata associated with each measurement. Example: data on ocean currents that includes pitch, roll and yaw information for the sensor. The general practice shall be to place the principal data values first in each data row, followed by any associated time-dependent metadata.

## Result Size ##

IOOS does not impose limits on the number of lines in the CSV or TSV response or values on each line. However, it is good to remember that common spreadsheet applications have a limit of 256 values per row and 65536 rows per sheet.

The CSV as well as TSV response may be compressed (see above under [Compression](#Compression) to minimize transmitted file size.

## Empty Dataset ##

A CSV or TSV response to a valid request that yielded no data at all (e.g., time range or bounding box did not match any stations) shall contain a header row and zero data rows.

> **NOTE:**
> This approach differs from the handling of empty datasets in the IOOS GML case. There, if no data corresponds to a query, an OGC Web Service Exception is returned (i.e., a brief XML document explaining     the nature of the problem). The Empty Dataset response proposed here means these CSV and TSV conventions could be applied regardless of whether the data are served by SOS or obtained through other means.

## Sort Order ##

Data from multiple times from a single station shall be sorted in order from earliest time to latest time.

Data from multiple depths from a single station shall be sorted in order from shallowest to deepest.

Data from multiple stations shall group data from each station together. No ordering is imposed for the station listing. A suggested practice is to sort by ascending **station_id**.

Sorting of data from multiple stations, times and depths shall be as follows:

 -  All data from a single station is grouped.
 -  Data from that station shall be in time order.
 -  All depths from that time shall be presented in order.

Conceptual illustration:
```
   Data from Station 1 at time 1 and depth 1
   Data from Station 1 at time 1 and depth 2
   Data from Station 1 at time 2 and depth 1  
   Data from Station 1 at time 2 and depth 2
   Data from Station 2 at time 1 and depth 1
   Data from Station 2 at time 1 and depth 2
   Data from Station 2 at time 2 and depth 1
   Data from Station 2 at time 2 and depth 2
```

<a name="Conventions_for_Specific_Observed_Properties"></a> 
# Conventions for Specific Observed  Properties #

In the following, the order and title of each IOOS mandatory column is specified. All mandatory columns must be included (with a null value if needed).

Any IOOS optional columns are listed next, and if used shall be placed after the mandatory columns. If any IOOS optional columns are used, they must be in the same order as specified here. If only some of the IOOS optional columns are used, then at least the first optional column and all columns up to and including the last column needed must be provided (with nulls as appropriate). A trailing set of one or more empty columns can be omitted. (In other words, do not omit columns that are not needed between columns that are needed--instead, put in a column(s) of nulls.)

Header fields shall use the column names specified below. The names are based on the CF (Climate and Forecast) Standard Names where possible.

Data providers may add additional provider-specific columns. However, if so then all the IOOS optional columns must be included (with nulls as needed) and the provider-specific columns placed after.

>**NOTE:**
>A generic document should probably be written for "scalars" and "vectors" at "points" and "profiles," omitting conventions for specific observed properties. However, because column order is  important for quantities with multiple data and metadata values on each row, it is difficult to write a general treatment without considering specific phenomena.


## Temperature ##

IOOS Mandatory fields:

   -  _**sea\_water\_temperature \(C\)**_

IOOS Optional fields:

   -  _none_

>**NOTE:**
>CF canonical units are K (degrees Kelvin) for temperature. IOOS uses C (degrees Celsius).

[Sample CSV response](http://sdf.ndbc.noaa.gov/sos/server.php?request=GetObservation&service=SOS&offering=urn:ioos:station:wmo:41012&observedproperty=Sea_Water_Temperature&responseformat=text/csv):

```
station_id,sensor_id,latitude (degree),longitude (degree),date_time,"depth (m)","sea_water_temperature (C)"

urn:ioos:station:wmo:41012:,urn:ioos:sensor:wmo:41012::watertemp1:,30.04,-80.55,2008-08-01T00:50:00Z,0.60,27.70

urn:ioos:station:wmo:41012:,urn:ioos:sensor:wmo:41012::watertemp1:,30.04,-80.55,2008-08-01T01:50:00Z,0.60,27.70

urn:ioos:station:wmo:41012:,urn:ioos:sensor:wmo:41012::watertemp1:,30.04,-80.55,2008-08-01T02:50:00Z,0.60,27.60
```

[Sample TSV response](http://sdf.ndbc.noaa.gov/sos/server.php?request=GetObservation&service=SOS&offering=urn:ioos:station:wmo:41012&observedproperty=Sea_Water_Temperature&responseformat=text/tab-separated-values):

```
station_id:METAVAR:TEXT:61   sensor_id:METAVAR:TEXT:61   latitude [degree]   longitude [degree]  time_ISO8601    depth [m]   sea_water_temperature [C]

urn:ioos:station:wmo:41012: urn:ioos:sensor:wmo:41012::watertemp1:  30.04   -80.55  2008-08-01T00:50:00Z    0.60    27.70

urn:ioos:station:wmo:41012: urn:ioos:sensor:wmo:41012::watertemp1:  30.04   -80.55  2008-08-01T01:50:00Z    0.60    27.70

urn:ioos:station:wmo:41012: urn:ioos:sensor:wmo:41012::watertemp1:  30.04   -80.55  2008-08-01T02:50:00Z    0.60    27.60  
```

## Salinity ##

IOOS Mandatory fields:

   -  _**sea\_water\_salinity (psu)**_

IOOS Optional fields:

   - _none_ 

[Sample CSV response](http://sdf.ndbc.noaa.gov/sos/server.php?request=GetObservation&service=SOS&offering=urn:ioos:station:wmo:41012&observedproperty=Sea_Water_Salinity&responseformat=text/csv):

```
station_id,sensor_id,latitude (degree),longitude (degree),date_time,"depth (m)","sea_water_salinity (psu)"

urn:ioos:station:wmo:41012:,urn:ioos:sensor:wmo:41012::ct1:,30.04,-80.55,2008-08-01T00:50:00Z,1.00,36.25

urn:ioos:station:wmo:41012:,urn:ioos:sensor:wmo:41012::ct1:,30.04,-80.55,2008-08-01T01:50:00Z,1.00,36.25

urn:ioos:station:wmo:41012:,urn:ioos:sensor:wmo:41012::ct1:,30.04,-80.55,2008-08-01T02:50:00Z,1.00,36.25
```

[Sample TSV response](http://sdf.ndbc.noaa.gov/sos/server.php?request=GetObservation&service=SOS&offering=urn:ioos:station:wmo:41012&observedproperty=Sea_Water_Salinity&responseformat=text/tab-separated-values):

```
station_id:METAVAR:TEXT:61  sensor_id:METAVAR:TEXT:61   latitude [degree]   longitude [degree]  time_ISO8601    depth [m]   sea_water_salinity [psu]     

urn:ioos:station:wmo:41012: urn:ioos:sensor:wmo:41012::ct1: 30.04   -80.55  2008-08-01T00:50:00Z    1.00    36.25     

urn:ioos:station:wmo:41012: urn:ioos:sensor:wmo:41012::ct1: 30.04   -80.55  2008-08-01T01:50:00Z    1.00    36.25     

urn:ioos:station:wmo:41012: urn:ioos:sensor:wmo:41012::ct1: 30.04   -80.55  2008-08-01T02:50:00Z    1.00    36.25
```

## Sea Floor Depth (Tsunameter Water Level) ##

IOOS Mandatory fields:

   - _**sea\_floor\_depth\_below\_sea\_surface (m)**_

IOOS Optional fields:

   - _**averaging interval (s)**_

[Sample CSV response](http://sdf.ndbc.noaa.gov/sos/server.php?request=GetObservation&service=SOS&offering=urn:ioos:station:wmo:46403&observedproperty=sea_floor_depth_below_sea_surface&responseformat=text/csv):

```
station_id,sensor_id,latitude (degree),longitude (degree),date_time,"sea_floor_depth_below_sea_surface (m)","averaging_interval (s)"     

urn:ioos:station:wmo:46403:,urn:ioos:sensor:wmo:46403::tsunameter0:,52.65,-156.94,2008-07-17T00:00:00Z,4509.488,900     

urn:ioos:station:wmo:46403:,urn:ioos:sensor:wmo:46403::tsunameter0:,52.65,-156.94,2008-07-17T00:15:00Z,4509.464,900     

urn:ioos:station:wmo:46403:,urn:ioos:sensor:wmo:46403::tsunameter0:,52.65,-156.94,2008-07-17T00:30:00Z,4509.435,900
```

[Sample TSV response](http://sdf.ndbc.noaa.gov/sos/server.php?request=GetObservation&service=SOS&offering=urn:ioos:station:wmo:46403&observedproperty=sea_floor_depth_below_sea_surface&responseformat=text/tab-separated-values):

```
station_id:METAVAR:TEXT:61  sensor_id:METAVAR:TEXT:61   latitude [degree]   longitude [degree]  time_ISO8601    sea_floor_depth_below_sea_surface [m]   averaging_interval [s]     

urn:ioos:station:wmo:46403: urn:ioos:sensor:wmo:46403::tsunameter0: 52.65   -156.94 2008-07-17T00:00:00Z    4509.488    900

urn:ioos:station:wmo:46403: urn:ioos:sensor:wmo:46403::tsunameter0: 52.65   -156.94 2008-07-17T00:15:00Z    4509.464    900

urn:ioos:station:wmo:46403: urn:ioos:sensor:wmo:46403::tsunameter0: 52.65   -156.94 2008-07-17T00:30:00Z    4509.435    900
```

## Water Surface Height (Tide Gauge Water Level) ##

IOOS Mandatory fields:

   -  _**water\_surface\_height\_above\_reference\_datum (m)**_
   -   _**datum\_id**_

IOOS Optional fields:

   -  _none_

>**NOTE:** 
>The **datum_id** is an identifier for the vertical datum to which the water-level measurements are referenced.  Values presently in use at IOOS are:
>
>_urn:x-noaa:def:datum:noaa::IGLD (International Great Lakes Datum)_
>_urn:x-noaa:def:datum:noaa::MHW (mean high water)_
>_urn:x-noaa:def:datum:noaa::MLLW (mean lower low water)_
>_urn:x-noaa:def:datum:noaa::MSL (mean sea level)_
>_urn:ogc:def:datum:epsg::5103 (North American Vertical Datum 1988)_
>_urn:x-noaa:def:datum:noaa::STND (station datum--values are referenced only to local station)_

  [Sample CSV response](http://tbd):

```
station_id,sensor_id,latitude (degree),longitude (degree),date_time,"water_surface_height_above_reference_datum (m)",datum_id

urn:x-noaa:def:station:NOAA.NOS.CO-OPS::1617433, urn:x-noaa:def:sensor:NOAA.NOS.CO-OPS::1617433:A1,20.03658,-155.82936, 2010-03-02T13:48:00Z,0.432,urn:x-noaa:def:datum:noaa::MLLW
```

[Sample TSV response](http://tbd):

```
station_id:METAVAR:TEXT:61  sensor_id:METAVAR:TEXT:61   latitude [degree]   longitude [degree]  time_ISO8601    water_surface_height_above_reference_datum [m]  datum_id

urn:x-noaa:def:station:NOAA.NOS.CO-OPS::1617433  urn:x-noaa:def:sensor:NOAA.NOS.CO-OPS::1617433:A1  20.03658    -155.82936   2010-03-02T13:48:00Z   0.432   urn:x-noaa:def:datum:noaa::MLLW
```


## Winds ##

IOOS Mandatory fields:

   - _**wind\_from\_direction (degree)**_
   - _**wind\_speed (m/s)**_
   - _**wind\_speed\_of\_gust (m/s)**_
   - _**upward\_air\_velocity (m/s)**_

IOOS Optional fields:

   - _none_
<br>

>**NOTE:**
>The **depth** value in the wind response is negative, because depth is positive below the water surface whereas the wind sensor is above the water.

<br>

[Sample CSV response](http://sdf.ndbc.noaa.gov/sos/server.php?request=GetObservation&service=SOS&offering=urn:ioos:station:wmo:41012&observedproperty=Winds&responseformat=text/csv):

```
station_id,sensor_id,latitude (degree),longitude (degree),date_time,"depth (m)"," wind_from_direction (degree)","wind_speed (m/s)"," wind_speed_of_gust (m/s)"," upward_air_velocity(m/s)"

urn:ioos:station:wmo:41012:,urn:ioos:sensor:wmo:41012::anemometer1:,30.04,-80.55,2008-08-01T00:50:00Z,-5.00,239.0,9.00,10.50

urn:ioos:station:wmo:41012:,urn:ioos:sensor:wmo:41012::anemometer1:,30.04,-80.55,2008-08-01T01:50:00Z,-5.00,234.0,8.30,9.30

urn:ioos:station:wmo:41012:,urn:ioos:sensor:wmo:41012::anemometer1:,30.04,-80.55,2008-08-01T02:50:00Z,-5.00,241.0,8.90,10.90
```

[Sample TSV response](http://sdf.ndbc.noaa.gov/sos/server.php?request=GetObservation&service=SOS&offering=urn:ioos:station:wmo:41012&observedproperty=Winds&responseformat=text/tab-separated-values):

```
station_id:METAVAR:TEXT:61  sensor_id:METAVAR:TEXT:61   latitude [degree]   longitude [degree]  time_ISO8601    depth [m]    wind_from_direction [degree]   wind_speed [m/s]     wind_speed_of_gust [m/s]    upward_air_velocity [m/s]

urn:ioos:station:wmo:41012: urn:ioos:sensor:wmo:41012::anemometer1: 30.04   -80.55  2008-08-01T00:50:00Z    -5.00   239.0   9.00    10.50

urn:ioos:station:wmo:41012: urn:ioos:sensor:wmo:41012::anemometer1: 30.04   -80.55  2008-08-01T01:50:00Z    -5.00   234.0   8.30    9.30

urn:ioos:station:wmo:41012: urn:ioos:sensor:wmo:41012::anemometer1: 30.04   -80.55  2008-08-01T02:50:00Z    -5.00   241.0   8.90    10.90
```

## Currents ##

IOOS Mandatory fields:

   - _**direction\_of\_sea\_water\_velocity (degree)**_
   - _**sea\_water\_speed (cm/s)**_
   - _**upward\_sea\_water\_velocity (cm/s)**_


IOOS Optional fields:

   -  _**Error Velocity (cm/s)**_
   -  _**platform\_orientation (degree)**_
   -  _**platform\_pitch\_angle (degree)**_
   -  _**platform\_roll\_angle (degree)**_
   -  _**sea\_water\_temperature (C)**_
   -  _**Pct Good 3 Beam (%)**_
   -  _**Pct Good 4 Beam (%)**_
   -  _**Pct Rejected (%)**_
   -  _**Pct Bad (%)**_
   -  _**Echo Intensity Beam1 (count)**_
   -  _**Echo Intensity Beam2 (count)**_ 
   -  _**Echo Intensity Beam3 (count)**_ 
   -  _**Echo Intensity Beam4 (count)**_
   -   _**Correlation Magnitude Beam1 (count)**_ 
   -   _**Correlation Magnitude Beam2 (count)**_ 
   -    _**Correlation Magnitude Beam3 (count)**_ 
   -  _**Correlation Magnitude Beam4 (count)**_ 
   -  _**Quality Flags**_
<br />

>**NOTES:**
>
>  - CF canonical units are **m/s** for the current speed quantities. IOOS uses **cm/s**.
>  - The Quality Flags are packed together in a single semicolon-delimited string. The meaning of the quality flags for a particular sensor would be described in metadata (such as might be obtained using the SOS **DescribeSensor** operation).
>  - NDBC currents from MMS stations are the only dataset for which we have quality flags at present. This is a topic for further discussion, especially in the case of differing quality flags for the same phenomenon measured by different models of sensor (or post-processed by different quality-control procedures).
>  - In the sample dataset and sample response below, there are 9 quality flags representing the results of the following quality tests based on their position (left to right) in the flags field:
>     - **Flag 1** represents the overall bin status.
>     - **Flag 2** represents the ADCP Built-In Test (BIT) status.
>     - **Flag 3** represents the Error Velocity test status.
>     - **Flag 4** represents the Percent Good test status.
>     - **Flag 5** represents the Correlation Magnitude test status.
>     - **Flag 6** represents the Vertical Velocity test status.
>     - **Flag 7** represents the North Horizontal Velocity test status.
>     - **Flag 8** represents the East Horizontal Velocity test status.
>     - **Flag 9** represents the Echo Intensity test status.
>
>>Valid flag values are:
>>  
>>   - **0** = quality not evaluated;
>>   - **1** = failed quality test; <br/>
>>   - **2** = questionable or suspect data;
>>   - **3** = good data/passed quality test; and
>>   - **9** = missing data.
>>
>These flag meanings and values do not necessarily apply to any other ocean currents data.
>
>**WARNING:** >
>The flags defined above have not been vetted with the QARTOD flagging scheme.  In a future update of this manual the flagging guidelines will be removed and instead the QARTOD scheme will be adopted.


[Sample CSV response](http://sdf.ndbc.noaa.gov/sos/server.php?request=GetObservation&service=SOS&offering=urn:ioos:station:wmo:42361&observedproperty=Currents&responseformat=text/csv):

```
station_id,sensor_id,latitude (degree),longitude (degree),date/time,"bin (count)","depth (m)","direction_of_sea_water_velocity (degree)","sea_water_speed (cm/s)","upward_sea_water_velocity (cm/s)","error_velocity (cm/s)",platform_orientation (degree),"platform_pitch_angle (degree)","platform_roll_angle (degree),"sea_water_temperature (c)","pct_good_3_beam (%)","pct_good_4_beam (%)","pct_rejected (%)","pct_bad (%)","echo_intensity_beam1 (count)","echo_intensity_beam2 (count)","echo_intensity_beam3 (count)","echo_intensity_beam4 (count)","correlation_magnitude_beam1 (count)","correlation_magnitude_beam2 (count)","correlation_magnitude_beam3 (count)","correlation_magnitude_beam4 (count)","quality_flags"     

urn:ioos:station:wmo:42361:,urn:ioos:sensor:wmo:42361::adcp0:,27.55,-92.49,2010-03-02T16:03Z,1,51.00,292,16.2,-2.0,0.5,,,,,99,99,99,,205,195,215,202,,,,,3;3;3;3;9;3;3;3;0     

urn:ioos:station:wmo:42361:,urn:ioos:sensor:wmo:42361::adcp0:,27.55,-92.49,2010-03-02T16:03Z,2,67.00,305,23.7,-0.5,-0.5,,,,,99,99,99,,194,183,198,190,,,,,3;3;3;3;9;3;3;3;0     

urn:ioos:station:wmo:42361:,urn:ioos:sensor:wmo:42361::adcp0:,27.55,-92.49,2010-03-02T16:03Z,3,83.00,317,19.8,-0.5,-1.0,,,,,99,99,99,,202,170,216,172,,,,,3;3;3;3;9;3;3;3;0
```

[Sample TSV response](http://sdf.ndbc.noaa.gov/sos/server.php?request=GetObservation&service=SOS&offering=urn:ioos:station:wmo:42361&observedproperty=Currents&responseformat=text/tab-separated-values):

```
station_id:METAVAR:TEXT:61  sensor_id:METAVAR:TEXT:61   latitude [degree]   longitude [degree]  time_ISO8601    bin [count] depth [m]   direction_of_sea_water_velocity [degree]    sea_water_speed [cm/s]  upward_sea_water_velocity [cm/s]    error_velocity [cm/s]   platform_orientation [degree]   platform_pitch_angle [degree]   platform_roll_angle [degree]    sea_water_temperature [c]   pct_good_3_beam [%] pct_good_4_beam [%] pct_rejected [%]    pct_bad [%] echo_intensity_beam1 [count]    echo_intensity_beam2 [count]    echo_intensity_beam3 [count]    echo_intensity_beam4 [count]    correlation_magnitude_beam1 [count] correlation_magnitude_beam2 [count] correlation_magnitude_beam3 [count] correlation_magnitude_beam4 [count] quality_flags     

urn:ioos:station:wmo:42361: urn:ioos:sensor:wmo:42361::adcp0:   27.55   -92.49  2010-03-02T16:03Z   1   51.00   292 16.2    -2.0    0.5                 99  99  99      205 195 215 202                 3;3;3;3;9;3;3;3;0     

urn:ioos:station:wmo:42361: urn:ioos:sensor:wmo:42361::adcp0:   27.55   -92.49  2010-03-02T16:03Z   2   67.00   305 23.7    -0.5    -0.5                    99  99  99      194 183 198 190                 3;3;3;3;9;3;3;3;0     

urn:ioos:station:wmo:42361: urn:ioos:sensor:wmo:42361::adcp0:   27.55   -92.49  2010-03-02T16:03Z   3   83.00   317 19.8    -0.5    -1.0                    99  99  99      202 170 216 172                 3;3;3;3;9;3;3;3;0
```
<br>

## Waves ##

Waves data may be reported either using only the list of mandatory fields or by including complete spectral information in the optional fields.

The spectral information includes spectral energy, directions and polar coordinates at a number of frequencies. The number of frequencies in the spectrum shall be indicated in each data row; the remaining fields shall provide values for each spectral quantity at each frequency. Each spectral quantity shall be expressed as a list of values separated by semicolons within a single pair of commas. (That is, rather than using a separate column for each value, we pack lists of values for the same quantity as a semicolon-delimited string). 

>**NOTES:**
>  - Illustrative example in the simplified case of 3 frequencies (at 0.04, 0.05 and 0.06 Hz) each of bandwidth 0.005 Hz:
```
        3,0.04;0.05;0.06,0.005;0.005;0.005 
```
>  - The spectral quantities are packed because a assigning a separate column (comma-delimited value) for each number causes the number of columns to exceed the maximum (256) supported by typical spreadsheet applications)

IOOS Mandatory fields:

   - _**sea\_surface\_wave\_significant\_height (m)**_
   - _**sea\_surface\_wave\_peak\_period (s)**_
   - _**sea\_surface\_wave\_mean\_period (s)**_
   - _**sea\_surface\_swell\_wave\_significant\_height (m)**_
   - _**sea\_surface\_swell\_wave\_period (s)**_
   - _**sea\_surface\_wind\_wave\_significant\_height (m)**_
   - _**sea\_surface\_wind\_wave\_period (s)**_
   - _**sea\_water\_temperature \(C\)**_
   - _**sea\_surface\_wave\_to\_direction (degree)**_
   - _**sea\_surface\_swell\_wave\_to\_direction (degree)**_
   - _**sea\_surface\_wind\_wave\_to\_direction (degree)**_

  IOOS Optional fields:

   - _**number\_of\_frequencies (count)**_
   - _**center\_frequencies (Hz)**_
   - _**bandwidths (Hz)**_
   - _**spectral\_energy (m:sup:`2`/Hz)**_
   - _**mean\_wave\_direction (degree)**_
   - _**principal\_wave\_direction (degree)**_
   - _**polar\_coordinate\_r1 (1)**_
   - _**polar\_coordinate\_r2 (1)**_
   - _**calculation\_method**_
   - _**sampling\_rate**_

>**NOTE:**
> as discussed above, **number\_of\_frequencies** is an integer, and all the following fields are semicolon-delimited lists each containing that number of elements.

[Sample CSV response](http://sdf.ndbc.noaa.gov/sos/server.php?request=GetObservation&service=SOS&offering=urn:ioos:station:wmo:46088&observedproperty=Currents&responseformat=text/csv):

```
station_id,sensor_id,latitude (degree),longitude (degree),date_time,"sea_surface_wave_significant_height (m)","sea_surface_wave_peak_period (s)","sea_surface_wave_mean_period (s)","sea_surface_swell_wave_significant_height (m)","sea_surface_swell_wave_period  (s)","sea_surface_wind_wave_significant_height  (m)","sea_surface_wind_wave_period  (s)","sea_water_temperature (c)","sea_surface_wave_to_direction (degree)","sea_surface_swell_wave_to_direction (degree)","sea_surface_wind_wave_to_direction (degree)","number of frequencies (count)","center frequencies (Hz)","bandwidths (Hz)","spectral energy (m**2/Hz)","mean wave direction (degree)","principal wave direction (degree)","polar coordinate r1 (1)","polar coordinate r2 (1)",calculation_method,"sampling_rate (Hz)"

urn:ioos:station:wmo:42002:,urn:ioos:sensor:wmo:42002::wpm1:,25.17,-94.42,2008-12-24T04:50:00Z,2.62,7.69,5.79,0.00,0.00,2.62,7.70,,111.0,,111.0,46,0.0325;0.0375;0.0425;0.0475;0.0525;0.0575;0.0625;0.0675;0.0725;0.0775;0.0825;0.0875;0.0925;0.1000;0.1100;0.1200;0.1300;0.1400;0.1500;0.1600;0.1700;0.1800;0.1900;0.2000;0.2100;0.2200;0.2300;0.2400;0.2500;0.2600;0.2700;0.2800;0.2900;0.3000;0.3100;0.3200;0.3300;0.3400;0.3500;0.3650;0.3850;0.4050;0.4250;0.4450;0.4650;0.4850,0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0200;0.0200;0.0200;0.0200;0.0200;0.0200;0.0200,0;0;0;0;0;0;0;0;0;0;0;0;0;0.0822186;1.22307;8.15093;9.32595;3.67259;4.63691;2.07881;1.957;1.36319;1.85478;0.995907;1.30765;0.781963;0.752923;0.907616;0.22676;0.632239;0.323894;0.430227;0.305904;0.295656;0.209008;0.199;0.159108;0.14396;0.126935;0.0803674;0.0732104;0.0557885;0.0367058;0.0182708;0.0189928;0.0254116,104.0;272.0;244.0;134.0;259.0;260.0;218.0;264.0;268.0;91.0;110.0;267.0;70.0;87.0;91.0;108.0;111.0;126.0;121.0;156.0;146.0;157.0;131.0;172.0;153.0;162.0;159.0;163.0;158.0;167.0;160.0;163.0;159.0;155.0;169.0;148.0;162.0;184.0;170.0;184.0;182.0;170.0;189.0;180.0;149.0;1.0,79.0;257.0;248.0;63.0;249.0;243.0;244.0;247.0;262.0;84.0;89.0;258.0;64.0;24.0;62.0;107.0;105.0;193.0;114.0;173.0;145.0;155.0;127.0;196.0;157.0;168.0;169.0;166.0;221.0;185.0;228.0;172.0;187.0;157.0;180.0;137.0;160.0;192.0;178.0;203.0;195.0;181.0;195.0;181.0;178.0;10.0,0.462758;0.365588;0.352569;0.446279;0.525379;0.316228;0.379088;0.525379;0.702193;0.607386;0.641337;0.607386;0.454444;0.365588;0.653071;0.797214;0.78289;0.564896;0.78289;0.702193;0.741445;0.715041;0.872861;0.715041;0.75501;0.797214;0.741445;0.841777;0.430386;0.75501;0.564896;0.728123;0.689577;0.75501;0.741445;0.715041;0.768824;0.78289;0.715041;0.741445;0.75501;0.768824;0.689577;0.677187;0.544779;0.677187,0.689577;0.564896;0.66502;0.340013;0.826652;0.607386;0.618498;0.641337;0.585756;0.415059;0.506669;0.304966;0.446279;0.462758;0.407602;0.372277;0.488626;0.161682;0.488626;0.515939;0.35902;0.186919;0.702193;0.379088;0.299487;0.544779;0.422653;0.575231;0.372277;0.393087;0.268617;0.310546;0.200979;0.365588;0.254397;0.273532;0.316228;0.446279;0.278536;0.497566;0.564896;0.462758;0.400278;0.294106;0.322013;0.446279,,
```

[Sample TSV response](http://sdf.ndbc.noaa.gov/sos/server.php?request=GetObservation&service=SOS&offering=urn:ioos:station:wmo:42002&observedproperty=waves&responseformat=text/tab-separated-values):

```
station_id:METAVAR:TEXT:61  sensor_id:METAVAR:TEXT:61   latitude [degree]   longitude [degree]  time_ISO8601     sea_surface_wave_significant_height [m]     sea_surface_wave_peak_period [s]    sea_surface_wave_mean_period [s]    sea_surface_swell_wave_significant_height [m]   sea_surface_swell_wave_period  [s]  sea_surface_wind_wave_significant_height  [m]   sea_surface_wind_wave_period  [s]   sea_water_temperature [c]   sea_surface_wave_to_direction [degree]  sea_surface_swell_wave_to_direction [degree]    sea_surface_wind_wave_to_direction [degree]    number of frequencies [count]   center frequencies [Hz] bandwidths [Hz] spectral energy [m**2/Hz]   mean wave direction [degree]    principal wave direction [degree]   polar coordinate r1 [1] polar coordinate r2 [1] calculation_method  sampling_rate [Hz]

urn:ioos:station:wmo:42002: urn:ioos:sensor:wmo:42002::wpm1:    25.17   -94.42  2008-12-24T04:50:00Z    2.62    7.69    5.79    0.00    0.00    2.62    7.70        111.0       111.0   46  0.0325;0.0375;0.0425;0.0475;0.0525;0.0575;0.0625;0.0675;0.0725;0.0775;0.0825;0.0875;0.0925;0.1000;0.1100;0.1200;0.1300;0.1400;0.1500;0.1600;0.1700;0.1800;0.1900;0.2000;0.2100;0.2200;0.2300;0.2400;0.2500;0.2600;0.2700;0.2800;0.2900;0.3000;0.3100;0.3200;0.3300;0.3400;0.3500;0.3650;0.3850;0.4050;0.4250;0.4450;0.4650;0.4850   0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0050;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0100;0.0200;0.0200;0.0200;0.0200;0.0200;0.0200;0.0200   0;0;0;0;0;0;0;0;0;0;0;0;0;0.0822186;1.22307;8.15093;9.32595;3.67259;4.63691;2.07881;1.957;1.36319;1.85478;0.995907;1.30765;0.781963;0.752923;0.907616;0.22676;0.632239;0.323894;0.430227;0.305904;0.295656;0.209008;0.199;0.159108;0.14396;0.126935;0.0803674;0.0732104;0.0557885;0.0367058;0.0182708;0.0189928;0.0254116   104.0;272.0;244.0;134.0;259.0;260.0;218.0;264.0;268.0;91.0;110.0;267.0;70.0;87.0;91.0;108.0;111.0;126.0;121.0;156.0;146.0;157.0;131.0;172.0;153.0;162.0;159.0;163.0;158.0;167.0;160.0;163.0;159.0;155.0;169.0;148.0;162.0;184.0;170.0;184.0;182.0;170.0;189.0;180.0;149.0;1.0   79.0;257.0;248.0;63.0;249.0;243.0;244.0;247.0;262.0;84.0;89.0;258.0;64.0;24.0;62.0;107.0;105.0;193.0;114.0;173.0;145.0;155.0;127.0;196.0;157.0;168.0;169.0;166.0;221.0;185.0;228.0;172.0;187.0;157.0;180.0;137.0;160.0;192.0;178.0;203.0;195.0;181.0;195.0;181.0;178.0;10.0 0.462758;0.365588;0.352569;0.446279;0.525379;0.316228;0.379088;0.525379;0.702193;0.607386;0.641337;0.607386;0.454444;0.365588;0.653071;0.797214;0.78289;0.564896;0.78289;0.702193;0.741445;0.715041;0.872861;0.715041;0.75501;0.797214;0.741445;0.841777;0.430386;0.75501;0.564896;0.728123;0.689577;0.75501;0.741445;0.715041;0.768824;0.78289;0.715041;0.741445;0.75501;0.768824;0.689577;0.677187;0.544779;0.677187  0.689577;0.564896;0.66502;0.340013;0.826652;0.607386;0.618498;0.641337;0.585756;0.415059;0.506669;0.304966;0.446279;0.462758;0.407602;0.372277;0.488626;0.161682;0.488626;0.515939;0.35902;0.186919;0.702193;0.379088;0.299487;0.544779;0.422653;0.575231;0.372277;0.393087;0.268617;0.310546;0.200979;0.365588;0.254397;0.273532;0.316228;0.446279;0.278536;0.497566;0.564896;0.462758;0.400278;0.294106;0.322013;0.446279     
```