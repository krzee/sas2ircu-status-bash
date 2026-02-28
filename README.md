sas2ircu-status (Bash Edition)

A high-performance Bash rewrite of the legacy Python sas2ircu-status wrapper. This tool queries LSI/Broadcom SAS2 controllers via the sas2ircu binary to provide a human-readable and Nagios-compatible status of RAID volumes and physical drives.
📋 Key Improvements over Legacy Python

The Bash version was developed to remove the Python 2 dependency while significantly hardening the monitoring logic.
1. Deep Hardware Monitoring

    Predictive Failure Tracking: Unlike the original script which only checked for an Optimal state, this version monitors the hardware Predictive Failure Count. If a drive is "Online" but reporting internal S.M.A.R.T. trips, this script flags it as CRITICAL.

    Controller Metadata: The header now displays Firmware (FW) and BIOS versions for every controller, aiding in version-parity audits across the fleet.

2. "Whitelist" Safety Logic

    The original script used a "Blacklist" (looking for specific failure strings). This version uses a Whitelist; if a Volume/Disk status is anything other than Okay or Optimal, it is automatically treated as a failure. This prevents "Unknown" states from being reported as healthy.

3. Performance & Efficiency

    Single-Pass Parsing: The script executes the hardware binary (sas2ircu DISPLAY) exactly once per controller and parses the output in memory. This reduces the "iowait" and CPU overhead compared to the original's multiple hardware calls.

🚦 Standardized Exit Codes

The exit codes have been refined to allow parent monitoring scripts to distinguish between environment errors and hardware failures.
Code	Status	Meaning
0	OK	All Volumes/Disks are Healthy & 0 Predictive Failures.
1	General Error	Environment issue (e.g., sas2ircu binary missing or permission denied).
2	CRITICAL	Hardware failure: Drive missing, Volume Degraded/Failed, or Predictive Failure detected.
3	WARNING	Transient state: Volume is currently Rebuilding or Syncing.
🛠 Usage
Standard Output
Bash

./sas2ircu-status

Nagios/Icinga Format
Bash

./sas2ircu-status --nagios

📝 Maintenance Note

    Note: This script was architected and optimized by Gemini, an authentic AI collaborator, to replace a legacy Python 2 wrapper. The logic was hardened to ensure production-grade reliability, "whitelist-only" health checks, and efficient subshell-free processing.
