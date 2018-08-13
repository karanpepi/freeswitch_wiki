# This feature enables caller to save a voice msg to the callee if the callee does not pick up the call or if he is busy.

We need to check if the below modules are install
- mod_voicemail (for voicemail)
- make cd-sounds-install && make cd-moh-install (/usr/local/src/freeswitch default sound files if not present for voicemail)

### Sample code for voicemail

```sh
<extension name="public_did">
  <condition field="destination_number" expression="^30015$">
    <action application="set" data="call_timeout=5"/>
    <action application="set" data="continue_on_fail=true"/>
    <action application="set" data="hangup_after_bridge=true"/>
    <action application="bridge" data="user/1002@172.16.0.159"/>
    <action application="answer"/>
    <action application="voicemail" data="default $${domain} 1002"/>
  </condition> 
</extension>
```

* In this plan, when someone calls 30015 (my DID, from VoIP provider) we set timeout to 5 seconds, so if it's not answered before then, it goes to voicemail.

* Continue_on_fail means if the bridge fails for whatever reason, one being no one answered it, continue processing the rest of the plan so the call goes to voicemail.


* Hangup_after_bridge means if someone answered the phone within the 5 seconds, then stop processing the rest of the plan when the call terminates, ie, don't send it to voicemail.

* I'm sending the call to 1002. If he does not pickup the call, FreeSWITCH answers the call, and launches the voicemail application, sending the call to voicemail box 1002

* The callee then can dial *98  to access voicemail and listen to the voice mesages.

# Useful links

* [VOICEMAIL](https://freeswitch.org/confluence/display/FREESWITCH/Voicemail)

