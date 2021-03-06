[![Build status](https://ci.appveyor.com/api/projects/status/cvbh3t0775gb6rj8?svg=true)](https://ci.appveyor.com/project/Nordes/newrelic-azurestoragequeue-agent)
# NewRelic Azure Storage Queue Agent
NewRelic Azure Storage Queue Agent is a NewRelic Plugin which monitors the size of the queues in one or more Windows Azure Storage accounts. 
Use it to see how the sizes of queues evolve over time.

* The plugin is NPI-compatible. *(On it's way)*
* When there's errors contacting NewRelic or Azure Storage Queue, an eventlog is created in Windows EventLogs ournals

# NewRelic Plugin Dashboard example
In the example, you will see statistics about queues being filled and consumed with a WebJob (Azure). FYI, the poison queue is where the errors are located.
![plugin-dashboard](https://cloud.githubusercontent.com/assets/446572/23294952/4ca15e1c-fa6e-11e6-918e-a9d89cd2ab11.png)

# Metrics
The metrics sent to NewRelic are the following

| Metric format | Description |
| :------------ | :---------- |
| Component/Queues/`Account Name from plugin.json`/all/`Queue Name`/size[messages] | All the Azure Storage Queues will be added in the `all` collection |
| Component/Queues/`Account Name from plugin.json`/groups/`Group Name`/`Queue Name`/size[messages] | Only Queues fulfilling the regex group setting will be added to the `Group Name` collection |

# Building and starting the pluging all by yourself
1. Build the solution `src/ScalableBytes.NewRelic.AzureStorageQueueSize.Plugin.sln` using Visual Studio 2015 or higher (C# 6)
2. Open the folder Bin\Release
3. Edit and rename `./config/newrelic.template.json` to `./config/newrelic.json` and replace the __[NEW_RELIC_LICENSE_KEY]__ with your NewRelic license key.
4. Edit and rename `./config/plugin.template.json` to `./config/plugin.json` and add your Windows Azure Storage accounts.
5. Execute _plugin.exe_ to start monitoring.

# Install as Windows Service
To install the plug-in as a Windows Service, execute `plugin.exe install`. This will add it was a Windows Service named _ScalableBytes.NewRelic.AzureStorageQueueSize.Plugin_ which will start automatically when windows start.

# Example of configuration 
**N.B.:** _If you modify the configuration while the service is running, you will need to restart the service_

## ./config/plugin.json
```javascript
{
  // Mandatory root
  "agents": [
    // App configuration
    {
      "name": "App Production",
      // Storage(s) configuration
      "storageAccounts": [
        {
          "name": "Account name",
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=app***;AccountKey=d0Wo************;BlobEndpoint=https://app***.blob.core.windows.net/;QueueEndpoint=https://app***.queue.core.windows.net/;TableEndpoint=https://app****.table.core.windows.net/;FileEndpoint=https://app***.file.core.windows.net/;"
        }
    }
}
```

### Configuration
| Plugin.Config                                      | Type   | Description                                            |
| :------------------------------------------------- |:------:| :------------------------------------------------------|
| agents                                             | array  | Define the agents that will run in the background. Could be one per storage account (Description available at https://github.com/newrelic-platform/newrelic_dotnet_sdk) |
| agents[*0*].name                                   | string | Define the agent name, by example *MyApp-Production*   |
| agents[*0*].storageAccounts                        | array  | Define the list of storage account you want to pull the statistics |
| agents[*0*].storageAccounts[*0*].name              | string | Define the account name that will be displayed in NewRelic. This is not the official storage account name |
| agents[*0*].storageAccounts[*0*].connectionString  | string | Define the connection string to the azure storage account |
| agents[*0*].storageAccounts[*0*].groups            | array  | *(Optional)* Define grouping we want to capture         |
| agents[*0*].storageAccounts[*0*].groups[*0*].name  | string | Gives a name to the group that will be sent to NewRelic |
| agents[*0*].storageAccounts[*0*].groups[*0*].regex | string | Define the regex to capture the group based on the queue name |

## ./config/newrelic.json
```javascript
{
  "license_key": "NEW_RELIC_LICENSE_KEY"
}
```

### Configuration
| Plugin.Config                                     | Type   | Description                                            |
| :------------------------------------------------ |:------:| :------------------------------------------------------|
| license_key                                       | string | NewRelic license key (Available from NewRelic site)    |

# Todo's
* Add an image with new relic example on alert
* Add the artefact when Master branch build : https://www.appveyor.com/docs/deployment/github/
* Be full NPI (~~Right now the folder structure does is wrong in order to have a src, dist~~, *to see* etc.)
