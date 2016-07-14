# Blue Medora NetApp E-Series Plugin for New Relic

The **Blue Medora NetApp E-Series Plugin for New Relic** allows you to monitor performance data from your NetApp E-Series arrays from within the New Relic platform by pulling in metrics in from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.						

This guide includes instructions for installing and configuring the Blue Medora NetApp E-Series Plugin for New Relic.

----

## Obtaining the Plugin
You can find the New Relic NetApp E-Series Storage Plugin in the following locations:

- [New Relic Storefront](http://newrelic.com/plugins/blue-medora/430)
- [Plugin Central](https://rpm.newrelic.com/accounts/890835/plugins/directory/430)

## System Requirements

The NetApp E-Series Storage plugin collects data using the SANtricity Web Service for E-Series.  Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**
- A New Relic account (Sign up for a free account [here](http://newrelic.com).)

**NetApp E-Series Storage Plugin Requirements**
- **A NetApp E- or EF-Series storage system**
- **API:** SANtricity Web Service for E-Series Proxy v1.3+.  
- **A Blue Medora License.** A trial license will ship with the plugin that is valid until September 13, 2016. To obtain a production license or get pricing information for the plugin, contact sales@bluemedora.com.

----

## Installing the Plugin

This plugin can be installed one of the following ways:

- Using the New Relic Platform Installer
- Installing the Plugin Manually

### Using the New Relic Platform Installer

The New Relic Platform Installer (NPI) is a command line tool that helps you easily download, configure and manage New Relic Platform Plugins.  For more information, refer to the [Installing an NPI-compatible plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/installing-plugins/installing-npi-compatible-plugin).

**NOTE:** We recommend using the New Relic Platform Installer for installing and running your Blue Medora plugins for New Relic. Issues can arise by running a plugin directly in the foreground (e.g., when the machine reboots, the process will not be started again). The NPI automates much of these processes.

Once the NPI tool has been installed, run the following command:

```
	./npi install com.bluemedora.netapp.eseries
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

**NOTE:** You can add multiple objects to the “agents” array to monitor multiple NetApp E-Series Storage instances.

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| polling_interval_seconds | The number of seconds between each data collection. |
| instance_name | The name of your New Relic NetApp E-Series Storage instance that will appear in the User Interface |
| username | User name to log into NetApp E-Series Storage |
| password | Password to log into NetApp E-Series Storage  |
| host | The hostname or ip address of the server |
| protocol | Optional parameter, either `http` or `https`. Defaults to `http` if not present |
| port | Optional parameter, port to connect to NetApp E-Series |
| enable_insights | Indicates whether or not to send data to New Relic Insights for this instance. |

**NOTE:** There are optional fields if `enable_insights` is `true` that allow specific event types to be toggled whether they send data to Insights. 
Theses fields are listed below and valid values are `true` or `false`:

* `enable_insights_for_array`
* `enable_insights_for_controller`
* `enable_insights_for_drive`
* `enable_insights_for_dynamic_disk_pool`
* `enable_insights_for_environment_overview`
* `enable_insights_for_host_interface`
* `enable_insights_for_lun`
* `enable_insights_for_network_interface`
* `enable_insights_for_tray`
* `enable_insights_for_volume`
* `enable_insights_for_volume_group`


**Example:**

```
{
  "polling_interval_seconds": 60,
  "agents": [
    {
      "instance_name": "http_basic_instance",
      "host": "your_value_here",
      "username": "your_value_here",
      "password": "your_value_here",
      "enable_insights": "true"
    },
    {
      "instance_name": "https_instance",
      "host": "your_value_here",
      "username": "your_value_here",
      "password": "your_value_here",
      "protocol": "https",
      "enable_insights": "true"
    },
    {
      "instance_name": "custom_port_instance",
      "host": "your_value_here",
      "username": "your_value_here",
      "password": "your_value_here",
      "protocol": "https or http",
      "port": "your_custom_port",
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
For questions or issues regarding the Blue Medora NetApp E-Series Storage Plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Resource Utilization (%)  | The percentage of IOPS and used space utilization for the Array. |
| Array IOPS (ops/sec)  | Average and total combined IOPS for related Volumes and Drives. |
| Array Latency (ms)  | Average and total combined latency for related Volumes and Drives. |
| Array Throughput (MB/sec)  | Average and total combined throughput for related Volumes and Drives. |
| Used Space (TB)  | Total used space across all related Drives. |
| Optimal Controller Statuses (%)  | The percentage of Controllers in an optimal status. |

**Dynamic Disk Pools**

| Metric Name  |  Description |
|:------------- |:-------------|
| Total Combined IOPS (ops/sec)  | Total combined IOPS for all related Volumes and Drives. |
| IOPS Utilization (%) | The percentage of maximum IOPS being used by the related Volumes and Drives in the pool. |
| Average Combined Latency (ms) | The average combined latency by the related Volumes and Drives in the pool. |
| Average Combined Throughput (MB/sec) | The average combined throughput by the related Volumes and Drives in the pool. |
| Average Cache Utilization (%) | The average percentage of bytes written to and read from the flash cache by the related Volumes and Drives in the pool. |
| Used Space (TB) | The total amount of space used by the related Volumes and Drives in the pool. |

**Volume Groups**

| Metric Name  |  Description |
|:------------- |:-------------|
| Total Combined IOPS (ops/sec)  | The combined IOPS of the volume group |
| IOPS Utilization (%) | The percentage of maximum IOPS being used by the volume group |
| Average Combined Latency (ms) | The average combined latency of drives and volumes in the volume group |
| Average Combined Throughput (MB/sec) | The average combined throughput of drives and volumes in the volume group |
| Average Cache Utilization (%) | The average cache utilization of drives and volumes in the volume group |
| Used Space (TB) | The space used by the volume group |

**Volumes**

| Metric Name  |  Description |
|:------------- |:-------------|
| Combined IOPS (ops/sec)  | The combined IOPS of the volume |
| IOPS Utilization (%)  | The percentage of maximum IOPS being used by the volume | 
| Read Latency (ms)  | The read latency of the volume | 
| Write Latency (ms)  | The write latency of the volume | 
| Combined Throughput (MB/sec)  | The combined throughput of the volume | 
| Physical IOPS (ops/sec)  | The physical IOPS of the volume | 

**Drives**

| Metric Name  |  Description |
|:------------- |:-------------|
| Combined IOPS (ops/sec)  | The combined IOPS of the drive |
| IOPS Utilization (%)  | The percentage of maximum IOPS being used by the drive | 
| Read Latency (ms)  | The read latency of the drive | 
| Write Latency (ms)  | The write latency of the drive | 
| Combined Throughput (MB/sec)  | The combined throughput of the drive | 
| Physical IOPS (ops/sec)  | The physical IOPS of the drive | 

**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| Avg. IOPS Utilization (%)  | The average percentage of combined IOPS used on the Array. |
| Avg. Space Utilization (%) | The average percentage of used space on the Array. |
| Avg. Drive Latency (ms) | The average combined latency across all Drives on the Array. |
| Avg. Drive Throughput (bytes/second) | The average combined throughput across all Drives on the Array. |


