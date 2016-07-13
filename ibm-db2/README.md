# Blue Medora IBM DB2 Plugin for New Relic

The **Blue Medora IBM DB2 Plugin for New Relic** allows you to monitor your DB2 performance data from within the New Relic platform by pulling metrics in from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.						

This guide includes instructions for installing and configuring the Blue Medora IBM DB2 Plugin for New Relic.

----

## Obtaining the Plugin
You can find the New Relic IBM DB2 plugin in the following locations:

- [New Relic Storefront](http://newrelic.com/plugins/blue-medora/432)
- [Plugin Central](https://rpm.newrelic.com/accounts/890835/plugins/directory/432)

## System Requirements

The IBM DB2 Plugin connects to a DB2 database using a JDBC connection. Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**
- A New Relic account

**IBM DB2 Plugin Requirements**

- **IBM DB2 Version:** This plugin requires IBM DB2 Versions 9.5+
- **A Blue Medora License.** A trial license will ship with the plugin. This license will remain effective for the duration of the Blue Medora beta trial period. 

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
	./npi install com.bluemedora.vce.vblock
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

Each Vblock instance can be made up of several different components. Component objects in the "agents" array should share an "instance_name" if they are part of the same Vblock system. Each component within a Vblock system should have a unique "component_name", as well as a "component_type" that indicates the type of component. The following component types are recognized by the Vblock plugin:

`nexus` - Cisco Nexus

`ucs` - Cisco UCS

`vnxblock` - EMC VNX Block

`vnxfile` - EMC VNX File

**Fields**

**EMC VNX File Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| instance_name | The name of your New Relic VNX File instance that will appear in the User Interface |
| primary_control_station  | The IP or domain name of the Primary Control Station for VNX File |
| secondary_control_station | The IP or domain name of the Secondary Control Station for VNX File |
| username | User name to log into the Control Stations |
| password | Password to log into the Control Stations |
| nas_path | Path to the NAS_DB configuration files location.  Usually `/nas`. |
| sample_seconds | Query interval parameter.  Must be an integer 1 or greater | |

**EMC VNX Block Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| instance_name | The name of your New Relic VNX Block instance that will appear in the User Interface |
| spa_host | The IP or domain name of storage processor A for the VNX Block array |
| spb_host | The IP or domain name of storage processor B for the VNX Block array |
| username | User name to connect to NaviSecCLI |
| password | Password to connect to NaviSecCLI |
| naviseccli_path | Full path to NaviSecCLI executable on the host where this target is being installed |
| scope | An integer of `0` for global or `1` for local |

**Cisco UCS Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| instance_name | The name of your New Relic Cisco UCS instance that will appear in the User Interface |
| username | User name to log into UCS Manager |
| password | Password to log into UCS Manager |
| host | The hostname or ip address of UCS Manager |
| protocol | Either `http` or `https` |
| port | Optional parameter, port to connect to UCS Manager |

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

**Example:**

```
{
  "agents": [
    {
      "instance_name": "Your Vblock Instance",
      "component_type": "nexus",
      "component_name": "Nexus Component Using SNMP Version 2",
      "version": "v2 or v2c",
      "management_ip": "your_value_here",
      "community_string": "your_value_here",
      "snmp_port": 161
    },
    {
      "instance_name": "Your Vblock Instance",
      "component_type": "ucs",
      "component_name": "Your UCS Component",
      "username": "your_value_here",
      "password": "your_value_here",
      "host": "your_value_here",
      "protocol": "http or https"
    },
    {
      "instance_name": "Your Vblock Instance",
      "component_type": "vnxblock",
      "component_name": "Your VNX Block Component",
      "spa_host": "your_value_here",
      "spb_host": "your_value_here",
      "username": "your_value_here",
      "password": "your_value_here",
      "naviseccli_path": "/opt/Navisphere/bin",
      "scope": 0
    },
    {
      "instance_name": "Your Vblock Instance",
      "component_type": "vnxfile",
      "component_name": "Your VNX File Component",
      "primary_control_station": "your_value_here",
      "secondary_control_station": "your_value_here",
      "username": "your_value_here",
      "password": "your_value_here",
      "nas_path": "/nas",
      "sample_seconds": 3
    },

    {
      "instance_name": "Your Big Vblock Instance",
      "component_type": "nexus",
      "component_name": "Nexus Component Using SNMP Version 2",
      "version": "v2 or v2c",
      "management_ip": "your_value_here",
      "community_string": "your_value_here",
      "snmp_port": 161
    },
    {
      "instance_name": "Your Big Vblock Instance",
      "component_type": "nexus",
      "component_name": "Another Nexus Component Using SNMP Version 3",
      "user": "your_value_here",
      "auth_password": "your_value_here",
      "privacy_password": "your_value_here",
      "authentication_type": "authSHA or authMD5",
      "privacy_type": "privAES256 or privAES192 or privAES128 or privDES or priv3DES",
      "security_level": "auth_priv or auth_nopriv or noauth_nopriv"
    },
    {
      "instance_name": "Your Big Vblock Instance",
      "component_type": "ucs",
      "component_name": "Your UCS Component",
      "username": "your_value_here",
      "password": "your_value_here",
      "host": "your_value_here",
      "protocol": "http or https"
    },
    {
      "instance_name": "Your Big Vblock Instance",
      "component_type": "ucs",
      "component_name": "Another UCS Component",
      "username": "your_value_here",
      "password": "your_value_here",
      "host": "your_value_here",
      "protocol": "http or https"
    },
    {
      "instance_name": "Your Big Vblock Instance",
      "component_type": "vnxblock",
      "component_name": "Your VNX Block Component",
      "spa_host": "your_value_here",
      "spb_host": "your_value_here",
      "username": "your_value_here",
      "password": "your_value_here",
      "naviseccli_path": "/opt/Navisphere/bin",
      "scope": 0
    },
    {
      "instance_name": "Your Big Vblock Instance",
      "component_type": "vnxblock",
      "component_name": "Another VNX Block Component",
      "spa_host": "your_value_here",
      "spb_host": "your_value_here",
      "username": "your_value_here",
      "password": "your_value_here",
      "naviseccli_path": "/opt/Navisphere/bin",
      "scope": 0
    },
    {
      "instance_name": "Your Big Vblock Instance",
      "component_type": "vnxfile",
      "component_name": "Your VNX File Component",
      "primary_control_station": "your_value_here",
      "secondary_control_station": "your_value_here",
      "username": "your_value_here",
      "password": "your_value_here",
      "nas_path": "/nas",
      "sample_seconds": 3
    },
    {
      "instance_name": "Your Big Vblock Instance",
      "component_type": "vnxfile",
      "component_name": "Another VNX File Component",
      "primary_control_station": "your_value_here",
      "username": "your_value_here",
      "password": "your_value_here",
      "nas_path": "/nas",
      "sample_seconds": 3
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
For questions or issues regarding the Blue Medora IBM DB2 plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Nexus Total Throughput (MB/sec)  | The total throughput of the system in megabytes per second |
| Nexus Port Status (%) | Percentage of the systems port statuses (Up or Down) |
| UCS Fabric Interconnect Load (%) | Load percentage of the fabric interconnect     |
| UCS Chassis Power (watts) | Power of the chassis in watts    |
| UCS Blade Memory Used Capacity (%) | Percentage of memory used capacity of the blade    |
| UCS Rack Unit Memory Used Capacity (%) | Percentage of memory used capacity of the rack unit  |
| VNX Block LUN Used Capacity (%) | Percentage of used capacity of the VNX Block LUN  |
| VNX Block LUN IOPS (ops/sec) | IOPS of the VNX Block IOPS  |
| VNX Block LUN Throughput (MB/sec) | Throughput of the VNX Block LUN in megabytes per second  |
| VNX File Latency | Latency of the VNX File |

**Nexus Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Port Status (%)  | Percentage of the port status (Up or Down)  |
| Inbound Packets (packets/sec) | Inbound packets per second    |
| Outbound Packets (packets/sec) | Outbound packets per second   |
| Inbound Throughput (MB/sec) | Inbound throughput in megabytes per second   |
| Outbound Throughput (MB/sec) | Outbound throughput in megabytes per second    |

**UCS Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Fabric Interconnect Load (%)  | Load percentage of the fabric interconnect  |
| Chassis Input Power (watts) | Input power of the chassis in watts    |
| Chassis Output Power (watts) | Output power of the chassis in watts   |
| Rack Memory Used Capacity (%) | Percentage of memory used capacity of the rack   |
| Blade Memory Used Capacity (%) | Percentage of memory used capacity of the blade    |
| Fabric Interconnect Inbound Throughput (MB/sec) | Inbound throughput of the fabric interconnect in megabytes per second    |
| Fabric Interconnect Outbound Throughput (MB/sec) | Outbound throughput of the fabric interconnect in megabytes per second    |
| IO Module Inbound Throughput (MB/sec) | Inbound throughput of the IO module in megabytes per second    |
| IO Module Outbound Throughput (MB/sec) | Outbound throughput of the IO module in megabytes per second   |
| IO Module Temperature (C) | Temperature of the IO module in celsius   |
| Rack Unit Temperature (C) | Temperature of the rack unit in celsius   |

**VNX Block Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| LUN Utilization (%)  | Percentage of LUN utilization  |
| Power (watts) | Power of the VNX Block in watts    |
| LUN IOPS (ops/sec) | IOPS of the LUN    |
| LUN Throughput (MB/sec) | Throughput of the LUN in megabytes per second |
| Storage Pool Used Capacity (%) | Percentage of used capacity of the Storage Pool    |
| Storage Processor Throughput (MB/sec) | Throughput of the Storage Processor in megabytes per second    |
| Read Hit Ratio (%) | Read hit ratio percentage    |
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
