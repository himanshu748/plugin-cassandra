# Cassandra cancellation UI QA for issue 135

These are genuine, inspected screenshots from the visible Comet browser running official Kestra 1.3.39 with the reviewed plugin JAR. They show successful scheduled recovery and twelve-row trigger output. Precise cancellation timing, thread identity and channel cleanup are separate driver/API evidence in the corresponding verification JSON, not deductions from screenshots.

The source/test PR branch contains only six Java files. This separate evidence branch contains only relevant, sanitized QA attachments and does not enter the PR code diff.

- STORE: automatic 90-second worker deadline; the held marked later-page channel closed at that evaluation's deadline, before the 240-second driver deadline. Scheduled recovery `4sfeaVsudzWB2Cgcxoenal` succeeded. Stored ION IDs 0–11 and expected QA payloads were verified. The original helper was absent after recovery.
- Initialization: the worker returned at its 90-second deadline while the exact driver builder helper remained alive. Metadata release allowed the builder to finish; the late handle closed 46.64 seconds after release, without an application query from that canceled helper. Scheduled recovery `mKdTjT56XC3lWBrpYvZ44` succeeded with 12 rows; the original helper was absent. The capture's initial 45-second close wait expired just before closure; existing events and the preserved early snapshot completed verification of the same run without rearming the fault.

The gate preserves TLS/mTLS and hostname validation, uses exact helper/session/channel ownership, and captures a unique arm nonce when the outbound request is sent. Each case runs in its own process with one enabled trigger. The final STORE runner copied only its own stopped single-flow repository, preserving the actual UI import. Initialization used a fresh empty repository and its flow was saved through Comet.

JAR SHA256: `766e4f7320ecc7365e5b09c649f5d4eba447117000a8ce32aa5a288acf91cfb9`.

The exact Java files were carried unchanged onto current main `9870fcbe8e6f32b75a37ffc7cb8db24756c2707e`. Fresh validation there passed 29 cancellation cases (zero failures/errors/skips), JAR assembly, all 25 documentation lint rules and six-file formatting. This separate build validates current build conventions; the live UI screenshots use the exact earlier reviewed JAR above.

UTC timestamps appear in JSON; Comet displayed local time at UTC+05:30 during capture. Credentials, signed download URLs, private account details, raw authentication logs, database files and thread dumps are excluded. The temporary QA token was revoked, private credential files removed, and only owned services and the localhost QA tab were stopped/closed.

Limitations: a synchronous driver builder can remain alive until it returns or fails; cancellation cannot close a session before it exists. Server-side query termination is not guaranteed. Buffered conversion/storage and completion races are best-effort. A combined cloud Gradle check was resource-killed; two unchanged upstream files fail global formatting. AI assistance was used; maintainer review is requested through a draft PR.
