TagCacheBundle
==============

Introduction
------------

This bundle provides cache with tags.

Features
------------

* Stores a cache with multiple tags. And deletes cache by using tag.
* Provides controller cache.

Requirements
------------

* Annotations for Controllers.

Installation
------------

Download the code by adding the git module or editing the deps file in the root project.

### Download via git submodule

```
git submodule add http://github.com/RickySu/TagCacheBundle.git vendor/bundles/RickySu/TagCacheBundle
```

### Download by editing deps file

```
[TagCacheBundle]
    git=http://github.com/RickySu/TagCacheBundle.git
    target=/bundles/RickySu/TagCacheBundle
```

### Register namespace :

```php
<?php
// app/autoload.php
$loader->registerNamespaces(array(
   // ...
   'RickySu'                       => __DIR__.'/../vendor/bundles',
));
```

### Instantiate Bundle :

```php
<?php
//app/AppKernel.hpp
public function registerBundles()
{
   $bundles = array(
        // ...
        new RickySu\TagCacheBundle\TagCacheBundle(),
   );
}
```

Configuration
-------------

### Configure cache adapter

```yml
// app/config.yml
tag_cache:
    driver:     Memcache
    namespace:  'Name_Space_For_Your_Project'
    options:
        hashkey: true
        enable_largeobject:    false
        cache_dir:  'Temp_Cache_File_Store_Path'
        servers:
            -    'localhost:11211:10'
            -    'otherhost:11211:20'
```

#### driver

The cache driver. Currently support "Memcache,Memcached,File,Sqlite,Apc,Nullcache". Nullcache is for testing only.

#### hashkey

some driver like Memcached,only support 250 characters key length. Enable this option will use md5 hashed key.

#### enable_largeobject

Memcache cannot store object over 1MB.Enable these option will fix this issue,but cause lower performance.default false.

#### servers

Memcache server configs. format => "Host:Port:Weight"


How to Use
----------

### Using TagCache for storing data.

```php
<?php
$TagCache=$container->get('tag_cache');

//store cache with Tags:{TagA,TagB} for 300 secs.
$TagCache->set('Key_For_Store','Data_For_Store',array('TagA','TagB'),300);

//get cache.
$TagCache->get('Key_For_Store');

//delete cache.
$TagCache->delete('Key_For_Store');

//delete cache by Tag.
$TagCache->TagDelete('TagA');

//acquire a lock.If a lock already exists,It will be blocked for 5 secs.
$TagCache->getLock('Your_Lock_Name',5);

//release a lock.
$TagCache->releaseLock('Your_Lock_Name');
```

### Controller Cache

```php
<?php
//in Controller
namespace Acme\DemoBundle\Controller;

// these import the "@TagCache" annotations
use RickySu\TagCacheBundle\Configuration\TagCache;

class DemoController extends Controller
{
    /**
     * @Route("/hello/{name}", name="_demo_hello")
     * @TagCache(expires=600,cache=true)
     * @Template()
     */
    public function helloAction($name)
    {  
        return array('name' => $name);
    }

    /**
     * @Route("/test", name="_demo_test")
     * @TagCache(expires=600,tags={"TagA","TagB"},,cache=true)
     * @Template()
     */
    public function testAction()
    {  
        return;
    }
}

