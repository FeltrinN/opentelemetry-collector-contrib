# Field Mappings

*Warning 1*: This is an early draft to document how the native Journald fields are mapped in OTel. It is subject to changes and should not be considered stable in any form.
*Warning 2*: These mappings are not currently implemented, but will be once a fist agreement on the sematics is reached.

## Alerting levels

<!-- TODO: check mappings -->

Journald `PRIORITY` field represents the message severity according to [RFC5424 6.2.1](https://datatracker.ietf.org/doc/html/rfc5424#section-6.2.1).

| Journald priority | Journald priority description | OTel severity | OTel severity description | OTel severity text |
|-------------------|-------------------------------|---------------|---------------------------|--------------------|
| `0`               | Emergency: system is unusable |               |                           |                    |
| `1`               | Alert: action must be taken immediately |     |                           |                    |
| `2`               | Critical: critical conditions |               |                           |                    |
| `3`               | Error: error conditions       |               |                           |                    |
| `4`               | Warning: warning conditions   |               |                           |                    |
| `5`               | Notice: normal but significant condition |    |                           |                    |
| `6`               | Informational: informational messages |       |                           |                    |
| `7`               | Debug: debug-level messages   |               |                           |                    |

## Log fields

<!-- TODO: add mappings from Journald, Otel -->
<!-- TODO: check syslog for additional existing fields -->
<!-- TODO: check ECS for additional existing fields -->
<!-- TODO: check otel semantics guide for rules on defining new fields -->

| Journald field | Journald description | OTel field | OTel description |
|----------------|----------------------|------------|------------------|
