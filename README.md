
![bbb-install.sh](images/bbb-install.png?raw=true "bbb-install.sh")

# bbb-install

`bbb-install` is a BASH script that will install BigBlueButton [BigBlueButton 2.0](http://docs.bigbluebutton.org/2.0/20overview.html) on a Ubuntu 16.04 64-bit server that meets the [minimal server requirements](http://docs.bigbluebutton.org/install/install.html#minimum-server-requirements)?  If your server has the necessary ports open to your users (see previous like for requirements), then you can install BigBlueButton with a single command.

~~~
wget -qO- https://ubuntu.bigbluebutton.org/bbb-install.sh | bash -s -- -v xenial-200
~~~

This will take about 15 minutes (depending on your server's internet connection).  After it finishes, you'll see a confirmation message that BigBlueButton is running and listening on the server's external IP address.  The script also installs the bbb-api demos so you can immediately try out the server.

~~~
# Warning: The API demos are installed and accessible from:
#
#    http://aaa.bbb.ccc.ddd/demo/demo1.jsp
#
# These API demos allow anyone to access your server without authentication
# to create/manage meetings and recordings. They are for testing purposes only.
# If you are running a production system, remove them by running:
#
#    sudo apt-get purge bbb-demo
~~~

To test the server, open the above URL in FireFox (use FireFox initially until you have a secure socket layer (SSL) certificate) and you can join the demo meeting.

The sections below go into futther details on quickly configuring your server with `bbb-install.sh`.


## Overview

The `bbb-install.sh` is a shell script that automates the [install steps](http://docs.bigbluebutton.org/2.0/20install.html#step-by-step-install) for installing BigBlueButton 2.0.

You'll need a server that meets the [minimal server requirements](http://docs.bigbluebutton.org/install/install.html#minimum-server-requirements).  This can be a dedicated or virtual server.

Furthermore, if you setup a fully qualified domain name (FQDN), such as `bbb.example.com`, that resolves to the external IP address of the server, then you can use `bbb-install.sh` to also install
  * a 4096 bit secure socket layers (SSL) certificate from Let's Encrypt,
  * the latest developer build of the HTML5 client, and/or
  * the GreenLight front-end.

We recommend installing a SSL certificate for production BigBlueButton servers as the certificate is a requirement for both Chrome and Safari to enable WebRTC audio for the user.

The source for the script is hosted at [github](https://github.com/bigbluebutton/bbb-install).  While you could clone this repository an execute `bbb-install.sh` at the command line, to make it easy for anyone to run the script with a single command, we host the latest version at `https://ubuntu.bigbluebutton.org/bbb-install.sh`.

### Server choices

Many companies, such as [Digital Ocean](https://www.digitalocean.com/), offer virtual and bare metal servers that provide a Ubuntu 16.04 64-bit server with single public IP address (no firewall).

Other companies, such as [ScaleWay](https://www.scaleway.com/), [Google Compute Engine](https://cloud.google.com/compute/) offer servers that are setup behind network address translation (NAT).  That is, they have both an internal and external IP address.  The `bbb-install.sh` will do a bit of sluthing detect if the server has internal/external address and configure BigBlueButton accordingly.

If your server is behind a firewall, such as on Amazon EC2 instance, then there are a few additional steps to configure the settings on your firewall (given below).

Finaly, if you find `bbb-install.sh` is unable to configure server behind NAT, we recommend going through the [step-by-step](http://docs.bigbluebutton.org/2.0/20install.html#step-by-step-install) for installing BigBlueButton.


### Configuring the EC2 firewall

If you want to install BigBlueButton 2.0 on an Amazon's EC2 instance -- we recommend a c5.xlarge instance type (or faster) when using EC2 -- then before your run `bbb-install.sh` you first need to configure the server's security group (a 'security group' is Amazon's term for a firewall) to allow incoming traffic on the following ports:

  * TCP/IP port 22 (for SSH)
  * TCP/IP ports 80/443 (for HTTP/HTTPS)
  * TCP/IP port 1935 (for RTMP)
  * UDP ports in the range 16384 - 32768 (for FreeSWITCH/HTML5 client RTP streams)

Here's a screen shot of what the security group configuration should look like to allow incoming traffic on the above ports:

![Security Group](images/security-group.png?raw=true "Security Group")

Before setting up a hostname, you need to assign an Elastic IP to the server so the IP does not change on reboot.

We also created in [installation video on EC2](https://youtu.be/-E9WIrH_yTs) going through the above steps.

### Installation Videos

Watch this [Install using bbb-install.sh on Digital Ocean](https://youtu.be/D1iYEwxzk0M) for a walkthrough of the configuration options for installing BigBlueButton 2.0 on Digital Ocean with `bbb-install.sh`.

Watch this [Install using bbb-install.sh on EC2](https://youtu.be/-E9WIrH_yTs) for a walkthrough of installing BigBlueButton 2.0 on Amazon EC2 using `bbb-install.sh`.

# Command options

You can get help by passing the `-h` option.

~~~
BigBlueButon 2.0 installer script

USAGE:
    bbb-install.sh [OPTIONS]

OPTIONS:

  -v <version>     Install given version of BigBlueButton (e.g. 'xenial-200') (required)

  -s <hostname>    Configure server with <hostname>
  -e <email>       Install SSL certificate from Let's Encrypt using <email>

  -t               Install HTML5 client (currently under development)
  -g               Install GreenLight

  -p <host>        Use apt-get proxy at <host>

  -h               Print help

EXAMPLES:

    ./bbb-install.sh -v xenial-200
    ./bbb-install.sh -v xenial-200 -s bbb.example.com -e info@example.com
    ./bbb-install.sh -v xenial-200 -s bbb.example.com -e info@example.com -t -g

SUPPORT:
     Source: https://github.com/bigbluebutton/bbb-install
   Commnity: https://bigbluebutton.org/support
~~~

## Install and configure with an IP address (no SSL)

To install BigBlueButton on a Ubuntu 16.04 64-bit server, login as root and run the following command:

~~~
wget -qO- https://ubuntu.bigbluebutton.org/bbb-install.sh | bash -s -- -v xenial-200
~~~

That's it.  The installation should finish with the following message:

~~~
** Potential problems described below **

......
# Warning: The API demos are installed and accessible from:
#
#    http://xxx.xxx.xxx.xxx/demo/demo1.jsp
#
# These API demos allow anyone to access your server without authentication
# to create/manage meetings and recordings. They are for testing purposes only.
# If you are running a production system, remove them by running:
#
#    sudo apt-get purge bbb-demo
~~~

The script also installs the `bbb-demo` package, so you can immediately test out the install.

If you want to remove the API demos, do the command

~~~
sudo apt-get purge bbb-demo
~~~

If you want to use this server with a front-end, such as Moodle, you can get the server's URL and shared secret with the command `sudo bbb-conf --secret`.

~~~
# bbb-conf --secret

       URL: http://xxx.xxx.xxx.xxx4/bigbluebutton/
    Secret: yyy

      Link to the API-Mate:
      http://mconf.github.io/api-mate/#server=http://xxx.xxx.xxx.xxx/bigbluebutton/&sharedSecret=yyy
~~~

Since the server does not have a SSL certificate, use FireFox for sharing audio (at the time of this writing, FireFox does not require SSL to use WebRTC audio).  However, Chrome *does* require a SSL certificate, so users will get an error when sharing their audio with WebRTC.

We recommend installing an SSL certificate (see next section).

## Install with SSL

Before `bbb-install.sh` can install a SSL certificate, you first need to configure a domain name, such as `bbb.example.com`, to resolve to the public IP address of your server.  That is, the command `dig bbb.example.com @8.8.8.8` should resolves to the public IP address of your server.

Next, you need a valid e-mail address, such as `info@example.com`, to receive updates from Let's Encrypt.

With these two pieces of information, you can use `bbb-install.sh` to automate the configuration of BigBlueButton server with an SSL certificate using the following command (here we using the sample hostname and e-mail in the command, but you would need to substitute your serverès hostname and your e-mail address):

~~~
wget -qO- https://ubuntu.bigbluebutton.org/bbb-install.sh | bash -s -- -v xenial-200 -s bbb.example.com -e info@example.com
~~~

The `bbb-install.sh` script will also install a cron job for you to automatically renew the Let's Encrypt certificate so it doesn't expire.

## Install latest build of HTML5 client

To try out the latest build of the latest developer build of the [HTML5 client](http://docs.bigbluebutton.org/html/html5-overview.html), add the `-t` option when setting up SSL (the HTML5 client needs SSL installed on the server).

~~~
wget -qO- https://ubuntu.bigbluebutton.org/bbb-install.sh | bash -s -- -v xenial-200 -s bbb.example.com -e info@example.com -t
~~~

After a few minutes, you'll have the HTML5 client is installed.  Use an Android or iOS (iOS 11+) phone/tablet to access your BigBlueButton server at `https://bbb.example.com/demo/demo1.jsp` and join using the HTML5 client.  The BigBlueButton server will detect when you are connecting from a mobile browser and automatically load the HTML5 client instead of the default Flash client.  Note: the HTML5 client is under [active development](http://docs.bigbluebutton.org/html/html5-overview.html) and is not ready (yet) for production.

## Install GreenLight

If you want to add a frontend to your BigBlueButton server, where users can easily create meetings and invite others, you can install [GreenLight](http://docs.bigbluebutton.org/install/green-light.html) by adding the `-g` option to the SSL options (GreenLight needs SSL installed on the server).

~~~
wget -qO- https://ubuntu.bigbluebutton.org/bbb-install.sh | bash -s -- -v xenial-200 -s bbb.example.com -e info@example.com -g
~~~

You can go to `https://<hostname>/` (where <hostname> is the host name for your BigBlueButton server) to launch the GreenLight interface.  To give users the ability to create and manage recorded meetings, See the GreenLight documentation for [setting up OAuth2 authentication](http://docs.bigbluebutton.org/install/green-light.html#6-configure-oauth2-optional).

## Do everything with a single command

Lastly, you could do all the above with a single command:

~~~
wget -qO- https://ubuntu.bigbluebutton.org/bbb-install.sh | bash -s -- -v xenial-200 -s bbb.example.com -e info@example.com -t -g
~~~

# Troubleshooting and Feedback

## GreenLight not running

If after the initial installation GreenLight does not run (you get a 500 error when accessing it), you can restart GreenLight with the following steps

~~~
docker stop greenlight-v2
docker rm greenlight-v2
docker run -d -p 5000:80 --restart=unless-stopped -v $(pwd)/db/production:/usr/src/app/db/production --env-file env --name greenlight-v2 bigbluebutton/greenlight:v2
~~~

After which, you should be able to open the URL for your server and see the GreenLight interface.

## Getting Help
If you have feedback on the script, or need help using it, please post to the [BigBlueButton Setup](https://bigbluebutton.org/support/community/) mailing list and we'll help you there.

If you encounter an error with this script, please open [GitHub issue](https://github.com/bigbluebutton/bbb-install/issues) and provide steps to reproduce the issue.


# Limitations

If you are running your BigBlueButton behind a firewall, such as on EC2, this script will not configure your firewall.
