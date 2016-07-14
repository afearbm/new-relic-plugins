# New Relic Plugin for MS SQL

The **Blue Medora New Relic Plugin for MS SQL** allows you to monitor your Microsoft SQL performance data from within the New Relic platform by pulling metrics in from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.
				
This guide includes instructions for installing and configuring the Blue Medora New Relic Plugin for MS SQL.

----

## Obtaining the Plugin
You can find the New Relic Plugin for MS SQL in the following locations:

- [New Relic Storefront](//TODO Update)
- [Plugin Central](//TODO Update)					

## System Requirements

The MS SQL plugin connects to a MS SQL database via JDBC connection. Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**

- A New Relic account (Sign up for a free account [here](http://newrelic.com).)

**MS SQL Plugin Requirements**
- **MS SQL versions.** The plugin is compatible with MS SQL 2008 R2, 2012, & 2014.
- **A Blue Medora License.** A trial license will ship with the plugin that is valid until September 13,  2016. To obtain a production license or get pricing information for the plugin, contact sales@bluemedora.com.

----

## Installing the Plugin

This plugin can be installed one of the following ways:

- Using the New Relic Platform Installer
- Installing the Plugin Manually

### Using the New Relic Platform Installer

The New Relic Platform Installer (NPI) is a command line tool that helps you easily download, configure and manage New Relic Platform Plugins.  For more information, refer to the [Installing an NPI-compatible plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/installing-plugins/installing-npi-compatible-plugin).

**NOTE:** We recommend using the New Relic Platform Installer for installing and running your Blue Medora plugins for New Relic. Issues can arise by running a plugin directly in the foreground (e.g., when the machine reboots, the process will not be started again). The NPI automates much of these processes.

Once the NPI tool has been installed, run the following command:

```
  ./npi install com.bluemedora.microsoft.sqlserver
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

### Configuring the `plugin.template.json` file: 

The second file, `plugin.template.json`, contains data specific to each plugin (e.g., a list of hosts and port combinations for what you are monitoring). Templates for both of these files should be located in the ‘config’ directory in your extracted plugin folder.

Make a copy of this template and rename it to `plugin.json`. Shown below is an example of the `plugin.json` file’s contents.

**NOTE:** You can add multiple objects to the “agents” array to monitor multiple Microsoft SQL Server instances.

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| polling_interval_seconds | The number of seconds between each data collection |
| instance_name | Alias for the name of your Microsoft SQL Server instance that will appear in the user interface |
| username | User name to log into Microsoft SQL Server |
| password | Password to log into Microsoft SQL Server |
| host | The hostname or IP address of Microsoft SQL Server |
| port | Port to connect to Microsoft SQL Server |
| instance | Microsoft SQL Server instance to monitor |
| enable_insights | Indicates whether or not to send data to New Relic Insights for this instance |

**NOTE:** There are optional fields if `enable_insights` is `true` that allow specific event types to be toggled whether they send data to Insights. 
Theses fields are listed below and valid values are `true` or `false`:

* `enable_insights_for_MsSqlQuery`
* `enable_insights_for_MsSqlDatabase`
* `enable_insights_for_MsSqlInstance`

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
      "instance": "your_value_here",
      "enable_insights": "true"
    }
  ]
}
```

## Using the Plugin
For more information about navigating New Relic’s user interface, refer to their [Using a plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/using-plugins/using-plugin) section.

## Troubleshooting/Known Issues
When running a plugin, a `java.lang.OutOfMemoryError` may occur if too much data is being processed for the system to handle. If that issues arises, you will need to modify the `java_args` field of the “master” `newrelic.json` file located in the npi base `config` directory.

`java_args` - `-Xmx128m` (-Xmxn specifies the maximum size, in bytes, of the memory allocation pool. This value must a multiple of 1024 greater than 2 MB. Append the letter k or K to indicate kilobytes, or m or M to indicate megabytes. The default value is chosen at runtime based on system configuration.)

**Examples:**

`-Xmx83886080`

`-Xmx81920k`

`-Xmx80m`

## Support Resources
For questions or issues regarding the MS SQL Plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Instance Buffer Pool Hit (%) | The percentage of buffer pools hits on the instance |
| Total Wait Time Across Instance (ms/sec) | The number of milliseconds spent ever second waiting across the instance |
| Database Top 10 Read Throughput (MB/sec) | The 10 databases with the highest read throughput |
| Database Top 10 Write Throughput (MB/sec) | The 10 databases with the highest write throughput |
| Database Top 10 Read IOPS (operations/sec) | The 10 databases with the highest read IOPS |
| Database Top 10 Write IOPS (operations/sec) | The 10 databases with the highest write IOPS |
| Query Top 10 Execution Time (sec/minute) | The 10 queries that spent the most seconds per every minute executing |
| Query Top 10 Execution Count (executions/minute) | The 10 queries with the highest number of executions per minute |

**Instance**

| Metric Name  |  Description |
|:------------- |:-------------|
| Compilations (compilations/sec) | The number of compilations per second on the instance |
| Recompilations (compilations/sec) | The number of re-compilations per second on the instance |
| Processes (processes) | The number of `Preconnect`, `Background`, `Dormant`, `Runnable`, `Suspended`, `Running`, `Blocked`, and `Sleeping` processes on the instance |
| Total Wait Time Across Instance (ms/sec) | The number of milliseconds spent ever second waiting across the instance |
| Runnable Tasks (tasks) | The number of runnable tasks on the instance |
| Transactions (transactions/sec) | The number of transactions per second on the instance |
| Page Splits (splits/sec) | The number of page splits per second on the instance |
| Forced Parameterizations (parameterizations/sec) | The number of forced parameterizations per second on the instance |

**Instance Buffer Pool**

| Metric Name  |  Description |
|:------------- |:-------------|
| Hit (%) | The percentage of hits on the buffer pool |
| Size (GB) | The size of the buffer pool |
| Page Life Expectancy (sec) | The life expectancy of a page in the buffer pool |
| Batch Requests (requests/sec) | The number of batch requests per second on the buffer pool |

**Databases**

| Metric Name  |  Description |
|:------------- |:-------------|
| Buffer Pool Size (GB) | The size of the buffer pool per database |
| Read Delay (ms/sec) | The number of milliseconds per second that a read is delayed per database |
| Write Delay (ms/sec) | The number of milliseconds per second that a write is delayed per database |
| Read Throughput (MB/sec) | The read throughput per database |
| Write Throughput (MB/sec) | The write throughput per database |
| Read IOPS (operations/sec) | The number of read operations per second on a database |
| Write IOPS (operations/sec) | The number of write operations per second on a database |

**Queries**

| Metric Name  |  Description |
|:------------- |:-------------|
| Top 10 Execution Time (sec/minute) | The 10 queries that spent the most seconds per every minute executing |
| Top 10 Average Execution Time (ms) | The 10 queries with the highest execution time on average |
| Top 10 Execution Count (executions/minute) | The 10 queries with the highest number of executions per minute |

**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| Buffer Pool Hit (%) | The percentage of buffer pools hits on the instance |
| Compilations (compilations/sec) | The number of compilations per second on the instance |
| Recompilations (compilations/sec) | The number of re-compilations per second on the instance |
| Total IOPS (operations/sec) | The total number of IOPS across the instance |
| Total Throughput (bytes/sec) | The total throughput across the instance |
