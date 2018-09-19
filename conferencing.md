## Creating OBD dialling and patching to an Ongoing Voice Conference

- Conference configuration is present in /usr/local/freeswitch/conf/autoload_configs as conference.conf.xml

- This file need not be modified if a simple conference is to be created
- A conference is set up using dialplan application conference

- Example for a conference dialplan
```sh

 <extension name="conference_member">
    <condition field="destination_number" expression="^61998700$">
        <action application="answer"/>
        <action application="conference" data="darrylconf@default+1234"/>
    </condition>
 </extension>
```
### Inbound conference calls

- When users dial 61998700 on their SIP phones, they will be greeted and asked to enter the PIN of the conference named 'darrylconf'.
- The 'default' refers to the profile present in conference.conf.xml (It is present in the original configuration, need not be changed)
- +1234 refers to the PIN of the conference, the '+' is a delimiter

When user enters his PIN, he will enter the conference and can hear, speak and expected functionalities will be executed.

### Outbound conference calls

- OBD calls are ones that we send to the customer and transfer him to this dialplan extension with an originate command like: 

```sh
originate {ignore_early_media=true,origination_caller_id_number=9821277407}user/1000@172.16.1.60 61997000 XML default
```

The above will enter the same dialplan extension. However, since this is an OBD call, the user will not be asked to enter his PIN and will enter the conference as above.


### Events during a conference (Conference name here is darrylconf)
- Check users in a conference
```sh
$ conference darrylconf list

#Output

3;sofia/internal/1001@172.16.1.60;0eb8564a-28b6-4acf-a60f-9a8d862c2895;1001;1001;hear|speak|talking;0;0;0;300
1;sofia/internal/1000@172.16.1.60;981c8792-33d1-41e4-90e2-d81ba2066a49;1000;1000;hear|speak|talking|floor;0;0;0;300

```

- The Two lines above refer to the users present in the conference
- The numbers 3 and 1 are member ids of users 1001 and 1000 respectively

- Mute/Unmute/Deaf/Undeaf a member

```sh
#Syntax
$ conference darrylconf mute <member_id>
#Usage
$ conference darrylconf mute 3
```
The above will mute member 3

- The mute command can be replaced by unmute, deaf, undeaf for the actions to take effect.

