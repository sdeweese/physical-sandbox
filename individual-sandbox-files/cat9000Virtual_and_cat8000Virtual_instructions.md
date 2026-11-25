## 📑 Table of Contents
- [Topology and Access Matrix](#topology-and-access-matrix)
  - [Connecting via DevNet AnyConnect VPN (If Required)](#connecting-via-devnet-anyconnect-vpn-if-required)
- [Step 0: Pre-Flight Verification and Programmability Prerequisites](#step-0-pre-flight-verification-and-programmability-prerequisites)
  - [A. Log in to Device CLI and Check Version](#a-log-in-to-device-cli-and-check-version)
  - [B. Verify NETCONF and RESTCONF Configuration](#b-verify-netconf-and-restconf-configuration)
  - [C. Enabling NETCONF and RESTCONF (Prerequisites / Fallback Config)](#c-enabling-netconf-and-restconf-prerequisites--fallback-config)
- [Step-by-Step Getting Started (In-Sandbox Execution)](#step-by-step-getting-started-in-sandbox-execution)
  - [Step 1: Access Tools to Work with Your Sandbox](#step-1-access-tools-to-work-with-your-sandbox)
  - [Step 2: Query RESTCONF with Cisco Native YANG Models and OpenAPI Swagger](#step-2-query-restconf-with-cisco-native-yang-models-and-openapi-swagger)
    - [Part A: Quick cURL Smoke Test](#part-a-quick-curl-smoke-test)
    - [Part B: Programmatic Exploration with Python Requests & Swagger Reference](#part-b-programmatic-exploration-with-python-requests--swagger-reference)
  - [Step 3: Direct NETCONF Programmability with Python ncclient](#step-3-direct-netconf-programmability-with-python-ncclient)
  - [Step 4: Declarative Automation with Terraform (NETCONF)](#step-4-declarative-automation-with-terraform-netconf)
  - [Step 5: Configuration Management with Ansible](#step-5-configuration-management-with-ansible)
- [Recommended DevNet Learning Lab](#recommended-devnet-learning-lab)

---

# Topology and Access Matrix

Each reservable sandbox provides a dedicated pod consisting of an in-sandbox Linux Developer Workstation and your selected virtual Cisco IOS XE device:

```text
┌────────────────────────────────────────────────────────────────────────┐
│                   In-Sandbox Developer Workstation                     │
│  • Web Terminal & Web VS Code: http://10.10.20.181:8080                │
│  • Jump Host: 10.10.20.181 (developer / C1sco12345)                    │
│  • Pre-installed: Python, ncclient, Requests, Ansible, Terraform       │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Management Subnet (198.18.1.0/24)
                                    ▼
       ┌─────────────────────────────────────────────────────────┐
       │   Cisco Catalyst Virtual Device (C9000V or C8000V)      │
       │   Target Management IP: 10.10.20.181                    │
       │   Credentials: developer / C1sco12345                   │
       │   Enabled Services: SSH (22), NETCONF (830), HTTPS (443)│
       └─────────────────────────────────────────────────────────┘
```

> 📌 **Important — Check Your Active Reservation:**  
> The IP addresses (`10.10.20.181`, `10.10.20.181`) and credentials (`developer` / `C1sco12345`) listed below reflect the standard internal pod defaults. Always verify the **Credentials & Details** panel on your active DevNet Sandbox reservation page for your specific assigned VPN endpoints, dynamic IP mappings, or password assignments.

| Resource / Tool | Access Method | Target Endpoint | Default Credentials |
| :--- | :--- | :--- | :--- |
| **Developer Workstation (Web UI)** | Browser (VS Code / Web Terminal) | `http://10.10.20.181:8080` | `developer` / `C1sco12345` |
| **Developer Workstation (SSH)** | Terminal / SSH | `10.10.20.181:22` | `developer` / `C1sco12345` |
| **Virtual Device (C9000V or C8000V)** | SSH / NETCONF / RESTCONF | `10.10.20.181` (Ports: `22`, `830`, `443`) | `developer` / `C1sco12345` |

---

### Connecting via DevNet AnyConnect VPN (If Required)

Depending on how you choose to interact with your sandbox, you can connect directly in your browser or from your local workstation:

1. **Option A: In-Browser Access (No VPN Required):**
   * Use the embedded Web Terminal or Web VS Code at `http://10.10.20.181:8080`.
   * All automation tools and connectivity are self-contained inside the sandbox.

2. **Option B: Local Machine / IDE Access (AnyConnect VPN Required):**
   * If you wish to run automation scripts, Terraform, or Ansible directly from your own laptop or IDE:
     1. Open **Cisco Secure Client** (formerly Cisco AnyConnect).
     2. Enter the **VPN Server Address** provided in the **VPN Access** panel of your active DevNet Sandbox reservation (e.g., `vpn.devnetsandbox.cisco.com` or your assigned gateway IP/port).
     3. Authenticate using the **VPN Username** and **VPN Password** listed under your active reservation credentials.
     4. Once the VPN connection establishes, verify network reachability from your local terminal:
        ```bash
        ping 10.10.20.181   # Developer Workstation
        ping 10.10.20.181    # Catalyst Virtual Device (C9000V or C8000V)
        ```

---

# Step 0: Pre-Flight Verification and Programmability Prerequisites

Before running automation scripts or API calls, start with familiar CLI commands to verify that your virtual device is online, running the expected software version, and has the required programmatic services enabled.
### A. Log in to Device CLI and Check Version
From the Developer Workstation terminal, SSH into your target device:

```bash
ssh developer@10.10.20.181
# Enter password: C1sco12345
```

Once logged in, verify the software release and platform:
```bash
show version | include Software|processor
# You should receive output similar to: Cisco IOS XE Software, Version 17.15.01
# cisco C9000V (or C8000V) (virtual) processor with 3816432K/3072K bytes of memory.
```

---

### B. Verify NETCONF and RESTCONF Configuration
Check the running configuration to confirm programmatic services are active:

```bash
show running-config | section netconf
# You should receive output: 
# netconf-yang

show running-config | section restconf|http
# You should receive output:
# ip http server
# ip http secure-server
# restconf

show netconf-yang status
# You should receive output: 
# netconf-yang: enabled
```

---

### C. Enabling NETCONF and RESTCONF (Prerequisites / Fallback Config)
If NETCONF or RESTCONF are not present (or if the configuration was wiped), apply the following prerequisite configuration directly at the CLI prompt:

```bash
configure terminal
!
! Enable AAA and local privilege level 15 user
aaa new-model
aaa authentication login default local
aaa authorization exec default local
username developer privilege 15 secret C1sco12345
!
! Enable HTTPS Server and RESTCONF API
ip http server
ip http secure-server
restconf
!
! Enable Model-Driven NETCONF over SSH (Port 830)
netconf-yang
!
end
write memory
```

Type `exit` to return to the Developer Workstation terminal.


---

# Step-by-Step Getting Started (In-Sandbox Execution)

---

### Step 1: Access Tools to Work with Your Sandbox

Note: none of the following steps are required. Rather, they are simply a way for you to get familiar with your virtual sandbox and some of the many tools available. Using your machine's terminal window, you can interface directly with your sandbox. For the tools below, you may need to review the linked documents to download or install the given tool to your device.

---

### Step 2: Query RESTCONF with Cisco Native YANG Models and OpenAPI Swagger

#### 💡 What is RESTCONF & Cisco OpenAPI?
[RESTCONF (RFC 8040)](https://datatracker.ietf.org/doc/html/rfc8040) is an HTTP-based protocol that provides a programmatic interface for accessing data defined in YANG models using standard JSON or XML payloads.

To explore all available RESTCONF endpoints, URI paths, and payload schemas for your device, refer to the **[Cisco IOS XE RESTCONF OpenAPI / Swagger Documentation](https://ciscodevnet.github.io/cisco-ios-xe-openapi-swagger/index.html)**.

---

#### Part A: Quick cURL Smoke Test
Before writing Python scripts, perform a quick cURL check directly in the terminal to verify the RESTCONF engine and credentials:

```bash
curl -k -s -u developer:C1sco12345 \
  -H "Accept: application/yang-data+json" \
  https://10.10.20.181/restconf/data/Cisco-IOS-XE-native:native/hostname
```

*(Note: `-k` bypasses self-signed sandbox certificates, and `-H "Accept: application/yang-data+json"` requests native YANG JSON).*

---

#### Part B: Programmatic Exploration with Python Requests & Swagger Reference
Once the cURL test confirms connectivity, use Python to programmatically query and pretty-print native YANG models:

1. Navigate to the RESTCONF workspace:
   ```bash
   mkdir -p ~/samples/restconf && cd ~/samples/restconf
   ```

2. Create the Python script `query_restconf.py`:
   ```bash
   cat <<'EOF' > query_restconf.py
   import requests
   import json
   import urllib3

   # Disable SSL certificate warnings for local self-signed sandbox certificates
   urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

   HOST = "10.10.20.181"
   USER = "developer"
   PASS = "C1sco12345"

   headers = {
       "Accept": "application/yang-data+json",
       "Content-Type": "application/yang-data+json"
   }

   # 1. Query Device Hostname (Refer to Swagger: Cisco-IOS-XE-native:native/hostname)
   url_hostname = f"https://{HOST}/restconf/data/Cisco-IOS-XE-native:native/hostname"
   resp = requests.get(url_hostname, auth=(USER, PASS), headers=headers, verify=False)
   print("=== Device Hostname ===")
   print(f"Status: {resp.status_code}")
   print(json.dumps(resp.json(), indent=2))

   # 2. Query Native Interfaces (Refer to Swagger: Cisco-IOS-XE-native:native/interface)
   url_interfaces = f"https://{HOST}/restconf/data/Cisco-IOS-XE-native:native/interface"
   resp = requests.get(url_interfaces, auth=(USER, PASS), headers=headers, verify=False)
   print("\n=== Native Interfaces ===")
   print(f"Status: {resp.status_code}")
   print(json.dumps(resp.json(), indent=2))
   EOF
   ```

3. Run the script:
   ```bash
   python3 query_restconf.py
   ```

---

### Step 3: Direct NETCONF Programmability with Python ncclient

#### 💡 What is NETCONF & ncclient?
[NETCONF (RFC 6241)](https://datatracker.ietf.org/doc/html/rfc6241) is a network management protocol that provides mechanisms to install, manipulate, and delete configurations on network devices over SSH (Port 830). **[ncclient](https://ncclient.readthedocs.io/)** is the standard Python library used to establish NETCONF sessions, retrieve operational state (`<get>`), and apply atomic configurations (`<edit-config>`).

1. Navigate to the NETCONF workspace:
   ```bash
   mkdir -p ~/samples/netconf && cd ~/samples/netconf
   ```

2. Create the Python script `get_config_netconf.py`:
   ```bash
   cat <<'EOF' > get_config_netconf.py
   from ncclient import manager
   import xml.dom.minidom

   HOST = "10.10.20.181"
   PORT = 830
   USER = "developer"
   PASS = "C1sco12345"

   # Define NETCONF filter for native hostname
   netconf_filter = """
   <filter xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
     <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
       <hostname/>
     </native>
   </filter>
   """

   print(f"Connecting to {HOST}:{PORT} via NETCONF...")
   with manager.connect(
       host=HOST,
       port=PORT,
       username=USER,
       password=PASS,
       hostkey_verify=False,
       device_params={"name": "iosxe"}
   ) as m:
       response = m.get_config(source="running", filter=netconf_filter)
       # Pretty-print XML output
       xml_pretty = xml.dom.minidom.parseString(response.xml).toprettyxml(indent="  ")
       print("\n=== NETCONF XML Configuration Response ===")
       print(xml_pretty)
   EOF
   ```

3. Run the script:
   ```bash
   python3 get_config_netconf.py
   ```

---

### Step 4: Declarative Automation with Terraform (NETCONF)

#### 💡 What is Terraform?
[Terraform](https://developer.hashicorp.com/terraform/intro) is an open-source **Infrastructure as Code (IaC)** tool created by HashiCorp. It allows you to define, provision, and manage network infrastructure using human-readable, declarative configuration files (`.tf`). 

In this sandbox, we use the official **[Cisco IOS XE Terraform Provider (`CiscoDevNet/iosxe`)](https://registry.terraform.io/providers/CiscoDevNet/iosxe/latest)**.

> ℹ️ **Protocol Note:**  
> The `CiscoDevNet/iosxe` Terraform provider connects directly via **NETCONF over SSH (Port 830)**. It translates Terraform declarative HCL resources into model-driven YANG RPCs to perform atomic configuration transactions and state synchronization directly against the device's `Cisco-IOS-XE-native` data model.

1. Navigate to the Terraform workspace:
   ```bash
   mkdir -p ~/samples/terraform && cd ~/samples/terraform
   ```

2. Create the `main.tf` configuration:
   ```bash
   cat <<'EOF' > main.tf
   terraform {
     required_providers {
       iosxe = {
         source  = "CiscoDevNet/iosxe"
         version = ">= 0.5.0"
       }
     }
   }

   provider "iosxe" {
     # Connects via NETCONF over SSH (Port 830)
     host     = "10.10.20.181"
     username = "developer"
     password = "C1sco12345"
   }

   resource "iosxe_interface_loopback" "loopback100" {
     name        = "100"
     description = "Provisioned via Terraform NETCONF in DevNet Virtual Sandbox"
     ip_address  = "10.100.100.1"
     subnet_mask = "255.255.255.255"
   }
   EOF
   ```

3. Initialize the provider, inspect the execution plan, and apply the configuration over NETCONF:
   ```bash
   terraform init
   terraform plan
   terraform apply -auto-approve
   ```

---

### Step 5: Configuration Management with Ansible

#### 💡 What is Ansible?
[Ansible](https://www.ansible.com/overview/how-ansible-works) is an open-source, agentless automation engine used for configuration management and orchestration. In this sandbox, we use the official **[Ansible `cisco.ios` Collection](https://galaxy.ansible.com/ui/repo/published/cisco/ios/)**, which provides declarative modules (`ios_vlans`, `ios_static_routes`, `ios_interfaces`) to manage Cisco IOS XE network devices over SSH.

1. Navigate to the Ansible workspace:
   ```bash
   mkdir -p ~/samples/ansible && cd ~/samples/ansible
   ```

2. Create the inventory file:
   ```bash
   cat <<'EOF' > inventory.yaml
   all:
     hosts:
       target_device:
         ansible_host: 10.10.20.181
         ansible_user: developer
         ansible_password: C1sco12345
         ansible_network_os: cisco.ios.ios
         ansible_connection: network_cli
   EOF
   ```

3. Create the playbook:
   * **For C9000V (Switching - VLAN Provisioning):**
     ```bash
     cat <<'EOF' > configure_device.yaml
     - name: Manage Catalyst VLANs
       hosts: target_device
       gather_facts: no
       tasks:
         - name: Create VLAN 100
           cisco.ios.ios_vlans:
             config:
               - vlan_id: 100
                 name: DevNet_Automated_VLAN
             state: merged
     EOF
     ```
   * **For C8000V (Routing - Static Route Provisioning):**
     ```bash
     cat <<'EOF' > configure_device.yaml
     - name: Manage Catalyst Routing
       hosts: target_device
       gather_facts: no
       tasks:
         - name: Configure Static Route
           cisco.ios.ios_static_routes:
             config:
               - address_families:
                   - afi: ipv4
                     routes:
                       - dest: 10.50.0.0/16
                         next_hops:
                           - forward_router_address: 198.18.1.1
             state: merged
     EOF
     ```

4. Run the playbook:
   ```bash
   ansible-playbook -i inventory.yaml configure_device.yaml
   ```

---

# Recommended DevNet Learning Lab

Follow this official guided learning lab to explore and validate Cisco YANG data models with hands-on exercises:

* **[Cisco YANG Suite: Introduction & Launching](https://developer.cisco.com/learning/labs/intro-yangsuite/introduction-launching-yang-suite/):** Master GUI-based YANG model exploration, device profiling, and RPC building using Cisco YANG Suite.
* **[RESTCONF: Cisco IOS XE YANG Documentation Hub](https://ciscodevnet.github.io/cisco-ios-xe-openapi-swagger/index.html/):** Learn how to use RESTCONF with YANG models for device configuration and data retrieval.