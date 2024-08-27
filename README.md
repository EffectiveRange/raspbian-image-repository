
[![Validate Configuration](https://github.com/EffectiveRange/raspbian-image-repository/actions/workflows/validate-config.yml/badge.svg)](https://github.com/EffectiveRange/raspbian-image-repository/actions/workflows/validate-config.yml)
[![Generate image](https://github.com/EffectiveRange/raspbian-image-repository/actions/workflows/generate-image.yml/badge.svg)](https://github.com/EffectiveRange/raspbian-image-repository/actions/workflows/generate-image.yml)

# raspbian-image-repository

Custom-built Raspberry Pi OS images

## Table of contents

- [Features](#features)
- [Generate your custom images](#generate-your-custom-images)
- [Configure your custom images](#configure-your-custom-images)
    - [Target configuration properties](#target-configuration-properties)
    - [Example configuration](#example-configuration)
- [Install your custom images to an SD card](#install-your-custom-images-to-an-sd-card)

## Features

- [x] Custom Raspberry Pi OS images
- [x] Custom APT sources (with GPG key and key server support)
- [x] Custom pre-installed packages (from APT repositories, GitHub releases, and direct URLs)
- [x] Custom boot command line options
- [x] Custom boot configuration options
- [x] Custom target build stage (default is `2` which results in the `lite` image)

## Generate your custom images

1. Fork this repository

   ![image](https://github.com/EffectiveRange/raspbian-image-repository/assets/171684354/a61f2969-2638-421f-8851-c55a77d798cd)

2. Clone your fork
    ```bash
    git clone https://github.com/<your-username>/raspbian-image-repository.git
    ```

3. Change `config/target-config.json` as per your requirements
   (See [Configure your custom images](#configure-your-custom-images) section)

4. Commit and push the changes

5. Go to `Actions` tab in your forked repository and select `Generate image` workflow

   ![image](https://github.com/EffectiveRange/raspbian-image-repository/assets/171684354/abbe853a-95c2-4ecc-a8f5-0a2a10a87125)

6. Click on `Run workflow` button, set `Image target name` to the target name in `config/target-config.json`

   ![image](https://github.com/EffectiveRange/raspbian-image-repository/assets/171684354/9a65fed0-3993-423b-ae3a-548c0f55c641)

7. Click on `Run workflow` button and wait for the workflow to complete

8. Go to the `Releases` page

   ![image](https://github.com/EffectiveRange/raspbian-image-repository/assets/171684354/ab810caf-7e4c-4b7a-a97c-9476379b5f56)

9. Download the generated image

   ![image](https://github.com/EffectiveRange/raspbian-image-repository/assets/171684354/ff6c209f-4f08-457f-89a2-aa6f43aeab3f)

10. Install your custom images to an SD card
    (See [Install your custom images to an SD card](#install-your-custom-images-to-an-sd-card) section)

## Configure your custom images

The configuration file is located at `config/target-config.json`. It contains a list of target configurations.

### Target configuration properties

Each configuration has the following properties:

- `name`: The name of the configuration to use as a reference
- `version`: The version of the configuration to distinguish between different versions of the same configuration
- `reference`: The reference (tag or commit hash) of the official Raspberry Pi
  OS [pi-gen](https://github.com/RPi-Distro/pi-gen.git) repository to be used as a base
- `stage`: The number of the target build stage to reach (optional, default is `2` which results in the `lite` image)
- `sources`: A list of custom APT sources to be added to the image (optional)
    - `name`: The name of the source to use as a reference
    - `source`: The source entry
    - `key_id`: The GPG key ID
    - `key_file`: The GPG key file URL (optional)
    - `key_server`: The GPG key server (optional)
- `packages`: A list of custom packages to be installed on the image
    - `package`: The name of the package
    - `version`: The version of the package (optional)
    - `file_url`: The URL of the package file (optional)
    - `release`: The release information of the package (optional)
        - `owner`: The owner of the repository
        - `repo`: The name of the repository
        - `tag`: The tag of the release
        - `matcher`: The matcher to filter the package file
        - `token`: The GitHub token to access a private repository, supports environment variable reference when `$` is
          used like `$GITHUB_TOKEN` to avoid putting the access token directly in the configuration file (optional)
- `boot_cmdline`: A list of boot command line parameters to be added to the `cmdline.txt` file (optional)
- `boot_config`: A list of boot configuration parameters to be added to the `config.txt` file (optional)

### Example configuration

A minimalist configuration to add an extra package to your image:

```json
{
  "name": "bookworm-lite-with-dns-server",
  "version": "1.0.0",
  "reference": "2024-07-04-raspios-bookworm-arm64",
  "packages": [
    {
      "package": "dnsmasq"
    }
  ]
}
```

A more complex configuration to add custom packages, APT sources, and boot configuration:

```json
{
  "name": "edge-pi-zero",
  "version": "1.0.0",
  "reference": "2024-03-12-raspios-bullseye",
  "sources": [
    {
      "name": "effective-range",
      "source": "deb http://aptrepo.effective-range.com stable main",
      "key_id": "C1AEE2EDBAEC37595801DDFAE15BC62117A4E0F3",
      "key_file": "http://aptrepo.effective-range.com/dists/stable/public.key",
      "key_server": "keyserver.ubuntu.com"
    }
  ],
  "packages": [
    {
      "package": "wifi-manager",
      "release": {
        "owner": "EffectiveRange",
        "repo": "wifi-manager",
        "tag": "latest",
        "matcher": "*armhf.deb"
      }
    },
    {
      "package": "picprogrammer",
      "release": {
        "owner": "EffectiveRange",
        "repo": "pic18-q20-programmer",
        "tag": "v0.3.0",
        "matcher": "*armhf.deb"
      }
    },
    {
      "package": "filebeat",
      "version": "8.12.2",
      "file_url": "https://github.com/EffectiveRange/elastic-beats-armhf-deb/releases/download/v8.12.2/filebeat-8.12.2-armv7l.deb"
    }
  ],
  "boot_cmdline": [
    "modules-load=dwc2,g_ether"
  ],
  "boot_config": [
    "enable_uart=1",
    "dtoverlay=dwc2"
  ]
}
```

## Install your custom images to an SD card

Flash the image to an SD card using [Raspberry Pi Imager](https://www.raspberrypi.org/software/)

1. Download and install the Raspberry Pi Imager

   ![image](https://github.com/EffectiveRange/raspbian-image-repository/assets/171684354/f255a426-f909-4625-a15d-9515e804050e)

2. Open the Raspberry Pi Imager

   ![image](https://github.com/EffectiveRange/raspbian-image-repository/assets/171684354/65ec51a5-bc7a-42fb-ade0-ae8794420d4d)

3. Choose your `Raspberry Pi Device` type and `Storage` volume

4. Click `Choose OS` and select `Use custom` option

   ![image](https://github.com/EffectiveRange/raspbian-image-repository/assets/171684354/82eed8c7-9af3-42fe-94d9-7d2fab07e430)

5. Select the custom image file you downloaded

   ![image](https://github.com/EffectiveRange/raspbian-image-repository/assets/171684354/7e51ee4d-eb73-4c8a-b102-9202baff4d7a)

6. Click `NEXT` and `EDIT SETTINGS` to configure the `Wi-Fi`, `SSH` and other settings

   ![image](https://github.com/EffectiveRange/raspbian-image-repository/assets/171684354/b442515d-ed16-4b85-b53d-1f1d2e114ea2)

7. Click `SAVE` to apply the configuration

   ![image](https://github.com/EffectiveRange/raspbian-image-repository/assets/171684354/815201d4-fe96-45e6-8ed9-66432643d7e4)

8. Click `YES` to flash the image to the SD card

   ![image](https://github.com/EffectiveRange/raspbian-image-repository/assets/171684354/17da86f8-8ee4-4e13-a833-dbba717af63a)

9. After the flashing process is completed, insert the SD card into your Raspberry Pi and power it on

   ![image](https://github.com/EffectiveRange/raspbian-image-repository/assets/171684354/ce647b31-3e07-4cd8-a2be-db250da62649)
