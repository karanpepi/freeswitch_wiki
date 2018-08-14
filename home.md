# FreeSwitch Development

### Setting up a FreeSwitch server over a docker container and executing commands.

System requirements
- [Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

The following sequence is to be followed to set up and use FreeSwitch

1. [Installing FreeSwitch using docker](freeswitch-installation)
2. [Setting up a SIP Phone for basic voice testing](SIP-Phone-registrations)
3. [Understanding and modifying a FreeSwitch Dialplan](freeswitch-dialplan)
4. [Executing Many FreeSwitch Actions/Features](freeswitch-actions)
5. [Installing custom FreeSwitch modules](freeswitch-modules)
6. [Installing CDR Pingback on a configured HTTP POST URL](installing-and-configuring-json-cdrs)
7. [Adding an event listener to FreeSwitch](freeswitch-event-subscription)

### Event listening and CDRs
Each event is sent to a RabbitMQ Queue
1. [Event listening for CDR](events-cdr)