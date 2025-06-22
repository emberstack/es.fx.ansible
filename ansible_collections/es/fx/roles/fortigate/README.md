# FortiGate Ansible Role

## Purpose

The `fortigate` role is a comprehensive, unified Ansible role for managing FortiGate firewall configurations. It provides a single entry point for configuring all aspects of FortiGate devices, from basic network settings to advanced security features.

## Features

This role supports configuration of:

- **System Configuration**
  - System settings and administration
  - Certificates and PKI
  - LDAP integration
  - User and group management
  - DNS and DDNS services
  - External resources

- **Network Configuration**
  - Interfaces and DHCP
  - Zones and VLANs
  - Switch controllers and MC-LAG
  - Static routes
  - SD-WAN and health checks

- **Security Configuration**
  - Firewall addresses and groups
  - Security policies
  - DNS filtering
  - Application and web filtering profiles
  - VPN (IPSec and SSL VPN)

- **Services**
  - Virtual IPs (VIPs) and server load balancing
  - IP pools
  - Custom services
  - Multicast configuration

- **Wireless**
  - SSID configuration
  - Access point profiles
  - Wireless security settings

## Requirements

- Ansible 2.9 or higher
- FortiOS versions: 7.0, 7.2, or 7.4
- Required Ansible collections:
  - `fortinet.fortios` >= 2.3.0
  - `fortinet.fortimanager` >= 2.2.0

Install collections using:
```bash
ansible-galaxy collection install -r requirements.yaml
```

## Role Variables

The role uses a modular variable structure. Each feature is controlled by specific variable prefixes:

| Variable Prefix | Purpose |
|-----------------|---------|
| `fortigate_system_*` | System-level configurations |
| `fortigate_interfaces` | Network interface configurations |
| `fortigate_firewall_*` | Firewall policies, addresses, and VIPs |
| `fortigate_vpn_*` | VPN configurations |
| `fortigate_wifi_*` | Wireless configurations |
| `fortigate_sdwan_*` | SD-WAN settings |

Variables can be defined at multiple levels:
- `group_vars/fortigates.yaml` - Common settings for all FortiGates
- `host_vars/<hostname>.yaml` - Host-specific configurations
- `vars/projects/<project>.yaml` - Project-specific configurations

## Usage

### Basic Usage - Complete Setup

Apply all configurations to FortiGate devices:

```yaml
---
- name: Configure FortiGate - Complete Setup
  hosts: fortigates
  gather_facts: no
  connection: httpapi
  
  roles:
    - fortigate
```

### Project-Specific Usage

Configure FortiGate for a specific project:

```yaml
---
- name: Configure FortiGate for Multimedia Services
  hosts: gateway
  gather_facts: no
  connection: httpapi
  
  vars_files:
    - ../vars/projects/multimedia.yaml
  
  pre_tasks:
    - name: Map project variables to role variables
      set_fact:
        fortigate_firewall_addresses: "{{ multimedia_firewall_addresses }}"
        fortigate_firewall_vips: "{{ multimedia_firewall_vips }}"
        fortigate_firewall_policies: "{{ multimedia_firewall_policies }}"
  
  roles:
    - fortigate
```

### Testing Connection

Test FortiGate connectivity:

```yaml
---
- name: Test FortiGate Connection
  hosts: fortigates
  gather_facts: no
  connection: httpapi
  
  tasks:
    - name: Test connection
      include_role:
        name: fortigate
        tasks_from: connection.yaml
```

## Project Variables Structure

When creating project-specific configurations, follow this pattern:

```yaml
# vars/projects/myproject.yaml
---
# Define project-specific variables with project prefix
myproject_wan1_interface: x8
myproject_backend_ip: 10.1.100.50
myproject_external_port: 8080

# Define FortiGate configurations
myproject_firewall_addresses:
  - state: present
    firewall_address:
      name: "local.myproject.server"
      type: ipmask
      subnet: "{{ myproject_backend_ip }}/32"

myproject_firewall_vips:
  - state: present
    firewall_vip:
      name: "myproject.wan1"
      extintf: "{{ myproject_wan1_interface }}"
      extport: "{{ myproject_external_port }}"
      mappedip: "{{ myproject_backend_ip }}"
```

## Conditional Execution

The role automatically skips tasks when their corresponding variables are not defined. This allows you to:

1. Apply only specific configurations by defining only needed variables
2. Use the same role for different deployment scenarios
3. Maintain a single source of truth for all FortiGate configurations

## Best Practices

1. **Inventory Organization**
   - Use group variables for common settings
   - Use host variables for device-specific settings
   - Use project variables for application-specific configurations

2. **Variable Naming**
   - Prefix project variables with the project name
   - Map project variables to role variables in pre_tasks
   - Keep variable names descriptive and consistent

3. **Testing**
   - Always test configurations in a staging environment first
   - Use the connection test playbook to verify connectivity
   - Review generated configurations before applying to production

4. **Version Control**
   - Track all variable files in version control
   - Document changes in commit messages
   - Use meaningful project names that reflect their purpose

## Troubleshooting

### Connection Issues

If you encounter connection problems:

1. Verify the FortiGate is reachable: `ping <fortigate-ip>`
2. Check HTTPS access is enabled on the management interface
3. Verify credentials in inventory or vault files
4. Run the connection test playbook

### Configuration Not Applied

If configurations aren't being applied:

1. Check that variables are properly defined
2. Verify variable mapping in pre_tasks (for project playbooks)
3. Run with `-vvv` to see which tasks are being skipped
4. Check FortiGate logs for any errors

### Version Compatibility

The role includes version-specific configurations in `vars/versions/`. If you encounter version-related issues:

1. Verify your FortiOS version is supported (7.0, 7.2, or 7.4)
2. Check version-specific variable files for compatibility notes
3. Some features may not be available on older versions

## Examples

See the `playbooks/` directory for example implementations:

- `site.yaml` - Complete FortiGate configuration
- `multimedia.yaml` - Media server configuration
- `qbittorrent.yaml` - BitTorrent client configuration
- `sp-vpn.yaml` - Site-to-site VPN configuration
- `cleanup-defaults.yaml` - Remove default configurations

## Support

For issues or questions:
1. Check existing playbooks for examples
2. Review the task files in `tasks/` for available options
3. Consult FortiGate documentation for feature-specific details
4. Check Ansible fortios collection documentation