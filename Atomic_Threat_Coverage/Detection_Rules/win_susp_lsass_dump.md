| Title                | Password Dumper Activity on LSASS                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects process handle on LSASS process with certain access mask and object type SAM_DOMAIN                                                                                                                                           |
| ATT&amp;CK Tactic    | <ul><li>[TA0006: Credential Access](https://attack.mitre.org/tactics/TA0006)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1003: Credential Dumping](https://attack.mitre.org/techniques/T1003)</li></ul>                             |
| Data Needed          | <ul></ul>                                                         |
| Trigger              | <ul><li>[T1003: Credential Dumping](../Triggers/T1003.md)</li></ul>  |
| Severity Level       | high                                                                                                                                                 |
| False Positives      | <ul><li>Unkown</li></ul>                                                                  |
| Development Status   | experimental                                                                                                                                                |
| References           | <ul><li>[https://twitter.com/jackcr/status/807385668833968128](https://twitter.com/jackcr/status/807385668833968128)</li></ul>                                                          |
| Author               |                                                                                                                                                 |


## Detection Rules

### Sigma rule

```
title: Password Dumper Activity on LSASS
description: Detects process handle on LSASS process with certain access mask and object type SAM_DOMAIN
status: experimental
references:
    - https://twitter.com/jackcr/status/807385668833968128
tags:
    - attack.credential_access
    - attack.t1003
logsource:
    product: windows
    service: security
detection:
    selection:
        EventID: 4656
        ProcessName: 'C:\Windows\System32\lsass.exe'
        AccessMask: '0x705'
        ObjectType: 'SAM_DOMAIN'
    condition: selection
falsepositives:
    - Unkown
level: high

```





### es-qs
    
```
(EventID:"4656" AND ProcessName:"C\\:\\\\Windows\\\\System32\\\\lsass.exe" AND AccessMask:"0x705" AND ObjectType:"SAM_DOMAIN")
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_xpack/watcher/watch/Password-Dumper-Activity-on-LSASS <<EOF\n{\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "query_string": {\n              "query": "(EventID:\\"4656\\" AND ProcessName:\\"C\\\\:\\\\\\\\Windows\\\\\\\\System32\\\\\\\\lsass.exe\\" AND AccessMask:\\"0x705\\" AND ObjectType:\\"SAM_DOMAIN\\")",\n              "analyze_wildcard": true\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": null,\n        "subject": "Sigma Rule \'Password Dumper Activity on LSASS\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(EventID:"4656" AND ProcessName:"C\\:\\\\Windows\\\\System32\\\\lsass.exe" AND AccessMask:"0x705" AND ObjectType:"SAM_DOMAIN")
```


### splunk
    
```
(EventID="4656" ProcessName="C:\\\\Windows\\\\System32\\\\lsass.exe" AccessMask="0x705" ObjectType="SAM_DOMAIN")
```


### logpoint
    
```
(EventID="4656" ProcessName="C:\\\\Windows\\\\System32\\\\lsass.exe" AccessMask="0x705" ObjectType="SAM_DOMAIN")
```


### grep
    
```
grep -P '^(?:.*(?=.*4656)(?=.*C:\\Windows\\System32\\lsass\\.exe)(?=.*0x705)(?=.*SAM_DOMAIN))'
```



