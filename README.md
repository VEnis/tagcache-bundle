TagcacheBundle
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
git submodule add http://github.com/RickySu/TagcacheBundle.git vendor/bundles/RickySu/TagcacheBundle
```

### Download by editing deps file

```
[TagcacheBundle]
    git=http://github.com/RickySu/TagcacheBundle.git
    target=/bundles/RickySu/TagcacheBundle
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
        new RickySu\TagcacheBundle\TagcacheBundle(),
   );
}
```

Configuration
-------------

### Configure cache adapter

```yml
// app/config/config.yml
tagcache:
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

### Using Tagcache for storing data.

```php
<?php
$Tagcache=$container->get('tagcache');

//store cache with Tags:{TagA,TagB} for 300 secs.
$Tagcache->set('Key_For_Store','Data_For_Store',array('TagA','TagB'),300);

//get cache.
$Tagcache->get('Key_For_Store');

//delete cache.
$Tagcache->delete('Key_For_Store');

//delete cache by Tag.
$Tagcache->TagDelete('TagA');

//acquire a lock.If a lock already exists,It will be blocked for 5 secs.
$Tagcache->getLock('Your_Lock_Name',5);

//release a lock.
$Tagcache->releaseLock('Your_Lock_Name');
```

### Controller Cache

#### Controller Setting
```php
<?php
//in Controller
namespace Acme\DemoBundle\Controller;

// these import the "@Tagcache" annotations
use RickySu\TagcacheBundle\Configuration\Tagcache;

class DemoController extends Controller
{
    /**
     * @Route("/hello/{name}", name="_demo_hello")
     * @Tagcache(expires=600,cache=true)
     * @Template()
     */
    public function helloAction($name)
    {
        return array('name' => $name);
    }

    /**
     * @Route("/test", name="_demo_test")
     * @Tagcache(expires=600,tags={"TagA","TagB"},cachekey=custom_cache_key,cache=true)
     * @Template()
     */
    public function testAction()
    {
        return;
    }
}
```

#### View Setting(Twig)

```html+jinja
{#in view render a controller#}
{%render 'AcmeDemoBundle:Demo:test' with {
     '_Tagcache':    {
           'key':    'custom_cache_key',
           'tags':   {'TagC','TagD'},
           'expires': 300
     }
}%}
```

#### Note

If you both define cache params in view and controller. _Tagcache in view will overwrite controller annotations.
But remember,controller annotation config "cache" must set to true,If you want to turn on controller cache.

TODO
----

* command line tools for clear cache.
* debug information for controller cache.

LICENSE
-------

MIT