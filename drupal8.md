# Topics
[PHP](#php)  
[SQL](#sql)  
[Drupal 8](#drupal-8)  
[Theme](#theme)  
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

<br>
<br>



### Drupal 8

##### Get Current ...
* Node > `$node = \Drupal::routeMatch()->getParameter('node');`
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
`../vendor/bin/phpcbf -n --standard="modules/contrib/coder/coder_sniffer/Drupal" modules/custom/custom_module_name/`

##### settings.local.php
* Prevent caching of classes
`$settings['class_loader_auto_detect'] = FALSE;`

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



### Linux
* Rsync remote to local

  ```
  rsync -ravz --omit-dir-times --exclude={php,css,js,google_tag,styles,xmlsitemap} --exclude='*.pdf' server:/srv/www//project/web/sites/default/files/ web/sites/default/files/
  ```
  
<br>
<br>



### Other Notes to self
* When creating a custom views filter, make sure you create a schema file to map the schema settings. (e.g. `config/schema/module.views.schema.yml`)

<br>
<br>
