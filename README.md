# OpCompiler
How to use Zend OPCache functions to protect your PHP code.
##Introduction
Up to PHP5.3 I used to encode PHP files with the [BCompiler](https://pecl.php.net/package/bcompiler). Inpired by [Bencoder](http://bencoder.urdada.net/) I have built my own scripts to encode and deploy.
Now with PHP7.0 is possible again to achieve similar behavior.

##Requirements

PHP7.0+

##Setup
Create a directory called */var/www/opcache_files* or whatever name.
Create a file called */var/www/opcache_files/blacklist.txt*, here is possible to specify files to be ignored. [See details](http://php.net/manual/en/opcache.configuration.php#ini.opcache.blacklist-filename).
Ensure OPCache is installed and enabled.
```bash
#phpenmod opcache
#php -m
```
Open php.ini in your text editor and enable the use of files in opcache section. See below example.
```bash
#nano /etc/php/7.2/mods-available/opcache.ini
```
Put the following lines.
```conf
opcache.blacklist_filename = /var/www/opcache_files/blacklist.txt
opcache.file_cache = /var/www/opcache_files
opcache.file_cache_only = 1
opcache.validate_timestamps = 0
opcache.enable_cli = 1
```
##The Code
```php
<?php
$file = "/var/www/info.php";

$uid = posix_getuid();
if ($uid) die("Must be root\n");

//We need to execute command as user www-data
$www_data = posix_getpwnam("www-data");
posix_setuid((int)$www_data["uid"]);

//Invalidate with force parameter
opcache_invalidate($file, true);

//Compile 
opcache_compile_file($file);
```
Seems like enabling *file_cache_only* will disable the *opcache_compile_file* function, [see](https://bugs.php.net/bug.php?id=72199). So run the code with the following command to avoid that bug.
```bash
#php -d opcache.file_cache_only=0 encode_file.php
```

##Donate

<a href="bitcoin:17PHL7Rw9rHujmRHkkPQGspLQ7rsDFV574"><img src="https://raw.githubusercontent.com/ivanhennig/op-compiler/master/donate.png" alt="17PHL7Rw9rHujmRHkkPQGspLQ7rsDFV574" title="17PHL7Rw9rHujmRHkkPQGspLQ7rsDFV574"/></a>

##References

[BCompiler](https://pecl.php.net/package/bcompiler)
[Bencoder](http://bencoder.urdada.net/)
[Binary Webshell Through OPcache in PHP 7](http://gosecure.net/2016/04/27/binary-webshell-through-opcache-in-php-7/)


