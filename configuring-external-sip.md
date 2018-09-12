### Configuring an external SIP Trunk

#### Requirements from provider

- Username e.g. 1507
- Password e.g. dwqnl23EW
- Provider's IP e.g. 209.190.19.50

#### Create a file inside /usr/local/freeswitch/conf/sip_profiles/external

```sh
$ vim fs_external.xml
```
- Add the following in the file as configuration

```sh
<include>
<!-- Gateway name is mandatory, but you can set it to any string you want -->
  <gateway name="enterux">
  <param name="username" value="1507"/>
  <param name="password" value="dwqnl23EW"/>
  <param name="proxy" value="209.190.19.50"/>
  <param name="register" value="true"/>
  </gateway>
</include>

```

- Restart FreeSwitch
- Check status of your new external connection using the following command

```sh
$ fs_cli
$ sofia status gateway enterux
```

- The output will be something like this:

```sh
========================================================================================
Name    	enterux
Profile 	external
Scheme  	Digest
Realm   	209.190.19.50
Username	1507
Password	yes
From    	<sip:197@209.190.19.50>
Contact 	<sip:gw+enterux@172.16.1.60:5080;transport=udp;gw=enterux>
Exten   	1507
To      	sip:1507@209.190.19.50
Proxy   	sip:209.190.19.50
Context 	public
Expires 	3600
Freq    	3600
Ping    	0
PingFreq	0
PingTime	0.00
PingState	0/0/0
State   	REGED
Status  	UP
Uptime  	956s
CallsIN 	0
CallsOUT	1
FailedCallsIN	0
FailedCallsOUT	0
========================================================================================

```

- A sample test of calling can be playing a sound-file after dialling OBD on a mobile number. E.g. The following command will trigger a call to 917045345285 and play the sound of a bell (Soundfile is present in our local server)

```sh
$ fs_cli
$ originate {origination_caller_id_number=9821277407,continue_on_fail=true}sofia/gateway/enterux/917045345285 &playback('/tmp/soundfiles/bell.mp3')


```