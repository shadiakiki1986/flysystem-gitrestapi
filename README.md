# flysystem-git
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

Example code

```php
use League\Flysystem\Filesystem;

// prepare adapter
// http://github.com/shadiakiki1986/git-rest-api-client-php
$git = new \GitRestApi\Client('http://localhost:8081');
$remote = 'https://someone:somepass@github.com/shadiakiki1986/git-data-repo-testDataRepo';
$repo = $git->cloneRemote(self::$remote);

// for writing to the repo, need to set username and email
// not needed if read-only usage
$repo->putConfig('user.name','phpunit test flysystem-git');
$repo->putConfig('user.email','shadiakiki1986@gmail.com');

// initialize filesystem for further usage
$adapter = new \shadiakiki1986\Flysystem\Git($repo,true);
$filesystem = new Filesystem($adapter);
```

