# Blazing-fast Drupal workflow

## Tools

You need to install:
* Composer
* Lando
* NPM

## Kickstart your project

Get a Drupal 8 codebase:

```bash
composer create-project drupal-composer/drupal-project:8.x-dev project --stability dev --no-interaction
```

Init your local:

```bash
lando init --recipe drupal8 --webroot web --name demo
```

Add required files:

```bash
cp example.settings.local.php project/web/sites/default/settings.local.php
cp example.settings.php project/web/sites/default/settings.php
cp project/.env.example project/.env
```

Edit `.env` file with Lando database credentials:

```bash
MYSQL_DATABASE=drupal8
MYSQL_PASSWORD=drupal8
MYSQL_HOSTNAME=database
MYSQL_PORT=3306
MYSQL_USER=drupal8
```

Edit `composer.json`:

```json
...
  "config": {
    "bin-dir": "bin/",
    "sort-packages": false,
    "preferred-install": "dist",
    "discard-changes": true
  },
...
```


Add configuration folders

```bash
cp example.settings.local.php project/web/sites/default/settings.local.php
cp example.settings.php project/web/sites/default/settings.php
```

Install Drupal

```bash
lando drush si standard -y
```

--- 

## Create a new theme

Create a custom theme:

```bash
./bin/drupal generate:theme  \
--theme="Custom Theme"  \
--machine-name="custom_theme"  \
--theme-path="themes/custom"  \
--description="My Awesome theme"  \
--core="8.x"  \
--package="Custom"  \
--global-library="global-styling"  \
--base-theme="false"
```

---

## Create a new module

Create a custom module:

```bash
drupal generate:module  \
  --module="custom_module"  \
  --machine-name="custom_module"  \
  --module-path="/modules/custom"  \
  --description="My Awesome Module"  \
  --core="8.x"  \
  --package="Custom"  \
  --module-file  \
  --composer  \
  --test  \
  --twigtemplate
```


## Daily workflow
