# New Relic Plugin for HP Rack Servers

The **Blue Medora New Relic Plugin for HP Rack Servers** allows you to monitor your HP Rack Servers performance data from within the New Relic platform by pulling metrics in from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.						
This guide includes instructions for installing and configuring the Blue Medora New Relic Plugin for HP Rack Servers.

----

## Obtaining the Plugin
You can find the New Relic Plugin for HP Rack Servers in the following locations:

- [New Relic Storefront](http://newrelic.com/plugins/blue-medora/429)
- [Plugin Central](https://rpm.newrelic.com/accounts/890835/plugins/directory/429)	

## System Requirements

The HP Rack plugin connects to the supported HP ProLiant server(s) via an HP iLO address.  Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**

- A New Relic account. Sign up for a free account [here](http://newrelic.com)

**HP Rack Plugin Requirements**

- **A Blue Medora License.** A trial license will ship with the plugin. This license will remain effective for the duration of the Blue Medora beta trial period. 
- **Enabled iLO REST Client.** Ensure the monitored Gen 8+ HP ProLiant Server has an iLO device with REST API enabled.

----

## Installing the Plugin

This plugin can be installed one of the following ways:

- Using the New Relic Platform Installer
- Installing the Plugin Manually

### Using the New Relic Platform Installer

The New Relic Platform Installer (NPI) is a command line tool that helps you easily download, configure and manage New Relic Platform Plugins.  For more information, refer to the [Installing an NPI-compatible plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/installing-plugins/installing-npi-compatible-plugin).

**NOTE:** We recommend using the New Relic Platform Installer for installing and running your Blue Medora plugins for New Relic. Issues can arise by running a plugin directly in the foreground (e.g., when the machine reboots, the process will not be started again). The NPI automates much of these processes.

Once the NPI tool has been installed, run the following command:

```
  ./npi install com.bluemedora.hp.rack
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

#### Configuring the plugin.template.json file: 

The second file, plugin.template.json, contains data specific to each plugin (e.g., a list of hosts and port combinations for what you are monitoring). Templates for both of these files should be located in the ‘config’ directory in your extracted plugin folder.

Make a copy of this template and rename it to plugin.json. Shown below is an example of the plugin.json file’s contents.

**NOTE** - You can add multiple objects to the “agents” array to monitor multiple HP Rack Servers instances.

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| polling_interval_seconds | The number of seconds between each data collection. |
| instance_name | Alias for the name of your HP iLO instance that will appear in the User Interface |
| username | HP iLO REST API Username used to connect to HP ProLiant device. |
| password | HP iLO REST API Password used to connect to HP ProLiant device. |
| host | IP address or hostname of HP iLO device |
| port | Port used to connect to the HP iLO REST API.  |
| enable_insights | Indicates whether or not to send data to New Relic Insights for this instance. |

**NOTE:** There are optional fields if `enable_insights` is `true` that allow specific event types to be toggled whether they send data to Insights. 
Theses fields are listed below and valid values are `true` or `false`:

* `enable_insights_for_port`
* `enable_insights_for_rack`
* `enable_insights_for_system`
* `enable_insights_for_fan`
* `enable_insights_for_power_supply`

**Example:**

```
{
  "agents": [
    {
      "instance_name": "YOUR_VALUE_HERE",
      "username": "YOUR_VALUE_HERE",
      "password": "YOUR_VALUE_HERE",
      "host": "YOUR_VALUE_HERE",
      "port": "YOUR_VALUE_HERE",
      "enable_insights": "true"
    }
  ]
}
```

## Using the Plugin
For more information about navigating New Relic’s user interface, refer to their [Using a plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/using-plugins/using-plugin) section.

## Support Resources
For questions or issues regarding the HP Rack Servers Plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**System Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| CPU Temperature (C)  |  Temperature readings of the CPU in celsius |
| Memory Temperature (C) |  Temperature readings of the memory in celsius |
| Power Consumed (watts) |  Power reading throughout system in watts  |
| Fan Status (%) |  Percentage of fans in a certain state  |
| Network Adapter Status (%) |  Percentage of network adapters in a certain state  |
| Port Status (%)  |  Percentage of ports in a certain state  |

**Network Adapters**

| Metric Name  |  Description |
|:------------- |:-------------|
| Port Status (%)  |  Percentage of ports in a certain state  |
| Good Receives (frames/sec) |  Number of frames successfully received  |
| Bad Receives (frames/sec) |  Number of frames unsuccessfully received  |
| Good Transmits (frames/sec) |  Number of frames successfully transmitted  |
| Bad Transmits (frames/sec) |  Number of frames unsuccessfully transmitted  |

**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| Max CPU Temperature (celsius)  |  Max Temperature readings of the CPU in celsius |
| Max Memory Temperature (celsius) |  Max Temperature readings of the memory in celsius  |
| Healthy Ports (%)  |  Percentage of ports in an "Ok" state  |
| Total Good Receives (frames/sec) |  Total number of Good Received frames received  |
| Total Good Transmits (frames/sec) |  Total number of Good Transmits frames transmitted |
