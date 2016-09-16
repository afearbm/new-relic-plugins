# Blue Medora's New Relic Knobs and Levers Readme

Every Blue Medora Plugin for New Relic provides a similar set of configurable "Knobs and Levers" to ensure that each customer has control over the information reported to their Plugin instances and to their Insights account. 
This document describes how these Knobs and Levers operate, and how you can use them to fine-tune the behavior of any Blue Medora Plugin for New Relic.

**Note:** For further details on the configuration of each individual Plugin, please refer to the README file associate with each Plugin.

----

##Summary of Expected Behavior 

As a general rule of thumb, every Blue Medora Plugin for New Relic will send all the information that it collects to your New Relic account. Specific types of data can be "turned off" using the flags described below. 

Each Blue Medora Plugin for New Relic can send data to Plugin instances, or to your Insights account, or to both.

Because New Relic's `Plugin API` limits the type and amount of data that can be transmitted, the specific types of data that can be sent to Plugin instances is restricted to a subset of what can be sent to Insights. The Knobs and Levers described in this document can allow you to constrict that data even further.

Sending data to Insights requires some additional configuration in the `newrelic.json`, but the amount and type of data is only limited by the fine-tuning you apply to your `plugin.json`. For more details, please refer to the README file that for any individual Blue Medora Plugin.

Each Blue Medora Plugin for New Relic comes packaged with a `plugin.template.json` file that includes a complete listing of all data types that can be turned off.

Data is collected and sent at a predefined interval, but can be customized in the `plugin.json`, as described below.

----

##Common Flags in `plugin.json`

- **polling_interval_seconds:** The amount of time, in seconds, between consecutive attempts to collect data from the monitored system. If this attribute is omitted or if it contains an invalid value, a hidden default is used (usually 60).

- **send_to_plugin** refers to the data types that should be send to your Plugin instance. As explained above, this is usually a subset of what can be sent to Insights.

- **send_to_insights** refers to the data types that should be send to your Insights account. As explained above, this is dependent on some additional configuration to your `newrelic.json`.

- **notifications** refers to events reported by the monitored system. These can be reported as Insights events and will include severity information as well as a description of the event.

- **relationships** refers to the dependence or interaction between objects. These are typically defined as parent-child relationships, and can be reported under the “blue_medora_relationships” event type in Insights.

----

##Example Configurations

----

##"All True" ("Out-of-Box") Configuration
- `send_to_plugin`  is fully defined. All flags set to "true"
- `send_to_insights` is fully defined. All flags set to "true"

###Plugins Behavior
- Sends Entire Whitelist

###Insights Behavior
- If `newrelic.json` is not configured to send to Insights, logs "Insights is not configured. If you'd like to use Insights, see the plugin documentation for instructions."

  ```
  {  
    "polling_interval_seconds": 60,  
    "agents": [  
      {  
        "instance_name": "my-oracle",  
        "username": "oracle",  
        "password": "password",  
        "host": "my-oracle",  
        "port": 1521,  
        "send_to_plugin": {  
          "oracle_database": true,  
          "oracle_instance": true,  
          "oracle_table": true,  
          "oracle_query": true  
        },  
        "send_to_insights": {  
          "oracle_database": true,  
          "oracle_instance": true,  
          "oracle_table": true,  
          "oracle_query": true,  
          "notifications": true,  
          "relationships": true  
        }  
      }  
    ]  
  }
  ``` 

----

##"All False" Configuration
- `send_to_plugin`  is fully defined. All flags set to "false"
- `send_to_insights` is fully defined. All flags set to "false"

###Behavior
- Detects that all flags for both Plugins and Insights are "false"
- Does not attempt to collect data at all. 
- Logs "Plugin is configured to send no data. No collection attempted."

```
{
  "polling_interval_seconds": 60,
  "agents": [
    {
      "instance_name": "my-oracle",
      "username": "oracle",
      "password": "password",
      "host": "my-oracle",
      "port": 1521,
      "send_to_plugin": {
        "oracle_database": false,
        "oracle_instance": false,
        "oracle_table":    false,
        "oracle_query":    false
      },
      "send_to_insights": {
        "oracle_database": false,
        "oracle_instance": false,
        "oracle_table":    false,
        "oracle_query":    false,
        "notifications":   false,
        "relationships":   false
      }
    }
  ]
}
```

##"Mixed" Configuration
- `send_to_plugin`  is fully defined. Some flags set to "true"
- `send_to_insights` is fully defined. Some flags set to "true"

###Plugins Behavior
- Filters Whitelist. Sends only Resource Types whose flags are set to "true"

###Insights Behavior
- If `newrelic.json` is not configured to send to Insights, logs "Insights is not configured. If you'd like to use Insights, see the plugin documentation for instructions."
- Otherwise, filters everything.
- Sends Resource Types whose flags are set to "true"
- If "notifications" is set to "true", sends Events that are attached to Resources whose flags are set to "true"
- If "relationships" is set to "true", sends Relationships that involve a Resource whose flag is set to "true"

