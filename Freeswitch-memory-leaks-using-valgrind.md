**We will need to install valgrind on the same machine running FreeSWITCH. I like using Debian for FreeSWITCH, which conveniently already has valgrind in the repository.**

```
apt install valgrind
```

**Now that valgrind is installed, we will need to make sure FreeSWITCH is not already running.**

```
systemctl stop freeswitch
ps -ef | grep freeswitch
```

**Once we stop FreeSWITCH, we want to start up FreeSWITCH using valgrind. Below is the command to do so. This will log all memory issues found by Valgrind to vg.log in the directory you run the command from.**

```
/usr/bin/valgrind.bin --tool=memcheck --error-limit=no --log-file=vg.log --leak-check=full --leak-resolution=high --show-reachable=yes /usr/local/freeswitch/bin -vg -ncwait -nonat
```

**Knowing the current total allocated memory for FreeSWITCH is cool, but not very useful for seeing the memory leak in action. If FreeSWITCH has a memory leak, it will fail to release memory when it is done with it, causing the allocated space to continue to grow. A useful way to observe this is by monitoring the RSS of the FreeSWITCH process over time, then running SIPp to create an artificial load on FreeSWTICH I created a simple bash script to log FreeSWITCH’s RSS, the current time, and the number of sip channels to a file.**


Create the script , such as memtest.sh, with the below code and then run it with ./memtest.sh &

```
#!/bin/sh


while true; do

INT=60
FILE=/root/mem.log
DATE=$(date +"%F-%R")
MEM=$(ps -aux | grep '[v]algrind.bin' | awk -F ' ' '{print $6}')
CHANNELS=$(/usr/local/freeswitch/bin/fs_cli -x "show channels" | grep total | awk -F ' ' '{print $1}')

echo ${DATE},${MEM},${CHANNELS} >> ${FILE}
sleep ${INT}s
done
```
You can confirm it is running in the background with jobs.


**Now, with FreeSWITCH running under valgrind, we will want to start up the SIPp stress test. On the SIPp server, run the following.**

```
sipp -sf uac.xml -s 9999999  <freeswitch-ip>:5080 -trace_msg -l 15 -d 60000
```

**This test is best done over a long period of time. I would let SIPp generate traffic on the server for a couple hours while our monitor script is running. After the system has ran under load for some time, the mem.log should look soemthing like this.**

```
2016-10-24-15:29,271212,15
2016-10-24-15:29,270800,15
2016-10-24-15:30,270672,15
2016-10-24-15:31,270828,15
2016-10-24-15:32,270708,15
2016-10-24-15:33,270604,15
2016-10-24-15:34,270812,15
2016-10-24-15:35,270628,15
2016-10-24-15:36,270816,15
```

**If FreeSWITCH has been running for multiple hours and the RSS has seemed to plateau, like above, FreeSWITCH is likely not showing any critical memory mismanagements. Here is an example of the RSS continuing to grow due to a memory leak.**

```
2016-08-11-04:09,379192,60
2016-08-11-04:10,381584,60
2016-08-11-04:11,383104,60
2016-08-11-04:12,385096,60
2016-08-11-04:13,385356,60
2016-08-11-04:14,387580,60
2016-08-11-04:16,387432,60
2016-08-11-04:17,389316,60
2016-08-11-04:18,391144,60
2016-08-11-04:19,393076,60
2016-08-11-04:20,394484,61
2016-08-11-04:21,395776,60
2016-08-11-04:22,397072,60
2016-08-11-04:23,398140,59
2016-08-11-04:24,400112,60
2016-08-11-04:25,401868,60
2016-08-11-04:26,403348,60
```