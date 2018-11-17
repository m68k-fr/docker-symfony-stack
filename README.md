# A Docker Stack for Symfony4


A Docker stack with all the necessary tools for Symfony4.  


 
* Configured for a development environment, please do not use this in a production environment.  
* Validate all the requirement needed by Symfony 4.1.
* Tested on Docker for Linux & Docker for Windows 10.


## Technology included

* [Docker](https://www.docker.com/)
* [Apache](https://httpd.apache.org/)
* [MySQL](https://www.mysql.com/)
* [PHP FPM 7.1](https://php.net/)
* [PhpMyAdmin](https://www.phpmyadmin.net/)
* [Symfony 4.1](https://symfony.com/)
* [Yarn](https://yarnpkg.com/lang/en/)
* [XDebug](https://xdebug.org/)
* [Webpack (using Webpack Encore)](https://symfony.com/doc/3.4/frontend/encore/installation-no-flex.html)

## Requirements

* [Docker CE](https://www.docker.com/)


## Installation

* Clone the repository.
* Copy .env.dist to .env

## File permissions

* In .env, update the USER section to match your local user.

## Configuring Docker for XDebug

In .env, update the **DOCKER_NAT_IP** argument with your own vEthernet DockerNAT ip.  
On Windows system, you can use the following command to find it. 
```sh
ipconfig /all
```
ON Linux system, simply use:
```sh
ip addr
```


## Start all Docker services

Change directory into the cloned project, then build the Docker image and launch containers.

```sh
docker-compose up -d
```

## PhpMyAdmin
phpmyAdmin should be available on http://localhost:8080 (user: root password:root)


## Install Symfony 4.1

Using a terminal, pull composer dependencies in a subfolder 
```sh
docker-compose exec web composer create-project symfony/website-skeleton temp-project
```

Move your project to the root folder
```sh
cat temp-project/.env >> .env
cat temp-project/.gitignore >> .gitignore
rm temp-project/.env
rm temp-project/.gitignore
mv temp-project/* .
mv temp-project/.env.test .
rm -fr temp-project
```

Add default .htaccess for Apache 
```
docker-compose exec web composer require symfony/apache-pack
```


Update your Database entry in .env file 
```
DATABASE_URL=mysql://user:userpass@db:3306/symfony
```

Now, you should be able to access the symfony app by browsing http://localhost


## Checking Requirements

```
docker-compose exec web composer require symfony/requirements-checker
```

The requirements checker tool creates a file called check.php in the public/ directory of your project.  
As our main Docker container is not local, edit that file and remove the '127.0.0.1' REMOTE_ADDR restriction.  
Open http://localhost/check.php with your browser to check the requirements.  

Regarding the 'PHP accelerator should be installed':  
Please note Opcache is installed, but deactivated by default, as this is not practical for a development environment. 

If the check is valid, uninstall the requirements checker:  

```
docker-compose exec web composer remove symfony/requirements-checker
```

## Configure XDebug for PhpStorm

In settings / Language & Frameworks / PHP / Debug / Servers :  
Add your docker-server.

![](https://drive.google.com/uc?export=download&id=1sxN9UjPmzWhaSgE0_NWNbKSeyGj2w7mI)

Create a new Run/Debug "PHP Remote debug" Configuration for your project:

![](https://drive.google.com/uc?export=download&id=1O_RkqM9r7oJHPwix411HQa6wD1gJp6JY)


In settings / Language & Frameworks / PHP / Debug :

* XDebug / Debug port : 9001 (check 'Can accept external connections')

In settings / Language & Frameworks / PHP / Debug / DBGp Proxy:

* IDE Key: docker
* Host: Use the **DOCKER_NAT_IP** ip
* Port: 9001


Using PHPStorm, in settings / Language & Frameworks / PHP / Debug / DBGPproxy:  
* IDE key : docker
* Port: 9001
* Host: Use the **DOCKER_NAT_IP** ip


## Configure PHP-CS-Fixer for PhpStorm

* On your local machine: Install PHP-CS-Fixer globally from composer
```sh
composer global require friendsofphp/php-cs-fixer
```
* Using PHPStorm, add a new "External Tool" in "Settings / Tools / External Tools" and configure it using the following parameters:
```
Program (for windows): C:\Users\myuser\AppData\Roaming\Composer\vendor\bin\php-cs-fixer.bat
Program (for unix): /home/user/.config/composer/vendor/bin/php-cs-fixer
Arguments: fix --verbose --config=$ProjectFileDir$/.php_cs.dist --path-mode=intersection "$FileDir$/$FileName$"
Working directory: $ProjectFileDir$
```

* Click or search for Keymap in settings. Then in the keymap window find External Tools and then again External Tools and Then whatever you named your external tool we just configured. Double click your external tool or the green pencil at the top and select Add Keyboard Shortcut from the list. Then type a key combo (I use Alt+F) and hit OK, and then, hit OK again on settings.
  


## Install Webpack Encore and Build web assets

```sh
docker-compose exec web composer require encore
docker-compose exec web yarn install
```
