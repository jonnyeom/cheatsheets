# Topics
[PHP](#php)  
[SQL](#sql)  
[Drupal 8](#drupal-8)  
[Theme](#theme)  
[JS](#js)  
[Linux](#linux)  
[Other](#other)  

<br>
<br>

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

##### Classes ...
* EntityReferenceFieldItemList
	* `->referencedEntities();`
	* `->first()->get('entity')->getTarget()->getValue()` > Gets Referenced Entities.

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
* EXPORT command
  ```
  export XDEBUG_CONFIG="idekey=PHPSTORM"
  ```
* php.ini
  ```
  xdebug.remote_enable = 1 
  xdebug.remote_connect_back = 1 
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

### Other Notes to self
* When creating a custom views filter, make sure you create a schema file to map the schema settings. (e.g. `config/schema/module.views.schema.yml`)

<br>
<br>
