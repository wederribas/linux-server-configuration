# Items Catalog App

[![GitHub issues](https://img.shields.io/github/issues/wederribas/items-catalog.svg)](https://github.com/wederribas/items-catalog/issues)
[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/wederribas/items-catalog/master/LICENSE)

This project is part of the [Udacity Full Stack Web Developer nanodegree](https://udacity.com/course/full-stack-web-developer-nanodegree--nd004)


The _Linux Server Configuration_ required the preparation of a Ubuntu Server to be used to serve applications in a production-ready environment. Starting from the server provisioning on Amazon Litghsail, through the firewall configuration and real application deploy.

## Project Details

### Server Address

* IP 18.216.10.126
* http://18.216.10.126/

### Sofware Installed

Once the applications deployed in the server runs inside Docker containers, it was only needed to install the following Docker packages:

* [Docker Engine](https://www.docker.com/)
* [Docker Compose](https://docs.docker.com/compose/)

### Summary of Performed Configurations

1. Update all packages to latest version

    ```
    $ sudo apt-get update
    $ sudo apt-get upgrade
    $ sudo apt-get autoremove
    ````

2. Firewall rules

    ```
    $ sudo ufw default deny incoming
    $ sudo ufw default allow outgoing
    $ sudo ufw allow 2200/tcp
    $ sudo ufw allow 123/tcp
    $ sudo ufw allow 8000/tcp
    $ sudo ufw allow 8080/tcp
    $ sudo ufw allow www
    $ sudo ufw enable
    ```

    Once the web application (Items Catalog) is running in the Docker on port 8080, it was needed to redirect all incoming requests from port 80 to port 8080. To accomplish that, I have edited the UFW rules file to add the redirect:

    ```
    *nat
    :PREROUTING ACCEPT [0:0]
    -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
    COMMIT
    ```


3. SSH Configuration

    I have changed the default SSH port to be 2200 instead of 22, and also disabled the remote root login by editing the file below:

    ```
    $ vim /etc/ssh/sshd_config
    ```

    Then, I changed the following file lines:

    ```
    Port 2200
    PermitRootLogin no 
    ```

    Then, I reloaded and restarted SSH service:

    ```
    $ sudo service ssh reload
    $ sudo service ssh restart
    ```


4. Grader user

    ```
    $ sudo adduser -m grader
    $ sudo usermod -aG sudo grader # adding grader to sudo
    ```

    -- Logged as grader

    ```
    $ mkdir ~/.ssh
    $ touch ~/.ssh/authorized_keys
    $ chmod 700 ~/.ssh
    $ chmod 644 ~/.ssh/authorized_keys
    ```

    I then copied the public key content that I have generated in my personal computer to the authorized_keys file on server, in order to allow the user grader to SSH into the server.

5. Item Catalog application deploy

    The Item Catalog application is composed by three different Docker images, each one hosting on layer of the application (Flask API, PostgreSQL and React Web App).

    In order to clone and get the project running, I have run the commands below:

    ```
    $ git clone https://github.com/wederribas/items-catalog.git
    $ cs items-catalog/docker/
    $ ./compose-up
    ```

6. Setting Timezone

    In order to configure the server timezone, I have executed the following command:

    ```
    $ sudo locale-gen pt_BR.UTF-8
    ```

### Additional Resources

https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce-1
https://docs.docker.com/compose/install/#install-compose
https://www.cyberciti.biz/faq/how-to-configure-ufw-to-forward-port-80443-to-internal-server-hosted-on-lan/