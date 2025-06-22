# EmberStack Frameworks Ansible Collection

A comprehensive Ansible collection for enterprise network infrastructure automation. This collection provides production-ready roles for managing network devices, security appliances, and infrastructure components.

## Overview

The EmberStack Frameworks (es.fx) collection delivers standardized, battle-tested Ansible roles for automating complex network environments. Each role follows consistent patterns and best practices for reliable infrastructure as code.

## Installation

```bash
# Install from source
ansible-galaxy collection install git+https://github.com/emberstack/es.fx.ansible.git

# Install from local directory
ansible-galaxy collection install ./ansible_collections/es/fx
```

## Available Roles

- **fortigate** - Comprehensive FortiGate firewall management with full feature support

More roles for additional platforms coming soon.

## Requirements

- Ansible 2.9+
- Python 3.6+
- Platform-specific collections (see individual role documentation)

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Support

- **Issues**: [GitHub Issues](https://github.com/emberstack/es.fx.ansible/issues)
- **Commercial Support**: support@emberstack.com
