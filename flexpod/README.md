# Blue Medora FlexPod Plugin for New Relic

The **Blue Medora FlexPod Plugin for New Relic** allows you to monitor your Flexpod performance data from within the New Relic platform by pulling metrics in from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.						

This guide includes instructions for installing and configuring the Blue Medora FlexPod Plugin for New Relic.
If you’re having a bad experience with one of our plugins, please get in touch and we’ll be happy to help you out.

----

## System Requirements

The FlexPod plugin collects data from NetApp by making REST calls to OnCommand API Services.  It collects data from Cisco UCS by making REST calls to the Cisco UCS Manager, and connects to Cisco Nexus hardware via SNMP v2, v2c, or v3. Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**

- A New Relic account (Sign up for a free account [here](http://newrelic.com).)

**FlexPod Plugin Requirements**

- **NetApp OnCommand ApiServices:** NetApp OnCommand API Services version 1.0 or 1.1 is required to collect data.  
- The plugin supports **Cisco UCS Manager 2.2+**
- The plugin supports SNMP v2, v2c, or v3 enabled **Cisco Nexus switch(es) 1k, 3k, 4k, 5k, 7k, and/or 9k**
- Java 1.7 or higher
- **A Blue Medora License.** A trial license will ship with the plugin that is valid for 14 days. To obtain a production license or get pricing information for the plugin, please contact sales@bluemedora.com.

----

## Installing the Plugin

We recommend using the New Relic Platform Installer for installing and running your Blue Medora plugins for New Relic.

The New Relic Platform Installer (NPI) is a command line tool that helps you easily download, configure, and manage New Relic Platform Plugins.  For more information, refer to the [Installing an NPI-compatible plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/installing-plugins/installing-npi-compatible-plugin).

Once the NPI tool has been installed, run the following command:

```
	./npi install com.bluemedora.netapp.flexpod
```	

**Note:** This command will take care of the creation of `newrelic.json` and `plugin.json` files described in the [Configuring the Plugin](#Configuring-the-Plugin) section.

Downloading the plugin binary (only use this link if you are not installing via NPI)
https://s3.amazonaws.com/bmibmtransfer/newrelic_netapp_flexpod_plugin-2.0.0_20160912_155548.tar.gz

###### [Download Plugin for Manual Installation](https://newrelic-bluemedora.s3.amazonaws.com/com-bluemedora-flexpod/newrelic_netapp_flexpod_plugin-3.0.0_20161206_185655.tar.gz)

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

Each FlexPod instance can be made up of several different components. Component objects in the "agents" array should share an "instance_name" if they are part of the same FlexPod system. Each component within a FlexPod system should have a unique "component_name", as well as a "component_type" that indicates the type of component. The following component types are recognized by the FlexPod plugin:

`nexus` - Cisco Nexus

`ucs` - Cisco UCS

`netapp` - NetApp API Services

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| polling_interval_seconds | The number of seconds between each data collection. |

**NetApp Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| instance_name | The name of your New Relic NetApp Storage instance that will appear in the User Interface |
| username | User name to log into NetApp Storage API Services |
| password | Password to log into NetApp Storage API Services |
| server | The hostname or ip address of the server |
| protocol | Either `http` or `https` |
| port | Optional parameter, port to connect to NetApp API Services |
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
#### java.lang.OutOfMemoryError

When running a plugin, a `java.lang.OutOfMemoryError` may occur if too much data is being processed for the system to handle. If that issues arises, you will need to modify the `java_args` field of the “master” `newrelic.json` file located in the npi base `config` directory.

`java_args` - `-Xmx128m` (-Xmxn specifies the maximum size, in bytes, of the memory allocation pool. This value must a multiple of 1024 greater than 2 MB. Append the letter k or K to indicate kilobytes, or m or M to indicate megabytes. The default value is chosen at runtime based on system configuration.)

- **Examples:**

- `-Xmx83886080`

- `-Xmx81920k`

- `-Xmx80m`



#### FATAL ERROR: JS Allocation failed - process out of memory

If you see `FATAL ERROR: JS Allocation failed - process out of memory` during installation, edit newrelic-npi/npi replacing `bin/node npi.js "$@"` with `bin/node --max-old-space-size=4096 npi.js "$@" #modified for 4gb memory`

----

## Support Resources
For questions or issues regarding the Blue Medora FlexPod Plugin for New Relic, visit http://support.bluemedora.com. 

----     

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Nexus Total Throughput (MB/sec)  | The total throughput of the system in megabytes per second |
| Nexus Port Status (%) | Percentage of systems port statuses (Up or Down) |
| UCS Fabric Interconnect Load (%) | The load percentage of the fabric interconnect |
| UCS Chassis Power (watts) | The power of the chassis in watts |
| UCS Blade Available Memory (GB)) | The memory available on a blade |
| UCS Blade Memory Speed (MHz) | The speed of memory on a blade |
| NetApp SVM Used Capacity (%) | Percentage of SVM used capacity |
| NetApp Aggregate Used Capacity (%) | Percentage of NetApp aggregate used capacity |
| NetApp System Latency (ms)  | The latency of the system |
| NetApp System IOPS (ops/sec)  | The iops per second of the system |

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

**NetApp Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| SVM Used Capacity (%)  | Percentage of the SVM used capacity  |
| Aggregate Used Capacity (%) | Percentage of the aggregate used capacity   |
| Volume Used Capacity (%) | Percentage of the used capacity of the Volume    |
| LUN Used Capacity (%) | Percentage of the used capacity of the LUN   |
| Disk Used Capacity (%) | Percentage of the used capacity of the Disk   |
| System Latency (ms) | System latency in milliseconds |
| System IOPS (ops/sec) | IOPS of the system |
| Volume Latency (ms) | Latency of the Volume in milliseconds   |
| Volume IOPS (ops/sec) | IOPS of the Volume |
| LUN Latency (ms) | Latency of the LUN in milliseconds |
| LUN IOPS (ops/sec) | IOPS of the LUN   |

**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| Nexus Ports in Error (%)  | The average percentage of nexus ports that are in an error state across all nexus switches |
| UCS Average Load (%)  | The average load percentage across fabric interconnects on all ucs connections |
| Netapp Average SVM Used Capacity (%)  | The average average SVM capacity used across all netapp systems |
| Netapp Average System latency (µs)  | The average average latency used across all netapp systems |
