# rutorrent-docker
[hub]: https://hub.docker.com/r/thevisad/rutorrent/


A full featured install of some of the most popular features for rutorrent and the supporting services. This install was designed specifically for the seedbox-from-scratch Mod 2 to replace the system installed version.
[Seedbox from scratch Mod 2 Github](https://github.com/thevisad/seedbox-from-scratch) 
[Rutorrent Github](https://github.com/thevisad/rutorrent-docker)
[Rutorrent Github](https://github.com/Novik/ruTorrent)



## Usage

```
docker create --name rutorrent  \
-h ${SYSTEMHOSTNAME}  \
-v <path to data>:/downloads  \
-v <path to data>:/config  \
-e PGID=id -u <username>  \
-e PUID=id -g <username>  \
-p <external port>:443  \
-p <external port>:80  \
-p 51413:51413  \
-p 6881:6881  \
-p 5000:5000  \
-v <path to local ssl certs>:/etc/ssl/certs:ro  \
-v <path to localtime>:/etc/localtime:ro  \
thevisad/rutorrent
```

## Parameters

`The -v and -p parameters are remapping host to container services and allow access to the outside world. These are represented by a colon, the left hand side is the host and the right the container side. 
For example a port remapping of -p 8080:80 (-p external:internal) would expose the containers port 80 to the host and would be accessible from the outside world. Browsing to http://localhost:8080 on the host will present you the site hosted at port 80 in the container. 


* `-p 80` - the port(s)
* `-p 5000` - the port(s)
* `-p 51413` - the port(s)
* `-p 6881/udp` - the port(s)
* `-v /config` - where rutorrent should store it's config files
* `-v /downloads` - path to your downloads folder
* `-e PGID` for GroupID - see below for explanation
* `-e PUID` for UserID - see below for explanation
* `-e TZ` for timezone information, eg Europe/London

It is based on alpine linux with s6 overlay, for shell access while the container is running do `docker exec -it rutorrent /bin/bash`.

### User / Group Identifiers

To avoid issues with file permissions betweeo host and container we add in the -e PGID commands to map the external user owner on the host to the internal owner on the container. 

In this instance `PUID=1001` and `PGID=1001`. To find yours use `id user` as below:

```
  $ id <dockeruser>
    uid=1001(dockeruser) gid=1001(dockergroup) groups=1001(dockergroup)
```

## Setting up the application

The web ui can be found at `<your-ip>:80` , configuration files for rtorrent are in /config/rtorrent, php in config/php and for the web ui in /config/rutorrent/settings.

`An settings changed by the user through the panel in ruTorrent, are only valid until rtorrent restarts. The settings will reset to the defaults configured in the rtorrent config file (/config/rtorrent/rtorrent.rc).`

`** It should be noted that this container will create subfolders ,completed, incoming and watched in the /downloads volume.**`

** The Port Assignments and configuration folder structure has been changed from the previous ubuntu based versions of this container and we recommend a clean install **

Umask can be set in the /config/rtorrent/rtorrent.rc file by changing value in `system.umask.set` 

## Info

* Shell access whilst the container is running: `sudo docker exec -i -t rutorrent /bin/bash`
* To monitor the logs of the container in realtime: `docker logs -f rutorrent`

* container version number 

`docker inspect -f '{{ index .Config.Labels "build_version" }}' rutorrent`

* image version number

`docker inspect -f '{{ index .Config.Labels "build_version" }}' thevisad/rutorrent`

