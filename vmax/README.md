# Blue Medora Dell EMC VMAX Plugin for New Relic

The **Blue Medora Dell EMC VMAX Plugin for New Relic** allows you to monitor your Dell EMC VMAX performance data from within the New Relic platform by pulling metrics in from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.                       

This guide includes instructions for installing and configuring the Blue Medora Dell EMC VMAX Plugin for New Relic.
If you’re having a bad experience with one of our plugins, please get in touch and we’ll be happy to help you out.

----

## System Requirements

The Dell EMC VMAX plugin collects data by make REST calls to Unisphere for VMAX. Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**

- A New Relic account (Sign up for a free account [here](http://newrelic.com).)

**Dell EMC VMAX Plugin Requirements**

- Unisphere for VMAX
- EMC VMAX3 or EMC VMAX AFA
- Unisphere REST API Version 8.1+
- A least privieged user account of read-only

- **A Blue Medora License.** A trial license will ship with the plugin that is valid until November 15th, 2016. To obtain a production license or get pricing information for the plugin, contact sales@bluemedora.com.

----

## Installing the Plugin

We recommend using the New Relic Platform Installer for installing and running your Blue Medora plugins for New Relic.

The New Relic Platform Installer (NPI) is a command line tool that helps you easily download, configure, and manage New Relic Platform Plugins.  For more information, refer to the [Installing an NPI-compatible plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/installing-plugins/installing-npi-compatible-plugin).

Once the NPI tool has been installed, run the following command:

```
    ./npi install com.bluemedora.emc.vmax
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


**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name | Description |
|:------------- |:-------------|
| polling_interval_seconds | The number of seconds between each data collection. |
| instance_name | The name of your New Relic Dell EMC VMAX instance that will appear in the user interface |
| username | User name to log into Unisphere for VMAX system (least privieged user account of read-only) |
| password | Password to log into Unisphere for VMAX system |
| host | The hostname or ip address of the Unisphere for VMAX system |
| port | Optional parameter, port to connect to. Defaults to `8443` if not present |
| max_events | Optional parameter, the maximum number of events to collect per collection cycle, `1` to `2147483647`. Defaults to `10000` if not present |
| request_timeout | Optional parameter, number of milliseconds to wait for HTTP Requests to timeout, `1` to `2147483647`. Defaults to `15000` if not present |
| max_thread_count| Optional parameter, number of to use, `1` to `2147483647`. Defaults to `50` if not present |
| ssl_trust_store_path | Optional parameter, path the the SSL Trust Store for example, `path/to/store`. Defaults to Trust all certificates if not present |
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
For questions or issues regarding the Blue Medora Dell EMC VMAX Plugin for New Relic, visit http://support.bluemedora.com. 

----     

## Metrics Source Documentation

**VMAX Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Array Combined Latency (milliseconds)  | The Combined Latency for each monitored Array |
| Front End Director Host Throughput (bytes/second) | The Front End Director Host Throughput for each monitored Front End Director |
| Storage Group Throughput (bytes/second) | The Storage Group Throughput for each monitored Storage Group |
| Array Requests Rate (requests/second) | The Array Requests Rate for each monitored Array |

**Arrays**

| Metric Name  |  Description |
|:------------- |:-------------|
| Array Combined Latency (milliseconds)  | The Combined Latency for each monitored Array |
| Cache Hit Ratio (%)  | The Cache Hit Ratio for each monitored Array |
| Read Latency (milliseconds)  | The Read Latency for each monitored Array |
| Write Latency (milliseconds)  | The Write Latency for each monitored Array |
| Total Host IOPS (operations/second)  | The Total Host IOPS for each monitored Array |
| Backend IOPS (operations/second)  | The Backend IOPS for each monitored Array |
| I/O Operations Percent Read (%)  | The I/O Operations Percent Read for each monitored Array |
| I/O Operations Percent Write (%)  | The I/O Operations Percent Write for each monitored Array |
| Array Requests Rate (requests/second) | The Array Requests Rate for each monitored Array |

**Storage Groups**

| Metric Name  |  Description |
|:------------- |:-------------|
| Response Time (milliseconds)  | The Response Time for each monitored Storage Group |
| Total IOPS (operations/second)  | The Total IOPS for each monitored Storage Group |
| Backend Read IOPS (operations/second)  | The Backend Read IOPS for each monitored Storage Group |
| Backend Write IOPS (operations/second)  | The Backend Write IOPS for each monitored Storage Group |
| Cache Hit Ratio (%)  | The Cache Hit Ratio for each monitored Storage Group |
| Cache Miss Ratio (%)  | The Cache Miss Ratio for each monitored Storage Group |
| I/O Operations Percent Read (%)  | The I/O Operations Percent Read for each monitored Storage Group  |
| I/O Operations Percent Write (%)  | The I/O Operations Percent Write for each monitored Storage Group  |
| Total Host Throughput (bytes/second) | The Total Host Group Throughput for each monitored Storage Group |
| Host Writes (operations/second)  | The Host Writes for each monitored Storage Group |

**Volumes**

| Metric Name  |  Description |
|:------------- |:-------------|
| Allocated Space (%)  | The Allocated Space for each monitored Volume |
| Used Space (GB)  | The Used Space for each monitored Volume |
| Capacity (GB)  | The Capacity for each monitored Volume |

**Front End Directors**

| Busy Time (%)  | The Busy Time for each monitored Front End Director |
| Total Latency (milliseconds)  | The Total Latency for each monitored Front End Director |
| Cache Hit Ratio (%)  | The Cache Hit Ratio for each monitored Front End Director |
| Cache Hit Rate (hits/second)  | The Cache Hit Rate for each monitored Front End Director |
| Combined IOPS (operations/second)  | The Combined IOPS for each monitored Front End Director |
| Host Throughput (bytes/second) | The Host Throughput for each monitored Front End Director |
| Write Ratio (%)  | The Write Ratio for each monitored Front End Director |