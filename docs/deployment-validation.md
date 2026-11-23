# Deployment and Validation Notes

## Lab environment

- Hypervisor: Oracle VirtualBox
- Server OS: Ubuntu Server 24.04 LTS
- Deployment model: all-in-one Wazuh virtual machine
- Network mode: bridged adapter
- Core services reviewed: Wazuh Manager, Wazuh Indexer, Wazuh Dashboard, Filebeat

Exact IP addresses, hostnames, usernames, and internal URLs are intentionally excluded.

## Deployment work

The lab was brought up as a single virtual machine running the Wazuh server-side components. The work then focused on service readiness and the dependencies between the Dashboard, Indexer, Manager, and Filebeat.

## Operational checks

The following commands are representative administrative checks. Run them only on your own lab server.

### Check service state

```bash
sudo systemctl --no-pager --full status \
  wazuh-indexer wazuh-manager filebeat wazuh-dashboard
```

### Review recent service logs

```bash
sudo journalctl -u wazuh-dashboard --since "15 minutes ago" --no-pager
sudo journalctl -u wazuh-indexer --since "15 minutes ago" --no-pager
sudo journalctl -u wazuh-manager --since "15 minutes ago" --no-pager
sudo journalctl -u filebeat --since "15 minutes ago" --no-pager
```

### Inspect listening services

```bash
sudo ss -lntp
```

Review only the ports expected for the services enabled in your deployment. Do not publish raw output if it contains private addresses, process arguments, or usernames.

### Validate the Indexer TLS connection

Use the configured CA certificate rather than disabling certificate verification:

```bash
openssl s_client \
  -connect <INDEXER_HOST>:9200 \
  -CAfile <PATH_TO_ROOT_CA> \
  -brief </dev/null
```

Replace placeholders locally. Do not commit CA certificates or private keys.

### Validate API reachability

The Wazuh API endpoint was checked and returned an authentication-required response. This established that the endpoint was reachable, but did not prove that API credentials worked. Keep reachability and authentication as separate validation items.

## Recorded outcome

- All four core services were observed active after troubleshooting.
- The Dashboard became accessible after resolving the Dashboard-to-Indexer service credential mismatch and reviewing TLS trust.
- Dashboard-to-Indexer certificate validation was checked.
- The Wazuh API endpoint was reachable but requested authentication.
- The captured dashboard showed no registered agents.

## Evidence boundaries

A running service is not the same as an end-to-end validated SIEM pipeline. To claim endpoint monitoring, enroll a dedicated test agent, confirm its status, generate a controlled test event, and verify that the event reaches the Dashboard. That workflow remains pending in this project.
