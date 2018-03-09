# Oracle-12.2-vagrant
A vagrant box that provisions Oracle Database 12c Release 2 automatically, using Vagrant, the latest Oracle Linux 7 box and a shell script.

## Prerequisites
1. Install [Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads)
2. Install [Vagrant](https://vagrantup.com/)

## Getting started
1. Copy Oracle-vagrant to folder C:/Users/{username}/oracle-vagrant
2. Change into version folder (12.2.0.1)
3. First time only (see #5): Download the Oracle Database 12c Release 2 binaries linuxx64_12201_database.zip
from [http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)
4. Run `vagrant up`
5. The first time you run this it will provision everything and may take a while. Ensure you have a good internet connection!
6. Connect to the database.
7. You can shut down the box via the usual `vagrant halt` and the start it up again via `vagrant up`.

## Connecting to Oracle
* Hostname: `localhost`
* Port: `1521`
* SID: `ORCLCDB`
* PDB: `ORCLPDB1`
* OEM port: `5500`
* All passwords are `password`

## Other info

* If you need to, you can connect to the machine via `vagrant ssh`.
* You can `sudo su - oracle` to switch to the oracle user.
* The Oracle installation path is `/opt/oracle/` by default.
* On the guest OS, the directory `/vagrant` is a shared folder and maps to wherever you have this file checked out.

### Customization
You can customize your Oracle environment by amending the environment variables in the `Vagrantfile` file.
The following can be customized:
* `ORACLE_BASE`: `/opt/oracle/`
* `ORACLE_HOME`: `/opt/oracle/product/12.2.0.1/dbhome_1`
* `ORACLE_SID`: `ORCLCDB`
* `ORACLE_PDB`: `ORCLPDB1`
* `ORACLE_CHARACTERSET`: `AL32UTF8`

# PHP client lib Oracle

Install Oracle Instant Client

- Download the Oracle Instant Client for Microsoft Windows (32-bit) v.12<http://www.oracle.com/technetwork/topics/winsoft-085727.html>
- Unzip file: `instantclient-basiclite-nt-12.2.0.1.0.zip`
- Enable php extension in php.ini: `extension=php_oci8_12c.dll`
- Restart Apache

You will download a zip file, which need to unzip to a certain directory, which indicated by XAMPP phpinfo. Like below, you need unzip the instant client into path=c:\php-sdk\oracle\x86\instantclient_12_1

- Set Windows system PATH

The last step, you have to set path c:\php-sdk\oracle\x86\instantclient_12_1 into Windows system PATH environment variable, so PHP can access all these .dll files.

You can use command line to do this. But it may distroy other settings. So here is a safeway to do that (Window ):

1. click the Start button, right-click the Computer option in the Start menu, and select Properties.
2. Click the Advanced System Settings link in the left column.
3. In the System Properties window, click on the Advanced tab, then click the Environment Variables button near the bottom of that tab.
4. In the Environment Variables window (pictured below), highlight the Path variable in the “System variables” section and click the Edit button. Add or modify the path lines with the paths you want the computer to access. Each different directory is separated with a semicolon.

**Note**: you need admin permission to do PATH change.

Now, you have complete the Oracle extension installed on your computer

Test connect oracle

~~~php
<?php
 
error_reporting(E_ALL);
ini_set('display_errors', 'On');
 
$host = 'localhost';
$port = '1521';

// Oracle service name (instance)
$db_name     = 'ORCLPDB1';
$db_username = "system";
$db_password = "password";


if ( @fsockopen($host,$port) ) {
    //connect to database
	echo "OK".PHP_EOL;
} else {
    // didn't work
	echo "NG".PHP_EOL;
}
 
if (function_exists("oci_connect")) {
    echo "oci_connect found\n";
} else {
    echo "oci_connect not found\n";
    exit;
}


$tns = "(DESCRIPTION =
	(CONNECT_TIMEOUT=3)(RETRY_COUNT=0)
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = $host)(PORT = $port))
    )
    (CONNECT_DATA =
      (SERVICE_NAME = $db_name)
    )
  )";
$tns = "$host:$port/$db_name";

try {
    $conn = oci_connect($db_username, $db_password, $tns);
    if (!$conn) {
        $e = oci_error();
        throw new Exception($e['message']);
    }
    echo "Connection OK\n";
    
    $stid = oci_parse($conn, 'SELECT * FROM ALL_TABLES');
    
    if (!$stid) {
        $e = oci_error($conn);
        throw new Exception($e['message']);
    }
    // Perform the logic of the query
    $r = oci_execute($stid);
    if (!$r) {
        $e = oci_error($stid);
        throw new Exception($e['message']);
    }
    
    // Fetch the results of the query
    while ($row = oci_fetch_array($stid, OCI_ASSOC + OCI_RETURN_NULLS)) {
        $row = array_change_key_case($row, CASE_LOWER);
        print_r($row);
        break;
    }
    
    // Close statement
    oci_free_statement($stid);
    
    // Disconnect
    oci_close($conn);
    
}
catch (Exception $e) {
    print_r($e);
}
~~~

## Known issues



None.
