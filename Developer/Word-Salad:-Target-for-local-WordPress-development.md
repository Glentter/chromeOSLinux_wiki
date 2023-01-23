Mon Jan 23 13:38:16 PST 2023
[Word Salad](https://github.com/10up/word-salad) is a Crouton target that installs a LEMP stack for local WordPress development.

Simply install the target by following the instructions on the Word Salad README and start the chroot environment. Word Salad ships with:
* Nginx
* MySQL
* PHP 7.0/php-fpm
* Mailcatcher running on port 1025, accessible at http://localhost:1080
* Memcached with PECL/Memcache for object caching

Word Salad is currently tested only with the xenial release. Pull requests and issue reports are always welcome. Happy coding!