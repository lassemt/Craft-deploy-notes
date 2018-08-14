# Craft-deploy-notes
Some useful notes and links when deploying craft to a digitalocean droplet with serverpilot.

### General links
* [Setting up a New Craft CMS 3 Project](https://nystudio107.com/blog/setting-up-a-craft-cms-3-project) - Notice the method of deployment
* [Serverpilot – How to Create a LEMP Stack (Only Nginx, No Apache)](https://serverpilot.io/community/articles/how-to-create-a-lemp-stack-only-nginx-no-apache.html)
* [Serverpilot – How to Use SSH Public Key Authentication](https://serverpilot.io/community/articles/how-to-use-ssh-public-key-authentication.html)
* [Serverpilot – How to restart services](https://serverpilot.io/community/articles/how-to-restart-services.html)
* [Serverpilot – Customizing the Nginx Configuration
](https://serverpilot.io/community/articles/customize-nginx-settings.html)

### Potential errors during install

##### "Zend OPcache API is restricted by “restrict_api” configuration directive."
If you get this error message while installing Craft with composer, go to and edit `/etc/phpX.X-sp/conf.d/opcache.ini` and change `opcache.restrict_api=1` to a emtpy string, like this `opcache.restrict_api=`. Remember to restart the PHP-FPM with: 
```sh
$ sudo service phpX.X-fpm-sp restart
```

##### Serverpilot's AutoSSL don't like custom LEMP stack shown in url ☝️
Usually this part is the problem because LetsEncrypt usually places a hidden file to validate domain (if the non DNS method is being used). A possible solution (haven't tried yet) will be adding this:  
```
location ^~ /.well-known/acme-challenge/ {
  allow all;
}
```
after "Don't serve hidden files." rule in NGINX config. 

### Potential errors with sending emails from server.

##### Problems with reverse DNS (PTR)
Problem occured when sending mails using postfix. Reverse DNS is fixed by using the a fully qualified domain name(FQDN) as hostname for droplets. To fix this you just have to click the droplet name in the control panel and change it to the relevant FQDN.

#####  Wrong hostname in postfix
Emails sent from the server had the wrong hostname (some "domain.default-fqdn.com" host. To solve this shh into root and do following: 
```sh
$ cd /etc/postfix
$ vim main.cf
```
Look for `myhostname = some-random-bs-hostname` and `smtp_helo_name = $myhostname.default-fqdn.com`. Change these to your relevant hostname eg. `myhostname = domain.com` and `smtp_helo_name = $myhostname`

Don't forget to restart the postfix service: `sudo service postfix restart`.

I found my answer [in this article.](https://adigital.agency/blog/migrating-to-digital-ocean-sink-or-swim)
