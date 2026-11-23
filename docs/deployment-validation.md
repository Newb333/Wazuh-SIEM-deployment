# Deployment and Validation

## My lab setup

- Hypervisor: Oracle VirtualBox
- Operating system: Ubuntu Server 24.04 LTS
- Deployment: All-in-one Wazuh VM
- Network adapter: Bridged
- Services checked: Wazuh Manager, Wazuh Indexer, Wazuh Dashboard, Filebeat

I have removed the actual IP addresses, hostnames, usernames, and internal URLs from this repository.

## Service checks

I used systemd to check the status of the main services:

```bash
sudo systemctl --no-pager --full status \
  wazuh-indexer wazuh-manager filebeat wazuh-dashboard
```

To review recent logs:

```bash
sudo journalctl -u wazuh-dashboard --since "15 minutes ago" --no-pager
sudo journalctl -u wazuh-indexer --since "15 minutes ago" --no-pager
sudo journalctl -u wazuh-manager --since "15 minutes ago" --no-pager
sudo journalctl -u filebeat --since "15 minutes ago" --no-pager
```

I also used `ss -lntp` to inspect listening services. I have not included raw output because it can expose internal addresses and process details.

## Checking Indexer TLS

I tested the connection using the configured CA certificate. The example below uses placeholders; I have not included the real lab paths or addresses.

```bash
openssl s_client \
  -connect <INDEXER_HOST>:9200 \
  -CAfile <PATH_TO_ROOT_CA> \
  -brief </dev/null
```

I kept certificate verification enabled.

## API check

The Wazuh API endpoint responded with an authentication-required response. This showed that the endpoint was reachable, but it did not prove that API authentication was working. I still need to complete that test.

## Results I recorded

- All four core services were active after troubleshooting.
- I was able to access the Dashboard after correcting the service-account credential issue.
- I checked the Dashboard-to-Indexer certificate trust configuration.
- The Wazuh API endpoint was reachable, but authentication remains unverified.
- The dashboard screenshot showed zero enrolled agents.

## Remaining validation

The services running does not by itself prove that the full SIEM pipeline is working. My next step is to enroll a test agent, confirm it appears in the Dashboard, generate a controlled event, and check that the event is received.
