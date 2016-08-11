# New Relic Plugins: Knobs and Levers Behavior

As a supplement to the README files specific to each Blue Medora New Relic Plugin, which help get a user up-and-running with a plugin, this "Knobs and Levers Behavior" README has been created to help guide you in fine-tuning the behavior of your plugin based on your needs/preferences.

----

##Summary of Expected Behavior 

- "Everything" is sent unless otherwise specified
- For Plugins, "Everything" means all Resource Types and Metric Types defined in the "Whitelist"
- For Insights, "Everything" means everything collected by the data collector, including all Resources, Metrics, Events, and Relationships

----

##Definitions

- **plugin.json:** The configuration file for defining connections to monitored systems. All code examples below are of plugin.json, unless otherwise specified.

- **newrelic.json:** The configuration file for defining account level information. The "insights_api_key", "insights_account_id", and "insights_use_ssl" attributes are defined by Blue Medora and are required for Insights functionality.

- **Whitelist:** A list of specific Resource Types and Metric Types that may be sent to the New Relic Plugin. This list is defined by the developer and may not be expanded by the user. However, it can be restricted as shown below. This list has no effect on what is sent to Insights.

- **Polling Interval:** The amount of time between consecutive attempts to collect data from the monitored system. This can be customized with the "polling_interval_seconds" attribute in plugin.json. If this attribute is omitted or if it contains an invalid value, a hidden default is used (usually 60).

- **“notifications”** refers to events reported by the monitored system. These can be reported as Insights events and will include severity information as well as a description of the event.

- **“relationships”** refers to the dependence or interaction between objects. These are typically defined as parent-child relationships, and can be reported under the “blue_medora_relationships” event type in Insights.

----

##"All True" ("Out-of-Box") Configuration
- "send_to_plugin"  is fully defined. All flags set to "true"
- "send_to_insights" is fully defined. All flags set to "true"

###Plugins Behavior
- Sends Entire Whitelist

###Insights Behavior
- Sends Everything, if newrelic.json is configured. 
- Otherwise, logs "Insights not configured. See User Guide to configure."

```{
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
        
        "oracle_table":    true,
        
        "oracle_query":    true
        
      },
      
      "send_to_insights": {
      
        "oracle_database": true,
        
        "oracle_instance": true,
        
        "oracle_table":    true,
        
        "oracle_query":    true,
        
        "notifications":   true,
        
        "relationships":   true
        
      }
      
    }
    
  ]
  
}```

----

##"All False" Configuration
- "send_to_plugin"  is fully defined. All flags set to "false"
- "send_to_insights" is fully defined. All flags set to "false"

###Behavior
- Detects that all flags for both Plugins and Insights are "false"
- Does not attempt to collect data at all. 
- Logs "Plugin is configured to send no data. No collection attempted."

```{
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
- "send_to_plugin"  is fully defined. Some flags set to "true"
- "send_to_insights" is fully defined. Some flags set to "true"

###Plugins Behavior
- Filters Whitelist. Sends only Resource Types whose flags are set to "true"

###Insights Behavior
- If newrelic.json is not configured, logs "Insights not configured. See User Guide to configure."
- Otherwise, filters everything.
- Sends Resource Types whose flags are set to "true"
- If "notifications" is set to "true", sends Events that are attached to Resources whose flags are set to "true"
- If "relationships" is set to "true", sends Relationships that involve a Resource whose flag is set to "true"

```{
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
- "send_to_plugin"  is partially defined. Some flags are omitted
- "send_to_insights" is partially defined. Some flags are omitted

###Plugins Behavior
- Omitted flags are assumed to be "true"
- Behaves as "All True" or "Mixed" Configuration

###Insights Behavior
- Omitted flags are assumed to be "true"
- Behaves as "All True" or "Mixed" Configuration

```{
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
- "send_to_plugin"  is an empty hash
- "send_to_insights" is an empty hash

###Plugins Behavior
- Omitted flags are assumed to be "true"
- Behaves as "All True" Configuration

###Insights Behavior
- Omitted flags are assumed to be "true"
- Behaves as "All True" Configuration

```{
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
}```

----

##"Simple" Configuration
- "send_to_plugin"  is a boolean
- "send_to_insights" is a boolean

###Plugins Behavior
- All flags take the value of the boolean
- Behaves as "All True" or "All False" Configuration

###Insights Behavior
- All flags take the value of the boolean
- Behaves as "All True" or "All False" Configuration

```{
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
}```
----

##"Mixed Simple" Configuration
- "send_to_plugin"  is a boolean
- "send_to_insights" is a hash with per resource type boolean

###Plugins Behavior
- All flags take the value of the boolean
- Behaves as "All True" or "All False" Configuration

###Insights Behavior
- All flags take the value of the boolean
- Behaves as "All True" or "All False" Configuration

```{
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
}```

----

##"Minimal" Configuration
- "send_to_plugin"  omitted
- "send_to_insights" omitted

###Plugins Behavior
- Omitted flags are assumed to be "true"
- Behaves as "All True" Configuration

###Insights Behavior
- Omitted flags are assumed to be "true"
- Behaves as "All True" Configuration

plugin.json
```{
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
}```

----

##"Invalid" Configuration
- "send_to_plugin"  is not a boolean or a hash, is a hash with non-boolean values
- "send_to_insights" is not a boolean or a hash, is a hash with non-boolean values

###Plugins Behavior
- Invalid configuration detected at process startup, logs "Invalid configuration..."

###Insights Behavior
- Invalid configuration detected at process startup, logs "Invalid configuration..."

```{
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
}```

----

##Example newrelic.json with Insights configured
```{
    "license_key": "483c8236c21480b52319ed9a2ebd950810670a3f",
    "log_level": "debug",
    "log_file_name": "newrelic_plugin.log",
    "log_file_path": "logs",
    "insights_api_key": "w09iHADYuqAaTP9Mu76v5d7hF7tgZD0i",
    "insights_account_id": "1234567",
    "insights_use_ssl": true
}```
