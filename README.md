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

    {"module_id": "photoprism1", "image_name": "photoprism", "image_url": "ghcr.io/maja2020/photoprism:latest"}

## Configure
Photoprism has a ton of configurable options: 1* nonconfigurable options like database settings
Non configurable options: mysql database settings, site url 

Example:
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
- Start and configure the photoprism instance with the right treafik settings:
- First start wil create an empty mysql database and configure photoprism with default settings. Settings are stored in .config environment files. 
- The default login is user:admin passwd:insecure. Change passowrd at first logon.

To change a configuration option: 

Launch `configure-module`, by setting the following parameters:
- `<MODULE_PARAM1_NAME>`: <MODULE_PARAM1_DESCRIPTION>
- `<MODULE_PARAM2_NAME>`: <MODULE_PARAM2_DESCRIPTION>
- ...

Example:

    api-cli run module/photoprism1/configure-module --data '{"}'


## Uninstall

To uninstall the instance:

    remove-module --no-preserve kickstart1

## Testing

Test the module using the `test-module.sh` script:


    ./test-module.sh <NODE_ADDR> ghcr.io/nethserver/kickstart:latest

The tests are made using [Robot Framework](https://robotframework.org/)

## UI translation

Translated with [Weblate](https://hosted.weblate.org/projects/ns8/).

To setup the translation process:

- add [GitHub Weblate app](https://docs.weblate.org/en/latest/admin/continuous.html#github-setup) to your repository
- add your repository to [hosted.weblate.org]((https://hosted.weblate.org) or ask a NethServer developer to add it to ns8 Weblate project
