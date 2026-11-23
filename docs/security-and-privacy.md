# Security and Privacy

This repository is public. Treat every committed file, image, command output, and diagram as information available to anyone.

## Redaction performed

The published materials use generalized labels and omit:
- private IP addresses, internal URLs, and network ranges;
- PC names, hostnames, usernames, MAC addresses, and device identifiers;
- passwords, API credentials, agent authentication keys, and tokens;
- private keys, CA certificates, and other trust material;
- browser tabs and unrelated desktop information.

The included dashboard screenshot has been cropped to remove the browser address bar and unrelated desktop context. It does not show the internal service address.

## Never commit

Do not commit:
- `.env` files or shell history containing secrets;
- Wazuh internal user databases or password backups;
- API credentials or agent enrollment keys;
- `.pem`, `.key`, `.p12`, or `.pfx` private key material;
- raw configuration backups containing internal addresses or credentials;
- unredacted logs, screenshots, or diagnostic output.

## Before publishing new evidence

1. Inspect the entire image or log, not only the obvious center.
2. Check terminal prompts, browser address bars, tabs, taskbars, file paths, and window titles.
3. Replace private values with neutral placeholders such as `<REDACTED_HOST>`.
4. Verify that the redaction is irreversible (crop or flatten the redacted image; do not rely on a movable overlay).
5. Search the staged files for secrets and private network identifiers.
6. Review the Git diff before pushing.

If a real credential is ever committed, removing it from the latest file is not enough. Rotate/revoke the credential and follow GitHub's guidance for removing sensitive data from repository history.

## Authorization

All deployment and troubleshooting work should be performed only on systems and networks where you have explicit authorization.
