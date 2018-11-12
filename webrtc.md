## Configuring WebRTC using FreeSWITCH

### Requirements
- SSL Certificates (Self signed are ok, too)
- Domain name pointing to the freeswitch server

## Step 1:
- Begin by setting up the following in the vars.xml file in freeswitch

```sh
  <!-- Internal SIP Profile -->
  <X-PRE-PROCESS cmd="set" data="internal_auth_calls=true"/>
  <X-PRE-PROCESS cmd="set" data="internal_sip_port=5060"/>
  <X-PRE-PROCESS cmd="set" data="internal_tls_port=5061"/>
  <X-PRE-PROCESS cmd="set" data="internal_ssl_enable=true"/>


  <!-- External SIP Profile -->
  <X-PRE-PROCESS cmd="set" data="external_auth_calls=false"/>
  <X-PRE-PROCESS cmd="set" data="external_sip_port=5080"/>
  <X-PRE-PROCESS cmd="set" data="external_tls_port=5081"/>
  <X-PRE-PROCESS cmd="set" data="external_ssl_enable=true"/>

```
- Ensure the following lines are uncommented inside sip_profiles/internal.xml

```sh
    <param name="wss-binding" value=":7443"/>
```

- Place the certificate files inside /usr/local/freeswitch/certs with the following naming conventions

1. agent.pem -  Cert file and key file separated by \n
2. wss.pem - Cert key and chain files separated by \n
3. cafile.pem - Chain file

Chain file is the root CA certificate, cert file is the signed crt/pem file and key file is our private key

If all these files are present, we can restart freeswitch

## Step 2
FreeSWITCH will start its websocket over port 7443 and this can be used by any JS client
- Install sipmljs and connect to freeswitch

-  Git clone the sipml project
```sh
git clone https://github.com/DoubangoTelecom/sipml5.git
```
- Visit the index page from the web browser
Enter the path to the index.html on your browser

- Configure the options as per the username/password combination and save the settings.

Use the following format:
1. Display name: username e.g. 1002
2. Private identity: username e.g. 1002
3. Public identity: sip:1002@fsdarryl.com
4. Password: 1002
5. Realm: fsdarryl (arbitrary)

fsdarryl.com is a dummy hostname that is configured into my /etc/hosts as I don't have a registered domain name

Enter web socket url into expert mode as wss:fsdarryl.com:7443
Save the details and proceed to make calls

- We can now receive/create calls using this as a SIP client


(The above was tested and works on chromium v49 with self signed certificates)