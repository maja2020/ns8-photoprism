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

To change a configuration option launch `configure-module`, type env-settings in lowercase.
Example:
```
runagent -m photoprism1 python ../bin/configure_environment_vars <<EOF
{
	"photoprism_site_caption": "caption",
	"photoprism_site_description": "site_description",
	"photoprism_site_author": "Yourname or Alias"
}
EOF

```

## Uninstall

To uninstall the instance:

    remove-module --no-preserve photoprism1


## Update
To update the instance (command line only for now)

    api-cli run update-module --data '{"module_url":"ghcr.io/maja2020/photoprism","instances":["photoprism1"],"force":true}'        

## Testing

Test the module using the `test-module.sh` script:

    ./test-module.sh <NODE_ADDR> ghcr.io/maja2020/photoprism:latest

The tests are made using [Robot Framework](https://robotframework.org/)

## UI translation

Translated with [Weblate](https://hosted.weblate.org/projects/ns8/).

To setup the translation process:

- add [GitHub Weblate app](https://docs.weblate.org/en/latest/admin/continuous.html#github-setup) to your repository
- add your repository to [hosted.weblate.org]((https://hosted.weblate.org) or ask a NethServer developer to add it to ns8 Weblate project
