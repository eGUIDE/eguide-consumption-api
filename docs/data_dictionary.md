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

Information about the Consumption Model and the output. See model documentation for more details. 

Field | description 
-------|--------
 model_version | model version
 model_output_id | ID of the model run, when output wsa generated
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
 resolution | Resolution of the cell data included in the API response. Highest available resolution is 250 square meters. Depending on the size of the query, the API may use lower resolution cells. 
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

For each cell, the following data is provided. This information indicates the model predictions for the given cell's boundary. The cell size is determined by the `data:resolution` parameter (250 square meters by default).

Field | description 
-------|--------
 classA_count | In the cell, count of structures predicted as belonging to class A 
 classA_prob_mean | The mean of prediction probability scores for class A in the cell.
 classA_prob_std | The standard deviation of prediction probability scores for class A in the cell.
 classB_count | In the cell, count of structures predicted as belonging to class B 
 classB_prob_mean | The mean of prediction probability scores for class B in the cell.
 classB_prob_std | The standard deviation of prediction probability scores for class B in the cell.

See section on "Interpreting the probability figures" for more information on the probability data. 
 

### Data Summary and Aggregation (`data_summary`)

Field | description 
-------|--------
 classA_total_count | In the AOI, count of structures predicted as belonging to class A 
 classA_pooled_prob_mean | The pooled mean of prediction probability scores for class A in the AOI
 classA_pooled_prob_std_dev | The pooled standard deviation of prediction probability scores for class A in the AOI. 
 classB_total_count | In the AOI, count of structures predicted as belonging to class B 
 classB_pooled_prob_mean | The pooled mean of prediction probability scores for class B in the AOI
 classB_pooled_prob_std_dev | The pooled standard deviation of prediction probability scores for class B in the AOI. 
 
See section on "Interpreting the probability figures" for more information on the probability data. 


## Interpreting the probability figures. 

Each prediction is provided a prediction probability between 0 and 1. A probability of 1 means that the model is 100% confident that the structure belongs in the given class. A probability of 0.6 means that the model is 60% confident. 

These predictions are aggregated per cell. In a given cell the model will provide the following information: 
 * Total count of structures predicted, for each class
 * Mean of all the probability scores of the structures in the cell, for each class
 * Standard deviation of all the probability scores of the structures in the cell, for each class

## NaN values
Not a Number (NaN) values in the data are NOT equivalent to zero values. 

## Example JSON response

```
{
    "data": {
        "data_array": [
            {
                "cell_bbox": "POLYGON((35.8829166666667 4.06736111111111,35.8854166666667 4.06736111111111,35.8854166666667 4.06486111111111,35.8829166666667 4.06486111111111,35.8829166666667 4.06736111111111))",
                "cell_id": null,
                "model_output_id": 1,
                "cell_data": {
                    "classA_count": 5.0,
                    "classA_prob_mean": 0.67,
                    "classA_prob_std": 0.1,
                    "classB_count": 0.0,
                    "classB_prob_mean": NaN,
                    "classB_prob_std": NaN
                }
            }
        ],
        "number_of_cells": 1,
        "resolution": 250,
        "summary_only": False
    },
    "data_summary": {
        "classA_pooled_prob_mean": 0.67,
        "classA_pooled_prob_std_dev": 0.1,
        "classA_total_count": 5,
        "classB_pooled_prob_mean": NaN,
        "classB_pooled_prob_std_dev": NaN,
        "classB_total_count": 0
    },
    "model_metadata": {
        "description": "Model output generated for project Eguide, \nIncludes whole of Kenya\nSamples only, not intended for production\n",
        "model_output_id": 1,
        "model_run_date": "2020-05-30",
        "model_version": "0.1.1"
    },
    "request": {
        "AOI_coords": "'POINT(35.8843 4.0665)'",
        "AOI_type": "latlong",
        "requested_resolution": 250
    }
}

```
