# flysystem-git
[![Build Status](https://travis-ci.org/shadiakiki1986/flysystem-git.svg?branch=master)](https://travis-ci.org/shadiakiki1986/flysystem-git)

Adapter for [flysystem](https://github.com/thephpleague/flysystem/) that interfaces to any git repository served with a [node-git-rest-api](https://github.com/korya/node-git-rest-api) server via [git-rest-api-client-php](https://github.com/shadiakiki1986/git-rest-api-client-php)

## Usage
Install from [packagist](https://packagist.org/packages/shadiakiki1986/flysystem-git)

```bash
composer install shadiakiki1986/flysystem-git
```

Launch a [node-git-rest-api](https://github.com/korya/node-git-rest-api) server

```bash
docker run -p 8081:8081 -it shadiakiki1986/docker-node-git-rest-api
```

Example
```php
<?php
require_once 'vendor/autoload.php';

use League\Flysystem\Filesystem;

// prepare adapter
// http://github.com/shadiakiki1986/git-rest-api-client-php
$git = new \GitRestApi\Client('http://localhost:8081');

// for read-write, include the correct username/password below
$remote = 'https://someone:somepass@github.com/shadiakiki1986/git-data-repo-testDataRepo';
$repo = $git->cloneRemote($remote,1); // passing 1 for clone --depth to save some time for repositories with a long history

// for writing to the remote repo, i.e. if "push" variable below is true, need to set username and email
// not needed if read-only usage
$repo->putConfig('user.name','phpunit test flysystem-git');
$repo->putConfig('user.email','shadiakiki1986@gmail.com');

// initialize filesystem for further usage
$push = false; // set to false to make read/write possible without pushing to remote. Set to true to push to remote
$pull = true; // set to true to skip pulling from the remote repository at each transaction in order to save on time
$adapter = new \shadiakiki1986\Flysystem\Git($repo,$push,$pull);
$filesystem = new Filesystem($adapter);

// read a file
$contents = $filesystem->read('bla');

// if username/password above are correct, can also update the file
$filesystem->update('bla','some new content');

// write to a new file
$filesystem->write('new folder/new file','content of file');
```

