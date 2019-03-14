| Title                | USB Device Plugged                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects plugged USB devices                                                                                                                                           |
| ATT&amp;CK Tactic    | <ul><li>[TA0001: Initial Access](https://attack.mitre.org/tactics/TA0001)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1200: Hardware Additions](https://attack.mitre.org/techniques/T1200)</li></ul>                             |
| Data Needed          | <ul></ul>                                                         |
| Trigger              | <ul><li>[T1200: Hardware Additions](../Triggers/T1200.md)</li></ul>  |
| Severity Level       | low                                                                                                                                                 |
| False Positives      | <ul><li>Legitimate administrative activity</li></ul>                                                                  |
| Development Status   | experimental                                                                                                                                                |
| References           | <ul><li>[https://df-stream.com/2014/01/the-windows-7-event-log-and-usb-device/](https://df-stream.com/2014/01/the-windows-7-event-log-and-usb-device/)</li><li>[https://www.techrepublic.com/article/how-to-track-down-usb-flash-drive-usage-in-windows-10s-event-viewer/](https://www.techrepublic.com/article/how-to-track-down-usb-flash-drive-usage-in-windows-10s-event-viewer/)</li></ul>                                                          |
| Author               | Florian Roth                                                                                                                                                |


## Detection Rules

### Sigma rule

```
title: USB Device Plugged
description: Detects plugged USB devices
references:
    - https://df-stream.com/2014/01/the-windows-7-event-log-and-usb-device/
    - https://www.techrepublic.com/article/how-to-track-down-usb-flash-drive-usage-in-windows-10s-event-viewer/
status: experimental
author: Florian Roth
tags:
    - attack.initial_access
    - attack.t1200
logsource:
    product: windows
    service: driver-framework
detection:
    selection:
        EventID: 
            - 2003  # Loading drivers
            - 2100  # Pnp or power management
            - 2102  # Pnp or power management
    condition: selection
falsepositives: 
    - Legitimate administrative activity
level: low

```





### es-qs
    
```
EventID:("2003" "2100" "2102")
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_xpack/watcher/watch/USB-Device-Plugged <<EOF\n{\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "query_string": {\n              "query": "EventID:(\\"2003\\" \\"2100\\" \\"2102\\")",\n              "analyze_wildcard": true\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": null,\n        "subject": "Sigma Rule \'USB Device Plugged\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
EventID:("2003" "2100" "2102")
```


### splunk
    
```
(EventID="2003" OR EventID="2100" OR EventID="2102")
```


### logpoint
    
```
EventID IN ["2003", "2100", "2102"]
```


### grep
    
```
grep -P '^(?:.*2003|.*2100|.*2102)'
```



