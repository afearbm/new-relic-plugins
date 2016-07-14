# New Relic Plugin for Amazon DynamoDB

The **Blue Medora New Relic Plugin for Amazon DynamoDB** allows you to monitor your Amazon DynamoDB performance data from within the New Relic platform by pulling metrics in from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.
				
This guide includes instructions for installing and configuring the Blue Medora New Relic Plugin for Amazon DynamoDB.

----

## Obtaining the Plugin
You can find the New Relic Plugin for Amazon DynamoDB in the following locations:

- [New Relic Storefront](//TODO Update)
- [Plugin Central](//TODO Update)					

## System Requirements

The Amazon DynamoDB plugin connects to a DynamoDB Region via account access keys. Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**

- A New Relic account. (Sign up for a free account [here](http://newrelic.com).)

**Amazon DynamoDB Plugin Requirements**
- **A Blue Medora License.** A trial license will ship with the plugin. This license will remain effective for the duration of the Blue Medora beta trial period.

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
  ./npi install com.bluemedora.amazon.dynamodb
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

The second file, `plugin.template.json`, contains data specific to each plugin (e.g., a list of hosts and port combinations for what you are monitoring). Templates for both of these files should be located in the `config` directory in your extracted plugin folder.

Make a copy of this template and rename it to `plugin.json`. Shown below is an example of the `plugin.json` file’s contents.

**NOTE** You can add multiple objects to the “agents” array to monitor multiple Amazon DynamoDB instances.

**NOTE:** Each object in the "agents" array should have a unique `instance_name`.

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| polling_interval_seconds | The number of seconds between each data collection. |
| instance_name | Alias for the name of your Amazon DynamoDB instance that will appear in the User Interface |
| access_key_id | Amazon AWS Access Key ID can be found by following [this guide](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSGettingStartedGuide/AWSCredentials.html) |
| secret_access_key | Amazon AWS Secret Access Key can be found by following [this guide](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSGettingStartedGuide/AWSCredentials.html) |
| region | Amazon AWS region where DynamoDB instance resides. Acceptable values are `U_WEST_1`, `SA_EAST_1`, `AP_NORTHEAST_2`, `US_EAST_1`, `AP_NORTHEAST_1`, `CN_NORTH_1`, `EU_CENTRAL_1`, `AP_SOUTHEAST_1`, `AP_SOUTHEAST_2`, `US_WEST_2`, `GovCloud`, `US_WEST_1` |
| enable_insights | Indicates whether or not to send data to New Relic Insights for this instance. |

**NOTE:** There are optional fields if `enable_insights` is `true` that allow specific event types to be toggled whether they send data to Insights. 
Theses fields are listed below and valid values are `true` or `false`:

* `enable_insights_for_dynamo_connection`
* `enable_insights_for_table`
* `enable_insights_for_global_secondary_index`
* `enable_insights_for_local_secondary_index`
* `enable_insights_for_key_schema`

**Example:**

```
{
  "polling_interval_seconds": 60,
  "agents": [
    {
      "instance_name": "your_value_here",
      "access_key_id": "your_value_here",
      "secret_access_key": "your_value_here",
      "region": "U_WEST_1 or SA_EAST_1 or AP_NORTHEAST_2 or US_EAST_1 or AP_NORTHEAST_1 or CN_NORTH_1 or EU_CENTRAL_1 or AP_SOUTHEAST_1 or AP_SOUTHEAST_2 or US_WEST_2 or GovCloud or US_WEST_1",
      "is_dev": "your_value_here",
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
For questions or issues regarding the Amazon DynamoDB Plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Table Read Throttle Events (events) | The number of read throttle events across DynamoDB tables |
| Table Write Throttle Events (events) | The number of write throttle events across DynamoDB tables |
| Table Read Capacity Usage (%) | Percent usage of Provisioned Read capacity per DynamoDB table |
| Table Write Capacity Usage (%) | Percent usage of Provisioned Write capacity per DynamoDB table |
| Global Secondary Index Size (KB) | Size across global secondary indexes |
| Local Secondary Index Size (KB) | Size across local secondary indexes |

**Tables**

| Metric Name  |  Description |
|:------------- |:-------------|
| Read Throttle Events (events) | The number of read throttle events per DynamoDB tables |
| Write Throttle Events (events) | The number of write throttle events per DynamoDB tables |
| Read Capacity Usage (%) | Percent usage of Provisioned Read capacity per DynamoDB table |
| Write Capacity Usage (%) | Percent usage of Provisioned Write capacity per DynamoDB table |
| Read Capacity Remaining (Units) | The number of Read capacity units that are free per DynamoDB table |
| Write Capacity Remaining (Units) | The number of Write capacity units that are free per DynamoDB table |

**Global Secondary Index**

| Metric Name  |  Description |
|:------------- |:-------------|
| Size (KB) | Size per global secondary index |
| Item Count (items) | The number of items per global secondary index |
| Provisioned Read Capacity (units) | Provisioned Read Capacity per global secondary index |
| Provisioned Write Capacity (units) | Provisioned Write Capacity per global secondary index |

**Local Secondary Index**

| Metric Name  |  Description |
|:------------- |:-------------|
| Size (KB) | Size per local secondary index |
| Item Count (items) | The number of items per local secondary index |

**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| Total Size (bytes) | Total size of all tables within region |
| Total Provisioned Read Capacity (units) | Total provisioned read capacity across all tables within region |
| Total Remaining Read Capacity (units) | Total remaining read capacity across all tables within region |
| Total Provisioned Write Capacity (units) | Total provisioned write capacity across all tables within region |
| Total Remaining Write Capacity (units) | Total remaining write capacity across all tables within region |
