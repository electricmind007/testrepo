### Prerequisites
- Have an ansible control environment with ssh keys created for a user with NOPASSWD sudo permissions on all remote systems.
  - See [Workstation Setup](../Workstation%20Setup.md) for instructions.

### Provisioning Servers via Zurich OpenStack (https://zc2.ibm.com/home)
This stage walks through provisioning 4 servers. One for Nexus and 3 or more for Kubernetes.  

1. **Log onto OpenStack**
  1. Open a browser window to: [https://zc2.ibm.com/home](https://zc2.ibm.com/home)  
  1. If this is your first time here, click on: `If you are new here` to create an account.  
  1. If you have an account, click on `Boot up virtual machines`  
  1. Log In.  
  1. Change to <USERNAME> project; click on dropdown to right of 'OpenStack' on very top menu and select <USERNAME>  

1. **Import SSH Key from ansible control environment**
  1. On left side, click on `Compute` to open subMenu. Select `Access & Security`
  1. Select `Key Pair` tab
  1. On right side to left of 'Delete Key Pairs' select `Import Key Pair`
    * Give name to KeyPair; USERNAME_controlKey
    * In a text editor, open key from vagrant deployment (ie. `zc2Cloud.pub`) and paste contents into the `PublicKey` field. (should be just 1 line)
  1. Imported key should now be listed.

1. **Open Default Ports**
  1. On left side, from the `Compute` subMenu select `Access & Security`
  1. Choose `Security Groups`
  1. For the default group click on `Manage Rules`
  1. Several ports are already opened; ensure the ports listed below are created.
    1. To create; click on `+ Add Rule` on right side
      * Rule:  `Custom TCP Rule` OR `Custom UDP Rule`
      * Direction:  `Ingress`
      * OpenPort:  `Port` OR `Port Range`
      * From: AS DESCRIBED BELOW
      * To: AS DESCRIBED BELOW
      * Remote: CIDR
      * CIDR: 0.0.0.0/0
1. **Create server instances**
  1. On left side, from the `Compute` subMenu select `Instances`
  1. On right side, select `Launch Instances`
  1. Fill in config and then press `next`
    * Name: Kube
    * Availabilty Zone: `nova`
    * Count: 4
  1. Find `Ubuntu 16.04.2 x64 - 20170224`, click `+` beside it and then press `next`
  1. Find `2core8gmem`, click `+` beside it and then press `Launch Instance`

1. **Persistent Mounts**
  1. On left side, from the `Compute` subMenu select `Volumes`
  1. Each Server requires a 10GB volume; do the following 4 times
    1. Click `Create Volume`
    1. Fill in config and press `Create Volume`
      * Volume Name: `Volume-#` (ie; Volume-1, Volume-2, etc)
      * Description: empty
      * Volume Source: `No source, empty volume`
      * Type: `Standard`
      * Size: `10`
      * AvailabiltyZone: `nova`
  1. For each volume, click the down arrow next to the `Edit Volume` button on the far right and select `Manage Attachments`
  1. Select a different Kubernetes or Nexus node instance for each volume from the `Attach to Instance` drop down list

### Environment Setup
Environment setup steps vary depending on provider. See [Appendix I - Environment Providers](#appendix-i-environment-providers) for general steps.

1. Persistent mounts - Each Kubernetes node must have a persistent volume attached
2. Firewall - Ensure the following ports are open for inbound connections.
   * TCP: `22`, `6443`, `8080`, `8082 -> 8084`, `30080 -> 30083`  
   * UDP: `30300 -> 30301`  
   **What the above ports are used for:**  
   * SSH
     * 22 (TCP)
   * Rancher (Only needed when using rancher)
     * 8080 (TCP) - WebUI
   * Nexus
     * 8082 (TCP) - WebUI
     * 8083 (TCP) - CLI internal repo (push/pull)
     * 8084 (TCP) - CLI internal/external proxy (pull only)
   * Kubernetes
     * 6443 (TCP) - API
     * 30080 (TCP) - Dashboard WebUI
     * 30083 (TCP) - Grafana WebUI
   * Graylog
     * 30300 (UDP) - Text UDP Input
     * 30301 (UDP) - Syslog UDP Input
     * 30081 (TCP) - WebUI
   * Zabbix
     * 30082 (TCP) - WebUI

## Appendix I - Environment Providers
### Zurich
#### SSH Keys
```bash
ssh-keygen -t rsa -f zc2Cloud.key
```
1. From the [OpenStack Dashboard](https://zc2.ibm.com/horizon) Navigate to `Project` > `Compute` > `Access & Security`
1. Click the `Key Pairs` tab
1. Click `Import Key Pair` near the top right and paste in the contents of `~/.ssh/id_rsa.pub` from your ansible control system.

#### Persistent Mounts
1. From the [OpenStack Dashboard](https://zc2.ibm.com/horizon) Navigate to `Project` > `Compute` > `Volumes`
1. Create a 10GB volume for each Kubernetes node and the Nexus node
1. For each volume, click the down arrow next to the `Edit Volume` button on the far right and select `Manage Attachments`
1. Select a different Kubernetes or Nexus node instance for each volume from the `Attach to Instance` drop down list

#### Opening Ports
1. From the [OpenStack Dashboard](https://zc2.ibm.com/horizon) Navigate to `Project` > `Compute` > `Access & Security`
1. Click `Manage Rules` for the `default` group
1. Click "Add Rule" near the top right and fill in the values as needed
    - Usually only filling in the `Port` will be necessary. For UDP rules also change `Rule` to `Custom UDP Rule`. All other values can be left as default.
