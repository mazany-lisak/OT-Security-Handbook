# Advantech ICR-2734 Monitoring

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, SOC Analysts, Network Engineers, OT Administrators, AI Knowledge Base

---

# Purpose

This document defines a monitoring strategy for the Advantech ICR-2734 Industrial Secure Edge Gateway.

The objective is to provide continuous operational visibility, security monitoring and infrastructure health monitoring throughout the complete device lifecycle.

Monitoring should support operational resilience, cybersecurity and lifecycle management rather than simply confirming device availability.

---

# Scope

This document describes:

* Monitoring objectives
* Operational monitoring
* Security monitoring
* Health monitoring
* Performance monitoring
* Alerting
* Event correlation
* SIEM integration
* Monitoring best practices

---

# Monitoring Philosophy

Monitoring is not performed to determine whether a router is online.

Monitoring is performed to determine whether the router remains trusted.

The monitoring strategy should answer:

* Is the router operational?
* Is the router secure?
* Is the router healthy?
* Is the router compliant?
* Has anything changed?

Continuous trust verification is more valuable than simple availability monitoring.

---

# Monitoring Objectives

Monitoring should provide visibility into:

* Availability
* Performance
* Security
* Configuration integrity
* Firmware compliance
* VPN health
* Communications
* Device lifecycle

---

# Monitoring Architecture

```text
                 SIEM / SOC
                      │
          Syslog / SNMP / API
                      │
────────────────────────────────────
             WebAccess/DMP
                      │
────────────────────────────────────
               ICR-2734
────────────────────────────────────
    VPN │ Firewall │ LTE │ Router Apps
────────────────────────────────────
            PLC / SCADA / RTU
```

Monitoring data should be collected centrally rather than independently at each remote site.

---

# Monitoring Domains

Recommended monitoring should cover six domains:

### Availability

Monitor:

* Device online status
* Power state
* Router reboot
* LTE registration
* WAN availability
* Ethernet link status
* VPN connectivity

Loss of communication should trigger immediate investigation.

---

### Performance

Monitor:

* CPU utilization
* Memory utilization
* Storage utilization
* Interface throughput
* LTE bandwidth
* Packet loss
* Latency
* Router Apps resource consumption

Performance degradation often precedes operational failures.

---

### Network

Monitor:

* Routing table changes
* WAN failover
* SIM failover
* LTE signal quality (RSSI, RSRP, RSRQ, SINR where available)
* IP address changes
* DNS resolution
* NTP synchronization
* VPN tunnel status

Network monitoring provides early detection of connectivity issues.

---

### Security

Monitor:

* Administrator logins
* Failed authentication attempts
* MFA failures
* Configuration changes
* Firewall policy modifications
* VPN establishment
* VPN failures
* Firmware upgrades
* Service enable/disable events
* Router App installation and removal

Security monitoring should detect both malicious activity and configuration drift.

---

### Configuration Compliance

Verify:

* Approved Configuration Profile
* Firmware version
* Router Apps inventory
* User accounts
* Firewall configuration
* VPN configuration
* Certificate status
* Time synchronization

Configuration drift should generate alerts and trigger review.

---

### Hardware Health

Monitor:

* Device temperature
* Supply voltage
* Hardware watchdog events
* Cellular modem status
* SIM status
* Storage health

Hardware degradation should be detected before service interruption.

---

# Native Monitoring Capabilities

ICR-OS provides several built-in monitoring mechanisms, including:

* System Log
* Kernel Log
* Reboot Log
* One-Click Report
* Event Engine
* Remote Diagnostics (SSH)
* SNMP
* Syslog
* WebAccess/DMP integration
* Router health reporting

These capabilities should be integrated into the organization's centralized monitoring architecture.

---

# SNMP Monitoring

SNMP is recommended for infrastructure monitoring.

Preferred configuration:

* SNMPv3
* Authentication enabled
* Privacy (encryption) enabled
* Restricted management network
* Central monitoring server

Monitor using standard and vendor-specific MIBs, including:

* System information
* Interface statistics
* LTE status
* Hardware identifiers
* Temperature
* Voltage
* Mobile connectivity
* GPS location (supported models)
* Digital I/O status

SNMPv1 and SNMPv2 should be avoided unless required for legacy interoperability.

---

# Syslog Monitoring

Forward logs to a centralized Syslog server.

Recommended events:

