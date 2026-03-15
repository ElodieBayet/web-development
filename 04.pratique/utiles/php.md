
# Guide : Installer PHP

## Windows

À partir de [Downloads - PHP](https://www.php.net/downloads.php), télécharger le`.zip` en **x64 Thread Safe** de la dernière version – par exemple `8.4.13` – et décompresser.

Déplacer le contenu dans un répertoire approprié – par exemple `C:\Program Files\PHP`.

Configurer PHP via le fichier `php.ini` – par exemple `C:\Program Files\PHP\php.ini` – et activer les extensions souhaitées.
```ini
# Quelques extensions courrantes
extension=bz2
extension=curl
extension=fileinfo
extension=gd
extension=gettext
extension=gmp
extension=intl
extension=ldap
extension=mbstring
extension=exif      ; Must be after mbstring as it depends on it
extension=mysqli
extension=openssl
extension=pdo_mysql
extension=pdo_sqlite

extension=soap
extension=sockets
extension=xsl

zend_extension=opcache

; Outil de couverture de code
[XDebug]
zend_extension=xdebug
xdebug.mode=coverage
```

À partir de [PHP Extension Community Library](https://pecl.php.net/packages.php), télécharger les DLL de chaque extension désirées, et les placer dans le sous-dossier `./ext/` de PHP.


### Les versions NTSF

Les versions _Non-Thread-Safe_ conviendraient plutôt pour les [environnements IIS](https://learn.microsoft.com/fr-fr/iis/application-frameworks/install-and-configure-php-on-iis/install-and-configure-php)


---


## WSL / Ubuntu 24.04

⚠️ 09-2025 : Le package **PHP 8.4 est indisponible dans les répertoires des versions actuelle d'Ubuntu et Debian**. Donc les packages de PHP 8.4 doivent venir d'un autre repository : [ondrej/php](https://launchpad.net/~ondrej/+archive/ubuntu/php).


Installer PHP 8.3
```sh
sudo apt install php8.3
```

Lister les modules chargés
```sh
php --modules
```

Lister les extensions disponibles. Appuyer 2x sur la touche tabulation pour obtenir la liste
```sh
sudo apt install php8.3-<TAB>
```

_Apercu des extensions PHP 8.3_
```
php8.3-amqp            php8.3-gd              php8.3-mbstring        php8.3-pspell          php8.3-tideways
php8.3-apcu            php8.3-gearman         php8.3-mcrypt          php8.3-psr             php8.3-tidy
php8.3-ast             php8.3-gmagick         php8.3-memcache        php8.3-raphf           php8.3-uopz
php8.3-bcmath          php8.3-gmp             php8.3-memcached       php8.3-readline        php8.3-uploadprogress
php8.3-bz2             php8.3-gnupg           php8.3-mongodb         php8.3-redis           php8.3-uuid
php8.3-cgi             php8.3-http            php8.3-msgpack         php8.3-rrd             php8.3-xdebug
php8.3-cli             php8.3-igbinary        php8.3-mysql           php8.3-sass            php8.3-xml
php8.3-common          php8.3-imagick         php8.3-oauth           php8.3-smbclient       php8.3-xmlrpc
php8.3-curl            php8.3-imap            php8.3-odbc            php8.3-snmp            php8.3-xsl
php8.3-dba             php8.3-interbase       php8.3-opcache         php8.3-soap            php8.3-yac
php8.3-dev             php8.3-intl            php8.3-pcov            php8.3-solr            php8.3-yaml
php8.3-ds              php8.3-ldap            php8.3-pgsql           php8.3-sqlite3         php8.3-zip
php8.3-enchant         php8.3-libvirt-php     php8.3-phpdbg          php8.3-ssh2            php8.3-zmq
php8.3-facedetect      php8.3-mailparse       php8.3-pinba           php8.3-stomp
php8.3-fpm             php8.3-maxminddb       php8.3-ps              php8.3-sybase
```

Installer les extensions les plus couramment utilisés
```sh
sudo apt install php8.3-{apcu,common,bcmath,bz2,curl,gd,gmp,intl,mbstring,mysql,opcache,readline,xml,zip}
```

Débugage
```sh
sudo apt install php8.3-xdebug
```

Supprimer l'ancienne version de PHP et ses extensions
```sh
sudo apt purge '^php8.2.*'
```

Contrôler l'installation de PHP
```sh
php --version
```


### Installation 8.4

Ajouter le repository de `ondrej/php`
```sh
sudo apt update
sudo apt install -y software-properties-common
sudo LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php -y
sudo apt update
```

Installer PHP
```sh
sudo apt install -y php8.4
```


### Techniques

**Virtual Host**
- Dans `C:\Windows\System32\drivers\etc`
- Ajouter la ligne `127.0.0.1	mon-domaine-virtuel`
- Et la suivante `::1 mon-domaine-virtuel`
- Puis à partir du dossier du projet lancer 
    - `php -S mon-domaine-virtuel:8000` si le fichier de démarrage est à la racine de la commande
    - `php -S mon-domaine-virtuel:8000 -t mon-dossier` si le fichier de démarrage se trouve dans un dossier

Avec un `.htaccess` en racine et quelques lignes
```.htaccess
Options -Indexes

RewriteEngine on

RewriteCond %{REQUEST_URI} ^/(core|lib|constants\.php)
RewriteRule . /index.php [QSA,L]
```


---
---


## Annexes

### Glossaire

**SAPI**
_Server Application Programming Interface_
- CLI, _Command Line Interface_ : Interface de ligne de commande
- CGI, _Common Gateway Interface_ : Interface de passerelle commune, FastCGI est une amélioration de CGI et l'implémentation la plus courrante est PHP-FPM

**PHP-FPM**
_PHP FastCGI Process Manager_, une interface SAPI permettant la communication entre un serveur Web et PHP, basée sur le protocole FastCGI. PHP-FPM fut initialement écrit par Andrei Nigmatulin en 2004. Il constitue ainsi une alternative au serveur PHP avec des options pour les sites subissant de fortes charges.


### Pour en savoir plus

- [Directives du php.ini - PHP](https://www.php.net/manual/fr/ini.php)
- [Install PHP 8.4 on Ubuntu / Debian - PHP Watch](https://php.watch/articles/php-84-install-upgrade-guide-debian-ubuntu)
- [Code Covegare Tools - PHP Watch](https://php.watch/articles/php-code-coverage-comparison)
