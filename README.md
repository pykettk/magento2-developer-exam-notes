![Magento 2.3](https://img.shields.io/badge/Magento-2.3-ec6611.svg)
![Study Guide](https://img.shields.io/badge/documentation-study_guide-blue.svg)
![GitHub last commit (master)](https://img.shields.io/github/last-commit/pykettk/magento2-associate-developer-certification/master.svg)
![License](https://img.shields.io/github/license/pykettk/magento2-associate-developer-certification.svg)

# Magento 2 Associate Developer Certification Study Guide
## Tips & Useful Information
The exam includes questions relating to Magento Admin - more than you would expect. It is worth knowing some of the more common features and areas:
- Marketing & SEO
- Category management
- Scopes (Global, Website, Store, and Store View)

The questions are typically structured in two parts. They first gives a bit of background information, usually in the form of "you have been asked to do...", then ask you the question they want answering. Make sure to read the questions **carefully** as the background information often includes key words that might trip you up and trick you into answering the wrong question.

Quite a few answers are very similar, make sure you are confident in the exact file paths of files.

It may be beneficial to set up a throwaway Magento environment to test some of the things that you revise - such as managing the database using the Declarative Schema; something introduced within Magento 2.3. There were quite a few questions which involved knowledge regarding Data and Schema patches and being aware when to use them.

To define what UI components are used for certain admin options, these are **SOURCE MODELS**, not Backend or Config models.

## Exam Structure
The exam is based on **Magento 2.3.\*** using the **Magento Luma Theme**.

The exam covers both **Open Source & Commerce** versions of Magento.

There are **60 multiple choice questions**.

There is a **90 minute time limit**.

The pass grade is **68%**.

## Exam Content
### 1.0. Magento Architecture & Customisation Techniques
This section covers **33%** of the exam.

#### 1.1. Describe the Magento Module-Based Architecture
> What are the significant steps to add a new module?

You need to create the following files:
- `registration.php`
- `module.xml`

##### `registration.php`
This file is included by the Composer autoloader and is added to the static list of components in `Magento\Framework\Component\ComponentRegistrar`. Example:

```PHP
<?php
use \Magento\Framework\Component\ComponentRegistrar;

ComponentRegistrar::register(
    ComponentRegistrar::MODULE,        // type
    'YourCompany_YourModule',          // componentName
    __DIR__                            // path
);
```

Once Magento has found this file, it will then look to find `etc/module.xml`.

##### `module.xml`
A component declares itself (that is, defines its name and existence) in the `module.xml` file located in the module's `etc/` directory. This file specifies the loading sequence for the module and it's setup version (if not using Declarative Schema).

The sequence tells Magento which order to load modules in. This is done in `Magento\Framework\Module\ModuleList\Loader.php` and only occurs when the module is installed. This sequence is useful for events, plugins, preferences, and layouts.
- **NB:** If your module modifies the layout of another and your module is loaded first, the other module will overwrite your modifications.

Example:
```XML
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
    <module name="YourCompany_YourModule" setup_version="1.0.0">
        <sequence>
            <module name="TheirCompany_TheirModule" />
            <module name="AnotherCompany_AnotherModule" />
        </sequence>
    </module>
</config>
```

---
> What are the different Composer package types?

| Name | Package Type | Description |
| :--: | :----------: | :---------- |
| Metapackage | metapackage | Technically, a Composer package type, not a Magento component type. A metapackage consists of only a `composer.json` file that specifies a list of components and their dependencies. For example, both Magento Open Source and Magento Commerce are metapackages. |
| Module | magento2-module | Code that modifies Magento application behavior. You can upload a single module to the Magento Marketplace or your module can be dependent on some parent package. |
| Theme | magento2-theme | Code that modifies the look and feel of the storefront or Magento Admin. |
| Language Package | magento2-language | Translations for the storefront or Admin. |

---
> When would you place a module in the app/code folder versus another location?

---
#### 1.2. Describe the Magento Directory Structure
> How do you locate different files in Magento?

All of the core Magento files are located in the `vendor/magento/` directory with some supporting JavaScript and CSS files being stored in `lib/`.

Third-party files can be found in their respective `vendor/vendor-name/module-name/` directories.

For your customisations:
- For modules, use `app/code`.
- For storefront themes, use `app/design/frontend`.
- For Admin themes, use `app/design/adminhtml`.
- For language packages, `use app/i18n`.

##### `/Api`: Service Contracts
The `/Api` directory stores the contracts or interfaces that are exposed to the API. An example of this would be `Magento\Catalog\Api\CategoryListInterface`.

##### `/Api/Data`: Data Service Contracts
This folder contains interfaces that represent data. Examples of this would be a
product interface, a category interface, or a customer interface. The concrete
implementations of these interfaces usually do little more than provide getters and
setters for data.

See `Magento\Catalog\Api\Data\ProductInterface` for an example.

##### `/Block`: View Models
Blocks can be considered as template assistants. Very little functionality or business logic should be done in templates - that is the responsibility of the Block.

##### `/Console`: Console Commands
When running `bin/magento` on the command line, a list of available commands
to run is output. The code for commands should be placed inside the `/Console` directory.

##### `/Controller`: Web Request Handlers
This directory stores controller classes for web requests.

##### `/Cron`: Cron Job Classes
Definitions for cron jobs are stored here.

##### `/etc`: Configuration Files
Any files directly within this directory are applied *globally*. You can restrict the *area* these files belong to by placing it in the relevant sub-directory - `etc/frontend/`, `etc/adminhtml/` etc.
- **NB:** Some files **MUST** be placed within a particular area whilst others **MUST** be global.

##### `/Helper`: Aggregated Functionality
Small, reusable snippets of code should be stored in a helper class.

##### `/i18n`: Localisation Files
This directory contains the translation CSV files for the module. These CSV files contain two columns, `from` and `to`.

##### `/Model`: Data Handling & Data Structures
A self-explanatory directory.

##### `/Model/ResourceModel`: Database Interactions
The classes stored in this directory dictate how data is stored and retrieved from the database. Any *direct* database interaction should be done within these files.

See `vendor/magento/module-catalog/Model/ResourceModel/Product.php` for an example.

##### `/Observer`: Event Listeners
When Magento fires an event, certain observers are called - decoupling the system. Magento Commerce integrates with RabbitMQ which allows even more control and reliability to this process. Event data should be able to be stored and then run in a queue at a later time.

Observers **MUST** implement the `Magento\Framework\Event\ObserverInterface`. The PHP class should follow the stand of using TitleCase while the event name should be snake_case.

Observers should not contain any business logic. This logic should instead be placed in another class and injected into your observer.

##### `/Plugin`
This directory stores your module's plugins. These are covered in more detail in a later section.

##### `/Setup`: Database Modification
Stores the following files:
- `InstallSchema.php`
    - Sets up table and column schema when the module is installed.
- `UpgradeSchema.php`
    - Modifies table and column schema when the module version is upgraded.
- `Recurring.php`
    - Runs after every install or upgrade.
- `InstallData.php`
    - Sets up data when the module is installed. An example would be adding a custom CMS block.
- `UpgradeData.php`
    - Modifies data after the module is installed and when the module version is upgraded.
- `RecurringData.php`
    - Applies to data after every install or upgrade.

##### `/Test`: Module Tests
This directory stores your module's tests. Tests can be run via the command line using `bin/magento dev:tests:run`.

##### `/Ui`: Data Generation Files

##### `/view/[area]/templates`: Block Templates
The counterpart to a Block is a template to render the HTML for the block. Whilst the block (in `/Block`) represents the business logic, the template represents how the results of the business logic are shown to the user.

##### `/view/[area]/web`: Web Assets
Web assets such as images, CSS, JavaScript, LESS, and SCSS are stored in this directory.

##### `view/[area]/web/template`: JavaScript Templates
HTML templates that can be requested asynchronously via JavaScript are stored in this directory. These files often contain KnockoutJS declarative bindings.

##### `/view/adminhtml/ui_component`: Adminhtml UI Components
This directory contains the XML configuration for UI components. They are used to represent distinct UI elements, such as grids and forms, and are designed to provide flexible user interface rendering. Most Magento Admin grids (such as the Catalog Product grid and the Customer grid) are composed with UI components. The
checkout on the frontend is also a UI component.

---
> What are the naming conventions, and how are namespaces established?

---
> How can you identify the files responsible for some functionality?

---
#### 1.3. Utilise Configuration and Configuration Variables Scope
> Which configuration files are important in the development cycle?

##### `acl.xml`
Defines permissions for accessing protected resources.

##### `config.xml`
Loads configuration values into `Stores > Configuration` in the Magento Admin. This file can also encrypt configuration entries.

##### `crontab.xml`
Defines cron job scheduling.

##### `di.xml`
This file configures dependency injection for your module. It defines plugins, preferences, concrete classes for interfaces, virtual types, and constructor argument modifications.

##### `email_templates.xml`

##### `events.xml`
This file registers observers.

##### `indexer.xml`
This file configures Magento indexers.

##### `module.xml`
This file is **required** by Magento.

##### `mview.xml`
Triggers a type of event when data is modified in a database column - most often used for indexing.

##### `view.xml`
This file is similar to `config.xml` but is used to specify the default values for design configuration.

##### `webapi.xml`
Configures API access and routes.

##### `widget.xml`
Configures widgets to be used in products, CMS blocks, and CMS pages.

##### `[area]/routes.xml`
This file tells Magento that this area accepts web requests. The route node configures the first part of the layout handle (route ID) and the front name (first segment in the URL after the domain name).

##### `adminhtml/menu.xml`
Configuration for the menu in Magento Admin.

##### `adminhtml/system.xml`
Configures tabs, sections, groups, and fields found in `Store > Configuration` in Magento Admin.

---
> How do you identify the configuration scope for a given variable?

---
> How do native Magento scopes (for example, price or inventory) affect development and decision-making processes?

---
> How can you fetch a system configuration value programmatically?

---
> How can you override system configuration values for a given store using XML configuration?

---
#### 1.4. Demonstrate How To Use Dependency Injection
> How are objects realized in code?
 
Since dependency injection happens automatically through the constructor, Magento
must handle class creation - either at the time of injection or via a factory.

##### Class Creation During Injection
First the object manager locates the proper class type. If an interface is requested, hopefully an entry in `di.xml` will provide a concrete class for the interface (if not, an exception will be thrown).

Then the parameters for the constructor are loaded and recursively parsed meaning that the dependencies for the initially requested class are loaded as well as the dependencies of those dependencies as well.

The deploy mode (`bin/magento deploy:mode:show)` determines which class loader is used:
- `vendor/magento/framework/ObjectManager/Factory/Dynamic/Developer.php`
- `vendor/magento/framework/ObjectManager/Factory/Dynamic/Production.php`

##### Class Creation Via Factories

---
> Why is it important to have a centralized object creation process?

Having a centralised process to create objects makes testing much easier. It also
provides a simple interface to substitute objects as well as modify existing ones.

---
> How can you override a native class, inject your class into another object, and use other techniques available in `di.xml` (for example, `virtualTypes`)?

##### Overriding Native Classes
Preferences are used to substitute entire classes. They can also be used to specify concrete classes for interfaces:
```XML
<preference for="Magento\GoogleTagManager\Block\ListJson"
            type="YourCompany\YourModule\Path\To\Your\Class"
/>
```

##### Injecting Your Class into Other Objects
Specify a `<type>` entry with your class as an `<argument>`:
```XML
<type name="Path\To\Your\Class\To\Inject\Into">
    <arguments>
        <argument xsi:type="object">
            Path\To\Your\Injected\Class
        </argument>
    </arguments>
</type>
```

##### Virtual Types
A virtual type allows you to create an instance of an existing class that has custom constructor arguments. This is useful in cases where you need a “new” class only because the constructor arguments need to be changed. This is used frequently in Magento to reduce redundant PHP classes.

---
> How would you obtain a class instance from different places in the code?

---
#### 1.5. Demonstrate Ability To Use Plugins
> How are plugins used in core code? How can they be used for customizations?

A plugin is a class that modifies the behavior of public class functions by intercepting a function call and running code before, after, or around that call. This allows you to customise or extend the behavior of original, public methods for any class or interface.

##### Before Plugin
Before plugins are used when you want to modify the function input. To modify the input of a function:
```PHP
public function beforeFunctionName(
    Class\Containing\The\Function $subject,
    $normalFunctionInput1,
    $normalFunctionInput2,
) {
    // ...
    
    return [$normalFunctionInput1, $normalFunctionInput2];
}
```

The `return` value determines the array of arguments being passed into the next plugin or targeted function.

##### After Plugin
After plugins are used when you want to modify the function output. To modify the output of a function:
```PHP
public function afterFunctionName(
    Class\Containing\The\Function $subject,
    $result
) {
    // ...
    
    return $result;
}
```

The `return` value determines the output of the function being passed into the next plugin or targeted function.

##### Around Plugin
Around plugins give you full control over the function, it's inputs, and it's output. To use an around plugin to replace a function:
```PHP
public function aroundFunctionName(
    Class\Containing\The\Function $subject,
    callable $proceed
) {
    // ...
    
    $result = $proceed();
    
    // ...
    
    return $result;
    
}
```

##### Declaring Plugins
To declare a plugin, add a `<type/>` to your module's `di.xml`:
```XML
<type name="Class\Containing\The\Function\To\Plug\Into">
    <plugin name="PluginName"
            type="Class\Containing\Your\Plugin"
            disabled="false"
            sortOrder="10"
    />
</type>
```

##### Plugin Limitations
Plugins have the following limitations:
- Only work with **public** functions
- **Do not** work with `final` classes or functions
- **Do not** work with `static` functions

---
#### 1.6. Configure Event Observers & Scheduled Jobs
Observers listen for events that are triggered in Magento. Scheduled jobs perform an action at a specified interval.

Observers **MUST** implement the `Magento\Framework\Event\ObserverInterface`.

---
> How are observers registered?

Create an `<event/>` node in your `etc/[area]/events.xml` file:
```XML
<event name="event_for_your_observer_to_listen_for">
    <observer name="observerName"
              instance="Your\Observer\Class"
    />
</event>
```

---
> How are they scoped for frontend or backend?

Place the `events.xml` file in the `etc/frontend/` and `etc/adminhtml/` directories respectively.

---
> How are automatic events created, and how should they be used?

Events should be used when you do not want to change the data. They can be triggered by injecting an instance of `Magento\Framework\Event\ManagerInterface` into the constructor and calling: `$this->eventManager->dispatch('event_name', [params]);`.

---
> How are scheduled jobs configured?

To configure a scheduled job you need to assign it a name, specify the function it should execute, the class that function belongs to, and set the schedule using the regular cron schedule notatation. Scheduled jobs are specified in the `etc/crontab.xml` file:
```XML
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"    
        xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Cron:etc/crontab.xsd">
    <group id="default">
        <job name="cron_job_name"
             instance="Path\To\Your\Class"
             method="execute">
            <schedule>*/5 * * * *</schedule>
        </job>
    </group>
</config>
```

---
#### 1.7. Utilise the CLI
> Which commands are available?

A full list of commands can be found by running `bin/magento`. All of the commands listed below can be run from within the `magento/` directory via the command line, prefixed with `bin/magento`.
```
Usage:
  command [options] [arguments]

Options:
  -h, --help            Display this help message
  -q, --quiet           Do not output any message
  -V, --version         Display this application version
      --ansi            Force ANSI output
      --no-ansi         Disable ANSI output
  -n, --no-interaction  Do not ask any interactive question
  -v|vv|vvv, --verbose  Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

Available commands:
  help                                     Displays help for a command
  list                                     Lists commands
 admin
  admin:user:create                        Creates an administrator
  admin:user:unlock                        Unlock Admin Account
 app
  app:config:dump                          Create dump of application
  app:config:import                        Import data from shared configuration files to appropriate data storage
  app:config:status                        Checks if config propagation requires update
 cache
  cache:clean                              Cleans cache type(s)
  cache:disable                            Disables cache type(s)
  cache:enable                             Enables cache type(s)
  cache:flush                              Flushes cache storage used by cache type(s)
  cache:status                             Checks cache status
 catalog
  catalog:images:resize                    Creates resized product images
  catalog:product:attributes:cleanup       Removes unused product attributes.
 config
  config:sensitive:set                     Set sensitive configuration values
  config:set                               Change system configuration
  config:show                              Shows configuration value for given path. If path is not specified, all saved values will be shown
 cron
  cron:install                             Generates and installs crontab for current user
  cron:remove                              Removes tasks from crontab
  cron:run                                 Runs jobs by schedule
 customer
  customer:hash:upgrade                    Upgrade customer's hash according to the latest algorithm
 deploy
  deploy:mode:set                          Set application mode.
  deploy:mode:show                         Displays current application mode.
 dev
  dev:di:info                              Provides information on Dependency Injection configuration for the Command.
  dev:profiler:disable                     Disable the profiler.
  dev:profiler:enable                      Enable the profiler.
  dev:query-log:disable                    Disable DB query logging
  dev:query-log:enable                     Enable DB query logging
  dev:source-theme:deploy                  Collects and publishes source files for theme.
  dev:template-hints:disable               Disable frontend template hints. A cache flush might be required.
  dev:template-hints:enable                Enable frontend template hints. A cache flush might be required.
  dev:tests:run                            Runs tests
  dev:urn-catalog:generate                 Generates the catalog of URNs to *.xsd mappings for the IDE to highlight xml.
  dev:xml:convert                          Converts XML file using XSL style sheets
 encryption
  encryption:payment-data:update           Re-encrypts encrypted credit card data with latest encryption cipher.
 i18n
  i18n:collect-phrases                     Discovers phrases in the codebase
  i18n:pack                                Saves language package
  i18n:uninstall                           Uninstalls language packages
 indexer
  indexer:info                             Shows allowed Indexers
  indexer:reindex                          Reindexes Data
  indexer:reset                            Resets indexer status to invalid
  indexer:set-dimensions-mode              Set Indexer Dimensions Mode
  indexer:set-mode                         Sets index mode type
  indexer:show-dimensions-mode             Shows Indexer Dimension Mode
  indexer:show-mode                        Shows Index Mode
  indexer:status                           Shows status of Indexer
 info
  info:adminuri                            Displays the Magento Admin URI
  info:backups:list                        Prints list of available backup files
  info:currency:list                       Displays the list of available currencies
  info:dependencies:show-framework         Shows number of dependencies on Magento framework
  info:dependencies:show-modules           Shows number of dependencies between modules
  info:dependencies:show-modules-circular  Shows number of circular dependencies between modules
  info:language:list                       Displays the list of available language locales
  info:timezone:list                       Displays the list of available timezones
 maintenance
  maintenance:allow-ips                    Sets maintenance mode exempt IPs
  maintenance:disable                      Disables maintenance mode
  maintenance:enable                       Enables maintenance mode
  maintenance:status                       Displays maintenance mode status
 module
  module:disable                           Disables specified modules
  module:enable                            Enables specified modules
  module:status                            Displays status of modules
  module:uninstall                         Uninstalls modules installed by composer
 queue
  queue:consumers:list                     List of MessageQueue consumers
  queue:consumers:start                    Start MessageQueue consumer
 sampledata
  sampledata:deploy                        Deploy sample data modules for composer-based Magento installations
  sampledata:remove                        Remove all sample data packages from composer.json
  sampledata:reset                         Reset all sample data modules for re-installation
 setup
  setup:backup                             Takes backup of Magento Application code base, media and database
  setup:config:set                         Creates or modifies the deployment configuration
  setup:cron:run                           Runs cron job scheduled for setup application
  setup:db-data:upgrade                    Installs and upgrades data in the DB
  setup:db-declaration:generate-patch      Generate patch and put it in specific folder.
  setup:db-declaration:generate-whitelist  Generate whitelist of tables and columns that are allowed to be edited by declaration installer
  setup:db-schema:upgrade                  Installs and upgrades the DB schema
  setup:db:status                          Checks if DB schema or data requires upgrade
  setup:di:compile                         Generates DI configuration and all missing classes that can be auto-generated
  setup:install                            Installs the Magento application
  setup:performance:generate-fixtures      Generates fixtures
  setup:rollback                           Rolls back Magento Application codebase, media and database
  setup:static-content:deploy              Deploys static view files
  setup:store-config:set                   Installs the store configuration. Deprecated since 2.2.0. Use config:set instead
  setup:uninstall                          Uninstalls the Magento application
  setup:upgrade                            Upgrades the Magento application, DB data, and schema
 store
  store:list                               Displays the list of stores
  store:website:list                       Displays the list of websites
 theme
  theme:uninstall                          Uninstalls theme
```

---
> How are commands used in the development cycle?

Commands provide a secure method of perfoming tasks that may otherwise be insecure or more time consuming to perform through the Magento Admin. Some of the more commonly used commands during development are listd below:

##### `cache:disable`
Disables specific caches. You might use this to disable the `layout`, `block_html`, and `full_page` caches during the development of some frontend templates so Magento always generates the pages you are working on rather than older versions that it has cached. Example:
```
bin/magento cache:disable layout block_html full_page
```

##### `cache:flush`
Destroys the Magento caches, deleting the cache keys. This can be used to destroy any of the Magento caches in combination. Example:
```
# destroy all caches
bin/magento cache:flush

# destroy configuration cache
bin/magento cache:flush config
```

##### `cache:status`
Lists the caches and whether they are enabled or disabled. You might use this prior to using the `cache:disable` or `cache:enable` commands.

##### `deploy:mode:set`
Sets your deploy mode. During development you will likely want to set this to `developer` whilst on your live server you will want to set this to `production`. Example:
```
# developer mode
bin/magento deploy:mode:set developer

# production mode
bin/magento deploy:mode:set production

# default mode
bin/magento deploy:mode:set default
```

##### `dev:query-log:enable`
Enables database query logging.

##### `indexer:reindex`
Runs the Magento indexers.

##### `module:disable`
Disables a specified module. Example:
```
bin/magento module:disable Module_Name
```

##### `module:enable`
Enables a specified module. Example:
```
bin/magento module:enable Module_Name
```

##### `module:status`
List all modules and whether they are enabled or disabled.

##### `setup:db:status`
Checks whether the Magento database needs to be upgraded.

##### `setup:upgrade`
Synchronises module versions in the database with those defined in the codebase.

---
### 2.0 Request Flow Processing
This section covers **7%** of the exam.

---
#### 2.1. Describe Magento 2 Modes
> Understand the pros and cons of using developer mode or production mode.

##### Production
This mode should be used on your live production server. Production mode leads to an increase in performance by providing all necessary static files at the time of deployment rather than requiring Magento to dynamically locate and create static files during run time.

In this mode Magento:
- Logs exceptions and **does not** show exceptions on the frontend
- Serves static view files from the cache **only**
- Prevents automatic code compilation meaning that new or updated files are **not** written to the file system
- Prevents you from enabling or disabled cache types via Magento Admin

##### Developer
As it's name suggests, this mode is intended to be used during development only. When in this mode, Magento:
- Disables static view file caching instead writing them to `pub/static` every time they are called
- Provides verbose logging in `var/report`
- Enables automatic code compilation
- Displays uncaught exceptions to the frontend
- Enables enhanced debugging
- Shows custom `x-magento-*` HTTP request and response headers
- Performs slower

---
> How do you enable/disable maintenance mode?

Maintenance mode can be enabled and disabled via the command line:
```
# enable maintenance mode
bin/magento maintenance:enable

# disable maintenance mode
bin/magento maintenance:disable
```

---
#### 2.2. Demonstrate Abilty To Use Frontend Controllers
> How do you identify which module/controller corresponds to a given URL?

`https://your-store.com/catalog/product/view/id/42`



---
> What would you do to create a given URL?
Set the Category and Product `url_key` attributes. You can also create URL rewrites through the Magento Admin path `Marketing > SEO & Search > URL Rewrites`

---
#### 2.3. URL Rewrites
URL rewrites provide a user-friendly URL to the customer in place of a cumbersome Magento URL. These values are stored in the `url_rewrite` table.

---
> How is the user-friendly URL of a product or category defined?

The product/category `url_key` attribute defines their URL. For each category a product belongs to, Magento will generate a URL based on the category tree before appending the product's `url_key`.

---
> How can you change a URL?

You can change a URL by creating a URL rewrite. The following actions also cause a URL to change:
- Updating a category's `url_key` attribute
- Updating a product's `url_key` attribute
- Modifying the categories a product belongs to

---
> How do you determine which page corresponds to a given user-friendly URL?

In the `url_rewrite` table you will find a row where the `request_path` value is the user-friendly URL. The corresponding `target_path` value is the internal Magento page. The `Magento_UrlRewrite` module contains a router that checks to see whether the given URL can be matched to a `request_path` in the `url_rewrite` table, redirecting to the `target_path` if a match is found.

---
### 3.0. Customising The Magento UI
This section covers **15%** of the exam.

#### 3.1. Customise The Magento UI Using Themes
> When would you create a new theme?

---
> How do you define theme hierarchy for a project?

---
#### 3.2. Blocks & Templates
> How do you assign a template to a block?

To assign a template to a block:
```XML
<block class="Namespace\Of\Your\Class"
       name="blockName"
       template="Module_Name::path/to/your/template.phtml"
/>
```

**NB:** The `path/to/your/template.phtml` starts from the `templates/` directory.

---
> How do you assign a different template to a native block?

To assign a template to an existing block, pass an argument to the block:
```XML
<referenceBlock name="blockName">
    <action method="setTemplate">
        <argument name="template" xsi:type="string">
            Module_Name::path/to/your/template.phtml
        <argument>
    </action>
</referenceBlock>
```

---
#### 3.3. Identify Different Types Of Blocks
> When would you use non-template block types?

| Block Type | Path | Use |
| :--------: | :--: | :-: |
| Text | `vendor/magento/framework/View/Element/Text.php` | Printing a string |
| ListText | `vendor/magento/framework/View/Element/Text/ListTest.php` | Output each of the child blocks |

---
#### 3.4. Magento Layout XML
> How do you use layout XML directives in your customizations?

---
> How do you register a new layout file?

---
#### 3.5. Create & Add Code & Markup To A Page
> How do you add new content to existing pages using layout XML?

---
### 4.0. Working With Databases In Magento
This section covers **18%** of the exam.

#### 4.1. Models, Resource Models, And Collections
> What are the responsibilities of each of the ORM object types?

---
> How do they relate to one another?

---
#### 4.2. Entity Loading And Saving
> How do you use the native Magento save/load process in the development process?

---
#### 4.3. Filter, Sort, And Select From Collections & Repositories
> How do you select a subset of records from the database?

---
#### 4.4. Declarative Schema
> How do you add a column using declarative schema?

---
> How do you modify a table added by another module?

---
> How do you delete a column?

---
> How do you add an index or foreign key using declarative schema?

---
> How do you manipulate data using data patches?

---
> What is the purpose of schema patches?

---
### 5.0. Developing With Adminhtml
This section covers **11%** of the exam.

#### 5.1. Admin Routes
> How would you create an admin controller?

---
> How do you ensure the right level of security for a new controller?

---
#### 5.2. System Configuration & Scopes
> How would you add a new system configuration option?

---
> What is the difference in this process for different option types (secret, file)?

---
#### 5.3. ACL
> How would you add a new ACL resource to a new entity?

---
> How do you manage the existing ACL hierarchy?

---
#### 5.4. Setup A Menu Item
> How do you add a new menu item to a given tab?

Adminhtml menu items are configured in `etc/adminhtml/menu.xml`. To add a new menu item, edit this file:
```XML
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Backend:etc/menu.xsd">
   <menu>
       <add id="Your_Module::brief_module_description"
           title="Your Menu Tab Title"
           translate="title"
           module="Your_Module"
           sortOrder="10"
           
           <!-- No parent means the tab appears in the side bar -->
           parent="Magento_Catalog::inventory"
           
           <!-- No action means the item acts as a header -->
           action="path/to/your/controller"
           
           <!-- ID of the ACL entry that validates permissions -->
           resource="ACL_Entry_ID"
           />
   </menu>
</config>
```

---
> How do you add a new tab to the Admin menu?

Do not specify a `parent` attribute in the `<add />` node.

---
#### 5.5. Create User Permissions
> How are menu items related to ACL permissions?

Menu items are not shown to users with insufficient permissions to access them.

---
> How do you add a new user with given set of permissions?

Navigate to `System > Permissions > All Users` in Magento admin, add a new user, and set their role under `User Information > User Role`.

This can also be done programmatically:
```PHP
use Magento\User\Model\UserFactory;

// ...

protected $userFactory;

public function __construct(UserFactory $userFactory)
{
    $this->_userFactory = $userFactory;
}

public function execute()
{
    $user = $this->userFactory->create();
    $user->setData(
        [
            'username'          => 'user.name',
            'firstname'         => 'Forename',
            'lastname'          => 'Surname',
            'email'             => 'admin@test.com',
            'password'          => 'badpassword123',       
            'interface_locale'  => 'en_US',
            'is_active'         => 1
        ]
    );
    
    $user->setRoleId(1);
    $user->save(); 
}
```

---
### 6.0. Customising Magento Business Logic
This section covers **16%** of the exam.

#### 6.1. Product Types
> How would you obtain a product of a specific type, and what tools (in general) does a product type model provide? 

---
#### 6.2. Category Properties
> How do you create and manage categories?

---
#### 6.3. Product/Category Relations
> How do you assign and unassign products to categories?

---
#### 6.4. Product Behaviour In The Cart
> How are configurable and bundle products rendered?

---
> How can you create a custom shopping cart renderer?

---
#### 6.5. Native Shipping Functionality
> How do you customize the shipment step of order management?

---
#### 6.6. Customer Account Area Customisation
> How would you add another tab in the “My Account” section?

To add an additional menu tab in the customer "My Account" area create the layout file in `Your_Company/Your_Module/view/frontend/layout/customer_account.xml`:
```XML
<body>
    <referenceContainer name="content">
        <referenceBlock name="customer_account_navigation">
            <block class="Magento\Customer\Block\Account\SortLinkInterface" 
                   name="yourBlockName"
                   after="customer-account-navigation-address-link">
                <arguments>
                    <argument name="label" xsi:type="string" translate="true">
                        Your Label
                    </argument>
                    <argument name="path" xsi:type="string">
                        path/to/your/module/view/
                    </argument>
                    <argument name="sortOrder" xsi:type="number">
                        10
                    </argument>
                </arguments>
            </block>
        </referenceBlock>
    </referenceContainer>
</body
```

---
> How do you customize the order history page?

To customise the "Order History" page create the layout file in `Your_Company/Your_Module/view/frontend/layout/sales_order_history.xml`. The `sales.order.history.info` container is a common location for modifications to be made.

---
#### 6.7. Add/Modify Customer Attributes
> How do you add or modify customer attributes in a setup script?

Create `Your_Company/Your_Module/Setup/UpgradeData.php` with an `upgrade()` function:
```PHP
use Magento\Customer\Model\Customer;

// ...

$attribute = $this->eavConfig->getAttribute(Customer::ENTITY, Attribute::CUSTOMER_PROMOTION_PREFERENCE);
$attribute->setData('used_in_forms', ['adminhtml_customer', 'customer_account_edit']);
$attribute->save();
```

---
#### 6.8. Customising Customer Addresses
> How do you add another field to the customer address entity using a setup script?

---
## References
### Magento 2 Dev Docs
[Component Load Order](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/build/module-load-order.html)

[Name Your Component](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/build/create_component.html)

[Create Your Component File Structure](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/build/module-file-structure.html)

[Plugins (Interceptors)](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/plugins.html)

[About Magento Modes](https://devdocs.magento.com/guides/v2.3/config-guide/bootstrap/magento-modes.html)
