# Field Mappings

*Warning 1*: This is an early draft to document how the native Journald fields are mapped in OTel. It is subject to changes and should not be considered stable in any form.
*Warning 2*: These mappings are not currently implemented, but will be once a fist agreement on the sematics is reached.

## Alerting levels

Journald `PRIORITY` field represents the message severity according to the Syslog protocol ([RFC5424 6.2.1](https://datatracker.ietf.org/doc/html/rfc5424#section-6.2.1)). Mappings for this protocol are already defined in the [Logs Data Model Appendix B](https://opentelemetry.io/docs/specs/otel/logs/data-model-appendix/#appendix-b-severitynumber-example-mappings).

| Journald priority | Journald priority description | OTel SeverityText | OTel SeverityNumber   |
|-------------------|-------------------------------|-------------------|-----------------------|
| `0`               | Emergency: system is unusable | `Emergency`       | `21` (FATAL)          |
| `1`               | Alert: action must be taken immediately | `Alert` | `19` (ERROR3)         |
| `2`               | Critical: critical conditions | `Critical`        | `18` (ERROR2)         |
| `3`               | Error: error conditions       | `Error`           | `17` (ERROR)          |
| `4`               | Warning: warning conditions   | `Warning`         | `13` (WARN)           |
| `5`               | Notice: normal but significant condition | `Notice` | `10` (INFO2)        |
| `6`               | Informational: informational messages | `Informational` | `9` (INFO)      |
| `7`               | Debug: debug-level messages   | `Debug`           | `5` (DEBUG)           |

## Log fields

<!-- TODO: add mappings from Journald, Otel -->
<!-- TODO: check syslog for additional existing fields -->
<!-- TODO: check ECS for additional existing fields -->
<!-- TODO: check otel semantics guide for rules on defining new fields -->

| Journald field | Journald description | OTel field | OTel description |
|----------------|----------------------|------------|------------------|
