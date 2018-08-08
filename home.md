# FreeSwitch Development

### Enabling SIP users to connect to this FreeSwitch server

```sh
$ cd /usr/local/freeswitch/directory/default
$ vim 1000.xml
```
Change the password field to any password

```sh
<!--<param name="password" value="$${default_password}"/>-->
<param name="password" value="darryl"/>
```
Reload the xml files for changes to take effect
```sh
$ fs_cli -x reloadxml
```
Connect a sip phone to our freeswitch server using the following credentials
- Username: 1000
- Password: darryl
- IP Address: your_system_ip

Repeat the same process for another user id and connect to the same Freeswitch server
After the setup, we can directly call the extension and speak over the SIP Phone