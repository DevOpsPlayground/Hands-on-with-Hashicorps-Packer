# devopsplayground4-packer

## Creating machine and container images for multiple platforms from a single source configuration

### Pre-requisites

- Create a folder named  `packer` in your PC

- Copy files from `Packer_Data` folder in the USB drive under the `packer` folder that was just created.

- Navigate the `Packer_version` folder in the USB drive and look for your OS and copy the 32 or 64 bit version in your local `packer` folder (this can also be donwnloaded from https://www.packer.io/downloads.html)

- Download Virtualbox from https://www.virtualbox.org/wiki/Downloads and install.

The structure of the packer folder should be the following

**Note:** The following image represents windows OS

![Folder Structure](img/img01.JPG)

## Using Packer


1. Under the command window navigate to the packer folder
- While inside the folder in the command window type:
 1. Linux/Mac/Git Bash `./packer`
 - Windows `packer`

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
## Working with packer commands  
**Note:** The following commands to call Packer works on windows, please make sure you are using the one that corresponds to your OS

### First lets make sure that the template is working properly

Run the following command under the console window

`packer validate template.json`

You should be getting the following output:

```
Failed to parse template: Error parsing JSON: invalid character '"' after object key:value pair
At line 7, column 4 (offset 96):
    6:          "OUTPUT_FOLDER": "MY_VM"
    7:          "
         ^
```

This means we have a syntax error at line 6. Before we can run our template we need to make sure this is fix.

Once you had the systax corrected `validate` again:

`packer validate template.json`

This time the output should be:

`Template validated successfully.`

### Now lets inspect our template

In our command windows lets run:

`packer inspect template.json`

Packer will show us the structure of our files

```
Optional variables and their defaults:

  CHECKSUM      = ad4e8c27c561ad8248d5ebc1d36eb172f884057bfeb2c22ead823f59fa8c3dff
  CHECKSUM_TYPE = sha256
  CPUS          = 1
  DISK_SIZE     = 5120
  GUEST_IP      = 10.0.2.15
  GUEST_PORT    = 80
  HOST_IP       = 127.0.0.1
  HOST_PORT     = 8080
  ISO_URL       = debian-8.5.0-amd64-netinst.iso
  MEMORY        = 512
  OS_TYPE       = Debian_64
  OUTPUT_FOLDER = MY_VM
  PASS          = forest
  PROTOCOL      = tcp
  USER          = forest

Builders:

  virtualbox-iso

Provisioners:

  shell

Note: If your build names contain user variables or template
functions such as 'timestamp', these are processed at build time,
and therefore only show in their raw form here.

```

### Working with user variables

User variables can be defined inside the template in the `variables` section with the following format:

```
"variables": {
   "aws_access_key": "",
   "aws_secret_key": ""
 },
```

This is how we have our variables currently set up in our template.

### Packer has two other methods for setting variables

#### 1) From a file

Variables can also be set from an external JSON file. The  `-var-file` flag reads a file.

We are going to create another file called `config.json` in the packer folder and we are going to put the variable list from our `template.json` (remember to delete the variables from `template.json`)

`config.json` should look like this:

```
{
  "USER": "forest",
  "PASS": "forest",
  "OUTPUT_FOLDER": "MY_VM",
  "OS_TYPE": "Debian_64",
  "MEMORY": "512",
  "CPUS": "1",
  "DISK_SIZE": "5120",
  "PROTOCOL": "tcp",
  "HOST_IP": "127.0.0.1",
  "HOST_PORT": "8080",
  "GUEST_IP": "10.0.2.15",
  "GUEST_PORT": "80",
  "ISO_URL": "debian-8.5.0-amd64-netinst.iso",
  "CHECKSUM": "ad4e8c27c561ad8248d5ebc1d36eb172f884057bfeb2c22ead823f59fa8c3dff",
  "CHECKSUM_TYPE": "sha256"
}
```

Let's try to validate `template.json` and see what happens.

Now it is not recognizing the variables. In order to pass them to the template we need to use the `-var-file` tag.

`Packer validate -var-file=config.json template.json`

#### 2) From The Command Line

To set variables from the command line, the `-var` flag is used as a parameter to `packer build`

## Building the template

Finally with all the information given we can build our template by passing the variables located under the `config.json` file and we are also going to modify one of the values using the `-var` flag

We want to change the name of the folder were our image is going to be created. The variable responsible for this is `OUTPUT_FOLDER`. By defauld it has the value `MY_VM` so we are going to change it to our name.

The command should look like this:

`packer build -var-file=config.json -var 'OUTPUT_FOLDER=XXXXX' template.json`

Another way to put it would be:  
**Note:** For windows use `^` (caret) instead of `\`

```
$ packer build \
    -var-file=config.json \
    -var 'OUTPUT_FOLDER=XXXXX' \
    template.json
```

After pressing Enter VirtualBox will begin creating the VM and running it in headless mode. Please be careful not to open up a windows with the running machine since interaction with it may cause a conflict while generating the image.

<img src=img/img02.JPG" width="250">

## PARALLEL BUILDS

Now that we have our template to create a VM under VirtualBox we can use the same configuration to do it under other tools in parallel. Here's an example of the syntax to add VMware to our build.

```
"builders": [{
    "type": "virtualbox-iso",
    ...
    ...
    "vboxmanage": [
      [ "modifyvm", "{{.Name}}", "--memory", "512" ],
      [ "modifyvm", "{{.Name}}", "--cpus", "1" ]
    ]
  },{
    "type": "vmware-iso",
    ...
    ...
    "vmx_data": {
      "memsize": "512",
      "numvcpus": "1",
    }
  }],
```

```
"provisioners": [
  {
    "type": "shell",
    "execute_command": "echo 'password'|sudo -S sh '{{.Path}}'",
    "override": {
      "virtualbox-iso": {
        "scripts": [
          ...
          ...
        ]
      },
      "vmware-iso": {
        "scripts": [
          ...
          ...
        ]
      }
    }
  }
],
```
