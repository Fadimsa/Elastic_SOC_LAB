# Elastic_SOC_LAB

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
- TryHackMe training environment

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
- Add an incident-investigation case study.
- Compare Elastic investigation workflows with Splunk.
- Document alert severity and escalation criteria.

## Author

**Fadi Msallam**

Aspiring SOC Analyst focused on security monitoring, SIEM investigation, threat hunting, malware analysis, and cloud security.

- GitHub: [Fadimsa](https://github.com/Fadimsa)
- Location: Ireland

## Disclaimer

This project was completed in an authorised training environment for educational purposes only. It does not contain unauthorised access instructions, credentials, or sensitive third-party information.
