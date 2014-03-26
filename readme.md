pkg-emoncms
========================

This repository maintains debian packaging for the upstream [emoncms/emoncms](https://github.com/emoncms/emoncms) repository.

The emoncms core product is packaged as `emoncms` and can be installed via the OpenEnergyMonitor public apt repository; for details on installation see below.

## Installation guide

### Preconditions

#### MySQL configuration

_Please note that passwordless DB access is not supported (see [#4](https://github.com/Dave-McCraw/pkg-emoncms/issues/4))._

The `emoncms` debian package has a declared dependency on the `mysql` package, so potentially you can just install emoncms and `apt-get` will pull in MySQL automatically. However, unless you really know what you are doing, set up MySQL first, and verify you can log in with your desired credentials using the following command:

    mysql -u <USERNAME> -p <PASSWORD>
    
If this works, providing the same credentials to emoncms during installation should result in a successful database initialisation.

### Configuring apt.sources

In order to access the OpenEnergyMonitor apt repository you need to add a line to your apt.sources configuration file, which is located at: 

    /etc/apt/sources.list

You need to add the following line:

    deb http://emon-repo.s3.amazonaws.com wheezy unstable

### Install emoncms

You will need to update your system repositories:

    sudo apt-get update

And then install emoncms (all dependencies will also be intalled at this point):

    sudo apt-get install emoncms

The Debian package manager will now ask you a series of questions to configure emoncms. These are used to generate a valid settings.php file for your installation.

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

The first time you run emoncms it will automatically setup the database and you will be taken straight to the register/login screen.

Create an account by entering your email and password and clicking register to complete.
