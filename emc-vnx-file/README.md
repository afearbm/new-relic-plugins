# Blue Medora EMC VNX File Plugin for New Relic

The **Blue Medora EMC VNX File Plugin for New Relic** allows you to monitor your VNX File performance data from within the New Relic platform by pulling in metrics from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.           

This guide includes instructions for installing and configuring the Blue Medora EMC VNX File Plugin for New Relic.
If you’re having a bad experience with one of our plugins, please get in touch and we’ll be happy to help you out.

----

## System Requirements

The VNX File plugin uses the SSH protocol to connect to the VNX File Control Station.  Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**

- A New Relic account (Sign up for a free account [here](http://newrelic.com).)

**EMC VNX File Plugin Requirements**

- **File Software Version:** The plugin requires VNX File Software Version 8.1.3
- **Permissions:** The plugin requires permissions to run Control Station commands
- Java 1.7 or higher
- **A Blue Medora License.** A trial license will ship with the plugin that is valid for 14 days. To obtain a production license or get pricing information for the plugin, please contact sales@bluemedora.com.

----

## Installing the Plugin

We recommend using the New Relic Platform Installer for installing and running your Blue Medora plugins for New Relic.

The New Relic Platform Installer (NPI) is a command line tool that helps you easily download, configure, and manage New Relic Platform Plugins.  For more information, refer to the [Installing an NPI-compatible plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/installing-plugins/installing-npi-compatible-plugin).

Once the NPI tool has been installed, run the following command:

```
  ./npi install com.bluemedora.emc.vnx.file
``` 

**Note:** This command will take care of the creation of `newrelic.json` and `plugin.json` files described in the [Configuring the Plugin](#Configuring-the-Plugin) section.

###### [Download Plugin for Manual Installation](https://newrelic-bluemedora.s3.amazonaws.com/com-bluemedora-emc-vnxfile/newrelic_emc_vnx_file_plugin-3.0.0_20161207_155542.tar.gz)

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

**Example:**

```
{
   “license_key”: “YOUR LICENSE KEY”
}
```

**Insights Configuration** - Blue Medora plugins support reporting events to New Relic Insights. 
In order to achieve this you need to supply your `insights_api_key` and `insights_account_id`. 
You can find these fields in on [your New Relic API Keys page](https://rpm.newrelic.com/apikeys). 
For more information, [refer to the New Relic Insights documentation](https://docs.newrelic.com/docs/insights/new-relic-insights/adding-querying-data/insert-custom-events-insights-api#register).

Below are the fields needed to configure Insights access.

`insights_api_key` - The api key associated with your Insights account.

`insights_account_id` - The ID associated with your Insights account.

`insights_use_ssl` - Signals whether to connect to Insights via SSL. Acceptable values are `true` or `false`.

**Example:**

```
{
    "license_key": "YOUR LICENSE KEY",
    "insights_api_key": "YOUR INSIGHTS API KEY",
    "insights_account_id": "YOUR INSIGHTS ACCOUNT ID",
    "insights_use_ssl": true
}
```

**Logging Configuration** - By default Platform plugins will have their logging turned on; however, you can modify these settings with the following configurations:

`log_level` - The log level. Valid values: [debug, info, warn, error, fatal]. Defaults to info.

`log_file_name` - The log file name. Defaults to newrelic_plugin.log.

`log_file_path` - The log file path. Defaults to logs.

`log_limit_in_kbytes` - The log file limit in kilobytes. Defaults to 25600 (25 MB). If limit is set to 0, the log file size would not be limited.

**Example:**

```
{
    "license_key": "YOUR LICENSE KEY",
    "log_level": "info",
    "log_file_name": "newrelic_plugin.log",
    "log_file_path": "logs",
}
```

**Proxy Configuration** - If you are running your plugin from a machine that runs outbound traffic through a proxy, you can use the following optional configurations in your `newrelic.json` file:

`proxy_host` - The proxy host (e.g. webcache.example.com)

`proxy_port` - The proxy port (e.g. 8080). Defaults to 80 if a proxy_host is set

`proxy_username` - The proxy username

`proxy_password` - The proxy password

**Example:**

```
{
  "license_key": "YOUR LICENSE KEY",
  "proxy_host": "proxy.mycompany.com",
  "proxy_port": 9000
}
```

### Configuring the `plugin.template.json` file: 

The second file, `plugin.template.json`, contains data specific to each plugin (e.g., a list of hosts and port combinations for what you are monitoring). Templates for both of these files should be located in the ‘config’ directory in your extracted plugin folder.

Make a copy of this template and rename it to `plugin.json`. Shown below is an example of the `plugin.json` file’s contents.

**NOTE:** You can add multiple objects to the “agents” array to monitor multiple VNX File instances.

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| polling_interval_seconds | The number of seconds between each data collection. |
| instance_name | The name of your New Relic VNX File instance that will appear in the User Interface |
| primary_control_station  | The IP or domain name of the Primary Control Station for VNX File |
| secondary_control_station | The IP or domain name of the Secondary Control Station for VNX File |
| username | User name to log into the Control Stations |
| password | Password to log into the Control Stations |
| nas_path | Path to the NAS_DB configuration files location.  Usually `/nas`. |
| sample_seconds | Query interval parameter.  Must be an integer 1 or greater |
| send_to_plugin | Indicates whether or not to send data to New Relic Plugins. See [Blue Medora's New Relic Knobs and Levers Readme](https://github.com/BlueMedora/new-relic-plugins/blob/master/configuration-variants/readme.md) for more details |
| send_to_insights | Indicates whether or not to send data to New Relic Insights. See [Blue Medora's New Relic Knobs and Levers Readme](https://github.com/BlueMedora/new-relic-plugins/blob/master/configuration-variants/readme.md) for more details |

## Using the Plugin
For more information about navigating New Relic’s user interface, refer to their [Using a plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/using-plugins/using-plugin) section.

## Troubleshooting/Known Issues
When running a plugin, a `java.lang.OutOfMemoryError` may occur if too much data is being processed for the system to handle. If that issues arises, you will need to modify the `java_args` field of the “master” `newrelic.json` file located in the npi base `config` directory.

`java_args` - `-Xmx128m` (-Xmxn specifies the maximum size, in bytes, of the memory allocation pool. This value must a multiple of 1024 greater than 2 MB. Append the letter k or K to indicate kilobytes, or m or M to indicate megabytes. The default value is chosen at runtime based on system configuration.)

**Examples:**

`-Xmx83886080`

`-Xmx81920k`

`-Xmx80m`

----

## Support Resources
For questions or issues regarding the Blue Medora EMC VNX File Plugin for New Relic, visit http://support.bluemedora.com. 

----

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Latency (ms) |The latency of the system in milliseconds |
| Network Throughput (MBps) | The network throughput of the system in megabytes per second |
| Disk IOPS (ops/sec) |The disk IOPS of the system |
| Disk Throughput (MBps) |The disk throughput of the system in megabytes per second |
| CPU Utilization (%) |The CPU utilization percentage of the system |
| Memory Utilization (%) |The memory utilization percentage of the system |
| Highest Component Temperature (C) |The highest component temperature in celsius |
| Average Component Temperature (C) |The average component temperature in celsius |

**Data Movers**

| Metric Name  |  Description |
|:------------- |:-------------|
| CPU Utilization (%)     | The CPU utilization percentage of a data mover  |
| Memory Utilization (%) | The percentage of memory utilized by a data mover  |
| Latency (ms) | The latency of a data mover  |
| Total IOPS (ops/sec) | The total IOPS of a data mover  |
| Network Throughput (MB/sec)  | The network throughput of a data mover  |
| Total Disk Throughput (MB/sec)  | The disk throughput of a data mover  |

**Network Interfaces**

| Metric Name  |  Description |
|:------------- |:-------------|
| Inbound Network Throughput (MB/sec)  | The inbound network throughput of the network interfaces |
| Outbound Network Throughput (MB/sec)  |The outbound network throughput of the network interfaces |
| Inbound Network Errors (errors/sec) |The inbound network errors per second of the network interfaces  |
| Outbound Network Errors (errors/sec)  | The outbound network errors per second of the network interfaces |

**File Systems**

| Metric Name  |  Description |
|:------------- |:-------------|
| Used Capacity (%) | The utilization percentage of a file system   |
| Used Capacity (GB)  | The used capacity of a file system in gigabytes |
| Total Capacity (GB)  | The total capacity of a file system in gigabytes |
| Read IOPS (ops/sec) | The read IOPS of a file system |
| Write IOPS (ops/sec) |  The write IOPS of a file system  |
| Read Throughput (MB/sec) | The read throughput of a file system  |
| Write Throughput (MB/sec) | The write throughput of a file system  |

**Storage Pools**

| Metric Name  |  Description |
|:------------- |:-------------|
| Used Capacity (%) |The utilization percentage of a storage pool  |             
| Used Capacity (GB) |The used capacity of a storage pool in gigabytes | 
| Total Capacity (GB) |The total capacity of a storage pool in gigabytes |


**Volumes**

| Metric Name  |  Description |
|:------------- |:-------------|
| Used Capacity (%) |The utilization percentage of a volume  |             
| Used Capacity (TB) |The used capacity of a volume in terabytes | 
| Total Capacity (TB) |The total capacity of a volume in terabytes |

**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| Latency (ms) | The average latency for Data Movers in micro seconds |
| Total Disk IOPS | The total IOPS for all disks in the system |
| Total Disk Throughput (Bps) | The total throughput for all disks in the system |
| Total Network Throughput (Bps) | The total network throughput for the system |
| High Temp (C) | The highest temperature detected in celsius |
