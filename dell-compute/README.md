# Blue Medora Dell PowerEdge Plugin for New Relic

The **Blue Medora Dell PowerEdge Plugin for New Relic** allows you to monitor your Dell PowerEdge performance data from within the New Relic platform by pulling metrics in from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.

This guide includes instructions for installing and configuring the Blue Medora Dell PowerEdge Plugin for New Relic.

----

## Obtaining the Plugin
You can find the New Relic Dell PowerEdge plugin in the following locations:

- [New Relic Storefront](http://newrelic.com/plugins/blue-medora/429)
- [Plugin Central](https://rpm.newrelic.com/accounts/890835/plugins/directory/429)

## System Requirements

The Dell plugin connects to the supported Dell server(s) via iDRAC IP address using SNMP v1, v2c, or v3.  Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**
- A New Relic account

**Dell PowerEdge Plugin Requirements**
- **Hardware:** The plugin supports the following R-, M-, and T-series generation Dell server types: **12g** and **13g**
- **iDRAC Firmware:** Servers must be equipped with **iDRAC v7** or **v8** with **firmware version 1.57.57+**
- **SNMP 1, 2c, or 3** must be configured on all Dell servers
- **Credentials: SNMP read-only community string -or- SNMPv3 User Name and Password** (Least privileged user = read-only SNMP user)
- **A Blue Medora License.** A trial license will ship with the plugin. This license will remain effective for the duration of the Blue Medora beta trial period.
 

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
  ./npi install com.bluemedora.dell.compute
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

**NOTE:** You can add multiple objects to the “agents” array to monitor multiple Dell PowerEdge instances.

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| polling_interval_seconds | The number of seconds between each data collection. |
| instance_name | The name of your Dell PowerEdge instance that will appear in the User Interface |
| version | SNMP version acceptable values `v1`, `v2c` or `v3` for SNMP Version 1, Version 2, or Version 3 respectively |
| management_ip | IP address of Dell PowerEdge device |
| snmp_port | Optional field, port SNMP communicates over. Defaults to `161` |
| community_string | Only applicable if `version` is `v2c`. The SNMP community string required to connect |
| security_level | Only applicable if `version` is `v3`. Acceptable values are `auth_priv`, `auth_nopriv`, or `noauth_nopriv` |
| user | Only applicable if `version` is `v3`. SNMP user name |
| auth_password | Only applicable if `version` is `v3`. SNMP Authentication password. Only needed if `security_level` is `auth_priv` or `auth_nopriv` |
| privacy_password | Only applicable if `version` is `v3`. SNMP privacy password, Only needed if `security_level` is `auth_priv` |
| privacy_type | Only applicable if `version` is `v3`. SNMP encryption type. Acceptable values are `privAES256`, `privAES192`, `privAES128`, `privDES`, or `priv3DES` for  256 bit AES, 192 bit AES, 128 bit AES, DES, or 3-DES respectively |
| enable_insights | Indicates whether or not to send data to New Relic Insights for this instance. |


**Example:**

```
{
  "polling_interval_seconds": 60,
  "agents": [
    {
      "instance_name": "Your SNMP Version 2 Instance",
      "snmp_version": "v2 or v2c",
      "management_ip": "your_value_here",
      "community_string": "your_value_here",
      "snmp_port": 161,
      "enable_insights": "true"
    },
    {
      "instance_name": "Your SNMP Version 3 Instance",
      "snmp_version": "v3",
      "management_ip": "your_value_here",
      "user": "your_value_here",
      "auth_password": "your_value_here",
      "privacy_password": "your_value_here",
      "authentication_type": "authSHA or authMD5",
      "privacy_type": "privAES256 or privAES192 or privAES128 or privDES or priv3DES",
      "security_level": "auth_priv or auth_nopriv or noauth_nopriv",
      "enable_insights": "true"
    }
  ]
}
```

## Using the Plugin
For more information about navigating New Relic’s user interface, refer to their [Using a plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/using-plugins/using-plugin) section.

## Support Resources
For questions or issues regarding the Blue Medora Dell PowerEdge Plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Temperature Probes (C)  |  Temperature readings throughout system in celsius |
| Amperage Probes (amps) |  Current reading throughout system in amps  |
| Wattage Probes (watts) |  Power reading throughout system in watts  |
| Processor Status (%) |  Percentage of processors in different states  |
| Memory Status (%) |  Percentage of memory devices in different states  |
| Physical Disk Status (%)  |  Percentage of disks in different states  |
| Battery Status (%) |  Percentage of battery in different states |

**Disks**

| Metric Name  |  Description |
|:------------- |:-------------|
| Used Capacity (%)  |  Percentage of disk capacity used |
| Used Capacity (GB) |  Disk capacity that is used in gigabytes  |
| Total Capacity (GB) |  Disk total capacity in gigabytes  |


**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| Used Capacity (%)  |  Percentage of system capacity used  |
| Processor Ok (%) |  Percentage  of processors in state "Ok"  |
| Memory Ok (%) |  Percentage of memory in state "Ok"  |
| Battery Ok (%) |  Percentage of battery in state "Ok"  |
