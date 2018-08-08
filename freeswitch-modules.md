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
apt-get install python python-dev
```
Enable compilation in modules.conf:
- Edit modules.conf and uncomment languages/mod_python

Recompile and install FreeSWITCH:

```sh
make install all
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
apt-get install libvorbis0a libogg0 libogg-dev libvorbis-dev
```

