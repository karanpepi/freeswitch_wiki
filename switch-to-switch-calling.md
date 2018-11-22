### Objective: Originate call from Freeswitch A to user registered on another SIP box (Freeswitch B) and execute dialplan/ivr on Freeswitch A

- Allow both IPs on both the freeswitch servers (Whitelisting)
- Port forwarding 5060/5080 needs to be open on both servers
- Execute originate with the following syntax

```sh
$ freeswitch@fsdarryl.com> originate sofia/external/1004@172.16.0.159:5080 808081 XML default
```
The following is assuming Freeswitch A: 172.16.1.60, Freeswitch B: 172.16.0.159
Freeswitch A calls a user registered on Freeswitch B and creates a session, executing dialplan instructions from extension 808081 on Freeswitch A

The same use case can be followed for Odine or any SIP system
- Appropriate odine IP needs to be in place of IP of FS B
- No port needs to be specified