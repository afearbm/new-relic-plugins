# Blue Medora EMC VNX Block Plugin for New Relic

The **Blue Medora EMC VNX Block Plugin for New Relic** allows you to monitor your VNX Block performance data from within the New Relic platform by pulling in metrics from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.						

This guide includes instructions for installing and configuring the Blue Medora EMC VNX Block Plugin for New Relic.

----

## Obtaining the Plugin
You can find the New Relic EMC VNX Block plugin in the following locations:

- [New Relic Storefront](http://newrelic.com/plugins/blue-medora/426)
- [Plugin Central](https://rpm.newrelic.com/accounts/890835/plugins/directory/426)

## System Requirements

The VNX Block plugin connects to VNX using NaviSecCLI(Navisphere Secure Command Line Interface).  This allows a network connection to the VNX storage processors to be made.  Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**
- A New Relic account.

**EMC VNX Block Plugin Requirements**
- **Block Software Version:** This plugin requires Block Software Version 5.33+
- **NaviSecCLI:** The host must have NaviCLI software installed
- **A Blue Medora License.** A trial license will ship with the plugin. This license will remain effective for the duration of the Blue Medora beta trial period. 

----

## Installing the Plugin

This plugin can be installed one of the following ways:

- Using the New Relic Platform Installer
- Installing the Plugin Manually

### Using the New Relic Platform Installer

The New Relic Platform Installer (NPI) is a command line tool that helps you easily download, configure, and manage New Relic Platform Plugins.  For more information, visit the [Getting Started with the Platform Installer section of New Relic’s Community Forum](https://discuss.newrelic.com/t/getting-started-with-the-platform-installer/842).

**NOTE:** We recommend using the New Relic Platform Installer for installing and running your Blue Medora plugins for New Relic. Issues can arise by running a plugin directly in the foreground (e.g., when the machine reboots, the process will not be started again). The NPI automates much of these processes.

Once the NPI tool has been installed, run the following command:

```
	./npi install com.bluemedora.emc.vnx.block
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
```
The two "template" files found in the config folder must be modified (i.e., customized) and renamed prior to setting up the plugin for monitoring.

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

#### Configuring the `plugin.template.json` file: 

The second file, `plugin.template.json`, contains data specific to each plugin (e.g., a list of hosts and port combinations for what you are monitoring). Templates for both of these files should be located in the ‘config’ directory in your extracted plugin folder.

Make a copy of this template and rename it to `plugin.json`. Shown below is an example of the `plugin.json` file’s contents.

**NOTE:** You can add multiple objects to the “agents” array to monitor multiple {monitored system name} instances.

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| instance_name | The name of your New Relic VNX Block instance that will appear in the User Interface |
| spa_host | The IP or domain name of storage processor A for the VNX Block array |
| spb_host | The IP or domain name of storage processor B for the VNX Block array |
| username | User name to connect to NaviSecCLI |
| password | Password to connect to NaviSecCLI |
| naviseccli_path | Full path to NaviSecCLI executable on the host where this target is being installed |
| scope | An integer of `0` for global or `1` for local |

**Example:**

```
{
  "agents": [
    {
      "instance_name": "Your VNX Block Instance",
      "spa_host": "your_value_here",
      "spb_host": "your_value_here",
      "username": "your_value_here",
      "password": "your_value_here",
      "naviseccli_path": "/opt/Navisphere/bin",
      "scope": 0
    }
  ]
}
```

## Using the Plugin
For more information about navigating New Relic’s user interface, refer to their [Using a plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/using-plugins/using-plugin) section.

## Support Resources
For questions or issues regarding the Blue Medora EMC VNX Block Plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**LUNs**

| Metric Name  |  Description |
|:------------- |:-------------|
| Used Capacity (%) | Percentage of LUN used capacity for a given LUN |
| Read IOPS (ops/sec) | The read IOPS per second of a LUN  |
| Write IOPS (ops/sec) | The write IOPS per second of a LUN |
| Read Throughput (MB/sec)  | The read throughput of a LUN |
| Write Throughput (MB/sec)  | The write throughput of a LUN        |
| Used Capacity (GB)  | The used capacity of a LUN in gigabytes |
| Total Capacity (GB)  | The total capacity of a LUN in gigabytes |

**Storage Pools**

| Metric Name  |  Description |
|:------------- |:-------------|
| Used Capacity (%) | The used capacity percentage of a storage pool |
| Used Capacity (TB) | The used capacity of a storage pool|             
| Available Capacity (TB) | The available capacity of a storage pool| 
| Subscribed Capacity (TB) | The subscribed capacity of a storage pool |
| Oversubscribed Capacity (TB) | The oversubscribed capacity of a storage pool |

**Storage Processors**

| Metric Name  |  Description |
|:------------- |:-------------|
| Read IOPS (ops/sec) | The read IOPS per second of a storage processor  |
| Write IOPS (ops/sec)  | The write IOPS ratio of a storage processor |
| Read Hit Ratio (%) | The read hit ratio percentage of a storage processor |
| Write Hit Ratio (%)  | The write hit ratio percentage of a storage processor |
| Read Throughput (MB/sec)    | The read throughput of a storage processor |
| Write Throughput (MB/sec) | The write throughput of a storage processor |
| CPU Utilization (%) | The CPU utilization percentage of a storage processor |
| Memory (gigabytes) | The memory of a storage processor in gigabytes |

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Power (watts)     | Amount of power being used in watts |
| LUN Used Capacity (%)     | The used capacity percentage of a LUN  |
| LUN IOPS (ops/sec)     | The IOPS per second of a LUN  |
| LUN Throughput (MB/sec)     | The throughput of a LUN in megabytes per second  |
| Storage Pool Used Capacity (%)     | The used capacity of a storage pool |
| Storage Processor Throughput (MB/sec)     | The throughput of a storage processor in megabytes per second |
| Storage Processor Read Hit Ratio (%)   | The read hit ratio of a storage pool |
| Storage Processor Write Hit Ratio (%)   | The write hit ratio of a storage pool |

**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| Read Miss (%)     | Read miss ratio for VNX Block |
| Write Miss Capacity (%)     | Write miss ration for VNX Block  |
| Average LUN Utilization (%)     | Average LUN Utilization for VNX Block  |


