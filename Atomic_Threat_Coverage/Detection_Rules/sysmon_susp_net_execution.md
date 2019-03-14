| Title                | Net.exe Execution                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects execution of Net.exe, whether suspicious or benign.                                                                                                                                           |
| ATT&amp;CK Tactic    | <ul><li>[TA0008: Lateral Movement](https://attack.mitre.org/tactics/TA0008)</li><li>[TA0007: Discovery](https://attack.mitre.org/tactics/TA0007)</li></ul>  |
| ATT&amp;CK Technique | <ul></ul>                             |
| Data Needed          | <ul><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li></ul>                                                         |
| Trigger              |  There is no Trigger for this technique yet.  |
| Severity Level       | low                                                                                                                                                 |
| False Positives      | <ul><li>Will need to be tuned. If using Splunk, I recommend | stats count by Computer,CommandLine following the search for easy hunting by computer/CommandLine.</li></ul>                                                                  |
| Development Status   | experimental                                                                                                                                                |
| References           | <ul><li>[https://pentest.blog/windows-privilege-escalation-methods-for-pentesters/](https://pentest.blog/windows-privilege-escalation-methods-for-pentesters/)</li></ul>                                                          |
| Author               | Michael Haag, Mark Woan (improvements)                                                                                                                                                |
| Other Tags           | <ul><li>attack.s0039</li><li>attack.s0039</li></ul> | 

## Detection Rules

### Sigma rule

```
title: Net.exe Execution
status: experimental
description: Detects execution of Net.exe, whether suspicious or benign.
references:
    - https://pentest.blog/windows-privilege-escalation-methods-for-pentesters/
author: Michael Haag, Mark Woan (improvements)
tags:
    - attack.s0039
    - attack.lateral_movement
    - attack.discovery

logsource:
    product: windows
    service: sysmon
detection:
    selection:
        EventID: 1
        Image:
            - '*\net.exe'
            - '*\net1.exe'
        CommandLine:
            - '* group*'
            - '* localgroup*'
            - '* user*'
            - '* view*'
            - '* share'
            - '* accounts*'
            - '* use*'
    condition: selection
fields:
    - CommandLine
    - ParentCommandLine
falsepositives:
    - Will need to be tuned. If using Splunk, I recommend | stats count by Computer,CommandLine following the search for easy hunting by computer/CommandLine.
level: low

```





### es-qs
    
```
(EventID:"1" AND Image.keyword:(*\\\\net.exe *\\\\net1.exe) AND CommandLine.keyword:(*\\ group* *\\ localgroup* *\\ user* *\\ view* *\\ share *\\ accounts* *\\ use*))
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_xpack/watcher/watch/Net.exe-Execution <<EOF\n{\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "query_string": {\n              "query": "(EventID:\\"1\\" AND Image.keyword:(*\\\\\\\\net.exe *\\\\\\\\net1.exe) AND CommandLine.keyword:(*\\\\ group* *\\\\ localgroup* *\\\\ user* *\\\\ view* *\\\\ share *\\\\ accounts* *\\\\ use*))",\n              "analyze_wildcard": true\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": null,\n        "subject": "Sigma Rule \'Net.exe Execution\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}Hit on {{_source.@timestamp}}:\\n      CommandLine = {{_source.CommandLine}}\\nParentCommandLine = {{_source.ParentCommandLine}}================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(EventID:"1" AND Image:("*\\\\net.exe" "*\\\\net1.exe") AND CommandLine:("* group*" "* localgroup*" "* user*" "* view*" "* share" "* accounts*" "* use*"))
```


### splunk
    
```
(EventID="1" (Image="*\\\\net.exe" OR Image="*\\\\net1.exe") (CommandLine="* group*" OR CommandLine="* localgroup*" OR CommandLine="* user*" OR CommandLine="* view*" OR CommandLine="* share" OR CommandLine="* accounts*" OR CommandLine="* use*")) | table CommandLine,ParentCommandLine
```


### logpoint
    
```
(EventID="1" Image IN ["*\\\\net.exe", "*\\\\net1.exe"] CommandLine IN ["* group*", "* localgroup*", "* user*", "* view*", "* share", "* accounts*", "* use*"])
```


### grep
    
```
grep -P '^(?:.*(?=.*1)(?=.*(?:.*.*\\net\\.exe|.*.*\\net1\\.exe))(?=.*(?:.*.* group.*|.*.* localgroup.*|.*.* user.*|.*.* view.*|.*.* share|.*.* accounts.*|.*.* use.*)))'
```



