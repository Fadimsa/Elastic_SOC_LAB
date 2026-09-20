# Elastic SOC Lab

![Elastic](https://img.shields.io/badge/Elastic-Stack-005571?logo=elastic)
![Kibana](https://img.shields.io/badge/Kibana-SIEM-005571?logo=elastic)
![Focus](https://img.shields.io/badge/Focus-SOC%20Operations-blue)

## Overview

This repository documents my hands-on work building and investigating a Security Operations Centre (SOC) lab using the Elastic Stack.

The project focuses on understanding how endpoint telemetry is collected, centrally managed, indexed, searched, and investigated. It demonstrates my practical experience with Elasticsearch, Kibana, Elastic Agent, and Fleet Server in an authorised training environment.

## Objectives

- Understand the architecture of an Elastic-based SOC.
- Configure the core components of the Elastic Stack.
- Enrol and manage endpoint agents through Fleet.
- Collect and inspect security telemetry.
- Use Kibana to search and investigate events.
- Apply a structured SOC triage and documentation process.

## Technologies

- Elasticsearch
- Kibana
- Elastic Agent
- Fleet Server
- Elastic Integrations
- KQL


## Architecture

```text
+------------------+
| Monitored Host   |
| Elastic Agent    |
+--------+---------+
         |
         | Telemetry and policy communication
         v
+------------------+
| Fleet Server     |
| Agent Management |
+--------+---------+
         |
         v
+------------------+        +------------------+
| Elasticsearch    | <----> | Kibana           |
| Storage/Search   |        | Investigation    |
+------------------+        +------------------+
```

## Component Roles

### Elasticsearch

Elasticsearch is the storage and search layer of the Elastic Stack. It indexes and stores security events, logs, alerts, and endpoint telemetry so that analysts can search and analyse the data efficiently.

The default HTTP API port is:

```text
9200
```

### Kibana

Kibana is the analyst interface for the Elastic Stack. It provides access to Discover, dashboards, visualisations, and investigation tools used to review security events.

The default web interface port is:

```text
5601
```

### Elastic Agent

Elastic Agent is installed on monitored systems to collect logs, metrics, and security telemetry. It provides a unified method for data collection and can be centrally managed through Fleet.

### Fleet Server

Fleet Server provides central management for Elastic Agents. It allows administrators to enrol agents, assign policies, configure integrations, and monitor agent health.

A commonly used Fleet Server port is:

```text
8220
```

## Deployment

### Elasticsearch and Kibana Installation

The first stage of the lab involved deploying Elasticsearch and Kibana on a Linux host using the provided Debian packages.

Installation was done using `dpkg`, as shown below:

<img width="1601" height="847" alt="image" src="https://github.com/user-attachments/assets/08e99d01-b272-4e16-bb92-abb12566c999" />

### Starting Elasticsearch

The Elasticsearch service was then started, enabled to launch at boot, and verified as running:

<img width="1901" height="860" alt="image" src="https://github.com/user-attachments/assets/d9343249-2f1f-423b-a88f-f120db3058a0" />

### Kibana Deployment

The same installation process used for Elasticsearch was followed for Kibana:

<img width="1502" height="810" alt="image" src="https://github.com/user-attachments/assets/d6936fd7-1d81-4f15-a46f-e161015e5938" />

Kibana's configuration file was then edited to support the Fleet Server installation, using `nano`:

<img width="1835" height="851" alt="image" src="https://github.com/user-attachments/assets/5854b66a-9cef-4301-a0e8-9022c5c2ff85" />

### Starting Kibana

Kibana was started as a systemd service:

<img width="1901" height="802" alt="image" src="https://github.com/user-attachments/assets/1876658d-5422-4677-943b-61966f33fa79" />

and accessed through its local web interface on port `5601`, as shown below:

<img width="1882" height="857" alt="image" src="https://github.com/user-attachments/assets/57f71bee-3656-4cec-87e8-00a1e426459f" />

Next, an enrollment token and a verification code were generated, in two steps:

1. Create an enrollment token:

```bash
/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana
```

<img width="1901" height="122" alt="image" src="https://github.com/user-attachments/assets/f3ec2029-da98-42b1-a6e5-436f8a75a4ee" />

2. Create a verification code:

```bash
/usr/share/kibana/bin/kibana-verification-code
```

<img width="1707" height="262" alt="image" src="https://github.com/user-attachments/assets/225ddf60-60d1-43c0-9d62-984deae15b68" />

<img width="1880" height="812" alt="image" src="https://github.com/user-attachments/assets/f8ab88a0-a67d-45c2-b1a1-f6532aa9fc1b" />

Kibana was then ready, as shown below:

<img width="1907" height="865" alt="image" src="https://github.com/user-attachments/assets/cd980d76-6808-4ae7-b6f7-99c00ba4e00d" />

After entering the username and password:

<img width="1802" height="862" alt="image" src="https://github.com/user-attachments/assets/3cd474ca-bfcd-4039-8b58-942d44498560" />

### Deploying Fleet Server and Elastic Agent

From the Kibana menu, under the Management section, there is a Fleet option. After clicking on it, the following is shown:

<img width="1846" height="867" alt="image" src="https://github.com/user-attachments/assets/a971bc88-19a7-42ab-ad9d-88e36a908aeb" />

After clicking "Add Fleet Server":

1. Entered the name `fleet-server`
2. Entered the lab machine IP as the URL: `https://IP:8220`
3. Clicked "Generate Fleet Server policy"

From here, Elastic generates a command to run in the terminal to install Fleet Server on the host.

After selecting linux-x86 as shown below:

<img width="1901" height="667" alt="image" src="https://github.com/user-attachments/assets/a4c9b4e6-fdd3-4b96-9926-1ca4810a3e47" />

The command was copied and the `--insecure` flag was added, since the lab uses self-signed TLS certificates. The command was run in root mode, with the following result:

<img width="1901" height="867" alt="image" src="https://github.com/user-attachments/assets/e93a9ba4-1f09-47a1-829e-59f5eb39c520" />

The agent installed successfully, and it can be confirmed as enrolled in the interface:

<img width="1912" height="815" alt="image" src="https://github.com/user-attachments/assets/7cb6ef74-3084-4422-b520-069e821a63a7" />

### Agent Policies and Integrations

Agent policies can be reviewed from Agent Policies > Fleet Server Policy:

<img width="1911" height="837" alt="image" src="https://github.com/user-attachments/assets/5a74aa0d-282a-4011-875c-fe74350acf18" />

As shown above, there are two Agent integrations. By default, the System integration collects host-level telemetry commonly used for security monitoring and troubleshooting:

- System logs such as syslog and authentication logs
- Basic system metrics such as CPU, memory, and process activity

### Confirming Log Ingestion

<img width="1917" height="471" alt="image" src="https://github.com/user-attachments/assets/e174fae4-5e75-464f-a94a-166b17fdd2db" />

### Elastic Integrations

<img width="1917" height="862" alt="image" src="https://github.com/user-attachments/assets/4a1bcec0-7b41-4b29-a55b-fbbc97c03912" />

Elastic's hundreds of integrations can be browsed and installed either in the form of an Agent through a Fleet server, or as a Beat. Each integration's details page provides an option to add the integration directly or to view installation instructions as commands.

As a test, the Apache HTTP server integration was added, selecting the previous Fleet agent:

<img width="1917" height="687" alt="image" src="https://github.com/user-attachments/assets/d191ead1-251f-4e59-867e-3246a8aa0390" />

### Managing Custom Log Types

The System and Apache integrations used earlier are pre-packaged: Elastic handles both collecting and parsing the data automatically. Real environments, however, often include proprietary tools or custom applications with their own log format that no built-in integration covers. This section documents how I onboarded one of those into the stack manually.

**Scenario:** a custom VPN solution producing its own log format, which needed to be ingested and made searchable in Elasticsearch. I generated 500 sample VPN log entries at `/var/log/vpnlog` using a provided Python script, then inspected the raw format to identify the fields that needed to be extracted: a timestamp, an event action, a username, a source IP, a VPN client IP, and a VPN server region.

<img width="1661" height="591" alt="image" src="https://github.com/user-attachments/assets/dfe3a18a-ebe5-4441-a0a0-d3c824219528" />

**Building an ingest pipeline.** Since this log source has no built-in integration, Elasticsearch needed to be told explicitly how to parse it. I created a new ingest pipeline (`vpn.logs.pipeline`) in Kibana under Stack Management → Ingest Pipelines, then added two processors:

- **Grok processor** on the `message` field, to extract the six fields identified above out of the raw log line into structured fields (`event.time_string`, `event.action`, `user.name`, `source.ip`, `vpn.client.ip`, `vpn.server.region`).
- **Date processor** on `event.time_string`, converting it into Elasticsearch's native `@timestamp` field so the events sort and filter correctly by time.

<img width="1607" height="422" alt="image" src="https://github.com/user-attachments/assets/6c066c5f-1ecc-44a0-b734-c7533e903e5c" />
<img width="900" height="610" alt="image" src="https://github.com/user-attachments/assets/cec3ca69-6ea4-4962-9422-920d06eab6a8" />
<img width="897" height="607" alt="image" src="https://github.com/user-attachments/assets/63b78500-a725-45d4-b8ca-9c812fd3ab32" />
<img width="893" height="516" alt="image" src="https://github.com/user-attachments/assets/7fa3fee8-55f5-41c3-8de5-198fad712b40" />



**Shipping the logs.** With the pipeline built, I added the **Custom Logs (Filestream)** integration from the Integrations menu, applied it to the Fleet Server Policy, pointed it at `/var/log/vpnlog`, and set it to use the `vpn.logs.pipeline` ingest pipeline created above, before saving and deploying the change.

<img width="1897" height="390" alt="image" src="https://github.com/user-attachments/assets/530b91b1-3a6b-47ce-b3b1-635fbf5756bf" />

**Confirming ingestion and parsing.** Back in Discover, I filtered on `event.module: "filestream"` over the last 24 hours and added the parsed fields (`event.action`, `user.name`, `source.ip`, `vpn.client.ip`, `vpn.server.region`) as table columns to confirm the Grok pattern had extracted them correctly, then saved the view as "VPN Logs" for reuse.

<img width="1917" height="717" alt="image" src="https://github.com/user-attachments/assets/7d4318da-fe9f-4952-a9eb-bf50668069e5" />

**Why this matters for a SOC:** most real environments have at least one log source that isn't covered by a pre-built integration. Being able to inspect a raw log format, build a parsing pipeline, and validate the result in Discover — rather than relying only on out-of-the-box integrations — reflects the kind of onboarding work a SOC/detection engineer does when a new data source needs to be brought into the SIEM.

## Validation

The deployment was validated by checking:

- Elasticsearch service status.
- Kibana service status.
- Listening ports.
- Kibana browser access.
- Successful authentication using the built-in `elastic` user.

Sensitive credentials, tokens, and verification codes have not been included in this repository.

## Data Flow

1. Elastic Agent collects telemetry from a monitored endpoint.
2. The agent communicates with Fleet Server for management and policy updates.
3. Security events are sent for ingestion and processing.
4. Elasticsearch indexes and stores the events.
5. Kibana retrieves the indexed data.
6. SOC analysts use Kibana to search, filter, and investigate activity.

## SOC Investigation Workflow

My investigation workflow was based on the following process:

1. Verify that the Elastic Agent is enrolled and healthy.
2. Confirm that the expected data source is available.
3. Select the relevant data view in Kibana.
4. Define an appropriate time range.
5. Search events using KQL and relevant fields.
6. Examine hostnames, usernames, processes, IP addresses, event actions, and outcomes.
7. Identify suspicious behaviour or possible indicators of compromise.
8. Record the evidence and prepare an investigation summary.

## Example KQL Queries

```kql
host.name : "example-host"
```

```kql
user.name : "example-user"
```

```kql
event.category : "process"
```

```kql
event.category : "network"
```

```kql
event.category : "authentication" and event.outcome : "failure"
```

These queries are examples for investigation practice. Field names may vary depending on the data source and Elastic integration.

## Evidence

The `screenshots` directory contains selected evidence from my lab work, including:

- Fleet and Elastic Agent status.
- Enrolled agent configuration.
- Kibana data views.
- Event investigation in Discover.
- KQL searches and returned event fields.
- Elastic Stack component configuration.

Sensitive information, credentials, tokens, VPN details, flags, and restricted training answers have been removed.

## Skills Demonstrated

- Elastic Stack architecture
- Elasticsearch fundamentals
- Kibana investigation workflow
- Fleet and Elastic Agent management
- Security telemetry collection
- Log search and event filtering
- KQL query development
- SOC alert triage
- Basic threat-hunting methodology
- Technical security documentation

## Key Learning Outcomes

This lab improved my understanding of how a SIEM platform collects and processes telemetry across monitored systems. I also practised moving from raw events to an organised investigation by using time ranges, event fields, filters, and supporting evidence.

The project helped connect the technical components of Elastic with a practical SOC workflow: visibility, triage, investigation, documentation, and escalation.

## Future Improvements

- Add additional endpoint integrations.
- Create dashboards for authentication and process activity.
- Develop detection rules for suspicious behaviour.
- Add an incident-investigation case study, linking to my [log-analysis-with-SIEM](https://github.com/Fadimsa/log-analysis-with-SIEM) and [Threat-Hunting-IOC-Analysis](https://github.com/Fadimsa/Threat-Hunting-IOC-Analysis-Splunk-VirusTotal-) repos as examples of investigations run on a stack like this one.
- Compare Elastic investigation workflows with Splunk.
- Document alert severity and escalation criteria.

## Author

**Fadi Msallam**

Aspiring SOC Analyst focused on security monitoring, SIEM investigation, threat hunting, malware analysis, and cloud security.

- GitHub: [Fadimsa](https://github.com/Fadimsa)
- Location: Ireland

## Disclaimer

This project was completed in an authorised training environment for educational purposes only. It does not contain unauthorised access instructions, credentials, or sensitive third-party information.
