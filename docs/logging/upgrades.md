Finding all the logging messages

regex:
(log\.)

# Logging Upgrades

Currently, the logs for the Pronghorn API are helpful to trace and debug common workflows, but actually lack some consistency and features that would allow the logs to be come more traceable for customer support.

This document discusses some simple changes that we could make to the log messages in order to pass along to a log engine and be able to be better prepared in a support call/outreach.

## Request Logging

For many of the given REST API endpoints, we only get a log entry for a API request that results in a successful output, i.e. 200, 201, 204.  When a request fails, we are sometimes logging the error message.  It the case of the happy path or the unhappy path, we are not creating a log of the actual request.  Body, Parameters, User-Agent, etc, that would allow us determine why a request may have failed/succeeded.

 
 A few plugins exist for grails that handle request logging in one way or another, but the format would not match those outlined below.  It would be simple enough to log out parts of the request object.

## JSON/Key Logging

Many of the current logs have key/value output, but these strings are not consumable by an elasticsearch service, and are therefore not indexable/searchable.  

For example, logging out a session:
```log
Successfully created sessionObj: id:3  key:d49246f449f54e51bb2917dd9ff5519a  clientIP:0:0:0:0:0:0:0:1  clientHost:localhost clientName:transformer clientDateCreated:null  timeoutMinutes:3  appName:dicom  appAction:null  externalId:null  encounterDate:null  encounterTime:null  encounterOperatorName:null  encounterDepartment:null  encounterDepartmentID:null  encounterProviderId:null  encounterProviderName:null  encounterMode:OP  modalityAuthProvId:null  modalityAuthProvName:  coerce:false  coerceUIDs:false  expectedFiles:0  accessionNumberPrefix:  sourceSite:null  isStandalone:true  demo: false
```
Logging out a manifest:
```log
Successfully created manifestObj: id:3  sessionId:3  notes:  studyDescription:  studyInstanceUID:   accessionNumber:   seriesDescription: null  encounterModality:VL  examCode:null  isOverread:false  reasonForStudy:  sendToPACS:true  sendToNuance:false  sendToXDSb:false  nuanceRepoId:0  imageModifiers:null  stationName:null  serverId:1  genPinCode:false  pinCode:null
```
If we moved to having these output logs be in JSON, we could filter forsessionKey:d49246f449f54e51bb2917dd9ff5519a and find the related session activity.

## Normalizing the Logs

There are several fields that should be “key” to any JSON output.  If these keys are present on every message, then we can trace the activity for that given key.

For example, right now, several messages begin with such keys:
```log
3-3: Incoming file=MF0077_Test_DICOMPatient__245_Cross_Line_OD.dcm
3-3-3: Storing uploaded file MF0077_Test_DICOMPatient__245_Cross_Line_OD.dcm
```
These keys are:

`sessionId`-`manifestId`-`fileId`

However, in its current state, as an engineer, I can’t find all the file uploads for sessionId:3.  Log modifications in this case that would allow better traceability:

```log
Incoming file - { session: 3, manifest: 3, filename: "MF0077_Test_DICOMPatient__245_Cross_Line_OD.dcm" }
Storing uploaded file - { session: 3, manifest: 3, file: 3, filename: "MF0077_Test_DICOMPatient__245_Cross_Line_OD.dcm" }
```

Then, we could search the logs for sessionId:3 and easily see the debug, info, error and warning logs for all the activity for session #3.

For the object creation logs, it is probably not important to key/index on all the object fields, but select which ones we would want to index. Like session.appName or session.clientNameand everything else is available for reference in a misc object.  i.e.
```log
Successfully created sessionObj - { session:3, key:"d49246f449f54e51bb2917dd9ff5519a", appName:"dicom", coerce:false, clientName:"transformer",  misc: { clientIP:"0:0:0:0:0:0:0:1", clientHost:"localhost", clientDateCreated:null, timeoutMinutes:3, appAction:null, externalId:null, encounterDate:null, encounterTime:null, encounterOperatorName:null, encounterDepartment:null, encounterDepartmentID:null, encounterProviderId:null, encounterProviderName:null, encounterMode:OP,  modalityAuthProvId:null, modalityAuthProvName:"", coerceUIDs:false, expectedFiles:0, accessionNumberPrefix:"", sourceSite:null, isStandalone:true, demo: false }}
```
For any of the messages, the idea is that there is a generic message, i.e. “Incoming File” or “Successfully created sessionObj” and then there is json data.  This format allows me to find all cases of “Incoming File”.  I could even establish a count of the number of “Incoming File” messages and then be able to filter them by session Id, or maybe it would be helpful to log out other metrics here, like filetype or session.appName, session.clientName, manifest.appName, etc.  

## GUIDs

