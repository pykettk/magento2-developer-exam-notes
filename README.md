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
> What are the significant steps to add a new module?</span>

You need to create the following files:
- `registration.php`
- `module.xml`
- `composer.json`

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

##### `composer.json`
This file provides a component name and specifies the your module's dependencies. Example:
```JSON
{
    "name": "your-company/your-module",
    "description": "Your module description",
    "require": {
        "php": "~7.1.3||~7.2.0",
        "magento/module-store": "102.1",
        "magento/module-catalog": "102.1",
        "magento/module-catalog-inventory": "102.1",
        "magento/module-ui": "self.version",
        "magento/magento-composer-installer": "*"
    },
    "suggest": {
      "magento/module-webapi": "102.1"
    },
    "type": "magento2-module",
    "version": "102.1",
    "license": [
        "OSL-3.0",
        "AFL-3.0"
    ],
    "autoload": {
        "files": [ "registration.php" ]
    }
}
```
---
> What are the different Composer package types?</span>

| Name | Package Type | Description |
| :--: | :----------: | :---------- |
| Metapackage | metapackage | Technically, a Composer package type, not a Magento component type. A metapackage consists of only a `composer.json` file that specifies a list of components and their dependencies. For example, both Magento Open Source and Magento Commerce are metapackages. |
| Module | magento2-module | Code that modifies Magento application behavior. You can upload a single module to the Magento Marketplace or your module can be dependent on some parent package. |
| Theme | magento2-theme | Code that modifies the look and feel of the storefront or Magento Admin. |
| Language Package | magento2-language | Translations for the storefront or Admin. |

---
> When would you place a module in the app/code folder versus another location?</span>

#### 1.2. Describe the Magento Directory Structure
> How do you locate different files in Magento?</span>

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
> What are the naming conventions, and how are namespaces established?</span>

---
> How can you identify the files responsible for some functionality?</span>

#### 1.3. Utilise Configuration and Configuration Variables Scope
> Which configuration files are important in the development cycle?</span>

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
> How do you identify the configuration scope for a given variable?</span>

---
> How do native Magento scopes (for example, price or inventory) affect development and decision-making processes?</span>

---
> How can you fetch a system configuration value programmatically?</span>

---
> How can you override system configuration values for a given store using XML configuration?</span>

#### 1.4. Demonstrate How To Use Dependency Injection
> How are objects realized in code?</span>
 
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
> Why is it important to have a centralized object creation process?</span>

Having a centralised process to create objects makes testing much easier. It also
provides a simple interface to substitute objects as well as modify existing ones.

---
> How can you override a native class, inject your class into another object, and use other techniques available in `di.xml` (for example, `virtualTypes`)?</span>

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

```

##### Virtual Types
A virtual type allows you to create an instance of an existing class that has custom constructor arguments. This is useful in cases where you need a “new” class only because the constructor arguments need to be changed. This is used frequently in Magento to reduce redundant PHP classes.

---
> How would you obtain a class instance from different places in the code?</span>

#### 1.5. Demonstrate Ability To Use Plugins
> How are plugins used in core code? How can they be used for customizations?</span>

#### 1.6. Configure Event Observers & Scheduled Joba
> How are observers registered?
> How are they scoped for frontend or backend?
> How are automatic events created, and how should they be used?
> How are scheduled jobs configured?

#### 1.7. Utilise the CLI
> Which commands are available?
> How are commands used in the development cycle?

### 2.0 Request Flow Processing
This section covers **7%** of the exam.

#### 2.1. Describe Magento 2 Modes
> Understand the pros and cons of using developer mode or production mode.
> How do you enable/disable maintenance mode?

#### 2.2. Demonstrate Abilty To Use Frontend Controllers
> How do you identify which module/controller corresponds to a given URL?
> What would you do to create a given URL?

#### 2.3. URL Rewrites
> How is the user-friendly URL of a product or category defined?
> How can you change it?
> How do you determine which page corresponds to a given user-friendly URL?

### 3.0. Customising The Magento UI
This section covers **15%** of the exam.

#### 3.1. Customise The Magento UI Using Themes
> When would you create a new theme?
> How do you define theme hierarchy for a project?

#### 3.2. Blocks & Templates
> How do you assign a template to a block?
> How do you assign a different template to a native block?

#### 3.3. Identify Different Types Of Blocks
> When would you use non-template block types?

#### 3.4. Magento Layout XML
> How do you use layout XML directives in your customizations?
> How do you register a new layout file?

#### 3.5. Create & Add Code & Markup To A Page
> How do you add new content to existing pages using layout XML?

### 4.0. Working With Databases In Magento
This section covers **18%** of the exam.

#### 4.1. Models, Resource Models, And Collections
> What are the responsibilities of each of the ORM object types?
> How do they relate to one another?

#### 4.2. Entity Loading And Saving
> How do you use the native Magento save/load process in the development process?

#### 4.3. Filter, Sort, And Select From Collections & Repositories
> How do you select a subset of records from the database?

#### 4.4. Declarative Schema
> How do you add a column using declarative schema?
> How do you modify a table added by another module?
> How do you delete a column?
> How do you add an index or foreign key using declarative schema?
> How do you manipulate data using data patches?
> What is the purpose of schema patches?

### 5.0. Developing With Adminhtml
This section covers **11%** of the exam.

#### 5.1. Admin Routes
> How would you create an admin controller?
> How do you ensure the right level of security for a new controller?

#### 5.2. System Configuration & Scopes
> How would you add a new system configuration option?
> What is the difference in this process for different option types (secret, file)?

#### 5.3. ACL
> How would you add a new ACL resource to a new entity?
> How do you manage the existing ACL hierarchy?

#### 5.4. Setup A Menu Item
> How do you add a new menu item to a given tab?
> How do you add a new tab to the Admin menu?

#### 5.5. Create User Permissions
> How are menu items related to ACL permissions?
> How do you add a new user with given set of permissions?

### 6.0. Customising Magento Business Logic
This section covers **16%** of the exam.

#### 6.1. Product Types
> How would you obtain a product of a specific type, and what tools (in general) does a product type model provide? 

#### 6.2. Category Properties
> How do you create and manage categories?

#### 6.3. Product/Category Relations
> How do you assign and unassign products to categories?

#### 6.4. Product Behaviour In The Cart
> How are configurable and bundle products rendered?
> How can you create a custom shopping cart renderer?

#### 6.5. Native Shipping Functionality
> How do you customize the shipment step of order management?

#### 6.6. Customer Account Area Customisation
> How would you add another tab in the “My Account” section?
> How do you customize the order history page?

#### 6.7. Add/Modify Customer Attributes
> How do you add or modify customer attributes in a setup script?

#### 6.8. Customising Customer Addresses
> How do you add another field to the customer address entity using a setup script?

## References
### Magento 2 Dev Docs
[Component Load Order](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/build/module-load-order.html)

[Name Your Component](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/build/create_component.html)

[Create Your Component File Structure](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/build/module-file-structure.html)

