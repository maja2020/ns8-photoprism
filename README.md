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

## Configure nethserver module
```
api-cli run configure-module --agent module/photoprism1 --data - <<EOF
{
  "host": "myphotoprism.domain.com",
  "http2https": true,
  "lets_encrypt": false
}
EOF
```
The above command will:
- Start and configure the photoprism instance with the right traefik settings. 
- First start wil create an empty mysql database and configure photoprism with default settings. 
- The default photoprism login is user:admin passwd:insecure. This is the photoprism default. 
**<span style="color:red;">Change password ASAP at first logon to photoprism</span>** 
```
  -> Settings - Account - Change password.
```
## Configure photoprism
Photoprism has a ton of configuration options. See https://dl.photoprism.app/podman/docker-compose.yml for a complete list.
- PHOTPRISM_SITE_URL parameter will automaically be set to the configured "host" parameter. Do not change.
- Many of the configuration options can be changed in de photoprism UI Settings.
- PHOTOPRISM INIT forces software updates at container startup and slows down container startup. Is untested and therefore disabled.
- Video transcoding settings are not enabled, is a whole different category and hardware dependent. Slow, but no specific hardware requirements: it just works.

View current photoprism settings: 
```
runagent -m photoprism1 cat photoprism.env
```
Edit current photoprism settings:
```
runagent -m photoprism1 vim photoprism.env
```

## Uninstall

To uninstall the instance:

    remove-module --no-preserve photoprism1


## Update
To update the instance (command line only for now)

    api-cli run update-module --data '{"module_url":"ghcr.io/maja2020/photoprism","instances":["photoprism1"],"force":true}'        
and restart the application:
    systemctl stop user@$(id -u photoprism1)
    systemctl start user@$(id -u photoprism1)

## Use external disks
The photoprism library are growing fast. About 4000 random original pictures and movies claims about 20 Gb. My nethserver nodes have a boot volume of 40 Gb. If you want to use local node storage, do nothing.

Or: please read following links:
[redirect-podman named volume mount points](https://github.com/NethServer/ns8-docs/blob/main/docs/tutorial/disk_usage.md#redirect-podman-named-volume-mount-points-named-volume-disk) 
[this](https://nethserver.github.io/ns8-core/modules/volumes/) in the dev manual

The volume assignments on the local host the module expects are:
- photoprism-storage        Sidecar files, config, album, user and cache (thumbs) location
- photoprism-originals      The originals photo library location
- photoprism-import         The import location
The msql database is stored on the local node.

Add your local mount points for the external shares in /etc/fstab
''' 
volumectl add-volume photoprism-originals --target /mnt/<ext. server/share1>/dir1 --for photoprism-originals 
volumectl add-volume photoprism-import --target /mnt/<ext. server/share1>/dir2 --for photoprism-import 
volumectl add-volume photoprism-storage --target /mnt/<ext. server/share2>/dir3 --for photoprism-storage
'''
Photoprism import moves files around, and writes a lot of data (cache files, sidecar etc.): the import and originals
- import folder may be located on the same share as the originals folder for performance reasons
- storage may be located on fast storage (SSD/NVME).
Example fstab mounts tested with Truenas SMB shares (so you can access the originals folder with foto editing or management software):

Pay special attention to the import folder: photoprism cannot handle imports from a directory inside the "originals".path: [photoprism docs](https://docs.photoprism.app/known-issues/#nested-import-folder).

If you use smb mounts, install cifs-utils:  '''sudo dnf install cifs-utils''' or expect the error "no route to host"

