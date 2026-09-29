# Performance Analysis: Type-1 (Proxmox VE) vs Type-2 (VMware Workstation) Hypervisors

![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04%20LTS-E95420?logo=ubuntu&logoColor=white)
![Proxmox](https://img.shields.io/badge/Hypervisor-Proxmox%20VE%20(Type--1)-E57000?logo=proxmox&logoColor=white)
![VMware](https://img.shields.io/badge/Hypervisor-VMware%20Workstation%20(Type--2)-607078?logo=vmware&logoColor=white)
![Benchmark](https://img.shields.io/badge/Benchmark-Sysbench%201.0.20-blue?logo=gnu-bash&logoColor=white)

---

## 📑 Table of Contents

- [Executive Summary](#-executive-summary)
- [Architectural Comparison: Type-1 vs Type-2 Hypervisors](#-architectural-comparison-type-1-vs-type-2-hypervisors)
- [Test Environment & System Specifications](#-test-environment--system-specifications)
- [Part A: Implementation on Type-1 Hypervisor (Proxmox VE)](#-part-a-implementation-on-type-1-hypervisor-proxmox-ve)
  - [1. Accessing Proxmox VE Web GUI](#1-accessing-proxmox-ve-web-gui)
  - [2. SSL Certificate Handling & Authentication](#2-ssl-certificate-handling--authentication)
  - [3. Proxmox Hierarchy & Navigation](#3-proxmox-hierarchy--navigation)
  - [4. Creating the Virtual Machine](#4-creating-the-virtual-machine)
  - [5. Starting VM & Opening Console](#5-starting-vm--opening-console)
  - [6. Ubuntu OS Installation](#6-ubuntu-os-installation)
  - [7. System Verification & Resource Inspection](#7-system-verification--resource-inspection)
  - [8. Sysbench Installation & CPU Benchmark Execution](#8-sysbench-installation--cpu-benchmark-execution)
  - [9. Proxmox Resource Monitoring & Observation](#9-proxmox-resource-monitoring--observation)
  - [10. Graceful VM Shutdown](#10-graceful-vm-shutdown)
- [Part B: Implementation on Type-2 Hypervisor (VMware Workstation)](#-part-b-implementation-on-type-2-hypervisor-vmware-workstation)
  - [1. Launching VMware Workstation Wizard](#1-launching-vmware-workstation-wizard)
  - [2. Installation Media & Guest OS Selection](#2-installation-media--guest-os-selection)
  - [3. VM Naming & Disk Configuration](#3-vm-naming--disk-configuration)
  - [4. Hardware Customization](#4-hardware-customization)
  - [5. Ubuntu Operating System Installation](#5-ubuntu-operating-system-installation)
  - [6. Post-Installation System Verification](#6-post-installation-system-verification)
  - [7. Sysbench Installation](#7-sysbench-installation)
  - [8. Executing the Sysbench CPU Benchmark](#8-executing-the-sysbench-cpu-benchmark)
  - [9. Resource Monitoring & Graceful Shutdown](#9-resource-monitoring--graceful-shutdown)
- [Empirical Results & Observation Tables](#-empirical-results--observation-tables)
  - [Type-2 Hypervisor (VMware Workstation) Results](#type-2-hypervisor-vmware-workstation-results)
  - [Comparative Analysis Table](#comparative-analysis-table)
- [Technical Discussion: Virtualization Overhead](#-technical-discussion-virtualization-overhead)
- [Summary Workflows](#-summary-workflows)
- [Conclusion](#-conclusion)

---

## 📌 Executive Summary

This laboratory experiment evaluates virtualization overhead and compute efficiency between:

1. **Type-1 Bare-Metal Hypervisor:** **Proxmox Virtual Environment (PVE)**, running directly on physical hardware without a host operating system.
2. **Type-2 Hosted Hypervisor:** **VMware Workstation**, running as an application on top of an existing host operating system.

Identical Ubuntu guest virtual machines (2 vCPUs, 2 GB RAM, 20 GB Virtual Disk) were provisioned on both hypervisors. CPU performance was empirically analyzed under an identical CPU-intensive workload using **Sysbench** (prime number calculation up to 20,000) to isolate and measure virtualization layer latency, scheduling penalties, and computation throughput.

---

## 🏛 Architectural Comparison: Type-1 vs Type-2 Hypervisors

Virtualization architectures differ fundamentally in where the hypervisor sits in relation to the physical hardware:

```mermaid
flowchart TD
    subgraph Type1["Type-1 Bare-Metal Hypervisor (Proxmox VE)"]
        direction TB
        HW1["Physical Hardware (CPU, RAM, Disk, NIC)"]
        HYP1["Proxmox VE Kernel / Hypervisor (KVM/QEMU)"]
        VM1["Guest VM: Ubuntu 22.04 LTS (2 vCPU, 2GB RAM)"]
        HW1 --> HYP1
        HYP1 --> VM1
    end

    subgraph Type2["Type-2 Hosted Hypervisor (VMware Workstation)"]
        direction TB
        HW2["Physical Hardware (CPU, RAM, Disk, NIC)"]
        HOST["Host Operating System (Windows / Linux)"]
        HYP2["VMware Workstation (VMM / Hypervisor App)"]
        VM2["Guest VM: Ubuntu 22.04 LTS (2 vCPU, 2GB RAM)"]
        HW2 --> HOST
        HOST --> HYP2
        HYP2 --> VM2
    end
