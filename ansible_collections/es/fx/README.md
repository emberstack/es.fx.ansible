# EmberStack Frameworks Collection (es.fx)

A comprehensive Ansible collection for network infrastructure automation, providing enterprise-grade configuration management for network devices.

## Description

The EmberStack Frameworks (es.fx) collection delivers production-ready Ansible roles for automating network infrastructure. Starting with comprehensive FortiGate firewall management, this collection will expand to support multiple network platforms.

## Requirements

- Ansible 2.9 or higher
- Python 3.6 or higher
- Network device specific requirements (see individual role documentation)

## Installation

### Install from Galaxy (when published)
```bash
ansible-galaxy collection install es.fx
```

### Install from Source
```bash
ansible-galaxy collection install git+https://github.com/emberstack/ansible-collection-fx.git
```

### Install from Local Directory
```bash
ansible-galaxy collection install /path/to/es/fx
```

### Using Requirements File
```yaml
# requirements.yml
collections:
  - name: es.fx
    version: ">=1.0.0"
```

```bash
ansible-galaxy collection install -r requirements.yml
```

## Included Content

### Roles

#### fortigate
Comprehensive FortiGate firewall configuration management.

**Features:**
- System configuration (hostname, DNS, NTP, certificates)
- Network configuration (interfaces, zones, VLANs, routing)
- Security policies and objects (addresses, services, policies)
- VPN configuration (IPSec, SSL VPN)
- SD-WAN configuration
- Wireless controller (FortiAP management)
- High availability and clustering
- Complete VDOM support

**Example Usage:**
```yaml
---
- name: Configure FortiGate Firewall
  hosts: fortigates
  collections:
    - es.fx
  
  roles:
    - fortigate
```

## Usage

### Basic Playbook
```yaml
---
- name: Configure Network Infrastructure
  hosts: network_devices
  collections:
    - es.fx
    
  tasks:
    - name: Configure FortiGate devices
      include_role:
        name: fortigate
      when: device_type == "fortigate"
```

### Using with Variables
```yaml
---
- name: Configure FortiGate with Custom Settings
  hosts: fortigates
  collections:
    - es.fx
    
  vars:
    fortigate_system_settings:
      hostname: "firewall-01"
      timezone: "America/New_York"
    
  roles:
    - fortigate
```

### Project-Specific Configuration
```yaml
---
- name: Configure FortiGate for Web Application
  hosts: gateway
  collections:
    - es.fx
    
  vars_files:
    - vars/webapp_config.yml
    
  pre_tasks:
    - name: Map project variables
      set_fact:
        fortigate_firewall_vips: "{{ webapp_vips }}"
        fortigate_firewall_policies: "{{ webapp_policies }}"
  
  roles:
    - fortigate
```

## Dependencies

This collection requires the following Ansible collections:
- `fortinet.fortios` >= 2.4.0 (for FortiGate role)
- `ansible.netcommon` >= 2.0.0

## Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details.

### Development Setup
```bash
# Clone the repository
git clone https://github.com/emberstack/ansible-collection-fx.git

# Install in development mode
cd ansible-collection-fx
ansible-galaxy collection install . --force
```

### Running Tests
```bash
# Run sanity tests
ansible-test sanity

# Run integration tests
ansible-test integration
```

## Support

- **Documentation**: [GitHub Wiki](https://github.com/emberstack/ansible-collection-fx/wiki)
- **Issues**: [GitHub Issues](https://github.com/emberstack/ansible-collection-fx/issues)
- **Discussions**: [GitHub Discussions](https://github.com/emberstack/ansible-collection-fx/discussions)
- **Commercial Support**: [support@emberstack.com](mailto:support@emberstack.com)

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Authors

- EmberStack Team <support@emberstack.com>
- [Contributors](https://github.com/emberstack/ansible-collection-fx/graphs/contributors)

## Roadmap

### Current (v1.0)
- ✅ FortiGate comprehensive configuration role

### Planned
- 🔄 Palo Alto Networks firewall role
- 🔄 Cisco ASA/FTD role
- 🔄 Check Point firewall role
- 🔄 F5 BIG-IP role
- 🔄 Citrix ADC role

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history.

## Examples

Find more examples in the [examples](examples/) directory:
- Basic FortiGate setup
- Multi-site VPN configuration
- SD-WAN deployment
- High availability setup
- Security policy templates