# PHP-FPM image

A base image for php-fpm that can also be used for php-cli

## xdebug 

You can pass an environment variable to the docker container named `XDEBUG_CONFIG`.
If you do the xdebug module will be enabled. If not the module is disabled to there
will be no performance drawback.

## blackfire 

You can pass an environment variable to the docker container named `BLACKFIRE_SOCKET`.
If you do the blackfire module will be enabled. If not the module is disabled.
