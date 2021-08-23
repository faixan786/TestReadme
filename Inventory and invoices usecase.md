# Use Cases

* [Inventory and Invoices](#Inventory and Invoices)
    * [Prerequisites](#prerequisites)
    * [Steps to install MySQL](#steps-to-install-mysql)
    * [Upload Mock Data](#upload-mock-data)
    * [Set up stream](#set-up-stream)


## Inventory and Invoices
In this example, we will be setting up "**MySQL**" on a machine and then get data from database of that  machine and finally after  some processing on the obtained data we will save the processed data to the `local` CloudTDMS server.

We will assume you have an "**ubuntu / debian**" machine up and running.

#### Prerequisites
You will need `sudo` privileges for the following steps.

#### Steps to install MySQL:  
You need to install **MySQL** on the machine.  
1. To install it, update the package index on your server with apt:
    ```
    sudo apt update
    ```
2. Then install the default package:
    ```
    sudo apt install mysql-server
    ```
3. Open the mysql shell:
    ```
    sudo mysql
    ```
4. Now in the MySQL shell, let's create a new user:
    ```
    mysql> CREATE USER 'tdms_user'@'%' IDENTIFIED BY 'yoursupersecretpassword';
    ```
5. Now log in to MySQL shell as the new user:
    ```
    mysql -u tdms_user -p
    ```
6. Create a new database:  
    ```
    mysql> CREATE DATABASE mydb;
    ```
5. Allow remote connections to your MySQL server:
    * Open the file located at `/etc/mysql/mysql.conf.d/mysqld.cnf`
    * Change the following lines:
        ```
        bind-address = 127.0.0.1
        mysqlx-bind-address = 127.0.0.1
        ``` 
        to  
        ```
        bind-address = 0.0.0.0
        mysqlx-bind-address = 0.0.0.0
        ```
    * Restart mysql service:
        ```
        sudo service mysql restart
        ```

#### Upload mock data
Assuming we have no data on the machine, let's upload some data to the machine.  
Go to your CloudTDMS web portal:
1. In your connections, add mysql connection details of your machine, e.g.,
    ```
    {
        ...
        "conn_machine": {
            "hostname": "x.x.x.x",
            "database": "mydb",
            "port": 3306,
            "username": "tdms_user",
            "password": "xxxxxx",
        }
        ...
    }
    ```
    **Note: The password to be used in the above snippet must be base64 encoded.**  
2. Now create a stream with the following content:  
    ```
    {
    "stream": "mysql_upload",
    "records": 5,
    "destinations": 
        {
            "location": "mysql",
            "connection": "conn_machine",
            "args": {
                "table": "products",
                "append_to_existing": true
            }
        }
    ],
    "schema": [
   
        {
            "column_name": "ID",
            "column_title": "ID",
            "source_type": "synthetic",
            "source": "Numbers.auto_increment",
            "display": true
        },
        {
            "column_name": "Name",
            "column_title": "Name",
            "source_type": "synthetic",
            "source": "Products.car_make",
            "display": true
        },
        {
            "column_name": "Description",
            "column_title": "Description",
            "source_type": "synthetic",
            "source": "English.sentence",
            "display": true
        },
        {
            "column_name": "Price",
            "column_title": "Price",
            "source_type": "synthetic",
            "source": "Numbers.random_numbers",
            "args": {
                "start": 50,
                "end": 100
            },
            "display": true
        },
        {
            "column_name": "VAT",
            "column_title": "VAT",
            "source_type": "synthetic",
            "source": "Numbers.random_numbers",
            "args": {
                "start": 5,
                "end": 10
            },
            "display": true
        }
    ]
   }
   ```
      In this stream, we are: 
    * Generating `Products` after one or every month adding about 5 products and is treated as referance data for invoices.
    * fetching the data from one machine with connection name "conn_machine"
    * making `id`, `Quantity`,`Divisor` columns synthetic
    * Fetching `ID`, `Name` ,`Description`,`Price`,`VAT` columns from source 
    * Fetching `Total_price`, `Total_price_multiply_VAT`, `Price_with_VAT` columns from source and then performing multiplication  operation on`Total_price`, `Total_price_multiply_VAT`columns and `float division` operation  on `Price_with_VAT` column respectively.
    * 
    * uploading the data to the `local` CloudTDMS server
    * Saving the data to `local` CloudTDMS server
  
3. Activate the stream and wait for the data to be generated inside your MySQL database.
4. To check the first 10 rows of data in your database:
    ```
    mysql> USE DATABASE mydb;
    ```
    ```
    mysql> SELECT * FROM companies LIMIT 10;
    ```

#### Set up stream
Let's set up the stream which will fetch the data from the database (on which we uploaded the mock data) and transform it and then upload the resulting data on the `local` CloudTDMS server.

1. Create a new stream with the following content:
    ```
    {

    "stream": "Invoices",
    "records": 5,
    "sources": [
        {
            "location": "mysql",
            "connection":"conn_machine",
            "files": [
                {
                    "table": "products",
                    "alias": "prod_table",
                    "randomize": true
                }
            ]
        }
    ],
    "destinations": [
        {
            "location": "local",
            "args": {
                "filename": "My-Invoices",
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
            "display": false
        },
        {
            "column_name": "ID",
            "column_title": "Product_id",
            "source_type": "source",
            "source": "prod_table",
            "display": true
        },
        {
            "column_name": "Name",
            "column_title": "product_name",
            "source_type": "source",
            "source": "prod_table",
            "display": true
        },
        {
            "column_name": "Description",
            "column_title": "product_description",
            "source_type": "source",
            "source": "prod_table",
            "display": true
        },
        {
            "column_name": "Price",
            "column_title": "product_price",
            "source_type": "source",
            "source": "prod_table",
            "display": true
        },
        {
            "column_name": "VAT",
            "column_title": "VAT",
            "source_type": "source",
            "source": "prod_table",
            "display": true
        },
        {
            "column_title": "Quantity",
            "source_type": "synthetic",
            "source": "Numbers.random_numbers",
            "args": {
                "start": 1,
                "end": 10
            },
            "display": true
        },
        {
            "column_title": "Total_price",
            "source_type": "operation",
            "source": "Maths.multiply",
            "operation_columns": {
                "first": {
                    "dependee_title": "product_price",
                    "from_generated": true
                },
                "second": {
                    "dependee_title": "Quantity",
                    "from_generated": true
                }
            },
            "display": true
        },
        {
            "column_title": "Total_price_multiply_VAT",
            "source_type": "operation",
            "source": "Maths.multiply",
            "operation_columns": {
                "first": {
                    "dependee_title": "Total_price",
                    "from_generated": true
                },
                "second": {
                    "dependee_title": "VAT",
                    "from_generated": true
                }
            },
            "display": false
        },
        {
            "column_name": "Divisor",
            "column_title": "Divisor",
            "source_type": "synthetic",
            "source": "Numbers.random_numbers",
            "args": {
                "start": 100,
                "end": 100
            },
            "display": false
        },
        {
            "column_title": "Price_with_VAT",
            "source_type": "operation",
            "source": "Maths.float_divide",
            "operation_columns": {
                "first": {
                    "dependee_title": "Total_price_multiply_VAT",
                    "from_generated": true
                },
                "second": {
                    "dependee_title": "Divisor",
                    "from_generated": true
                }
            },
            "display": true
        }
    ]
   }
    ```
    
    In this stream, we are:  
    * fetching the data from one machine with connection name "conn_machine"
    * making `id`, `Quantity`,`Divisor` columns synthetic
    * Fetching `ID`, `Name` ,`Description`,`Price`,`VAT` columns from source 
    * Fetching `Total_price`, `Total_price_multiply_VAT`, `Price_with_VAT` columns from source and then performing multiplication  operation on`Total_price`, `Total_price_multiply_VAT`columns and `float division` operation  on `Price_with_VAT` column respectively.
    * Generating `Total_price_multiply_VAT` column as multiplication of `Total_price` and `VAT` columns  but setting display as `false` since this column is not required in the output
    * Generating `Divisor` column synthetic  but setting display as `false` since this column is not required in the output
    * Generating `Price_with_VAT` column as float division of `Total_price_multiply_VAT` and `Divisor` column
    * Generating data after a duration of an hour generating upto 5 items in each run
    * Saving the data to `local` CloudTDMS server
2. Activate  the stream and wait for the data to be generated inside  `Data generated` Tab on CloudTDMS web portal...



