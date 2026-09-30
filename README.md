# PHOTOPRISM

https://www.photoprism.app/
 PhotoPrism® is an AI-Powered Photos App for the Decentralized Web.
 It makes use of the latest technologies to tag and find pictures automatically without getting in your way.

This is the free version. Additional functionality, available in support plans like multi user/backend auth/AI features, are not supported (yet).

## Install

Instantiate the module with:

    add-module ghcr.io/maja2020/photoprism:latest 1

The output of the command will return the instance name.
Output example:

    {"module_id": "photoprism1", "image_name": "photoprism", "image_url": "ghcr.io/maja2020/photoprism:0.1.1"}

## Configure
```
api-cli run configure-module --agent module/photoprism1 --data - <<EOF
{ "website_name": "Mytestname",
  "website_title": "MytestTitle",
  "website_caption": "MytestCaption",
  "admin_user":"admin",
  "admin_password":"TheveryLongSentenceIllNeverForget",
  "website_description":"MytestDescription",
  "website_author":"MyTestAuthor",
  "host": "webtest.incanto.info",
  "http2https": true,
  "lets_encrypt": false
}
EOF
```
The values admin_password, host, lets_encrypt and http2https are mandatory.
The above command will:
- Start and configure the photoprism instance with the right traefik settings. 
- First start wil create an empty mariadb database and configure photoprism with default settings. 
- If you used above example password: **<span style="color:red;">Change password ASAP at first logon</span>**. Hackers read this repository too.
```
  -> Settings - Account - Change password.
```
## Configure photoprism
Photoprism has a ton of configuration options. See https://dl.photoprism.app/docker/docker-compose.yml for a complete list.
- PHOTPRISM_SITE_URL parameter will automatically be set to the configured "host" parameter. Do not change.
- Many of the configuration options can be changed in de photoprism UI (advanced) settings pages.
- Photoprism forces software updates at container startup. Check the logs. 
- Video transcoding settings and AI integration are not enabled, this is a whole different category and hardware dependent. Adjust as needed in photoprism.env.

View current photoprism settings:
```
runagent -m photoprism<instance_id> cat photoprism.env
runagent -m $(ls /home | grep photoprism) cat photoprism.env
```
View photoprism logs:
```
journalctl _UID=$(id -u photoprism<instance_id>)
journalctl _UID=$(id -u $(ls /home|grep photoprism))
```

Edit current photoprism settings:
```
runagent -m photoprism<photoprism<instance_id> vim photoprism.env
runagent -m $(ls /home | grep photoprism) vim photoprism.env
```

## Uninstall

To uninstall the instance:
```
remove-module --no-preserve photoprism1
```

## Update
To update the instance (command line only for now)
```
api-cli run update-module --data '{"module_url":"ghcr.io/maja2020/photoprism","instances":["photoprism1"],"force":true}'        
```
and restart the application:
```
systemctl stop user@$(id -u photoprism<instance_id>) &&sleep 10 && systemctl start user@$(id -u photoprism<instance_id>)
```

## Use external disks
The photoprism library storage grows fast. About 4000 random original pictures and small movies claim about 20 Gb. My nethserver nodes have 40 Gb disks. 

Configure the host to use external storage for photoprism: please read following links:
[redirect-podman named volume mount points](https://github.com/NethServer/ns8-docs/blob/main/docs/tutorial/disk_usage.md#redirect-podman-named-volume-mount-points-named-volume-disk) 
[this](https://nethserver.github.io/ns8-core/modules/volumes/) in the dev manual

The volume assignments on the local host the module expects are:
- photoprism-storage        Sidecar files, config, album, user and cache (thumbs) location
- photoprism-originals      The originals photo library location
- photoprism-import         The import location
The mariadb database is stored on the local node itself.

Add your local mount points for the external shares in /etc/fstab and make sure its working.
```
volumectl add-volume photoprism-originals --target /mnt/<ext. server/share1>/dir1 --for photoprism-originals 
volumectl add-volume photoprism-import --target /mnt/<ext. server/share1>/dir2 --for photoprism-import 
volumectl add-volume photoprism-storage --target /mnt/<ext. server/share2>/dir3 --for photoprism-storage
```
Pay special attention to the import folder: photoprism cannot handle imports from a directory inside the "originals".path: [photoprism docs](https://docs.photoprism.app/known-issues/#nested-import-folder).

Photoprism import moves files around, and writes a lot of data (cache files, sidecar etc.):
- import folder may be located on the same share as the originals folder for performance reasons when moving files.
- storage may be located on fast storage (SSD/NVME).
Example fstab mounts tested with Truenas SMB shares (file access via the local network of the originals folder):
```
ToBeDone
```

If you use smb mounts, you may want to install cifs-utils: ```sudo dnf install cifs-utils```
or expect the very descriptive error "no route to host".

