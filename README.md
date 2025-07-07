# Kubernetes Setup Script

A comprehensive bash script to set up a single-node Kubernetes cluster on Ubuntu 24.10 systems. This script automates the installation and configuration of all necessary components for running Kubernetes locally.

## 📋 Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [What Gets Installed](#what-gets-installed)
- [Configuration Details](#configuration-details)
- [Troubleshooting](#troubleshooting)
- [Security Considerations](#security-considerations)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

This project provides an automated setup script for Kubernetes on Ubuntu 24.10. It handles the complete installation process including:

- System updates and package installation
- Container runtime (containerd) setup
- Kubernetes repository configuration
- Core Kubernetes components installation
- Network and system configuration
- Kernel module loading

## ⚙️ Prerequisites

Before running the setup script, ensure your system meets these requirements:

### System Requirements
- **OS**: Ubuntu 24.10 (or compatible Ubuntu versions)
- **Architecture**: x86_64/amd64
- **RAM**: Minimum 2GB (4GB recommended)
- **Storage**: At least 20GB free space
- **CPU**: 2 cores minimum

### User Requirements
- Root or sudo access
- Internet connection for package downloads
- Basic familiarity with Linux command line

### Network Requirements
- Outbound internet access for package downloads
- Port 6443 available (for API server)
- Ports 10250, 10251, 10252 available (for kubelet)

## ✨ Features

- **Automated Installation**: One-command setup process
- **Latest Kubernetes**: Installs Kubernetes v1.32+
- **Systemd Integration**: Proper cgroup driver configuration
- **Network Ready**: Configures bridge networking and IP forwarding
- **Swap Disabled**: Automatically disables swap (Kubernetes requirement)
- **Version Locked**: Prevents accidental package upgrades
- **Error Handling**: Robust error checking and reporting

## 🚀 Installation

### Quick Start

1. **Clone or download the repository:**
   ```bash
   git clone <repository-url>
   cd github-repo-k8s
   ```

2. **Make the script executable:**
   ```bash
   chmod +x setup.sh
   ```

3. **Run the setup script:**
   ```bash
   sudo ./setup.sh
   ```

### Manual Download

If you prefer to download just the script:

```bash
curl -O https://raw.githubusercontent.com/your-username/github-repo-k8s/main/setup.sh
chmod +x setup.sh
sudo ./setup.sh
```

## 📖 Usage

### Running the Setup

The setup script will:

1. Update your system packages
2. Install and configure containerd
3. Add the official Kubernetes repository
4. Install kubelet, kubeadm, and kubectl
5. Configure system settings for Kubernetes
6. Disable swap and configure networking

### Post-Installation Steps

After the script completes, you'll need to initialize your cluster:

```bash
# Initialize the cluster (run as root or with sudo)
sudo kubeadm init --pod-network-cidr=10.244.0.0/16

# Set up kubectl for your user
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Install a CNI (Container Network Interface)
# Example with Flannel:
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```

### Verifying the Installation

Check that all components are working:

```bash
# Check node status
kubectl get nodes

# Check system pods
kubectl get pods --all-namespaces

# Check cluster info
kubectl cluster-info
```

## 🔧 What Gets Installed

### Core Components
- **containerd**: Container runtime
- **kubelet**: Node agent
- **kubeadm**: Cluster management tool
- **kubectl**: Kubernetes command-line tool

### System Configuration
- Kernel modules: `overlay`, `br_netfilter`
- Network settings: bridge networking, IP forwarding
- Swap disabled
- Systemd cgroup driver enabled

### Repository Setup
- Official Kubernetes repository (v1.32+)
- GPG key verification
- Package version locking

## ⚙️ Configuration Details

### Containerd Configuration
- Uses systemd as cgroup driver
- Default configuration with optimized settings
- Automatic restart after configuration

### Network Configuration
```bash
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
```

### Package Management
- Kubernetes packages are held to prevent automatic upgrades
- Uses official Kubernetes repository
- Includes GPG key verification

## 🔍 Troubleshooting

### Common Issues

#### 1. Permission Denied
```bash
# Ensure script is executable
chmod +x setup.sh

# Run with sudo
sudo ./setup.sh
```

#### 2. Network Issues
```bash
# Check if modules are loaded
lsmod | grep -E "(overlay|br_netfilter)"

# Manually load if needed
sudo modprobe overlay
sudo modprobe br_netfilter
```

#### 3. Swap Still Enabled
```bash
# Check swap status
swapon --show

# Disable manually if needed
sudo swapoff -a
```

#### 4. Containerd Issues
```bash
# Check containerd status
sudo systemctl status containerd

# Restart if needed
sudo systemctl restart containerd
```

#### 5. Package Installation Failures
```bash
# Update package lists
sudo apt update

# Clear apt cache
sudo apt clean
sudo apt autoclean
```

### Logs and Debugging

Check relevant logs for issues:

```bash
# Kubernetes logs
sudo journalctl -u kubelet

# Containerd logs
sudo journalctl -u containerd

# System logs
sudo dmesg | grep -i kube
```

## 🔒 Security Considerations

### General Security
- Script requires sudo privileges (necessary for system configuration)
- Uses official Kubernetes repositories with GPG verification
- Installs only necessary components

### Network Security
- Default Kubernetes ports may need firewall configuration
- Consider using a CNI with network policies
- Review and configure RBAC as needed

### Best Practices
- Run on dedicated systems when possible
- Keep system and Kubernetes components updated
- Use strong authentication for production deployments
- Consider using a proper CNI with security features

## 🤝 Contributing

We welcome contributions! Here's how you can help:

### Reporting Issues
- Use the GitHub issue tracker
- Include system information and error logs
- Provide steps to reproduce the issue

### Suggesting Improvements
- Open a feature request issue
- Describe the use case and benefits
- Consider backward compatibility

### Code Contributions
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 📞 Support

### Getting Help
- Check the [troubleshooting section](#troubleshooting)
- Review Kubernetes documentation
- Search existing issues

### Resources
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Ubuntu Server Guide](https://ubuntu.com/server/docs)
- [Containerd Documentation](https://containerd.io/docs/)

---

**Note**: This script is designed for development and testing environments. For production deployments, consider using managed Kubernetes services or following enterprise deployment guidelines.
