# How To Install NVM on CentOS/RHEL 7

By [Rahul](https://tecadmin.net/author/myadmin/)November 23, 20203 Mins Read

**NVM** stands for Node Version Manager is a command-line utility for managing Node versions. Sometimes you required to deploy multiple node application with different-2 versions.

Managing the multiple Node.js versions for differnt-2 projects are a pain for the developers. But NVM helped to easily manage multiple active Node.js versions on a single system.

This tutorial will explain you to install NVM on CentOS/RHEL 7/6 systems and manage multiple Node.js versions.

## Installing NVM on CentOS 7

NVM provides a simple bash script for the installation on Linux systems. Open a terminal on your system or connect a remote system using SSH.

Make sure your system have curl installed.

```
sudo yum install curl -y 
```

Then execute the installer script as following command:

```
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash   
```

Basically, NVM keeps all files under the $HOME/.nvm directory. Then it sets environment in users .bashrc file. You need to load this environment to set required configuration by running the following command:

```
source ~/.bashrc
```

## Installing Node.js using NVM

Nvm allows to install multiple node versions on your system for the logged in user. You can find all the available Node version for installation by running command:

```
nvm ls-remote 
```

Above command will show you a list of available node versions. Now you can install any node version by typing:

```
nvm install 12.19.1 
```

Just change 12.19.1 with your required version like 11.15.0, 10.11.0, etc.

It also provided alias for the latest stable node version, LTS version and other previous LTS version.

```
nvm install node        ## install latest stable version 
nvm install lts/*       ## install latest lts version 
```

You can also use lts/dubnium, lts/carbon etc.

## Using NVM

You can use the following command to list installed versions of node for the current user.

```
nvm ls 
```

With this command, you can find the available node.js version for the installation.

```
nvm ls-remote 
```

You can also select a different version for the current session. The selected version will be the currently active version for the current shell only.

```
nvm use 12.18.3 
```

To find the default Node version set for the current user, type:

```
nvm run default --version 
```

You can run a Node script with the desired version of node.js using the below command:

```
nvm exec 12.18.3 server.js 
```

## Uninstall Specific Node Version with NVM

You can remove any unused version by running the following command. Just make sure the version you are removing is not currently active version.

To remove Node.js 5.8.0, type:

```
nvm uninstall 5.8.0 
```

## Conclusion

This tutorial helped you to install [nvm](https://github.com/nvm-sh/nvm) and node on Fedora Linux system. You also learned about basics of nvm command line utility.