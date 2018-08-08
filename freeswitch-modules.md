# Installing and running custom FreeSwitch modules

We will install the following modules
- mod_python (to run python scripts)
- mod_shout (to play .mp3 files)
- mod_flite (to use text to speech)

### Installing mod_python

We need to change our current directory to our freeswitch installation directory

```sh
$ cd /usr/local/src/freeswitch
```

Install python-dev package 

```sh
$ apt-get install python python-dev
```
Enable compilation in modules.conf:
- Edit modules.conf and uncomment languages/mod_python

Recompile and install FreeSWITCH:

```sh
$ make install all
```

Enabling mod_python

Open up /usr/local/freeswitch/conf/autoload_configs/modules.conf.xml and uncomment the line that has

```sh
<load module="mod_python"/>
```

Next step, restart freeswitch

These steps will install mod_python successfully.

### Installing mod_shout

Run the following command inside the container

```sh
$ apt-get install libvorbis0a libogg0 libogg-dev libvorbis-dev
```
This will install dependencies needed by mod_shout

Edit modules.conf in /usr/local/src/freeswitch directory to add mod_shout.

```sh
formats/mod_shout
```
Configure/Make:

```sh
$ ./configure && make install
```

Enabling mod_shout

Open up /usr/local/freeswitch/conf/autoload_configs/modules.conf.xml and uncomment the line that has

```sh
<load module="mod_shout"/>
```

Next step, restart freeswitch

These steps will install mod_shout successfully.

### Installing mod_flite
We need to uncomment the asr_tts/mod_flite line from the modules.conf file.

```sh
$ vi /usr/local/src/freeswitch/modules.conf
```
Uncomment the asr_tts/mod_flite and run the following command

```sh
$ make install
```

In freeswitch/conf/autoload_configs/modules.conf.xml:
- Uncomment <load module="mod_flite"/>

```sh
<load module="mod_flite"/>
```

Next step, restart freeswitch

These steps will install mod_flite successfully.