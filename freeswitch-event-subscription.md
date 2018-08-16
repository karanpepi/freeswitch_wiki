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

### Configuring event listener to log events in JSON, and further, sending these to a configured RabbitMQ queue.

We need to change the following lines to receive JSON events inside events.py

```sh
#con.events('xml', 'DTMF')'
con.events('plain', 'ALL')

#print e.serialize()
print e.serialize("json")
```
We will now receive the same event log in JSON format. Further, we need to re-create the script in order to send it to a RabbitMQ queue

```sh
import ESL
import pika
import json

def producer(data):
	credentials = pika.PlainCredentials('guest', 'guest')
	connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost',credentials=credentials))
	channel = connection.channel()
	channel.basic_publish(exchange='fs_exchange',
	                      routing_key='fs_queue',
	                      body=data)

	connection.close()

con = ESL.ESLconnection('127.0.0.1', '8021', 'ClueCon')

if con.connected():
	con.events('plain', 'ALL')
	while 1:
		e = con.recvEvent()
		if e:
			producer(e.serialize("json"))
```