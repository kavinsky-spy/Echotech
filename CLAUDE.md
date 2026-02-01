# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Drupal 11 project using DDEV for local development. Drupal is an open source content management platform.

**Important**: All commands should be run through `ddev` (e.g., `ddev drush`, `ddev composer`).

## Build/Lint/Test Commands

- **Build**: `ddev composer install`
- **Install**: `ddev drush site:install --existing-config`
- **Lint**:
  - If the project has a `/phpcs.xml` or `/phpcs.xml.dist`: `ddev exec phpcs`
  - Otherwise: `ddev exec phpcs --standard=Drupal path/to/test`
- **Static Analysis**:
  - If the project has a `/phpstan.neon` or `phpstan.neon.dist`: `ddev exec phpstan`
  - Otherwise: `ddev exec phpstan analyse --level 6 path/to/test`
- **Run Single Test**:
  - If the project has a `/phpunit.xml` or `/phpunit.xml.dist`: `ddev exec phpunit --filter Test path/to/test`
  - Otherwise: `ddev exec phpunit -c web/core/phpunit.xml.dist --filter Test path/to/test`

## Configuration Management

- **Export configuration**: `ddev drush config:export -y`
- **Import configuration**: `ddev drush config:import -y`
- **Import partial configuration**: `ddev drush config:import --partial --source=[path-to-module/config/install]`
- **Verify configuration**: `ddev drush config:export --diff`
- **View config details**: `ddev drush config:get [config.name]`
- **Change config value**: `ddev drush config:set [config.name] [key] [value]`
- **Install from config**: `ddev drush site:install --existing-config`
- **Get the config sync directory**: `ddev drush status --field=config-sync`

## Development Commands

- **List available modules**: `ddev drush pm:list [--filter=FILTER]`
- **List enabled modules**: `ddev drush pm:list --status=enabled [--filter=FILTER]`
- **Download a Drupal module**: `ddev composer require drupal/[module_name]`
- **Install a Drupal module**: `ddev drush en [module_name]`
- **Clear cache**: `ddev drush cache:rebuild`
- **Inspect logs**: `ddev drush watchdog:show --count=20`
- **Delete logs**: `ddev drush watchdog:delete all`
- **Run cron**: `ddev drush cron`
- **Show status**: `ddev drush status`

## Testing

- **Run all tests**: `ddev exec vendor/bin/phpunit` (from core/ directory)
- **Run specific test suite**: `ddev exec vendor/bin/phpunit --testsuite=unit`
  - Available suites: `unit`, `unit-component`, `kernel`, `functional`, `functional-javascript`, `build`
- **Run single test**: `ddev exec vendor/bin/phpunit path/to/TestFile.php`
- **Test configuration**: `core/phpunit.xml.dist`

## Code Quality

- **PHP CodeSniffer**: `ddev composer phpcs` (uses `core/phpcs.xml.dist`)
- **PHP Code Beautifier**: `ddev composer phpcbf`
- **PHPStan static analysis**: Available in `core/phpstan.neon.dist`

## Frontend Development (from core/ directory)

- **Build all assets**: `ddev exec yarn build`
- **Watch for changes**: `ddev exec yarn watch`
- **Build CSS**: `ddev exec yarn build:css` / `ddev exec yarn watch:css`
- **Build CKEditor5**: `ddev exec yarn build:ckeditor5` / `ddev exec yarn watch:ckeditor5`
- **Lint JavaScript**: `ddev exec yarn lint:core-js`
- **Lint CSS**: `ddev exec yarn lint:css`
- **Lint YAML**: `ddev exec yarn lint:yaml`
- **Spellcheck**: `ddev exec yarn spellcheck`

## Entity Management

- **View fields on entity**: `ddev drush field:info [entity_type] [bundle]`

## Architecture Overview

### Core Structure

- **`core/lib/Drupal.php`**: Static service container wrapper for legacy code
- **`core/lib/Drupal/Core/`**: Main core classes organized by subsystem
- **`core/lib/Drupal/Component/`**: Standalone components that can be used independently
- **`core/modules/`**: Core modules providing essential functionality
- **`core/themes/`**: Core themes
- **`core/profiles/`**: Installation profiles
- **`core/recipes/`**: Configuration recipes for common setups

### Key Subsystems (core/lib/Drupal/Core/)

