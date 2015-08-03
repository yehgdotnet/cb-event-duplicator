# Event extractor/duplicator for Carbon Black

Extract events from one Carbon Black server and send them to another server - useful for demo/testing purposes

## Overview & Requirements

This script is in **BETA** testing and requires some manual setup. You can run this on a Cb server locally or any other
platform that has Python 2.6+ and the `psycopg2` package (used to talk to the PostgreSQL server; already installed
on a Cb server).

Optionally, you can also install the `paramiko` package to enable SSH support - allowing you to use a remote Cb server
as a source or destination for extracted event data.

## Installation

This is a pip-installable package. You can install it via:

```
python setup.py install
```

Once the package is installed, you will have a new script in your $PATH: `cb-event-duplicator`.

## Usage

Command line usage:

```
usage: cb-event-duplicator [-h] [-v] [--key KEY] [--anonymize] [-q QUERY]
                           [--tree]
                           source destination

Transfer data from one Cb server to another

positional arguments:
  source                Data source - can be a pathname (/tmp/blah), a URL
                        referencing a zip package
                        (http://my.server.com/package.zip), the local Cb
                        server (local), or a remote Cb server
                        (root@cb5.server:2202)
  destination           Data destination - can be a filepath (/tmp/blah), the
                        local Cb server (local), or a remote Cb server
                        (root@cb5.server:2202)

optional arguments:
  -h, --help            show this help message and exit
  -v, --verbose         Increase output verbosity
  --key KEY             SSH private key location
  --anonymize           Anonymize data in transport
  -q QUERY, --query QUERY
                        Source data query (required for server input)
  --tree                Traverse up and down process tree
```

Examples:

* `cb-event-duplicator http://server.com/package.zip local`

  Will import the events from the zip file located at http://server.com/package.zip into your local Cb server.
  The zip file is simply a packaged version of the directory tree created by this tool.

* `cb-event-duplicator --tree -q "process_name:googleupdate.exe" —anonymize root@172.22.10.7 /tmp/blah`

  Takes all processes that match “process_name:googleupdate.exe” and their parents/children and saves them all to `/tmp/blah`

* `cb-event-duplicator --tree -q "process_name:googleupdate.exe" —anonymize root@172.22.10.7 root@172.22.5.118`

  Same as above, just copies directly to the 172.22.5.118 server instead of saving the files to the local disk
