# FortiGate Ansible Role - Coding Patterns

This document defines the standard patterns to use when creating or modifying tasks in this role to ensure consistency and reduce code duplication.

## Standard Task Patterns

### 1. Basic Resource Configuration Pattern

For resources that support VDOM and have a simple structure:

```yaml
- name: Configure [resource type] - {{ item.[resource_key].name }}
  fortinet.fortios.fortios_[module_name]:
    state: "{{ item.state | default(fortigate_default_state) }}"
    access_token: "{{ access_token }}"
    vdom: "{{ item.vdom | default(vdom | default(fortigate_vdom)) }}"
    [resource_key]: "{{ item.[resource_key] }}"
  loop: "{{ fortigate_[resource_list] | default([]) }}"
  loop_control:
    label: "{{ item.[resource_key].name }}"
  when: fortigate_[resource_list] is defined and fortigate_[resource_list] | length > 0
```

### 2. Resource Without VDOM Pattern

For resources that don't support VDOM:

```yaml
- name: Configure [resource type] - {{ item.[resource_key].name }}
  fortinet.fortios.fortios_[module_name]:
    state: "{{ item.state | default(fortigate_default_state) }}"
    access_token: "{{ access_token }}"
    [resource_key]: "{{ item.[resource_key] }}"
  loop: "{{ fortigate_[resource_list] | default([]) }}"
  loop_control:
    label: "{{ item.[resource_key].name }}"
  when: fortigate_[resource_list] is defined and fortigate_[resource_list] | length > 0
```

### 3. Custom Loop Variable Pattern

When you need to avoid variable name conflicts:

```yaml
- name: Configure [resource] - {{ [custom_var].[resource_key].name }}
  fortinet.fortios.fortios_[module_name]:
    state: "{{ [custom_var].state | default(fortigate_default_state) }}"
    access_token: "{{ access_token }}"
    vdom: "{{ [custom_var].vdom | default(vdom | default(fortigate_vdom)) }}"
    [resource_key]: "{{ [custom_var].[resource_key] }}"
  loop: "{{ fortigate_[resource_list] | default([]) }}"
  loop_control:
    loop_var: [custom_var]
    label: "{{ [custom_var].[resource_key].name | default('unnamed') }}"
  when: fortigate_[resource_list] is defined and fortigate_[resource_list] | length > 0
```

### 4. Fact Gathering Pattern

For gathering facts with error handling:

```yaml
- name: Get all [resource type]
  fortinet.fortios.fortios_configuration_fact:
    access_token: "{{ access_token }}"
    selector: "[selector_path]"
    vdom: "{{ vdom | default(fortigate_vdom) }}"
    formatters:
      - name
      - id
  register: all_[resources]
  retries: "{{ fortigate_connection_retries }}"
  delay: "{{ fortigate_connection_delay }}"
  until: all_[resources] is not failed
  failed_when: false
```

### 5. Monitor Facts Pattern

For monitor facts (system status, certificates, etc.):

```yaml
- name: Monitor [description]
  fortinet.fortios.fortios_monitor_fact:
    access_token: "{{ access_token }}"
    selector: "[monitor_selector]"
    vdom: "{{ vdom | default(fortigate_vdom) }}"
  register: [fact_variable]
  retries: "{{ fortigate_connection_retries }}"
  delay: "{{ fortigate_connection_delay }}"
  until: [fact_variable] is not failed
```

## Variable Naming Conventions

1. **Resource Lists**: `fortigate_[category]_[resources]`
   - Example: `fortigate_firewall_addresses`, `fortigate_vpn_tunnels`

2. **Module Keys**: Match the API structure
   - Example: `firewall_address`, `system_interface`

3. **Loop Labels**: Always show the resource name
   - Use: `label: "{{ item.[key].name }}"`

4. **Custom Loop Variables**: Use descriptive names
   - Example: `health_check`, `static_route`, `tunnel`

## Conditional Logic Standards

1. **When Conditions**: Always check both defined and length
   ```yaml
   when: fortigate_[variable] is defined and fortigate_[variable] | length > 0
   ```

2. **Default Values**: Use cascade pattern
   ```yaml
   state: "{{ item.state | default(fortigate_default_state) }}"
   vdom: "{{ item.vdom | default(vdom | default(fortigate_vdom)) }}"
   ```

3. **Optional Parameters**: Use conditional inclusion
   ```yaml
   vdom: "{{ item.vdom | default(vdom | default(fortigate_vdom)) if use_vdom else omit }}"
   ```

## File Organization

1. **Task Files**: One file per logical group
   - `addresses.yaml` - Firewall addresses and groups
   - `services.yaml` - Services and service groups
   - `policies.yaml` - Firewall policies

2. **Comments**: Add section headers
   ```yaml
   ---
   # [Description of what this file configures]
   
   # [Section 1]
   - name: ...
   
   # [Section 2]
   - name: ...
   ```

3. **Task Names**: Be descriptive and consistent
   - Good: `Configure firewall address - {{ item.firewall_address.name }}`
   - Bad: `Add address`

## Error Handling

1. **Retries**: Use default retry values
   ```yaml
   retries: "{{ fortigate_connection_retries }}"
   delay: "{{ fortigate_connection_delay }}"
   ```

2. **Failed When**: Use sparingly
   ```yaml
   failed_when: false  # Only for fact gathering that might return empty
   ```

3. **Until**: Ensure success
   ```yaml
   until: result is not failed
   ```

## Examples

### Example 1: Simple Resource List

```yaml
# Configure custom firewall services
- name: Configure firewall service - {{ item.firewall_service_custom.name }}
  fortinet.fortios.fortios_firewall_service_custom:
    state: "{{ item.state | default(fortigate_default_state) }}"
    access_token: "{{ access_token }}"
    vdom: "{{ item.vdom | default(vdom | default(fortigate_vdom)) }}"
    firewall_service_custom: "{{ item.firewall_service_custom }}"
  loop: "{{ fortigate_firewall_services | default([]) }}"
  loop_control:
    label: "{{ item.firewall_service_custom.name }}"
  when: fortigate_firewall_services is defined and fortigate_firewall_services | length > 0
```

### Example 2: Resource with Custom Loop Variable

```yaml
# Configure firewall health checks
- name: Configure firewall health check - {{ health_check.firewall_ldb_monitor.name }}
  fortinet.fortios.fortios_firewall_ldb_monitor:
    access_token: "{{ access_token }}"
    state: "{{ health_check.state | default(fortigate_default_state) }}"
    vdom: "{{ health_check.vdom | default(vdom | default(fortigate_vdom)) }}"
    firewall_ldb_monitor: "{{ health_check.firewall_ldb_monitor }}"
  loop: "{{ fortigate_firewall_health_checks | default([]) }}"
  loop_control:
    loop_var: health_check
    label: "{{ health_check.firewall_ldb_monitor.name | default('health_check') }}"
  when: fortigate_firewall_health_checks is defined and fortigate_firewall_health_checks | length > 0
```

## Checklist for New Tasks

- [ ] Use FQCN for module names (`fortinet.fortios.fortios_*`)
- [ ] Include standard parameters (state, access_token, vdom)
- [ ] Use default values from `defaults/main.yaml`
- [ ] Add descriptive task names with resource identification
- [ ] Include proper loop controls with labels
- [ ] Add when conditions checking both defined and length
- [ ] Follow the variable naming conventions
- [ ] Add section comments in the task file
- [ ] Test with both single items and lists
- [ ] Test with missing optional parameters