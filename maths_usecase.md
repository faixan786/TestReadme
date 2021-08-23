# Use Cases
* [Maths Use-Case](#maths-use-case)
    * [visitors-daily](#visitors-daily)
    * [visitors-monthly](#visitors-monthly)
    * [visitors-weekly](#visitors-weekly)
 
Go to your CloudTDMS web portal:

#### Set up stream
Let's create streams  which will generate daily, weekly and monthly statistics of visitors  and  then upload the resulting statistics inside  `Data generated` Tab on CloudTDMS web portal...
Statistics of `Visitors` is a gaussian graph.

#### visitors-daily
In this example, we will generate visitors daily statistics. 


1. Create a stream with the following content:  
    ```
    {
        "stream": "visitors_daily",
        "records": 133,
        "destinations": [
            {
                "location": "local",
                "args": {
                    "filename": "visitors_daily.csv",
                    "format": "csv"
                }
            }
        ],
        "schema": [
            {
                "column_name": "id",
                "column_title": "id",
                "source_type": "synthetic",
                "source": "Numbers.auto_increment",
                "display": true
            },
            {
                "column_name": "date",
                "column_title": "Date",
                "source_type": "synthetic",
                "source": "Numbers.date",
                "display": true
            },
            {
                "column_name": "time",
                "column_title": "Time",
                "source_type": "synthetic",
                "source": "Numbers.incremental_date",
                "args": {
                           "start": {"hour":9},
                           "interval": {"minutes": 5},
                          "format": "%r"
                },
                "display": true
            },
            {
                "column_name": "visitors",
                "column_title": "Visitors",
                "source_type": "synthetic",
                "source": "Numbers.gaussian_distribution",
                     "args": {
                           "mean": 50,
                           "std_deviation":2
                },
                "display": true
            }
        ]
    }
    ```
      In this stream, we are:  
    * Making `id`, `Date`, `Time`,`Visitors` columns synthetic
    * Generating `Visitors` after a duration of 5 minutes
    * Saving the data to `local` CloudTDMS server

2. Activate  the stream and wait for the data to be generated inside  `Data generated` Tab on CloudTDMS web portal...





#### visitors-weekly
In this example, we will generate visitors weeky statistics. 
1. Create a stream with the following content:
    ```
    {
        "stream": "visitors_weekly",
        "records": 23,
        "destinations": [
            {
                "location": "local",
                "args": {
                    "filename": "visitors_weekly.csv",
                    "format": "csv"
                }
            }
        ],
        "schema": [
            {
                "column_name": "id",
                "column_title": "id",
                "source_type": "synthetic",
                "source": "Numbers.auto_increment",
                "display": true
            },
            {
                "column_name": "date",
                "column_title": "Date",
                "source_type": "synthetic",
                "source": "Numbers.date",
                "display": true
            },
            {
                "column_name": "time",
                "column_title": "Time",
                "source_type": "synthetic",
                "source": "Numbers.incremental_date",
                "args": {
                           "start": {"hour":9},
                           "interval": {"minutes": 30},
                          "format": "%r"
                },
                "display": true
            },
            {
                "column_name": "visitors",
                "column_title": "Visitors",
                "source_type": "synthetic",
                "source": "Numbers.gaussian_distribution",
                 "args": {
                           "mean": 30,
                           "std_deviation":3
                },
    
                "display": true
            }
        ]
    }
    ```
     In this stream, we are:  
    * Making `id`, `Date`, `Time`,`Visitors` columns synthetic
    * Generating `Visitors` after a duration of 30 minutes
    * Saving the data to `local` CloudTDMS server

2. Activate the stream and wait for the data to be generated inside  `Data generated` Tab on CloudTDMS web portal...

#### visitors-monthly
In this example, we will generate visitors monthly statistics. 
1. Create a stream with the following content:
    ```
    {
        "stream": "visitors_monthly",
        "records": 12,
        "destinations": [
            {
                "location": "local",
                "args": {
                    "filename": "visitors_monthly.csv",
                    "format": "csv"
                }
            }
        ],
        "schema": [
            {
                "column_name": "id",
                "column_title": "id",
                "source_type": "synthetic",
                "source": "Numbers.auto_increment",
                "display": true
            },
            {
                "column_name": "date",
                "column_title": "Date",
                "source_type": "synthetic",
                "source": "Numbers.date",
                "display": true
            },
            {
                "column_name": "time",
                "column_title": "Time",
                "source_type": "synthetic",
                "source": "Numbers.incremental_date",
                "args": {
                           "start": {"hour":9},
                           "interval": {"hours": 1},
                          "format": "%r"
                },
                "display": true
            },
            {
                "column_name": "visitors",
                "column_title": "Visitors",
                "source_type": "synthetic",
                "source": "Numbers.gaussian_distribution",
                 "args": {
                           "mean": 50,
                           "std_deviation":2
                },
    
                "display": true
            }
        ]
    }
    ```
     In this stream, we are:  
    * Making `id`, `Date`, `Time`,`Visitors` columns synthetic
    * Generating `Visitors` after a duration of an hour
    * Saving the data to `local` CloudTDMS server

2. Activate  the stream and wait for the data to be generated inside  `Data generated` Tab on CloudTDMS web portal...





