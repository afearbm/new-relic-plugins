# New Relic Plugin for MySQL

The **Blue Medora New Relic Plugin for MySQL** allows you to monitor your MySQL performance data from within the New Relic platform by pulling metrics in from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.
				
This guide includes instructions for installing and configuring the Blue Medora New Relic Plugin for MySQL.

----

## Obtaining the Plugin
You can find the New Relic Plugin for MySQL in the following locations:

- [New Relic Storefront](//TODO Update)
- [Plugin Central](//TODO Update)					

## System Requirements

The MySQL plugin connects to a supported MySQL Instance via a hostname or IP address. Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**

- A New Relic account. Sign up for a free account [here](http://newrelic.com)

**MySQL Plugin Requirements**
- The plugin supports **MySQL Versions 5.6 and 5.7** and **MariaDB Versions 10.0 and 10.1**?
- **A Blue Medora License.** A trial license will ship with the plugin. This license will remain effective for the duration of the Blue Medora beta trial period.

----

## Installing the Plugin

This plugin can be installed one of the following ways:

- Using the New Relic Platform Installer
- Installing the Plugin Manually

### Using the New Relic Platform Installer

The New Relic Platform Installer (NPI) is a command line tool that helps you easily download, configure and manage New Relic Platform Plugins.  For more information, visit the [Getting Started with the Platform Installer section of New Relic’s Community Forum](https://discuss.newrelic.com/t/getting-started-with-the-platform-installer/842).

**NOTE:** We recommend using the New Relic Platform Installer for installing and running your Blue Medora plugins for New Relic. Issues can arise by running a plugin directly in the foreground (e.g., when the machine reboots, the process will not be started again). The NPI automates much of these processes.

Once the NPI tool has been installed, run the following command:

```
  ./npi install com.bluemedora.mysql
``` 

**NOTE:** This command will take care of the creation of `newrelic.json` and `plugin.json` files described in the [Configuring the Plugin](#Configuring-the-Plugin) section.

### Installing the Plugin Manually

#### Downloading and Extracting the Plugin

The latest version of the plugin can be downloaded from the locations listed in the [Obtaining the Plugin](#obtaining-the-plugin) section.  Once the plugin is on your box, extract it to your preferred directory location.

#### Configuring the Plugin

Refer to the [Configuring the Plugin](#Configuring-the-Plugin) section, for details on setting up the plugin. 

#### Running the Plugin

To run the plugin, navigate to the directory where the plugin was extracted, then execute the following command from your terminal or command line window:

```
  java -jar plugin.jar
```

----     
## Configuring the Plugin
From the extracted plugin folder you receive when downloading your plugin, you will find the following files: 

```
  plugin.jar
  eula.txt
  oss_attribution.txt
  [config folder]
    newrelic.template.json
    plugin.template.json 
    plugin_license.json
```

The "template" .json files found in the config folder must be modified (i.e., customized) and renamed prior to setting up the plugin for monitoring.

### Configuring the `newrelic.template.json` file

The first file, `newrelic.template.json`, contains configurations used by all Platform plugins (e.g., license key, logging information, proxy settings) and can be shared across your plugins.
Make a copy of this template and rename it to `newrelic.json`. Listed below are the configurable fields within the `newrelic.json` file:

**New Relic License Key** - The only required field in the newrelic.json file is the License Key. This unique identifier informs New Relic about the specific account tied to the plugin. For more information on the License Key, [refer to the New Relic License key documentation](https://docs.newrelic.com/docs/accounts-partnerships/accounts/account-setup/license-key).

```
{
   “license_key”: “YOUR_LICENSE_KEY_HERE”
}
```

**Logging Configuration** - By default Platform plugins will have their logging turned on; however, you can modify these settings with the following configurations:

`log\_level` - The log level. Valid values: [debug, info, warn, error, fatal]. Defaults to info.

`log\_file\_name` - The log file name. Defaults to newrelic_plugin.log.

`log\_file\_path` - The log file path. Defaults to logs.

`log\_limit\_in\_kbytes` - The log file limit in kilobytes. Defaults to 25600 (25 MB). If limit is set to 0, the log file size would not be limited.

```
{
  "license_key": "YOUR_LICENSE_KEY_HERE",
  "log_level": "debug",

  "log_file_path": "log_file_path": “logs"
}
```

**Proxy Configuration** - If you are running your plugin from a machine that runs outbound traffic through a proxy, you can use the following optional configurations in your newrelic.json file:

`proxy\_host` - The proxy host (e.g. webcache.example.com)

`proxy\_port` - The proxy port (e.g. 8080). Defaults to 80 if a proxy_host is set

`proxy\_username` - The proxy username

`proxy\_password` - The proxy password


**Example:**

```
{
  "license_key": "YOUR_LICENSE_KEY_HERE",

  "proxy_host": "proxy.mycompany.com",

  "proxy_port": 9000
}
```

#### Configuring the plugin.template.json file: 

The second file, plugin.template.json, contains data specific to each plugin (e.g., a list of hosts and port combinations for what you are monitoring). Templates for both of these files should be located in the ‘config’ directory in your extracted plugin folder.

Make a copy of this template and rename it to plugin.json. Shown below is an example of the plugin.json file’s contents.

**NOTE** - You can add multiple objects to the “agents” array to monitor multiple MySQL instances.

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| instance_name | Alias for the name of your MySQL instance that will appear in the User Interface |
| host | IP address or hostname of MySQL Management IP |
| port | Port used to connect to the MySQL Instance. Default is `5432` |
| username | User Name for MySQL login. |
| password | Password for MySQL login. |
| ssl_enabled | Signals if SSL should be used. Acceptable values are `true` or `false`. |
| ssl_cert_path | Only applicable if `ssl_enabled` is `true`. Path to the SSL certificate on system. |

**Example:**

```
{
  "agents": [
    {
      "instance_name": "YOUR_VALUE_HERE",
      "username": "YOUR_VALUE_HERE",
      "password": "YOUR_VALUE_HERE",
      "host": "YOUR_VALUE_HERE",
      "port": "YOUR_VALUE_HERE",
      "ssl_enabled": false
    },
    {
      "instance_name": "YOUR_VALUE_HERE",
      "username": "YOUR_VALUE_HERE",
      "password": "YOUR_VALUE_HERE",
      "host": "YOUR_VALUE_HERE",
      "port": "YOUR_VALUE_HERE",
      "ssl_enabled": true
      "ssl_cert_path" : "PATH_TO_CRT_FILE"
    }
  ]
}
```

## Using the Plugin
For more information about navigating New Relic’s user interface, refer to their [Using a plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/using-plugins/using-plugin) section.

## Support Resources
For questions or issues regarding the MySQL Plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Instance Size (GB) |  The size allocated to MySQL instance |
| Instance Connections (connection/minute) |  Rate at which connections are made to MySQL instance |
| Database IO Read (MB/sec) |  Read IO across databases  |
| Database IO Write (MB/sec) |  Write IO across databases  |
| Database Size (MB) |  Size of databases in instance  |
| Tablespace Size (GB) |  Size of tablespaces in instance  |

**Instance**

| Metric Name  |  Description |
|:------------- |:-------------|
| Size (GB) |  The size allocated to MySQL instance |
| Connections (connection/minute) |  Rate at which connections are made to MySQL instance |
| Data Reads (reads/sec) |  Read rate of MySQL instance |
| Data Writes (writes/sec) |  Write rate of MySQL instance |
| Deadlocks (deadlocks/minute) |  Deadlock rate of MySQL instance |
| OS Waits (waits/minute) |  The number of OS waits per minute of MySQL instance |
| Spin Waits (waits/minute) |  The number of spin waits per minute of MySQL instance |
| Waits (waits/minute) |  The number of non-os and non-spin waits per minute of MySQL instance |

**Tablespaces**

| Metric Name  |  Description |
|:------------- |:-------------|
| Size (GB) |  Size of tablespaces  |
| Max File Size (GB) |  Maximum file size of tablespaces  |

**Databases**

| Metric Name  |  Description |
|:------------- |:-------------|
| Size (MB) | Size of databases |
| Data Size (MB) | Size of the data stored in databases |
| Index Size (MB) | Size of the indexes stored in databases |
| Wait Time (sec/minute) | How many seconds for every minute a database is spent waiting |
| IO Read (MB/sec) | Database read rate |
| IO Write (MB/sec) | Database write rate |
| IO Read Latency (ms) | Database read Latency |
| IO Write Latency (ms) | Database write Latency |

**Tables**

| Metric Name  |  Description |
|:------------- |:-------------|
| Top 10 Size (MB) | The 10 tables with the largest size |
| Top 10 Wait Time (ms) | The 10 tables with the largest wait time |
| Top 10 IO Read (KB/sec) | The 10 tables with the largest read rate |
| Top 10 IO Write (KB/sec) | The 10 tables with the largest write rate |
| Top 10 IO Read Latency (ms) | The 10 tables with the largest read Latency |
| Top 10 IO Write Latency (ms) | The 10 tables with the largest write Latency |

**Indexes**

| Metric Name  |  Description |
|:------------- |:-------------|
| Row Operations (rows/minute) | The number of operations done on rows per minute by an index |
| Latency (ms) | Latency of an index |

**Queries**

Only 10 queries are displayed at a time. The 10 that are displayed are the queries that have the highest number of overall calls.

| Metric Name  |  Description |
|:------------- |:-------------|
| Execution Time (ms) | Time that a specific query takes to execute |
| Average Execution Time (ms) | Average time that a specific query takes to execute |
| Calls (calls) | Number of overall calls made to a query |
