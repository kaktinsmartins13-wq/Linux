# Security Policy

## Reporting a vulnerability

Do not publish an unpatched kernel security vulnerability in a public issue or pull request.

For vulnerabilities in unmodified upstream Linux code, use the official Linux kernel security reporting process described by the Linux kernel documentation. Include the affected subsystem, kernel revision, configuration, reproduction steps, impact, and proof of concept if available.

For vulnerabilities introduced only by fork-specific changes in this repository, contact the repository owner privately through GitHub. Do not include secrets or personal data in reports.

## Response expectations

- Do not guarantee a response or fix time because this fork may not have a staffed security team.
- Fork-only patches should be triaged, fixed, tested, and disclosed after the upstream impact is understood.
- Shared upstream vulnerabilities should be handled through established upstream maintainers and disclosure processes.

## Safe testing

Test kernel vulnerabilities only on disposable virtual machines or dedicated systems that you own or are authorized to test.
