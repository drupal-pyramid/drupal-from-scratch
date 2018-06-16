# Demo 

Get a Drupal 8 codebase

```bash
composer create-project drupal-composer/drupal-project:8.x-dev . --stability dev --no-interaction
```

Init your local

```bash
lando init --recipe drupal8 --webroot web --name demo
```

Install Drupal

```bash
lando drush si standard -y
```

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
