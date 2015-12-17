# Blue Medora NetApp Storage Plugin for New Relic

The **Blue Medora NetApp Storage Plugin for New Relic** allows you to monitor your NetApp Storage performance data from within the New Relic platform by pulling metrics in from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.						

This guide includes instructions for installing and configuring the Blue Medora NetApp Storage Plugin for New Relic.

----

## Obtaining the Plugin
You can find the New Relic NetApp Storage Plugin in the following locations:

- [New Relic Storefront](http://newrelic.com/plugins/blue-medora/430)
- [Plugin Central](https://rpm.newrelic.com/accounts/890835/plugins/directory/430)

## System Requirements

The NetApp Storage plugin collects data by making REST calls to OnCommand API Services.  Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**
- A New Relic account

**NetApp Storage Plugin Requirements**
- **NetApp OnCommand ApiServices:** NetApp OnCommand API Services version 1.0 or 1.1 is required to collect data.  
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
	./npi install com.bluemedora.netapp.apiservices
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

#### Configuring the `plugin.template.json` file: 

The second file, `plugin.template.json`, contains data specific to each plugin (e.g., a list of hosts and port combinations for what you are monitoring). Templates for both of these files should be located in the ‘config’ directory in your extracted plugin folder.

Make a copy of this template and rename it to `plugin.json`. Shown below is an example of the `plugin.json` file’s contents.

**NOTE:** You can add multiple objects to the “agents” array to monitor multiple NetApp Storage instances.

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| instance_name | The name of your New Relic NetApp Storage instance that will appear in the User Interface |
| username | User name to log into NetApp Storage API Services |
| password | Password to log into NetApp Storage API Services |
| server | The hostname or ip address of the server |
| protocol | Either `http` or `https` |
| port | Optional parameter, port to connect to NetApp API Services |


**Example:**

```
{
  "agents": [
    {
      "instance_name": "Your NetApp Storage Instance",
      "username": "your_value_here",
      "password": "your_value_here",
      "server": "your_value_here",
      "protocol": "http or https"
    },
    {
      "instance_name": "Your NetApp Storage Instance",
      "username": "your_value_here",
      "password": "your_value_here",
      "server": "your_value_here",
      "protocol": "http or https",
      "port": "your_custom_port"
    }
  ]
}
```

## Using the Plugin
For more information about navigating New Relic’s user interface, refer to their [Using a plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/using-plugins/using-plugin) section.

## Support Resources
For questions or issues regarding the Blue Medora NetApp Storage Plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| SVM Used Capacity (%)  | Percentage of svm capacity used |
| Aggregate Used Capacity (%) | Percentage of aggregate capacity used |
| Volume Used Capacity (%) |  Percentage of volume capacity used  |
| LUN Used Capacity (%) |  Percentage of LUN capacity used  |
| System Latency (µs) |   The average latency for systems |
| System IOPS (ops/sec) |  The total IOPS for systems  |
| Volume Latency (µs) |  The average latency for volumes |
| Volume IOPS (ops/sec) |  The total IOPS for volumes  |
| LUN Latency (µs) |  The average latency for LUNs  |
| LUN IOPS (ops/sec) |  The total IOPS for LUNs  |

**SVMs**

| Metric Name  |  Description |
|:------------- |:-------------|
| Volume Used Capacity (%)  | Percentage of capacity used by volumes on each svm |
| Volume Used Capacity (TB) |  Terabytes of capacity used by volumes on each svm |
| Volume Total Capacity (TB) |  The total terabytes of volumes on each svm  |
| Volume Read Latency (µs) |  Average read latency for volumes on each svm |
| Volume Write Latency (µs) |  Average write latency for volumes on each svm |
| Volume Read IOPS (ops/sec) |  Total IOPS for volumes on each svm |
| Volume Write IOPS (ops/sec) |  Total write IOPS for volumes on each svm |
| Inbound Network Throughput (MB/sec) |  The inbound network throughput for svms |
| Outbound Network Throughput (MB/sec) |  The outbound network thoughput for svms  |
| Inbound Network Errors (errors/sec) |  Number of inbound network errors per second on this svm  |
| Outbound Network Errors (errors/sec) |  Number of outbound network errors per second on this svm  |

**Systems**

| Metric Name  |  Description |
|:------------- |:-------------|
| CPU Utilization (%)  | Percentage of cpu utilized by each system |
| Processors |  Number of processors on each system  |
| Data Disks |  Number of data disks on each system  |
| Read Latency (µs) |  Read latency for each system  |
| Write Latency (µs) |  Write latency for each system  |
| Read IOPS (ops/sec) |  Total read IOPS for each system  |
| Write IOPS (ops/sec) |  Total write IOPS for each system |
| Inbound Network Throughput (MB/sec) |  Inbound network throughput for each system  |
| Outbound Network Throughput (MB/sec) |  Outbound network throughput for each system  |
| Failed Fans |  Number of failed fans on each system  |
| Failed Power Supplies |  Number of failed power supplies on each system  |

**Disks**

| Metric Name  |  Description |
|:------------- |:-------------|
| Used Capacity (%)  | Percentage of capacity used for each disk |
| Used Capacity (TB) |  Terabytes of capacity used for each disk  |
| Total Capacity (TB) | The total capacity of each disk in terabytes   |
| Busy Time (%) |  Percentage of time in which the disk is busy  |
| IOPS (ops/sec) |  Total IOPS for each disk  |

**Aggregates**

| Metric Name  |  Description |
|:------------- |:-------------|
| Used Capacity (%)  |  Percentage of capacity used for each aggregate  |
| Used Capacity (TB) |  Terabytes of capacity used for each aggregate  |
| Total Capacity (TB) |  The total capacity of each aggregate in terabytes  |
| Volume Read Latency (µs) |  Average read latency for volumes on each aggregate  |
| Volume Write Latency (µs) |  Average write latency for volumes on each aggregate  |
| Volume Read IOPS (ops/sec) |  Total IOPS for volumes on each aggregate  |
| Volume Write IOPS (ops/sec) |  Total write IOPS for volumes on each aggregate  |
| Volume Deduplication Space Savings (GB) |  Space saved by deduplication on volumes on each aggregate  |
| Volume Compression Space Savings (GB) |  Space saved by compression on volumes on each aggregate  |

**Volumes**

| Metric Name  |  Description |
|:------------- |:-------------|
| Used Capacity (%)  |  Percentage of capacity used for each volume  |
| Used Capacity (TB) |  Terabytes of capacity used for each volume  |
| Total Capacity (TB) |  The total capacity of each volume in terabytes  |
| Read Latency (µs) |  Read latency for each volume  |
| Write Latency (µs) |  Write latency for each volume  |
| Read IOPS (ops/sec) |  Read IOPS for each volume  |
| Write IOPS (ops/sec) |  Write IOPS for each volume  |
| LUN Read Throughput (MB/sec) |  Read throughput for LUNs on each volume  |
| LUN Write Throughput (MB/sec) |  Write throughput for LUNs on each volume |
| Deduplication Space Savings (GB) |  Space saved by deduplication on each volume  |
| Compression Space Savings (GB) |  Space saved by compression on each volume  |

**LUNs**

| Metric Name  |  Description |
|:------------- |:-------------|
| Used Capacity (%)  |  Percentage of capacity used for each LUN  |
| Used Capacity (TB) |  Terabytes of capacity used for each LUN  |
| Total Capacity (TB) |  The total capacity of each LUN in terabytes  |
| Read Latency (µs) |  Read latency for each LUN  |
| Write Latency (µs) |  Write latency for each LUN  |
| Read IOPS (ops/sec) |  Read IOPS for each LUN  |
| Write IOPS (ops/sec) |  Write IOPS for each LUN  |
| Read Throughput (MB/sec) |  Read throughput for each LUN  |
| Write Throughput (MB/sec) |  Write throughput for each LUN  |

**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| SVM Used Capacity (%)  | The percentage of svm capacity used |
| System IOPS (ops/sec) |  Total IOPS for systems  |
| System Latency (µs) |  Average Latency of systems  |


