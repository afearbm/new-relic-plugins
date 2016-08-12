# Blue Medora EMC VNX Block Plugin for New Relic

The **Blue Medora EMC VNX Block Plugin for New Relic** allows you to monitor your VNX Block performance data from within the New Relic platform by pulling in metrics from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.						
This guide includes instructions for installing and configuring the Blue Medora EMC VNX Block Plugin for New Relic.
If you’re having a bad experience with one of our plugins, please get in touch and we’ll be happy to help you out.

----

## System Requirements

The VNX Block plugin connects to VNX using NaviSecCLI (Navisphere Secure Command Line Interface).  This allows a network connection to the VNX storage processors to be made.  Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**
- A New Relic account (Sign up for a free account [here](http://newrelic.com).)

**EMC VNX Block Plugin Requirements**
- **Block Software Version:** This plugin requires Block Software Version 5.33+
- **NaviSecCLI:** The host must have NaviSecCLI software installed
- **A Blue Medora License.** A trial license will ship with the plugin that is valid until October 11, 2016. To obtain a production license or get pricing information for the plugin, contact sales@bluemedora.com.

----

## Installing the Plugin

We recommend using the New Relic Platform Installer for installing and running your Blue Medora plugins for New Relic.

The New Relic Platform Installer (NPI) is a command line tool that helps you easily download, configure, and manage New Relic Platform Plugins.  For more information, refer to the [Installing an NPI-compatible plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/installing-plugins/installing-npi-compatible-plugin).

Once the NPI tool has been installed, run the following command:

```
  ./npi install com.bluemedora.emc.vnx.block
``` 

**Note:** This command will take care of the creation of `newrelic.json` and `plugin.json` files described in the [Configuring the Plugin](#Configuring-the-Plugin) section.

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
```
The two "template" files found in the config folder must be modified (i.e., customized) and renamed prior to setting up the plugin for monitoring.

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

**NOTE:** You can add multiple objects to the “agents” array to monitor multiple {monitored system name} instances.

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| polling_interval_seconds | The number of seconds between each data collection. |
| instance_name | The name of your New Relic VNX Block instance that will appear in the User Interface |
| spa_host | The IP or domain name of storage processor A for the VNX Block array |
| spb_host | The IP or domain name of storage processor B for the VNX Block array |
| username | User name to connect to NaviSecCLI |
| password | Password to connect to NaviSecCLI |
| naviseccli_path | Full path to NaviSecCLI executable on the host where this target is being installed |
| no_poll | Disables poll requests, significantly increasing performance in larger systems, but in exchange for returning cached data. |
| scope | Optional parameter, an integer of `0` for global or `1` for local |
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
For questions or issues regarding the Blue Medora EMC VNX Block Plugin for New Relic, visit http://support.bluemedora.com. 

----     

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| LUN Used Capacity (%)     | The used capacity percentage of a LUN  |
| Power (watts)     | Amount of power being used in watts |
| LUN IOPS (ops/sec)     | The IOPS per second of a LUN  |
| LUN Throughput (MB/sec)     | The throughput of a LUN in megabytes per second  |
| Storage Pool Used Capacity (%)     | The used capacity of a storage pool |
| Storage Processor Throughput (MB/sec)     | The throughput of a storage processor in megabytes per second |
| Storage Processor Read Hit Ratio (%)   | The read hit ratio of a storage pool |
| Storage Processor Write Hit Ratio (%)   | The write hit ratio of a storage pool |

**Storage Processors**

| Metric Name  |  Description |
|:------------- |:-------------|
| CPU Utilization (%) | The CPU utilization percentage of a storage processor |
| Read Hit Ratio (%) | The read hit ratio percentage of a storage processor |
| Write Hit Ratio (%)  | The write hit ratio percentage of a storage processor |
| Read IOPS (ops/sec) | The read IOPS per second of a storage processor  |
| Write IOPS (ops/sec)  | The write IOPS ratio of a storage processor |
| Read Throughput (MB/sec)    | The read throughput of a storage processor |
| Write Throughput (MB/sec) | The write throughput of a storage processor |

**Storage Pools**

| Metric Name  |  Description |
|:------------- |:-------------|
| Used Capacity (%) | The used capacity percentage of a storage pool |
| Used Capacity (TB) | The used capacity of a storage pool|             
| Available Capacity (TB) | The available capacity of a storage pool| 
| Subscribed Capacity (TB) | The subscribed capacity of a storage pool |
| Oversubscribed Capacity (TB) | The oversubscribed capacity of a storage pool |

**Disks**

| Metric Name  |  Description |
|:------------- |:-------------|
| Top 10 Capacity (GB) | The 10 Disks with the highest capacity |
| Top 10 User Capacity (GB) | The 10 Disks with the highest user capacity |
| Top 10 Read Throughput (MB/sec) | The 10 Disks with the highest read throughput |
| Top 10 Write Throughput (MB/sec) | The 10 Disks with the highest Write throughput |
| Top 10 Read IOPS (ops/sec) | The 10 Disks with the highest read IOPS |
| Top 10 Write IOPS (ops/sec) | The 10 Disks with the highest write IOPS |
| Top 10 Read Requests (ops/sec) | The 10 Disks with the highest read requests |
| Top 10 Write Requests (ops/sec) | The 10 Disks with the highest write requests |

**LUNs**

| Metric Name  |  Description |
|:------------- |:-------------|
| Used Capacity (%) | Percentage of LUN used capacity for a given LUN |
| Used Capacity (GB)  | The used capacity of a LUN in gigabytes |
| Total Capacity (GB)  | The total capacity of a LUN in gigabytes |
| Read IOPS (ops/sec) | The read IOPS per second of a LUN  |
| Write IOPS (ops/sec) | The write IOPS per second of a LUN |
| Read Throughput (MB/sec)  | The read throughput of a LUN |
| Write Throughput (MB/sec)  | The write throughput of a LUN        |

**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| Read Miss (%)     | Read miss ratio for VNX Block |
| Write Miss (%)     | Write miss ratio for VNX Block  |
| Average LUN Utilization (%)     | Average LUN Utilization for VNX Block  |


