# Install the following modules
 
- Install unixODBC

```sh
sudo apt-get install unixodbc-dev
```

- Install the unixODBC driver

```sh
sudo apt-get install libmyodbc
```

- Configure odbc.ini and odbcinst.ini
  * /etc/odbc.ini for MySQL

```sh
[freeswitch]
Driver = /usr/lib64/odbc/libmyodbc.so
SERVER = localhost
PORT = 3306
DATABASE = freeswitch 
OPTION = 67108864
USER = root 
PASSWORD = password
```

 * /etc/odbcinst.ini for MySQL

```sh
[MySQL]
Description = MySQL driver
Driver = /usr/lib64/odbc/libmyodbc.so
Setup = /usr/lib64/odbc/libodbcmyS.so
UsageCount = 1
FileUsage = 1
Threading = 0
``` 

- Now we need to test if you set-up ODBC correctly run the command below in CLI

```sh
isql -v freeswitch
```

- If the above command gives below output then the ODBC is setup correctly
```sh
+---------------------------------------+
| Connected!                            |
|                                       |
| sql-statement                         |
| help [tablename]                      |
| quit                                  |
|                                       |
+---------------------------------------+
SQL>
```
- Adding mod_odbc_cdr in modules.conf.xml
```sh
    <load module="mod_odbc_cdr"/>
```

- Add below code in odbc_cdr.conf.xml
```sh
<configuration name="odbc_cdr.conf" description="ODBC CDR Configuration">
  <settings>
    <!-- <param name="odbc-dsn" value="database:username:password"/> -->
 <param name="odbc-dsn" value="odbc://databasename:username:passowrd"/>
        <!-- global value can be "a-leg", "b-leg", "both" (default is "both") -->
        <param name="log-leg" value="both"/>
    <!-- value can be "always", "never", "on-db-fail" -->
    <param name="write-csv" value="on-db-fail"/>
        <!-- location to store csv copy of CDR -->
    <param name="csv-path" value="/usr/local/freeswitch/log/odbc_cdr"/>
    <!-- if "csv-path-on-fail" is set, failed INSERTs will be placed here as CSV files otherwise they will be placed in "csv-path" -->
    <param name="csv-path-on-fail" value="/usr/local/freeswitch/log/odbc_cdr/failed"/>
    <!-- dump SQL statement after leg ends -->
        <param name="debug-sql" value="true"/>
  </settings>
  <tables>
        <!-- only a-legs will be inserted into this table -->
    <table name="cdr_table_a_leg" log-leg="a-leg">
      <field name="CallId" chan-var-name="call_uuid"/>
      <field name="orig_id" chan-var-name="uuid"/>
      <field name="term_id" chan-var-name="sip_call_id"/>
      <field name="ClientId" chan-var-name="uuid"/>
      <field name="IP" chan-var-name="sip_network_ip"/>
      <field name="IPInternal" chan-var-name="sip_via_host"/>
      <field name="CODEC" chan-var-name="read_codec"/>
      <field name="directGateway" chan-var-name="sip_req_host"/>
      <field name="redirectGateway" chan-var-name="sip_redirect_contact_host_0"/>
      <field name="CallerID" chan-var-name="sip_from_user"/>
      <field name="TelNumber" chan-var-name="sip_req_user"/>
      <field name="TelNumberFull" chan-var-name="sip_to_user"/>
      <field name="sip_endpoint_disposition" chan-var-name="endpoint_disposition"/>
      <field name="sip_current_application" chan-var-name="current_application"/>
    </table>
        <!-- only b-legs will be inserted into this table -->
    <table name="cdr_table_b_leg" log-leg="b-leg">
      <field name="CallId" chan-var-name="call_uuid"/>
      <field name="orig_id" chan-var-name="uuid"/>
      <field name="term_id" chan-var-name="sip_call_id"/>
      <field name="ClientId" chan-var-name="uuid"/>
      <field name="IP" chan-var-name="sip_network_ip"/>
      <field name="IPInternal" chan-var-name="sip_via_host"/>
      <field name="CODEC" chan-var-name="read_codec"/>
      <field name="directGateway" chan-var-name="sip_req_host"/>
      <field name="redirectGateway" chan-var-name="sip_redirect_contact_host_0"/>
      <field name="CallerID" chan-var-name="sip_from_user"/>
      <field name="TelNumber" chan-var-name="sip_req_user"/>
      <field name="TelNumberFull" chan-var-name="sip_to_user"/>
      <field name="sip_endpoint_disposition" chan-var-name="endpoint_disposition"/>
      <field name="sip_current_application" chan-var-name="current_application"/>
    </table>
        <!-- both legs will be inserted into this table -->
    <table name="cdr_table_both">
      <field name="CallId" chan-var-name="uuid"/>
      <field name="orig_id" chan-var-name="Caller-Unique-ID"/>
      <field name="TEST_id" chan-var-name="sip_from_uri"/>
    </table>
  </tables>
</configuration>
```

