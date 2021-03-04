> ___This is just copy of Digital Ocean's Guideline___ 



- [Initial Server Setup with Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04)

 - [How To Install Linux, Apache, MySQL, PHP (LAMP) stack on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-20-04)

 - [How To Secure Apache with Let's Encrypt on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu-20-04)

- [How To Set Up Apache Virtual Hosts on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-apache-virtual-hosts-on-ubuntu-18-04)



---

- ## __A. Creating a new User & give SUDO Power & Installing OpenSSH__

    1. __Log in as root in terminal once you have logged in using the GUI interface__


        ```
        $ sudo -i
        ```

        _Give your account password & now you are logged in as root_


    2. __Creating a New User__

        Once you are logged in as root, we’re prepared to add the new user account that we will use to log in from now on.

        This example creates a new user called sammy, but you should replace it with a username that you like:

        ``` 
        adduser john 
        ```

        You will be asked a few questions, starting with the account password.

        Enter a strong password and, optionally, fill in any of the additional information if you would like. This is not required and you can just hit ENTER in any field you wish to skip.

    3. __Granting Administrative Privileges__

        Now, we have a new user account with regular account privileges. However, we may sometimes need to do administrative tasks.

        To avoid having to log out of our normal user and log back in as the root account, we can set up what is known as “superuser” or root privileges for our normal account. This will allow our normal user to run commands with administrative privileges by putting the word sudo before each command.

        To add these privileges to our new user, we need to add the new user to the sudo group. By default, on Ubuntu 18.04, users who belong to the sudo group are allowed to use the sudo command.

        As root, run this command to add your new user to the sudo group (substitute the highlighted word with your new user):

        ```
        usermod -aG sudo john
        ```

        Now, when logged in as your regular user, you can type sudo before commands to perform actions with superuser privileges.

    4. __Installing OpenSSH__

        1. Open your terminal either by using the Ctrl+Alt+T keyboard shortcut or by clicking on the terminal icon and install the openssh-server package by typing:

        ```
        sudo apt update
        sudo apt install openssh-server
        ```
        
        Enter the password when prompted and enter Y to continue with the installation.

        2. Once the installation is completed, the SSH service will start automatically. To verify that the installation was successful and SSH service is running type the following command which will print the SSH server status:

        ```
        sudo systemctl status ssh
        ```

    5. __Setting Up a Basic Firewall__

        Ubuntu 18.04 servers can use the UFW firewall to make sure only connections to certain services are allowed. We can set up a basic firewall very easily using this application.

        Different applications can register their profiles with UFW upon installation. These profiles allow UFW to manage these applications by name. OpenSSH, the service allowing us to connect to our server now, has a profile registered with UFW.

        You can see this by typing:

        ```
        ufw app list
        ```

        ```
        #output
        Available applications:
        OpenSSH
        ```

        Afterwards, we can enable the firewall by typing:

        ```
        ufw enable
        ```

        Type “y” and press ENTER to proceed. You can see that SSH connections are still allowed by typing:

        ```
        ufw status
        ```

        ```
        #output
        Status: active

        To                         Action      From
        --                         ------      ----
        OpenSSH                    ALLOW       Anywhere
        OpenSSH (v6)               ALLOW       Anywhere (v6)
        ```

        As the firewall is currently blocking all connections except for SSH, if you install and configure additional services, you will need to adjust the firewall settings to allow acceptable traffic in. You can learn some common UFW operations in 
        [this guide](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands).


    6. __Enabling External Access for Your Regular User__ _(OPTIONAL IF ALREADY LOGGED IN VIA PASS)_

        The process for configuring SSH access for your new user depends on whether your server’s root account uses a password or SSH keys for authentication.

        __If the Root Account Uses Password Authentication__

        If you logged in to your root account using a password, then password authentication is enabled for SSH. You can SSH to your new user account by opening up a new terminal session and using SSH with your new username:

        ```
        ssh john@your_server_ip
        ```

        After entering your regular user’s password, you will be logged in. Remember, if you need to run a command with administrative privileges, type sudo before it like this:

        ```
        sudo command_to_run
        ```

        You will be prompted for your regular user password when using sudo for the first time each session (and periodically afterwards).

        To enhance your server’s security, we strongly recommend setting up SSH keys instead of using password authentication. Follow our guide on [setting up SSH keys on Ubuntu 18.04 to learn how to configure key-based authentication](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-1804).

        __If the Root Account Uses SSH Key Authentication__

        If you logged in to your root account using SSH keys, then password authentication is disabled for SSH. You will need to add a copy of your local public key to the new user’s ~/.ssh/authorized_keys file to log in successfully.

        Since your public key is already in the root account’s ~/.ssh/authorized_keys file on the server, we can copy that file and directory structure to our new user account in our existing session.

        The simplest way to copy the files with the correct ownership and permissions is with the rsync command. This will copy the root user’s .ssh directory, preserve the permissions, and modify the file owners, all in a single command. Make sure to change the highlighted portions of the command below to match your regular user’s name:

        _Note: The rsync command treats sources and destinations that end with a trailing slash differently than those without a trailing slash. When using rsync below, be sure that the source directory (~/.ssh) does not include a trailing slash (check to make sure you are not using ~/.ssh/)._

        _If you accidentally add a trailing slash to the command, rsync will copy the contents of the root account’s ~/.ssh directory to the sudo user’s home directory instead of copying the entire ~/.ssh directory structure. The files will be in the wrong location and SSH will not be able to find and use them._

        ```
        rsync --archive --chown=sammy:sammy ~/.ssh /home/john
        ```

        You should be logged in to the new user account without using a password. Remember, if you need to run a command with administrative privileges, type sudo before it like this:

        ```
        sudo command_to_run
        ```

        You will be prompted for your regular user password when using sudo for the first time each session (and periodically afterwards).

         _To find your IP: Type this into CLI_

        `dig +short myip.opendns.com @resolver1.opendns.com`

- ### __B. Installing LAMPP (Linux, MySql, Apache & Php)__

    __Introduction__

    A “LAMP” stack is a group of open-source software that is typically installed together to enable a server to host dynamic websites and web apps. This term is actually an acronym which represents the Linux operating system, with the Apache web server. The site data is stored in a MySQL database, and dynamic content is processed by PHP.

    In this guide, we will install a LAMP stack on an Ubuntu 18.04 server.

    __Prerequisites__

    Step __A.__ is the Prerequisites


    1. __Installing Apache and Updating the Firewall__

        The Apache web server is among the most popular web servers in the world. It’s well-documented and has been in wide use for much of the history of the web, which makes it a great default choice for hosting a website.

        Install Apache using Ubuntu’s package manager, `apt`:

        ```
        sudo apt update
        sudo apt install apache2
        ```

        Since this is a sudo command, these operations are executed with root privileges. It will ask you for your regular user’s password to verify your intentions.

        Once you’ve entered your password, apt will tell you which packages it plans to install and how much extra disk space they’ll take up. Press Y and hit ENTER to continue, and the installation will proceed.

        _Adjust the Firewall to Allow Web Traffic_

        Next, assuming that you have followed the initial server setup instructions and enabled the UFW firewall, make sure that your firewall allows HTTP and HTTPS traffic. You can check that UFW has an application profile for Apache like so:

        ```
        sudo ufw app list
        ```

        ```
        #Output
        Available applications:
            Apache
            Apache Full
            Apache Secure
            OpenSSH
        ```

        If you look at the Apache Full profile, it should show that it enables traffic to ports 80 and 443:

        ```
        sudo ufw app info "Apache Full"
        ```

        ```
        #Output
        Profile: Apache Full
        Title: Web Server (HTTP,HTTPS)
        Description: Apache v2 is the next generation of the omnipresent Apache web
        server.

        Ports:
            80,443/tcp
        ```

        Allow incoming HTTP and HTTPS traffic for this profile:

        ```
        sudo ufw allow in "Apache Full"
        ```

        You can do a spot check right away to verify that everything went as planned by visiting your server’s public IP address in your web browser (see the note under the next heading to find out what your public IP address is if you do not have this information already): `http://your_server_ip`

        You will see the default Ubuntu 18.04 Apache web page, which is there for informational and testing purposes. It should look something like this:

        ![Apache2 Ubuntu Default Page](https://assets.digitalocean.com/articles/how-to-install-lamp-ubuntu-18/small_apache_default_1804.png)

        ___If you see this page, then your web server is now correctly installed and accessible through your firewall___.

        __How To Find your Server’s Public IP Address__

        If you do not know what your server’s public IP address is, there are a number of ways you can find it. Usually, this is the address you use to connect to your server through SSH.

        There are a few different ways to do this from the command line. First, you could use the iproute2 tools to get your IP address by typing this:

        ```
        ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
        ```

        This will give you two or three lines back. They are all correct addresses, but your computer may only be able to use one of them, so feel free to try each one.

        An alternative method is to use the curl utility to contact an outside party to tell you how it sees your server. This is done by asking a specific server what your IP address is:

        ```
        sudo apt install curl
        curl http://icanhazip.com
        ```

        Regardless of the method you use to get your IP address, type it into your web browser’s address bar to view the default Apache page.

    2. __Installing MySQL__

        Now that you have your web server up and running, it is time to install MySQL. MySQL is a database management system. Basically, it will organize and provide access to databases where your site can store information.

        Again, use apt to acquire and install this software:

        ```
        sudo apt install mysql-server
        ```
        __Note__: In this case, you do not have to run sudo apt update prior to the command. This is because you recently ran it in the commands above to install Apache. The package index on your computer should already be up-to-date.

        This command, too, will show you a list of the packages that will be installed, along with the amount of disk space they’ll take up. Enter Y to continue.

        When the installation is complete, run a simple security script that comes pre-installed with MySQL which will remove some dangerous defaults and lock down access to your database system. Start the interactive script by running:

        ```
        sudo mysql_secure_installation
        ```

        This will ask if you want to configure the VALIDATE PASSWORD PLUGIN.

        __Note__: Enabling this feature is something of a judgment call. If enabled, passwords which don’t match the specified criteria will be rejected by MySQL with an error. This will cause issues if you use a weak password in conjunction with software which automatically configures MySQL user credentials, such as the Ubuntu packages for phpMyAdmin. It is safe to leave validation disabled, but you should always use strong, unique passwords for database credentials.

        Answer `Y` for yes, or anything else to continue without enabling.

        ```
        VALIDATE PASSWORD PLUGIN can be used to test passwords
        and improve security. It checks the strength of password
        and allows the users to set only those passwords which are
        secure enough. Would you like to setup VALIDATE PASSWORD plugin?

        Press y|Y for Yes, any other key for No:
        ```

        If you answer `“yes”`, you’ll be asked to select a level of password validation. Keep in mind that if you enter `2` for the strongest level, you will receive errors when attempting to set any password which does not contain numbers, upper and lowercase letters, and special characters, or which is based on common dictionary words.

        ```
        There are three levels of password validation policy:

        LOW    Length >= 8
        MEDIUM Length >= 8, numeric, mixed case, and special characters
        STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

        Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1
        ```

        Regardless of whether you chose to set up the VALIDATE PASSWORD PLUGIN, your server will next ask you to select and confirm a password for the MySQL __root__ user. This is an administrative account in MySQL that has increased privileges. Think of it as being similar to the __root__ account for the server itself (although the one you are configuring now is a MySQL-specific account). Make sure this is a strong, unique password, and do not leave it blank.

        If you enabled password validation, you’ll be shown the password strength for the root password you just entered and your server will ask if you want to change that password. If you are happy with your current password, enter `N` for “no” at the prompt:

        ```
        Using existing password for root.

        Estimated strength of the password: 100
        Change the password for root ? ((Press y|Y for Yes, any other key for No) : n
        ```

        For the rest of the questions, press `Y` and hit the ENTER key at each prompt. This will remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes you have made.

        Note that in Ubuntu systems running MySQL 5.7 (and later versions), the __root__ MySQL user is set to authenticate using the `auth_socket` plugin by default rather than with a password. This allows for some greater security and usability in many cases, but it can also complicate things when you need to allow an external program (e.g., phpMyAdmin) to access the user.

        If you prefer to use a password when connecting to MySQL as __root__, you will need to switch its authentication method from `auth_socket` to `mysql_native_password`. To do this, open up the MySQL prompt from your terminal:

        ```
        sudo mysql
        ```

        Next, check which authentication method each of your MySQL user accounts use with the following command:

        ```
        mysql>SELECT user,authentication_string,plugin,host FROM mysql.user;
        ```

        ```
        #Output
        +------------------+-------------------------------------------+-----------------------+-----------+
        | user             | authentication_string                     | plugin                | host      |
        +------------------+-------------------------------------------+-----------------------+-----------+
        | root             |                                           | auth_socket           | localhost |
        | mysql.session    | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | mysql_native_password | localhost |
        | mysql.sys        | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | mysql_native_password | localhost |
        | debian-sys-maint | *CC744277A401A7D25BE1CA89AFF17BF607F876FF | mysql_native_password | localhost |
        +------------------+-------------------------------------------+-----------------------+-----------+
        4 rows in set (0.00 sec)
        ```

        In this example, you can see that the root user does in fact authenticate using the auth_socket plugin. To configure the root account to authenticate with a password, run the following ALTER USER command. Be sure to change password to a strong password of your choosing:

        ```
        mysql>ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
        ```

        Then, run FLUSH PRIVILEGES which tells the server to reload the grant tables and put your new changes into effect:

        ```
        mysql>FLUSH PRIVILEGES;
        ```

        Check the authentication methods employed by each of your users again to confirm that root no longer authenticates using the auth_socket plugin:

        ```
        mysql>SELECT user,authentication_string,plugin,host FROM mysql.user;
        ```

        ```
        Output
        +------------------+-------------------------------------------+-----------------------+-----------+
        | user             | authentication_string                     | plugin                | host      |
        +------------------+-------------------------------------------+-----------------------+-----------+
        | root             | *3636DACC8616D997782ADD0839F92C1571D6D78F | mysql_native_password | localhost |
        | mysql.session    | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | mysql_native_password | localhost |
        | mysql.sys        | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | mysql_native_password | localhost |
        | debian-sys-maint | *CC744277A401A7D25BE1CA89AFF17BF607F876FF | mysql_native_password | localhost |
        +------------------+-------------------------------------------+-----------------------+-----------+
        4 rows in set (0.00 sec)
        ```

        You can see in this example output that the root MySQL user now authenticates using a password. Once you confirm this on your own server, you can exit the MySQL shell:

        ```
        mysql>exit
        ```

    3. __Installing PHP__

        PHP is the component of your setup that will process code to display dynamic content. It can run scripts, connect to your MySQL databases to get information, and hand the processed content over to your web server to display.

        Once again, leverage the apt system to install PHP. In addition, include some helper packages this time so that PHP code can run under the Apache server and talk to your MySQL database:

        ```
        sudo apt install php libapache2-mod-php php-mysql
        ```    

        This should install PHP without any problems. We’ll test this in a moment.

        In most cases, you will want to modify the way that Apache serves files when a directory is requested. Currently, if a user requests a directory from the server, Apache will first look for a file called `index.html`. We want to tell the web server to prefer PHP files over others, so make Apache look for an `index.php` file first.

        To do this, type this command to open the `dir.conf` file in a text editor with root privileges:

        ```
        sudo nano /etc/apache2/mods-enabled/dir.conf
        ```

        It will look like this

        ```
        <IfModule mod_dir.c>
            DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        </IfModule>
        ```

        Move the PHP index file (highlighted above) to the first position after the DirectoryIndex specification, like this:

        ```
        <IfModule mod_dir.c>
            DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
        </IfModule>    
        ```

        When you are finished, save and close the file by pressing CTRL+X. Confirm the save by typing Y and then hit ENTER to verify the file save location.

        After this, restart the Apache web server in order for your changes to be recognized. Do this by typing this:

        ```
        sudo systemctl restart apache2
        ```

        You can also check on the status of the `apache2` serving using `systemctl`:

        ```
        sudo systemctl status apache2
        ```

        ```
        #Sample Output
        ● apache2.service - LSB: Apache2 web server
        Loaded: loaded (/etc/init.d/apache2; bad; vendor preset: enabled)
        Drop-In: /lib/systemd/system/apache2.service.d
                └─apache2-systemd.conf
        Active: active (running) since Tue 2018-04-23 14:28:43 EDT; 45s ago
            Docs: man:systemd-sysv-generator(8)
        Process: 13581 ExecStop=/etc/init.d/apache2 stop (code=exited, status=0/SUCCESS)
        Process: 13605 ExecStart=/etc/init.d/apache2 start (code=exited, status=0/SUCCESS)
            Tasks: 6 (limit: 512)
        CGroup: /system.slice/apache2.service
                ├─13623 /usr/sbin/apache2 -k start
                ├─13626 /usr/sbin/apache2 -k start
                ├─13627 /usr/sbin/apache2 -k start
                ├─13628 /usr/sbin/apache2 -k start
                ├─13629 /usr/sbin/apache2 -k start
                └─13630 /usr/sbin/apache2 -k start
        ```    

        Press `Q` to exit this status output.

        To enhance the functionality of PHP, you have the option to install some additional modules. To see the available options for PHP modules and libraries, pipe the results of apt search into less, a pager which lets you scroll through the output of other commands:

        ```
        apt search php- | less
        ```

        Use the arrow keys to scroll up and down, and press `Q` to quit.

        The results are all optional components that you can install. It will give you a short description for each:

        ```
        bandwidthd-pgsql/bionic 2.0.1+cvs20090917-10ubuntu1 amd64
        Tracks usage of TCP/IP and builds html files with graphs

        bluefish/bionic 2.2.10-1 amd64
        advanced Gtk+ text editor for web and software development

        cacti/bionic 1.1.38+ds1-1 all
        web interface for graphing of monitoring systems

        ganglia-webfrontend/bionic 3.6.1-3 all
        cluster monitoring toolkit - web front-end

        golang-github-unknwon-cae-dev/bionic 0.0~git20160715.0.c6aac99-4 all
        PHP-like Compression and Archive Extensions in Go

        haserl/bionic 0.9.35-2 amd64
        CGI scripting program for embedded environments

        kdevelop-php-docs/bionic 5.2.1-1ubuntu2 all
        transitional package for kdevelop-php

        kdevelop-php-docs-l10n/bionic 5.2.1-1ubuntu2 all
        transitional package for kdevelop-php-l10n
        …
        :

        ```

        To learn more about what each module does, you could search the internet for more information about them. Alternatively, look at the long description of the package by typing:

        ```
        apt show package_name
        ```

        There will be a lot of output, with one field called Description which will have a longer explanation of the functionality that the module provides.

        For example, to find out what the `php-cli` module does, you could type this:

        ```
        apt show php-cli
        ```

        Along with a large amount of other information, you’ll find something that looks like this:

        ```
        Output
        …
        Description: command-line interpreter for the PHP scripting language (default)
        This package provides the /usr/bin/php command interpreter, useful for
        testing PHP scripts from a shell or performing general shell scripting tasks.
        .
        PHP (recursive acronym for PHP: Hypertext Preprocessor) is a widely-used
        open source general-purpose scripting language that is especially suited
        for web development and can be embedded into HTML.
        .
        This package is a dependency package, which depends on Ubuntu's default
        PHP version (currently 7.2).
        …    
        ```

        If, after researching, you decide you would like to install a package, you can do so by using the `apt install` command like you have been doing for the other software.

        If you decided that `php-cli` is something that you need, you could type:

        ```
        sudo apt install php-cli
        ```

    4. __Setting Up Virtual Hosts (Recommended)__

        When using the Apache web server, you can use virtual hosts (similar to server blocks in Nginx) to encapsulate configuration details and host more than one domain from a single server. We will set up a domain called __testdomain__, but you should replace this with your own domain name.

        Apache on Ubuntu 18.04 has one server block enabled by default that is configured to serve documents from the /var/www/html directory. While this works well for a single site, it can become unwieldy if you are hosting multiple sites. Instead of modifying /var/www/html, let’s create a directory structure within /var/www for our __testdomain__  site, leaving /var/www/html in place as the default directory to be served if a client request doesn’t match any other sites.

        Create the directory for __testdomain__ as follows:

        ```
        sudo mkdir /var/www/testdomain
        ```

        Next, create a sample `index.html` page using nano or your favorite editor:

        ```
        nano /var/www/testdomain/index.html`
        ```
        
        Inside, add the following sample HTML:

        ```
        <html>
            <head>
                <title>Welcome to testdomain!</title>
            </head>
            <body>
                <h1>Success!  The testdomain server block is working!</h1>
            </body>
        </html>
        ```

        Save and close the file when you are finished.

        In order for Apache to serve this content, it’s necessary to create a virtual host file with the correct directives. Instead of modifying the default configuration file located at /etc/apache2/sites-available/000-default.conf directly, let’s make a new one at 
        `/etc/apache2/sites-available/testdomain.conf`

        Paste in the following configuration block, which is similar to the default, but updated for our new directory and domain name:

        ```
        <VirtualHost *:80>
            ServerAdmin webmaster@localhost
            ServerName testdomain
            ServerAlias www.testdomain
            DocumentRoot /var/www/testdomain
            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>
        ```

        Notice that we’ve updated the DocumentRoot to our new directory and ServerAdmin to an email that the `testdomain` site administrator can access. We’ve also added two directives: ServerName, which establishes the base domain that should match for this virtual host definition, and ServerAlias, which defines further names that should match as if they were the base name.

        Save and close the file when you are finished.

        Let’s enable the file with the `a2ensite` tool:

        ```
        sudo a2ensite testdomain.conf
        ```

        Disable the default site defined in `000-default.conf`:

        ```
        sudo a2dissite 000-default.conf
        ```

        Next, let’s test for configuration errors:

        ```
        sudo apache2ctl configtest
        ```

        You should see the following output:

        ```
        #Output
        Syntax OK
        ```

        Now go to `/etc` and open the file `hosts` file in `vim` or `nano`

        ```
        sudo nano /etc/hosts
        ```

        and bind your _domain name_ againsts your _ip address_

        ```
        127.0.0.1   testdomain
        127.0.0.1   localhost
        ```

        Restart Apache to implement your changes:

        ```
        sudo systemctl restart apache2
        ```

        Apache should now be serving your domain name. You can test this by navigating to `http://testdomain` where you should see something like this:

        ![SuccessMessage](https://assets.digitalocean.com/articles/apache_virtual_hosts_ubuntu/vhost_your_domain.png)

        With that, you virtual host is fully set up. Before making any more changes or deploying an application, though, it would be helpful to proactively test out your PHP configuration in case there are any issues that should be addressed.


        In order to test that your system is configured properly for PHP, create a very basic PHP script called `info.php`. In order for Apache to find this file and serve it correctly, it must be saved to your web root directory.

        Create the file at the web root you created in the previous step by running:

        ```
        sudo nano /var/www/your_domain/info.php
        ```

        This will open a blank file. Add the following text, which is valid PHP code, inside the file:

        ```
        <?php
            phpinfo();
        ?>
        ```

        When you are finished, save and close the file.

        Now you can test whether your web server is able to correctly display content generated by this PHP script. To try this out, visit this page in your web browser. You’ll need your server’s public IP address again.

        The address you will want to visit is:

        `http://testdomain/info.php`

        The page that you come to should look something like this:

        ![info.php](https://assets.digitalocean.com/articles/how-to-install-lamp-ubuntu-18/small_php_info_1804.png)

- ## __C.__  __Enable https using certbot__
 
    __Prerequisites__

    To follow this tutorial, you will need:

    One Ubuntu 18.04 server set up by following this initial server setup for Ubuntu 18.04 tutorial, including a sudo non-root user and a firewall.
    A fully registered domain name. This tutorial will use your_domain as an example throughout. You can purchase a domain name on Namecheap, get one for free on Freenom, or use the domain registrar of your choice.
    Both of the following DNS records set up for your server. You can follow this introduction to DigitalOcean DNS for details on how to add them.

    - An A record with your_domain pointing to your server’s public IP address.
    - An A record with www.your_domain pointing to your server’s public IP address.

    Apache installed by following How To Install Apache on Ubuntu 18.04. Be sure that you have a virtual host file for your domain. This tutorial will use `/etc/apache2/sites-available/testdomain.conf` as an example.

1. __Installing Certbot__

    The first step to using Let’s Encrypt to obtain an SSL certificate is to install the Certbot software on your server.

    Certbot is in very active development, so the Certbot packages provided by Ubuntu tend to be outdated. However, the Certbot developers maintain a Ubuntu software repository with up-to-date versions, so we’ll use that repository instead.

    First, add the repository:

    ```
    sudo add-apt-repository ppa:certbot/certbot
    ```

    You’ll need to press ENTER to accept.

    Install Certbot’s Apache package with apt:

    ```
    sudo apt install python-certbot-apache
    ```

    Certbot is now ready to use, but in order for it to configure SSL for Apache, we need to verify some of Apache’s configuration.

2. __Set Up the SSL Certificate__

    Certbot needs to be able to find the correct virtual host in your Apache configuration for it to automatically configure SSL. Specifically, it does this by looking for a ServerName directive that matches the domain you request a certificate for.

    If you followed the virtual host set up step in the Apache installation tutorial, you should have a VirtualHost block for your domain at `/etc/apache2/sites-available/testdomain.com.conf` with the ServerName directive already set appropriately.

    To check, open the virtual host file for your domain using nano or your favorite text editor:

    ```
    sudo nano /etc/apache2/sites-available/your_domain.conf
    ```

    Find the existing ServerName line. It should look like this:

    ```
    ServerName your_domain;
    ```

    If it does, exit your editor and move on to the next step.

    If it doesn’t, update it to match. Then save the file, quit your editor, and verify the syntax of your configuration edits:

    ```
    sudo apache2ctl configtest
    ```

    If you get an error, reopen the virtual host file and check for any typos or missing characters. Once your configuration file’s syntax is correct, reload Apache to load the new configuration:

    ```
    sudo systemctl reload apache2
    ```

3. __Allowing HTTPS Through the Firewall__

    If you have the `ufw` firewall enabled, as recommended by the prerequisite guides, you’ll need to adjust the settings to allow for HTTPS traffic. Luckily, Apache registers a few profiles with ufw upon installation.

    You can see the current setting by typing:

    ```
    sudo ufw status
    ```

    It will probably look like this, meaning that only HTTP traffic is allowed to the web server:

    ```
    Output
    Status: active

    To                         Action      From
    --                         ------      ----
    OpenSSH                    ALLOW       Anywhere                  
    Apache                     ALLOW       Anywhere                  
    OpenSSH (v6)               ALLOW       Anywhere (v6)             
    Apache (v6)                ALLOW       Anywhere (v6)
    ```

    To additionally let in HTTPS traffic, allow the Apache Full profile and delete the redundant Apache profile allowance:

    ```
    sudo ufw allow 'Apache Full'
    sudo ufw delete allow 'Apache'
    ```

    ```
    Output
    Status: active

    To                         Action      From
    --                         ------      ----
    OpenSSH                    ALLOW       Anywhere                  
    Apache Full                ALLOW       Anywhere                  
    OpenSSH (v6)               ALLOW       Anywhere (v6)             
    Apache Full (v6)           ALLOW       Anywhere (v6)  
    ```

    Next, let’s run Certbot and fetch our certificates.

4. __Obtaining an SSL Certificate__

    Certbot provides a variety of ways to obtain SSL certificates through plugins. The Apache plugin will take care of reconfiguring Apache and reloading the config whenever necessary. To use this plugin, type the following:

    ```
    sudo certbot --apache -d your_domain -d www.your_domain
    ```

    This runs certbot with the --apache plugin, using -d to specify the names you’d like the certificate to be valid for.

    If this is your first time running certbot, you will be prompted to enter an email address and agree to the terms of service. After doing so, certbot will communicate with the Let’s Encrypt server, then run a challenge to verify that you control the domain you’re requesting a certificate for.

    If that’s successful, certbot will ask how you’d like to configure your HTTPS settings:

    ```
    Output
    Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
    -------------------------------------------------------------------------------
    1: No redirect - Make no further changes to the webserver configuration.
    2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
    new sites, or if you're confident your site works on HTTPS. You can undo this
    change by editing your web server's configuration.
    -------------------------------------------------------------------------------
    Select the appropriate number [1-2] then [enter] (press 'c' to cancel):
    ```

    Select your choice then hit `ENTER`. The configuration will be updated, and Apache will reload to pick up the new settings. `certbot` will wrap up with a message telling you the process was successful and where your certificates are stored:

    ```
    Output
    IMPORTANT NOTES:
    - Congratulations! Your certificate and chain have been saved at:
    /etc/letsencrypt/live/your_domain/fullchain.pem
    Your key file has been saved at:
    /etc/letsencrypt/live/your_domain/privkey.pem
    Your cert will expire on 2018-07-23. To obtain a new or tweaked
    version of this certificate in the future, simply run certbot again
    with the "certonly" option. To non-interactively renew *all* of
    your certificates, run "certbot renew"
    - Your account credentials have been saved in your Certbot
    configuration directory at /etc/letsencrypt. You should make a
    secure backup of this folder now. This configuration directory will
    also contain certificates and private keys obtained by Certbot so
    making regular backups of this folder is ideal.
    - If you like Certbot, please consider supporting our work by:

    Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
    Donating to EFF:                    https://eff.org/donate-le
    ```

    Your certificates are downloaded, installed, and loaded. Try reloading your website using `https://` and notice your browser’s security indicator. It should indicate that the site is properly secured, usually with a green lock icon. If you test your server using the SSL Labs Server Test, it will get an __`A`__ grade.

5. __Verifying Certbot Auto-Renewal__

    The `certbot` package we installed takes care of renewals by including a renew script to `/etc/cron.d`, which is managed by a systemctl service called certbot.timer. This script runs twice a day and will automatically renew any certificate that’s within thirty days of expiration.

    To check the status of this service and make sure it’s active and running, you can use:

    ```
    sudo systemctl status certbot.timer
    ```

    You’ll get output similar to this:

    ```
    Output
    ● certbot.timer - Run certbot twice daily
        Loaded: loaded (/lib/systemd/system/certbot.timer; enabled; vendor preset: enabled)
        Active: active (waiting) since Tue 2020-04-28 17:57:48 UTC; 17h ago
        Trigger: Wed 2020-04-29 23:50:31 UTC; 12h left
    Triggers: ● certbot.service

    Apr 28 17:57:48 fine-turtle systemd[1]: Started Run certbot twice daily.
    ```

    To test the renewal process, you can do a dry run with `certbot`:
    
    ```
    sudo certbot renew --dry-run
    ```

    If you see no errors, you’re all set. When necessary, Certbot will renew your certificates and reload Apache to pick up the changes. If the automated renewal process ever fails, Let’s Encrypt will send a message to the email you specified, warning you when your certificate is about to expire.
