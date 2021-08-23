# Use Case - Data Protection

* [Data Protection](#data-protection)
    * [Prerequisites](#prerequisites)
    * [Steps to install MySQL](#steps-to-install-mysql)
    * [Upload Mock Data](#upload-mock-data)
    * [Set up stream](#set-up-stream)

## Data Protection
In this example, we will be setting up "**MySQL**" on two machines and then get data from database on one machine and then do some processing on the obtained data and finally save to the database on another machine.

We will assume you have two "**ubuntu / debian**" machines up and running.

#### Prerequisites
You will need `sudo` privileges for the following steps.

#### Steps to install MySQL:  
You need to install **MySQL** on both machines.  
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
    * Change the following line:
        ```
        bind-address = 127.0.0.1
        ``` 
        to  
        ```
        bind-address = 0.0.0.0
        ```
    * Restart mysql service:
        ```
        sudo service mysql restart
        ```

#### Upload mock data
Assuming we have no data on any of the machines, let's upload some data on one of the machines.  

Go to your CloudTDMS web portal:
1. In your connections, add mysql connection details of one of your machines, e.g.,
    ```
    {
        ...
        "conn_machine_1": {
            "hostname": "x.x.x.x",
            "database": "mydb",
            "port": 3306,
            "username": "tdms_user",
            "password": "xxxxxx",
        },
        "conn_machine_2": {
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
        "records": 100,
        "destinations": [
            {
                "location": "mysql",
                "connection": "conn_machine_1",
                "args": {
                    "table": "employees",
                    "append_to_existing": true
                }
            }
        ],
        "schema": [
            {
                "column_title": "id",
                "source_type": "synthetic",
                "source": "Numbers.auto_increment",
                "display": true
            },
            {
                "column_title": "fname",
                "source_type": "synthetic",
                "source": "Personal.first_name",
                "display": true
            },
            {
                "column_title": "lname",
                "source_type": "synthetic",
                "source": "Personal.last_name",
                "display": true
            },
            {
                "column_title": "dob",
                "source_type": "synthetic",
                "source": "Personal.date_of_birth",
                "display": true
            },
            {
                "column_title": "country",
                "source_type": "synthetic",
                "source": "Geo.country",
                "display": true
            },
            {
                "column_title": "city",
                "source_type": "synthetic",
                "source": "Geo.city",
                "display": true
            },
            {
                "column_title": "post_code",
                "source_type": "synthetic",
                "source": "Geo.postal_code",
                "display": true
            },
            {
                "column_title": "address",
                "source_type": "synthetic",
                "source": "Geo.street_address",
                "display": true
            },
            {
                "column_title": "salary",
                "source_type": "synthetic",
                "source": "Personal.salary",
                "display": true
            },
            {
                "column_title": "bonus",
                "source_type": "synthetic",
                "source": "Numbers.random_numbers",
                "args": {
                    "start": 1,
                    "end": 15
                },
                "display": true
            },
            {
                "column_title": "iban",
                "source_type": "synthetic",
                "source": "Bank.iban",
                "display": true
            }
        ]
    }
    ```
3. Activate the stream and wait for the data to be generated inside your MySQL database.
4. To check the first 10 rows of data in your database:
    ```
    mysql> USE DATABASE mydb;
    ```
    ```
    mysql> SELECT * FROM companies LIMIT 10;
    ```

#### Set up stream
Let's set up the stream which will fetch the data from one database (on which we uploaded the mock data) and transform it and then upload the resulting data on another database.

1. Create a new stream with the following content:
    ```
    {
        "stream": "Data_Protection",
        "records": 100,
        "sources": [
            {
                "location": "mysql",
                "connection": "conn_machine_1",
                "files": [
                    {
                        "table": "employees",
                        "alias": "mysql_data"
                    }
                ]
            }
        ],
        "destinations": [
            {
                "location": "mysql",
                "connection": "conn_machine_2",
                "args": {
                    "table": "employees_protected",
                    "append_to_existing": true
                }
            }
        ],
        "schema": [
            {
                "column_name": "id",
                "column_title": "id",
                "source_type": "source",
                "source": "mysql_data",
                "display": true
            },
            {
                "column_title": "fname",
                "source_type": "synthetic",
                "source": "Personal.first_name",
                "display": true
            },
            {
                "column_title": "lname",
                "source_type": "synthetic",
                "source": "Personal.last_name",
                "display": true
            },
            {
                "column_title": "dob",
                "source_type": "synthetic",
                "source": "Personal.date_of_birth",
                "display": true
            },
            {
                "column_name": "country",
                "column_title": "country",
                "source_type": "source",
                "source": "mysql_data",
                "display": true
            },
            {
                "column_name": "city",
                "column_title": "city",
                "source_type": "source",
                "source": "mysql_data",
                "display": true
            },
            {
                "column_name": "post_code",
                "column_title": "post_code",
                "source_type": "source",
                "source": "mysql_data",
                "display": true
            },
            {
                "column_name": "address",
                "column_title": "address",
                "source_type": "source",
                "source": "mysql_data",
                "display": true
            },
            {
                "column_title": "salary",
                "source_type": "synthetic",
                "source": "Personal.salary",
                "display": true
            },
            {
                "column_title": "bonus",
                "source_type": "synthetic",
                "source": "Numbers.random_numbers",
                "args": {
                    "start": 1,
                    "end": 15
                },
                "display": true
            },
            {
                "column_name": "iban",
                "column_title": "iban",
                "source_type": "source",
                "source": "mysql_data",
                "display": true
            }
        ],
        "masking": [
            {
                "mask_type": "Datamasking.shuffle",
                "columns": [
                    "country",
                    "city",
                    "post_code"
                ]
            },
            {
                "mask_type": "Datamasking.encrypt",
                "columns": [
                    "address"
                ]
            },
            {
                "mask_type": "Datamasking.mask",
                "columns": [
                    "iban"
                ],
                "args": {
                    "type": "suffix"
                }
            }
        ]
    }
    ```
    In this stream, we are:  
    * fetching the data from one machine with connection name "conn_machine_1"
    * masking the `iban` column
    * encrypting the `address` column
    * shuffling the `country`, `city` and `post_code` columns
    * making `dob`, `salary`, `bonus` columns synthetic
    * uploading the data to the machine with connection name "conn_machine_2"
2. Activate the stream and wait for the data to be generated inside your MySQL database.
3. To check the first 10 rows of your transformed data in your destination machine's database:
    ```
    mysql> USE DATABASE mydb;
    ```
    ```
    mysql> SELECT * FROM employees_protected LIMIT 10;
    ```