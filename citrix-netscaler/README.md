# Blue Medora Citrix NetScaler Plugin for New Relic

The **Blue Medora Citrix NetScaler Plugin for New Relic** allows you to monitor your Citrix NetScaler load balancing performance data from within the New Relic platform by pulling in metrics from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.           

This guide includes instructions for installing and configuring the Blue Medora Citrix NetScaler Plugin for New Relic.

----


## Obtaining the Plugin
You can find the New Relic Citrix NetScaler plugin in the following locations:

- [New Relic Storefront](http://newrelic.com/plugins/blue-medora/488)
- [Plugin Central](https://rpm.newrelic.com/accounts/890835/plugins/directory/488)

## System Requirements

The Citrix NetScaler plugin connects to the Citrix NetScaler Appliance using REST.  Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**
- A New Relic account

**Citrix NetScaler Plugin Requirements**
- **NetScaler Version:** The plugin requires Citrix NetScaler MPX or VPX, Version 10.x+
- In the NetScaler user interface, you must create a user account for monitoring purposes that has a **System Command Policy** of read-only.
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
  ./npi install com.bluemedora.citrix.netscaler
``` 

**Note:** This command will take care of the creation of `newrelic.json` and `plugin.json` files described in the [Configuring the Plugin](#Configuring-the-Plugin) section.

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
Make a copy of this template and rename it to `newrelic.json`. Listed below are the configurable fields within the `newrelic.json file`:

**New Relic License Key** - The only required field in the `newrelic.json` file is the License Key. This unique identifier informs New Relic about the specific account tied to the plugin. For more information on the License Key, [refer to the New Relic License key documentation](https://docs.newrelic.com/docs/accounts-partnerships/accounts/account-setup/license-key).

```
{
   “license_key”: “YOUR_LICENSE_KEY_HERE”
}
```

**Logging Configuration**

By default Platform plugins will have their logging turned on; however, you can modify these settings with the following configurations:

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

**Proxy Configuration**

If you are running your plugin from a machine that runs outbound traffic through a proxy, you can use the following optional configurations in your newrelic.json file:

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

**NOTE:** You can add multiple objects to the “agents” array to monitor multiple Citrix NetScaler instances. 

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| instance_name | The name of your New Relic Citrix NetScaler instance that will appear in the User Interface |
| username | Username to make REST calls to your Citrix NetScaler Appliance |
| password | Password to make REST calls to your Citrix NetScaler Appliance |
| protocol | Protocol ("http" or "https") used to make REST calls to your Citrix NetScaler Appliance |
| netscaler_ip | IP address or fully qualified host name of your Citrix NetScaler Appliance |

**Example:**

```
{
  "agents": [
    {
      "instance_name": "Your Citrix NetScaler Appliance collecting data via http",
      "username": "your_value_here",
      "password": "your_value_here",
      "protocol": "http"
      "netscaler_ip": "your_value_here",
    },
    {
      "instance_name": "Your Citrix NetScaler Appliance collecting data via https",
      "username": "your_value_here",
      "password": "your_value_here",
      "protocol": "https"
      "netscaler_ip": "your_value_here",
    }
  ]
}
```

## Using the Plugin
For more information about navigating New Relic’s user interface, refer to their [Using a plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/using-plugins/using-plugin) section.

## Support Resources
For questions or issues regarding the Blue Medora Citrix NetScaler Plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**Summary Metrics**

| Metric Name  |  Description |
|:------------- |:-------------|
| Rx Data Rate (bps) | Number of bits per second received by the NetScaler appliance |
| Tx Data Rate (bps) | Number of bits per second sent by the NetScaler appliance |
| Management CPU Usage (%) | Management CPU utilization percentage |
| Memory Usage (%) | Percentage of memory utilization on NetScaler |

**Appliance Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Tx Throughput (bps) | Number of bits per second sent by the NetScaler appliance |
| Rx Throughput (bps) | Number of bits per second received by the NetScaler appliance |
| HTTP Compression Ratio | Ratio of the compressible data received from the server to the compressed data sent to the client |
| Resident CPU Load (%) | Average CPU utilization percentage if more than 1 CPU is present |
| Packet Engines CPU Load (%) | Average CPU utilization percentage for all packet engines excluding management PE |
| Management CPU Load (%) | Management CPU utilization percentage |
| Memory Load (%) | Percentage of memory utilization on NetScaler |

**Virtual Servers**

| Metric Name  |  Description |
|:------------- |:-------------|
| Hits (per sec) | Number of times that the service has been provided per second |
| Request Data Rate (bytes/sec) | Number of request bytes received per second by this virtual server |
| Response Data Rate (bytes/sec) | Number of response bytes received per second by this virtual server |
| Services Up (%) | Percentage of weights of active Services divided by all Services on this virtual server |
| Surge Queue Length (requests) | Number of requests in the surge queue |

**Services**

| Metric Name  |  Description |
|:------------- |:-------------|
| Throughput (bps) | Number of bytes received or sent by this service (Mbps) |
| Request Data Rate (bytes/sec) | Number of request bytes received per second by this service |
| Response Data Rate (bytes/sec) | Number of response bytes received per second by this service |
| Client Connections | Number of current client connections |
| Server Connections | Number of current connections to the actual servers behind the Virtual Server |
| Latency (ms)  | Average time to first byte between the NetScaler appliance and the server |
| Surge Queue Length (requests) | Number of requests in the surge queue |
