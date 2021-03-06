# Executing FreeSwitch actions using our dialplan

### We will execute the following actions using our dialplan on multiple extensions and destinations

- Playing a pre-recorded sound file
- Play a pre-recorded soundfile and capture user input from the keypad
- Record a message spoken by the user, play the recorded message back to him
- Speak out a numeric entry
- Bridge the call to another user registered on the same server
- Run an IVR script

#### Playing a pre-recorded sound file
First, we will need a pre-recorded soundfile which needs to be placed inside our docker container. We can use the following command to place a .wav soundfile inside our docker container

```sh
$ sudo docker cp drum.wav <container_name>:/tmp
```

We will now create a new example.xml file in the dialplan and make changes in this file

```sh
<include>
    <extension name="play_soundfile">
        <condition field="destination_number" expression="^61998996$">
            <action application="playback" data="/tmp/drum.wav"/>
        </condition>
    </extension>
</include>
```

After saving the file, we need to run the following command to reload the xml files

```sh
$ fs_cli -x reloadxml
```

The above extension will match for destination 61998996. If a match is found, it will enter the condition and play the soundfile found as /tmp/drum.wav and exit.

This can be demonstrated by dialling 61998996 from a sip phone connected to this freeswitch server.

#### Play a pre-recorded soundfile and capture user input from the keypad

Once again, we need a pre-recorded soundfile that will play, preferably, 'Please enter either 1 or 2 from your keypad', we will wait for the user input and log the key that the user pressed.

Assuming our soundfile is present in /tmp/welcome_pagd.wav

We will edit the example.xml file to include new extensions

```sh
<extension name="play_and_get_digits example">
  <condition field="destination_number" expression="^61998998$">
    <action application="play_and_get_digits" data="1 1 2 5000 # /tmp/soundfiles/initial.wav /tmp/yamaha.wav foobar \d"/>
    <action application="log" data="DTMF ENTERED BY DARRYL IS ${foobar}"/>
  </condition>
</extension>
```
Once again after saving this file, we need to reloadxml using

```sh
$ fs_cli -x reloadxml
```
We can now test it by dialling 61998998. We will be prompted to enter a dtmf, we can press a key using our keypad. We will get a log of the key pressed in the freeswitch logs.

### Record a message spoken by the user, play the recorded message back to him

In this example, we will record a soundfile, place it as /tmp/data.wav and play the same file after the recording is complete. The recording will be for 5 seconds.

We will edit example.xml to include the following extension

```sh
<extension name="record_session">
    <condition field="destination_number" expression="^61999000$">
        <action application="set" data="playback_terminators=#"/>
        <action application="record" data="/tmp/data.wav 5"/>
        <action application="playback" data="/tmp/data.wav"/>
    </condition>
</extension>

```
Once again after saving this file, we need to reloadxml using

```sh
$ fs_cli -x reloadxml
```

This can be tested by dialling 61999000 on the sip phone. We need to begin recording as soon as the call is answered. After 5 seconds, or on press of #, our recording will terminate and the sound we had recorded will be played to us.

### Speak out a numeric entry

This example will use a pre-built freeswitch module 'say' to speak out any numeric value we pass to it.

We need to add the following lines to example.xml file

```sh
<extension name="say_module">
    <condition field="destination_number" expression="^61997000$">
        <action application="say" data="en number pronounced 12345"/>
    </condition>
</extension>
```

Once again after saving this file, we need to reloadxml using

```sh
$ fs_cli -x reloadxml
```
This can be tested by dialling 61997000 on the sip phone, A sound will be played speaking out the value of 12345 in words. We can substitute "12345" using the above Play and get digits example to get a more dynamic example.

### Bridge the call to another user registered on the same server

This example assumes that our primary user has registered on a sip phone network using the local Freeswitch IP and ID=1000
Out secondary user needs to be registered on the same network with ID=1001

Registration instructions can be found in [SIP Phone registration](home)

We can then proceed and make the following changes to our example.xml script

```sh
<extension name="bridge_somewhere">
    <condition field="destination_number" expression="^61998997$">
        <action application="bridge" data="user/1001@172.16.1.60"/>
    </condition>
</extension>
```
Once again after saving this file, we need to reloadxml using

```sh
$ fs_cli -x reloadxml
```

After dialling 61998997, we will automatically get bridged to the user 1001, given that he answers the call. The call will continue until someone hangs up.

### Run an IVR script

We will now redirect our call to a python script which will use freeswitch actions as an API from the script. 
We will need to create a python script and place it inside the docker container at the following location: /usr/local/freeswitch/scripts/

We can create a sample script as follows:

```sh
import csv
from freeswitch import *
from datetime import datetime

def handler(session,args):
	message = "This is a log from the python ivr "
	caller = session.getVariable("caller") # We use the variable we set previously in our Dialplan
	session.execute("log",message + "Caller id is %s"%caller)
	session.execute("playback","/tmp/drum.wav")
	session.hangup()

```
Save it as sample.ivr.py, and copy it inside the docker container using

```sh
$ sudo docker cp sample_ivr.py <container_name>:/usr/local/freeswitch/scripts
```

We now need to add the following to example.xml

```sh
<extension name="play_ivr">
    <condition field="destination_number" expression="^61998999$">
        <action application="python" data="sample_ivr"/>
    </condition>
</extension>
```
Once again after saving this file, we need to reloadxml using

```sh
$ fs_cli -x reloadxml
```

We can now test this feature by dialling 61998999. In this example, we are logging the caller id, and then playing the drum.wav soundfile, then terminating. 


