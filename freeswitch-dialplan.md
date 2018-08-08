# Building a Freeswitch dialplan and executing actions

The default dialplan in the setup points to the "default" context for the existing users created in the directory.

We can work on the default context and execute actions by adding lines to our dialplan
The following command enters the default dialplan directory, we will create a new xml file with instructions
```sh
$ cd /usr/local/freeswitch/conf/dialplan/default/
$ vi example.xml
```

We can paste a sample extension to demonstrate our working dialplan instruction as follows.

```sh
<include>
    <extension name="log_something">
        <condition field="destination_number" expression="^61998996$">
            <action application="log" data="This is a sample example of logging"/>
        </condition>
    </extension>
</include>
```
After saving the above file, we will have to reload our xml files for changes to take effect

```sh
$ fs_cli -x reloadxml
```

Testing the above configuration would only require us to call on "61998996", the call will hangup and we can see the log lines on our freeswitch logs

The default location of log files is

```sh
$ cat /var/log/freeswitch/freeswitch.log
```