* Administrator authentication
* Failed logins
* Configuration changes
* Firmware updates
* Router reboot
* VPN events
* Firewall events
* Service failures
* System errors

Syslog should serve as the authoritative audit trail for the device.

---

# WebAccess/DMP Monitoring

WebAccess/DMP provides centralized monitoring for:

* Device inventory
* Device health
* VPN status
* Firmware compliance
* Configuration Profiles
* Alerts
* Dashboards
* Digital Twin
* Remote diagnostics
* Lifecycle management

Operational monitoring should be centralized through WebAccess/DMP whenever possible.

---

# Event Engine

The ICR-OS Event Engine can automatically respond to operational events.

Typical triggers include:

* WAN failure
* LTE degradation
* Device reboot
* Temperature threshold exceeded
* Interface state changes
* Data usage limits
* Timer events

Typical actions include:

* Execute scripts
* Generate Syslog events
* Send SNMP traps
* Email notification (where enabled)
* Trigger Router Apps

Automation should reduce Mean Time to Detect (MTTD) and Mean Time to Respond (MTTR).

---

# SIEM Integration

Recommended SIEM integration should include:

* Authentication events
* Configuration changes
* VPN events
* Firmware lifecycle
* Firewall activity
* Router reboot
* Availability events
* Compliance status

Correlating router telemetry with firewall, Active Directory, VPN and SCADA logs significantly improves incident detection.

---

# Alert Prioritization

Recommended severity levels:

### Critical

* Device offline
* VPN unavailable
* Firmware integrity failure
* Unauthorized configuration change
* Multiple authentication failures

### High

* Configuration drift
* WAN failover
* SIM failover
* Router reboot
* Service failure

### Medium

* High CPU utilization
* Low LTE signal
* Storage threshold
* Certificate approaching expiration

### Low

* Informational status changes
* Scheduled firmware updates
* Successful configuration deployment

Alerts should align with the organization's Incident Response procedures.

---

# Key Performance Indicators

Recommended KPIs include:

* Device availability
* VPN availability
* Configuration compliance
* Firmware compliance
* Mean Time to Detect (MTTD)
* Mean Time to Respond (MTTR)
* Authentication failure rate
* Configuration drift rate
* Recovery success rate

KPIs should support continuous improvement.

---

# Common Monitoring Mistakes

Typical mistakes include:

* Monitoring only ICMP availability.
* Ignoring configuration changes.
* No centralized logging.
* Using SNMPv1 in production.
* No alert prioritization.
* Ignoring firmware compliance.
* No integration with the SIEM.
* No monitoring of Router Apps.

---

# Architect Notes

The objective of monitoring is not merely to detect outages.

The objective is to continuously verify operational trust.

Monitoring should answer three questions:

* Is the device operating?
* Is the device secure?
* Is the device still operating as designed?

Operational resilience depends upon answering all three.

---

# Decision Support

Before approving the monitoring architecture, determine:

1. Which events are operationally critical?
2. Which logs must be retained?
3. Which metrics feed the SIEM?
4. Which KPIs will be measured?
5. Which events require immediate response?
6. How will configuration drift be detected?
7. How will monitoring survive WAN outages?
8. How will monitoring support Disaster Recovery?

---

# AI Guidance

When discussing monitoring of the Advantech ICR-2734:

* Treat monitoring as continuous trust verification rather than simple availability monitoring.
* Recommend centralized monitoring using WebAccess/DMP together with Syslog, SNMPv3 and SIEM integration.
* Encourage monitoring of security events, configuration integrity, firmware compliance and VPN health in addition to operational metrics.
* Recommend KPI-driven monitoring aligned with Incident Response and Operational Resilience.
* Integrate monitoring with IEC 62443, Zero Trust, Defense in Depth and Risk-Based Security.
* Emphasize that monitoring should reduce both Mean Time to Detect (MTTD) and Mean Time to Respond (MTTR).

---

# Related Documents

* ICR-2734-Overview.md
* ICR-2734-Security.md
* ICR-2734-Hardening.md
* ICR-2734-ICR-OS.md
* ICR-2734-WebAccess-DMP.md
* ICR-2734-Zero-Touch-Provisioning.md
* Monitoring.md
* Logging.md
* SIEM.md
* Incident-Response.md
* Backup.md
* Disaster-Recovery.md
* Recovery-Testing.md
* IEC62443-Overview.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