- Create the following tables

```sh
CREATE TABLE IF NOT EXISTS `cdr_table_a_leg` (
`CallId` varchar(80) DEFAULT NULL,
`orig_id` varchar(80) DEFAULT NULL,
`term_id` varchar(80) DEFAULT NULL,
`ClientId` varchar(80) DEFAULT NULL,
`IP` varchar(80) DEFAULT NULL,
`IPInternal` varchar(80) DEFAULT NULL,
`CODEC` varchar(80) DEFAULT NULL,
`directGateway` varchar(80) DEFAULT NULL,
`redirectGateway` varchar(80) DEFAULT NULL,
`CallerID` varchar(80) DEFAULT NULL,
`TelNumber` varchar(80) DEFAULT NULL,
`TelNumberFull` varchar(80) DEFAULT NULL,
`sip_endpoint_disposition` varchar(80) DEFAULT NULL,
`sip_current_application` varchar(80) DEFAULT NULL
);

CREATE TABLE IF NOT EXISTS `cdr_table_b_leg` (
`CallId` varchar(80) DEFAULT NULL,
`orig_id` varchar(80) DEFAULT NULL,
`term_id` varchar(80) DEFAULT NULL,
`ClientId` varchar(80) DEFAULT NULL,
`IP` varchar(80) DEFAULT NULL,
`IPInternal` varchar(80) DEFAULT NULL,
`CODEC` varchar(80) DEFAULT NULL,
`directGateway` varchar(80) DEFAULT NULL,
`redirectGateway` varchar(80) DEFAULT NULL,
`CallerID` varchar(80) DEFAULT NULL,
`TelNumber` varchar(80) DEFAULT NULL,
`TelNumberFull` varchar(80) DEFAULT NULL,
`sip_endpoint_disposition` varchar(80) DEFAULT NULL,
`sip_current_application` varchar(80) DEFAULT NULL
);

CREATE TABLE IF NOT EXISTS `cdr_table_both` (
`CallId` varchar(80) DEFAULT NULL,
`orig_id` varchar(80) DEFAULT NULL,
`TEST_id` varchar(80) DEFAULT NULL
);
```

- Restart the freeswitch and check for odbc module exist or not using below command in CLI

```
module_exists mod_odbc_cdr
```
- If all worked correctly you will see the below output

```
freeswitch@internal> module_exists mod_odbc_cdr
true
freeswitch@internal>
```

- In case if cdr logs are not insrted then check the below path

```
/usr/local/freeswitch/log/freeswitch.log
```
# USEFUL LINKS

- http://lists.freeswitch.org/pipermail/freeswitch-users/2008-December/037049.html
- [CDR REPORTING](http://www.cdr-stats.org/documentation/beginners-guide/howto-installing-on-freeswitch/)
- [CDR RECORD INTO MYSQL] (http://saranamguru.blogspot.com/2017/07/freeswitch-cdr-store-into-mysql-on.html)