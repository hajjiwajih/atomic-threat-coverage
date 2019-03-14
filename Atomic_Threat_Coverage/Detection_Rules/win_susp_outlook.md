| Title                | Suspicious Execution from Outlook                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects EnableUnsafeClientMailRules used for Script Execution from Outlook                                                                                                                                           |
| ATT&amp;CK Tactic    | <ul><li>[TA0002: Execution](https://attack.mitre.org/tactics/TA0002)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1059: Command-Line Interface](https://attack.mitre.org/techniques/T1059)</li><li>[T1202: Indirect Command Execution](https://attack.mitre.org/techniques/T1202)</li></ul>                             |
| Data Needed          | <ul><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li><li>[DN_0002_4688_windows_process_creation_with_commandline](../Data_Needed/DN_0002_4688_windows_process_creation_with_commandline.md)</li></ul>                                                         |
| Trigger              | <ul><li>[T1059: Command-Line Interface](../Triggers/T1059.md)</li><li>[T1202: Indirect Command Execution](../Triggers/T1202.md)</li></ul>  |
| Severity Level       | high                                                                                                                                                 |
| False Positives      | <ul><li>unknown</li></ul>                                                                  |
| Development Status   | experimental                                                                                                                                                |
| References           | <ul><li>[https://github.com/sensepost/ruler](https://github.com/sensepost/ruler)</li><li>[https://www.fireeye.com/blog/threat-research/2018/12/overruled-containing-a-potentially-destructive-adversary.html](https://www.fireeye.com/blog/threat-research/2018/12/overruled-containing-a-potentially-destructive-adversary.html)</li></ul>                                                          |
| Author               | Markus Neis                                                                                                                                                |


## Detection Rules

### Sigma rule

```
title: Suspicious Execution from Outlook
status: experimental
description: Detects EnableUnsafeClientMailRules used for Script Execution from Outlook
references:
    - https://github.com/sensepost/ruler
    - https://www.fireeye.com/blog/threat-research/2018/12/overruled-containing-a-potentially-destructive-adversary.html
tags:
    - attack.execution
    - attack.t1059
    - attack.t1202
author: Markus Neis
date: 2018/12/27
logsource:
    category: process_creation
    product: windows
detection:
    clientMailRules:
        CommandLine: '*EnableUnsafeClientMailRules*'
    outlookExec:
        ParentImage: '*\outlook.exe'
        CommandLine: \\\\*\\*.exe
    condition: clientMailRules or outlookExec
falsepositives:
    - unknown
level: high

```





### es-qs
    
```
(CommandLine.keyword:*EnableUnsafeClientMailRules* OR (ParentImage.keyword:*\\\\outlook.exe AND CommandLine:"\\\\\\\\*\\\\*.exe"))
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_xpack/watcher/watch/Suspicious-Execution-from-Outlook <<EOF\n{\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "query_string": {\n              "query": "(CommandLine.keyword:*EnableUnsafeClientMailRules* OR (ParentImage.keyword:*\\\\\\\\outlook.exe AND CommandLine:\\"\\\\\\\\\\\\\\\\*\\\\\\\\*.exe\\"))",\n              "analyze_wildcard": true\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": null,\n        "subject": "Sigma Rule \'Suspicious Execution from Outlook\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(CommandLine:"*EnableUnsafeClientMailRules*" OR (ParentImage:"*\\\\outlook.exe" AND CommandLine:"\\\\\\\\*\\\\*.exe"))
```


### splunk
    
```
(CommandLine="*EnableUnsafeClientMailRules*" OR (ParentImage="*\\\\outlook.exe" CommandLine="\\\\\\\\*\\\\*.exe"))
```


### logpoint
    
```
(CommandLine="*EnableUnsafeClientMailRules*" OR (ParentImage="*\\\\outlook.exe" CommandLine="\\\\\\\\*\\\\*.exe"))
```


### grep
    
```
grep -P '^(?:.*(?:.*.*EnableUnsafeClientMailRules.*|.*(?:.*(?=.*.*\\outlook\\.exe)(?=.*\\\\\\\\.*\\\\.*\\.exe))))'
```



