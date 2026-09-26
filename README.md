# Cowrie SSH Honeypot on GCP

A medium-interaction SSH/Telnet honeypot deployed on Google Cloud Platform to capture and analyze real-world attack traffic against internet-facing SSH. Attacker sessions were logged, parsed, and mapped to the MITRE ATT&CK framework.

> Status: decommissioned. The instance has been torn down. This repo documents the setup, the data collected, and what I took away from it.

## Overview

I stood up a Cowrie honeypot on a GCP VM with SSH exposed to the public internet to observe how automated bots and attackers behave once they think they have a shell. Cowrie emulates a Linux system, accepts login attempts, and records every command, credential, and file the attacker interacts with.

Over the collection period the honeypot logged **600+ events**, which I mapped to MITRE ATT&CK techniques to understand the tactics behind the noise.

## Key finding: Panchan malware sample

The standout capture was a live **Panchan** sample. Panchan is a Golang-based peer-to-peer botnet that spreads over SSH, harvests credentials to move laterally, and drops cryptomining payloads. Catching it in the wild confirmed the honeypot was doing exactly what it was built for: pulling in real, self-propagating malware rather than just credential-stuffing noise.

## Stack

- **Honeypot:** Cowrie (SSH/Telnet, medium-interaction)
- **Host:** Google Cloud Platform VM
- **Analysis:** MITRE ATT&CK technique mapping of captured events
- **Artifacts:** session logs, captured payloads, credential attempts

## What the data showed

- High-volume automated brute forcing against common credential pairs (root/admin defaults)
- Post-access command sequences: system recon, downloading second-stage payloads, attempts to establish persistence
- At least one confirmed malware sample (Panchan) delivered through a live session

## What I took away

- How SSH attacks actually unfold end to end, from initial brute force to payload delivery, using captured sessions rather than theory
- How to translate raw honeypot logs into MITRE ATT&CK techniques so the activity maps to a shared vocabulary
- The operational reality of running an internet-facing service: it gets attacked within minutes, continuously
- Why the value of monitoring depends entirely on the quality of the data feeding it

## Repository contents

- `logs/`: sanitized Cowrie session logs
- `analysis/`: MITRE ATT&CK mapping and notes
- `samples/`: hashes and metadata for captured payloads (samples handled per responsible-disclosure practice)
- `setup/`: deployment notes and configuration

## Notes

Credentials, IPs, and any personal data in the logs have been sanitized. Malware samples are referenced by hash and metadata only; live binaries are not distributed here.
