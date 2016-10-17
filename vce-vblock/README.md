# Blue Medora VCE Vblock Plugin for New Relic

The **Blue Medora VCE Vblock Plugin for New Relic** allows you to monitor your Vblock performance data from within the New Relic platform by pulling metrics in from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.						
This guide includes instructions for installing and configuring the Blue Medora VCE Vblock Plugin for New Relic.
If you’re having a bad experience with one of our plugins, please get in touch and we’ll be happy to help you out.

----

## System Requirements

The VCE Vblock Plugin connects to VNX Block using NaviSecCLI (Navisphere Secure Command Line Interface). This allows a connection to VNX sotrage processors to be made.  It connects to VNX File using SSH protocol to connect to the VNX File Control Station. It collects data from Cisco UCS by making REST calls to the Cisco UCS Manager, and connects to Cisco Nexus hardware via SNMP v2, v2c, or v3.  Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**
- A New Relic account (Sign up for a free account [here](http://newrelic.com).)

**VCE Vblock Plugin Requirements**

- **Block Software Version:** This plugin requires Block Software Version 5.33+
- **NaviSecCLI:** The host must have NaviSecCLI software installed
- The plugin supports **Cisco UCS Manager 2.2+**
- The plugin supports SNMP v2, v2c, or v3 enabled **Cisco Nexus switch(es) 1k, 3k, 4k, 5k, 7k, and/or 9k**
- **A Blue Medora License.** A trial license will ship with the plugin that is valid until November 15, 2016. To obtain a production license or get pricing information for the plugin, contact sales@bluemedora.com.

----

## Installing the Plugin

We recommend using the New Relic Platform Installer for installing and running your Blue Medora plugins for New Relic.

The New Relic Platform Installer (NPI) is a command line tool that helps you easily download, configure, and manage New Relic Platform Plugins.  For more information, refer to the [Installing an NPI-compatible plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/installing-plugins/installing-npi-compatible-plugin).

Once the NPI tool has been installed, run the following command:

```
	./npi install com.bluemedora.vce.vblock
```	

**Note:** This command will take care of the creation of `newrelic.json` and `plugin.json` files described in the [Configuring the Plugin](#Configuring-the-Plugin) section.

Downloading the plugin binary (only use this link if you are not installing via NPI)
https://s3.amazonaws.com/bmibmtransfer/newrelic_vce_vblock_plugin-2.0.0_20160912_180607.tar.gz


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

Make a copy of this template and rename it to plugin.json. Shown below is an example of the `plugin.json` file’s contents.

Each Vblock instance can be made up of several different components. Component objects in the "agents" array should share an "instance_name" if they are part of the same Vblock system. Each component within a Vblock system should have a unique "component_name", as well as a "component_type" that indicates the type of component. The following component types are recognized by the Vblock plugin:

`nexus` - Cisco Nexus

`ucs` - Cisco UCS

`vnxblock` - EMC VNX Block

`vnxfile` - EMC VNX File

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| polling_interval_seconds | The number of seconds between each data collection. |

**EMC VNX File Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| instance_name | The name of your New Relic VNX File instance that will appear in the User Interface |
| primary_control_station  | The IP or domain name of the Primary Control Station for VNX File |
| secondary_control_station | The IP or domain name of the Secondary Control Station for VNX File |
| username | User name to log into the Control Stations |
| password | Password to log into the Control Stations |
| nas_path | Path to the NAS_DB configuration files location.  Usually `/nas`. |
| sample_seconds | Query interval parameter.  Must be an integer 1 or greater |
| send_to_plugin | Indicates whether or not to send data to New Relic Plugins. See [Blue Medora's New Relic Knobs and Levers Readme](https://github.com/BlueMedora/new-relic-plugins/blob/master/configuration-variants/readme.md) for more details |
| send_to_insights | Indicates whether or not to send data to New Relic Insights. See [Blue Medora's New Relic Knobs and Levers Readme](https://github.com/BlueMedora/new-relic-plugins/blob/master/configuration-variants/readme.md) for more details |

**EMC VNX Block Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| instance_name | The name of your New Relic VNX Block instance that will appear in the User Interface |
| spa_host | The IP or domain name of storage processor A for the VNX Block array |
| spb_host | The IP or domain name of storage processor B for the VNX Block array |
| username | User name to connect to NaviSecCLI |
| password | Password to connect to NaviSecCLI |
| naviseccli_path | Full path to NaviSecCLI executable on the host where this target is being installed |
| no_poll | Disables poll requests, significantly increasing performance in larger systems, but in exchange for returning cached data. |
| send_to_plugin | Indicates whether or not to send data to New Relic Plugins. See [Blue Medora's New Relic Knobs and Levers Readme](https://github.com/BlueMedora/new-relic-plugins/blob/master/configuration-variants/readme.md) for more details |
| send_to_insights | Indicates whether or not to send data to New Relic Insights. See [Blue Medora's New Relic Knobs and Levers Readme](https://github.com/BlueMedora/new-relic-plugins/blob/master/configuration-variants/readme.md) for more details |

**Cisco UCS Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| instance_name | The name of your New Relic Cisco UCS instance that will appear in the User Interface |
| username | User name to log into UCS Manager |
| password | Password to log into UCS Manager |
| host | The hostname or ip address of UCS Manager |
| protocol | Either `http` or `https` |
| port | Optional parameter, port to connect to UCS Manager |
| send_to_plugin | Indicates whether or not to send data to New Relic Plugins. See [Blue Medora's New Relic Knobs and Levers Readme](https://github.com/BlueMedora/new-relic-plugins/blob/master/configuration-variants/readme.md) for more details |
| send_to_insights | Indicates whether or not to send data to New Relic Insights. See [Blue Medora's New Relic Knobs and Levers Readme](https://github.com/BlueMedora/new-relic-plugins/blob/master/configuration-variants/readme.md) for more details |

**Cisco Nexus Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| instance_name | The name of your New Relic Cisco Nexus instance that will appear in the User Interface |
| version | SNMP version acceptable values `v2c` or `v3` for SNMP Version 2 or Version 3 respectively |
| management_ip | IP address of Nexus Switch |
| snmp_port | Optional field, port SNMP communicates over. Defaults to `161` |
| community_string | Only applicable if `version` is `v2c`. The SNMP community string required to connect |
| security_level | Only applicable if `version` is `v3`. Acceptable values are `auth_priv`, `auth_nopriv`, or `noauth_nopriv` |
| user | Only applicable if `version` is `v3`. SNMP user name |
| auth_password | Only applicable if `version` is `v3`. SNMP Authentication password. Only needed if `security_level` is `auth_priv` or `auth_nopriv` |
| privacy_password | Only applicable if `version` is `v3`. SNMP privacy password, Only needed if `security_level` is `auth_priv` |
| privacy_type | Only applicable if `version` is `v3`. SNMP encryption type. Acceptable values are `privAES256`, `privAES192`, `privAES128`, `privDES`, or `priv3DES` for  256 bit AES, 192 bit AES, 128 bit AES, DES, or 3-DES respectively |
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
For questions or issues regarding the Blue Medora VCE Vblock plugin for New Relic, visit http://support.bluemedora.com. 

----     

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Nexus Total Throughput (MB/sec)  | The total throughput of the system in megabytes per second |
| Nexus Port Status (%) | Percentage of the systems port statuses (Up or Down) |
| UCS Fabric Interconnect Load (%) | Load percentage of the fabric interconnect     |
| UCS Chassis Power (watts) | Power of the chassis in watts    |
| UCS Blade Available Memory (GB)) | The memory available on a blade |
| UCS Blade Memory Speed (MHz) | The speed of memory on a blade |
| VNX Block LUN Used Capacity (%) | Percentage of used capacity of the VNX Block LUN  |
| VNX Block LUN IOPS (ops/sec) | IOPS of the VNX Block IOPS  |
| VNX Block LUN Throughput (MB/sec) | Throughput of the VNX Block LUN in megabytes per second  |
| VNX File Latency (ms) | Latency of the VNX File |

**Nexus Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Port Status (%)  | The percentage of port statuses (Up or Down) |
| Top 10 Port Inbound Packets (packets/sec) | The 10 ports with the highest inbound packets per second |
| Top 10 Port Outbound Packets (packets/sec) | The 10 ports with the highest outbound packets per second |
| Top 10 Port Inbound Throughput (MB/sec) | The 10 ports with the highest inbound throughput in megabytes per second |
| Top 10 Port Outbound Throughput (MB/sec) | The 10 ports with the highest outbound throughput in megabytes per second   |
| Top 10 L3 Interface Inbound Throughput (MB/sec) | The 10 L3 interfaces with the highest inbound throughput in megabytes per second |
| Top 10 L3 Interface Outbound Throughput (MB/sec) | The 10 L3 interfaces with the highest outbound throughput in megabytes per second   |
| Top 10 L3 Interface Inbound Packets (packets/sec) | The 10 L3 interfaces with the highest inbound packets per second |
| Top 10 L3 Interface Outbound Packets (packets/sec) | The 10 L3 interfaces with the highest outbound packets per second |

**UCS Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Fabric Interconnect Load (%)  | The load percentage of the fabric interconnect |
| Chassis Input Power (watts) | The input power of the chassis in watts   |
| Chassis Output Power (watts) | The output power of the chassis in watts   |
| Blade Available Memory (GB)) | The memory available on a blade |
| Blade Memory Speed (MHz) | The speed of memory on a blade |
| Top 10 Ethernet Port Inbound Throughput (bits/sec) | The 10 ethernet ports with the highest inbound throughput  |
| Top 10 Ethernet Port Outbound Throughput (bits/sec)  | The 10 ethernet ports with the highest outbound throughput  |
| Top 10 Fiber Channel Inbound Throughput (bits/sec)  | The 10 fiber channels with the highest inbound throughput  |
| Top 10 Fiber Channel Outbound Throughput (bits/sec)  | The 10 fiber channels with the highest outbound throughput |
| IO Module Ambient Temperature (C)     | The ambient temperature of the IO module |
| IO Module Internal Temperature (C) | The internal temperature of the IO module  |
| Rack Unit Temperature (C) | The temperature of the rack unit in celsius |
| Rack Available Memory (GB) | The available memory of a rack unit  |

**VNX Block Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| LUN Utilization (%)  | Percentage of LUN utilization  |
| Power (watts) | Power of the VNX Block in watts    |
| LUN IOPS (ops/sec) | IOPS of the LUN    |
| LUN Throughput (MB/sec) | Throughput of the LUN in megabytes per second |
| Storage Pool Used Capacity (%) | Percentage of used capacity of the Storage Pool    |
| Storage Processor Throughput (MB/sec) | Throughput of the Storage Processor in megabytes per second    |
| Storage Processor Read Hit Ratio (%) | Read hit ratio percentage of the Storage Processor |
| Storage Processor Write Hit Ratio (%) | Write hit ratio percentage of the Storage Processor |

**VNX File Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Latency (ms)  | Latency of the VNX File in milliseconds  |
| Network Throughput (MB/sec) | Network throughput in megabytes per second    |
| Disk IOPS (ops/sec) | IOPS of the VNX File disk    |
| Disk Throughput (MB/sec) | Throughput of the disk in megabytes per second    |
| CPU Utilization (%) | Percentage of CPU utilization    |
| Memory Utilization (%) | Percentage of memory utilization   |
| Highest Component Temperature (C) | Highest component temperature in celsius   |
| Average Component Temperature (C) | Average component temperature in celsius   |


**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| Nexus Ports in Error (%)  | The average percentage of nexus ports that are in an error state across all nexus switches |
| UCS Average Load (%)  | The average load percentage across fabric interconnects on all ucs connections |
| VNX Block Average LUN Utilization (%)  | The average LUN Utilization across all VNX Blocks |
| VNX Block Average LUN Throughput (bytes/second)  | The average LUN throughput across all VNX Blocks |
