# Jackalope [![Build Status](https://secure.travis-ci.org/jackalope/jackalope-prismic.png?branch=master)](http://travis-ci.org/jackalope/jackalope-prismic)

Implementation of the PHP Content Repository API ([PHPCR](http://phpcr.github.io))
using a relational database to persist data.

Jackalope uses the http://prismic.io service for storage.

For the moment, it is less feature complete, performant and robust than
[Jackalope-Jackrabbit](http://github.com/jackalope/jackalope-jackrabbit).

Discuss on jackalope-dev@googlegroups.com or visit #jackalope on irc.freenode.net

## License

This code is dual licensed under the MIT license and the Apache License Version
2.0. Please see the file LICENSE in this folder.


# Preconditions

* php >= 5.3
* One of the following databases, including the PDO extension for it:
    * MySQL >= 5.1.5 (we need the ExtractValue function)
    * PostgreSQL
    * SQLite


# Installation

The recommended way to install jackalope is through [composer](http://getcomposer.org/).
You can of course do without, but then you will need to resolve the dependencies
manually.

```sh
$ mkdir my-project
$ cd my-project
$ curl -s http://getcomposer.org/installer | php
$ ./composer.phar init
$ ./composer.phar require jackalope/jackalope-prismic
$ ./composer.phar install
```

## Create a repository

Create a login on http://prismic.io and follow the instructions there.

## Enable the commands

There are a couple of useful commands to interact with the repository.

To use the console, copy ``cli-config.php.dist`` to ``cli-config.php`` and configure
the connection parameters.
Then you can run the commands from the jackalope directory with ``./bin/jackalope``

NOTE: If you are using PHPCR inside of **Symfony**, the DoctrinePHPCRBundle
provides the commands inside the normal Symfony console and you don't need to
prepare anything special.

You have many useful commands available from the phpcr-utils. To get a list of
all commands, type:

```sh
$ ./bin/jackalope
```

To get more information on a specific command, use the `help` command. To learn
more about the `phpcr:workspace:export` command for example, you would type:

```sh
$ ./bin/jackalope help phpcr:workspace:export
```

# Bootstrapping

Jackalope relies on autoloading. Namespaces and folders are compliant with
PSR-0. You should use the autoload file generated by composer:
``vendor/autoload.php``

If you want to integrate jackalope into other PSR-0 compliant code and use your
own classloader, find the mapping in ``vendor/composer/autoload_namespaces.php``

Before you can use jackalope with a database, you need to set the database up.
Create a database as described above, then make sure the command line utility
is set up (see above "Enable the commands"). Now you can run:

```sh
$ bin/jackalope jackalope:init:dbal
```

Once these steps are done, you can bootstrap the library. A minimalist
sample code to get a PHPCR session with the prismic backend:

```php
$uri = 'https://%s.prismic.io/api';
$workspace  = 'lesbonneschoses'; // phpcr workspace to use

$factory = new \Jackalope\RepositoryFactoryPrismic();
$repository = $factory->getRepository(array('jackalope.prismic_uri' => $uri));

// dummy credentials to comply with the API
$credentials = new \PHPCR\SimpleCredentials(null, null);
$session = $repository->login($credentials, $workspace);
```

# Usage

The entry point is to create the repository factory. The factory specifies the
storage backend as well. From this point on, there are no differences in the
usage (except for supported features, that is).

```php
// see Bootstrapping for how to get the session.

$rootNode = $session->getNode("/");
```

See [PHPCR Tutorial](https://github.com/phpcr/phpcr-docs/blob/master/tutorial/Tutorial.md)
for a more detailed tutorial on how to use the PHPCR API.


# Performance tweaks

# Advanced configuration

## Logging

Jackalope supports logging, for example to investigate the number and type of
queries used. To enable logging, provide a logger instance to the repository
factory:

```php
$factory = new \Jackalope\RepositoryFactoryPrismic();
$logger = new Jackalope\Transport\Logging\DebugStack();
$options = array(
  'jackalope.uri' => $uri,
  'jackalope.logger' => $logger,
);
$repository = $factory->getRepository($options);

//...

// at the end, output debug information
var_dump($logger->calls);
```

You can also wrap a [PSR-3](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md)
compatible logger like [monolog](https://github.com/Seldaek/monolog) with the
Psr3Logger class.

Note that when using jackalope in Symfony2, the logger is integrated in the
debug toolbar.

# Implementation notes

See [doc/architecture.md](https://github.com/jackalope/jackalope/blob/master/doc/architecture.md)
for an introduction how Jackalope is built. Have a look at the source files and
generate the phpdoc.

# Running the tests

Jackalope-prismic is integrated with the phpcr-api-tests suite that tests
all PHPCR functionality.

If you want to run the tests, please see the [README file in the tests folder](https://github.com/jackalope/jackalope-prismic/blob/master/tests/README.md).


# Things left to do

The best overview of what needs to be done are the skipped API tests.
Have a look at [ImplementationLoader](https://github.com/jackalope/jackalope-prismic/blob/master/tests/ImplementationLoader.php)
to see what is currently not working and start hacking :-)

Also have a look at the issue trackers of this project and the base jackalope/jackalope.


# Contributors

* Christian Stocker <chregu@liip.ch>
* David Buchmann <david@liip.ch>
* Tobias Ebnöther <ebi@liip.ch>
* Roland Schilter <roland.schilter@liip.ch>
* Uwe Jäger <uwej711@googlemail.com>
* Lukas Kahwe Smith <smith@pooteeweet.org>
* Benjamin Eberlei <kontakt@beberlei.de>
* Daniel Barsotti <daniel.barsotti@liip.ch>
* [and many others](https://github.com/jackalope/jackalope-prismic/contributors)
