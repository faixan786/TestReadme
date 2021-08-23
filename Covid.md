# Use Case - Covid

In this example, we will generate covid statistics.  
We will upload a file containing country names along with their population and from that data we will generate covid statistics.

1. Upload the file "population.csv" in `User Data` tab of CloudTDMS web portal. 

2. Create a new stream with the following content:
    ```
    {
        "stream": "Covid_Stats",
        "records": 200,
        "sources": [
            {
                "location": "local",
                "files": [
                    {
                        "filename": "population_data.csv",
                        "format": "csv",
                        "has_header": true,
                        "alias": "country_source"
                    }
                ]
            }
        ],
        "destinations": [
            {
                "location": "local",
                "args": {
                    "format": "csv"
                }
            }
        ],
        "schema": [
            {
                "column_title": "Country",
                "column_name": "Country",
                "source_type": "source",
                "source": "country_source",
                "display": true
            },
            {
                "column_title": "Population",
                "column_name": "Population",
                "source_type": "source",
                "source": "country_source",
                "display": true
            },
            {
                "column_title": "Cases",
                "source_type": "operation",
                "source": "Transform.random_percentage_of_data",
                "operation_columns": {
                    "array": {
                        "column_name": "Population",
                        "source": "country_source"
                    }
                },
                "args": {
                    "min": 5,
                    "max": 15,
                    "integer": true
                },
                "display": true
            },
            {
                "column_title": "Recovered",
                "source_type": "operation",
                "source": "Transform.random_percentage_of_data",
                "operation_columns": {
                    "array": {
                        "dependee_title": "Cases",
                        "from_generated": true
                    }
                },
                "args": {
                    "min": 90,
                    "max": 95,
                    "integer": true
                },
                "display": true
            },
            {
                "column_title": "Deaths",
                "source_type": "operation",
                "source": "Transform.random_percentage_of_data",
                "operation_columns": {
                    "array": {
                        "dependee_title": "Cases",
                        "from_generated": true
                    }
                },
                "args": {
                    "min": 3,
                    "max": 4,
                    "integer": true
                },
                "display": true
            },
            {
                "column_title": "recovery_plus_deaths",
                "source_type": "operation",
                "source": "Maths.add",
                "operation_columns": {
                    "first": {
                        "dependee_title": "Recovered",
                        "from_generated": true
                    },
                    "second": {
                        "dependee_title": "Deaths",
                        "from_generated": true
                    }
                },
                "display": false
            },
            {
                "column_title": "Recovering",
                "source_type": "operation",
                "source": "Maths.subtract",
                "operation_columns": {
                    "first": {
                        "dependee_title": "Cases",
                        "from_generated": true
                    },
                    "second": {
                        "dependee_title": "recovery_plus_deaths",
                        "from_generated": true
                    }
                },
                "display": true
            }
        ]
    }
    ```
    In this stream, we are:  
    * fetching the data from the file "population_data.csv"
    * Generating `Cases` column as 5% to 15% of `Population`
    * Generating `Recovered` column as 90% to 95% of `Cases`
    * Generating `Deaths` column as 4% to 5% of `Cases`
    * Generating `recovery_plus_deaths` column as sum of `Recovered` and `Deaths` columns but setting display as `false` since this column is not required in the output
    * Generating `Recovering` column as subtraction of `recovery_plus_deaths` from `Cases` column
    * Saving the data to `local` CloudTDMS server
3. Activate the stream and wait for the data to be generated inside the `Data Generated` tab of CloudTDMS web portal.