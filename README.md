# hdefazio.mcp_fetch

Ansible collection that installs the [Fetch MCP server](https://github.com/modelcontextprotocol/servers/blob/main/src/fetch/README.md) in Ansible Execution Environments.

The Fetch MCP server provides web content fetching capabilities for AI assistants and automation workflows through the Model Context Protocol.

## Requirements

- Ansible >= 2.16.0
- Python >= 3.10 in the execution environment
- `ansible.mcp_builder` collection >= 1.0.0

## Installation
### Install from source

```bash
git clone https://github.com/hdefazio/hdefazio.mcp_fetch.git
cd hdefazio.mcp_fetch
ansible-galaxy collection build
ansible-galaxy collection install hdefazio-mcp_fetch-*.tar.gz
```

### Development

To work on this collection in place without building/installing:

```bash
# Clone directly into your collections path
mkdir -p ${ANSIBLE_COLLECTIONS_PATH}/ansible_collections/hdefazio
cd ${ANSIBLE_COLLECTIONS_PATH}/ansible_collections/hdefazio
git clone https://github.com/hdefazio/hdefazio.mcp_fetch.git mcp_fetch
```

Now you can edit files directly and test changes immediately without rebuilding.

## Roles

### fetch_mcp

Installs the Fetch MCP server using the `ansible.mcp_builder` framework.

**Usage in a playbook:**

```yaml
---
- name: Install Fetch MCP Server
  hosts: localhost
  roles:
    - hdefazio.mcp_fetch.fetch_mcp
```

**Or with include_role:**

```yaml
---
- name: Install Fetch MCP Server
  hosts: localhost
  tasks:
    - name: Install Fetch MCP
      ansible.builtin.include_role:
        name: hdefazio.mcp_fetch.fetch_mcp
```

## Playbooks

### install_fetch_mcp.yml

Installs the Fetch MCP server into the execution environment.

```yaml
ansible-playbook hdefazio.mcp_fetch.install_fetch_mcp
```

## Usage in Execution Environments

This collection is designed to be used in Ansible Execution Environment builds to pre-install the Fetch MCP server.

### Example execution-environment.yml

```yaml
---
version: 3

images:
  base_image:
    name: registry.redhat.io/ansible-automation-platform-25/ee-minimal-rhel9:latest

dependencies:
  ansible_core:
    package_pip: ansible-core
  ansible_runner:
    package_pip: ansible-runner
  galaxy: requirements.yml

options:
  package_manager_path: /usr/bin/microdnf

additional_build_files:
  - src: hdefazio-mcp_fetch-1.0.0.tar.gz
    dest: configs

additional_build_steps:
  append_final: |
    RUN ansible-playbook hdefazio.mcp_fetch.install_fetch_mcp
```

### Example requirements.yml

```yaml
---
collections:
  - name: ansible.mcp_builder
  - name: hdefazio.mcp_fetch
    source: /build/configs/hdefazio-mcp_fetch-1.0.0.tar.gz
    type: file
```

### Building the EE

```bash
# Build the collection
ansible-galaxy collection build --output-path /path/to/ee_build/

# Build the execution environment
cd /path/to/ee_build
ansible-builder build --tag my-ee-with-fetch:latest
```
