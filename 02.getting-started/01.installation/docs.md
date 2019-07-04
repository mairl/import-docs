---
title: Installation
visible: true
---

To use the **M2IF - Magento 2 Import Framework**, one of three existing **M2IF** installation routines are offered according to a existing ***Magento installation*** and its given requirements.

## Case 1: **M2IF** standalone installation
* Use of the **M2IF** tool to import csv files to ***Magento***.
* Install **M2IF** as a standalone composer Project independent to ***Magento*** to a seperate location.
* e.g. extend the **M2IF** functionality to fit the needs for a specific data import to ***Magento***.
* In a Docker environment, the use case could be to run **M2IF** in additional containers.

#### Requirements
* console 
* composer

> Using this command without a addidional location, the default installation folder is "import-cli-simple"

```sh
composer create-project techdivision/import-cli-simple --no-dev
```

> Or using this command under specification of a custom target folder (e.g. m2if-standalone)

```sh
composer create-project techdivision/import-cli-simple --no-dev m2if-standalone
```

After using the installation command, the repository is cloned from our internal gitlab and **M2IF** is installed locally. No further steps are required to use **M2IF**.

## Case 2: Install **M2IF** as Composer Library in magento 2

The second option, and in most ***Magento 2*** projects the preferred way, is to install it as a composer library. 
For example, if you want to ship **M2IF** in your Magento 2 project, simply add the following code snippet to the required section in ***Magento*** ```composer.json```  

#### Requirements
* console 
* composer
* ***Magento 2*** setup

```json
{
  "require": {
    "techdivision/import-cli-simple" : "3.4.*"
  }
}
```

After adding the **M2IF** requirenment to the ```composer.json```, simply run the following command from your Magento 2 root directory, to ensure Magento is up to date.

```sh 
composer update
```

### Case 3: Run **M2IF** as PHAR Archive 

The third installation option is to download **M2IF** as a phar archive. 

* To receive the latest PHAR **M2IF - Simple Console Tool** from our release page and make it executable (e.g. with `wget`).
* [Github](https://github.com/techdivision/import-cli-simple/releases)

```sh
wget https://github.com/techdivision/import-cli-simple/releases/download/3.4.1/import-cli-simple.phar && sudo chmod +x import-cli-simple.phar
```

To install the PHAR in your actual Magento 2 installation, move it 

* to the following location: `<MAGENTO-ROOT>/bin/import-cli-simple.phar`
* or to install it globally, to `/usr/bin/import-cli-simple.phar`. 
* Make sure to set the right permissions in order to be able to install the ```import-cli-simple.phar``` to ```usr/bin```


### IMPORTANT: Add missing Indexes to ***Magento*** tables

* As the **M2IF** functionality differs from the *Magento 2* standard, for performance reasons, it is necessary to add some essential indexes manually. 
* To do that, open a MySQL command line, connect to your MySQL server instance and enter the following SQL statement
 
 #### Requirements
* console 
* ***Magento 2*** setup

```sql
ALTER TABLE `eav_attribute_option_value` ADD INDEX `EAV_ATTRIBUTE_OPTION_VALUE_VALUE` (`value` ASC);
ALTER TABLE `catalog_product_entity_int` ADD INDEX `CATALOG_PRODUCT_ENTITY_INT_VALUE` (`value` ASC);
ALTER TABLE `catalog_product_entity_varchar` ADD INDEX `CATALOG_PRODUCT_ENTITY_VARCHAR_VALUE` (`value` ASC);
ALTER TABLE `catalog_product_entity_decimal` ADD INDEX `CATALOG_PRODUCT_ENTITY_DECIMAL_VALUE` (`value` ASC);
ALTER TABLE `catalog_product_entity_datetime` ADD INDEX `CATALOG_PRODUCT_ENTITY_DATETIME_VALUE` (`value` ASC);
ALTER TABLE `url_rewrite` ADD INDEX `URL_REWRITE_ENTITY_ID` (`entity_id` ASC);
ALTER TABLE `url_rewrite` ADD INDEX `URL_REWRIRE_ENTITY_TYPE_ENTITY_ID` (`entity_id` ASC, `entity_type` ASC);
ALTER TABLE `catalog_product_entity_media_gallery` ADD INDEX `CATALOG_PRODUCT_ENTITY_MEDIA_GALLERY_VALUE` (`value`);
```

When using the Magic 360 [component](/components/3rd-party-components), additionally the following indexes have to be added
 
```sql
ALTER TABLE `magic360_gallery` ADD INDEX `MAGIC360_GALLERY_PRODUCT_ID` (`product_id`);
ALTER TABLE `magic360_gallery` ADD INDEX `MAGIC360_GALLERY_POSITION` (`position`);
```

> This also improves performance of the *Magento 2* standard import functionality, but not at same level as for **M2IF**.

> If the **M2IF** gets customized, make sure to set the Indexes to the nessary tables. 