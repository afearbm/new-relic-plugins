# Blue Medora VMware vSphere Plugin for New Relic

The **Blue Medora VMware vSphere Plugin for New Relic** allows you to monitor your VMware vSphere performance data from within the New Relic platform by pulling metrics in from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.						

This guide includes instructions for installing and configuring the Blue Medora VMware vSphere Plugin for New Relic.

----

## Obtaining the Plugin
You can find the New Relic VMware vSphere plugin in the following locations:

- [New Relic Storefront](http://newrelic.com/plugins/blue-medora/432)
- [Plugin Central](https://rpm.newrelic.com/accounts/890835/plugins/directory/432)

## System Requirements

The VMware vSphere Plugin connects to VMware vSphere using <?>. Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**
- A New Relic account

**VMware vSphere Plugin Requirements**

- **VMware vSphere:** This plugin requires VMware vSphere Version 5.0 or later
- **A Blue Medora License:** A trial license will ship with the plugin. This license will remain effective for the duration of the Blue Medora beta trial period. 

----

## Installing the Plugin

This plugin can be installed one of the following ways:

- Using the New Relic Platform Installer
- Installing the Plugin Manually

### Using the New Relic Platform Installer

The New Relic Platform Installer (NPI) is a command line tool that helps you easily download, configure, and manage New Relic Platform Plugins.  For more information, refer to the [Installing an NPI-compatible plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/installing-plugins/installing-npi-compatible-plugin).

**NOTE:** We recommend using the New Relic Platform Installer for installing and running your Blue Medora plugins for New Relic. Issues can arise by running a plugin directly in the foreground (e.g., when the machine reboots, the process will not be started again). The NPI automates much of these processes.

Once the NPI tool has been installed, run the following command:

```
	./npi install com.bluemedora.vmware.vcenter
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

**NOTE:** You can add multiple objects to the “agents” array to monitor multiple VMware vSphere instances.

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| polling_interval_seconds | The number of seconds between each data collection. |
| instance_name | The name of your New Relic VMware vSphere instance that will appear in the User Interface. |
| username  | User name to log into vSphere. |
| password | Password to log into vSphere. |
| host | vSphere hostname. |
| port | Port to connect to vSphere. |
| useSSL | Indicate wether to connect via ssl. Valid values are `Yes` or `No`. |
| enable_insights | Indicates whether or not to send data to New Relic Insights for this instance. |

**NOTE:** There are optional fields if `enable_insights` is `true` that allow specific event types to be toggled whether they send data to Insights. 
Theses fields are listed below and valid values are `true` or `false`:

* `enable_insights_for_cluster`
* `enable_insights_for_datacenter`
* `enable_insights_for_datastore`
* `enable_insights_for_host_system`
* `enable_insights_for_resource_pool`
* `enable_insights_for_virtual_machine`
* `enable_insights_for_vsphere`

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
      "useSSL": "your_value_here",
      "port": "your_value_here",
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
For questions or issues regarding the Blue Medora VMware vSphere plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Host Status  | The number of hosts in `Healthy`, `Warning`, `Critical`, and `Unknown` states |
| Virtual Machine Status | The number of Virtual Machines in `Healthy`, `Warning`, `Critical`, and `Unknown` states |
| Top 10 VM CPU Utilization (%) | The 10 Virtual Machines with the highest CPU utilization |
| Top 10 VM Memory Utilization (%) | The 10 Virtual Machines with the highest memory utilization |
| Top 10 Host CPU Utilization (%) | The 10 Hosts with the highest CPU utilization |
| Top 10 Host Memory Utilization (%) | The 10 Hosts with the highest memory utilization |
| Top 10 VM CPU Ready Percentage (%) | The 10 Virtual Machines with the highest CPU ready percentage |
| Top 10 Datastore Space Utilization (%) | The 10 Datastores with the highest space utilization |

**Virtual Machines**

| Metric Name  |  Description |
|:------------- |:-------------|
| Status | The number of Virtual Machines in `Healthy`, `Warning`, `Critical`, and `Unknown` states |
| Top 10 CPU Utilization (%) | The 10 Virtual Machines with the highest CPU utilization |
| Top 10 CPU Ready Percentage (%) | The 10 Virtual Machines with the highest CPU ready percentage |
| Top 10 Disk Utilization (%) | The 10 Virtual Machines with the highest disk utilization |
| Top 10 Memory Utilization (%) | The 10 Virtual Machines with the highest memory utilization |
| Top 10 Network Throughput (MB/sec) | The 10 Virtual Machines with the network throughput |
| Top 10 Disk Throughput (MB/sec) | The 10 Virtual Machines with the disk throughput |

**Host Systems**

| Metric Name  |  Description |
|:------------- |:-------------|
| Top 10 CPU Utilization (%) | The 10 Hosts with the highest CPU utilization |
| Top 10 Memory Utilization (%) | The 10 Hosts with the highest memory utilization |
| Top 10 Memory Swapin/Swapout (MB/sec) | The 10 Hosts with the highest memory Swaping/Swapout rate |
| Disk Read Throughput (MB/sec) | Disk read throughput across hosts |
| Disk Write Throughput (MB/sec) | Disk write throughput across hosts |
| Disk Read Latency (ms) | Disk read latency across hosts |
| Disk Write Latency (ms) | Disk write latency across hosts |
| Network Received Packets (packets/sec) | Packets received on the network across hosts |
| Network Transmitted Packets (packets/sec) | Packets transmitted on the network across hosts |
| Network Received Throughput (MB/sec) | Traffic received on the network across hosts |
| Network Transmitted Throughput (MB/sec) | Traffic transmitted on the network across hosts |

**Datastores**

| Metric Name  |  Description |
|:------------- |:-------------|
| Top 10 Space Utilization (%) | The 10 Datastores with the highest space utilization |
| Top 10 Total Space (TB) | The 10 Datastores with the largest total space |
| Top 10 Used Space (TB) | The 10 Datastores with the most used space |
| Top 10 Free Space (TB) | The 10 Datastores with the most free space |

**Clusters**

| Metric Name  |  Description |
|:------------- |:-------------|
| Available CPU (MHz) | The amount of CPU available |
| Effective CPU (MHz) | The amount of effective cpu |
| Available Memory (TB) | The amount of memory available to the cluster |
| Effective Memory (TB) | The amount of effective memory on the cluster |
| Host Systems | The number of hosts and effective hosts per cluster |
| CPU Resources | The number of CPU cores and threads per cluster |

**Datacenters**

| Metric Name  |  Description |
|:------------- |:-------------|
| Average Host System CPU Usage (%) | The average CPU usage across all hosts per datacenter |
| Disk Space (TB) | The total disk space per datacenter |
| CPUs | The total CPU count per datacenter |
| Network Adapters | The total network adapter count per datacenter |
| Host Systems | The total host system count per datacenter |
| Virtual Machines | The total virtual machines count per datacenter |
| Host Systems w/ Warning Status | The number of host systems with a warning status per datacenter |
| Virtual Machines w/ Warning Status | The number of virtual machines with a warning status per datacenter |
| Host Systems w/ Critical Status | The number of host systems with a critical status per datacenter |
| Virtual Machines w/ Critical Status | The number of virtual machines with a critical status per datacenter |

**vSphere**

| Metric Name  |  Description |
|:------------- |:-------------|
| Up/Down Hosts | The number of hosts that are Up and Down on the vSphere |
| Entities | The number of VMs, Hosts, Datastores, Clusters, and Datacenters on the vSphere |
| Host System Status | The number of hosts in `Healthy`, `Warning`, `Critical`, and `Unknown` states |
| Virtual Machine Status | The number of Virtual Machines in `Healthy`, `Warning`, `Critical`, and `Unknown` states |
| Clusters Status | The number of Clusters in `Healthy`, `Warning`, `Critical`, and `Unknown` states |
| Datastore Status | The number of Datastores in `Healthy`, `Warning`, `Critical`, and `Unknown` states |
| Resources | The number of CPU cores, Network Adapters, and CPUs per vSphere |
| Available CPU (GHz) | The available CPU on the vSphere |
| Total Space (TB) | The total disk space on the vSphere |
| Available Memory (TB) | The memory available to the vSphere |

**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| Distressed Hosts | The number of hosts that are in a `Warning` or `Critical` state |
| Distressed VMs | The number of virtual machines that are in a `Warning` or `Critical` state |
| Distressed Clusters | The number of clusters that are in a `Warning` or `Critical` state |
| Distressed Datastores | The number of datastores that are in a `Warning` or `Critical` state |
