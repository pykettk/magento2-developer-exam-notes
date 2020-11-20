<div align="center">

![](https://i.imgur.com/I8i36kp.png)

</div>

<h2 align="center">Magento 2 Developer Certification Exam Notes</h2>

<p align="center">
	A collection of notes for the Magento 2 certified developer exams.
</p>

<div align="center">
	
![Magento 2.4](https://img.shields.io/badge/Magento-2.4ec6611.svg)
![GitHub last commit (master)](https://img.shields.io/github/last-commit/pykettk/magento2-associate-developer-certification/master.svg)

</div>

---

<div align="center">

[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://paypal.me/pykettk)

</div>

---

<br>

## 📝 Table of Contents
- [Tips & Useful Information](#tips--useful-information)
- [Exam Structure](#exam-structure)
- [Exam Content](#exam-content)
  - [Magento Architecture & Customisation Techniques](#10-magento-architecture--customisation-techniques)
  - [Request Flow Processing](#20-request-flow-processing)
  - [Customising Magento UI](#30-customising-the-magento-ui)
  - [Working with Magento Databases](#40-working-with-databases-in-magento)
  - [Developing with Adminhtml](#50-developing-with-adminhtml)
  - [Customising Magento Business Logic](#60-customising-magento-business-logic)
- [References](#references)

<br>

## 🧐 Tips & Useful Information
The exam includes questions relating to Magento Admin - more than you would expect. It is worth knowing some of the more common features and areas:
- Marketing & SEO
- Category management
- Scopes (Global, Website, Store, and Store View)

The questions are typically structured in two parts. They first gives a bit of background information, usually in the form of "you have been asked to do...", then ask you the question they want answering. Make sure to read the questions **carefully** as the background information often includes key words that might trip you up and trick you into answering the wrong question.

Quite a few answers are very similar, make sure you are confident in the exact file paths of files.

It may be beneficial to set up a throwaway Magento environment to test some of the things that you revise - such as managing the database using the Declarative Schema; something introduced within Magento 2.3. There were quite a few questions which involved knowledge regarding Data and Schema patches and being aware when to use them.

<br>

## ⛏️ Exam Structure
### Associate Developer
|       |       |
| :---: | :---: |
| Magento Version | 2.4.x |
| Magento Theme | Luma |
| Number of Questions | 60 |
| Time Limit | 90 Minutes |
| Passing Grade | 68% |

<br>

## ✍️ Exam Content
### 1.0. Magento Architecture & Customisation Techniques
| Certification | Exam Content |
| :-----------: | :----------: |
| Associate Developer | 33% |
| Professional Developer | 18% |

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

Custom built modules and modules you build to extend other functionality are placed in the `app/code/` directory.
Other modules, including extensions and the Magento 2 core modules, are placed in the `vendor/` directory as they
are installed via Composer.

---
> How do different modules interact with each other?



---
> What side effects can come from this interaction?



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

You can use the functions contained within the `Magento\Framework\App\Config\ScopeConfigInterface` class, passing the 
configuration path as the minimum required parameter e.g. `web/secure/base_url`.

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
> How do multiple plugins interact?

The `sortOrder` from the plugins declared in `di.xml` determines the plugin’s prioritisation when more than one plugin is observing the same method.

The `Magento\Framework\Interception\PluginListInterface` which is implemented by `Magento\Framework\Interception\PluginList\PluginList`
is responsible to define when to call the `befor`e, `around`, or `after` methods respecting this prioritisation.

If two or more plugins have the same `sortOrder` value or do not specify it, the component load order declared in the `<sequence>` node from
`module.xml` and the area will define the merge sequence. The component load order can be checked in `app/etc/config.php`.

Magento executes plugins using these rules during each plugin execution in two main flows:

1. Before the execution of the observed method, starting from lowest to highest `sortOrder`.
    - Magento executes the current plugin’s `before` method.
    - Then the current plugin’s `around` method is called.
        - The first part of the plugin’s `around` method is executed.
        - The `around` method executes the callable.
            - If there is another plugin in the chain, all subsequent plugins are wrapped in an independent sequence loop and the execution starts another flow.
            - If the current plugin is the last in the chain, the observed method is executed.
        - The second part of the `around` method is executed.
    - Magento moves on to the next plugin.

2. Following the execution flow, starting from lowest to highest `sortOrder` in the current sequence plugins loop.
    - The current plugin’s `after` method is executed.
    - Magento moves on to the next plugin.
    
As a result of these rules, the execution flow of an observed method is affected not only by the prioritisation of the plugins,
but also by their implemented methods.

Examples of how this prioritisation is realised can be found in the [Magento 2 dev docs regarding plugins and interceptors])https://devdocs.magento.com/guides/v2.4/extension-dev-guide/plugins.html#examples.

---
> How can the plugin execution order be controlled?

The `sortOrder` attribute defines the order in which to execute plugins, starting from the lowest and working through to the highest.

---
> How do you debug a plugin if it doesn’t work?



---
> What are the limitations of using plugins for customization?

Plugins can not be used on the following:
- Final methods
- Final classes
- Non-public methods
- Class methods (such as static methods)
- `__construct`
- Virtual types
- Objects that are instantiated before `Magento\Framework\Interceptio`n is bootstrapped

---
> In which cases should plugins be avoided?


 
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
> Which parameters are used in configuration?



---
> How can configuration interact with server configuration?



---
> Identify the function and proper use of automatically available events, for example *_load_after, etc.



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
#### 1.8. Managing the Cache
> Describe the cache types.



---
> What tools are there to manage the cache?



---
> How do you add dynamic content to pages served from the full page cache?



---
> How would you clean the cache?



---
> When would you refresh/flush the cache storage?



---
> How do you clear the cache programmatically?



---
> What mechanisms are available for clearing all or part of the cache?



---
### 2.0 Request Flow Processing
| Certification | Exam Content |
| :-----------: | :----------: |
| Associate Developer | 7% |
| Professional Developer | 12% |

---
#### 2.1. Magento 2 Modes & Application Initialisation
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
> When do you use default mode?

As its name implies, `default` mode is how Magento operates if no other mode is specified.
It enables you to deploy the Magento application on a single server without changing any settings.
However, `default` mode is not as optimized for production.

To deploy the Magento application on more than one server or to optimize it for production, change to one of the other modes.

In `default` mode:
- Errors are logged to the file reports at server, and never shown to a user
- Static view files are cached
- Not optimized for a production environment, primarily because of the adverse performance impact of static files being dynamically generated rather than materialized. In other words, creating static files and caching them has a greater performance impact than generating them using the static files creation tool.

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
> Identify the steps for application initialisation.



---
> How would you design a customization that should act on every request and capture output data regardless of the controller?



---
#### 2.2. Demonstrate Ability To Use Frontend Controllers
> Describe front controller responsibilities.



---
> In which situations will the front controller be involved in execution, and how can it be used in the scope of customizations?



---
#### 2.3. URL Processing
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
> How do you identify which module/controller corresponds to a given URL?

`https://your-store.com/catalog/product/view/id/42`



---
> What would you do to create a given URL?

Set the Category and Product `url_key` attributes. You can also create URL rewrites through the Magento Admin path `Marketing > SEO & Search > URL Rewrites`

---
> Describe how action controllers and results function.



---
> How do controllers interact with each other?



---
> How are different response types generated?



---
#### 2.4. Customising Request Routing
> Describe the request routing flow in Magento 2.



---
> When is it necessary to create a new router or customise an existing router?



---
> How do you handle custom 404 pages?



---
#### 2.5. Layout Initialisation Process
> Determine how layout is compiled.



---
> How would you debug your `layout.xml` files and verify the right layout instructions are used?



---
> Determine how HTML output is rendered.



---
> How does Magento flush output?



---
> What mechanisms exist to access and customise output?



---
> How do you add new elements to pages introduced by a given module?



---
> How do you identify which exact `layout.xml` file is processed in a given scope?



---
> How does Magento treat layout XML files with the same names in different modules?



---
> Identify the differences between admin and frontend scopes.



---
> What differences exist for layout initialization for the admin scope?



---
#### 2.6. Block Template Structures
> Identify and understand root templates, empty.xml, and page_layout.



---
> How are page structures defined, including number of columns, which basic containers are present, etc.?



---
> Describe the role of blocks and templates in the request flow.



---
> In which situations would you create a new block or a new template?


---
### 3.0. Customising The Magento UI
| Certification | Exam Content |
| :-----------: | :----------: |
| Associate Developer | 15% |
| Professional Developer | 10% |

#### 3.1. Customise The Magento UI Using Themes
> When would you create a new theme?

You would create a new theme when making design changes to the Magento frontend or admin. This typically involves copying and modifying layout files, templates, and styles to achieve your desired design.

---
> How do you define theme hierarchy for a project?

The `theme.xml` file can be used to specify the theme's parent using the `<parent />` node:
```XML
<theme xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/theme.xsd">
    <title>Your Theme Name</title>
	<parent>Vendor/Theme</parent>
</theme>
```

Omitting the `<parent />` node dictates that the theme is the base, or default, theme.

---
> How do you identify which exact theme file is used in different situations?



---
> How can you override native files?



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
> Which objects are accessible from the block?



---
> What is the typical block’s role?



---
> In what cases would you put your code in the `_prepareLayout()`, `_beforeToHtml()`, and `_toHtml()` methods?



---
> How would you use events fired in the abstract block?



---
> Describe how blocks are rendered and cached.



---
> When would you use non-template block types?

| Block Type | Path | Use |
| :--------: | :--: | :-: |
| Text | `vendor/magento/framework/View/Element/Text.php` | Printing a string |
| ListText | `vendor/magento/framework/View/Element/Text/ListTest.php` | Output each of the child blocks |

---
> In what situation should you use a template block or other block types?



---
#### 3.3. Magento Layout XML
> How do you use layout XML directives in your customizations?

Layout XML is what links templates to blocks and there are a number of directives available for use.

##### `<block />`
Defines a block. A block is a unit of page output that renders some distinctive content (anything visually tangible for the end-user), such as a piece of information or a user interface element. Blocks are a foundational building unit for layouts in Magento. They are the link between a PHP block class (which contains logic) and a template (which renders content). Blocks can have children and grandchildren (and so on).

| Attribute | Description | Values | Required |
| :-------: | :---------: | :----: | :------: |
| `class` | Name of a class that implements rendering of a particular block. An object of this class is responsible for actual rendering of block output. | Class name | No |
|` name` | Name that can be used to address the block to which this attribute is assigned. The name must be unique per generated page. If not specified, an automatic name will be assigned in the format `ANONYMOUS_n`. | 0-9, A-Z, a-z, underscore (`_`), period (`.`), dash (`-`). Should start with a letter. Case-sensitive. | No |
| `before` | Used to position the block before an element under the same parent. The element name or alias name is specified in the value. Use dash (`-`) to position the block before all other elements of its level of nesting. | Element name or dash (`-`). | No |
| `after` | Used to position the block after an element under the same parent. The element name or alias name is specified in the value. Use dash (`-`) to position the block after all other elements of its level of nesting. | Element name or dash (`-`). | No |
| `template` | A template that represents the functionality of the block to which this attribute is assigned. | Template file name | No |
| `as` | An alias name that serves as identifier in the scope of the parent element. | Same as `name` | No |
| `cachable` | Defines whether a block element is cacheable. This can be used for development purposes and to make needed elements of the page dynamic. | `true` or `false` | No |

```XML
@TODO: <block /> example
```

##### `<container />`
A structure without content that holds other layout elements such as blocks and containers.  A container renders child elements during view output generation. It can be empty or it can contain an arbitrary set of `<container>` and `<block>` elements. If the `<container>` is empty, and there is no child `<block>` available, it will not be displayed in the frontend source code.

| Attribute | Description | Values | Required |
| :-------: | :---------: | :----: | :------: |
|` name` | Name that can be used to address the block to which this attribute is assigned. The name must be unique per generated page. If not specified, an automatic name will be assigned in the format `ANONYMOUS_n`. | 0-9, A-Z, a-z, underscore (`_`), period (`.`), dash (`-`). Should start with a letter. Case-sensitive. | No |
| `label` | An arbitrary name to display in the web browser. | Any | No |
| `before` | Used to position the block before an element under the same parent. The element name or alias name is specified in the value. Use dash (`-`) to position the block before all other elements of its level of nesting. | Element name or dash (`-`). | No |
| `after` | Used to position the block after an element under the same parent. The element name or alias name is specified in the value. Use dash (`-`) to position the block after all other elements of its level of nesting. | Element name or dash (`-`). | No |
| `as` | An alias name that serves as identifier in the scope of the parent element. | Same as `name` | No |
| `output` | Defines whether to output the root element. If specified, the element will be added to output list. If not specified, the parent element is responsible for rendering its children. | Any value except the obsolete `toHtml`. Recommended value is `1`. | No |
| `htmlTag` | Output parameter. If specified, the output is wrapped into specified HTML tag. | Any valid HTML5 tag | No |
| `htmlID` | Output parameter. If specified, the value is added to the wrapper element. If there is no wrapper element, this attribute has no effect. | Any valid HTML5 `id` value | No |
| `htmlClass` | Output parameter. If specified, the value is added to the wrapper element. If there is no wrapper element, this attribute has no effect. | Any valid HTML5 `class` value | No |

```XML
@TODO: <container /> example
```

##### `<referenceBlock />` and `<referenceContainer />`
Updates in `<referenceBlock>` and `<referenceContainer>` are applied to the corresponding `<block>` or `<container>`. For example, if you make a reference by `<referenceBlock name="right">`, you are targeting the block `<block name="right">`.

| Attribute | Description | Values | Required |
| :-------: | :---------: | :----: | :------: |
| `remove` | Allows you to remove or cancel the removal of the element. When a container is removed, its child elements are removed as well. | `true` or `false` | No |
| `display` | Allows you to disable rendering of specific block or container with all its children (both set directly and by reference). The block’s/container’s and its children’ respective PHP objects are still generated and available for manipulation. | `true` or `false` | No |

The `remove` attribute is optional and its default value is `false` This implementation allows you to remove a block or container in your layout by setting the remove attribute value to `true`, or to cancel the removal of a block or container by setting the value to `false`.
```XML
<referenceBlock name="blockName" remove="true" />
```

The `display` attribute is optional and its default value is `true`. You are always able to overwrite this value in your layout. In situation when the remove value is `true`, the `display` attribute is ignored.
```XML
<referenceContainer name="containerName" display="false" />
```

##### `<arguments />`
`<arguments>` is a required container for `<argument>`. It does not have its own attributes.
```XML
<arguments>
    ...
</arguments>
```

##### `<argument />`
Information can be passed from layout XML files to blocks using the `<argument />` node. These directives must always be enclosed within `<arguments />` directives. Argument values set in a layout file are added to the block's `data` array and can be accessed in templates using the `getData('argumentName')` and `hasData('argumentName')` functions. The latter returns a boolean defining whether there’s any value set.

| Attribute | Description | Values | Required |
| :-------: | :---------: | :----: | :------: |
| `name` | Argument name. | Must be unique. | Yes |
| `xsi:type` | Argument type. | 	`string` / `boolean` / `object` / `number` / `null` / `array` / `options` / `url` / `helper` | Yes |
| `translate` |  | `true` or `false` | No |

```XML
<arguments>
    <!-- String -->
    <argument name="stringArgName" xsi:type="string">
        Some String
    </argument>
    
    <!-- Boolean -->
    <argument name="booleanArgName" xsi:type="boolean">
        true
    </argument>
    
    <!-- Object -->
    <argument name="objectArgName" xsi:type="object">
        <!-- Must implement `Magento\Framework\View\Element\Block\ArgumentInterface` -->
        Namespace\To\Your\Class
    </argument>
    
    <!-- Number -->
    <argument name="numberArgName" xsi:type="number">
        42
    </argument>
    
    <!-- Null -->
    <argument name="nullArgName" xsi:type="null" />
    
    <!-- Array -->
    <argument name="arrayArgName" xsi:type="array">
        <item name="arrayKeyOne" xsi:type="string">First Item</item>
        <item name="arrayKeyTwo" xsi:type="object">Namespace\Of\Your\Class</item>
        ...
    </argument>
    
    <!-- Options -->
    <argument name="optionsArgName" xsi:type="options">
        <!-- Must implement `Magento\Framework\Data\OptionSourceInterface` -->
        Namespace\Of\Your\Class
    </argument>
    
    <!-- URL -->
    <argument name="urlArgName"
              xsi:type="url"
              path="your/url/path"
    />
    
    <!-- Helper -->
    <argument name="helperArgName" 
              xsi:type="string"
              helper="Namespace\To\Your\Helper\Class::helperFunction">
        <param name="paramName">paramValue</param>
    </argument>
<arguments>
```

**NB:** The `helper` can only use public functions.

##### `<move />`
Sets the declared block or container element as a child of another element in the specified order. 

| Attribute | Description | Values | Required |
| :-------: | :---------: | :----: | :------: |
| `element` | Name of the element to move. | Element name | Yes |
| `destination` | Name of the target parent element. | Element name | Yes |
| `as` | Alias name for the element in the new location. | 0-9, A-Z, a-z, underscore (`_`), period (`.`), dash (`-`). Case-sensitive.	 | No |
| `after` or `before` | Specifies the element’s position relative to siblings. Use dash (`-`) to position the block before or after all other siblings of its level of nesting. If the attribute is omitted, the element is placed after all siblings. | Element name | No |

```XML
<move element="elementName"
      destination="parentElementName"
      as="newAlias"
      after="siblingElementName"
      before="anotherSiblingElementName"
/>
```

**NB:**
- `<move>` is skipped if the element to be moved is not defined.
- If the `as` attribute is not defined, the current value of the element alias is used. If that is not possible, the value of the `name` attribute is used instead.
- During layout generation, the `<move>` instruction is processed before the removal (set using the `remove` attribute). This means if any elements are moved to the element scheduled for removal, they will be removed as well.

##### `<remove />`
`<remove>` is used only to remove the static resources linked in a page `<head>` section. For removing blocks or containers, use the `remove` attribute for `<referenceBlock>` and `<referenceContainer>`.
```XML
<!-- Remove local resources -->
<remove src="css/styles-m.css" />
<remove src="my-js.js"/>
<remove src="Magento_Catalog::js/compare.js" />

<!-- Remove external resources -->
<remove src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap-theme.min.css"/>
<remove src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/js/bootstrap.min.js"/>
<remove src="http://fonts.googleapis.com/css?family=Montserrat" />
```

##### `<update />`
Includes a certain layout file.
```XML
<update handle="{handleNameToInclude}" />
```

---
> How do you register a new layout file?



---
> How do you pass variables from layout to block?

Information can be passed from layout XML files to blocks using the `<argument />` node.
These directives must always be enclosed within `<arguments />` directives. Argument values set in a layout file
are added to the block's `data` array and can be accessed in templates using the `getData('argumentName')` and
`hasData('argumentName')` functions. The latter returns a boolean defining whether there’s any value set.

| Attribute | Description | Values | Required |
| :-------: | :---------: | :----: | :------: |
| `name` | Argument name. | Must be unique. | Yes |
| `xsi:type` | Argument type. | `string` / `boolean` / `object` / `number` / `null` / `array` / `options` / `url` / `helper` | Yes |
| `translate` |  | `true` or `false` | No |

```XML
<arguments>
    <!-- String -->
    <argument name="stringArgName" xsi:type="string">
        Some String
    </argument>
    
    <!-- Boolean -->
    <argument name="booleanArgName" xsi:type="boolean">
        true
    </argument>
    
    <!-- Object -->
    <argument name="objectArgName" xsi:type="object">
        <!-- Must implement `Magento\Framework\View\Element\Block\ArgumentInterface` -->
        Namespace\To\Your\Class
    </argument>
    
    <!-- Number -->
    <argument name="numberArgName" xsi:type="number">
        42
    </argument>
    
    <!-- Null -->
    <argument name="nullArgName" xsi:type="null" />
    
    <!-- Array -->
    <argument name="arrayArgName" xsi:type="array">
        <item name="arrayKeyOne" xsi:type="string">First Item</item>
        <item name="arrayKeyTwo" xsi:type="object">Namespace\Of\Your\Class</item>
        ...
    </argument>
    
    <!-- Options -->
    <argument name="optionsArgName" xsi:type="options">
        <!-- Must implement `Magento\Framework\Data\OptionSourceInterface` -->
        Namespace\Of\Your\Class
    </argument>
    
    <!-- URL -->
    <argument name="urlArgName"
              xsi:type="url"
              path="your/url/path"
    />
    
    <!-- Helper -->
    <argument name="helperArgName" 
              xsi:type="string"
              helper="Namespace\To\Your\Helper\Class::helperFunction">
        <param name="paramName">paramValue</param>
    </argument>
<arguments>
```

**NB:** The `helper` can only use public functions.

---
#### 3.4. Utlise JavaScript in Magento
> Which JavaScript modules are are suited for which tasks?



---
> In which situation would you use UI Components over a regular JavaScript module?



---
> Describe the use of `requirejs-config.js`, `x-magento-init`, `data-mage-init`.



---
### 4.0. Working With Databases In Magento
| Certification | Exam Content |
| :-----------: | :----------: |
| Associate Developer | 18% |
| Professional Developer | 7% |

#### 4.1. Models, Resource Models, And Collections
> What are the responsibilities of each of the ORM object types?

The Magento ORM elements are:
- **Models:** Define entities, their data, and their behaviour.
- **Resource Models:** Data mappers for storage structures.
- **Collections:** Stores sets of Models and related functionality including filtering, sorting, and pagination.
- **Resources:** Maintain database connections through adapters.

---
> How do they relate to one another?



---
> Describe repositories and data API classes.



---
> How do you obtain an object or set of objects from the database using a repository?



---
> How do you configure and create a SearchCriteria instance using the builder?



---
> How do you use Data/Api classes?



---
> How do you add a new table to the database?



---
> Describe the entity load and save process.



---
> Describe how to extend existing entities. What mechanisms are available to extend existing classes, for example by   adding a new attribute, a new field in the database, or a new related entity?



---
> Describe how to filter, sort, and specify the selected values for collections and repositories.

##### Collections
**Filter:** `$collection->addFieldToFilter()`
**Sort:** `$collection->addOrder()`
**Select Column:** `$collection->addFieldToSelect()`
**Pagination:** `$collection->setPageSize()` and `$collection->setCurPage()`

##### Repositories
https://devdocs.magento.com/guides/v2.2/extension-dev-guide/searching-with-repositories.html

---
> How do you select a subset of records from the database?



---
> Describe the database abstraction layer for Magento.



---
> What type of exceptions does the database layer throw?



---
> What additional functionality does Magento provide over Zend_Adapter?



---
#### 4.2. Declarative Schema
Declarative schema in Magento 2.3 allows developers to declare the final desired state of the database and has the system adjust to it automatically, without performing redundant operations. Developers are no longer forced to write scripts for each new version. Additionally, this approach allows data be deleted when a module is uninstalled - something that previously had to be done separately.

##### Terminology
Data Patch
- A class that contains data modification instructions. It can have dependencies on other data or schema patches.
- Some data patches can be reverted as a module or patch is uninstalled or deleted. Revertable operations are Data Query Language (DQL) and Data Manipulation Language (DML) operations: `INSERT`, `UPDATE`.

Schema Patch
- A class that contains custom schema modification instructions. Schema patches are used along with declarative schema, but these patches allow complex operations such as:
    - Adding triggers, stored procedures, or functions
    - Performing data migration with inside DDL operations
    - Renaming tables, columns, and other entities
    - Adding partitions and options to a table

---
> How do you add a column using declarative schema?

You can create/modify the `etc/db_schema.xml` file, specifying a `<table />` node for the table you are editing and adding a `<column />` node inside to create the new column.
```XML
<schema xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Setup/Declaration/Schema/etc/schema.xsd">
    <table name="table_name">
        <column xsi:type="varchar"
                name="title"
                padding="10"
                comment="Page Title"
        />
    </table>
</schema>
```

You can also create a schema patch file in the `YourCompany\YourModule\Setup\Patch\Schema` namespace:
```php
<?php
namespace YourCompany\YourModule\Setup\Patch\Schema;

use Magento\Framework\DB\Ddl\Table;
use Magento\Framework\Setup\Patch\SchemaPatchInterface;
use Magento\Framework\Setup\SchemaSetupInterface;

class YourSchemaPatch implements SchemaPatchInterface
{
    /** @var SchemaSetupInterface */
    private $moduleSchemaSetup;

    /**
     * YourSchemaPatch Constructor.
     *
     * @param SchemaSetupInterface $moduleSchemaSetup
     */
    public function __construct(SchemaSetupInterface $moduleSchemaSetup)
    {
        $this->moduleSchemaSetup = $moduleSchemaSetup;
    }

    /**
     * {@inheritdoc}
     */
    public function apply()
    {
        $this->schemaSetup->startSetup();
        $table = $this->schemaSetup->getTable('your_table_name');
        $connection = $this->schemaSetup->getConnection();
        
        if ($connection->isTableExists($table)) {
            // declare new columns to add
            $newColumns = [
                'shiny_new_column' => [
                    'type' => Table::TYPE_TEXT,
                    'nullable' => true,
                    'comment' => 'Shiny New Column',
                ],
                'another_shiny_column' => [
                    'type' => Table::TYPE_TEXT,
                    'nullable' => true,
                    'comment' => 'OMG More Shinies! :O',
                ],
            ];

            foreach ($newColumns as $newColumn => $columnDescription) {
                $connection->addColumn($table, $newColumn, $columnDescription);
            }
        }

        $this->schemaSetup->endSetup();
    }

    /**
     * {@inheritDoc}
     */
    public static function getDependencies()
    {
        // return [dependencies]
    }
    
    /**
     * {@inheritDoc}
     */
    public function getAliases()
    {
        // return [aliases]
    }
}
```

**NB:** When adding new columns to a table, you also need to generate the `db_schema_whitelist.json` file.

---
> How do you modify a table added by another module?

To modify a table added by another module, create `Your_Company/Your_Module/etc/db_schema.xml` and specify the table name in the `<table />` node:
```XML
<schema xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Setup/Declaration/Schema/etc/schema.xsd">
    <table name="table_create_by_other_module">
        ...
    </table>
</schema>
```

---
> How do you delete a column?

To remove a column from a table created by your module you can simply remove the corresponding `<column />` node in `Your_Company/Your_Module/etc/db_schema.xml`. To drop a column declared in another module, you must redeclare it and set the `disabled` attribute to `true`:
```XML
<schema xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Setup/Declaration/Schema/etc/schema.xsd">
    <table name="table_create_by_other_module">
        ...
        <column xsi:type="int"
                name="new_id"
                padding="10"
                unsigned="true"
                nullable="false"
                comment="New ID"
                disabled="true"
        />
        ...
    </table>
</schema>
```

---
> How do you add an index or foreign key using declarative schema?

##### Index
To add an index, declare an `<index />` node in `Your_Company/Your_Module/etc/db_schema.xml`:
```XML
<schema xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Setup/Declaration/Schema/etc/schema.xsd">
    <table name="table_create_by_other_module">
        ...
        <index referenceId="INDEX_NAME" indexType="btree">
            <column name="column_name" />
        </index>
        ...
    </table>
</schema>
```

##### Foreign Key
To add a foreign key, declare a `<constraint />` node with an `xsi:type` of `foreign` in `Your_Company/Your_Module/etc/db_schema.xml`:
```XML
<schema xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Setup/Declaration/Schema/etc/schema.xsd">
    <table name="table_create_by_other_module">
        ...
        <constraint xsi:type="foreign"
                    referenceId="YOUR_REFERENCE_ID"
                    
                    <!-- The current table -->
                    table="table_name"
                    
                    <!-- The column in the current table that refers to a column in another table -->
                    column="column_name"
                    
                    <!-- The table being referenced -->
                    referenceTable=""
                    
                    <!-- The column being referenced -->
                    referenceColumn=""
                    
                    <!-- Foreign key trigger (CASCADE/SET/NULL/NO ACTION) -->
                    onDelete="CASCADE"
        />
        ...
    </table>
</schema>
```

---
> How do you manipulate data using data patches?

---
> What is the purpose of schema patches?

Schema patches are used to make custom database schema modifications:
- Renaming tables
- Adding/removing columns
- Setting primary & foreign keys

---
> What is the purpose of whitelisting?



---
> How to use Data and Schema patches?



---
> How to manage dependencies between patch files?

Dependencies between patch files are managed via the patch file's `getDependencies` method. This method should
return an array of all of the patch files that are required to be executed **before** it.

---
### 5.0. Entity-Attribute-Value (EAV) 
#### 5.1. EAV Model Concepts
> Describe the EAV hierarchy structure.
> What happens when a new attribute is added to the system?
> What is the role of attribute sets and attribute groups?
> How are attributes presented in the admin?
> Describe how EAV data storage works in Magento.
> Which additional options do you have when saving EAV entities?
> How do you create customizations based on changes to attribute values?
> Describe the key differences between EAV and flat table collections.
> In which situations would you use EAV for a new entity?
> What are the pros and cons of EAV architecture?

#### 5.2. EAV Loading & Saving
> Describe the EAV load and save process and differences from the flat table load and save process.
> What happens when an EAV entity has too many attributes?
> How does the number of websites/stores affect the EAV load/save process?
> How would you customize the load and save process for an EAV entity in the situations described here?

#### 5.3. Attribute Management
> Describe EAV attributes, including the frontend/source/backend structure.



---
> How would you add dropdown/multiselect attributes?



---
> What other possibilities do you have when adding an attribute (to a product, for example)?



---
> Describe how to implement the interface for attribute frontend models.



---
> What is the purpose of this interface?



---
> How can you render your attribute value on the frontend?



---
> Identify the purpose and describe how to implement the interface for attribute source models.



---
> For a given dropdown/multiselect attribute, how can you specify and manipulate its list of options?



---
> Identify the purpose and describe how to implement the interface for attribute backend models.



---
> How (and why) would you create a backend model for an attribute?



---
> Describe how to create and customize attributes.



---
> How would you add a new attribute to the product, category, or customer entities?



---
> What is the difference between adding a new attribute and modifying an existing one?



---
### 6.0. Developing With Adminhtml
| Certification | Exam Content |
| :-----------: | :----------: |
| Associate Developer | 11% |
| Professional Developer | 10% |

#### 6.1. Adminhtml Structure/Architecture
> Describe the difference between Adminhtml and frontend.



---
> What additional tools and requirements exist in the admin?



---
#### 6.2. Form & Grid Widgets
> Define form structure, form templates, grids, grid containers, and elements.



---
> What steps are needed to display a grid or form?



--- 
> How is data provided to the grid or form? How can this be process be customized or extended?



---
> Describe how to create a simple form and grid for a custom entity.



---
> Given a specific entity with different types of fields (text, dropdown, image, file, date, and so on) how would you create a grid and a form?



---
#### 6.3. System Configuration & Scopes
> How would you add a new system configuration option?

---
> What is the difference in this process for different option types (secret, file)?

---
> How do you access system configuration options programmatically?

You can use the functions contained within the `Magento\Framework\App\Config\ScopeConfigInterface` class, passing the 
configuration path as the minimum required parameter e.g. `web/secure/base_url`.

---
#### 6.4. ACL & User Permissions
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
5
            'is_active'         => 1
        ]
    );
    
    $user->setRoleId(1);
    $user->save(); 
}
```

---
### 11.0. Customising Magento Business Logic
| Certification | Exam Content |
| :-----------: | :----------: |
| Associate Developer | 16% |

#### 11.1. Product Types

| Product Type | Description |
| :----------: | :---------: |
| Simple | A simple product is a physical item with a single SKU. Simple products have a variety of pricing and of input controls which makes it possible to sell variations of the product. Simple products can be used in association with grouped, bundle, and configurable products. |
| Configurable | A configurable product appears to be a single product with lists of options for each variation. However, each option represents a separate, simple product with a distinct SKU, which makes it possible to track inventory for each variation.  |
| Grouped | A grouped product presents multiple, standalone products as a group. You can offer variations of a single product, or group them for a promotion. The products can be purchased separately, or as a group. |
| Bundle | A bundle product let customers “build their own” from an assortment of options. The bundle could be a gift basket, computer, or anything else that can be customized. Each item in the bundle is a separate, standalone product. |
| Virtual | Virtual products are not tangible products, and are typically used for products such as services, memberships, warranties, and subscriptions. Virtual products can be used in association with grouped and bundle products. |
| Downloadable | A digitally downloadable product that consists of one or more files that are downloaded. The files can reside on your server or be provided as URLs to any other server. |
| Gift Card | There are three kinds of gift cards: virtual gift cards which are sent by email, physical gift cards which are shipped to the recipient, and combined gift cards which are a combination of the two. Each has a unique code, that is redeemed during checkout. Gift cards can also be included in a grouped product. |

---
> How would you obtain a product of a specific type? 

To obtain a specific product type, build search criteria and retrieve them from the product repository:
```PHP
use Magento\Catalog\Api\Data\ProductInterface;
use Magento\Bundle\Model\Product\Type;

...

public function getBundleProducts()
{
    $searchCriteria = $this->searchCriteriaBuilder
        ->addFilter(ProductInterface::TYPE_ID, Type::TYPE_CODE)
        ->create();
    
    return $this->productRepository->getList($searchCriteria)->getItems();
}
```

---
> What tools (in general) does a product type model provide?

The product type model is responsible for:
- Loading and configuring product options
- Preparing the product for the cart
- Processing product data to/from the database
- Checking whether the product can be sold
- Loading child products, when applicable

---
#### 11.2. Category Properties
> How do you create and manage categories?

---
#### 11.3. Product/Category Relations
> How do you assign and unassign products to categories?

Navigate to `Catalog > Inventory > Products` in Magento Admin, select the product you wish to add to a category, and use the `Categories` drop down selection options to assign it to categories.

---
#### 11.4. Product Behaviour In The Cart
> How are configurable and bundle products rendered?

`view/frontend/layout/checkout_cart_item_renderers.xml` define how products are rendered in the cart.

Configurable products are shown as a single line item. It is shown with the parent product's title and the simple product option selected by the customer.

Each product in the Bundle product is rendered as a single line item.

---
> How can you create a custom shopping cart renderer?

---
#### 11.5. Native Shipping Functionality
> How do you customize the shipment step of order management?

---
#### 11.6. Customer Account Area Customisation
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
#### 11.7. Add/Modify Customer Attributes
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
#### 11.8. Customising Customer Addresses
> How do you add another field to the customer address entity using a setup script?

---
## References
### Magento 2 Dev Docs
[Component Load Order](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/build/module-load-order.html)

[Name Your Component](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/build/create_component.html)

[Create Your Component File Structure](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/build/module-file-structure.html)

[Plugins (Interceptors)](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/plugins.html)

[About Magento Modes](https://devdocs.magento.com/guides/v2.3/config-guide/bootstrap/magento-modes.html)

[Layout Instructions](https://devdocs.magento.com/guides/v2.3/frontend-dev-guide/layouts/xml-instructions.html)

[Product Types](https://docs.magento.com/m2/ee/user_guide/catalog/product-types.html)

[Declarative Schema](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/declarative-schema/)
