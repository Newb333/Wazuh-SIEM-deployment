# Troubleshooting Notes

This document summarizes the issues investigated in the lab. It avoids publishing live credentials, private addresses, usernames, or certificate material.

## 1. Dashboard unavailable / initial 503 response

**Observed:** The Dashboard service initially did not provide a usable interface.

**Investigation:**
- Checked the Dashboard systemd state and process.
- Reviewed recent Dashboard logs.
- Checked local connectivity to the Indexer.
- Reviewed TLS handshake and authentication errors.

**Resolution work:** The internal service-account credential was corrected and the Dashboard keystore was synchronized with the intended credential. The relevant services were restarted and checked. The Dashboard subsequently became accessible.

**Lesson:** Dashboard availability depends on more than the Dashboard process being active; its backend authentication and TLS trust must also work.

## 2. Dashboard-to-Indexer authentication failure

**Observed:** Dashboard logs contained authentication exceptions.

**Investigation:**
- Reviewed the configured Indexer username and credential source.
- Confirmed the Dashboard keystore contained the expected credential entries.
- Checked that the service account could read the keystore.
- Compared the configured service credential with the Indexer's internal user configuration.

**Resolution work:** The internal service-account password was reset through the Wazuh security tooling, and the Dashboard keystore was aligned with the updated value. Secret values are intentionally not included here.

**Lesson:** When a service credential is rotated, every dependent component must be reviewed. Avoid placing passwords directly in shell history, screenshots, or Git.

## 3. TLS certificate trust errors

**Observed:** Logs included TLS errors indicating that the peer did not accept the presented certificate.

**Investigation:**
- Reviewed the root CA path configured for the Dashboard.
- Checked certificate file permissions and parent-directory traversal permissions.
- Tested the TLS connection as the Dashboard service account.
- Reviewed the configured certificate verification mode.

**Resolution work:** The shared root CA configuration and service-account access were reviewed. Certificate-based verification was retained; disabling verification was not used as the final fix.

**Lesson:** Validate the certificate chain and service identity. Do not solve trust problems by turning off TLS verification.

## 4. Wazuh API reachability versus authentication

**Observed:** The API endpoint was reachable and returned an authentication-required response.

**Interpretation:** Network/service reachability was demonstrated. Successful API authentication was not demonstrated and is not claimed in this project.

**Next check:** Complete API authentication using a securely managed credential, then validate a permitted read-only API request.

## 5. General diagnostic sequence

1. Check whether the relevant systemd service is active.
2. Inspect recent logs for the same time window as the failure.
3. Confirm that the expected local service is listening.
4. Test connectivity from the actual service account where permissions matter.
5. Validate certificate trust using the intended CA.
6. Check service-account credential consistency and keystore contents without printing secret values.
7. Restart only the affected services after a controlled configuration change.
8. Repeat the checks and record the observed result.

## Redaction rule

Before sharing terminal output, remove:
- passwords, API keys, tokens, and authentication keys;
- private keys and certificate contents;
- private IP addresses and internal URLs;
- hostnames, PC names, usernames, MAC addresses, and device identifiers;
- browser tabs, account names, and unrelated desktop context.
