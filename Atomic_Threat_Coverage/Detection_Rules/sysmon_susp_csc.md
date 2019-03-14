| Title                | Suspicious Parent of Csc.exe                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects a suspicious parent of csc.exe, which could by a sign of payload delivery                                                                                                                                           |
| ATT&amp;CK Tactic    | <ul><li>[TA0005: Defense Evasion](https://attack.mitre.org/tactics/TA0005)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1036: Masquerading](https://attack.mitre.org/techniques/T1036)</li></ul>                             |
| Data Needed          | <ul><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li></ul>                                                         |
| Trigger              | <ul><li>[T1036: Masquerading](../Triggers/T1036.md)</li></ul>  |
| Severity Level       | high                                                                                                                                                 |
| False Positives      | <ul><li>Unkown</li></ul>                                                                  |
| Development Status   | experimental                                                                                                                                                |
| References           | <ul><li>[https://twitter.com/SBousseaden/status/1094924091256176641](https://twitter.com/SBousseaden/status/1094924091256176641)</li></ul>                                                          |
| Author               | Florian Roth                                                                                                                                                |


## Detection Rules

### Sigma rule

```
title: Suspicious Parent of Csc.exe
description: Detects a suspicious parent of csc.exe, which could by a sign of payload delivery
status: experimental
references:
    - https://twitter.com/SBousseaden/status/1094924091256176641
author: Florian Roth
date: 2019/02/11
tags:
    - attack.defense_evasion
    - attack.t1036
logsource:
    product: windows
    service: sysmon
detection:
    selection:
        EventID: 1
        Image: '*\csc.exe*'
        ParentImage:
            - '*\wscript.exe'
            - '*\cscript.exe'
            - '*\mshta.exe'
    condition: selection
falsepositives: 
    - Unkown
level: high

```





### es-qs
    
```
(EventID:"1" AND Image.keyword:*\\\\csc.exe* AND ParentImage.keyword:(*\\\\wscript.exe *\\\\cscript.exe *\\\\mshta.exe))
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_xpack/watcher/watch/Suspicious-Parent-of-Csc.exe <<EOF\n{\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "query_string": {\n              "query": "(EventID:\\"1\\" AND Image.keyword:*\\\\\\\\csc.exe* AND ParentImage.keyword:(*\\\\\\\\wscript.exe *\\\\\\\\cscript.exe *\\\\\\\\mshta.exe))",\n              "analyze_wildcard": true\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": null,\n        "subject": "Sigma Rule \'Suspicious Parent of Csc.exe\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(EventID:"1" AND Image:"*\\\\csc.exe*" AND ParentImage:("*\\\\wscript.exe" "*\\\\cscript.exe" "*\\\\mshta.exe"))
```


### splunk
    
```
(EventID="1" Image="*\\\\csc.exe*" (ParentImage="*\\\\wscript.exe" OR ParentImage="*\\\\cscript.exe" OR ParentImage="*\\\\mshta.exe"))
```


### logpoint
    
```
(EventID="1" Image="*\\\\csc.exe*" ParentImage IN ["*\\\\wscript.exe", "*\\\\cscript.exe", "*\\\\mshta.exe"])
```


### grep
    
```
grep -P '^(?:.*(?=.*1)(?=.*.*\\csc\\.exe.*)(?=.*(?:.*.*\\wscript\\.exe|.*.*\\cscript\\.exe|.*.*\\mshta\\.exe)))'
```



