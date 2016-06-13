# devopsplayground4-packer

## Creating machine and container images for multiple platforms from a single source configuration

### Pre-requisites

- Create a folder named  `packer` in your PC

- Copy files from `Packer_Data` folder in the USB drive under the `packer` folder that was just created.

- Navigate the `Packer_version` folder in the USB drive and look for your OS and copy the 32 or 64 bit version in your local `packer` folder (this can also be donwnloaded from https://www.packer.io/downloads.html)

- Download Virtualbox from https://www.virtualbox.org/wiki/Downloads and install.

The structure of the packer folder should be the following

**Note:** The following image represents windows OS
![Folder Structure](img01.jpg)

## Using Packer


1. Under the command window navigate to the packer folder
- While inside the folder in the command window type:
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

## Working with packer commands  
**Note:** The following commands to call Packer works on windows, please make sure you are using the one that corresponds to your OS

### First lets make sure that the template is working properly

Run the following command under the console window

`packer validate template.json`

You should be getting the following output:

```
Failed to parse template: Error parsing JSON: invalid character '"' after object key:value pair
At line 6, column 4 (offset 66):
    5:          "PASS": "forest"
    6:          "
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

  BOOT_COMMAND  = <enter><wait><f6><esc><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs>
  CHECKSUM      = b8b172cbdf04f5ff8adc8c2c1b4007ccf66f00fc6a324a6da6eba67de71746f6
  CHECKSUM_TYPE = sha256
  CPUS          = 1
  DISK_SIZE     = 40960
  GUEST_IP      = 10.0.2.15
  GUEST_PORT    = 80
  HOST_IP       = 127.0.0.1
  HOST_PORT     = 8080
  ISO_URL       = ubuntu-16.04-server-amd64.iso
  MEMORY        = 1024
  OS_TYPE       = Ubuntu_64
  OUTPUT_FOLDER = MY_VM
  PASS          = forest
  Protocol      = tcp
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
		"OS_TYPE": "Ubuntu_64",
    "OUTPUT_FOLDER": "MY_VM",
		"MEMORY": "1024",
		"CPUS": "1",
		"DISK_SIZE": "40960",
		"Protocol": "tcp",
		"HOST_IP": "127.0.0.1",
		"HOST_PORT": "8080",
		"GUEST_IP": "10.0.2.15",
		"GUEST_PORT": "80",
		"ISO_URL": "ubuntu-16.04-server-amd64.iso",
		"CHECKSUM": "b8b172cbdf04f5ff8adc8c2c1b4007ccf66f00fc6a324a6da6eba67de71746f6",
		"CHECKSUM_TYPE": "sha256",
		"BOOT_COMMAND": "<enter><wait><f6><esc><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs>"
	}
```

###

Let's try to validate `template.json` and see what happens.

Now it is not recognizing the variables. In order to pass them to the template we need to use the `-var-file` tag.

#### 2) From The Command Line

To set variables from the command line, the `-var` flag is used as a parameter to `packer build`

## Building the template

Finally with all the information given we can build our template by passing the variables located under the `config.json` file and we are also going to modify one of the values using the `-var` flag

We want to change the name of the folder were our image is going to be created. The variable responsible for this is `OUTPUT_FOLDER`. By defauld it has the value `MY_VM` so we are going to change it to our name.

The command should look like this:

`packer build -var-file=config.json -var 'OUTPUT_FOLDER=XXXXX' template.json`

Another way to put it would be:

```
$ packer build \
    -var-file=config.json \
    -var 'OUTPUT_FOLDER=XXXXX' \
    template.json
```
