**Dowload the Sipp on different machine using the below URl**

* https://sourceforge.net/projects/sipp/files/sipp/3.4/ 

```
tar -xzf sipp-3.3.990.tar.gz*

cd sipp-3.3.990*

apt install libncurses6-dev libncurses6*

./configure; make*
```


**If you are on Debian 8, You will need to install the following ncurses libraries instead:**

``` 
apt install libncurses5-dbg libncurses5 libncurses
```


**SIPp will now be installed in the directory you ran the make command in.**
 



**FreeSWITH Dialplan**

We will create a basic extension in FreeSWITCH to process the sip traffic we will be sending via SIPp.

```
vim dialplan/public/0000000_sipp.xml 
```

<include>
  <extension name="sipp_test">
    <condition field="destination_number" expression="^(9999999)$">
    <action application="answer"/>
    <action application="playback" data="ivr/ivr-welcome_to_freeswitch.wav"/>
    <action application="hangup"/>
    </condition>
  </extension>
</include>

**Once you have this configured, give FreeSWITCH the reloadxml command:**

```
fs_cli -x 'reloadxml'
```

This will create the extension sipp_test in the public context, which is listening for incoming traffic to the number 9999999. Freeswitch will playback an ivr message to every calling matching this number.

**Next we will want to make sure our SIPp server is able to reach FreeSWITCH on the sip and rtp ports. Run the following iptables commands:**


```
iptables -A INPUT -p udp --dport 5080 -s -j ACCEPT
iptables -A INPUT -p udp -m multiport --dports 16384:32768 -s -j ACCEPT
```

**SIPp Testing**

* We are going to need a scenario file. You can paste the following into an xml file called uac.xml 

```
<?xml version="1.0" encoding="ISO-8859-1" ?>
<!DOCTYPE scenario SYSTEM "sipp.dtd">

<!-- This program is free software; you can redistribute it and/or      -->
<!-- modify it under the terms of the GNU General Public License as     -->
<!-- published by the Free Software Foundation; either version 2 of the -->
<!-- License, or (at your option) any later version.                    -->
<!--                                                                    -->
<!-- This program is distributed in the hope that it will be useful,    -->
<!-- but WITHOUT ANY WARRANTY; without even the implied warranty of     -->
<!-- MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the      -->
<!-- GNU General Public License for more details.                       -->
<!--                                                                    -->
<!-- You should have received a copy of the GNU General Public License  -->
<!-- along with this program; if not, write to the                      -->
<!-- Free Software Foundation, Inc.,                                    -->
<!-- 59 Temple Place, Suite 330, Boston, MA  02111-1307 USA             -->
<!--                                                                    -->
<!--                 Sipp default 'uac' scenario.                       -->
<!--                                                                    -->

<scenario name="Basic Sipstone UAC">
  <!-- In client mode (sipp placing calls), the Call-ID MUST be         -->
  <!-- generated by sipp. To do so, use [call_id] keyword.                -->
  <send retrans="500">
    <![CDATA[

      INVITE sip:[service]@[remote_ip]:[remote_port] SIP/2.0
      Via: SIP/2.0/[transport] [local_ip]:[local_port];branch=[branch]
      From: sipp <sip:sipp@[local_ip]:[local_port]>;tag=[pid]SIPpTag00[call_number]
      To: [service] <sip:[service]@[remote_ip]:[remote_port]>
      Call-ID: [call_id]
      CSeq: 1 INVITE
      Contact: sip:sipp@[local_ip]:[local_port]
      Max-Forwards: 70
      Subject: Performance Test
      Content-Type: application/sdp
      Content-Length: [len]

      v=0
      o=user1 53655765 2353687637 IN IP[local_ip_type] [local_ip]
      s=-
      c=IN IP[media_ip_type] [media_ip]
      t=0 0
      m=audio [media_port] RTP/AVP 0
      a=rtpmap:0 PCMU/8000

    ]]>
  </send>
  <recv response="100"
        optional="true">
  </recv>

  <recv response="180" optional="true">
  </recv>

  <recv response="183" optional="true">
  </recv>

  <!-- By adding rrs="true" (Record Route Sets), the route sets         -->
  <!-- are saved and used for following messages sent. Useful to test   -->
  <!-- against stateful SIP proxies/B2BUAs.                             -->
  <recv response="200" rtd="true">
  </recv>

  <!-- Packet lost can be simulated in any send/recv message by         -->
  <!-- by adding the 'lost = "10"'. Value can be [1-100] percent.       -->
  <send>
    <![CDATA[

      ACK sip:[service]@[remote_ip]:[remote_port] SIP/2.0
      Via: SIP/2.0/[transport] [local_ip]:[local_port];branch=[branch]
      From: sipp <sip:sipp@[local_ip]:[local_port]>;tag=[pid]SIPpTag00[call_number]
      To: [service] <sip:[service]@[remote_ip]:[remote_port]>[peer_tag_param]
      Call-ID: [call_id]
      CSeq: 1 ACK
      Contact: sip:sipp@[local_ip]:[local_port]
      Max-Forwards: 70
      Subject: Performance Test
      Content-Length: 0

    ]]>
  </send>

  <!-- This delay can be customized by the -d command-line option       -->
  <!-- or by adding a 'milliseconds = "value"' option here.             -->
  <pause/>

  <!-- The 'crlf' option inserts a blank line in the statistics report. -->
  <send retrans="500">
    <![CDATA[

      BYE sip:[service]@[remote_ip]:[remote_port] SIP/2.0
      Via: SIP/2.0/[transport] [local_ip]:[local_port];branch=[branch]
      From: sipp <sip:sipp@[local_ip]:[local_port]>;tag=[pid]SIPpTag00[call_number]
      To: [service] <sip:[service]@[remote_ip]:[remote_port]>[peer_tag_param]
      Call-ID: [call_id]
      CSeq: 2 BYE
      Contact: sip:sipp@[local_ip]:[local_port]
      Max-Forwards: 70
      Subject: Performance Test
      Content-Length: 0

    ]]>
  </send>

  <recv response="200" crlf="true">
  </recv>

  <!-- definition of the response time repartition table (unit is ms)   -->
  <ResponseTimeRepartition value="10, 20, 30, 40, 50, 100, 150, 200"/>

  <!-- definition of the call length repartition table (unit is ms)     -->
  <CallLengthRepartition value="10, 50, 100, 500, 1000, 5000, 10000"/>

</scenario>
```

**We can now run the following command to start the SIPp load test. This will start up an ncurses graph to show you the progress.**

```
sipp -sf uac.xml -s 99999 <freeswitch-server-ip>:5080 -trace_msg -l 100 -d 60000
```

