
# Guide : Installer Composer et Symfony CLI


## WSL / Ubuntu 24.04

### Composer

Télécharger l'installateur de Composer
```sh
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
```

Vérifier le SHA-384 de l'installateur
```sh
php -r "if (hash_file('sha384', 'composer-setup.php') === 'ed0feb545ba87161262f2d45a633e34f591ebb3381f2e0063c345ebea4d228dd0043083717770234ec00c5a9f9593792') { echo 'Installer verified'.PHP_EOL; } else { echo 'Installer corrupt'.PHP_EOL; unlink('composer-setup.php'); exit(1); }"
```

Exécuter l'installateur
```sh
php composer-setup.php
```

Supprimer l'installateur
```sh
php -r "unlink('composer-setup.php');"
```

Déplacer Composer dans le répertoire global de binaires – peremettra d'exécuter `composer` à partir de la console
```sh
sudo mv composer.phar /usr/local/bin/composer
```

Coontrôler installation de Composer
```sh
composer --version
```


### Symfony CLI

Installer Symfony
```sh
wget https://get.symfony.com/cli/installer -O - | bash
```

Déplacer Symfony dans le répertoire global de binaires – peremettra d'exécuter `symfony` à partir de la console
```sh
sudo mv /home/elo/.symfony5/bin/symfony /usr/local/bin/symfony
```

Contrôler l'installation de Symfony
```sh
symfony --version
```

Contrôler la configuration locale requise pour Symfony
```sh
symfony check:requirements
```


---
---

## Annexes

### Glossaire


### Pour en savoir plus

- [Install Composer - GetComposer](https://getcomposer.org/download/)
- [Set Composer Globally - GetComposer](https://getcomposer.org/doc/00-intro.md#globally)
- [Download - Symfony](https://symfony.com/download)
- [Documentation : Setting up - Symfony](https://symfony.com/doc/current/setup.html)