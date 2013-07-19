Python Integer Overflow MySQL Check(Check Max Value)
================================

*Purpose: Check for max values in all columns of integer types that have reached N pct of the maximum value for that type of integer.*

*Notes: This is a translation of https://github.com/palominodb/palominodb-priv/tree/master/tools/mysql/int-overflow-check*

Install Requirements
-------------------------------

Install MySQL-python required packages:

For Ubuntu:
```
sudo apt-get install python-dev libmysqlclient-dev
```

It is recommended that virtual environment is used when using the tool rather than updating system packages.

Install virtualenv if you don't have it yet:

For Ubuntu:
```
sudo apt-get install python-virtualenv
```

Create virtual environment:
```
$ virtualenv --no-site-packages ~/myenv
```

Activate virtual environment and install requirements:
```
$ source ~/myenv/bin/activate
(myenv)$ pip install -r requirements.txt
```
At this point you can now run pdb_check_maxvalue.py script.

Deactivate virtual environment when done working with it:
```
(myenv)$ deactivate
```

Running the Script
------------------

To run a pdb_check_maxvalue.py in a virtual environment, you can either:

    * Activate virtual environment and run script with Python:

        $ source ~/myenv/bin/activate
        (myenv)$ python pdb_check_maxvalue.py <args>
        (myenv)$ deactivate
        $

        or if pdb_check_maxvalue.py's first line is: #!/usr/bin/env python

        $ source ~/myenv/bin/activate
        (myenv)$ ./pdb_check_maxvalue.py <args>
        (myenv)$ deactivate
        $

    * Run using full path of virtual environment's version of Python:

        $ ~/myenv/bin/python pdb_check_maxvalue.py <args>


Usage
-----

Usage: pdb_check_maxvalue.py \[options\]

Sample Usage:
  `pdb_check_maxvalue.py -u root -p password -d db1,db2,db3 --critical 75 --warning 50`
  `pdb_check_maxvalue.py -C ./config_sample.yml`

```
Options:
  -e EXCLUDE_COLUMNS, --exclude-columns=EXCLUDE_COLUMNS
                        Specify columns to exclude in the following format:
                        schema1.table1=col1,col2,colN;schemaN.tableN=colN;...
  --display-row-count-max-ratio-columns
                        In separate section, display columns containing high
                        values compared to maximum for the column datatype,
                        but number of rows is less than the value of --row-
                        count-max-ratio.
  -i IGNORE_DBS, --ignore-dbs=IGNORE_DBS
                        A comma-separated list of db names to be ignored
  -d USE_DBS, --use-dbs=USE_DBS
                        A comma-separated list of db names to be inspected
  -P PORT, --port=PORT  The port to be used
  -T THREADS, --threads=THREADS
                        Number of threads to spawn
  -u USER, --user=USER  Database user
  --row-count-max-ratio=ROW_COUNT_MAX_RATIO
                        If table row count is less than this value, exclude
                        this column from display.
  -p PASSWORD, --password=PASSWORD
                        Database password
  -C CONFIG, --config=CONFIG
                        Configuration filename
  -v, --verbose
  -H HOSTNAME, --hostname=HOSTNAME
  -w WARNING, --warning=WARNING
  -c CRITICAL, --critical=CRITICAL
  -t TIMEOUT, --timeout=TIMEOUT
  -h, --help            show this help message and exit
```

  *When not in use, the Warning and Critical parameters are set to 100.*


Testing
-------------------------------
*Make sure that the default user has CREATE grant*

In the script directory,
`python -m unittest tests.test`


Logging
-------

Logging can be configured only via configuration file.
To quickly enable logging, add the following in your configuration file:
```
logging:
    version: 1
    disable_existing_loggers: False
    formatters:
        simple:
            format: '[%(asctime)s] {%(pathname)s:%(lineno)d} %(levelname)s - %(message)s'
        console:
            format: '[%(asctime)s] %(message)s'
    handlers:
        console:
            level: 'INFO'
            formatter: console
            class: logging.StreamHandler
        new_file:
            formatter: simple
            level: DEBUG
            class: logging.FileHandler
            filename: debug.log
            mode: w
            encoding: utf_8
            delay: True
    loggers:
        __main__:
            handlers: [new_file]
        pdb_check_maxvalue:
            handlers: [new_file]
```

There are many combinations of formatters and handlers.
If you need to customize the settings further, the following link will help you understand the details about the configuration dictionary schema:
http://docs.python.org/2/library/logging.config.html#logging-config-dictschema