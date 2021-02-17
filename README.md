# Calibre Web

[![Docker Stars](https://img.shields.io/docker/stars/technosoft2000/calibre-web.svg)]()
[![Docker Pulls](https://img.shields.io/docker/pulls/technosoft2000/calibre-web.svg)]()
[![](https://images.microbadger.com/badges/image/technosoft2000/calibre-web.svg)](https://microbadger.com/images/technosoft2000/calibre-web "Get your own image badge on microbadger.com")
[![](https://images.microbadger.com/badges/version/technosoft2000/calibre-web.svg)](https://microbadger.com/images/technosoft2000/calibre-web "Get your own version badge on microbadger.com")

## Calibre Web - Manage your Calibre e-book collection ##

[Calibre Web](https://github.com/janeczku/calibre-web) is a web app providing a clean interface for browsing, reading and downloading eBooks using an **existing Calibre database**.

![screenshot](https://raw.githubusercontent.com/janeczku/docker-calibre-web/master/screenshot.png)

__Calibre Web__ comes with the following features:

 * Bootstrap 3 HTML5 interface
 * full graphical setup
 * User management
 * Admin interface
 * User Interface in english, french, german, polish, simplified chinese, spanish
 * OPDS feed for eBook reader apps
 * Filter and search by titles, authors, tags, series and language
 * Create custom book collection (shelves)
 * Support for editing eBook metadata
 * Support for converting eBooks from EPUB to Kindle format (mobi/azw)
 * Restrict eBook download to logged-in users
 * Support for public user registration
 * Send eBooks to Kindle devices with the click of a button
 * Support for reading eBooks directly in the browser (.txt, .epub, .pdf)
 * Upload new books in PDF, epub, fb2 format
 * Support for Calibre custom columns
 * Fine grained per-user permissions
 * Self update capability

If you want to know more you can head over to the __Calibre Web__ project site: https://github.com/janeczku/calibre-web.

And if you are interested in the original __Calibre__ ebook management tool then look at the project site: https://calibre-ebook.com/.

## Updates ##

**2020-10-24 - v1.5.0**

 * new base image [technosoft2000/alpine-base:3.12-1](https://hub.docker.com/r/technosoft2000/alpine-base/) based on Alpine 3.12.0
 * fixed `[WARNING]: Empty continuation line found in: ...` in Dockerfile
 * upgrade of [sgerrand/alpine-pkg-glibc](https://github.com/sgerrand/alpine-pkg-glibc) to version 2.32-r0
 * changed glibc compiled package (ArchLinux) `libutil-linux` to new `util-linux-libs` - see issue **Build failure in v1.4.1** #87
 * updated dependencies from `requirements.txt` and `optional-requirements.txt` - see issue **Missing jsonschema in the docker image** #90
 * changed python from `python2` to `python3`
 * allow **gevent 20.9.0** otherwise: 
   - error will appear `<frozen importlib._bootstrap>:219: RuntimeWarning: greenlet.greenlet size changed, may indicate binary incompatibility. Expected 144 from C header, got 152 from PyObject`
   - see at `gevent` where the issue was known: https://github.com/gevent/gevent/issues/1260
 * added **unrar**
 * **kepubify** is not added, because Alpine Linux doesn't contains a pre-build package at the moment
 * updated Calibre **ebook-convert** from 4.13.0 to 5.3.0
 * uses `https://github.com/Technosoft2000/docker-calibre-web/releases/download/kindlegen/kindlegen_linux_2.6_i386_v2_9.tar.gz` to download Amazon kindlegen

**2020-04-12 - v1.4.1**

 * new base image [technosoft2000/alpine-base:3.11-2](https://hub.docker.com/r/technosoft2000/alpine-base/) based on Alpine 3.11.5
 * upgrade of [sgerrand/alpine-pkg-glibc](https://github.com/sgerrand/alpine-pkg-glibc) to version 2.31-r0
 * updated to Calibre 4.13.0
 * added the packages tar xz zstd to unpack needed Arch Linux packages which are compiled with glibc
 * still no upgrade to Python 3 because it doesn't work correct, get the following error:
```
[INFO] Launching Calibre-Web ...
Traceback (most recent call last):
  File "/calibre-web/app/cps.py", line 34, in <module>
    from cps import create_app
  File "/calibre-web/app/cps/__init__.py", line 28, in <module>
    from babel import Locale as LC
ImportError: No module named babel
```
 * maybe I'll switch the base image because Alpine gets to hacky to run Calibre Web correctly, because of the glibc dependencies - see also at https://pythonspeed.com/articles/base-image-python-docker-images/
 * or maybe I'll switch to the linuxserver/calibre-web docker image and discontinue maintanance of this image
 * IMPORTANT regarding update: if you've issues then remove the content from your volume mount -v <your Calibre Web application folder>:/calibre-web/app before you start the container

For previous changes see at [full changelog](CHANGELOG.md).

## Features ##

 * running Calibre Web under its own user (not root)
 * changing of the UID and GID for the Calibre Web user
 * no usage of NGINX inside the container, only the Calibre Web application is served as single application without any supervisor
 * Google Drive integration is included
 * creation of gdrive.db symlink at `/books/gdrive.db` for external access like backup possibility
 * creation of app.db symlink at `/books/app.db` for external access like backup possibility
 * support of **Amazon kindlegen** and **Calibre ebook-convert** tool to convert to MOBI
 * **Calibre ebook-convert** uses `glibc` and therefore additional packages from **ArchLinux** are needed

## Hints & Tips ##
 
 * if you need SSL support similiar to the original Docker Container [janeczku/calibre-web](https://hub.docker.com/r/janeczku/calibre-web/) then use an additional NGINX or Apache HTTP Server as Reverse-Proxy, e.g see [jwilder/nginx-proxy](https://hub.docker.com/r/jwilder/nginx-proxy/)
 * if you don't specify __PGID__ and __PUID__ values the default __PGID__ and __PUID__ of the image are used,
and if they are used then the mapped host volume/directory which is alligned to `/books` must have _read-write-execute_ permission for **_others_** , otherwise the configuration of Calibre-Web can't be finished :-|
 * for Synology Users - don't map a top-level volume directory from the NAS as `/books` volume, e.g. `/volume1/books` because it results into problems with directory permissons. Create instead a subdirectory __calibre__ at `/volume1/books` and map then `/volume1/books/calibre` as volume for `/books`

## Configuration at first launch ##
 1. Point your browser to `http://hostname:<HTTP PORT>` e.g. `http://hostname:8083`
 2. Set Location of your Calibre books folder to the path of the folder where you mounted your Calibre folder in the container, which is by default `\books`.
    So enter at the field __Location of Calibre database__ the mapped volume `\books`.
 3. Hit __Submit__ then __Login__.

Default admin login:
 * __Username:__ admin
 * __Password:__ admin123

After successful login change the default password and set the email adress.

To access the OPDS catalog feed, point your Ebook Reader to `http://hostname:<HTTP PORT>/opds`

## Configuration of a converter ##
   at **Admin** -> **Basic Configuration** -> **E-Book converter** you've to set the converter which you want to use:
   - for the option **Use Kindlegen** set the **Path to convertertool** to `/calibre-web/app/vendor/kindlegen`
     and at **About** you will see then `kindlegen	Amazon kindlegen(Linux) V2.9 build 1028-0897292`
   - for the option **Use calibre's ebook converter** set the **Path to convertertool** to `/opt/calibre/ebook-convert`
     and at **About** you will see then `Calibre converter	ebook-convert (calibre 3.30.0)`

## Known issue ##
1. if you map the old/existing app volume like `-v /volume1/docker/apps/calibre-web/app:/calibre-web/app`
   then you'll get the following issue at startup

```
[INFO] Checkout the latest Calibre-Web version ...
[INFO] Autoupdate is active, try to pull the latest sources for Calibre-Web ...
[INFO] ... current git status is
fatal: not a git repository (or any parent up to mount point /calibre-web)
Stopping at filesystem boundary (GIT_DISCOVERY_ACROSS_FILESYSTEM not set).
[INFO] ... pulling sources
fatal: not a git repository (or any parent up to mount point /calibre-web)
Stopping at filesystem boundary (GIT_DISCOVERY_ACROSS_FILESYSTEM not set).
[INFO] ... git status after update is
fatal: not a git repository (or any parent up to mount point /calibre-web)
Stopping at filesystem boundary (GIT_DISCOVERY_ACROSS_FILESYSTEM not set).
```

   To solve the issue delete the old files at `-v /volume1/docker/apps/calibre-web/app:/calibre-web/app`
   before you create and start the container.

## Usage ##

__Create the container:__

```
docker create --name=calibre-web --restart=always \
-v <your Calibre books folder>:/books \
[-v <your Calibre Web application folder>:/calibre-web/app] \
[-v <your Calibre Web kindlegen folder>:/calibre-web/kindlegen`]
[-v <your Calibre Web config folder>:/calibre-web/config \]
[-e USE_CONFIG_DIR=true \]
[-e APP_REPO=https://github.com/janeczku/calibre-web.git \]
[-e APP_BRANCH=master \]
[-e SET_CONTAINER_TIMEZONE=true \]
[-e CONTAINER_TIMEZONE=<container timezone value> \]
[-e PGID=<group ID (gid)> -e PUID=<user ID (uid)> \]
-p <HTTP PORT>:8083 \
technosoft2000/calibre-web
```

__Example:__

```
docker create --name=calibre-web --restart=always \
-v /volume1/books/calibre:/books \
-v /etc/localtime:/etc/localtime:ro \
-e PGID=65539 -e PUID=1029 \
-p 8083:8083 \
technosoft2000/calibre-web
```

or

```
docker create --name=calibre-web --restart=always \
-v /volume1/books/calibre:/books \
-v /volume1/docker/apps/calibre-web/config:/calibre-web/config \
-e USE_CONFIG_DIR=true \
-e SET_CONTAINER_TIMEZONE=true \
-e CONTAINER_TIMEZONE=Europe/Vienna \
-e PGID=65539 -e PUID=1029 \
-p 8083:8083 \
technosoft2000/calibre-web
```

__Start the container:__
```
docker start calibre-web
```

## Parameters ##

### Introduction ###
The parameters are split into two parts which are separated via colon.
The left side describes the host and the right side the container. 
For example a port definition looks like this ```-p external:internal``` and defines the port mapping from internal (the container) to external (the host).
So ```-p 8080:80``` would expose port __80__ from inside the container to be accessible from the host's IP on port __8080__.
Accessing http://'host':8080 (e.g. http://192.168.0.10:8080) would then show you what's running **INSIDE** the container on port __80__.

### Details ###
* `-p 8083` - http port for the web user interface
* `-v /books` - local path which contains the Calibre books and the necessary `metadata.db`  which holds all collected meta-information of the books
* `-v /calibre-web/app` - local path for Calibre Web application files; set this volume if you want to use Google Drive
* `-v /calibre-web/kindlegen` - local path for Calibre Web kindlegen application
* `-v /etc/localhost` - for timesync - __optional__
* `-v /calibre-web/config` - local path for Calibre Web config files, like `app.db` and `gdrive.db`; **IMPORTANT**: the environment variable `USE_CONFIG_DIR` must be set to `true` - __optional__
* `-e USE_CONFIG_DIR=true` - activate the usage of a dedicated configuration directory, otherwise the `books` directory will be used (default) - __optional__
* `-e APP_REPO` - set it to the Calibre Web GitHub repository; by default it uses https://github.com/janeczku/calibre-web.git - __optional__
* `-e APP_BRANCH` - set which Calibre Web GitHub repository branch you want to use, __master__ (default branch) - __optional__
* `-e SET_CONTAINER_TIMEZONE` - set it to `true` if the specified `CONTAINER_TIMEZONE` should be used - __optional__
* `-e CONTAINER_TIMEZONE` - container timezone as found under the directory `/usr/share/zoneinfo/` - __optional__
* `-e PGID` for GroupID - see below for explanation - __optional__
* `-e PUID` for UserID - see below for explanation - __optional__

### Container Timezone

In the case of the Synology NAS it is not possible to map `/etc/localtime` for timesync, and for this and similar case
set `SET_CONTAINER_TIMEZONE` to `true` and specify with `CONTAINER_TIMEZONE` which timezone should be used.
The possible container timezones can be found under the directory `/usr/share/zoneinfo/`.

Examples:

 * ```UTC``` - __this is the default value if no value is set__
 * ```Europe/Berlin```
 * ```Europe/Vienna```
 * ```America/New_York```
 * ...

Once the container is running you can get all possible timezones as tree via the command ```docker exec -it <CONTAINER> tree /usr/share/zoneinfo```

See also at [possible timezone values](TIMEZONES.md).

__Don't use the value__ `localtime` because it results into: `failed to access '/etc/localtime': Too many levels of symbolic links`

### Container Locale

By default the container comes with the following locale environment setting:

```
    # - LANG, LANGUAGE, LC_ALL: language dependent settings (Default: en_US.UTF-8)
    LANG="en_US.UTF-8"
    LANGUAGE="en_US.UTF-8"
    LC_ALL="en_US.UTF-8"
``` 

To set a different locale set the corressponding environment settings while container creation via `-e LANG`, `-e LANGUAGE`, `-e LC_ALL`.
Look here at [availaible locale](LOCALE.md) which values are supported - e.g. for **de_DE** you've to use then **de_DE.UTF-8** as value,
here an example `-e LANG=de_DE.UTF-8`.

To check what locale is active inside the container:
```
root@NAS:~# docker exec -it calibre-web bash

bash-4.4# echo $LANG $LANGUAGE $LC_ALL
en_US.UTF-8 en_US.UTF-8 C
```

When you execute the `ebook-convert` tool inside the container you'll get a translated output according to the active locale:

```
bash-4.4# ebook-convert 
Usage: ebook-convert input_file output_file [options]

Convert an e-book from one format to another.

input_file is the input and output_file is the output. Both must be specified as the first two arguments to the command.

The output e-book format is guessed from the file extension of output_file. output_file can also be of the special format .EXT where EXT is the output file extension. In this cas
e, the name of the output file is derived from the name of the input file. Note that the filenames must not start with a hyphen. Finally, if output_file has no extension, then it
 is treated as a directory and an "open e-book" (OEB) consisting of HTML files is written to that directory. These files are the files that would normally have been passed to the
 output plugin.

After specifying the input and output file you can customize the conversion by specifying various options. The available options depend on the input and output file types. To get
 help on them specify the input and output file and then use the -h option.

For full documentation of the conversion system see
https://manual.calibre-ebook.com/conversion.html

Whenever you pass arguments to ebook-convert that have spaces in them, enclose the arguments in quotation marks. For example: "/some path/with spaces"

Options:
  --version       show program's version number and exit

  -h, --help      show this help message and exit

  --list-recipes  List builtin recipe names. You can create an e-book from a
                  builtin recipe like this: ebook-convert "Recipe Name.recipe"
                  output.epub


Created by Kovid Goyal <kovid@kovidgoyal.net>
bash-4.4#
```

After change to e.g. **de_DE.UTF-8** you'll see the following translated output:

```
bash-4.4# export LANG=de_DE.UTF-8; LANGUAGE=de_DE.UTF-8; LC_ALL=de_DE.UTF-8
bash-4.4# echo $LANG $LANGUAGE $LC_ALL
de_DE.UTF-8 de_DE.UTF-8 de_DE.UTF-8

bash-4.4# ebook-convert 
Verwendung: ebook-convert Quelldatei Ausgabedatei [Optionen]

Ein eBook von einem Format in ein anderes konvertieren.

Quelldatei ist die Eingabe, Ausgabedatei die Ausgabe. Beide müssen dem Befehl als die ersten beiden Argumente übergeben werden.

Das Zielformat wird durch die Dateiendung von Ausgabedatei bestimmt. Ausgabedatei kann auch das Spezialformat .EXT besitzen, wobei EXT die gewünschte Dateinamenerweiterung ist. I
n diesem Fall wird der Name der Ausgabedatei aus dem Namen der Quelldatei abgeleitet. Bitte beachten: Die Dateinamen dürfen nicht mit einem Bindestrich beginnen. Zu guter Letzt, 
falls Ausgabedatei keine Dateinamenerweiterung besitzt, wird der Name als Verzeichnis behandelt, und ein "open ebook" (OEB), das aus HTML-Dateien besteht, wird in dieses Verzeich
nis geschrieben. Diese Dateien sind jene, die normalerweise an die Ausgabe-Erweiterung übergeben würden.

Nach der Angabe von Quell- und Ausgabedatei kann die Konvertierung noch durch die Angabe verschiedener Optionen angepasst werden.  Welche Optionen hier zur Verfügung stehen, häng
t von den Dateitypen der Quell- und Ausgabedateien ab.  Um dazu Hilfe zu erhalten, geben Sie die Quell- und Ausgabedatei gefolgt von der Option -h an.

Die komplette Dokumentation des Konvertierungssystems findet sich unter
https://manual.calibre-ebook.com/de/conversion.html

Wann immer Sie Argumente mit Leerzeichen an ebook-convert weitergeben, müssen diese Argumente in Anführungsstriche gesetzt werden. Zum Beispiel: "/some path/with spaces"

Optionen:
  --version       Programmversion anzeigen und beenden

  -h, --help      Diesen Hilfetext anzeigen und beenden.

  --list-recipes  Anzeigen der Namen der integrierten Nachrichtenquellen. Sie
                  können aus einer integrierten Quelle ein eBook wie folgt
                  erstellen: ebook-convert "Recipe Name.recipe" output.epub


Erstellt von Kovid Goyal <kovid@kovidgoyal.net>
bash-4.4#
```

To check which locale are supported execute the following command:

```
bash-4.4# /usr/glibc-compat/bin/locale -a

C
POSIX
aa_DJ.utf8
aa_ER.utf8
aa_ET.utf8
af_ZA.utf8
am_ET.utf8
an_ES.utf8
ar_AE.utf8
ar_BH.utf8
ar_DZ.utf8
ar_EG.utf8
...

bash-4.4#
```

## User / Group Identifiers ##
Sometimes when using data volumes (-v flags) permissions issues can arise between the host OS and the container. We avoid this issue by allowing you to specify the user PUID and group PGID. Ensure the data volume directory on the host is owned by the same user you specify and it will "just work" ™.

In this instance PUID=1001 and PGID=1001. To find yours use id user as below:

```
  $ id <dockeruser>
    uid=1001(dockeruser) gid=1001(dockergroup) groups=1001(dockergroup)
```

## Container Directory Structure ##
```
 /
   |- books
   |- calibre-web
       |- app
       |    |- "all Calibre Web Application files"
       |    |- app.db -> /calibre-web/config/app.db
       |    |- gdrive.db -> /calibre-web/config/gdrive.db
       |    |- calibre-web.log
       |    |- cps 
       |    |    |- *.py
       |    |    |- *.pyc
       |    |
       |    |- vendor
       |         |- kindlegen -> /calibre-web/kindlegen/kindlegen
       |
       |- config
       |    |- app.db
       |    |- gdrive.db
       |
       |- kindlegen
            |- EULA*.txt
            |- KindleGen Legal Notices*.txt
            |- docs
            |- kindlegen
            |- manual.html
```

## Additional ##
Shell access whilst the container is running: `docker exec -it calibre-web /bin/bash`

Upgrade to the latest version of Calibre Web: `docker restart calibre-web`

To monitor the logs of the container in realtime: `docker logs -f calibre-web`

To monitor the logs of Calibre Web: `docker exec -it calibre-web tail -f /calibre-web/app/calibre-web.log`

Show used base image version number of Calibre Web: `docker inspect -f '{{ index .Config.Labels "image.base.version" }}' calibre-web`

Show used image version number of Calibre Web: `docker inspect -f '{{ index .Config.Labels "image.version" }}' calibre-web`

---

## For Synology NAS users ##

Login into the DSM Web Management
* Open the Control Panel
* Control _Panel_ > _Privilege_ > _Group_ and create a new one with the name 'docker'
* add the permissions for the directories 'downloads', 'video' and so on
* disallow the permissons to use the applications
* Control _Panel_ > _Privilege_ > _User_ and create a new on with name 'docker' and assign this user to the group 'docker'

Connect with SSH to your NAS
* after sucessful connection change to the root account via
```
sudo -i
```
or
```
sudo su -
```
for the password use the same one which was used for the SSH authentication.

* create a 'docker' directory on your volume (if such doesn't exist)
```
mkdir -p /volume1/docker/
chown root:root /volume1/docker/
```

* get your Docker User ID and Group ID of your previously created user and group
```
id docker
uid=1029(docker) gid=100(users) groups=100(users),65539(docker)
```

* get the Docker image
```
docker pull technosoft2000/calibre-web
```

* create a Docker container (take care regarding the user ID and group ID, change timezone and port as needed)
```
docker create --name=calibre-web --restart=always \
-v /volume1/books/calibre:/books \
-e SET_CONTAINER_TIMEZONE=true \
-e CONTAINER_TIMEZONE=Europe/Vienna \
-e PGID=65539 -e PUID=1029 \
-p 8083:8083 \
technosoft2000/calibre-web
```

* check if the Docker container was created successfully
```
docker ps -a
CONTAINER ID        IMAGE                           COMMAND                CREATED             STATUS              PORTS               NAMES
40cc1bfaf7be        technosoft2000/calibre-web      "/bin/bash -c /init/s" 8 seconds ago       Created 
```

* start the Docker container
```
docker start calibre-web
```

* analyze the log (stop it with CTRL+C)
```
docker logs -f calibre-web
        ,----,                                   
      ,/   .`|                                   
    ,`   .'  : .--.--.        ,----,        ,-.  
  ;    ;     //  /    '.    .'   .' \   ,--/ /|  
.'___,/    ,'|  :  /`. /  ,----,'    |,--. :/ |  
|    :     | ;  |  |--`   |    :  .  ;:  : ' /   
;    |.';  ; |  :  ;_     ;    |.'  / |  '  /    
`----'  |  |  \  \    `.  `----'/  ;  '  |  :    
    '   :  ;   `----.   \   /  ;  /   |  |   \   
    |   |  '   __ \  \  |  ;  /  /-,  '  : |. \  
    '   :  |  /  /`--'  / /  /  /.`|  |  | ' \ \ 
    ;   |.'  '--'.     /./__;      :  '  : |--'  
    '---'      `--'---' |   :    .'   ;  |,'     
                        ;   | .'      '--'       
                        `---'                    
      PRESENTS ANOTHER AWESOME DOCKER IMAGE
      ~~~~~         Calibre Web       ~~~~~
[INFO] Docker image version: 1.3.0
[INFO] Alpine Linux version: 3.9.0
[WARNING] A group with id 100 exists already [in use by users] and will be modified.
[WARNING] The group users will be renamed to calibre
[INFO] Create user calibre with id 1029
[INFO] Current active timezone is UTC
Sun Feb 17 10:43:25 CET 2019
[INFO] Container timezone is changed to: Europe/Vienna
[INFO] Change the ownership of /calibre-web (including subfolders) to calibre:calibre
[INFO] Current git version is:
git version 2.20.1
[INFO] Checkout the latest Calibre-Web version ...
[INFO] Autoupdate is active, try to pull the latest sources for Calibre-Web ...
[INFO] ... current git status is
fatal: not a git repository (or any parent up to mount point /calibre-web)
Stopping at filesystem boundary (GIT_DISCOVERY_ACROSS_FILESYSTEM not set).
[INFO] ... pulling sources
fatal: not a git repository (or any parent up to mount point /calibre-web)
Stopping at filesystem boundary (GIT_DISCOVERY_ACROSS_FILESYSTEM not set).
[INFO] ... git status after update is
fatal: not a git repository (or any parent up to mount point /calibre-web)
Stopping at filesystem boundary (GIT_DISCOVERY_ACROSS_FILESYSTEM not set).
[INFO] Config directory option is ACTIVATED
> due this the directory /calibre-web/config will be used to store the configuration
[INFO] Change the ownership of /calibre-web/config (including subfolders) to calibre:calibre
[INFO] Checking permissions of the config directory: /calibre-web/config
> Output is: 755 calibre 100 calibre 1029
> Permissions: 755
> Assigned group: calibre
ln: failed to create symbolic link '/calibre-web/app/gdrive.db': File exists
> Assigned group ID: 100
> Assigned owner: calibre
> Assigned owner ID: 1029
> Using permissions for checks: 0755
> The user calibre:1029 is the owner and has write access at /calibre-web/config
[INFO] 'app.db' and 'gdrive.db' will be linked into /calibre-web/config
> 'app.db' link /calibre-web/app/app.db exists already and won't be recreated
> create 'gdrive.db' link /calibre-web/app/gdrive.db assigned to source /calibre-web/config/gdrive.db
> change the ownership of /calibre-web/app/gdrive.db to calibre:calibre
[INFO] Checking permissions of the books directory: /books
> Output is: 772 calibre 100 UNKNOWN 1026
> Permissions: 772
> Assigned group: calibre
> Assigned group ID: 100
> Assigned owner: UNKNOWN
> Assigned owner ID: 1026
> Using permissions for checks: 0772
> Everyone has write access at /books
[INFO] The mapped volume for /books contains a Calibre database file 'metadata.db' which will be used
[INFO] The kindlegen directory exist already and will be used: /calibre-web/kindlegen
[INFO] Kindlegen application exists already in directory: /calibre-web/kindlegen
[INFO] kindlegen (Amazon Kindle Generator) will be linked into /calibre-web/app/vendor
> kindlegen link /calibre-web/app/vendor/kindlegen exists already and won't be recreated
[INFO] Creating directory for temporary directories and files: /tmp
[INFO] Change the ownership of /tmp (including subfolders) to calibre:calibre
[INFO] Launching Calibre-Web ...
[2019-02-17 10:43:30,762] INFO in web: Starting Calibre Web...
[2019-02-17 10:43:31,293] INFO in server: Starting Gevent server
```
