# aws_cli_v2_docker

#Setting up and using the official AWS CLI version 2 Docker image#

This topic describes how to run, version control, configure and test the AWS CLI version 2 on Docker. For more information on how to use Docker, see [Docker's documentation](https://docs.docker.com/).

Official Docker images provide isolation, portability, and security that AWS directly supports and maintains. This enables you to use the AWS CLI version 2 in a container-based environment without having to manage the installation yourself.


- [Prerequisites](#prerequisites)
- [Pull the official AWS CLI version 2 Docker image](#pull-the-official-aws-cli-version-2-docker-image)
- [Run the official AWS CLI version 2 Docker image](#run-the-official-aws-cli-version-2-docker-image)
- [Use specific versions and tags](#use-specific-versions-and-tags)
- [Update to the latest Docker image](#update-to-the-latest-docker-image)
- [Configuration and credential file settings](#configuration-and-credential-file-settings)
  - [Where are configuration settings stored?](#where-are-configuration-settings-stored)
  - [Set and view configuration settings](#set-and-view-configuration-settings)
- [Share host files, credentials, and configuration](#share-host-files-credentials-and-configuration)
  - [Example 1: Providing credentials and configuration](#example-1-providing-credentials-and-configuration)
  - [Example 2: Downloading an Amazon S3 file to your host system](#example-2-downloading-an-amazon-s3-file-to-your-host-system)
- [Shorten the Docker command](#shorten-the-docker-command)
- [References](#references)

## Prerequisites

You must have Docker installed. For installation instructions, see the [Docker website](https://docs.docker.com/get-docker/).

To verify your installation of Docker, run the following command and confirm there is an output.
```
$ docker --version
Docker version 19.03.1
```

## Pull the official AWS CLI version 2 Docker image

The official AWS CLI version 2 Docker image is hosted on DockerHub in the amazon/aws-cli repository. The first time you use the docker run command, the latest Docker image is downloaded to your computer. Each subsequent use of the docker run command runs from your local copy.

To pull the official AWS CLI version 2 docker image, use this docker run command.

To run the AWS CLI version 2 Docker image, use the docker run command.
```
$ docker run --rm -it amazon/aws-cli <command>
```
This will pull down the image to your local host for the first time, and echo out the version of the AWS CLI version 2 binary.

## Run the official AWS CLI version 2 Docker image

To run the AWS CLI version 2 Docker image, use the docker run command.
```
$ docker run --rm -it amazon/aws-cli <command>
```

This is how the command functions:

* docker run --rm --it amazon/aws-cli – The equivalent of the aws executable. Each time you run this command, Docker spins up a container of your downloaded amazon/aws-cli image, and executes your aws command. By default, the Docker image uses the latest version of the AWS CLI version 2.

  For example, to call the aws --version command in Docker, you run the following.
  ```
  $ docker run --rm -it amazon/aws-cli --version
  aws-cli/2.0.47 Python/3.7.3 Linux/4.9.184-linuxkit botocore/2.0.0dev10
  ```

* --rm – Specifies to clean up the container after the command exits.

* -it – Specifies to open a pseudo-TTY with stdin. This enables you to provide input to the AWS CLI version 2 while it's running in a container, for example, by using the aws configure and aws help commands.

For more information about the docker run command, see the [Docker reference guide](https://docs.docker.com/engine/reference/run/).

## Use specific versions and tags

The official AWS CLI version 2 Docker image has multiple versions you can use, starting with version 2.0.6. To run a specific version of the AWS CLI version 2, append the appropriate tag to your docker run command. The first time you use the docker run command with a tag, the latest Docker image for that tag is downloaded to your computer. Each subsequent use of the docker run command with that tag runs from your local copy.

You can use two types of tags:

* latest – Defines the latest version of the AWS CLI version 2 for the Docker image. We recommend you use the latest tag when you want the latest version of the AWS CLI version 2. However, there are no backward-compatibility guarantees when relying on this tag. The latest tag is used by default in the docker run command. To explicitly use the latest tag, append the tag to the container image name.
  ``` 
  $ docker run --rm -it amazon/aws-cli:latest <command>
  ```

* <major.minor.patch> – Defines a specific version of the AWS CLI version 2 for the Docker image. If you plan to use the Docker image in production, we recommend you use a specific version of the AWS CLI version 2 to ensure backward compatibility. For example, to run version 2.0.6, append the version to the container image name.
  ```
  $ docker run --rm -it amazon/aws-cli:2.0.6
  ```

## Update to the latest Docker image

Because the latest Docker image is downloaded to your computer only the first time you use the docker run command, you need to manually pull an updated image. To manually update to the latest version, we recommend you pull the latest tagged image. Pulling the Docker image downloads the latest version to your computer.
```
$ docker pull amazon/aws-cli:latest
```

## Configuration and credential file settings

You can save your frequently used configuration settings and credentials in files that are maintained by the AWS CLI.

The files are divided into profiles. By default, the CLI uses the settings found in the profile named default. To use alternate settings, you can create and reference additional profiles. For more information on [named profiles, see [Named profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html).

You can override an individual setting by either setting one of the supported environment variables, or by using a command line parameter. For more information on configuration setting precedence, see [Configuration settings and precedence](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-precedence).


### Where are configuration settings stored?

The AWS CLI stores sensitive credential information that you specify with aws configure in a local file named credentials, in a folder named .aws in your home directory. The less sensitive configuration options that you specify with aws configure are stored in a local file named config, also stored in the .aws folder in your home directory.

Storing credentials in the config file
You can keep all of your profile settings in a single file as the AWS CLI can read credentials from the config file. If there are credentials in both files for a profile sharing the same name, the keys in the credentials file take precedence.

These files are also used by the various language software development kits (SDKs). If you use one of the SDKs in addition to the AWS CLI, confirm if the credentials should be stored in their own file.

Where you find your home directory location varies based on the operating system, but is referred to using the environment variables %UserProfile% in Windows and $HOME or ~ (tilde) in Unix-based systems. You can specify a non-default location for the config file by setting the AWS_CONFIG_FILE environment variable to another local path. See [Environment variables to configure the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html) for details.

For example, the files generated by the CLI for a default profile configured with aws configure looks similar to the following.

~/.aws/credentials
```
[default]
aws_access_key_id=AKIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```
~/.aws/config
```
[default]
region=us-west-2
output=json
```
For file examples with multiple named profiles, see [Named profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html).

When you use a shared profile that specifies an AWS Identity and Access Management (IAM) role, the AWS CLI calls the AWS STS AssumeRole operation to retrieve temporary credentials. These credentials are then stored (in ~/.aws/cli/cache). Subsequent AWS CLI commands use the cached temporary credentials until they expire, and at that point the AWS CLI automatically refreshes the credentials.


### Set and view configuration settings

There are several ways to view and set your configuration settings in the files.

Credentials and config file
View and edit your settings by directly editing the config and credentials files in a text editor. For more information see [Where are configuration settings stored?](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html#cli-configure-files-where)

To remove a setting, delete the corresponding setting in your config and credentials files.

* aws configure
  Run this command to quickly set and view your credentials, region, and output format. The following example shows sample values.
  ```
  $ aws configure
  AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
  AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
  Default region name [None]: us-west-2
  Default output format [None]: json
  ```
  For more information see Quick configuration with aws configure

* aws configure set
  You can set any credentials or configuration settings using aws configure set. Specify the profile that you want to view or modify with the --profile setting.

  For example, the following command sets the region in the profile named integ.
  ```
  $ aws configure set region us-west-2 --profile integ
  ```
  To remove a setting, use an empty string as the value, or manually delete the setting in your config and credentials files in a text editor.
  ```
  $ aws configure set cli_pager "" --profile integ
  ```
* aws configure get
  You can retrieve any credentials or configuration settings you've set using aws configure get. Specify the profile that you want to view or modify with the --profile setting.

  For example, the following command retrieves the region setting in the profile named integ.
  ```
  $ aws configure get region --profile integ
  us-west-2
  ```
  If the output is empty, the setting is not explicitly set and uses the default value.

* aws configure import
  This feature is available only with AWS CLI version 2.
  The following feature is available only if you use AWS CLI version 2. It isn't available if you run AWS CLI version 1. For information on how to install version 2, see Installing the AWS CLI version 2.

  Import CSV credentials generated from the AWS web console. A CSV file is imported with the profile name matching the IAM user name.
  ```
  $ aws configure import –csv file://credentials.csv
  ```
* aws configure list
  To list all configuration data, use the aws configure list command. This command displays the AWS CLI name of all settings you've configured, their values, and where the configuration was retrieved from.
  ```
  $ aws configure list
        Name                    Value             Type    Location
        ----                    -----             ----    --------
     profile                <not set>             None    None
  access_key     ****************ABCD  shared-credentials-file    
  secret_key     ****************ABCD  shared-credentials-file    
      region                us-west-2             env    AWS_DEFAULT_REGION
  ```
* aws configure list-profiles
  This feature is available only with AWS CLI version 2.
  The following feature is available only if you use AWS CLI version 2. It isn't available if you run AWS CLI version 1. For information on how to install version 2, see Installing the AWS CLI version 2.

  To list all your profile names, use the aws configure list-profiles command.
  ```
  $ aws configure list-profiles
  default
  test
  ```

## Share host files, credentials, and configuration

Because the AWS CLI version 2 is run in a container, by default the CLI can't access the host file system, which includes configuration and credentials. To share the host file system, credentials, and configuration to the container, mount the host system’s ~/.aws directory to the container at /root/.aws with the -v flag to the docker run command. This allows the AWS CLI version 2 running in the container to locate host file information.
```
$ docker run --rm -it -v ~/.aws:/root/.aws amazon/aws-cli <command>
```
For more information about the -v flag and mounting, see the [Docker reference guide](https://docs.docker.com/storage/volumes/).

### Example 1: Providing credentials and configuration

In this example, we're providing host credentials and configuration when running the s3 ls command to list your buckets in Amazon Simple Storage Service (Amazon S3).
```
$ docker run --rm -ti -v ~/.aws:/root/.aws amazon/aws-cli s3 ls
2020-03-25 00:30:48 aws-cli-docker-demo
```

### Example 2: Downloading an Amazon S3 file to your host system

For some AWS CLI version 2 commands, you can read files from the host system in the container or write files from the container to the host system.

In this example, we download the S3 object s3://aws-cli-docker-demo/hello to your local file system by mounting the current working directory to the container's /aws directory. By downloading the hello object to the container's /aws directory, the file is saved to the host system’s current working directory also.

Linux and macOS:
```
$ docker run --rm -it -v ~/.aws:/root/.aws -v $(pwd):/aws amazon/aws-cli s3 cp s3://aws-cli-docker-demo/hello .
download: s3://aws-cli-docker-demo/hello to ./hello
```

Windows:
```
$ docker run --rm -it -v %cd%.aws:/root/.aws -v %cd%:/aws amazon/aws-cli s3 cp s3://aws-cli-docker-demo/hello .
download: s3://aws-cli-docker-demo/hello to ./hello
```

To confirm the downloaded file exists in the local file system, run the following.

Linux and macOS:
```
$ cat hello
Hello from Docker!
```

Windows:
```
$ type hello
Hello from Docker!
```

## Shorten the Docker command

To shorten the Docker aws command, we suggest you use your operating system's ability to create a [symbolic link](https://www.linux.com/tutorials/understanding-linux-links/) (symlink) or [alias](https://www.linux.com/tutorials/aliases-diy-shell-commands/) in Linux and macOS, or [doskey](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/doskey) in Windows. To set the aws alias, you can run one of the following commands.

For basic access to aws commands, run the following.

Linux and macOS:
```
$ alias aws='docker run --rm -it amazon/aws-cli'
```

Windows:
```
C:\> doskey aws=docker run --rm -it amazon/aws-cli $*
```
For access to the host file system and configuration settings when using aws commands, run the following.

Linux and macOS:
```
$ alias aws='docker run --rm -it -v ~/.aws:/root/.aws -v $(pwd):/aws amazon/aws-cli'
```

Windows:
```
doskey aws=docker run --rm -it -v ~/.aws:/root/.aws -v %cd%:/aws amazon/aws-cli $*
```

To assign a specific version to use in your aws alias, append your version tag.

Linux and macOS:
```
$ alias aws='docker run --rm -it -v ~/.aws:/root/.aws -v $(pwd):/aws amazon/aws-cli:2.0.6'
```

Windows:
```
doskey aws=docker run --rm -it -v ~/.aws:/root/.aws -v %cd%:/aws amazon/aws-cli:2.0.6 $*
```

After setting your alias, you can run the AWS CLI version 2 from within a Docker container as if it's installed on your host system.

$ aws --version
aws-cli/2.0.47 Python/3.7.3 Linux/4.9.184-linuxkit botocore/2.0.0dev10

## References

* [Install AWS CLI version 2 on docker](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-docker.html)
* [Configuration and credential file settings](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)
