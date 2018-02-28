# php72-devbox-shared-local

This repository contains the files needed to create a development environment for Magento using 
docker containers.

# Intro

This image is designed to serve as a development environment for upgrading the Magento2 stack to php7.2. The Docker 
image brings up three containers:

**Database:** mySQL 5.6

**Service:** Redis, Varnish, Elasticsearch and RabbitMQ

**Web:** PHP 7.2.1, Apache2

# Instructions

Prerequesits : Docker Community Edition

**Step 1**: Open a terminal window and clone the php72-devbox-shared-local repository on your local system.
The name you choose as the folder name will be used later so make sure you name it 
something descriptive. When the containers get created in step 3 Docker uses the folder 
name as the beginning part of the container name followed by the service name and 
instance. ex myfolder_web_1

```
mkdir myfolder
cd myfolder
git clone https://github.com/giabmf11/php72-devbox-shared-local.git
```

**Step 2**: Navigate to the folder you just created in step 1 and open the docker-compose.yml file. Find the section
labled volumes. The first entry in the list of volumes contains a placeholder labled [MAGENTO_REPO_LOCATION]. You 
must replace this placeholder with the path to your Magento code base. This will created a mirrored directory in the
web container that syncs with the host directory. This allows you to make code changes on your local Magento
repository and the changes will be reflected in the container. Make sure to clear the cache if you don't see your 
changes.

Section to change in docker-compose.yml:

```
volumes:
      - "~/[MAGENTO_REPO_LOCATION]:/var/www/magento2"
      ...
```

Example:

```
volumes:
      - "~/git/Magento2:/var/www/magento2"
      ...
```

**Step 3**: Save docker-compose.yml

**Step 4**: Go back to your terminal and run the following command to build the web image.

```
docker-compose build
```

**Step 5**: Run the following command Create the containers by executing this command.

```
docker-compose up -d
```

**Step 6**: Run the following command to see the new containers and their details.

```
docker ps
```

**Step 7**: Run the following command to find the port docker is exposing for the web container's port 80. The format 
will be 0.0.0.0:[port]. Copy the port. ex 32909

```
docker-compose port web 80
```

**Step 8**: Using the port you copied in step 7 run the install.sh script in the web container. "32909" is only an 
example.

```
docker-compose exec --user=magento2 web install.sh 32909
```

**Step 7**: Navigate to your instance by going to http://localhost:[port from step 7]. ex http://localhost:32909.


# Comments, questions, bug reports, contributions?

Please use GitHub issue/pull request features to ask questions, reports bugs or provide contributions.

# What hosts are supported?

* 64-bit Windows 10 Pro, Enterprise and Education (1511 November update, Build 10586 or later)
* Mac OS 10.10.3 "Yosemite" or later

# My site doesn't work after restart

Docker assigns a random free port to the container on restart. Run the m2devbox-reset script to reinitialize containers 
to use the proper port.

# Why do you use a script to generate the container files?

Using a script helps us provide a unique name for the containers, which allows you to run multiple sets of containers 
on one host. The script wraps the commands and also enables containers to run without conflict.
In the future, keys will be integrated so there will be no need to enter them.

# Why are Apache and PHP in the same container? Docker best practices suggest a container for each component...

Our goal was to create an easy to use development environment, not a production environment. The containers in Magento 
DevBox should not be used as a model for production use.

Additionally, when we tried separate containers, we encountered stability issues, especially when running multiple sets 
of containers. Docker is a quickly evolving product but it's not yet sufficiently stable. We are eager to revisit this 
approach and split the container in components, with option for nginx instead of Apache.

# Why do you include Redis, Varnish, Elasticsearch and RabbitMQ?

We think that you should develop as much as possible in an environment resembling a production configuration. In the 
majority of cases, a production configuration includes those components (Elasticsearch and RabbitMQ for Magento EE 
deployments).

This approach allows you to catch early issues related to incompatibility instead of finding those issues at the last 
moment. During Magento DevBox installation, use *Advanced Options* to choose which of these components to install.

# Warmup

By default, to save installation time, no warmup is performed. If you would like to use the container for demo 
purposes, you can enable warmup. More information is available in documention 
http://devdocs.magento.com/guides/v2.1/install-gde/docker/docker-commands.html.

# Cron

By default, to save batteries/energy, cron is disabled. Our experience shows, that running cron in container results 
in very quick draining of laptop batteries. To enable cron, you can follow the instructions in the documentation 
http://devdocs.magento.com/guides/v2.1/install-gde/docker/docker-commands.html.

