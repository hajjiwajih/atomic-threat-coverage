| Title                | Processes created by MMC                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Processes started by MMC could be a sign of lateral movement using MMC application COM object                                                                                                                                           |
| ATT&amp;CK Tactic    | <ul><li>[TA0008: Lateral Movement](https://attack.mitre.org/tactics/TA0008)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1175: Distributed Component Object Model](https://attack.mitre.org/techniques/T1175)</li></ul>                             |
| Data Needed          | <ul><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li><li>[DN_0002_4688_windows_process_creation_with_commandline](../Data_Needed/DN_0002_4688_windows_process_creation_with_commandline.md)</li></ul>                                                         |
| Trigger              | <ul><li>[T1175: Distributed Component Object Model](../Triggers/T1175.md)</li></ul>  |
| Severity Level       | medium                                                                                                                                                 |
| False Positives      | <ul><li>unknown</li></ul>                                                                  |
| Development Status   | experimental                                                                                                                                                |
| References           | <ul><li>[https://enigma0x3.net/2017/01/05/lateral-movement-using-the-mmc20-application-com-object/](https://enigma0x3.net/2017/01/05/lateral-movement-using-the-mmc20-application-com-object/)</li></ul>                                                          |
| Author               |                                                                                                                                                 |


## Detection Rules

### Sigma rule

```
title: Processes created by MMC
status: experimental
description: Processes started by MMC could be a sign of lateral movement using MMC application COM object
references:
    - https://enigma0x3.net/2017/01/05/lateral-movement-using-the-mmc20-application-com-object/
tags:
    - attack.lateral_movement
    - attack.t1175
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        ParentImage: '*\mmc.exe'
        Image: '*\cmd.exe'
    exclusion:
        CommandLine: '*\RunCmd.cmd'
    condition: selection and not exclusion
fields:
    - CommandLine
    - ParentCommandLine
falsepositives:
    - unknown
level: medium

```





### es-qs
    
```
((ParentImage.keyword:*\\\\mmc.exe AND Image.keyword:*\\\\cmd.exe) AND NOT (CommandLine.keyword:*\\\\RunCmd.cmd))
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_xpack/watcher/watch/Processes-created-by-MMC <<EOF\n{\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "query_string": {\n              "query": "((ParentImage.keyword:*\\\\\\\\mmc.exe AND Image.keyword:*\\\\\\\\cmd.exe) AND NOT (CommandLine.keyword:*\\\\\\\\RunCmd.cmd))",\n              "analyze_wildcard": true\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": null,\n        "subject": "Sigma Rule \'Processes created by MMC\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}Hit on {{_source.@timestamp}}:\\n      CommandLine = {{_source.CommandLine}}\\nParentCommandLine = {{_source.ParentCommandLine}}================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
((ParentImage:"*\\\\mmc.exe" AND Image:"*\\\\cmd.exe") AND NOT (CommandLine:"*\\\\RunCmd.cmd"))
```


### splunk
    
```
((ParentImage="*\\\\mmc.exe" Image="*\\\\cmd.exe") NOT (CommandLine="*\\\\RunCmd.cmd")) | table CommandLine,ParentCommandLine
```


### logpoint
    
```
((ParentImage="*\\\\mmc.exe" Image="*\\\\cmd.exe")  -(CommandLine="*\\\\RunCmd.cmd"))
```


### grep
    
```
grep -P '^(?:.*(?=.*(?:.*(?=.*.*\\mmc\\.exe)(?=.*.*\\cmd\\.exe)))(?=.*(?!.*(?:.*(?=.*.*\\RunCmd\\.cmd)))))'
```



