# Quick Start

## Prerequisite

Cloudor maintains a few open-sourced docker images on [github](https://github.com/cloudor-io/dockers). 
But eventually you will need a docker image for running your own applications.

## Install `cloudor`

Currently, `cloudor` command line tool is the only way to register and run jobs. Here is how to install it:

1. Download the command line tool `cloudor` from [github](https://github.com/cloudor-io/cloudctl/releases/). Supported platforms are:
	* Mac OS X
	* 64-bit Linux and
	* 64-bit Windows
	
	Remember to move the binary file to a directory in your `$PATH` list so that it can be called easily on the command line window.
	
	The command line examples assume you are in a linux terminal.

2. Run the following command and follow the prompt to set up your user name and password:
	```
	$ cloudor user register
	```
3. Log in and update to the latest binary, if exists:
	```
	$ cloudor user login
	$ cloudor update
	```
	It is recommended to run `cloudor update` command from time to time to upgrade the tool to the latest version.

4. Now you are good to go! Try to run a simple `hello world` job:
	```
	$ cloudor run hello-world --vendor aws --region us-east-1 --instance-type t2.micro
	```

## Console

The web console service is avaialble at https://cloudor.dev. You can use the same user name and password to login.

* View basic user profile and current credit 
* View recent jobs (similar to run `cloudor job list`)
* View recent credit transactions (similar to run `cloudor credit`)
* Make payment (you can only do it in the web console)


## 