```
{
  "polling_interval_seconds": 60,
  "agents": [
    {
      "instance_name": "my-oracle",
      "username": "oracle",
      "password": "password",
      "host": "my-oracle",
      "port": 1521,
      "send_to_plugin": {
        "oracle_database": true,
        "oracle_instance": false,
        "oracle_table":    true,
        "oracle_query":    false
      },
      "send_to_insights": {
        "oracle_database": true,
        "oracle_instance": false,
        "oracle_table":    false,
        "oracle_query":    true,
        "notifications":   true,
        "relationships":   false
      }
    }
  ]
}
```
----

##"Partial" Configuration
- `send_to_plugin`  is partially defined. Some flags are omitted
- `send_to_insights` is partially defined. Some flags are omitted

###Plugins Behavior
- Omitted flags are assumed to be "true"
- Behaves as "All True" or "Mixed" Configuration

###Insights Behavior
- Omitted flags are assumed to be "true"
- Behaves as "All True" or "Mixed" Configuration

```
{
  "polling_interval_seconds": 60,
  "agents": [
    {
      "instance_name": "my-oracle",
      "username": "oracle",
      "password": "password",
      "host": "my-oracle",
      "port": 1521,
      "send_to_plugin": {
        "oracle_database": false
      },
      "send_to_insights": {
        "oracle_query":    true,
        "notifications":   false
      }
    }
  ]
}
```
----

##"Empty" Configuration
- `send_to_plugin`  is an empty hash
- `send_to_insights` is an empty hash

###Plugins Behavior
- Omitted flags are assumed to be "true"
- Behaves as "All True" Configuration

###Insights Behavior
- Omitted flags are assumed to be "true"
- Behaves as "All True" Configuration

```
{
  "polling_interval_seconds": 60,
  "agents": [
    {
      "instance_name": "my-oracle",
      "username": "oracle",
      "password": "password",
      "host": "my-oracle",
      "port": 1521,
      "send_to_plugin":  { },
      "send_to_insights": { }
    }
  ]
}
```

----

##"Simple" Configuration
- `send_to_plugin`  is a boolean
- `send_to_insights` is a boolean

###Plugins Behavior
- All flags take the value of the boolean
- Behaves as "All True" or "All False" Configuration

###Insights Behavior
- All flags take the value of the boolean
- Behaves as "All True" or "All False" Configuration

```
{
  "polling_interval_seconds": 60,
  "agents": [
    {
      "instance_name": "my-oracle",
      "username": "oracle",
      "password": "password",
      "host": "my-oracle",
      "port": 1521,
      "send_to_plugin":  true,
      "send_to_insights": false
    }
  ]
}
```
----

##"Mixed Simple" Configuration
- `send_to_plugin`  is a boolean
- `send_to_insights` is a hash with per resource type boolean

###Plugins Behavior
- All flags take the value of the boolean
- Behaves as "All True" or "All False" Configuration

###Insights Behavior
- All flags take the value of the boolean
- Behaves as "All True" or "All False" Configuration

```
{
  "polling_interval_seconds": 60,
  "agents": [
    {
      "instance_name": "my-oracle",
      "username": "oracle",
      "password": "password",
      "host": "my-oracle",
      "port": 1521,
      "send_to_plugin":  false,
      "send_to_insights": {
        "oracle_database": true,
        "oracle_instance": false,
        "oracle_table":    false,
        "oracle_query":    true,
        "notifications":   true,
        "relationships":   false
      }
    }
  ]
}
```

----

##"Minimal" Configuration
- `send_to_plugin`  omitted
- `send_to_insights` omitted

###Plugins Behavior
- Omitted flags are assumed to be "true"
- Behaves as "All True" Configuration

###Insights Behavior
- Omitted flags are assumed to be "true"
- Behaves as "All True" Configuration

`plugin.json`:
```
{
  "polling_interval_seconds": 60,
  "agents": [
    {
      "instance_name": "my-oracle",
      "username": "oracle",
      "password": "password",
      "host": "my-oracle",
      "port": 1521
    }
  ]
}
```

----

##"Invalid" Configuration
- `send_to_plugin`  is not a boolean or a hash, is a hash with non-boolean values
- `send_to_insights` is not a boolean or a hash, is a hash with non-boolean values

###Plugins Behavior
- Invalid configuration detected at process startup, logs "Invalid configuration..."

###Insights Behavior
- Invalid configuration detected at process startup, logs "Invalid configuration..."

```
{
  "polling_interval_seconds": 60,
  "agents": [
    {
      "instance_name": "my-oracle",
      "username": "oracle",
      "password": "password",
      "host": "my-oracle",
      "port": 1521,
      "send_to_plugin":  12345,
      "send_to_insights": { 
        "oracle_database": hi
      }
    }
  ]
}
```

----

##Example newrelic.json with Insights configured
```
{
    "license_key": "483c8236c21480b52319ed9a2ebd950810670a3f",
    "log_level": "debug",
    "log_file_name": "newrelic_plugin.log",
    "log_file_path": "logs",
    "insights_api_key": "w09iHADYuqAaTP9Mu76v5d7hF7tgZD0i",
    "insights_account_id": "1234567",
    "insights_use_ssl": true
}
```
