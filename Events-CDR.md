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
