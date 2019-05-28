# GOAL

Share a single linux directory, read-only, with a select group of users in an Active Directory domain.


# REQUIREMENTS

- use docker
- specify all details on the command line
- be able to enter container, make changes, and restart the service

# BUILDING DOCKER IMAGE

Before you can run this, you must build the image locally.  The following command will clone the repo, and build a local image named **docker-samba-ad-member**.

```
git clone https://github.com/pgraziano/docker-samba-ad-member.git
cd docker-samba-ad-member/
docker build -t docker-samba-ad-member .
```

If you make any changes to **docker-entrypoint.sh**, don't forget to rebuild the image.


# USAGE

Starting the container (will join AD and share the /shared/ folder):
```
docker run -it -d --add-host "foo foo.mydomain.local":192.168.0.6 \
 --name foo \                   # container name
 --hostname foo \               # hostname
 -v /path/on/linux:/shared \    # path to linux directory being shared
 -e SHARENAME=stuff \           # share name seen by windows users
 -e SHAREGROUP=StuffUsers \     # AD group with read-only share permissions
 -e TZ=America/Los_Angeles \
 -e DOMAIN_NAME=mydomain.local \
 -e ADMIN_SERVER=myad.mydomain.local \
 -e WORKGROUP=mydomain \
 -e AD_USERNAME=myAdminAccount \
 -e AD_PASSWORD=myAdminPassword \
 -p 137:137/udp \
 -p 138:138/udp \
 -p 139:139/tcp \
 -p 445:445/tcp \
 docker-samba-ad-member
```

After starting the container, you users should be able to browse the folder share using the docker host's IP address in a UNC path (eg: \\192.168.0.100).  To use a UNC hostname, such as \\foo, you must add "foo" to the AD DNS, mapping it to the docker host's IP address.


# Useful commands:

```
# enter the container
docker exec -it foo /bin/bash

# check status of services
supervisorctl status nmbd
supervisorctl status smbd

# edit smb.conf from inside the container, and restart samba
vim /etc/samba/smb.conf
supervisorctl restart smbd
```

# TROUBLESHOOTING

If you have to destroy the container and start a new one, you'll probably have to delete the computer account from Active Directory before starting the new container.

