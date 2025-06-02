L4S (Low Latency, Low Loss and Scalable Throughput) is an IETF-proposed architecture designed to provide low queuing latency, low congestion loss, and scalable throughput for Internet applications. It achieves this through changes in hosts and network nodes, such as new "Scalable" Congestion Control (CC) algorithms (e.g., TCP Prague) and a dual-queue coupled mechanism in routers. The primary goal is to ensure low latency and scalable throughput, even under congestion.

This repository contains artifacts and results from an experiment evaluating the L4S architecture's vulnerability to ECN field manipulation by non-compliant flows. The study focuses on the "non-responsive ECN attack," where a malicious flow (using classic congestion control like Reno, BBR, or Cubic) marks its packets with the ECT(1) codepoint to exploit the low-latency queue but, unlike legitimate L4S flows (e.g., TCP Prague), does not react to congestion signals (CE marks).
The L4S topology used for testing is illustrated below:


<div align="center">
  <img src="/Image/topology.svg" alt="Experiment Topology" width="300">
</div>

To run this experiment we use:

L4STEAM MODULE (Kernel tree containing patches for TCP Prague and the dualpi2 qdisc)

Vagrant (version 2.3.0)

VirtualBox (version 6.1)

Iperf (version 2.0.13)

