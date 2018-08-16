### Adding mod_curl to freeswitch

1. We need to comment out applications/mod_curl from /usr/local/src/freeswitch/modules.conf

```sh
$ vi /usr/local/src/freeswitch/modules.conf

#applications/mod_curl
applications/mod_curl
```
2. Change directory to /usr/local/src/freeswitch and run

```sh
$ make && make install
```

3. Uncomment mod_curl in /usr/local/freeswitch/conf/autoload_configs/modules.conf.xml

```sh
<load module="mod_curl"/>
```

4. Restart FreeSwitch

### Application: Sending caller id to HTTP APIs (Missed call service)

Configuring a dialplan to receive instructions when user dials extension 61998990 to hangup the call and send the caller id to the HTTP API.

1. Configuring a GET request using mod_curl
Add the following lines in /usr/local/freeswitch/conf/dialplan/default/example.xml

```sh
<extension name="missed_call">
    <condition field="destination_number" expression="^61998990$">
        <action application="curl" data="http://localhost:8000/hitAPI?msisdn=${caller_id_number}"/>
        <action application="hangup" />
    </condition>
</extension>
```
Save the file and enter fs_cli -x reloadxml to reload xml files. Next, dial 61998990 and Freeswitch will hit the configured URL with the caller id in the msisdn field