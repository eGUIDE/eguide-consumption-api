# Data Dictionary

This “data dictionary” provides field descriptions and an annotated list of the data columns, fields, values, units, etc. used by the Consumption API

### API Response

A response from the Consumption API consists of 4 sets: 

* "request"
* "model_metadata"
* "data"
* "data_summary"

Each of these sets contains several values, described below. 


### Request summary (`request`)

Request summarizes your API request and query. AOI refers the "Area of Interest", usually a point or polygon. 


Field | description 
-------|--------
 requested_resolution | Resolution of cells requested, if any
 AOI_coords | Requested AOI coorinates
 AOI_type | Type of AOI, such as POINT or POLYON
 summary_only | TRUE or FALSE. This parameter limits the response to summary fields only, and excludes data for each cell in the AOI.

### Model Output Information (`model_metadata`)

Information about the Consumption Model and the output. [See model documentation](https://docs.google.com/document/u/1/d/17rVR3HTGpc00XnwyGlraEdbf9N4lumi2p6VddXEHSQU/edit?usp=sharing) for more details. 

Field | description 
-------|--------
 model_version | model version
 model_output_id | ID of the model run, when output was generated
 model_run_date | The date the model output was generated. 
 description | Description of the model run and output
 

### Data (`data`)

Nested field with the following structure: 

```
data:
  number of cells: n
  ...
  data_array: [cell1, cell2, cell3, ... celln ]
```

Field | description 
-------|--------
 number_of_cells | Total number of cells that fit in the requested AOI boundary. Minimum cells is 1, for example, for a POINT AOI. 
 resolution | Resolution of the cell data included in the API response. Highest available resolution is 250 square meters. Depending on the size of the query, the API may use lower resolution cells. Available resolutions are: [250, 500, 1000, 10000] square meters.
 summary_only | TRUE or FALSE. If true, the data array is not included in the API response.
 data_array | Data for each cell in the API response. 

#### Data Array (`data:data_array`)

The data array provides model predictions for each cell. 

Note that if `summary_only` is `True`, the data array is not included in the API response.

Field | description 
-------|--------
 cell_bbox | The bounding box for the cell 
 cell_id | unique id of the cell
 model_output_id | unique id of the model run that produced the data contained in the cell
cell_data | model predictions for each cell. See below. 
 

#### Cell-Level Data (`data:data_array:cell_data`)

For each cell, the following data is provided. 

Field | description 
-------|--------
 low_class_count | In the cell, count of structures predicted as belonging to low consumption class  
 high_class_count | In the cell, count of structures predicted as belonging to high consumption class  


This information indicates the model predictions for the given cell's boundary. The cell size is determined by the `data:resolution` parameter. However, larger area queries are automatically assigned larger resolutions as described below

Area size | Default resolution 
-------|--------
less than 100km² |  250m²
100-1,000km² |  500m²
1,000-10,000km² |  1,000m²
greater than 10,000km² |  10,000m²

See section on "Interpreting the probability figures" for more information on the probability data. 
 

### Data Summary and Aggregation (`data_summary`)

Field | description 
-------|--------
 low_class_total_count | In the AOI, count of structures predicted as belonging to low consumption class 
 low_class_pooled_prob_mean | The pooled mean of prediction probability scores for low consumption class in the AOI
 low_class_pooled_prob_std_dev | The pooled standard deviation of prediction probability scores for low consumption class in the AOI. 
 high_class_total_count | In the AOI, count of structures predicted as belonging to high consumption class 
 high_class_pooled_prob_mean | The pooled mean of prediction probability scores for high consumption class in the AOI
 high_class_pooled_prob_std_dev | The pooled standard deviation of prediction probability scores for high consumption class in the AOI. 
 
See section on "Interpreting the probability figures" for more information on the probability data. 


## Interpreting the probability figures. 

Each prediction is provided a prediction probability between 0 and 1. A probability of 1 means that the model is 100% confident that the structure belongs in the given class. A probability of 0.6 means that the model is 60% confident. 

These predictions are aggregated per cell. In a given cell the model will provide the following information: 
 * Total count of structures predicted, for each class.
 * Mean of all the probability scores of the structures in the cell, for each class.
 * Standard deviation of all the probability scores of the structures in the cell, for each class.

## NaN values
Not a Number (NaN) values in the data are NOT equivalent to zero values. 

## Example JSON responses
summary_only flag set to ***FALSE***
```
{
    "data": {
        "data_array": [
            {
                "cell_bbox": "POLYGON((35.880218139897 4.07396190162495,35.8892012516469 4.07396190162495,35.8892012516469 4.06497878987503,35.880218139897 4.06497878987503,35.880218139897 4.07396190162495))",
                "cell_data": {
                    "high_class_count": 1.0,
                    "low_class_count": 15.0
                },
                "cell_id": 238,
                "model_output_id": 3
            },
            {
                "cell_bbox": "POLYGON((35.880218139897 4.06497878987503,35.8892012516469 4.06497878987503,35.8892012516469 4.05599567812512,35.880218139897 4.05599567812512,35.880218139897 4.06497878987503))",
                "cell_data": {
                    "high_class_count": 21.0,
                    "low_class_count": 32.0
                },
                "cell_id": 262,
                "model_output_id": 3
            }
        ],
        "number_of_cells": 2,
        "summary_only": false
    },
    "data_summary": {
        "high_class_pooled_prob_mean": 0.58,
        "high_class_pooled_prob_std_dev": 0.07,
        "high_class_total_count": 22,
        "low_class_pooled_prob_mean": 0.65,
        "low_class_pooled_prob_std_dev": 0.08,
        "low_class_total_count": 47,
        "total_structure_count": 69
    },
    "model_metadata": {
        "description": "Model output generated for EGUIDE API Includes 1 country Intended for production",
        "model_output_id": 1,
        "model_run_date": "2020-07-23",
        "model_version": "0.1.1",
        "resolution (meters)": 1000
    },
    "request": {
        "AOI_coords": "'POLYGON((35.8843 4.0665,35.8851 4.0660, 35.8843 4.0606, 35.8795 4.0658, 35.8843 4.0665 ))'",
        "AOI_type": "polygon",
        "other params": {},
        "requested_resolution": "1000",
        "summary_only": false
    }
}
```

summary_only set to ***TRUE***
```
{
    "data": {
        "number_of_cells": 2,
        "summary_only": true
    },
    "data_summary": {
        "high_class_pooled_prob_mean": 0.58,
        "high_class_pooled_prob_std_dev": 0.07,
        "high_class_total_count": 22,
        "low_class_pooled_prob_mean": 0.65,
        "low_class_pooled_prob_std_dev": 0.08,
        "low_class_total_count": 47,
        "total_structure_count": 69
    },
    "model_metadata": {
        "description": "Model output generated for EGUIDE API Includes 1 country Intended for production",
        "model_output_id": 1,
        "model_run_date": "2020-07-23",
        "model_version": "0.1.1",
        "resolution (meters)": 1000
    },
    "request": {
        "AOI_coords": "'POLYGON((35.8843 4.0665,35.8851 4.0660, 35.8843 4.0606, 35.8795 4.0658, 35.8843 4.0665 ))'",
        "AOI_type": "polygon",
        "other params": {},
        "requested_resolution": "1000",
        "summary_only": true
    }
}
```
