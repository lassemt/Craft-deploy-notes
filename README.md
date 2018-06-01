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
