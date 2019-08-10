**Setting Up VMSS auto scaling using custom metrics**

When Scaling based on the CPU in VMSS you can use the host metrics to create the auto scale rules. The metrics are available by default in any windows Or Linux VMSS.

For scaling using memory counter we need use guest OS based metrics, The metrics are defined Differently for windows or Linux VMSS instance. It's possible to implement Programmatically by CLI.

**Installation of diagnostic extension in VMSS for Windows Based Machine**

**Steps for Installation.**

1. Copy the below script to the notepad and save the file as **“config.txt”****

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

"resourceId": "**ENTER THE RESOURCE ID OF THE VMSS**"

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

"counterSpecifier": "[\\Memory\\% Committed Bytes In Use](file://Memory/%25%20Committed%20Bytes%20In%20Use)",

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

"counterSpecifier": "[\\Memory\\Available Bytes](file://Memory/Available%20Bytes)",

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

"counterSpecifier": "[\\Memory\\Committed Bytes](file://Memory/Committed%20Bytes)",

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

"counterSpecifier": "[\\Memory\\Commit Limit](file://Memory/Commit%20Limit)",

"sampleRate": "PT15S",

"unit": "Bytes"

}

],

"scheduledTransferPeriod": "PT1M"

},

"overallQuotaInMB": "4096"

}

},

"storageAccount": "**ENTER THE STORAGE ACCOUNT NAME**"

}

**Note: Edit config.txt -> change the resourceid and storage account name.**

2.  Copy the below script to the notepad and save the file as **“protected settings. Json”**
 {

"storageAccountName": "**STORAGE ACCOUNT NAME**",

"storageAccountSasToken": **"SAS TOKEN**"

} 
**Note: Edit protected settings. Json -> change the storage account name and exclude “?” in SAS TOKEN, Make sure to have the SAS token valid for a longer period, else scaling will not work as it depends on Guest Metrics.**

3. Run the below CLI Command

az vmss diagnostics set --resource-group xxx --vmss-name xxx --settings config.txt --protected-settings protected-settings. Json.

4. Once the provision is successful you will be able to view the Guest metrics.

**Installation of diagnostic extension in VMSS for Linux**

1. Copy the below script to the notepad and save the file as **“config.txt”**

{

"StorageAccount": "**ENTER THE STORAGE ACCOUNT NAME**",

"ladCfg": {

"diagnosticMonitorConfiguration": {

"eventVolume": "Medium",

"metrics": {

"metricAggregation": [

{

"scheduledTransferPeriod": "PT1H"

},

{

"scheduledTransferPeriod": "PT1M"

}

],

"resourceId": "**ENTER THE RESOURCE ID OF THE VMSS**"

},

"performanceCounters": {

"performanceCounterConfiguration": [

{

"annotation": [

{

"displayName": "Memory available",

"locale": "en-us"

}

],

"class": "memory",

"counter": "availablememory",

"counterSpecifier": "/builtin/memory/availablememory",

"type": "builtin",

"unit": "Bytes"

},

{

"annotation": [

{

"displayName": "Swap percent used",

"locale": "en-us"

}

],

"class": "memory",

"counter": "percentusedswap",

"counterSpecifier": "/builtin/memory/percentusedswap",

"type": "builtin",

"unit": "Percent"

},

{

"annotation": [

{

"displayName": "Memory used",

"locale": "en-us"

}

],

"class": "memory",

"counter": "usedmemory",

"counterSpecifier": "/builtin/memory/usedmemory",

"type": "builtin",

"unit": "Bytes"

},

{

"annotation": [

{

"displayName": "Page reads",

"locale": "en-us"

}

],

"class": "memory",

"counter": "pagesreadpersec",

"counterSpecifier": "/builtin/memory/pagesreadpersec",

"type": "builtin",

"unit": "CountPerSecond"

},

{

"annotation": [

{

"displayName": "Swap available",

"locale": "en-us"

}

],

"class": "memory",

"counter": "availableswap",

"counterSpecifier": "/builtin/memory/availableswap",

"type": "builtin",

"unit": "Bytes"

},

{

"annotation": [

{

"displayName": "Swap percent available",

"locale": "en-us"

}

],

"class": "memory",

"counter": "percentavailableswap",

"counterSpecifier": "/builtin/memory/percentavailableswap",

"type": "builtin",

"unit": "Percent"

},

{

"annotation": [

{

"displayName": "Mem. percent available",

"locale": "en-us"

}

],

"class": "memory",

"counter": "percentavailablememory",

"counterSpecifier": "/builtin/memory/percentavailablememory",

"type": "builtin",

"unit": "Percent"

},

{

"annotation": [

{

"displayName": "Pages",

"locale": "en-us"

}

],

"class": "memory",

"counter": "pagespersec",

"counterSpecifier": "/builtin/memory/pagespersec",

"type": "builtin",

"unit": "CountPerSecond"

},

{

"annotation": [

{

"displayName": "Swap used",

"locale": "en-us"

}

],

"class": "memory",

"counter": "usedswap",

"counterSpecifier": "/builtin/memory/usedswap",

"type": "builtin",

"unit": "Bytes"

},

{

"annotation": [

{

"displayName": "Memory percentage",

"locale": "en-us"

}

],

"class": "memory",

"counter": "percentusedmemory",

"counterSpecifier": "/builtin/memory/percentusedmemory",

"type": "builtin",

"unit": "Percent"

},

{

"annotation": [

{

"displayName": "Page writes",

"locale": "en-us"

}

],

"class": "memory",

"counter": "pageswrittenpersec",

"counterSpecifier": "/builtin/memory/pageswrittenpersec",

"type": "builtin",

"unit": "CountPerSecond"

}

]

},

"syslogEvents": {

"syslogEventConfiguration": {

"LOG_AUTH": "LOG_DEBUG",

"LOG_AUTHPRIV": "LOG_DEBUG",

"LOG_CRON": "LOG_DEBUG",

"LOG_DAEMON": "LOG_DEBUG",

"LOG_FTP": "LOG_DEBUG",

"LOG_KERN": "LOG_DEBUG",

"LOG_LOCAL0": "LOG_DEBUG",

"LOG_LOCAL1": "LOG_DEBUG",

"LOG_LOCAL2": "LOG_DEBUG",

"LOG_LOCAL3": "LOG_DEBUG",

"LOG_LOCAL4": "LOG_DEBUG",

"LOG_LOCAL5": "LOG_DEBUG",

"LOG_LOCAL6": "LOG_DEBUG",

"LOG_LOCAL7": "LOG_DEBUG",

"LOG_LPR": "LOG_DEBUG",

"LOG_MAIL": "LOG_DEBUG",

"LOG_NEWS": "LOG_DEBUG",

"LOG_SYSLOG": "LOG_DEBUG",

"LOG_USER": "LOG_DEBUG",

"LOG_UUCP": "LOG_DEBUG"

}

}

},

"sampleRateInSeconds": 15

}

}

**Note: Edit config.txt -> change the resourceid and storage account name.**

2.  Copy the below script to the notepad and save the file as **“protected settings. Json”**

{

"storageAccountName": "**STORAGE ACCOUNT NAME**",

"storageAccountSasToken": **"SAS TOKEN**"

}

**Note: Edit protected settings. Json -> change the storage account name and exclude “?” in SAS TOKEN, Make sure to have the SAS token valid for a longer period, else scaling will not work as it depends on Guest Metrics.**

3. Run the below CLI Command

az vmss diagnostics set --resource-group xxx --vmss-name xxx --settings config.txt --protected-settings protected-settings. Json.

4. Once the provision is successful you will be able to view the Guest metrics.