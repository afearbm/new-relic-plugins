# Blue Medora Nimble Storage Plugin for New Relic

The **Blue Medora Nimble Storage Plugin for New Relic** allows you to monitor your Nimble performance data from within the New Relic platform by pulling in metrics from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.           

This guide includes instructions for installing and configuring the Blue Medora Nimble Storage Plugin for New Relic.

----


## Obtaining the Plugin
You can find the New Relic Nimble Storage plugin in the following locations:

- [New Relic Storefront](http://newrelic.com/plugins/blue-medora/459)
- [Plugin Central](https://rpm.newrelic.com/accounts/890835/plugins/directory/459)

## System Requirements

The Nimble Storage plugin connects to the Nimble hardware using REST and optionally SNMP v2 for additional group metrics.  Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**
- A New Relic account

**Nimble Storage Plugin Requirements**
- The plugin supports **Nimble 2.3+** through REST
- **SNMP v2** can be used for additional group metrics 
- **A Blue Medora License.** A trial license will ship with the plugin. This license will remain effective for the duration of the Blue Medora beta trial period.


----

## Installing the Plugin

This plugin can be installed one of the following ways:

- Using the New Relic Platform Installer
- Installing the Plugin Manually

### Using the New Relic Platform Installer

The New Relic Platform Installer (NPI) is a command line tool that helps you easily download, configure and manage New Relic Platform Plugins.  For more information, visit the [Getting Started with the Platform Installer section of New Relic’s Community Forum](https://discuss.newrelic.com/t/getting-started-with-the-platform-installer/842).


**NOTE:** We recommend using the New Relic Platform Installer for installing and running your Blue Medora plugins for New Relic. Issues can arise by running a plugin directly in the foreground (e.g., when the machine reboots, the process will not be started again). The NPI automates much of these processes.


Once the NPI tool has been installed, run the following command:

```
  ./npi install com.bluemedora.nimble.storage
``` 

**Note:** This command will take care of the creation of `newrelic.json` and `plugin.json` files described in the [Configuring the Plugin](#Configuring-the-Plugin) section.

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
    "license_key": "YOUR LICENSE KEY",
    "log_level": "info",
    "log_file_name": "newrelic_plugin.log",
    "log_file_path": "logs",
    "insights_api_key": "YOUR INSIGHTS API KEY",
    "insights_account_id": "YOUR INSIGHTS ACCOUNT ID",
    "insights_use_ssl": true
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

#### Configuring the `plugin.template.json` file: 

The second file, `plugin.template.json`, contains data specific to each plugin (e.g., a list of hosts and port combinations for what you are monitoring). Templates for both of these files should be located in the ‘config’ directory in your extracted plugin folder.

Make a copy of this template and rename it to `plugin.json`. Shown below is an example of the `plugin.json` file’s contents.

**NOTE:** You can add multiple objects to the “agents” array to monitor multiple Nimble Storage instances. 

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| polling_interval_seconds | The number of seconds between each data collection. |
| instance_name | The name of your New Relic Nimble Storage instance that will appear in the User Interface. |
| username | Username to make REST calls to your Nimble Storage device. |
| password | Password to make REST calls to your Nimble Storage device. |
| hostname | IP address or fully qualified host name of your Nimble Storage device. |
| rest_port | Optional: if not specified, default port '5392' will be used. |
| community_string | Optional: community string required to connect to SNMP (not including disables SNMP metrics). |
| snmp_port | Optional: if not specified and 'community_string' is specified, default port '161' will be used. |
| enable_insights | Indicates whether or not to send data to New Relic Insights for this instance. |

**NOTE:** There are optional fields if `enable_insights` is `true` that allow specific event types to be toggled whether they send data to Insights. 
Theses fields are listed below and valid values are `true` or `false`:

* `enable_insights_for_group`
* `enable_insights_for_array`
* `enable_insights_for_volume`
* `enable_insights_for_volume_collection`
* `enable_insights_for_folder`
* `enable_insights_for_pool`
* `enable_insights_for_protection_schedule`
* `enable_insights_for_performance_policy`
* `enable_insights_for_replication`

**Example:**

```
{
  "polling_interval_seconds": "60",
  "agents": [
    {
      "instance_name": "Your SNMP Disabled Instance",
      "username": "your_value_here",
      "password": "your_value_here",
      "hostname": "your_value_here",
      "enable_insights": "true"
    },
    {
      "instance_name": "Your SNMP Enabled Instance",
      "username": "your_value_here",
      "password": "your_value_here",
      "hostname": "your_value_here",
      "community_string": "your_value_here",
      "enable_insights": "true"
    },
    {
      "instance_name": "Your SNMP Enabled Custom Port Instance",
      "username": "your_value_here",
      "password": "your_value_here",
      "hostname": "your_value_here",
      "rest_port": "your_value_here",
      "community_string": "your_value_here",
      "snmp_port": "your_value_here",
      "enable_insights": "true"
    }
  ]
}
```

## Using the Plugin
For more information about navigating New Relic’s user interface, refer to their [Using a plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/using-plugins/using-plugin) section.

## Support Resources
For questions or issues regarding the Blue Medora Nimble Storage Plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**Group Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Capacity (TB) |  The free, usable, used, and unused reserve capacity of the Nimble group |
| Disk Usage (GB)  | The disk used in the Nimble group by volumes and snapshots |
| Latency (ms)  | The read and write latency of the Nimble group |
| Cache Hit (%)  | The percentage of non-sequential read cache hits to non-sequential reads for the Nimble group  |
| Throughput (MB/sec)  | The read and write throughput for the Nimble group |
| IOPS (ops/sec)  | The read and write IOPs for the Nimble group |

**Pools**

| Metric Name  |  Description |
|:------------- |:-------------|
| Capacity (TB) |  The total, free, and used capacity of each Nimble pool |
| Associated Objects  | The number of child objects on each Nimble pool |
| Cache Capacity (TB)  | The pinable, pinned, and total cache capacity of each Nimble pool |

**Folders**

| Metric Name  |  Description |
|:------------- |:-------------|
| Capacity (TB) | The free, used, and unused reserve capacity of each Nimble folder |
| Compression Ratios  | The snapshot, volume, and folder compression ratio for each Nimble folder |
| Volume Usage (GB)  | The space used by volumes for each Nimble folder |
| Snapshot Usage (GB) | The space used by snapshots for each Nimble folder|
| Snapshots | The number of snapshots on each Nimble folder |

**Volumes**

| Metric Name  |  Description |
|:------------- |:-------------|
| Capacity (TB) | The total, free, and usable capacity of each Nimble volume |
| Compression Ratio | The compression ratio of folders and volume for each Nimble volume |
| Pinned Cache Capacity (GB)  | The pinned cache capacity of each Nimble volume |
| Snapshot Capacity (GB) | The snapshot capacity of each Nimble volume |
| Connections | The number of connections on each Nimble volume |
| Snapshots | The number of snapshots on each Nimble volume |

**Arrays**

| Metric Name  |  Description |
|:------------- |:-------------|
| Capacity (TB) | The free, usable, and used capacity of each Nimble array |
| Compression Ratio  | The compression ratio of volumes and snapshots on each Nimble array |
| Usage (TB)  | The space used by volumes and snapshots for each Nimble array |
| Space Saved (TB) | The space saved by volumes and snapshots for each Nimble array |

**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| Cache Hit (%) | The cache hit percent of the Nimble group |
| Read Latency (ms)  | The average read latency of the Nimble group |
| Write Latency (ms)  | The average write latency Nimble group |
| Read Throghput (bytes/sec) | The read throughput of the Nimble group |
| Write Throghput (bytes/sec) | The write throughput of the Nimble group |
