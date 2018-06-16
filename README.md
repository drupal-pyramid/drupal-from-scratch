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
cp -R config/ project/
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


**Checklist (the order is important)**

1. [] Have you exported default content?       →  (work in progress)
1. [] Have you exported translations?          →  `/admin/config/regional/translate`
1. [] Have you merged dev recently (today)?    →  `git checkout develop && git checkout <branch> && git merge --ff develop`
1. [] Have you exported your configurations?   →  `drush cex`
1. [] Have you imported latest changes?        →  `drush cim`
1. [] Have you run the Linters?                →  `npm run lint` → Fix all errors (see logs/ files)
1. [] Have you run a Code Review?              →  `composer code-review` → Fix all errors (see logs/ files)


**Commands (the order is important)**

```bash
# Backup your database
lando db-export

# Export your changes
lando drush cex -r docroot -y

# Pull new changes
git fetch
git checkout develop
git pull —rebase
git checkout <feature/XXX-your-feature>
git merge --fast-forward develop <feature/XXX-your-feature>

# Update your local files
lando composer install

# Option 1) UPDATE YOUR LOCAL DATABASE
# This way, you keep your existing content.
lando drush cim y
lando drush entup y
lando drush updb -y
lando drush locale-update -y

# Option 2) REINSTALL THE SITE
# Export your default content in custom module first.
lando drush si config_installer -y

# Generate assets
lando npm install
lando npm run reset

# Run code review
lando npm run lint # -> # Fix coding standards issues and commit your fixes.
lando composer code-review # -> Fix coding standards issues and commit your fixes.

# You're all clean now!
```

---

Interesting read:
* [*The WHY and the HOW `one file per component` in Drupal 8 much better than I do*](https://www.previousnext.com.au/blog/performance-improvements-drupal-8-libraries)

* [File organization in Drupal 8](https://www.drupal.org/docs/develop/standards/css/css-file-organization-for-drupal-8#aggregate)

* [WHY we must render the `content` variable in Twig and try to avoid using fields...](https://www.previousnext.com.au/blog/ensuring-drupal-8-block-cache-tags-bubble-up-page)
