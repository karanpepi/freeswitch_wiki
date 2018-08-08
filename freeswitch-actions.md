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

The above extension will match for destination 61998996. If a match is found, it will enter the condition and play the soundfile found as /tmp/drum.wav and exit.

This can be demonstrated by dialling 61998996 from a sip phone connected to this freeswitch server.


