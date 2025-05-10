# 📡 Integration of Outdoor Base Station and Amarisoft 5G Core

**Author:** Tanu Agarwal  
**Supervisors:** Prof. Dr. Armin Lehmann, Prof. Dr. Ulrich Trick  
**Project Start:** August 14, 2023  
**Submission:** February 19, 2024

---

## 📘 Abstract

This project presents the integration of an outdoor 5G base station with the Amarisoft 5G Core (SA) network. The objective is to design and validate a functional testbed where Amarisoft's software suite running on a compact PC (AW2S) serves as the 5G core, interfacing with a gNodeB (gNB) connected to a remote radio head (RRH) via fiber. The work involved theoretical analysis, hardware/software configuration, implementation, and testing (VoNR, signal strength, and throughput).

---

## 🎯 Motivation and Objectives

Driven by the evolution to software-defined mobile networks, this project implements a real-world use case of **Network Functions Virtualization (NFV)** and **Cloud-RAN** to build a modular, scalable 5G testbed.  

### Goals:

The primary objective of this project work is to integrate an outdoor base station and the Amarisoft-5G Core to build a fully working 5G standalone (SA) network. For this project, the completion of the deployment of the emulated 5G Standalone environment involves four primary stages. These stages are outlined as follows:
- Thoroughly examining the PCI Express Board through both general and in-built Built-In-Self-Test (BIST) application testing.
- Configuring and deploying all the software components of Amarisoft for the 5G Core Network ("5GC") and 5G Base Station ("gNB") in standalone mode within the AW2S PC. Additionally, configuring Radio Management Unit (RMU) settings and Swallow driver files in the AW2S PC is part of this stage.
- Configuring and testing the test User Equipment (UEs) along with the provided test USIM cards from Amarisoft.
- Furthermore, conducting call testing on Voice over New Radio (VoNR) and also measuring RF signal strength is integral to this stage.

---

## 🧠 Technologies Used

- **5G NR (Release 17)**
- **Network Functions Virtualization (NFV)**
- **Software Defined Networking (SDN)**
- **Cloud-RAN (C-RAN)**
- **Multi-access Edge Computing (MEC)**
- **Amarisoft LTE/NR Stack**
- **CPRI and PCIe signaling**

---

## 💻 Hardware & Software

### 🔌 Hardware Components
| Component | Description |
|----------|-------------|
| **AW2S Compact PC (Cincoze DS-1101)** | Hosts Amarisoft core and gNB |
| **Swallow PCIe Board** | RF driver card interfacing with RRH |
| **AW2S Blackhawk RRH** | Remote radio unit for NR signal transmission |
| **AW3014 Sector Antenna** | Antenna for NR downlink/uplink |
| **Test UE** | Commercial 5G smartphones with test SIMs |
| **R&S Spectrum Analyzer** | For RF signal validation |

### 🧠 Software Stack
- **Amarisoft Core Suite** (5G Core + gNB)
- **Ubuntu 20.04 LTS**
- **iPerf** – Throughput testing
- **Wireshark** – Packet sniffing
- **Amarisoft Web GUI** – Management console

---

## 🧭 System Architecture
The overall setup of the 5G Standalone test network as shown in figure mainly includes the following components:
-	**AW2S Compact PC:** It is required for hosting the Amarisoft 5GC and gNB Software suite. Since the PC is without a Graphical User Interface (GUI), a monitor is required for the display of its ter-minal, and a keyboard is required for giving inputs to the PC.
-	**Client PC:**  It is required to access the Amarisoft Software Stack Web GUI and other components of the 5G network remotely.
-	**Switch:** It acts as an interconnection between the client PC and the AW2S PC to the university network via ethernet LAN cables.
-	**RRH and Antenna:** It is required for the operation of transmitting or receiving RF power. The RRH is connected to the AW2S PC via a high-speed Fiber connection.
-	**User Equipment:** It is used to examine 5G/NR services by connecting it under the coverage area of the emulated 5G network. The test USIM card is provided by Amarisoft to be inserted into these 5G-enabled mobile phone devices.

