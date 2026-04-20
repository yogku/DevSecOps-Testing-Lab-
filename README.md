
#  Secure DevSecOps Testing Laboratory

An enterprise-grade, three-tier network isolation lab designed for **secure malware analysis, software testing, and vulnerability assessment**.

This repository provides the architecture, configurations, and validation steps to deploy a **"digital clean room"** using Cisco Packet Tracer.

## 1. Overview

The rapid evolution of sophisticated cyber threats necessitates a shift in how organizations approach software development and security testing.

This lab creates an **immutable security boundary** where potentially harmful code can be executed safely—without risk to production environments.

### Key Features

- **Three-Tier Segmentation**  
  Logical isolation of Production, Testing, and DMZ zones

- **Zero-Trust ACLs**  
  Granular traffic filtering using the Principle of Least Privilege

- **Audit Logging**  
  Real-time logging of unauthorized access attempts

- **Scalable Blueprint**  
  Compatible with physical hardware and cloud sandbox environments


## 2.  Network Architecture

The environment is divided into three isolated segments to prevent cross-contamination:

| Network Zone | Subnet         | CIDR | Default Gateway | Purpose                              |
|-------------|----------------|------|------------------|--------------------------------------|
| Production  | 192.168.10.0   | /24  | 192.168.10.1     | Critical corporate assets            |
| Testing     | 192.168.20.0   | /24  | 192.168.20.1     | High-risk sandbox / malware analysis |
| DMZ         | 192.168.30.0   | /24  | 192.168.30.1     | Shared update & logging repository   |

### Logic Diagram

```

[Testing (192.168.20.x)] <--> [R1-Firewall] <--> [Production (192.168.10.x)]
^
|
[DMZ (192.168.30.x)]

````


## 3. Getting Started

### Prerequisites

- **Software:** Cisco Packet Tracer (v8.2.x or higher)
- **Knowledge:** Basic familiarity with Cisco IOS CLI



### Installation & Setup

#### 1. Clone the Repository

```bash
git clone git@github.com:yogku/DevSecOps-Testing-Lab-.git
````

#### 2. Open the Lab

* Launch Cisco Packet Tracer
* Open the `.pkt` file included in this repository

---

### 3. Apply Firewall Configuration

#### Configure ACL 110

```bash
R1-Firewall(config)# access-list 110 deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255 log
R1-Firewall(config)# access-list 110 permit ip any any
```

#### Apply ACL to Testing Interface (Ingress)

```bash
R1-Firewall(config)# interface gigabitethernet 0/0
R1-Firewall(config-if)# ip access-group 110 in
```


### 4. Configure Routing (R2-Core)

```bash
ip route 192.168.10.0 255.255.255.0 10.0.0.1
ip route 192.168.20.0 255.255.255.0 10.0.0.1
ip route 192.168.30.0 255.255.255.0 10.0.0.1
```


## 4. Usage & Validation

Perform the following tests to verify functionality:

| Test Type      | Source        | Destination   | Expected Result           |
| -------------- | ------------- | ------------- | ------------------------- |
| Connectivity   | Testing PC    | DMZ Server    | Successful Ping           |
| Connectivity   | Production PC | DMZ Server    | Successful Ping           |
| Security Check | Testing PC    | Production PC | Request Timeout (Blocked) |
| Audit Check    | R1 Console    | N/A           | Log entry generated       |



### 📊 Monitoring Logs

View real-time firewall logs:

```bash
%SEC-6-IPACCESSLOGP: list 110 denied ip 192.168.20.2 -> 192.168.10.2 (1 packet)
```


## 5. Troubleshooting

### Common Issues

* **Routing Asymmetry**

  * Ensure `permit ip any any` exists at the end of ACL 110

* **ACL Direction**

  * Apply ACL **inbound** on Testing interface (Gig0/0)

* **Interface State**

  * Ensure all interfaces are:

    ```bash
    no shutdown
    ```

## 6. Contributing

Contributions are welcome.

You can enhance this lab by adding:

* OSPF support
* VLAN-based segmentation
* IDS/IPS simulations

Submit a Pull Request to contribute.



## 📄 License & Maintenance

Maintained as an **open-source security research project**.

