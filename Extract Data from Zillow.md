# Zillow Data


Now use the following Python code to extract information from Zillow: 

```
#load libraries
import pandas as pd
import numpy as np
import math
from pyzillow.pyzillow import ZillowWrapper, GetDeepSearchResults, GetUpdatedPropertyDetails
from datetime import datetime
```

Convert Address fields into upper case and remove any leading or trailing spaces -- Pandas Dataframe
```
# convert to upper case
data.AddrLine = data['AddrLine'].astype(str).str.upper()
data.City = data['City'].astype(str).str.upper()
data.State = data['State'].astype(str).str.upper()

#remove leading and trailing white spaces
data.AddrLine = data['AddrLine'].str.strip()
data.City = data['City'].str.strip()
data.State = data['State'].str.strip()

#clean zipcode- extract first 5 digits of zipcode only
data.Zip = data.Zip.str[0:5]
```
Concatenate columns to form Full Address  
```
data['FullAddress'] = data.apply(lambda x: '%s %s %s' % (x['AddrLine'], x['City'], x['State']), axis = 1)
data.FullAddress = data.FullAddress.str.upper()
```

Sample Dataframe at the end of data cleaning stage to insert into Zillow: 

```
Id| Zip | FullAddress
1 | 33563 | 908 SPENCER ST W PLANT CITY FL
2 | 33606 | 2120 W CYPRESS STREET TAMPA FL
3 | 33606 | 2120 W CYPRESS ST TAMPA FL
4 | 06877 | 177 MAMANASCO ROAD RIDGEFIELD CT
5 | 34112 | 3635 BOCA CIEGA DR #314 NAPLES FL
```

### Use Zillow API to extract features for the above mentioned addresses

```
#zillow API
zillow_api_key = 'XXXXXXXXXXXXXXXXXXXXXXXXXXX'
```

```
#initialize values
zillowiddict=[]
resultindex =[]
errorlist=[]
``
```
data_set = data

for i in range(data_set.shape[0]):
    zillow_data = ZillowWrapper(zillow_api_key)
    try:
        deep_search_response = zillow_data.get_deep_search_results(data_set.FullAddress.loc[i],data_set.zip.loc[i])
        zillowiddict.append(GetDeepSearchResults(deep_search_response))
        resultindex.append(i)
    except:
        errorlist.append(i)
```

```
print(resultindex)
```

```
len(errorlist)
```

```
print(errorlist) #indexes for which a match was not found on zillow
```

```
zillowdf=pd.DataFrame()
```

```
#extract elements and store in pd dataframe
zillowdf['zillow_id']=pd.Series([i.zillow_id for i in zillowiddict])
zillowdf['home_type']=pd.Series([i.home_type for i in zillowiddict])
#zillowdf['home_detail_link']=pd.Series([i.home_detail_link for i in zillowiddict])
#zillowdf['graph_data_link']=pd.Series([i.graph_data_link for i in zillowiddict])
#zillowdf['map_this_home_link']=pd.Series([i.map_this_home_link for i in zillowiddict])
zillowdf['latitude']=pd.Series([i.latitude for i in zillowiddict])
zillowdf['longitude']=pd.Series([i.longitude for i in zillowiddict])
zillowdf['tax_year']=pd.Series([i.tax_year for i in zillowiddict])
zillowdf['tax_value']=pd.Series([i.tax_value for i in zillowiddict])
zillowdf['year_built']=pd.Series([i.year_built for i in zillowiddict])
zillowdf['property_size']=pd.Series([i.property_size for i in zillowiddict])
zillowdf['home_size']=pd.Series([i.home_size for i in zillowiddict])
zillowdf['area_unit']=pd.Series([i.area_unit for i in zillowiddict])
zillowdf['bathrooms']=pd.Series([i.bathrooms for i in zillowiddict])
zillowdf['bedrooms']=pd.Series([i.bedrooms for i in zillowiddict])
zillowdf['last_sold_date']=pd.Series([i.last_sold_date for i in zillowiddict])
zillowdf['last_sold_price']=pd.Series([i.last_sold_price for i in zillowiddict])
zillowdf['zestimate_amount']=pd.Series([i.zestimate_amount for i in zillowiddict])
zillowdf['zestimate_last_updated']=pd.Series([i.zestimate_last_updated for i in zillowiddict])
zillowdf['zestimate_value_change']=pd.Series([i.zestimate_value_change for i in zillowiddict])
zillowdf['zestimate_valuation_range_high']=pd.Series([i.zestimate_valuation_range_high for i in zillowiddict])
zillowdf['zestimate_valuationRange_low']=pd.Series([i.zestimate_valuationRange_low for i in zillowiddict])
zillowdf['zestimate_percentile']=pd.Series([i.zestimate_percentile for i in zillowiddict])

zillowdf['DF_Index'] = resultindex

zillowdf.head(10)
```

####Merge Zillow Results to Original Dataframe
```
#assign index values to column
#data_set = data_set.reset_index(drop=True)
data_set['Index1'] = data_set.index
data_set.tail()
```

```
#datatype of Index1
zillowdf['DF_Index'] = zillowdf['DF_Index'].astype(int)
data_set['Index1'] = data_set['Index1'].astype(int)
```

```
#merge the two dataframes by index id
result = pd.merge(data_set, zillowdf, left_on='Index1', right_on='DF_Index', how = 'left')
result.head()
```
