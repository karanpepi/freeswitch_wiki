# Subscribing to Freeswitch Events

Creating a python based TCP event listener to listen to all/filtered FreeSwitch events

### Installation

- Installing Python's Event socket listener (ESL) Library

```sh
$ cd /usr/src/freeswitch/
$ make pymod
$ make pymod-install
```

### Creating a python script which will listen and print FreeSwitch Events to console

```sh
$ vi /tmp/event.py


import ESL
 
con = ESL.ESLconnection('localhost', '8021', 'ClueCon')
 
if con.connected():
    con.events('xml', 'all')
    while 1:
        e = con.recvEvent()
        if e:
            print e.serialize()
```

### Run the script

```sh
$ python /tmp/event.py
```
All the FreeSwitch events will be printed on the console in XML Format

### Filtering Events based on requirements

- Visit [FreeSwitch Event List](https://freeswitch.org/confluence/display/FREESWITCH/Event+List)
- We need to replace the 'all' keyword with our custom event keyword

e.g. We need to listen to only DTMF

We make the following change

```sh
import ESL
 
con = ESL.ESLconnection('localhost', '8021', 'ClueCon')
 
if con.connected():
    #con.events('xml', 'all')
     con.events('xml', 'DTMF')
    while 1:
        e = con.recvEvent()
        if e:
            print e.serialize()
```
