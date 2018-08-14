# Fetching CDR Information from the event

We have two cases where CDRs are generated
- Single user scenario
- Two users scenario

### Single user scenario
In this event, we receive a single CDR for a Call UUID. The following is a descriptive table of the CDR keys received in JSON Format

| CDR Key | Sample Value | Description
| -------- | -------- | -------- |
| Event-Name  | CHANNEL_DESTROY | Specifies that this event is invoked when the channel is destroyed.
| Channel-Call-UUID  | 9d66b3a6-c48e-4811-ab64-9ede683f7cc0 | Unique ID for a call
| Event-Date-Local | 2018-08-14 10:24:46 | Date and time of event invocation
| Call-Direction | inbound | Current call direction 
| Hangup-Cause | NORMAL_CLEARING | Key that specifies if call was patched or not with reason in the Hangup-Cause
| Caller-Caller-ID-Number | 1001 | Caller ID
| Caller-Destination-Number | 61998999 | Number call is patched with
| variable_duration | 14 | Call duration in seconds
| variable_answersec | 0 | Time taken to answer the call in seconds
| variable_billsec | 0 | Time taken in a bridged session in seconds
| variable_uduration | 14600035 | Call duration in microseconds
| variable_answerusec | 0 | Time taken to answer the call in microseconds
| variable_billusec | 0 | Time taken in a bridged session in microseconds
| variable_start_stamp | 2018-08-14 10:24:32 | Call start Datetime
| variable_end_stamp | 2018-08-14 10:24:46 | Call end Datetime


### Two user scenario
- Case : User registered on 1001 calls up user registered on 1000, 1000 picks up the call in 10 seconds and terminates it in 20 seconds

At the end of the call, we receive 2 CDRs, following is their description
#### First CDR

| CDR Key | Sample Value | Description
| -------- | -------- | -------- |
| Event-Name  | CHANNEL_DESTROY | Specifies that this event is invoked when the channel is destroyed.
| Channel-Call-UUID  | a2c40495-cf62-404d-ac37-08444f892387 | Unique ID for a call
| Event-Date-Local | 2018-08-14 11:23:37 | Date and time of event invocation
| Call-Direction | outbound | Current call direction 
| Hangup-Cause | NORMAL_CLEARING | Key that specifies if call was patched or not with reason in the Hangup-Cause
| Caller-Caller-ID-Number | 1001 | Caller ID
| Caller-Destination-Number | 1000 | Number call is patched with
| variable_duration | 31 | Total Call duration in seconds
| variable_answersec | 10 | Time taken to answer the call in seconds
| variable_billsec | 21 | Time taken in a bridged session in seconds
| variable_uduration | 31500008 | Call duration in microseconds
| variable_answerusec |10480001 | Time taken to answer the call in microseconds
| variable_billusec | 21020007 | Time taken in a bridged session in microseconds
| variable_start_stamp | 2018-08-14 11:23:06 | Call start Datetime
| variable_end_stamp | 2018-08-14 11:23:37 | Call end Datetime
| variable_answer_stamp | 2018-08-14 11:23:16 | Call answer Datetime
| variable_sip_contact_user | 1000 | Contact endpoint
| Other-Leg-Unique-ID | 6cd419d9-8510-4ae0-bfaa-1b02e4877a36 | Unique-ID of the other leg

#### Second CDR

| CDR Key | Sample Value | Description
| -------- | -------- | -------- |
| Event-Name  | CHANNEL_DESTROY | Specifies that this event is invoked when the channel is destroyed.
| Channel-Call-UUID  | 6cd419d9-8510-4ae0-bfaa-1b02e4877a36 | Unique ID for a call
| Event-Date-Local | 2018-08-14 11:23:37 | Date and time of event invocation
| Call-Direction | inbound | Current call direction 
| Hangup-Cause | NORMAL_CLEARING | Key that specifies if call was patched or not with reason in the Hangup-Cause
| Caller-Caller-ID-Number | 1001 | Caller ID
| Caller-Destination-Number | 1000 | Number call is patched with
| variable_duration | 31 | Total Call duration in seconds
| variable_answersec | 10 | Time taken to answer the call in seconds
| variable_billsec | 21 | Time taken in a bridged session in seconds
| variable_uduration | 31500008 | Call duration in microseconds
| variable_answerusec |10480001 | Time taken to answer the call in microseconds
| variable_billusec | 21020007 | Time taken in a bridged session in microseconds
| variable_start_stamp | 2018-08-14 11:23:06 | Call start Datetime
| variable_end_stamp | 2018-08-14 11:23:37 | Call end Datetime
| variable_answer_stamp | 2018-08-14 11:23:16 | Call answer Datetime
| variable_sip_contact_user | 1001 | Contact endpoint
| Other-Leg-Unique-ID | a2c40495-cf62-404d-ac37-08444f892387 | Unique-ID of the other leg