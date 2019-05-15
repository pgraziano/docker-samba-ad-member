# GOAL

Share a single linux directory, read-only, with a select group of users in an Active Directory domain.


# REQUIREMENTS

- use docker
- specify all details on the command line
- be able to enter container, make changes, and restart the service

# USAGE

Starting the container (will join AD and share the /shared/ folder):
```
docker run -it -d --add-host "foo foo.mydomain.local":192.168.0.6 \
 --name foo \
 --hostname foo \
 -e SHARENAME=stuff \
 -e SHAREGROUP=StuffUsers \
 -e TZ=America/Los_Angeles \
 -e DOMAIN_NAME=mydomain.local \
 -e ADMIN_SERVER=myad.mydomain.local \
 -e WORKGROUP=mydomain \
 -e AD_USERNAME=myAdminAccount \
 -e AD_PASSWORD=myAdminPassword \
 -v /path/on/linux:/shared \
 -p 137:137/udp \
 -p 138:138/udp \
 -p 139:139/tcp \
 -p 445:445/tcp \
 docker-samba-ad-member
```

Useful commands:
```
# enter the container
docker exec -it foo /bin/bash

# edit smb.conf from inside the container
vim /etc/samba/smb.conf

# restart samba, from inside the container
supervisorctl restart smbd
```

# TROUBLESHOOTING

If you have to destroy the container and start a new one, you'll probably have to delete the computer account from Active Directory before starting the new container.

