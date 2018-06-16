# Blazing-fast Drupal workflow

## Tools

You need to install:
* [Lando](https://docs.devwithlando.io/installation/installing.html)
* [Composer](https://getcomposer.org/doc/00-intro.md) (see [Quick way to get composer locally](#quick-install-composer))

* [NPM](https://www.npmjs.com/)


## Kickstart your project

**Note**:
We assume *project* is an empty folder.  

Get a Drupal 8 codebase:

```bash
composer create-project drupal-composer/drupal-project:8.x-dev project --stability dev --no-interaction
```

Init your local:

```bash
# Init a new lando env
cd project && lando init --recipe drupal8 --webroot web --name project
# OR use the minimal version
cp example.lando.min.yml project/.lando.yml
# OR better, use our enhanced version :)
cp example.lando.yml project/.lando.yml
```

Add required files:

```bash
cp example.settings.local.php project/web/sites/default/settings.local.php
cp example.settings.php project/web/sites/default/settings.php
cp project/.env.example project/.env
```

Edit `composer.json`:

```json
...
  "require:" {
    "oomphinc/composer-installers-extender": "^1.1",    
    "drupal/config_ignore": "^2.1",
    "drupal/config_split": "^1.3",
    "drupal/config_installer": "^1.8"
  }
...
  "config": {
    "bin-dir": "bin/",
    "sort-packages": false,
    "preferred-install": "dist",
    "discard-changes": true
  },
...
  "extra" : {
    "installer-paths": {
      "web/core": [
        "type:drupal-core"
      ],
      "web/libraries/{$name}": [
        "type:drupal-library"
      ],
      "web/modules/contrib/{$name}": [
        "type:drupal-module"
      ],
      "web/profiles/contrib/{$name}": [
        "type:drupal-profile"
      ],
      "web/themes/contrib/{$name}": [
        "type:drupal-theme"
      ],
      "web/themes/custom/{$name}": [
        "type:drupal-theme-custom"
      ],
      "drush/contrib/{$name}": [
        "type:drupal-drush"
      ]
    },
    "drupal-scaffold": {
      "source": "https://cgit.drupalcode.org/drupal/plain/{path}?h={version}",
      "excludes": [
        "sites/example.settings.local.php",
        "sites/example.settings.php"
      ],
      "initial": {
        "sites/default/example.services.development.yml": "sites/default/services.development.yml",
        "sites/default/example.settings.local.php": "sites/default/settings.local.php",
        "sites/default/example.settings.php": "sites/default/settings.php"
      }
    }
  }
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

<a name="quick-install-composer)">Quickly install Composer</a>
```bash
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
export PATH="$HOME/.composer/vendor/bin:$PATH"
source ~/.bash_profile
```

---

Interesting read:
* [*The WHY and the HOW `one file per component` in Drupal 8 much better than I do*](https://www.previousnext.com.au/blog/performance-improvements-drupal-8-libraries)
* [File organization in Drupal 8](https://www.drupal.org/docs/develop/standards/css/css-file-organization-for-drupal-8#aggregate)
* [WHY we must render the `content` variable in Twig and try to avoid using fields...](https://www.previousnext.com.au/blog/ensuring-drupal-8-block-cache-tags-bubble-up-page)