- **Entity**: Content/configuration entity system (`Entity/`)
- **Database**: Database abstraction layer (`Database/`)
- **Cache**: Caching system with multiple backends (`Cache/`)
- **Config**: Configuration management system (`Config/`)
- **Form**: Form API (`Form/`)
- **Routing**: URL routing system (`Routing/`)
- **Access**: Access control system (`Access/`)
- **Field**: Field API for content types (`Field/`)
- **Plugin**: Plugin system for extensible functionality (`Plugin/`)
- **Asset**: CSS/JS asset management (`Asset/`)

### Dependency Injection

- Drupal uses Symfony's DependencyInjection component
- Services defined in `*.services.yml` files
- Use `\Drupal::service('service.name')` for legacy code or inject services in OOP code
- Container built by `DrupalKernel`

### Module Development

- **Contrib modules**: Place in `modules/contrib/` or `web/modules/contrib/`
- **Custom modules**: Place in `modules/custom/` or `web/modules/custom/`
- **Module structure**: `module_name/src/` for main classes, tests in `module_name/tests/`
- **Module definition**: `module_name.info.yml`
- **Hook implementations**: `module_name.module`

### Theme Development

- **Contrib themes**: Place in `themes/contrib/` or `web/themes/contrib/`
- **Custom themes**: Place in `themes/custom/` or `web/themes/custom/`
- **Base starter theme**: Use `core/themes/starterkit_theme/` as starting point
- **Twig templating**: Templates in `templates/` directory

### Testing Strategy

- **Unit tests**: Fast, isolated tests (`tests/src/Unit/`)
- **Kernel tests**: Tests with minimal Drupal bootstrap (`tests/src/Kernel/`)
- **Functional tests**: Full HTTP tests (`tests/src/Functional/`)
- **JavaScript tests**: Browser-based tests (`tests/src/FunctionalJavascript/`)
- **Build tests**: Composer/build process tests (`tests/src/Build/`)

## Environment Setup

### Requirements

- PHP 8.3+ (configured in composer.json platform)
- Database: MySQL 5.7.8+, MariaDB 10.6+, PostgreSQL 16+, or SQLite 3
- DDEV for local development

### DDEV Commands

- **Start environment**: `ddev start`
- **Stop environment**: `ddev stop`
- **SSH into container**: `ddev ssh`
- **Run composer**: `ddev composer [command]`
- **Run drush**: `ddev drush [command]`
- **View logs**: `ddev logs`
- **Database export**: `ddev export-db`
- **Database import**: `ddev import-db`

### Local Development

- Use `sites/default/settings.local.php` for local overrides
- Copy from `sites/example.settings.local.php`
- Enable development services with `sites/development.services.yml`
- DDEV handles database configuration automatically

## Best Practices

- If making configuration changes to a module's config/install, these should also be applied to active configuration
- Always export configuration after making changes
- Check configuration diffs before importing
- If a module provides install configuration, this should be done via `config/install` not `hook_install`
- Attempt to use contrib modules for functionality, rather than replicating in a custom module
- If phpcs/phpstan/phpunit are not available, they should be installed by `ddev composer require --dev drupal/core-dev`

## Code Style Guidelines

- **PHP Version**: 8.3+ compatibility required
- **Coding Standard**: Drupal coding standards
- **Indentation**: 2 spaces, no tabs
- **Line Length**: 120 characters maximum
- **Comment**: 80 characters maximum line length, always finishing with a full stop
- **Namespaces**: PSR-4 standard, `Drupal\{module_name}`
- **Types**: Strict typing with PHP 8 features, union types when needed
- **Documentation**: Required for classes and methods with PHPDoc
- **Class Structure**: Properties before methods, dependency injection via constructor
- **Naming**: CamelCase for classes/methods/properties, snake_case for variables, ALL_CAPS for constants
- **Error Handling**: Specific exception types with `@throws` annotations, meaningful messages
- **Plugins**: Follow Drupal plugin conventions with attributes for definition

## Key Files

- **`composer.json`**: Main project dependencies and scripts
- **`core/composer.json`**: Core-specific dependencies
- **`core/package.json`**: Frontend build dependencies and scripts
- **`autoload.php`**: Composer autoloader entry point
- **`index.php`**: Main web entry point
- **`core/install.php`**: Installation entry point
- **`update.php`**: Database update entry point

## Development Workflow

1. Start DDEV: `ddev start`
2. Install dependencies: `ddev composer install` and `ddev exec yarn install` (in core/)
3. Install Drupal via web or CLI
4. Enable development settings
5. Make changes and test with appropriate test suite
6. Run code quality checks before committing
7. Frontend changes require rebuilding assets with yarn commands

When working in this codebase, prioritize adherence to Drupal patterns and conventions.
