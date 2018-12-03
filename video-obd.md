**To make a Video OBD call through freeswitch the basic configuration required are listed below.**

*  mod_av
*  (H261 - H.261 Video H263 - H.263 Video) Video codec

You will need to add the global codec preferences in vars.xml file with the below code.
```
<X-PRE-PROCESS cmd="set" data="global_codec_prefs=H264,H263,H261,H263-1998,PCMU,PCMA"/>
<X-PRE-PROCESS cmd="set" data="outbound_codec_prefs=H264,H263,H261,H263-1998,PCMU,PCMA"/>
```

**DialPlan for video OBD Call(video format mp4)**

```
<extension name="video_bridge">
   <condition field="destination_number" expression="^808081$">
       <action application="answer"/>
       <action application="playback" data="av:///tmp/small.mp4"/>
      <!--<action application="play_mp4" data="/tmp/earth.mp4"/>-->
       <!--<action application="bridge" data="user/1002@172.16.0.159"/>-->
       <action application="hangup"/>
   </condition>
        </extension>
```

**Alter the Video resolution**

```
originate {video-canvas-size=240x160,video-codec-bandwidth=1mb,rtp_video_max_bandwidth_in=1mb,rtp_video_max_bandwidth_out=1mb,rtp_mirror_fmtp=true}user/1005@172.16.0.159 &bridge(user/1004@172.16.0.159)
```


**Docker Image that was used for Video OBD can be found in the below link**

https://hub.docker.com/r/bettervoice/freeswitch-container/~/dockerfile/

**For testing purpose a softphone that support video codec is needed.**

Ekiga softphone with video support can be used.