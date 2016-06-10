# devopsplayground4-packer

## Creating machine and container images for multiple platforms from a single source configuration

### Pre-requisites

1. Create Packer folder named  `packer`
2. Navigate into the packer folder and download Packer into it and unzip (tip: To unzip in Linux from command line use the `unzip` package) https://www.packer.io/downloads.html
3. While inside the folder in the command window type:
 1. Linux `./packer`
 1. Windows `packer`

Make sure the following comes up to know that Packer is working.

```
usage: packer [--version] [--help] <command> [<args>]

Available commands are:
    build       build image(s) from template
    fix         fixes templates from old versions of packer
    inspect     see components of a template
    push        push a template and supporting files to a Packer build service
    validate    check that a template is valid
    version     Prints the Packer version

```
3. Download Virtualbox https://www.virtualbox.org/wiki/Downloads and install.
4. Download http://releases.ubuntu.com/16.04/ubuntu-16.04-server-amd64.iso and place it in the packer folder.
