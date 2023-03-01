
[cli](#cli)  
[composer](#composer)  
[code references](#code-references)  
[drupal](#drupal)  
[docker](#docker)  
[Issue Template](#issue-template)  
[javascript](#js)  
[GatsbyJS](#gatsbyjs)  
[kubernetes](#kubernetes)  
[mac](#mac)  
[notes to self](#notes-to-self)  
[other](#other)  
[PHP](#php)  
[PHPUnit](#phpunit)  
[psql](#psql)  
[slack themes](#slack-themes)  
[sql](#sql)  
[theme](#theme)  
[windows server](#windows-server)  
[xdebug](#xdebug)  
<br>
<br>



### Composer
* Get all composer config
  ```bash
  composer config --list --global
  ```
* Default global home dir
  ```
  ~/.config/composer
  ```

<br>
<br>


### Code References
* Client - https://github.com/KnpLabs/php-github-api/blob/master/lib/Github/Client.php

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

<br>
<br>



### Docker
##### SSH into docker-compose container
```bash
# as default user
# here php is container name
docker-compose -f docker-compose.yml exec -e php /bin/sh

# as root
docker-compose -f docker-compose.yml -f docker-compose.osx.yml exec -e XDEBUG_MODE=off -u root php /bin/sh
```
  
<br>
<br>


### Drupal

##### Log Exception Error
```php
$e = new Exception();
$variables = Error::decodeException($e);
\Drupal::logger('channel')->error('%type: @message in %function (line %line of %file) @backtrace_string.', $error + ['backtrace' => $backtrace]);
```

##### Kill Static Page Cache
- `\Drupal::service('page_cache_kill_switch')->trigger();`
- OR `no_cache` in `routing.yml`

##### settings.local.php
* Prevent caching of classes
`$settings['class_loader_auto_detect'] = FALSE;`

##### Drupal.org Contrib
* Patch file naming `[project_name]-[short-description]-[issue-number]-[comment-number].patch`

##### phpcbf
`../vendor/bin/phpcbf -n --standard="../vendor/drupal/coder/coder_sniffer/Drupal" modules/custom/custom_module_name/`

##### Twig Tricks
* Add this to twig template if not rendering {{ content }}. This properly adds/invalidates cache tags.
`{% set catch_cache = content|render %}`

##### Priority
- Higher comes first. 100 starts before 0
<br>
<br>

**Theme**
* Theme a form

  This Requires a hook_theme().
  ```php
  return [
    'form_id' => [
      'render element' => form
    ]
  ]
  ``` 

**Custom Solr Functions**

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


### JS
* Console log with auto-indents
  ```
  function log(message) {
    console.log('  '.repeat(new Error().stack.match(/\m/g).length -2) + message);
  }
  ```
  
##### GatsbyJS
* Prevent render before running server side js [link](https://www.joshwcomeau.com/react/the-perils-of-rehydration/)
  ```
  const [hasMounted, setHasMounted] = React.useState(false);
  React.useEffect(() => {
      setHasMounted(true);
  }, []);
  if (!hasMounted) {
      return null;
  }
  ```
  
##### Vue
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


### CLI
* Rsync remote to local

  ```
  rsync -ravz --no-perms --no-owner --no-group --omit-dir-times --exclude={php,css,js,google_tag,styles,xmlsitemap} --exclude='*.pdf' server:/srv/www//project/web/sites/default/files/ web/sites/default/files/
  ```
  
* Search for string in all gz files in a directory.
  ```
  find . -name '*.gz' -print0 | xargs -0 zgrep "pattern"
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
  
* List subdirectories in order of size.
  ```bash
  du -sh ./node_modules/* | sort -nr | grep '\dM.*'
  ```
  
* Change default shell
  ```sh
  usermod --shell /bin/bash username
  ```
  
* Append file to another
  ```sh
  cat old_file file_to_append_to | sponge file_to_append_to
  ```
  
* Bash arrow completion
  `.inputrc`
  ```
  $include /etc/inputrc
  ## arrow up
  "\e[A":history-search-backward
  ## arrow down
  "\e[B":history-search-forward
  ```

<br>
<br>


### Kubernetes

* Create login token for user "admin-user"
  ```bash
  kubectl -n namespace-name create token admin-user
  ```

* ssh into kubernetes pod
  ```bash
  kubectl get pods -A
  kubectl exec -it --namespace namespace-name pod-name -- bash
  ```

<br>
<br>


### Mac
* Remount NTFS drive as writable (Install osxfuse + ntfs-3g first)
   ```
   sudo umount /dev/disk2s1
   sudo /usr/local/bin/ntfs-3g /dev/disk2s1 /Volumes/NTFS -olocal -oallow_other -o auto_xattr
   ```

<br>
<br>


### PHP
* Pretty print a variable.
  ```php
  <?php
  highlight_string("<?php\n\$var_name =\n" . var_export($var_name, true) . ";\n?>");
  ?>
  ```
* Install ext-imagick on macos
   ```bash
   brew instal pkg-config
   brew install imagemagick
   pecl install imagick
   ```
* Get configuration file
   ```bash
   php -r "phpinfo();" | grep php.ini
   ```

<br>
<br>


### PHPUnit
* Disable deprecations notifications
  ```xml
      <server name="SYMFONY_DEPRECATIONS_HELPER" value="disabled" />
  ```

<br>
<br>


### psql
* Connect
  ```bash
  psql -h host -d database -U user -W
  ```
* psql commands
  ```psql
  \l \* List databases *\
  \s \* Command History  *\
  \? \* Help  *\
  \q \* Quit  *\
  
  \dt \* List tables *\
  \dn \* List schemas *\
  \df \* List functions *\
  \dv \* List views *\
  \du \* List users + roles *\
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


### Other
* Faster npm install (Also try `npm ci`
  ```
  npm install --prefer-offline --no-audit --progress=false
  ```

* Sample Production deployer Deployment command
  ```
  ./vendor/bin/dep deploy --branch=master production  --override-config
  ```
  
* Sample Issue Close Message
  ```
  Issue resolved.
  Please re-open this ticket for any questions/concerns!
  ```
  100-night
trial
* 502 Bad Gateway Nginx
  ```
  fastcgi_buffers 8 16k;
  fastcgi_buffer_size 32k;
  ```

* Increase 504 Nginx Gateway w/ php-fpm
  You may only need the nginx change
  ```
  /etc/nginx/sites-enabled/192...conf fastcgi_read_timeout = 300;
  /etc/php/7.?/fpm/php.ini max_execution_time = 300;
  /etc/php/7.?/fpm/pool.d/www.conf request_terminate_timeout = 300;
  ```
  
* Dell 5587 boot kernel params
  ```
  acpi_rev_override=1 acpi_osi=! acpi_osi='Windows 2009' acpi_backlight=native
  ```
  
* Vagrant Hostsupdator PWless sudo
  ```
  # /etc/sudoers.d/vagrant_hostsupdater
  
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
  # /etc/sudoers.d/vagrant_syncedfolders
  
  Cmnd_Alias VAGRANT_EXPORTS_CHOWN = /bin/chown 0\:0 /tmp/*
  Cmnd_Alias VAGRANT_EXPORTS_MV = /bin/mv -f /tmp/* /etc/exports
  Cmnd_Alias VAGRANT_NFSD_CHECK = /etc/init.d/nfs-kernel-server status
  Cmnd_Alias VAGRANT_NFSD_START = /etc/init.d/nfs-kernel-server start
  Cmnd_Alias VAGRANT_NFSD_CHECK_2 = /usr/bin/systemctl status --no-pager nfs-server.service
  Cmnd_Alias VAGRANT_NFSD_START_2 = /usr/bin/systemctl start nfs-server.service
  Cmnd_Alias VAGRANT_NFSD_APPLY = /usr/sbin/exportfs -ar
  %jonny ALL=(root) NOPASSWD: VAGRANT_EXPORTS_CHOWN, VAGRANT_EXPORTS_MV, VAGRANT_NFSD_CHECK, VAGRANT_NFSD_START, VAGRANT_NFSD_CHECK_2, VAGRANT_NFSD_START_2, VAGRANT_NFSD_APPLY
  ```
  
<br>
<br>


### Slack Themes
* Drupal `#0c1e3e,#153161,#0678BE,#FFFFFF,#153161,#FFFFFF,#FFA500,#FFA500`
* Drupal `#193549,#E7412A,#FFC600,#005D66,#E7412A,#FFFFFF,#FFC600,#E7412A,#005D66,#FFFFFF`

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
  ```sh
  pv sqlfile.sql | mysql -u user -p dbname
  ```

* sql import error `Unknown collation: 'utf8mb4_0900_ai_ci'`
  ```sh
  # For mac.
  sed -i '' 's/utf8mb4_0900_ai_ci/utf8mb4_unicode_ci/g' 2019-10-26-prod.sql
  
  # For linux.
  sed -e 's/utf8mb4_0900_ai_ci/utf8mb4_unicode_ci/g' -i myfilename.sql
  ```
  Solution from [this github issue](https://github.com/drud/ddev/issues/1902)<br>
  This usually happens when importing from mysql 8
  
* sql import error `@@GLOBAL.GTID_PURGED can only be set when @@GLOBAL.GTID_EXECUTED is empty`
 
  ```sh
  ../vendor/bin/drush sql-dump --extra-dump=--set-gtid-purged=OFF | gzip > /tmp/db-backup.sql.gz
  ```
  Solution from [this stackoverflow issue](https://superuser.com/questions/906843/import-mysql-data-failed-with-error-1839)
  
* sql give SUPER priveleges to a user
  ```sql
  grant super on *.* to 'my_user'@'localhost';
  revoke super on *.* from 'my_user'@'localhost';
  ```
* search within sql file
  ```bash
  sed -n -e 123456p your-file.sql
  sed -n 123455,123457p your-file.sql
  ```

<br>
<br>


### Notes to self
* When creating a custom views filter, make sure you create a schema file to map the schema settings. (e.g. `config/schema/module.views.schema.yml`)

<br>
<br>


### XDebug (Version 3 only)
* Debugging Command Line (with PHPSTORM)
  * (preferred) run these EXPORT commands each time you need it.
    ```
    export XDEBUG_CONFIG="idekey=PHPSTORM mode=debug start_with_request=trigger remote_port=9000"
    export PHP_IDE_CONFIG="serverName=nameOfYourServerInPhpStorm"
    ```
  * OR Set cli php.ini
    ```
    [XDebug]
    zend_extension="/usr/lib/php/7.4/modules/xdebug-2.9.5.so"

    xdebug.mode=develop,debug
    xdebug.client_host=10.0.2.2
    xdebug.client_port=9000
    xdebug.idekey=PHPSTORM
    ```
  
* Debugging Browser Requests
  ```
  xdebug.mode=develop,debug
  xdebug.client_host=10.0.2.2
  xdebug.client_port=9000
  xdebug.start_with_request=yes
  xdebug.idekey = PHPSTORM
  ```
  
* `Cannot accept external Xdebug connection: Cannot evaluate expression 'isset($_SERVER['PHP_IDE_CONFIG'])'`  
  Add `/usr/lib/php/sessionclean` to the session_config line. e.g.  
  **/usr/lib/php/sessionclean**
  > session_config=$(PHP_INI_SCAN_DIR=/etc/php/${version}/${conf_dir}/conf.d/ php${version} -c /etc/php/${version}/${conf_dir}/php.ini -d "xdebug.remote_autostart=0" -d "error_reporting='~E_ALL'" -r 'foreach(ini_get_all("session") as $k => $v) echo "$k=".$v["local_value"]."\n";')

<br>
<br>

