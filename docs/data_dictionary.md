# Data Dictionary

This “data dictionary” provides field descriptions and an annotated list of the data columns, fields, values, units, etc. used by the Consumption API

### API Response

A response from the Consumption API consists of 4 sets: 

* "request"
* "model_metadata"
* "data"
* "data_summary"

Each of these sets contains several values, described below. 


### request

Request summarizes your API request and query. 


Field | description 
-------|--------
 requested_resolution | Lorem Ipsum
 AOI_coords | Lorem Ipsum
 AOI_type | Lorem Ipsum
 summary_only | Lorem Ipsum

### model_metadata

Information about the Consumption model and the output. 

Field | description 
-------|--------
 description | Lorem Ipsum
 model_output_id | Lorem Ipsum
 model_run_date | Lorem Ipsum
 model_version | Lorem Ipsum


### data


Field | description 
-------|--------
 data_array | Lorem Ipsum
 number_of_cells | Lorem Ipsum
 resolution | Lorem Ipsum
 summary_only | Lorem Ipsum


#### Data Array
The data array provides the following fields for each cell. This object is not returned if `summary_only == True`

Field | description 
-------|--------
 cell_bbox | Lorem Ipsum
 cell_id | Lorem Ipsum
 resolution | Lorem Ipsum
 data: classA_count | Lorem Ipsum
 data: classA_prob_mean | Lorem Ipsum
 data: classA_prob_std | Lorem Ipsum
 data: classB_count | Lorem Ipsum
 data: classB_prob_mean | Lorem Ipsum
 data: classB_prob_std | Lorem Ipsum

#### NaN values
Not a Number (NaN) values in the data indicate that ... Lorem Ipsum


### data_summary

Field | description 
-------|--------
 classA_pooled_prob_mean | Lorem Ipsum
 classA_pooled_prob_std_dev | Lorem Ipsum
 classA_total_count | Lorem Ipsum
 classB_pooled_prob_mean | Lorem Ipsum
 classB_pooled_prob_std_dev | Lorem Ipsum
 classB_total_count | Lorem Ipsum


## Example JSON response

```
{
    "data": {
        "data_array": [
            {
                "cell_bbox": "POLYGON((35.8829166666667 4.06736111111111,35.8854166666667 4.06736111111111,35.8854166666667 4.06486111111111,35.8829166666667 4.06486111111111,35.8829166666667 4.06736111111111))",
                "cell_id": null,
                "data": {
                    "classA_count": 5.0,
                    "classA_prob_mean": 0.67,
                    "classA_prob_std": 0.1,
                    "classB_count": 0.0,
                    "classB_prob_mean": NaN,
                    "classB_prob_std": NaN
                },
                "model_output_id": 1
            }
        ],
        "number_of_cells": 1,
        "resolution": null,
        "summary_only": null
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
        "requested_resolution": null
    }
}

```
