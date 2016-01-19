# How to Create and Publish Your PHP Package with Composer (Status: Outline)

#### Create directory
```
$ mkdir string-trimmer
```

#### Make a composer repos (Follow prompt)
```
$ composer init

// Package name: string-trimmer/string-trimmer
// Description: String utility to trim white space
// Author: <Your name>
// minimum stability: ????
// package type: ???
// License: MIT
// Would you like to define your dependencies?: No
// Would you like to define your dev dependencies?: No
// Do you confirm generation? Yes
```
> NOTE: Now you'll have a composer.json

#### Add autoloading to your composer.json 

```
{
  ...
  "autoload":{
    "psr-4": {
      "StringTrimmer\\": "src/"
    }
  },
  ...
```

#### ???? 

```
  ...
  "minimum-stability": "dev",
  ...
```

#### Install the autoloader 

```
$ composer install
```
> This will create the ``vendor/`` directory and create ``vendor/autoloader.php`` file, which would make it possible for our project to have autoloading

How could we then use autoloading?

```
require_once 'vendor/autoload.php';
```

Example:

```
require_once 'vendor/autoload.php';

use WooWee\Brah;
$client = new GuzzleHttp\Client();

echo Brah::hi();
echo StringTrimmer\StringTrimmer::left("       NOO!");
```

NOTE: I only think we the autoload file if we are using other packagist 

#### Create a class in the package 
> Must be at src/StringTrimmer.php

```
<?php
namespace StringTrimmer;

class StringTrimmer {
  public static function left($s){
    return ltrim($s);
  }
}
?>
```

#### Push code to GitHub

#### Add package to packagist

#### Version control the packagist

You do this by creating tags (ie. "v1.0.0.0" or "1.0.0.0")

> NOTE: Everytime you create a new branch, it becomes available as a version of the package

## How could people install a version of the packages now?

????
