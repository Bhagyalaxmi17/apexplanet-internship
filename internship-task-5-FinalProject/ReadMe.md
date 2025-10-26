Project Title: Mini-SIEM using ELK Stack on Kali Linux

Description

This project implements a Mini Security Information and Event Management (SIEM) system using the ELK Stack (Elasticsearch, Logstash, and Kibana) with Filebeat for log forwarding.
The goal of this project is to collect, monitor, and visualize system and SSH authentication logs in real time to detect suspicious login attempts and improve system security visibility.

Project Overview

Filebeat collects logs from /var/log/auth.log and /var/log/syslog.

Logstash (optional) processes and filters incoming log data.

Elasticsearch stores and indexes the logs for fast search and analysis.

Kibana provides powerful dashboards and visualizations for monitoring failed SSH logins, top attacker IPs, and user login patterns.

The setup mimics a real-world SIEM environment, demonstrating log collection, threat detection, and incident analysis on a single host system.

 Features

Real-time log collection and indexing

Visualization of failed SSH attempts

Identification of top IP sources

Interactive dashboards in Kibana

Lightweight, easily deployable setup for students or researchers

 Technologies Used

Kali Linux

Elasticsearch 8.19.6

Kibana 8.19.6

Filebeat

(Optional) Logstash

SSH / Syslog Monitoring

Use Case

This project demonstrates the detection of brute-force SSH attacks by visualizing failed login attempts in Kibana.
It serves as a foundational step toward enterprise-level SIEM setups used in cybersecurity monitoring and blue team operations.

Project Outputs

Filebeat service running and sending logs successfully

Real-time visualization of authentication logs in Kibana Discover

Dashboards showing failed SSH login counts and attacker IP trends
