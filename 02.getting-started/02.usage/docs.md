---
title: Usage
visible: true
---
After a successful installation, the Importer is ready for use. 
 
There are two ways to address the Importer or to access the Importer configuration file:

1. Assuming that you are in the ***Magento*** root directory, the configuration for the Workflow Engine does ***NOT***  have to be specified, because the Importer analyzes the setting of the installation under `app/etc/env.php` and loads the ***Magento Edition/Version*** as well as the database configuration. 

2. if you are outside the ***Magento*** root directory, e.g., using the **M2IF** standalone, or are working with the `import-cli-simple.phar` from the folder `<MAGENTO-ROOT>/bin/import-cli-simple.phar` or globally under `usr/bin/import-cli-simple.phar`, you have to explicitly specify the configuration file path as a parameter, where you can also create and access a separate configuration file for each Magento installation.

By default, the importer searches for CSV files in the directory `<MAGENTO-ROOT>/var/importexport`.<br>

>If the `importexport` folder does not exist, it must be created.<br>
The import command does not implement any directory clean-up or archiving functionality, what means that you’ve to copy the the CSV files manually to the source directory. <br>
By default, the ***CSV*** files as well as the **OK** file are expected to be in the folder `var/importexport` below your Magento root directory.

## Filename Convention
The naming of the ***CSV*** files is subject to the following rules:

* The hyphen in the ***prefix*** and ***Date/Timestamp*** is allowed.
* The three parts of the file name are separated with ***underscores***.
* ***Part 1:*** The prefix can be named arbitrarily.
* ***Part 2:*** The date/time stamp should have the format **YYYYMMMDD-hhmmss**.
* ***Part 3:*** Should contain a consecutive number.
* The file suffix must be **.csv**.

e.g. `prefix_Date/Timestamp_continuous_number.csv`.                     

## Examples

`product-import_20161024-194026_01.csv`<br>
`product-import_20161024-194026_02.csv`<br>
`attribute-import_20161024-194026_01.csv`<br>
`attribute-import_20161024-194026_02.csv`<br>

Assuming, your ***CSV*** file `var/importexport/product-import_20180403-190920_01.csv` is ready to be imported and you are using 
`import-cli-simple.phar`, you can start the importer with  

> Without explicit specification of a configuration file in the CLI command

```sh
bin/import-cli-simple.phar import:create:ok-file && bin/import-cli-simple.phar import:products
```
> With explicit specification of a configuration file in the CLI command. <br>
> In case of concatinating serveral commands, it is required to set the configuration argument for each command

