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

<!-- TODO: consider adding facility mappings -->

## Log fields

Journald provides a well defined [set of log fields](https://www.freedesktop.org/software/systemd/man/latest/systemd.journal-fields.html).

*Note*: in the table below the Journald descriptions have been amended for readability; refer to the source if more information is needed.

<!-- TODO: check syslog for additional existing fields -->
<!-- TODO: check ECS for additional existing fields -->
<!-- TODO: check otel semantics guide for rules on defining new fields -->

| Journald field | Journald description | OTel field | OTel description | Notes |
|----------------|----------------------|------------|------------------|-------|
| `MESSAGE`      | The human-readable message string for this entry. This is supposed to be the primary text shown to the user. | Body.[MESSAGE][LogsDataModelSemConv] | Description: A value containing the body of the log record (see the description of any type above). Can be for example a human-readable string message (including multi-line) describing the event in a free form or it can be a structured data composed of arrays and maps of other values. First-party Applications SHOULD use a string message. However, a structured body SHOULD be used to preserve the semantics of structured logs emitted by Third-party Applications. | |
| `MESSAGE_ID`   | A 128-bit message identifier ID for recognizing certain message types, if this is desirable. | | | New field required? |
| `PRIORITY`     | A priority value between 0 ("emerg") and 7 ("debug") formatted as a decimal string. This field is compatible with syslog's priority concept. | [SeverityText and SeverityNumber][LogsDataModelSemConv] | Severity Text (also known as log level). This is the original string representation of the severity as it is known at the source. / Severity Number numerical value of the severity, normalized to values described in this document. | See [Alerting levels](#alerting-levels) |
| `CODE_FILE`    | The code location generating this message, if known. Contains the source filename. | Attributes.[code.filepath][GenAttrSemConv] | The source code file name that identifies the code unit as uniquely as possible (preferably an absolute file path). |
| `CODE_LINE`    | The code location generating this message, if known. Contains the source line number. | Attributes.[code.lineno][GenAttrSemConv] | The line number in `code.filepath` best representing the operation. It SHOULD point within the code unit named in `code.function`. |
| `CODE_FUNC`    | The code location generating this message, if known. Contains the source function. | Attributes.[code.function][GenAttrSemConv] | The method or function name, or equivalent (usually rightmost part of the code unit's name). |
| `ERRNO`        | The low-level Unix error number causing this entry, if any. | Attributes.[error.type][AttrRegSemConv] | Describes a class of error the operation ended with. | We might want to use a custom attribute (e.g. error.type.unix) ensure that the numeric code is not confused with different numeric error codes |
| `INVOCATION_ID` | A randomized, unique 128-bit ID identifying each runtime cycle of the unit. This is different from _SYSTEMD_INVOCATION_ID in that it is only used for messages coming from systemd code (e.g. logs from the system/user manager or from forked processes performing systemd-related setup). | | | Keep in body or custom field? |
| `USER_INVOCATION_ID` | A randomized, unique 128-bit ID identifying each runtime cycle of the unit. This is different from _SYSTEMD_INVOCATION_ID in that it is only used for messages coming from systemd code (e.g. logs from the system/user manager or from forked processes performing systemd-related setup). | | | Keep in body or custom field? |
| `SYSLOG_FACILITY` | Syslog compatibility field containing the facility (formatted as decimal string) as specified in the original datagram. | Attributes.facility | | Could not find docs, but this seems to be consistent with syslog collector |
| `SYSLOG_IDENTIFIER` | Syslog compatibility field containing the identifier string (i.e. "tag") as specified in the original datagram. | | | Could not find docs, check syslog collector |
| `SYSLOG_PID`   | Syslog compatibility field containing the client PID as specified in the original datagram. | Attributes.proc_id | | Could not find docs, but this seems to be consistent with syslog collector |
| `SYSLOG_TIMESTAMP` | Syslog compatibility field containing the timestamp as specified in the original datagram. | | | Could not find docs, check syslog collector |
| `SYSLOG_RAW`   | The original contents of the syslog line as received in the syslog datagram. This field is only included if the MESSAGE= field was modified compared to the original payload or the timestamp could not be located properly and is not included in SYSLOG_TIMESTAMP=. | | | Could not find docs, check syslog collector |
| `DOCUMENTATION` | A documentation URL with further information about the topic of the log message. | | |  Keep in body or custom field? |
| `TID`          | The numeric thread ID (TID) the log message originates from. | Attributes.[thread.id][GenAttrSemConv] | Current "managed" thread ID (as opposed to OS thread ID). |
| `UNIT`         | The name of a unit. Used by the system and user managers when logging about specific units. | | | Keep in body or custom field? |
| `USER_UNIT`    | The name of a unit. Used by the system and user managers when logging about specific units. | | | Keep in body or custom field? |
| `_PID`         | The process ID of the process the journal entry originates from formatted as a decimal string. | ResourceAttributes.[process.pid][ResProcSemConv] | Process identifier (PID). |
| `_UID`         | The user ID of the process the journal entry originates from formatted as a decimal string. | | | Propose new resource attribute process.uid |
| `_GID`         | The group ID of the process the journal entry originates from formatted as a decimal string. | | | Propose new resource attribute process.gid |
| `_COMM`        | The name of the process the journal entry originates from. | ResourceAttributes.[process.executable.name][ResProcSemConv] | The name of the process executable. |
| `_EXE`         | The executable path of the process the journal entry originates from. | ResourceAttributes.[process.executable.path][ResProcSemConv] | The full path to the process executable. |
| `_CMDLINE`     | The command line of the process the journal entry originates from. | ResourceAttributes.[process.command_line][ResProcSemConv] | The full command used to launch the process as a single string representing the full command. |
| `_CAP_EFFECTIVE` | The effective capabilities(7) of the process the journal entry originates from. | | | Keep in body or custom field? |
| `_AUDIT_SESSION` | The session of the process the journal entry originates from, as maintained by the kernel audit subsystem. | | | Keep in body or custom field? |
| `_AUDIT_LOGINUID` | The login UID of the process the journal entry originates from, as maintained by the kernel audit subsystem. | | | Keep in body or custom field? |
| `_SYSTEMD_CGROUP` | The control group path in the systemd hierarchy of the process the journal entry originates from. | | | Keep in body or custom field? |
| `_SYSTEMD_SLICE` | The systemd slice unit name of the process the journal entry originates from. | | | Keep in body or custom field? |
| `_SYSTEMD_UNIT` | The systemd unit name of the process the journal entry originates from. | | | Keep in body or custom field? |
| `_SYSTEMD_USER_UNIT` | The unit name in the systemd user manager (if any) of the process the journal entry originates from. | | | Keep in body or custom field? |
| `_SYSTEMD_USER_SLICE` | The slice unit name in the systemd user manager (if any) of the process the journal entry originates from. <!-- TODO: unclear, description might be wrong  --> | | | Keep in body or custom field? |
| `_SYSTEMD_SESSION` | The systemd session ID (if any) of the process the journal entry originates from. | | | Keep in body or custom field? |
| `_SYSTEMD_OWNER_UID` | The owner UID of the systemd user unit or systemd session (if any) of the process the journal entry originates from. | | | Keep in body or custom field? |
| `_SELINUX_CONTEXT` | The SELinux security context (label) of the process the journal entry originates from. | | | Keep in body or custom field? |
| `_SOURCE_REALTIME_TIMESTAMP` | The earliest trusted timestamp of the message, if any is known that is different from the reception time of the journal. | | | Keep in body or custom field? |
| `_BOOT_ID`     | The kernel boot ID for the boot the message was generated in. | | | Keep in body or custom field? -> probably something like ResourceAttributes.host.boot_id |
| `_MACHINE_ID`  | The machine ID of the originating host. | ResourceAttributes.[host.id][ResHostSemConv] | Unique host ID. |
| `_SYSTEMD_INVOCATION_ID` | The invocation ID for the runtime cycle of the unit the message was generated in. | | | Keep in body or custom field? |
| `_HOSTNAME`    | The name of the originating host. | ResourceAttributes.[host.name][ResHostSemConv] | Name of the host. |
| `_TRANSPORT`   | How the entry was received by the journal service. | Attributes.[log.iostream][LogsSemConv] | The stream associated with the log. See below for a list of well-known values. | Only with `_TRANSPORT=stdout`. Other possible values (audit, driver, syslog, journal, kernel) would require a different field |
| `_STREAM_ID`   | Only applies to "_TRANSPORT=stdout" records: specifies a randomized 128-bit ID assigned to the stream connection when it was first created. | | | Keep in body or custom field? |
| `_LINE_BREAK`  | Only applies to "_TRANSPORT=stdout" records: indicates that the log message in the standard output/error stream was not terminated with a normal newline character ("\n", i.e. ASCII 10). | | | Keep in body or custom field? |
| `_NAMESPACE`   | If this file was written by a systemd-journald instance managing a journal namespace that is not the default, this field contains the namespace identifier. | | | Keep in body or custom field? |
| `_RUNTIME_SCOPE` | A string field that specifies the runtime scope in which the message was logged. If "initrd", the log message was processed while the system was running inside the initrd. If "system", the log message was generated after the system switched execution to the host root filesystem. | | | Keep in body or custom field? |
| `_KERNEL_DEVICE` | The kernel device name. | | | Keep in body or custom field? -> probably something like ResourceAttributes.os.kernel.device |
| `_KERNEL_SUBSYSTEM` | The kernel subsystem name. | | | Keep in body or custom field? -> probably something like ResourceAttributes.os.kernel.subsystem |
| `_UDEV_SYSNAME` | The kernel device name as it shows up in the device tree below /sys/. | | | Keep in body or custom field? -> probably something like ResourceAttributes.os.kernel.udev_sysname |
| `_UDEV_DEVNODE` | The device node path of this device in /dev/. | | | Keep in body or custom field? -> probably something like ResourceAttributes.os.kernel.udev_devnode |
| `_UDEV_DEVLINK` | Additional symlink names pointing to the device node in /dev/. This field is frequently set more than once per entry. | | | Keep in body or custom field? -> probably something like ResourceAttributes.os.kernel.udev_devlinks ; as there might be multiple entries this field probably needs to be a list of strings |
| `COREDUMP_UNIT` | Used to annotate messages containing coredumps from system and session units. | | | Keep in body or custom field? |
| `COREDUMP_USER_UNIT` | Used to annotate messages containing coredumps from system and session units. | | | Keep in body or custom field? |
| `OBJECT_PID`   | PID of the program that this message pertains to. | | | Keep in body or custom field?
| `OBJECT_UID`   | This is an additional field added automatically by systemd-journald. Its meaning is the same as _UID as described above, except that the process identified by PID is described, instead of the process which logged the message. | | | Keep in body or custom field?
| `OBJECT_GID`   | This is an additional field added automatically by systemd-journald. Its meaning is the same as _GID= as described above, except that the process identified by PID is described, instead of the process which logged the message. | | | Keep in body or custom field?
| `OBJECT_COMM`  | This is an additional field added automatically by systemd-journald. Its meaning is the same as _COMM= as described above, except that the process identified by PID is described, instead of the process which logged the message. | | | Keep in body or custom field?
| `OBJECT_EXE`   | This is an additional field added automatically by systemd-journald. Its meaning is the same as _EXE= as described above, except that the process identified by PID is described, instead of the process which logged the message. | | | Keep in body or custom field?
| `OBJECT_CMDLINE` | This is an additional field added automatically by systemd-journald. Its meaning is the same as _CMDLINE as described above, except that the process identified by PID is described, instead of the process which logged the message. | | | Keep in body or custom field?
| `OBJECT_AUDIT_SESSION` | This is an additional field added automatically by systemd-journald. Its meaning is the same as _AUDIT_SESSION as described above, except that the process identified by PID is described, instead of the process which logged the message. | | | Keep in body or custom field?
| `OBJECT_AUDIT_LOGINUID` | This is an additional field added automatically by systemd-journald. Its meaning is the same as _AUDIT_LOGINUID as described above, except that the process identified by PID is described, instead of the process which logged the message. | | | Keep in body or custom field?
| `OBJECT_SYSTEMD_CGROUP` | This is an additional field added automatically by systemd-journald. Its meaning is the same as _SYSTEMD_CGROUP as described above, except that the process identified by PID is described, instead of the process which logged the message. | | | Keep in body or custom field?
| `OBJECT_SYSTEMD_SESSION` | This is an additional field added automatically by systemd-journald. Its meaning is the same as _SYSTEMD_SESSION as described above, except that the process identified by PID is described, instead of the process which logged the message. | | | Keep in body or custom field?
| `OBJECT_SYSTEMD_OWNER_UID` | This is an additional field added automatically by systemd-journald. Its meaning is the same as _SYSTEMD_OWNER_UID as described above, except that the process identified by PID is described, instead of the process which logged the message. | | | Keep in body or custom field?
| `OBJECT_SYSTEMD_UNIT` | This is an additional field added automatically by systemd-journald. Its meaning is the same as _SYSTEMD_UNIT as described above, except that the process identified by PID is described, instead of the process which logged the message. | | | Keep in body or custom field?
| `OBJECT_SYSTEMD_USER_UNIT` | This is an additional field added automatically by systemd-journald. Its meaning is the same as _SYSTEMD_USER_UNIT as described above, except that the process identified by PID is described, instead of the process which logged the message. | | | Keep in body or custom field?
| `__CURSOR`     | The cursor for the entry. A cursor is an opaque text string that uniquely describes the position of an entry in the journal and is portable across machines, platforms and journal files. | Attributes.[log.record.uid][LogsSemConv] | A unique identifier for the Log Record. | Not sure about this one: could help identifiying entries that have been doubly-processed through journald, but it will not help deduplicating entries coming from multiple receivers (e.g. log collected by both syslog and journald) |
| `__REALTIME_TIMESTAMP` | The wallclock time (CLOCK_REALTIME) at the point in time the entry was received by the journal. | | | Keep in body or custom field? |
| `__MONOTONIC_TIMESTAMP` | The monotonic time (CLOCK_MONOTONIC) at the point in time the entry was received by the journal in microseconds, formatted as a decimal string. | | | Keep in body or custom field? |
| `__SEQNUM` | The sequence number of this journal entry in the journal file it originates from. | | | Keep in body or custom field? |
| `__SEQNUM_ID` | The sequence number number ID of this journal entry in the journal file it originates from. | | | Keep in body or custom field? |

<!-- Sources -->
[AttrRegSemConv]: https://github.com/open-telemetry/semantic-conventions/blob/main/docs/attributes-registry/error.md
[GenAttrSemConv]: https://github.com/open-telemetry/semantic-conventions/blob/main/docs/general/attributes.md
[LogsDataModelSemConv]: https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/logs/data-model.md
[LogsSemConv]: https://github.com/open-telemetry/semantic-conventions/blob/main/docs/general/logs.md
[ResHostSemConv]: https://github.com/open-telemetry/semantic-conventions/blob/main/docs/resource/host.md
[ResProcSemConv]: https://github.com/open-telemetry/semantic-conventions/blob/main/docs/resource/process.md
