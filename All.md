# Topics
[Issue Template](#issue-template)  
[PHP](#php)  
[SQL](#sql)  
[Drupal 8](#drupal-8)  
[Theme](#theme)  
[JS](#js)  
[Linux](#linux)  
[Other](#other)  

<br>
<br>

### Issue Template
```
### Description
Describe your issue here.

### Environment
* version of angular-translate
* version of angular
* which browser and its version

### Steps to reproduce
Tell us how to reproduce this issue. Please provide a working demo, you can use [this template](https://plnkr.co/edit/XorWgI?p=preview) as a base.

### Expected behavior
Tell us what should happen

### Actual behavior
Tell us what happens instead
```

### PHP
* Pretty print a variable.
  ```php
  <?php
  highlight_string("<?php\n\$var_name =\n" . var_export($var_name, true) . ";\n?>");
  ?>
  ```

<br>
<br>

### SQL
* Show sql tables by size
  ```sql
  SELECT (data_length+index_length)/power(1024,2) tablesize_mb, table_name 
  FROM information_schema.tables 
  WHERE table_schema='db_name' order by tablesize_mb;
  ```
  
* Show Sql Import status
  ```
  pv sqlfile.sql | mysql -u user -p dbname
  ```

<br>
<br>

### Drupal 8

##### Get Current ...
* Node > `$node = \Drupal::routeMatch()->getParameter('node');`
* Path > `$current_path = \Drupal::service('path.current')->getPath();`
* URL > `\Drupal::request()->getSchemeAndHttpHost()` = http://site.dev
* User Account > `\Drupal::currentUser()` or get `current_user`from $container.
* User Object > `\Drupal\user\Entity\User::load($id)`. This can be retrieved from the User Account.

##### Log Exception Error
```php
$e = new Exception();
$variables = Error::decodeException($e);
\Drupal::logger('channel')->error('%type: @message in %function (line %line of %file) @backtrace_string.', $error + ['backtrace' => $backtrace]);
```

##### Kill Static Page Cache
`\Drupal::service('page_cache_kill_switch')->trigger();`

##### Load module code
` \Drupal::moduleHandler()->loadInclude('module_name', 'module');`

##### Drupal.org Contrib
* Patch file naming `[project_name]-[short-description]-[issue-number]-[comment-number].patch`

##### phpcbf
`../vendor/bin/phpcbf -n --standard="../vendor/drupal/coder/coder_sniffer/Drupal" modules/custom/custom_module_name/`

##### settings.local.php
* Prevent caching of classes
`$settings['class_loader_auto_detect'] = FALSE;`

##### Twig Tricks
* Add this to twig template if not rendering {{ content }}. This properly adds/invalidates cache tags.
`{% set catch_cache = content|render %}`

<br>
<br>

##### Theme
* Theme a form

  This Requires a hook_theme().
  ```php
  return [
    'form_id' => [
      'render element' => form
    ]
  ]
  ``` 

<br>
<br>

### JS
* Console log with auto-indents
  ```
  function log(message) {
    console.log('  '.repeat(new Error().stack.match(/\m/g).length -2) + message);
  }
  ```
  
#### Vue
* No Chunks, No Hash
   ```
   // vue.config.js
   module.exports = {
     filenameHashing: false,
     chainWebpack: config => {
       config.optimization.delete('splitChunks')
     }
   }
   ```

<br>
<br>

### Linux
* Rsync remote to local

  ```
  rsync -ravz --omit-dir-times --exclude={php,css,js,google_tag,styles,xmlsitemap} --exclude='*.pdf' server:/srv/www//project/web/sites/default/files/ web/sites/default/files/
  ```
  
* Search for string in all files in a directory.
  ```
  grep -inr "Text" folder/to/be/searched/
  ```
  
* Command for all directories in a directory.
  ```
  find files -type d -exec chmod 775 {} \;
  ```
  
* Command for all files in a directory.
  ```
  find files -type f -exec chmod 664 {} \;
  ```

<br>
<br>

### XDebug
* EXPORT command for cli debugging
  ```
  export XDEBUG_CONFIG="idekey=PHPSTORM remote_enable=1 remote_mode=req remote_port=9000 remote_host=10.0.2.2 remote_connect_back=0"
  export PHP_IDE_CONFIG="serverName=nameOfYourServer"
  ```
* php.ini
  ```
  xdebug.remote_enable = 1 
  xdebug.remote_connect_back = 0
  xdebug.max_nesting_level = 300 
  xdebug.idekey = PHPSTORM
  ```
* cli php.ini
  ```
  [XDebug]
  zend_extension="/usr/lib/php/7.2/modules/xdebug-2.6.0.so"

  xdebug.remote_autostart=true
  xdebug.remote_enable=1
  xdebug.remote_connect=1
  xdebug.remote_host=10.0.2.2
  xdebug.remote_port=9000
  xdebug.max_nesting_level=500
  xdebug.idekey=PHPSTORM
  ```

<br>
<br>

### Windows Server
* Start MySQL
```
net start MySQL
```
  
<br>
<br>

### Custom Solr Functions

Drupal: hook_search_api_solr_query_alter()
```php
// If the date is in the future, give the score a diminshing boost.
$solr_field_names = $query->getIndex()->getServerInstance()->getBackend()->getSolrFieldNames($query->getIndex());
$boost_functions = [];
if ($solr_field_names['future_only_date']) {
  $boost_functions[] = 'if(min(0,sub(ms(NOW/HOUR),ms(' . $solr_field_names['future_only_date'] . '))),recip(ms(' . $solr_field_names['future_only_date'] . ',NOW/HOUR),3.16e-11,3,.9),0)^2';
}

// Boost content_type_to_match Content based on the published date.
// The closer the date is to the current date, the higher the boost.
if ($solr_field_names['published_date']) {
  $boost_functions[] = 'if(eq(' . $solr_field_names['type'] .',\'content_type_to_match\'),recip(abs(ms(' . $solr_field_names['published_date'] . ',NOW/HOUR)),3.16e-11,3,.9),0)^2';
}

// Apply all the boost functions.
if ($boost_functions) {
  $boost_functions = implode(' ', $boost_functions);
  $solarium_query->getEDisMax()->setBoostFunctions($boost_functions);
  $solarium_query->addParam('defType', 'edismax');
}
```
Links to helpful webpages with graphs.
* https://atendesigngroup.com/blog/drupal-8-apache-solr-boost-search-term-relevance-publish-date
* https://www.hashbangcode.com/article/drupal-8-date-search-boosting-search-api-and-solr-search
  
<br>
<br>

### Other
* Sample Production deployer Deployment command
  ```
  ./vendor/bin/dep deploy --branch=master production  --override-config
  ```
  
* Sample Issue Close Message
  ```
  Issue resolved.
  Please re-open this ticket for any questions/concerns!
  ```
  
* 502 Bad Gateway Nginx
  ```
  fastcgi_buffers 8 16k;
  fastcgi_buffer_size 32k;
  ```

  
* Increase 504 Nginx Gateway w/ php-fpm
  ```
  /etc/php/7.?/fpm/php.ini max_execution_time = 300;
  /etc/php/7.?/fpm/pool.d/www.conf reequest_terminate_timeout = 300;
  /etc/nginx/sites-enabled/192...conf fastcgi_read_timeout = 300;
  ```
  
* Dell 5587 boot kernel params
  ```
  acpi_rev_override=1 acpi_osi=! acpi_osi='Windows 2009' acpi_backlight=native
  ```
  
* Vagrant Hostsupdator PWless sudo
  ```
  vagrant ALL=(ALL) NOPASSWD:ALL
  Defaults:vagrant !requiretty

  # Allow passwordless startup of Vagrant with vagrant-hostsupdater.
  Cmnd_Alias VAGRANT_HOSTS_ADD = /usr/bin/sh -c echo "*" >> /etc/hosts
  Cmnd_Alias VAGRANT_HOSTS_ADD_1 = /usr/bin/sh -c 'echo "*" >> /etc/hosts'
  Cmnd_Alias VAGRANT_HOSTS_ADD_2 = /bin/sh -c echo "*" >> /etc/hosts
  Cmnd_Alias VAGRANT_HOSTS_REMOVE = /usr/bin/sed -i -e /*/ d /etc/hosts
  Cmnd_Alias VAGRANT_HOSTS_REMOVE_1 = /bin/sed -i -e /*/ d /etc/hosts
  %jonny ALL=(root) NOPASSWD: VAGRANT_HOSTS_ADD, VAGRANT_HOSTS_ADD_1, VAGRANT_HOSTS_ADD_2, VAGRANT_HOSTS_REMOVE, VAGRAND_HOSTS_REMOVE_1
  ```
  
* Vagrant Syncedfolders PWless sudo
  ```
  Cmnd_Alias VAGRANT_EXPORTS_CHOWN = /bin/chown 0\:0 /tmp/*
  Cmnd_Alias VAGRANT_EXPORTS_MV = /bin/mv -f /tmp/* /etc/exports
  Cmnd_Alias VAGRANT_NFSD_CHECK = /etc/init.d/nfs-kernel-server status
  Cmnd_Alias VAGRANT_NFSD_START = /etc/init.d/nfs-kernel-server start
  Cmnd_Alias VAGRANT_NFSD_CHECK_2 = /usr/bin/systemctl status --no-pager nfs-server.service
  Cmnd_Alias VAGRANT_NFSD_START_2 = /usr/bin/systemctl start nfs-server.service
  Cmnd_Alias VAGRANT_NFSD_APPLY = /usr/sbin/exportfs -ar
  %jonny ALL=(root) NOPASSWD: VAGRANT_EXPORTS_CHOWN, VAGRANT_EXPORTS_MV, VAGRANT_NFSD_CHECK, VAGRANT_NFSD_START, VAGRANT_NFSD_CHECK_2, VAGRANT_NFSD_START_2, VAGRANT_NFSD_APPLY
  ```
  
### Slack Themes
* Drupal `#0c1e3e,#153161,#0678BE,#FFFFFF,#153161,#FFFFFF,#FFA500,#FFA500`

### Other Notes to self
* When creating a custom views filter, make sure you create a schema file to map the schema settings. (e.g. `config/schema/module.views.schema.yml`)

<br>
<br>
