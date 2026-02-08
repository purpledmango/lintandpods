# Welcome to My Network Knowledge Base 🌐

> A personal collection of networking commands, RHEL configurations, troubleshooting guides, and technical Q&A.

---

## Quick Navigation

<div class="grid cards" markdown>

-   :material-console-network:{ .lg .middle } __Networking__

    ---

    Commands, protocols, and network fundamentals

    [:octicons-arrow-right-24: Explore Networking](networking/index.md)

-   :material-redhat:{ .lg .middle } __RHEL__

    ---

    Red Hat Enterprise Linux configurations and services

    [:octicons-arrow-right-24: RHEL Guides](rhel/index.md)

-   :material-frequently-asked-questions:{ .lg .middle } __Q&A__

    ---

    Common issues, solutions, and best practices

    [:octicons-arrow-right-24: Browse Q&A](qa/index.md)

-   :material-file-document-multiple:{ .lg .middle } __Cheatsheets__

    ---

    Quick reference guides for everyday tasks

    [:octicons-arrow-right-24: View Cheatsheets](cheatsheets/nmcli-cheatsheet.md)

</div>

---

## 🔍 Search Tips

Use the search bar above to find:

- **Commands**: `nmcli`, `ip addr`, `firewall-cmd`
- **Topics**: `static ip`, `dns`, `routing`
- **Error messages**: Copy/paste exact error text
- **Tags**: `#troubleshooting`, `#configuration`

---

## 📚 Popular Topics

### Most Referenced Commands

- [nmcli - NetworkManager CLI](networking/commands/nmcli.md)
- [ip command - Network Configuration](networking/commands/ip-command.md)
- [firewall-cmd - Firewall Management](networking/commands/firewall-cmd.md)
- [tcpdump - Packet Capture](networking/commands/tcpdump.md)

### Common Troubleshooting

- [Connectivity Issues](networking/troubleshooting/connectivity.md)
- [DNS Problems](networking/troubleshooting/dns-issues.md)
- [Network Performance](networking/troubleshooting/performance.md)

### RHEL Configuration

- [Network Configuration](rhel/configuration/network-config.md)
- [Firewall Setup](rhel/configuration/firewall.md)
- [SELinux Configuration](rhel/configuration/selinux.md)

---

## 🆕 Recent Updates

!!! tip "Latest Additions"
    - Added comprehensive nmcli guide
    - Updated troubleshooting workflows
    - New VLAN configuration examples

---

## 🏷️ Browse by Tag

Explore content by topic:

[View all tags →](tags.md){ .md-button .md-button--primary }

---

## 💡 About This KB

This knowledge base is my personal collection of:

- **Commands I use frequently** with real examples and outputs
- **Solutions to problems I've encountered** in production
- **Configuration snippets** that I've tested and verified
- **Troubleshooting workflows** that actually work

Everything here is:

- ✅ Tested on RHEL 8/9
- ✅ Based on real-world scenarios
- ✅ Continuously updated
- ✅ Searchable and organized

---

## 🚀 Quick Start Examples

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

## 📖 How to Use This KB

1. **Search First**: Use the search bar for quick answers
2. **Browse by Section**: Navigate using the top menu
3. **Check Tags**: Find related topics easily
4. **Copy Commands**: All code blocks have copy buttons
5. **Follow Examples**: Real outputs included for verification

---

## 🤝 Contributing

Found an error or want to improve something?

- This KB is version controlled with Git
- All changes are tracked and reviewed
- Feel free to suggest improvements

---

**Last Updated**: {Date}  
**Version**: 1.0
