# Troubleshooting Notes

These are the main issues I ran into while working on the Wazuh lab.

## 1. Dashboard was not loading

The Dashboard initially returned an unavailable/503 response.

I checked the Dashboard service status and process, recent logs, the local connection to the Indexer, and authentication and TLS errors.

I eventually found that the internal service-account credential and the value stored in the Dashboard keystore were not aligned. I reset the credential, updated the keystore, restarted the affected services, and checked them again. After that, the Dashboard became accessible.

## 2. Dashboard-to-Indexer authentication errors

The Dashboard logs showed authentication exceptions.

I reviewed the configured service account, checked the keystore entries, and verified that the Dashboard service account could read the keystore. I then reset the internal service-account password using the Wazuh security tooling and updated the Dashboard keystore with the matching credential.

I have not included the password or any command output containing it in this repository.

## 3. TLS certificate errors

I also saw TLS handshake errors while troubleshooting the Dashboard connection.

I checked the root CA configured for the Dashboard, certificate file and directory permissions, whether the Dashboard service account could read the certificate files, and the certificate verification setting.

I kept certificate verification enabled. Disabling verification would have hidden the trust problem rather than fixing it.

## 4. Wazuh API response

The API endpoint was reachable, but it returned an authentication-required response. That confirms reachability only. I still need to verify a successful API login and a permitted API request.

## Checks I found useful

1. Check the service status with systemd.
2. Review logs around the time the issue occurred.
3. Check whether the expected service is listening.
4. Test file access as the service account, not just as root.
5. Verify the certificate chain using the configured CA.
6. Check that service credentials match without printing them in the terminal.
7. Restart only the services affected by the change.
8. Repeat the checks and record the result.

## Before sharing logs or screenshots

I remove passwords, tokens, API keys, private keys, internal IP addresses, hostnames, PC names, usernames, MAC addresses, and any other identifying details.