```sh
bin/import-cli-simple.phar import:create:ok-file --configuration=pathToMyConfigurationFile.json && bin/import-cli-simple.phar import:products --configuration=pathToMyConfigurationFile.json
```
> While the first command creates the mandatory `.OK` file that signals that all import artifacts are present, the second command finally starts the import with the [add-update](#operations) operation on your ***Magento 2*** installation. The import commands support **one argument** and **several options**.

```
# or in this example calling the default phar json configuration file e.g.
bin/import-cli-simple.phar import:attributes:set --configuration=phar://bin/import-cli-simple.phar/vendor/techdivision/import-attribute-set/etc/techdivision-import.json
```

## Meaning and use of the .OK file
Since the standard ***Magento*** import functionality does not use an **.OK** file, here a brief introduction to what the **.OK** file is needed for. 

**M2IF** also supports bundles. A bundle in the sense of **M2IF** is a ***CSV*** file which contains only a part of the entire data to be imported.<br>
Therefore it is necessary to signal that all files are available for an import and that the import process can now be started. 

As this is usually not done manually on the command line, but mostly via ***CRON-Job***, this signal is the **.OK** file.<br>
The import process only starts, when an **.OK** file is available in the same directory where the CSV files are located. <br>
The name of the **.OK** file **MUST** follow one of these naming conventions:

`<IMPORT-DIRECTORY>/<PREFIX>.ok`<br>
`<IMPORT-DIRECTORY>/<PREFIX>_<FILENAME>.ok`<br>
`<IMPORT-DIRECTORY>/<PREFIX>_<FILENAME>_<COUNTER>.ok`<br>

which results in one the following examples

`var/importexport/product-import.ok`<br>
`var/importexport/product-import_20161024-194026.ok`<br>
`var/importexport/product-import_20161024-194026_01.ok`<br>

> If you want to import a stack of **CSV** files, the **.OK** file **MUST** contain the name of the existing **CSV** files that will need to be imported in the next iteration.

> In our case the **.OK** file should be named `var/importexport/product-import.ok` and should contain the name of the **CSV** files to be imported in the next iteration. <br>
> In our case the **.OK** file should be named `var/importexport/product-import.ok` and should contain the following lines e.g.
 
`product-import_20161024-194026_01.csv<br>
`product-import_20161024-194026_02.csv<br>
`product-import_20161024-194026_03.csv<br>
`product-import_20161024-194026_04.csv<br>
 
`product-import_20161024-194026_01.csv`<br>
`product-import_20161024-194026_02.csv`<br>
`product-import_20161024-194026_03.csv`<br>
`product-import_20161024-194026_04.csv`<br>

Creating the **.OK** file can either be done manually, or you may let the **M2IF – Simple Console Tool** create it. <br>
Therefore use following command

```sh
bin/import-cli-simple.phar import:create:ok-file
```

> which should result in

```sh
Successfully written OK file var/importexport/product-import.ok
```

## Commands

Besides the ***import commands***, several other more or fewer ***helper commands*** are available. <br>

**The following commands for importing the entities are available:**

| Argument                      | Description                                                     | Format |
|:------------------------------|:----------------------------------------------------------------|:-----------------|
| **import:categories**             | Starts importing categories | [Customer + Customer Address Import](/file-structure/category-import) |
| **import:customers**              | Starts importing customers | [Customer + Customer Address Import](/file-structure/customer-and-customer-address-import) |
| **import:customers:address**      | Starts importing customer addresses, expects that the customers are available | [Customer + Customer Address Import](/file-structure/customer-and-customer-address-import) |
| **import:attributes:set**         | Starts importing attribute sets and their groups | [Attribute Set + Group Import](/file-structure/attributes-set-and-group-import) |
| **import:attributes**             | Starts importing attributes, expects that the referenced attribute sets + groups are available | [Attribute Import](/file-structure/attributes) |
| **import:products**               | Starts the product import, expects that the referenced attributes as well as the attribute sets and groups are available | [Product Import](/file-structure/product-import) |
| **import:products:inventory**     | Starts importing product inventory, expects that the products are available | [Product Import](/file-structure/product-import) |
| **import:products:inventory:msi** | Starts importing product MSI inventory, expects that the products are available | [Product Import // MSI](/file-structure/product-import-msi) |
| **import:products:price**         | Starts importing product prices, expects that the products are available | [Product Import](/file-structure/product-import) |
| **import:products:price:tier**    | Starts importing product tier prices, expects that the products are available | [Product Import // Tier Price](/file-structure/product-import-tier-price) |

By default, if no other source directory has been configured, either as command line option or in the configuration file, all commands are searching for the CSV files and the matching .OK file in the var/importexport directory of your Magento installation.

> In general, it is possible to **ALWAYS** use the `import:products` command if a configuration file with the `--configuration` option is specified. <br>
The different commands make sure that the corresponding default configuration files are used.

## Arguments

The following configuration arguments are available:

| Argument             | Description                                                     | Default value |
|:---------------------|:----------------------------------------------------------------|:--------------|
| **operation**            | Specify the operation name to execute, either one of add-update, replace or delete (for further information look at the next [section](#operations))| n/a |

## Operations

In addition to the ***Magento 2*** standard import functionality, **M2IF** offers 3 different import operations:

| Operation                 | Description
|:--------------------------|:-----------------------------------------------------------------------------------|
| add-update                | New product data is added to the existing product data for the existing entries in the database. All fields except sku can be updated. New tax classes that are specified in the import data are created automatically. New SKUs that are specified in the import file are created automatically. |
| replace                   | The existing product data is replaced with new data. If a SKU in the import data matches the SKU of an existing entity, all fields, including the SKU are deleted, and a new record is created using the CSV data. An error occurs if the CSV file references a SKU that does not exist in the database. |
| delete                    | Any entities in the import data that already exist in the database are deleted from the database. Delete ignores all columns in the import data, except for SKU. You can disregard all other attributes in the data. An error occurs if the CSV file references a SKU that does not exist in the database. |

> Exercise caution when replacing data because the existing product data will be completely cleared and all references in the system will be lost.

## Options

The following configuration options are available:

| Option               | Description                                                     | Default value |
|:---------------------|:----------------------------------------------------------------|:--------------|
| <nobr>**--serial**</nobr>             | Specify the unique identifier of this import process | Some UUID |
| <nobr>**--configuration**</nobr>      | Specify the pathname to the configuration file to use | `./vendor/techdivision/import-product/etc/techdivision-import.json` |
| <nobr>**--pid-filename**</nobr>       | The explicit PID filename to use | `<system-temp-dir>/importer.pid` |
| <nobr>**--system-name**</nobr>        | The system name to be used (will added to the mail subject, if mails are configured) | The hostname |
| <nobr>**--installation-dir**</nobr>   | The Magento installation directory to which the files has to be imported | The actual working directory |
| <nobr>**--entity-type-code**</nobr>   | The Magento entity type code, **MUST** be one of `catalog_product` or `catalog_category`  | n/a |
| <nobr>**--source-dir**</nobr>         | The directory that has to be watched for new files | n/a |
| <nobr>**--target-dir**</nobr>         | The target directory with the files that has been imported | n/a |
| <nobr>**--archive-dir**</nobr>        | The directory with the archived files that has been imported | n/a |
| <nobr>**--archive-artefacts**</nobr>  | The flag to activate the artefact archiving functionality. Note: only `true/false` notation is allowed. | `true` |
| <nobr>**--magento-edition**</nobr>    | The Magento edition to be used, either one of CE or EE | n/a |
| <nobr>**--magento-version**</nobr>    | The Magento version to be used, e. g. 2.1.2 | n/a |
| <nobr>**--source-date-format**</nobr> | The date format used in the CSV file(s) | n/a |
| <nobr>**--use-db-id**</nobr>          | The ID of the database to use, if not specified, the database with the default flag will be used | n/a |
| <nobr>**--db-pdo-dsn**</nobr>         | The DSN used to connect to the Magento database where the data has to be imported, e. g. `mysql:host=127.0.0.1;dbname=magento` | n/a |
| <nobr>**--db-username**</nobr>        | The username used to connect to the Magento database | n/a |
| <nobr>**--db-password**</nobr>        | The password used to connect to the Magento database | n/a |
| <nobr>**--debug-mode**</nobr>         | The flag to activate the debug mode | `false` |
| <nobr>**--log-level**</nobr>          | The log level to use (see Monolog documentation for further information) | `info` |
| <nobr>**--single-transaction**</nobr> | The flag to wrap the import process into a single transaction | `false` |
| <nobr>**--params**</nobr>             | A JSON encoded string that'll be merged with the params from the configuration file (has to be in the same format) | n/a |
| <nobr>**--params-file**</nobr>        | The path to a file with the JSON encoded params that will be merged with the params from the configuration file  (has to be in the same format)| n/a |

Besides the `configuration` option, all options can and **SHOULD** be defined in the configuration file. The command-line options should only be used to override these values in some circumstances.

If the `configuration` option is specified **NOT**, the system attempts to find the Magento edition based on the `installation-dir` option specified.<br>
 If the `Installationsdir` option **IS** is explicitly specified and the directory is a valid ***Magento*** root directory, the application will attempt to load database credentials from the `app/etc/env.php` script so that it is **NOT** necessary to specify a database configuration, either in the configuration file or as a command line parameter.

## Debug Mode

The debug mode provides a more detailed logging output (including PHP version, a list with activated extensions and a check if XDebug is enabled), by automatically setting the Monolog log level to `LogLevel::DEBUG` if **NOT** overwritten with the commandline option `--log-level`. 

Additionally, it ignores

* product category relations that not exists 
* product images that are **NOT** available
* product images that can **NOT** be cleaned-up, e.g. the files are **NOT** available anymore
* product links (related, upsell, crosssell, etc.) for SKUs which are **NOT** available anymore
* product tier prices that can **NOT** be deleted, e. g. they are **NOT** available anymore
* product URL rewrites that can **NOT** be created
* configurable products for SKUs which are **NOT** available or available more than one time
* archive plugin can not create the archive directory, e. g. invalid permissions
* missing option values
* mission option values plugin will send a CSV file with the missing option values to the configured mail address

but logs those issues as warnings to the console.

When the debug mode is enabled, missing attribute option values will **NOT** throw an exception, instead, they will be logged and put on an internal stack. If the [MissingOptionValuesPlugin](/framework/plug-ins?#missing-option-values) has been enabled, a CSV file with the missing option values will be created in the temporary import folder. If the plugin configuration has enabled a Swift Mailer, the CSV file will be sent to the given mail addresses.

It helps developers to test imports with partially invalid CSV files which do **NOT** break data consistency.

### Running Parallel Imports

To avoid an undesirable behavior, only one import process can be started at a time. <br>
To make sure, that only one process is running, a PID file in the system's temporary directory (`sys_get_temp_dir()`) gets created which contains the UUID of the actual import process. <br>
After the import process is finished, the file will be deleted and a new process can be started.