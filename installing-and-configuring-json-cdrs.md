# Installation and configuration of JSON based CDR on call end


### Installation

First, we need to enable/uncomment the event_handlers/mod_json_cdr line from the freeswitch source installation

```sh
$ vi /usr/local/src/freeswitch/modules.conf
```
Uncomment the following line so that it looks like this

```sh
event_handlers/mod_json_cdr
```
Save the file and proceed with the following commands

```sh
$ cd /usr/local/src/freeswitch
$ ./configure
$ make && make install
```

Next step is to configure our POST URL to receive the JSON from FreeSwitch

Create the following file inside the autoload_configs folder

```sh
$ vi /usr/local/freeswitch/conf/autoload_configs/json_cdr.conf.xml
```
Paste the following

```sh
  <configuration name="json_cdr.conf" description="JSON CDR">
    <settings>

      <!-- Global parameters -->
      <param name="log-b-leg" value="true"/>
      <param name="prefix-a-leg" value="false"/>

      <!-- Whether to URL encode the individual JSON values. Defaults to true, set to false for standard JSON. -->
      <param name="encode-values" value="true"/>

      <!-- Normally if url and log-dir are present, url is attempted first and log-dir second. This options allows to do both systematically. -->
      <param name="log-http-and-disk" value="false"/>

      <!-- File logging -->
      <!-- Directory where to create the "json_cdr" directory used to store JSON CDRs. Leave empty for no file logging. -->
      <!-- Might be overriden by a channel variable "json_cdr_base". -->
      <param name="log-dir" value=""/>
      <!-- Whether to rotate file CDRs. -->
      <param name="rotate" value="false"/>

      <!-- HTTP(S) logging -->
      <!-- URL where to POST JSON CDRs. Leave empty for no URL logging. Up to 20 URLs may be specified. -->
      <param name="url" value="<<YOUR POST URL>>"/>
      <!-- Authentication scheme for the above URL. May be one of basic|digest|NTLM|GSS-NEGOTIATE|any-->
      <!--<param name="auth-scheme" value="basic"/>-->
      <!-- Credentials in the form  username:password  if auth-scheme is used. Leave empty for no authentication. -->
      <!--<param name="cred" value="string"/>-->
      <!-- Whether to base64 encode the entire JSON document before POSTing it. -->
      <!--<param name="encode" value="base64|true|false"/>-->
      <!-- Number of retries in case of failure. Each specified URL is tried in turn. -->
    </settings>
  </configuration>
```
Save the file, and proceed with the following

Add the following entry in modules.conf.xml

```sh
$ vi /usr/local/freeswitch/conf/autoload_configs/modules.conf.xml

<load module="mod_json_cdr"/>
```

Restart FreeSwitch for changes to take effect.

### Configuration

The only configuration change[s] needs to be made inside json_cdr.conf.xml, where we change our POST URL.

We need to add/replace the line to configure our URL as follows

```sh
$ vi /usr/local/freeswitch/conf/autoload_configs/json_cdr.conf.xml

<param name="url" value="http://localhost:8000/"/>
```

We may now start our http server at http://localhost:8000/ and receive CDRs of all calls in JSON Format