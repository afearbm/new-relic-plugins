# New Relic Plugin for PostgreSQL

The **Blue Medora New Relic Plugin for PostgreSQL** allows you to monitor your PostgreSQL performance data from within the New Relic platform by pulling metrics in from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.
				
This guide includes instructions for installing and configuring the Blue Medora New Relic Plugin for PostgreSQL.

----

## Obtaining the Plugin
You can find the New Relic Plugin for PostgreSQL in the following locations:

- [New Relic Storefront](//TODO Update)
- [Plugin Central](//TODO Update)					

## System Requirements

The PostgreSQL plugin connects to the supported PostgreSQL instance via a host or IP address. Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**

- A New Relic account. Sign up for a free account [here](http://newrelic.com)

**PostgreSQL Plugin Requirements**
- **A Blue Medora License.** A trial license will ship with the plugin. This license will remain effective for the duration of the Blue Medora beta trial period.
- The plugin supports **PostgreSQL Version 9.0+**

**PostgreSQL Query Tracking Prerequisites**

To track queries, these settings need to be added to the `postgresql.conf` `shared_preload_libraries` settings:

```
shared_preload_libraries = ‘pg_stat_statements' 
pg_stat_statements.max = 10000
pg_stat_statements.track = all
```
You also need to create the extension in the database. The extension is database bound and must be created for each database, even though it pulls data from each:

```
CREATE EXTENSION IF NOT EXISTS pg_stat_statements
```

You can check if it is installed on the database by calling:

```
SELECT *
FROM pg_available_extensions
WHERE name = ‘pg_stat_statements’ AND installed_version IS NOT NULL
```
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
  ./npi install com.bluemedora.Postgres
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

**NOTE:** You can add multiple objects to the “agents” array to monitor multiple PostgreSQL instances.

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| polling_interval_seconds | The number of seconds between each data collection. |
| instance_name | Alias for the name of your PostgreSQL instance that will appear in the User Interface. |
| host | IP address or hostname of PostgreSQL instance. |
| port | Port used to connect to the PostgreSQL instance. Default is `443`. |
| username | User Name for PostgreSQL login. |
| password | Password for PostgreSQL login. |
| ssl_mode | Indicates if you wish to connect while using ssl. Acceptable values are `disable`, `verify-full`, `verify-ca`, or `require`. |
| database | Optional parameter, comma separated list of databases to monitor |
| enable_insights | Indicates whether or not to send data to New Relic Insights for this instance. |

**NOTE:** There are optional fields if `enable_insights` is `true` that allow specific event types to be toggled whether they send data to Insights. 
Theses fields are listed below and valid values are `true` or `false`:

* `enable_insights_for_postgresql_query`
* `enable_insights_for_postgresql_database`
* `enable_insights_for_postgresql_function`
* `enable_insights_for_postgresql_index`
* `enable_insights_for_postgresql_tablespace`
* `enable_insights_for_postgresql_table`
* `enable_insights_for_postgresql_instance`

**Example:**

```
{
  "polling_interval_seconds": 60,
  "agents": [
    {
      "instance_name": "all_database_instance",
      "username": "your_value_here",
      "password": "your_value_here",
      "port": "your_value_here",
      "host": "your_value_here",
      "ssl_mode": "verify-full, require, disable, or verify-ca",
      "enable_insights": "true"
    },
    {
      "instance_name": "single_database_instance",
      "username": "your_value_here",
      "password": "your_value_here",
      "port": "your_value_here",
      "host": "your_value_here",
      "ssl_mode": "verify-full, require, disable, or verify-ca",
      "database": "database_1"
      "enable_insights": "true"
    },
    { 
      "instance_name": "multi_database_instance",
      "username": "your_value_here",
      "password": "your_value_here",
      "port": "your_value_here",
      "host": "your_value_here",
      "ssl_mode": "verify-full, require, disable, or verify-ca",
      "database": "database_1,database_2,database_3"
      "enable_insights": "true"
    },
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
For questions or issues regarding the PostgreSQL Plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Database Sessions (session) |  Active, Waiting, Idle in Transactions, Idle in Abort Transactions, Fastpath Function Call sessions for all databases in instance |
| Database Size (MB) |  Size across all databases |
| Query Execution Time (ms/minute) |  The time in milliseconds for every minute a query takes to execute  |
| Function Execution Time (sec/minute) |  The time in seconds for every minute a query takes to execute  |
| Table Size (MB) | Size across all tables |
| Tablespace Size (MB)  | Size across all tablesapces |

**Databases**

| Metric Name  |  Description |
|:------------- |:-------------|
| Size (MB)  |  Size across all databases  |
| Sessions (sessions)  |  Active, Waiting, Idle in Transactions, Idle in Abort Transactions, Fastpath Function Call sessions for all databases in instance  |
| Buffer Hit Ratio (%)  |  Percentage of time buffer is hit  |
| Active Connections (connections)  | Number of active connections per database |
| Deadlocks (deadlocks/minute)  | Number of deadlocks per minute across databases |

**Tables**

| Metric Name  |  Description |
|:------------- |:-------------|
| Size (MB)  | Size across tables |
| Buffer Reads (reads/sec) | Number of buffer reads per second for a table |
| Sequential Scans (scans/sec)  | Sequential scans per second for a table |
| Index Scans (scans/sec) | Index scans per second for a table |
| Index Buffer Hits (hits/sec) | Buffer hits per second for an index in a table |

**Indexes**

| Metric Name | Description |
|:------------- |:-------------|
| Scans (scans/sec) | Scans per second for an index |
| Buffer hits (hits/sec) | Buffer hits per second for an index |
| Block Reads (reads/sec) | Block reads per second for an index |

**Queries**

Only 10 queries are displayed at a time. The 10 that are displayed are the queries that have the highest number of overall calls.

| Metric Name | Description |
|:------------- |:-------------|
| Execution Time (ms/minute) | The time in milliseconds for every minute a query takes to execute |
| Average Execution Time (ms) | The average time in milliseconds a query takes to execute |
| Calls (Calls/minute) | The number of calls per minute to a query |

**Functions**

Only 20 functions are displayed at a time. The 20 that are displayed are the functions that have the highest number of overall calls.

| Metric Name | Description |
|:------------- |:-------------|
| Execution Time (ms/minute) | The time in milliseconds for every minute a function takes to execute |
| Average Execution Time (ms) | The average time in milliseconds a function takes to execute |
| Calls (calls/minute) | The number of calls per minute to a function |

**Summary**

| Metric Name | Description |
|:------------- |:-------------|
| Total Database Size (bytes) | The sum of the size of all databases being monitored |
| Active Sessions (sessions) | The sum of active sessions across all databases being monitored |
| Waiting Sessions (sessions) | The sum of waiting sessions across all databases being monitored |
| Total Deadlocks (deadlocks/minute) | The sum of deadlocks across all databases being monitored |
