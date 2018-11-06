# Application to ban IPs that try to register onto freeswitch via SIP and fail
### Causes:
- Incorrect password
- Multiple failed logins from the same IP

## Step 1
- Install fail2ban

```sh
$ apt-get install fail2ban
```
## Step 2
- Add configuration for freeswitch in jail.conf

```sh
$ vim /etc/jail.conf

[freeswitch]

port     = 5060,5061
filter   = freeswitch
action   = %(banaction)s[name=%(__name__)s-tcp, port="%(port)s", protocol="tcp", chain="%(chain)s", actname=%(banaction)s-tcp]
           %(banaction)s[name=%(__name__)s-udp, port="%(port)s", protocol="udp", chain="%(chain)s", actname=%(banaction)s-udp]
           %(mta)s-whois[name=%(__name__)s, dest="%(destemail)s"]
logpath  = /usr/local/freeswitch/log/freeswitch.log
maxretry = 5

``` 
- Register a regular expression for failed authentication detection (filter)

```sh
$ vi /etc/fail2ban/filter.d/freeswitch.conf

[Definition]
failregex = ^\.\d+ \[WARNING\] sofia_reg\.c:\d+ SIP auth (failure) \((REGISTER)\) on sofia profile \'[^']+\' for \[.*\] from ip <HOST>$
            ^\.\d+ \[WARNING\] sofia_reg\.c:\d+ Can't find user \[\d+@\d+\.\d+\.\d+\.\d+\] from <HOST>$
            \"FAIL2BAN_HOST=<HOST>\"                                   
```
## Step 3

- Log auth attempts in freeswitch.log

```sh
$ vim /usr/local/freeswitch/conf/sip_profiles/internal.xml

<param name="log-auth-failures" value="true"/>

```
- Restart Freeswitch for changes to take effect

### All failed attempts will be banned after 5 failed tries. 

Customisations can be done in jail.conf.


## Step 4 (Installing Fail2Web instance for managing/monitoring Fail2Ban)
- Install dependencies
```sh
$ sudo apt-get install golang git gcc

```
- Download/Start Fail2Rest
```sh
$ go get github.com/Sean-Der/fail2rest
$ cd $GOPATH/src/github.com/Sean-Der/fail2rest
$ sudo -E go run *.go

```
- Download/Confgure Fail2Web

```sh
$ git clone --depth=1 https://github.com/Sean-Der/fail2web.git /var/www/fail2web

```

- Configure Fail2Web in your http server in this way (Nginx)
```sh
#Needs a proxy pass to the go server
server {
        listen 81 default_server;
        root /var/www/html/fail2web/web;
        index index.html;

        server_name localhost;

        location /api/ {
                proxy_pass http://localhost:5000/;
        }

}

```
Visit localhost:81 to use the interface. We can Add/Remove Banned IPs, optionally lookup the banned IPs etc.


### References
- Configuring with FreeSWITCH:  https://freeswitch.org/confluence/display/FREESWITCH/Fail2Ban

- Installing Fail2Web: 
http://siobud.com/blog/installing-fail2web