# New Relic Plugin for Oracle Database

The **Blue Medora New Relic Plugin for Oracle Database** allows you to monitor your Oracle Database performance data from within the New Relic platform by pulling metrics in from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.
				
This guide includes instructions for installing and configuring the Blue Medora New Relic Plugin for Oracle Database.

----

## Obtaining the Plugin
You can find the New Relic Plugin for Oracle Database in the following locations:

- [New Relic Storefront](//TODO Update)
- [Plugin Central](//TODO Update)					

## System Requirements

The Oracle Database plugin connects to a single Oracle Database Instance via a host or IP address. Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**

- A New Relic account. Sign up for a free account [here](http://newrelic.com)

**Oracle Database Plugin Requirements**
- The plugin supports **Oracle Database Version 11.2+**
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
  ./npi install com.bluemedora.oracle.database
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
Make a copy of this template and rename it to `newrelic.json`. Listed below are the configurable fields within the newrelic.json file:

**New Relic License Key** - The only required field in the `newrelic.json` file is the License Key. This unique identifier informs New Relic about the specific account tied to the plugin. For more information on the License Key, [refer to the New Relic License key documentation](https://docs.newrelic.com/docs/accounts-partnerships/accounts/account-setup/license-key).

```
{
   “license_key”: “YOUR_LICENSE_KEY_HERE”
}
```

**Insights Configuration** - Blue Medora plugins support reporting events to New Relic Insights. In order to achieve this you need to supply your `insights_api_key` and `insights_account_id`. For more information on where to find these fields, [refer to the New Relic Insights documentation]
(https://docs.newrelic.com/docs/insights/new-relic-insights/adding-querying-data/insert-custom-events-insights-api#register). Below are the fields needed to configure Insights access.

`insights_api_key` - The api key associated with your Insights account.

`insights_account_id` - The ID associated with your Insights account.

`insights_use_ssl` - Signals whether to connect to Insights via SSL. Acceptable values are `true` or `false`.


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

**Proxy Configuration** - If you are running your plugin from a machine that runs outbound traffic through a proxy, you can use the following optional configurations in your `newrelic.json` file:

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

**NOTE** - You can add multiple objects to the “agents” array to monitor multiple Oracle Database instances.

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| polling_interval_seconds | The number of seconds between each data collection. |
| instance_name | Alias for the name of your Oracle Database instance that will appear in the User Interface |
| host | IP address or hostname of Oracle Database instance. |
| port | Port used to connect to the Oracle Database instance. Default is `1521` for when `ssl_enabled` is `false`, and `2484` for when `ssl_enabled` is `true`. |
| username | User Name for Oracle Database login. |
| password | Password for Oracle Database login. |
| ssl_enabled | Indicates if you wish to connect while using ssl. Acceptable values are `true` and `false`. |
| sid | Optional parameter, SID or Service Name. |
| truststore_path | Optional parameter, only used if `ssl_enabled` is `true`. Location of truststore. |
| truststore_password | Optional parameter, only used if `ssl_enabled` is `true`. Password for the truststore. |
| enable_insights | Indicates whether or not to send data to New Relic Insights for this instance. |

**Example:**

```
{
  "polling_interval_seconds": 60,
  "agents": [
    {
      "instance_name": "your_value_here",
      "username": "your_value_here",
      "password": "your_value_here",
      "host": "your_value_here",
      "port": "your_value_here",
      "ssl_enable": "your_value_here",
      "enable_insights": "true"
    },
    {
      "instance_name": "your_trust_store_instance",
      "username": "your_value_here",
      "password": "your_value_here",
      "host": "your_value_here",
      "port": "your_value_here",
      "ssl_enable": "your_value_here",
      "truststore_path": "path_to_truststore",
      "truststore_password": "password_for_truststore",
      "enable_insights": "true"
    },
    {
      "instance_name": "your_sid_instance",
      "username": "your_value_here",
      "password": "your_value_here",
      "host": "your_value_here",
      "port": "your_value_here",
      "sid": "your_value_here",
      "ssl_enable": "your_value_here",
      "truststore_path": "path_to_truststore",
      "truststore_password": "password_for_truststore",
      "enable_insights": "true"
    }
  ]
}
```

## Using the Plugin
For more information about navigating New Relic’s user interface, refer to their [Using a plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/using-plugins/using-plugin) section.

## Support Resources
For questions or issues regarding the Oracle Database Plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Instance Waits (waits/sec) |  The number of waits per second on the instance |
| Instance Sessions (sessions) |  Inactive, Killed, No Wait, Sniped, Unblocked, Waiting, Active, Blocked, and Cached sessions on the instance |
| Instance CPU Usage (centiseconds/sec) | CPU usage of an instance |
| Tablespace Size (GB) | Size across tablespaces |
| Query Execution Time (sec/minute) | The number of seconds every minute that a query takes to execute |
| Query Wait Time (ms/minute) | The number of milliseconds every minute that a query spends waiting |
| Database File Size (GB) | File size across databases of an instance |
| Control File Size (MB) | Size of control files across an instance |

**Instance**

| Metric Name  |  Description |
|:------------- |:-------------|
| Waits (waits/sec) |  The number of waits per second on the instance |
| Time Waited (sec/minute) | The number of seconds every minute that the instance is spent waiting  |
| CPU Usage (centiseconds/sec) | CPU usage of an instance |
| Sessions (sessions) |  Inactive, Killed, No Wait, Sniped, Unblocked, Waiting, Active, Blocked, and Cached sessions on the instance |
| Cache Hit Ratio (%) | The hit precentage of instance on the cache  |
| Cache Size (GB) | The size of the instance cache  |
| Read Time (ms/minute) | The number of milliseconds every minute that the instance spends reading |
| Write Time (ms/minute) | The number of milliseconds every minute that the instance spends writing |

**Tablespaces**

| Metric Name  |  Description |
|:------------- |:-------------|
| Size (GB)  | Size across tablespaces |
| Free Space (GB)  | Free Space available across tablespaces |

**Database Files**

| Metric Name  |  Description |
|:------------- |:-------------|
| Size (GB)  | Size across database files |
| Max Size (GB)  | Max size allowed across database files |
| IO Time (ms)  | Time taken for IO operations |
| Read Time (ms/minute) | The number of milliseconds every minute across database files spent reading |
| Write Time (ms/minute) | The number of milliseconds every minute across database files spent writing |
| Reads (reads/sec) | The read rate across database files |
| Write (writes/sec) | The write rate across database files |

**Queries**

Only 10 queries are displayed at a time. The 10 that are displayed are the queries that have the highest number of overall executions.

| Metric Name  |  Description |
|:------------- |:-------------|
| Execution Time (sec/minute)  | The number of seconds every minute that a query takes to execute |
| Wait Time (ms/minute)  | The number of milliseconds every minute that a query spends waiting |
| CPU Time (ms/minute)  | The number of milliseconds every minute that a query spends on the cpu |
| Memory (KB/sec)  | The memory usage every second of a query |
| Executions (executions/sec)  | The number of executions per second of a query |

**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| Waits (waits/sec)  | The total number of waits per second across all instances  |
| Average CPU Usage (centiseconds/sec)  | The average CPU usage across all instaces  |
| Active Sessions (sessions)  | The total number of active sessions across all instaces  |