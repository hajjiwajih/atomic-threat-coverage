| Title                | Windows Shell Spawning Suspicious Program                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects a suspicious child process of a Windows shell                                                                                                                                           |
| ATT&amp;CK Tactic    | <ul></ul>  |
| ATT&amp;CK Technique | <ul></ul>                             |
| Data Needed          | <ul><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li></ul>                                                         |
| Trigger              |  There is no Trigger for this technique yet.  |
| Severity Level       | high                                                                                                                                                 |
| False Positives      | <ul><li>Administrative scripts</li><li>Microsoft SCCM</li></ul>                                                                  |
| Development Status   | experimental                                                                                                                                                |
| References           | <ul><li>[https://mgreen27.github.io/posts/2018/04/02/DownloadCradle.html](https://mgreen27.github.io/posts/2018/04/02/DownloadCradle.html)</li></ul>                                                          |
| Author               | Florian Roth                                                                                                                                                |


## Detection Rules

### Sigma rule

```
title: Windows Shell Spawning Suspicious Program
status: experimental
description: Detects a suspicious child process of a Windows shell
references:
    - https://mgreen27.github.io/posts/2018/04/02/DownloadCradle.html
author: Florian Roth
date: 2018/04/06
modified: 2019/02/05
logsource:
    product: windows
    service: sysmon
detection:
    selection:
        EventID: 1
        ParentImage:
            - '*\mshta.exe'
            - '*\powershell.exe'
            - '*\cmd.exe'
            - '*\rundll32.exe'
            - '*\cscript.exe'
            - '*\wscript.exe'
            - '*\wmiprvse.exe'
        Image:
            - '*\schtasks.exe'
            - '*\nslookup.exe'
            - '*\certutil.exe'
            - '*\bitsadmin.exe'
            - '*\mshta.exe'
    falsepositives:
        CurrentDirectory: '*\ccmcache\*'
    condition: selection and not falsepositives
fields:
    - CommandLine
    - ParentCommandLine
falsepositives:
    - Administrative scripts
    - Microsoft SCCM
level: high


```





### es-qs
    
```
((EventID:"1" AND ParentImage.keyword:(*\\\\mshta.exe *\\\\powershell.exe *\\\\cmd.exe *\\\\rundll32.exe *\\\\cscript.exe *\\\\wscript.exe *\\\\wmiprvse.exe) AND Image.keyword:(*\\\\schtasks.exe *\\\\nslookup.exe *\\\\certutil.exe *\\\\bitsadmin.exe *\\\\mshta.exe)) AND NOT (CurrentDirectory.keyword:*\\\\ccmcache\\*))
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_xpack/watcher/watch/Windows-Shell-Spawning-Suspicious-Program <<EOF\n{\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "query_string": {\n              "query": "((EventID:\\"1\\" AND ParentImage.keyword:(*\\\\\\\\mshta.exe *\\\\\\\\powershell.exe *\\\\\\\\cmd.exe *\\\\\\\\rundll32.exe *\\\\\\\\cscript.exe *\\\\\\\\wscript.exe *\\\\\\\\wmiprvse.exe) AND Image.keyword:(*\\\\\\\\schtasks.exe *\\\\\\\\nslookup.exe *\\\\\\\\certutil.exe *\\\\\\\\bitsadmin.exe *\\\\\\\\mshta.exe)) AND NOT (CurrentDirectory.keyword:*\\\\\\\\ccmcache\\\\*))",\n              "analyze_wildcard": true\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": null,\n        "subject": "Sigma Rule \'Windows Shell Spawning Suspicious Program\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}Hit on {{_source.@timestamp}}:\\n      CommandLine = {{_source.CommandLine}}\\nParentCommandLine = {{_source.ParentCommandLine}}================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
((EventID:"1" AND ParentImage:("*\\\\mshta.exe" "*\\\\powershell.exe" "*\\\\cmd.exe" "*\\\\rundll32.exe" "*\\\\cscript.exe" "*\\\\wscript.exe" "*\\\\wmiprvse.exe") AND Image:("*\\\\schtasks.exe" "*\\\\nslookup.exe" "*\\\\certutil.exe" "*\\\\bitsadmin.exe" "*\\\\mshta.exe")) AND NOT (CurrentDirectory:"*\\\\ccmcache\\*"))
```


### splunk
    
```
((EventID="1" (ParentImage="*\\\\mshta.exe" OR ParentImage="*\\\\powershell.exe" OR ParentImage="*\\\\cmd.exe" OR ParentImage="*\\\\rundll32.exe" OR ParentImage="*\\\\cscript.exe" OR ParentImage="*\\\\wscript.exe" OR ParentImage="*\\\\wmiprvse.exe") (Image="*\\\\schtasks.exe" OR Image="*\\\\nslookup.exe" OR Image="*\\\\certutil.exe" OR Image="*\\\\bitsadmin.exe" OR Image="*\\\\mshta.exe")) NOT (CurrentDirectory="*\\\\ccmcache\\*")) | table CommandLine,ParentCommandLine
```


### logpoint
    
```
((EventID="1" ParentImage IN ["*\\\\mshta.exe", "*\\\\powershell.exe", "*\\\\cmd.exe", "*\\\\rundll32.exe", "*\\\\cscript.exe", "*\\\\wscript.exe", "*\\\\wmiprvse.exe"] Image IN ["*\\\\schtasks.exe", "*\\\\nslookup.exe", "*\\\\certutil.exe", "*\\\\bitsadmin.exe", "*\\\\mshta.exe"])  -(CurrentDirectory="*\\\\ccmcache\\*"))
```


### grep
    
```
grep -P '^(?:.*(?=.*(?:.*(?=.*1)(?=.*(?:.*.*\\mshta\\.exe|.*.*\\powershell\\.exe|.*.*\\cmd\\.exe|.*.*\\rundll32\\.exe|.*.*\\cscript\\.exe|.*.*\\wscript\\.exe|.*.*\\wmiprvse\\.exe))(?=.*(?:.*.*\\schtasks\\.exe|.*.*\\nslookup\\.exe|.*.*\\certutil\\.exe|.*.*\\bitsadmin\\.exe|.*.*\\mshta\\.exe))))(?=.*(?!.*(?:.*(?=.*.*\\ccmcache\\.*)))))'
```



