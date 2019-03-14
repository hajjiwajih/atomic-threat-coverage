| Title                | PsExec Service Start                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects a PsExec service start                                                                                                                                           |
| ATT&amp;CK Tactic    | <ul><li>[TA0002: Execution](https://attack.mitre.org/tactics/TA0002)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1035: Service Execution](https://attack.mitre.org/techniques/T1035)</li></ul>                             |
| Data Needed          | <ul><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li><li>[DN_0002_4688_windows_process_creation_with_commandline](../Data_Needed/DN_0002_4688_windows_process_creation_with_commandline.md)</li></ul>                                                         |
| Trigger              | <ul><li>[T1035: Service Execution](../Triggers/T1035.md)</li></ul>  |
| Severity Level       | low                                                                                                                                                 |
| False Positives      | <ul><li>Administrative activity</li></ul>                                                                  |
| Development Status   |                                                                                                                                                 |
| References           | <ul></ul>                                                          |
| Author               | Florian Roth                                                                                                                                                |
| Other Tags           | <ul><li>attack.s0029</li><li>attack.s0029</li></ul> | 

## Detection Rules

### Sigma rule

```
title: PsExec Service Start
description: Detects a PsExec service start
author: Florian Roth
date: 2018/03/13
modified: 2012/12/11
tags:
    - attack.execution
    - attack.t1035
    - attack.s0029
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        ProcessCommandLine: C:\Windows\PSEXESVC.exe
    condition: selection
falsepositives:
    - Administrative activity
level: low

```





### es-qs
    
```
ProcessCommandLine:"C\\:\\\\Windows\\\\PSEXESVC.exe"
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_xpack/watcher/watch/PsExec-Service-Start <<EOF\n{\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "query_string": {\n              "query": "ProcessCommandLine:\\"C\\\\:\\\\\\\\Windows\\\\\\\\PSEXESVC.exe\\"",\n              "analyze_wildcard": true\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": null,\n        "subject": "Sigma Rule \'PsExec Service Start\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
ProcessCommandLine:"C\\:\\\\Windows\\\\PSEXESVC.exe"
```


### splunk
    
```
ProcessCommandLine="C:\\\\Windows\\\\PSEXESVC.exe"
```


### logpoint
    
```
ProcessCommandLine="C:\\\\Windows\\\\PSEXESVC.exe"
```


### grep
    
```
grep -P '^C:\\Windows\\PSEXESVC\\.exe'
```



