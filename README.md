---

##### Due to a recent changes, **StuffTracker** now depends on [UserManager](https://github.com/zidmar/UserManager). Please install and configure [UserManager](https://github.com/zidmar/UserManager) before using **StuffTracker**.

---

# StuffTracker

**Stuff Tracker** is a project that helps small groups to keep track of "stuff" in a central location, instead of using spreadsheets.

* [Installation instructions for Debian](#installation)
* [Production web server installation](#production)
* [Using the import script](#import)

<a name="installation"/>
## Installation instructions for Debian

1.) Install necessary packages as the root user

```sh
apt-get install git sqlite3 libdancer2-perl libdbi-perl libdbd-sqlite3-perl libjson-xs-perl 
```

2.) Create the **starman** user where programs will reside

```sh
adduser --gecos '' --disabled-password starman
```

3.) Log in to the **starman** user

```sh
su - starman
```

4.) Download the program using git

```sh
git clone --recursive https://github.com/zidmar/StuffTracker
```

5.) Create a directory where the database files will reside

```sh
mkdir /home/starman/db
```

6.) Create the database for the program

```sh
sqlite3 /home/starman/db/StuffTracker.db < /home/starman/StuffTracker/sql/stuff_tracker-sqlite.sql
```

7.) Test the program by starting a test web server, available at http://localhost:5000

```sh
cd /home/starman/StuffTracker
plackup -p 5000 bin/app.psgi
```

8.) After verifying the web app loads correctly, stop the test web server with **Ctrl-C**

<a name="production"/>
## Production web server installation

1.) As root user, install the following packages

```sh
apt-get install apache2 libapache2-mod-proxy-html libxml2-dev libdaemon-control-perl starman
```

2.) Enable web server modules

```sh
a2enmod proxy proxy_http
```

3.) Copy web server configuration file

```sh
cp /home/starman/StuffTracker/scripts/debian/apache.conf /etc/apache2/conf-enabled/StuffTracker.conf
```

4.) Give execute permissions to the program startup script and create a logical link to start the program (as a service) at boot

```sh
chmod +x /home/starman/StuffTracker/scripts/debian/startup.pl

ln -s /home/starman/StuffTracker/scripts/debian/startup.pl /etc/init.d/StuffTracker
ln -s /home/starman/StuffTracker/scripts/debian/startup.pl /etc/rc0.d/K20StuffTracker
ln -s /home/starman/StuffTracker/scripts/debian/startup.pl /etc/rc1.d/K20StuffTracker
ln -s /home/starman/StuffTracker/scripts/debian/startup.pl /etc/rc2.d/S20StuffTracker
ln -s /home/starman/StuffTracker/scripts/debian/startup.pl /etc/rc3.d/S20StuffTracker
ln -s /home/starman/StuffTracker/scripts/debian/startup.pl /etc/rc4.d/S20StuffTracker
ln -s /home/starman/StuffTracker/scripts/debian/startup.pl /etc/rc5.d/S20StuffTracker
ln -s /home/starman/StuffTracker/scripts/debian/startup.pl /etc/rc6.d/K20StuffTracker

```

5.) Start the program as a service and the production web server

```sh
/etc/init.d/StuffTracker start
/etc/init.d/apache2 restart
```

6.) Access the website, using the production server, on http://localhost/StuffTracker

<a name="import"/>
## Using the import script

1.) As root user, install the following package

```sh
apt-get install libtext-csv-perl
```

2.) The script currently has limitations and data normalization/standarization should be done to the file before using script

* Only supports the CSV format.
* The first line of the file should hold the column names and each name should match an already defined column in StuffTracker.
* The data fields should match the column type
  * integer (examples: 200 1,345)
  * date (examples: 1-Feb-2016)
  * select (field data should reflect already defined Pulldown entries)

3.) Sample Columns and Data

* Pre-defined Select Column in StuffTracker

| Sample Select Column |
| -------------------- |
| Select Data 1        |
| Select Data 2        |
| Select Data 3        |

* Sample Data in spreadsheet

| Sample Text Column | Sample Date Column | Sample Select Column |
| ------------------ | :----------------: | -------------------- |
| Text Data - One    | 1-Feb-2016         | Select Data 1        |
| Text Data - Two    | 1-May-2016         | Select Data 2        |
| Text Data - Three  | 1-Jul-2016         | Select Data 3        |

* Converted spreadsheet to CSV

```sh
Sample Text Column,Sample Date Column,Sample Select Column
Text Data - One,1-Feb-2016,Select Data 1
Text Data - Two,1-May-2016,Select Data 2
Text Data - Three,1-Jul-2016,Select Data 3
```

4.) Run script with CSV file as argument

```sh
cd /home/starman/StuffTracker/scripts

perl csv_import.pl /path/to/file.csv
```