![image](https://github.com/user-attachments/assets/d6484d27-4176-4c7a-9d20-9640d2ee0db0)

---
## 🖥️ Overview of Amarisoft off-the-shelf (OTS) 5G NR Software Suite
The Amarisoft off-the-shelf (OTS) 5G NR software suite offers a dependable and streamlined approach for deploying a fully operational 5G NR system. It primarily comprises two core components: LTEMME and LTEENB, as illustrated in the figure. Although these modules were originally associated with LTE 4G, Amarisoft initially developed a software-defined LTE solution that was later enhanced to support 5G NR capabilities. Consequently, LTEMME and LTEENB now refer more broadly to the 4G/5G core network and radio access network (RAN), respectively.

![image](https://github.com/user-attachments/assets/910ef0b5-ecfb-41bf-8596-7c050c347f0c)

The LTEMME component serves as Amarisoft's software-based 5G Core (5GC) solution, supporting flexible deployment of LTE and/or NR networks. Its configuration files are located in /root/mme/config and include:
- **mme.cfg** – the main configuration file for defining core network functions,
- **mme-ims.aw2s.cfg** – linked symbolically to mme.cfg for IMS support,
- **ue.db-ims.cfg** – a database file for test UE profiles,
- **mme-ifup** – a script for setting up the virtual network interface.

These files are invoked by the mme.cfg file during execution, ensuring proper core network setup.

The second module, LTEENB, acts as the software-defined base station (gNB) and runs entirely on the AW2S PC. It interfaces with the 5G Core using the standard NG interface. The AW2S PC generates the baseband signal, which is transmitted via the Swallow board (PCIe) acting as the radio front-end. The board performs digital-to-analog conversion during transmission and the inverse during reception.

LTEENB configuration files are also located in /root/mme/config and include:
- **enb.cfg** – the primary configuration file for the 5G NR gNB, written in JSON format,
- **gnb-sa-aw2s.cfg** – additional standalone mode settings,
- **drb_nr.cfg** – configuration for data radio bearers.

---
## ⚙️ Setup & Configuration

### 🖥️ 1. Amarisoft Core Installation

```bash
# Assuming Amarisoft software archive is extracted
cd amarisoft-ltemme
chmod +x ltemme
./ltemme -c config/mme.cfg
```

- Ensure `mme.cfg' are configured for standalone 5GC.

### 🧩 2. Swallow Board & RRH Setup

- Insert the Swallow board into the PCIe slot.
- Connect fiber to the RRH using SFP interface.
- Confirm hardware via BIST:

```bash
cd /opt/swallow-tools
./swallow_bist -v
```

### 📡 3. gNB + RMU Configuration

- Edit `enb.cfg` for PLMN, TAC, and beamforming parameters.
- Load driver:

```bash
insmod swallow_driver.ko
systemctl start swallow-gNB.service
```

- Monitor logs via:

```bash
tail -f /var/log/amarisoft/gNB.log
```

---

## 🔬 Testing & Validation

### 📞 VoNR Call Test

- Register test UEs with valid IMSI/APN.
- Initiate VoNR call.
- Validate NAS logs on `ltemme.log`.
- Monitor QoS flow assignments in GUI.

### 📶 Signal Strength Testing

- Move with UE across campus.

### 🌐 Throughput Testing with iPerf

```bash
# On Amarisoft PC
iperf -s

# On UE (connected to 5G)
iperf -c <client IP Address> -u -b 600M -i 1 -t 100
```

---

## ⚠️ Challenges and Resolutions

| Challenge | Resolution |
|----------|------------|
| Swallow board not detected | Rescanned PCIe bus, updated kernel modules |
| RRH sync issues | Adjusted CPRI config, ensured fiber integrity |
| UE not registering | Debugged NGAP logs, corrected user profile |
| VoNR failure | Configured IMS and QoS profiles in Amarisoft |

---

## 📌 Results Summary

| Test | Status | Notes |
|------|--------|-------|
| UE Attach | ✅ | Successful registration |
| PDU Session | ✅ | IPv4 and dual-stack |
| VoNR Call | ✅ | Call quality validated |
| iPerf (DL) | ✅ | ~250 Mbps |

---

## 🔭 Future Work

- Automate deployment using Docker or Ansible
- Expand testbed to include slicing and MEC
- Integrate real-world KPIs via Prometheus + Grafana
- Conduct high-mobility testing scenarios

---

## 📚 References

- Amarisoft Tech Academy (2024)
- 3GPP TS 23.501 v15.10.0
- Trick, U. (2021). *Future Networks & NFV*
- Holma, H., et al. (2020). *5G Technology: Principles and Practices*

---