Integers are certainly easier to follow, but lack a uniqueness within the application design.  When a session or a manifest have expired they are cleaned up and logged out, in certain scenarios.  Multiple sites will be funneling to the same monitoring/logging site, which presents the opportunity for multiple collisions for integer numbers.  Furthermore, when a site is restarted, assuming that redis is flushed, the number restarts from 1.  

Work arounds exist, in that we can filter session 1 by the server ip, url, or dns to trace traffic/activity specific to a given server/VNA.  It might be helpful/advisable to update the manifest to use a GUID and to present that GUID in the logs for further delimiting.

## Billing

For the purpose of billing of ImageMover transactions, we use the log files to output “STUDY” and “ORDER” messages so that they may be parsed and tallied.  This operation can continue, but we could address the formatting in much the same way as the other messages, allowing these STUDY/ORDERs to be easily indexed.
```json
{"STUDY":{"App":"Media Uploader","Dept":"","StudyUID":"1.2.840.113711.2117041813.8.15608.592263803.332893413.10","AccessionNumber":0,"Operator":null,"Provider":"","NumberOfFileSuccesses":1,"NumberOfFileErrors":0,"HL7enabled":true,"HL7sent":true,"appName":"media","clientName":"transformer"}}
```

## HL7 Messages

When an HL7 message is required, we log that it was transmitted and to the server/receiver to which it was sent, but we do not offer any additional information about the composition of the message.  Furthermore, HL7 Messages are not difficult to decode, but are not readable without some decoding.  We could de-identify these HL7 Messages and allow that they are logged out to a separate pipe/service.   Persisting these messages in some form might be helpful if we needed to resend, replay, or redirect (to a different sender), however, this would require some level of encrypted storage.

## PHI / PII

Manifest Objects seem to have the most PHI.  There are other locations that log out some PHI that we would need to find and obfuscate or otherwise remove.

### Error and Exception Handling

For any try/catch blocks that exist, we should be capturing the stack before deciding to bubble or propagate the exception.  Again, a generalization for the error would help find instances where the error is occurring and then the stack trace would help us have more information about the actual failure.

## Logger Class

By wrapping the Logger function into a wrapper class, we could accomplish several notable tasks:

1. Given a generic message and a JSON object, we could parse the json, and identify key fields and metrics that we want indexed in the logs and dump the remaining json into the misc field.

1. With a blacklist of fields that are known to contain PHI, we can parse the json fields found in the blacklist and obfuscate ANY patient data.

1. Normalize all log output so that any log request in the system must pass through the logger class and thus be a standardized output without PHI.

## Other Logging Examples

### General

Any log messages that are built into a formatted string need to be generalized, with details about the message given in the json.
```java
log.info("HL7 message being sent for manifest# $event.manifestId to $hl7DestKey $hl7DestValue")
```

Will need to be changed such that we key on the important factors, and then push the rest.
```
HL7 message being sent for manifest - { manifest: $event.manifestId, hl7dest: $hl7DestValue, hl7status: $hl7DestKey }
```

### Expired Cleanup Logs
 
#### Stats

There are several stats logs that come out of the ExpiredCleanupJobs
```log
Session 1 upload stats: manifests=1  files=1  jpegs=0  mpegs=0  dicoms=1  others=0
Session 1 results stats: total=1  success=2  cancelled=0
Session 1 success stats: total=2  jpegs=0  mpegs=0  dicoms=2  others=0  studies=0
Stats: #Sessions=1  #Manifests=1
```
Unsure exactly what the “results” stats are related to, almost a different version of the other 2.

Here, we would probably want to combine these messages such that we are just getting one message, i.e.
```log
Session Complete - { session: 1, misc: { stats: {
  upload: { manifests:1, files:1, jpegs:0, mpegs:0, dicoms:1, others:0 },
  results: { total: 1, success: 2, cancelled: 0},
  success: { total: 2, jpegs: 0, mpegs: 0, dicoms: 2, others: 0, studies: 0}
}}}
```

#### Cleanup
```log
1 of 2 sessions expired: [1]
1 of 1 manifests are expired: [1]
1 of 2 sessions to remove: [1]
1 of 1 manifests to remove: [1]
```
These could potentially become:
```log
Sessions Expired - { count: 1, total: 2, ids: [1] }
Manifests Expired - { count: 1, total: 1, ids: [1] }
```
It would be beneficial to key on the session or manifest Id in order to see when it was expired, but the logs under Session Complete may help and the messages are actually TRACE level and/or obsolete.

#### Network Requirements

For the monitoring portion of the logging to work, we need ImageMover Customers to enable outbound secure TCP ports (443) in order to communicate with our monitoring and elasticsearch stack.  When we can certify that there is no PHI within our outbound logs, this should be an easy sell.

Perhaps an alternate solution is to allow an engine (perhaps the admin console?) to package up and send a weekly email (SMTP) attaching the server logs that we can ingest and debug/process locally. 