# Welcome to LintandPods Knowledge Base :material-earth:

> A comprehensive collection of Linux, Docker, Kubernetes, database administration, networking commands, RHEL configurations, troubleshooting guides, and technical Q&A.

---

## Quick Navigation

<div class="grid cards" markdown>

-   :material-penguin:{ .lg .middle } __Linux__

    ---

    Linux administration, networking, RHEL configuration, and system management

    [:octicons-arrow-right-24: Explore Linux](linux/index.md)

-   :material-docker:{ .lg .middle } __DevOps & Infrastructure__

    ---

    Docker, Kubernetes, CI/CD pipelines, and infrastructure automation

    [:octicons-arrow-right-24: DevOps Guides](devops/index.md)

-   :material-database:{ .lg .middle } __Databases__

    ---

    MySQL, MongoDB, PostgreSQL administration and management

    [:octicons-arrow-right-24: Explore Databases](databases/index.md)

-   :material-frequently-asked-questions:{ .lg .middle } __Q&A__

    ---

    Common issues, solutions, and best practices

    [:octicons-arrow-right-24: Browse Q&A](qa/index.md)

-   :material-file-document-multiple:{ .lg .middle } __Cheatsheets__

    ---

    Quick reference guides for everyday tasks

    [:octicons-arrow-right-24: View Cheatsheets](cheatsheets/index.md)

</div>

---

## :material-magnify: Search Tips

Use the search bar above to find:

- **Commands**: `nmcli`, `ip addr`, `firewall-cmd`
- **Topics**: `static ip`, `dns`, `routing`
- **Error messages**: Copy/paste exact error text
- **Tags**: `#troubleshooting`, `#configuration`

---

## :material-library: Popular Topics

### Linux & Networking

- [nmcli - NetworkManager CLI](networking/commands/nmcli.md)
- [ip command - Network Configuration](networking/commands/ip-command.md)
- [firewall-cmd - Firewall Management](networking/commands/firewall-cmd.md)
- [Network Troubleshooting](networking/troubleshooting/connectivity.md)

### Docker & Kubernetes

- [DevOps & Infrastructure](devops/index.md)
- [Docker Container Management](devops/docker/index.md)
- [Kubernetes Orchestration](devops/kubernetes/index.md)
- [CI/CD Pipelines](devops/cicd/index.md)

### Databases

- [MySQL User Management](databases/mysql/index.md)
- [MongoDB Collections & Queries](databases/mongodb/index.md)
- [PostgreSQL Configuration](databases/postgres/index.md)

### RHEL Administration

- [Network Configuration](rhel/configuration/network-config.md)
- [Firewall Setup](rhel/configuration/firewall.md)
- [SELinux Configuration](rhel/configuration/selinux.md)

---

## :material-plus-circle: Recent Updates

!!! tip "Latest Additions"
    - Added comprehensive nmcli guide
    - Updated troubleshooting workflows
    - New VLAN configuration examples

---

## :material-tag-multiple: Browse by Tag

Explore content by topic:

[View all tags →](tags.md){ .md-button .md-button--primary }

---

## :material-information: About This KB

This knowledge base is a comprehensive collection of:

- **Linux & RHEL Administration**: System configuration, networking, and services
- **DevOps Technologies**: Docker containerization and Kubernetes orchestration
- **Database Administration**: MySQL, MongoDB, and PostgreSQL management
- **Networking**: Commands, protocols, troubleshooting, and best practices
- **Troubleshooting Workflows**: Real solutions to real problems
- **Command References**: Tested and verified with real outputs

Everything here is:

- :material-check-circle: Tested on modern Linux systems
- :material-check-circle: Based on real-world scenarios
- :material-check-circle: Continuously updated
- :material-check-circle: Searchable and well-organized
- :material-check-circle: Covers multiple technologies and platforms

---

## :material-rocket: Quick Start Examples

### Example 1: Configure Static IP

```bash
nmcli connection modify ens33 \
  ipv4.method manual \
  ipv4.addresses "192.168.1.100/24" \
  ipv4.gateway "192.168.1.1" \
  ipv4.dns "8.8.8.8 8.8.4.4"

nmcli connection up ens33
```

[:octicons-arrow-right-24: Full Guide](networking/commands/nmcli.md)

### Example 2: Troubleshoot Connectivity

```bash
# Check interface
ip link show ens33

# Check IP
ip addr show ens33

# Test gateway
ping -c 4 192.168.1.1

# Check DNS
dig google.com
```

[:octicons-arrow-right-24: Complete Troubleshooting](networking/troubleshooting/connectivity.md)

---

## :material-book-open: How to Use This KB

1. **Search First**: Use the search bar for quick answers
2. **Browse by Section**: Navigate using the top menu
3. **Check Tags**: Find related topics easily
4. **Copy Commands**: All code blocks have copy buttons
5. **Follow Examples**: Real outputs included for verification

---

## :material-handshake: Contributing

Found an error or want to improve something?

- This KB is version controlled with Git
- All changes are tracked and reviewed
- Feel free to suggest improvements

---

**Last Updated**: {Date}  
**Version**: 1.0
