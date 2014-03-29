pkg-emoncms
========================

This repository maintains debian packaging for the upstream [emoncms/emoncms](https://github.com/emoncms/emoncms) repository.

The emoncms core product is packaged as `emoncms` and can be installed via the OpenEnergyMonitor public apt repository; for details on installation see below.

## Installation guide

This guide has been verified using a Raspberry Pi with a clean copy of the [latest version of Raspian](http://downloads.raspberrypi.org/raspbian_latest) imaged to the SD card.

No pre-configuration is required - just stick the SD card in your Pi, switch it on, and SSH in, then:

### Configuring apt.sources

In order to access the OpenEnergyMonitor apt repository you need to add a line to your apt.sources configuration file.

If you don't know your distribution, you can get it by running `lsb_release -cs` (you may need to run `sudo apt-get install lsb-release` first).

Now run the following command, substituting `<DISTRO>`:

    sudo echo "deb http://emon-repo.s3.amazonaws.com <DISTRO> unstable" >> /etc/apt/sources.list

### Install emoncms

You will need to update your system repositories:

    sudo apt-get update

And then install emoncms (all dependencies will also be intalled at this point):

    sudo apt-get -y --force-yes install emoncms

The Debian package manager will now ask you a series of questions to configure emoncms. These are used to generate a valid settings.php file for your installation.

If you have not yet installed MySQL, it will be installed automatically at this point, and you will be prompted to enter a root password. *This must not be blank.*

#### Reconfigure emoncms

If you were to get DB credentials wrong, installation would fail. To update your answers (for this, or any other reason, at any time) you can run:

    sudo dpkg-reconfigure emoncms --force
    
Note that your previous answers are pre-populated for convenience; this includes the passwords (just hit enter on the empty field to use the previous password - so for this reason, you cannot use a passwordless DB user).

If installation failed (but not otherwise) then you will need to run the emoncms install command again to finish setting it up.

### Install PECL modules (redis and swift mailer)

These modules are optional but will enhance the functionality of emoncms: redis will greatly reduce disk I/O (especially useful if you're running from an SD card). Swift mailer provides email :)

Install all dependencies:

    sudo apt-get install php-pear php5-dev

Install pecl modules:

    sudo pear channel-discover pear.swiftmailer.org
    sudo pecl install redis swift/swift
    
Add pecl modules to php5 config:
    
    sudo sh -c 'echo "extension=redis.so" > /etc/php5/apache2/conf.d/20-redis.ini'
    sudo sh -c 'echo "extension=redis.so" > /etc/php5/cli/conf.d/20-redis.ini'


### Install add-on emoncms modules

You don't need to install all (or indeed any) of the optional add-on modules, but they may enhance the functionality or utility of your emoncms installation:

| Module  | Install from apt? |
| ------------- | ------------- |
| [Raspberry Pi](https://github.com/emoncms/raspberrypi) | `sudo apt-get install emoncms-module-rfm12pi` <br/> (from [pkg-emoncms-module-rfm12pi](https://github.com/Dave-McCraw/pkg-emoncms-module-rfm12pi) ) |
| [Event](https://github.com/emoncms/event) | `sudo apt-get install emoncms-module-event` <br/> (from [pkg-emoncms-module-event](https://github.com/Dave-McCraw/pkg-emoncms-module-event) ) |
| [Notify](https://github.com/emoncms/notify) | manual only |
| [Energy](https://github.com/emoncms/energy) | manual only |
| [Report](https://github.com/emoncms/report) | manual only |
| [Open BEM](https://github.com/emoncms/openbem) | manual only |
| [Event](https://github.com/emoncms/event) | manual only |
| [Packetgen](https://github.com/emoncms/packetgen) | manual only |
| [MQTT](https://github.com/elyobelyob/mqtt) | manual only |

See the linked readme files for individual modules' installation instructions.

### Restart services

Once you have installed emoncms, you need to enable it in Apache:

    sudo a2ensite emoncms

Now is also a good time to ensure that `mod_rewrite` is also running:

    sudo a2enmod rewrite

Now restart Apache:

    sudo /etc/init.d/apache2 restart

### In an internet browser, load emoncms:

[http://localhost/emoncms](http://localhost/emoncms)

_If you're running a headless server, you'll obviously need to substitute its IP address or hostname instead of localhost_

The first time you run emoncms it will automatically setup the database and you will be taken straight to the register/login screen.

Create an account by entering your email and password and clicking register to complete.


## Troubleshooting

_Please feel free to contribute suggestions for this section if you have encountered issues setting up emoncms_

### APT configuration

The OpenEnergyMonitor APT repository currently supports two distributions: 
 - `wheezy` (Debian/Raspbian) 
 - `quantal` (Ubuntu). 

Check the row you've added to `/etc/apt/sources.list` matches the existing rows in terms of the distribution, and that the distribution is one of the above (if not, get in touch!)

### MySQL configuration

**Please note that passwordless DB access is not supported (see [#4](https://github.com/Dave-McCraw/pkg-emoncms/issues/4)).**

The `emoncms` debian package has a declared dependency on the `mysql-server` and `mysql-client` packages, so you can just install emoncms and `apt-get` will pull in MySQL automatically. If you are having trouble with the installation, verify your credentials manually by running:

    mysql -u <USERNAME> -p <PASSWORD>
    
If this works, providing the same credentials to emoncms during installation should result in a successful database initialisation.
