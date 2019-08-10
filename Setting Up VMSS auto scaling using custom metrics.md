#Setting Up VMSS auto scaling using custom metrics for Windows

When Scaling based on the CPU in VMSS you can use the host metrics to create the auto scale rules. The metrics are available by default in any windows Or Linux VMSS.

For scaling using memory counter we need use guest OS based metrics, The metrics are defined Differently for windows or Linux VMSS instance. It's possible to implement Programmatically by CLI.

**Installation of diagnostic extension in VMSS for Windows Based Machine**

**Steps for Installation.**

1. Copy the below script to the notepad and save the file as **“config.txt”****
```
{
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "DiagnosticInfrastructureLogs": {
        "scheduledTransferLogLevelFilter": "Error"
      },
      "Metrics": {
        "MetricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "ENTER THE RESOURCE ID OF THE VMSS"
      },
      "PerformanceCounters": {
        "PerformanceCounterConfiguration": [
          {
            "annotation": [
              {
                "displayName": "Memory usage",
                "locale": "en-us"
              }
            ],
            "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
            "sampleRate": "PT15S",
            "unit": "Percent"
          },
          {
            "annotation": [
              {
                "displayName": "Memory available",
                "locale": "en-us"
              }
            ],
            "counterSpecifier": "\\Memory\\Available Bytes",
            "sampleRate": "PT15S",
            "unit": "Bytes"
          },
          {
            "annotation": [
              {
                "displayName": "Memory committed",
                "locale": "en-us"
              }
            ],
            "counterSpecifier": "\\Memory\\Committed Bytes",
            "sampleRate": "PT15S",
            "unit": "Bytes"
          },
          {
            "annotation": [
              {
                "displayName": "Memory commit limit",
                "locale": "en-us"
              }
            ],
            "counterSpecifier": "\\Memory\\Commit Limit",
            "sampleRate": "PT15S",
            "unit": "Bytes"
          }
        ],
        "scheduledTransferPeriod": "PT1M"
      },
      "overallQuotaInMB": "4096"
    }
  },
  "storageAccount": "ENTER THE STORAGE ACCOUNT NAME"
}
```
**Note: Edit config.txt -> change the resourceid and storage account name.**

2.  Copy the below script to the notepad and save the file as **“protected settings. Json”**
```
{
  "storageAccountName": "**STORAGE ACCOUNT NAME**",
  "storageAccountSasToken": "**SAS TOKEN**"
}
```
**Note: Edit protected settings. Json -> change the storage account name and exclude “?” in SAS TOKEN, Make sure to have the SAS token valid for a longer period, else scaling will not work as it depends on Guest Metrics.**

3. Run the below CLI Command

az vmss diagnostics set --resource-group xxx --vmss-name xxx --settings config.txt --protected-settings protected-settings. Json.

4. Once the provision is successful you will be able to view the Guest metrics
