# After cloning this project make the following:

## Install the googleapi sdk in your project
``composer install``
## Change the API call version at this file
``` /path/to/project/vendor/google/apiclient-services/src/Google/Service/Drive.php, _constructor```

from
```
$this->servicePath = 'drive/v3/';
$this->version = 'v3';
```
to 
```
$this->servicePath = 'drive/v2/';
$this->version = 'v2';
```

## Adding the GoogleApiComponent
 in the ``common/config/main-local.php`` inside the ``components`` array add these lines
```
'GoogleApiComponent' => [      
   'class' => 'common\components\GoogleApiComponent',
 ],
```

## Enable pretty URL
in the ``common/config/main-local.php`` inside the ``components`` array add these lines
```
'urlManager' => [
   'class' => 'yii\web\UrlManager',
   'showScriptName' => false,
   'enablePrettyUrl' => true,
   'rules' => array(
      '<controller:\w+>/<id:\d+>' => '<controller>/view',
      '<controller:\w+>/<action:\w+>/<id:\d+>' => '<controller>/<action>',
      '<controller:\w+>/<action:\w+>' => '<controller>/<action>',
   ),
],
```
Then you need to change the default nginx block to 
```
server {
        charset utf-8;
        client_max_body_size 128M;

        listen 80 default_server;
		listen [::]:80 default_server;

        root        /path/to/project/frontend/web/;
        index       index.php;

        access_log  /path/to/project/frontend-access.log;
        error_log   /path/to/project/frontend-error.log;

        location / {
            # Redirect everything that isn't a real file to index.php
            try_files $uri $uri/ /index.php$is_args$args;
        }

        # uncomment to avoid processing of calls to non-existing static files by Yii
        #location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
        #    try_files $uri =404;
        #}
        #error_page 404 /404.html;

        # deny accessing php files for the /assets directory
        location ~ ^/assets/.*\.php$ {
            deny all;
        }

        location ~ \.php$ {
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            #fastcgi_pass 127.0.0.1:9000;
            fastcgi_pass unix:/var/run/php/php7.1-fpm.sock;
            try_files $uri =404;
        }

        location ~* /\. {
            deny all;
        }
    }
```
## Reload nginx, open your browser and go to 
``http://localhost/site/driveurl ``
