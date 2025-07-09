# Amazon EC2 x86 to Graviton Migration Guide

A comprehensive step-by-step guide for migrating from Amazon EC2 x86 instances to AWS Graviton-powered instances.

## Table of Contents

1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Understanding AWS Graviton](#understanding-aws-graviton)
4. [Price Comparison](#price-comparison)
5. [Instance Family Comparison](#instance-family-comparison)
6. [Pre-Migration Assessment](#pre-migration-assessment)
7. [Migration Planning](#migration-planning)
8. [Step-by-Step Migration Process](#step-by-step-migration-process)
9. [Testing and Validation](#testing-and-validation)
10. [Troubleshooting](#troubleshooting)
11. [Post-Migration Optimization](#post-migration-optimization)
12. [Additional Resources](#additional-resources)

## Overview

AWS Graviton processors are custom-built by AWS to deliver the best price performance for cloud workloads running on Amazon EC2. This guide provides a comprehensive approach to migrating from x86-based EC2 instances to Graviton-powered instances, helping you achieve up to 40% better price performance while maintaining or improving application performance.

### Key Benefits of Graviton Migration

- **Cost Savings**: Up to 40% better price performance compared to x86 instances
- **Performance**: Improved performance for many workloads, especially compute-intensive tasks
- **Sustainability**: More energy-efficient processors reducing environmental impact
- **Innovation**: Access to latest ARM-based architecture optimizations

### Migration Scope

This guide covers:
- General purpose workloads (web servers, microservices, development environments)
- Compute-optimized workloads (CPU-intensive applications)
- Memory-optimized workloads (in-memory databases, real-time analytics)
- Container workloads (Docker, Kubernetes)
- Serverless functions (AWS Lambda)

**Important Note**: Graviton instances only support Linux operating systems. Windows workloads cannot be migrated to Graviton instances.

## Prerequisites

### Technical Requirements

1. **Operating System Compatibility**
   - Linux-based workloads only
   - Supported distributions: Amazon Linux 2, Ubuntu, RHEL, SUSE, Debian
   - ARM64 architecture support required

2. **Application Compatibility**
   - Applications must support ARM64 architecture
   - Dependencies and libraries must be available for ARM64
   - No Windows-specific dependencies

3. **AWS Account Requirements**
   - Active AWS account with EC2 access
   - Appropriate IAM permissions for instance management
   - Access to target AWS regions (Graviton available in most regions)

### Skills and Knowledge

1. **Technical Expertise**
   - Basic understanding of EC2 instance management
   - Linux system administration knowledge
   - Application deployment and configuration experience
   - Understanding of your application architecture and dependencies

2. **Tools and Access**
   - AWS CLI configured
   - SSH access to existing instances
   - Application monitoring and testing tools
   - Backup and recovery procedures in place

### Pre-Migration Checklist

- [ ] Inventory current x86 instances and their specifications
- [ ] Identify applications and their dependencies
- [ ] Verify ARM64 compatibility for all software components
- [ ] Plan testing and validation procedures
- [ ] Establish rollback procedures
- [ ] Schedule migration windows
- [ ] Notify stakeholders and users

## Understanding AWS Graviton

### Graviton Processor Generations

#### Graviton (1st Generation)
- **Instance Types**: A1 family
- **Use Cases**: Scale-out workloads
- **Status**: Previous generation, limited availability

#### Graviton2 (2nd Generation)
- **Instance Types**: M6g, C6g, R6g, T4g, X2gd, I4g, Im4gn, Is4gen
- **Performance**: Up to 40% better price performance vs x86
- **Features**: 64-bit ARM Neoverse-N1 cores, custom silicon
- **Memory**: DDR4 support

#### Graviton3 (3rd Generation)
- **Instance Types**: M7g, C7g, R7g, Hpc7g
- **Performance**: Up to 25% better performance vs Graviton2
- **Features**: ARM Neoverse-V1 cores, DDR5 memory support
- **Specialized**: Enhanced ML, cryptographic, and floating-point performance

#### Graviton4 (4th Generation)
- **Instance Types**: R8g, M8g, C8g (newest generation)
- **Performance**: Latest ARM architecture improvements
- **Features**: Enhanced performance and efficiency

### Architecture Differences

#### x86 vs ARM64 Key Differences

| Aspect | x86 (Intel/AMD) | ARM64 (Graviton) |
|--------|-----------------|------------------|
| **vCPU Definition** | Logical core (hyperthreaded) | Physical core |
| **Instruction Set** | CISC (Complex) | RISC (Reduced) |
| **Power Efficiency** | Standard | Higher efficiency |
| **Memory Access** | Traditional | Optimized for cloud workloads |
| **Software Ecosystem** | Mature, extensive | Growing, cloud-native focused |

#### Performance Characteristics

- **CPU Performance**: Graviton provides dedicated physical cores vs hyperthreaded logical cores
- **Memory Performance**: Graviton3+ includes DDR5 support for higher bandwidth
- **Network Performance**: Enhanced networking capabilities with SR-IOV
- **Storage Performance**: Optimized for NVMe and EBS performance

## Price Comparison

### Cost Savings Overview

AWS Graviton instances typically provide 15-40% cost savings compared to equivalent x86 instances, while often delivering equal or better performance. The exact savings depend on the instance family and size.

### Sample Price Comparisons (US East - N. Virginia, On-Demand)

#### General Purpose Instances

| x86 Instance | Graviton Instance | vCPU | Memory (GB) | x86 Price/Hour | Graviton Price/Hour | Savings |
|--------------|-------------------|------|-------------|----------------|---------------------|---------|
| t3.micro | t4g.micro | 2 | 1 | $0.0104 | $0.0084 | 19.2% |
| t3.small | t4g.small | 2 | 2 | $0.0208 | $0.0168 | 19.2% |
| t3.medium | t4g.medium | 2 | 4 | $0.0416 | $0.0336 | 19.2% |
| t3.large | t4g.large | 2 | 8 | $0.0832 | $0.0672 | 19.2% |
| t3.xlarge | t4g.xlarge | 4 | 16 | $0.1664 | $0.1344 | 19.2% |
| m5.large | m6g.large | 2 | 8 | $0.096 | $0.077 | 19.8% |
| m5.xlarge | m6g.xlarge | 4 | 16 | $0.192 | $0.154 | 19.8% |
| m5.2xlarge | m6g.2xlarge | 8 | 32 | $0.384 | $0.308 | 19.8% |

#### Compute Optimized Instances

| x86 Instance | Graviton Instance | vCPU | Memory (GB) | x86 Price/Hour | Graviton Price/Hour | Savings |
|--------------|-------------------|------|-------------|----------------|---------------------|---------|
| c5.large | c6g.large | 2 | 4 | $0.085 | $0.068 | 20.0% |
| c5.xlarge | c6g.xlarge | 4 | 8 | $0.17 | $0.136 | 20.0% |
| c5.2xlarge | c6g.2xlarge | 8 | 16 | $0.34 | $0.272 | 20.0% |
| c5.4xlarge | c6g.4xlarge | 16 | 32 | $0.68 | $0.544 | 20.0% |
| c6i.4xlarge | c7g.4xlarge | 16 | 32 | $0.6800 | $0.5781 | 15.0% |

#### Memory Optimized Instances

| x86 Instance | Graviton Instance | vCPU | Memory (GB) | x86 Price/Hour | Graviton Price/Hour | Savings |
|--------------|-------------------|------|-------------|----------------|---------------------|---------|
| r5.large | r6g.large | 2 | 16 | $0.126 | $0.1008 | 20.0% |
| r5.xlarge | r6g.xlarge | 4 | 32 | $0.252 | $0.2016 | 20.0% |
| r5.2xlarge | r6g.2xlarge | 8 | 64 | $0.504 | $0.4032 | 20.0% |
| r5.4xlarge | r6g.4xlarge | 16 | 128 | $1.008 | $0.8064 | 20.0% |

*Note: Prices are subject to change. Use the [AWS Pricing Calculator](https://calculator.aws) for current pricing.*

### Total Cost of Ownership (TCO) Considerations

#### Direct Cost Savings
- **Instance Costs**: 15-40% reduction in compute costs
- **Reserved Instance Savings**: Additional savings when combined with Reserved Instances
- **Spot Instance Savings**: Enhanced savings when using Spot Instances

#### Performance-Based Savings
- **Higher Efficiency**: Better performance per dollar spent
- **Reduced Instance Count**: Potentially fewer instances needed for same workload
- **Lower Operational Overhead**: Simplified management with better performance

#### Additional Cost Benefits
- **Energy Efficiency**: Lower power consumption reduces environmental costs
- **Sustainability Credits**: Some organizations benefit from sustainability reporting
- **Innovation Investment**: Access to latest ARM ecosystem developments

## Instance Family Comparison

### General Purpose Instances

#### T-Series (Burstable Performance)

| Feature | t3 (x86) | t4g (Graviton2) | Performance Notes |
|---------|----------|-----------------|-------------------|
| **Processor** | Intel Xeon Platinum | AWS Graviton2 | Graviton2 provides consistent performance |
| **vCPU** | 2-8 | 2-8 | Physical cores vs hyperthreaded |
| **Memory** | 0.5-32 GB | 0.5-32 GB | Same memory configurations |
| **Network** | Up to 5 Gbps | Up to 5 Gbps | Enhanced networking on Graviton |
| **EBS Bandwidth** | Up to 2,085 Mbps | Up to 2,085 Mbps | Optimized EBS performance |
| **Use Cases** | Web servers, dev/test | Web servers, dev/test | Better sustained performance |
| **Free Tier** | t3.micro eligible | t4g.small free until Dec 2025 | Extended free tier for Graviton |

#### M-Series (Balanced Compute, Memory, Networking)

| Feature | m5 (x86) | m6g (Graviton2) | m7g (Graviton3) | Performance Notes |
|---------|----------|-----------------|-----------------|-------------------|
| **Processor** | Intel Xeon Platinum | AWS Graviton2 | AWS Graviton3 | Latest generation advantages |
| **vCPU** | 2-96 | 1-64 | 1-64 | Physical cores provide better consistency |
| **Memory** | 8-384 GB | 4-256 GB | 4-256 GB | DDR5 support in m7g |
| **Network** | Up to 25 Gbps | Up to 25 Gbps | Up to 50 Gbps | Enhanced networking in m7g |
| **EBS Bandwidth** | Up to 19,000 Mbps | Up to 19,000 Mbps | Up to 40,000 Mbps | Significantly improved in m7g |
| **Use Cases** | Web apps, microservices | Web apps, microservices | High-performance web apps | Better price-performance ratio |

### Compute Optimized Instances

#### C-Series (High-Performance Processors)

| Feature | c5 (x86) | c6g (Graviton2) | c7g (Graviton3) | Performance Notes |
|---------|----------|-----------------|-----------------|-------------------|
| **Processor** | Intel Xeon Platinum | AWS Graviton2 | AWS Graviton3 | ARM architecture optimizations |
| **vCPU** | 2-96 | 1-64 | 1-64 | Dedicated physical cores |
| **Memory** | 4-192 GB | 2-128 GB | 2-128 GB | DDR5 in c7g for higher bandwidth |
| **Network** | Up to 25 Gbps | Up to 25 Gbps | Up to 50 Gbps | Enhanced networking capabilities |
| **EBS Bandwidth** | Up to 19,000 Mbps | Up to 19,000 Mbps | Up to 40,000 Mbps | Improved storage performance |
| **Use Cases** | HPC, scientific computing | HPC, scientific computing | CPU-intensive workloads | Better floating-point performance |

### Memory Optimized Instances

#### R-Series (Memory Optimized)

| Feature | r5 (x86) | r6g (Graviton2) | r7g (Graviton3) | Performance Notes |
|---------|----------|-----------------|-----------------|-------------------|
| **Processor** | Intel Xeon Platinum | AWS Graviton2 | AWS Graviton3 | Enhanced memory subsystem |
| **vCPU** | 2-96 | 1-64 | 1-64 | Physical cores for consistent performance |
| **Memory** | 16-768 GB | 8-512 GB | 8-512 GB | DDR5 support in r7g |
| **Memory:vCPU Ratio** | 8:1 | 8:1 | 8:1 | Consistent memory ratios |
| **Network** | Up to 25 Gbps | Up to 25 Gbps | Up to 50 Gbps | Improved network performance |
| **EBS Bandwidth** | Up to 19,000 Mbps | Up to 19,000 Mbps | Up to 40,000 Mbps | Enhanced storage throughput |
| **Use Cases** | In-memory databases | In-memory databases | Real-time analytics | Better memory bandwidth |

### Storage Optimized Instances

#### I-Series (High Sequential Read/Write, Low Latency)

| Feature | i3 (x86) | i4g (Graviton2) | Performance Notes |
|---------|----------|-----------------|-------------------|
| **Processor** | Intel Xeon E5 | AWS Graviton2 | ARM optimization for storage workloads |
| **vCPU** | 2-72 | 2-64 | Physical cores for consistent I/O |
| **Memory** | 15.25-488 GB | 12-384 GB | Optimized memory configuration |
| **Instance Storage** | NVMe SSD | NVMe SSD | Enhanced NVMe performance |
| **Network** | Up to 25 Gbps | Up to 50 Gbps | Improved network throughput |
| **Use Cases** | Distributed file systems | Distributed file systems | Better I/O performance per dollar |

### Accelerated Computing Instances

#### G-Series (GPU Instances with Graviton)

| Feature | g4dn (x86) | g5g (Graviton2) | Performance Notes |
|---------|------------|-----------------|-------------------|
| **Processor** | Intel Xeon Platinum | AWS Graviton2 | ARM + GPU combination |
| **GPU** | NVIDIA T4 | NVIDIA T4G | Optimized GPU-CPU communication |
| **vCPU** | 4-96 | 4-64 | Physical ARM cores |
| **Memory** | 16-384 GB | 16-256 GB | Balanced CPU-GPU memory |
| **Use Cases** | ML inference, graphics | ML inference, graphics | Better price-performance for inference |

### Instance Selection Guidelines

#### Choose Graviton When:
- Running Linux-based workloads
- Cost optimization is a priority
- Applications support ARM64 architecture
- Workloads benefit from consistent CPU performance
- Environmental sustainability is important

#### Consider x86 When:
- Running Windows workloads
- Legacy applications with x86 dependencies
- Specialized software without ARM64 support
- Existing licensing tied to x86 architecture
- Short-term deployments where migration effort isn't justified

#### Performance Expectations:
- **CPU-bound workloads**: 10-40% better performance per dollar
- **Memory-intensive workloads**: Consistent performance with cost savings
- **Network-intensive workloads**: Enhanced networking capabilities
- **Storage-intensive workloads**: Improved I/O performance
- **Mixed workloads**: Generally better price-performance ratio
## Pre-Migration Assessment

### Application Compatibility Assessment

#### 1. Architecture Analysis

**Identify Current Architecture:**
```bash
# Check current architecture
uname -m
# Output: x86_64 (current) -> target: aarch64

# Check processor information
lscpu | grep Architecture
# Architecture: x86_64 -> target: aarch64

# List installed packages and their architectures
dpkg --print-architecture  # Debian/Ubuntu
rpm -qa --queryformat '%{NAME}-%{VERSION}-%{RELEASE}.%{ARCH}\n' | grep x86_64  # RHEL/CentOS
```

**Application Dependencies Audit:**
```bash
# Check for x86-specific binaries
find /usr/bin /usr/local/bin -type f -exec file {} \; | grep "x86-64"

# Check shared libraries
ldd /path/to/your/application | grep x86

# Check for architecture-specific paths
ls -la /usr/lib/x86_64-linux-gnu/  # Ubuntu/Debian
ls -la /usr/lib64/  # RHEL/CentOS
```

#### 2. Software Compatibility Matrix

| Software Category | Compatibility Status | Migration Notes |
|-------------------|---------------------|-----------------|
| **Operating Systems** | ✅ Full Support | Amazon Linux 2, Ubuntu 18.04+, RHEL 8+, SUSE 15+ |
| **Web Servers** | ✅ Full Support | Apache, Nginx, Lighttpd |
| **Databases** | ✅ Full Support | MySQL, PostgreSQL, MongoDB, Redis |
| **Programming Languages** | ✅ Full Support | Python, Java, Node.js, Go, Rust |
| **Container Runtimes** | ✅ Full Support | Docker, containerd, Podman |
| **Orchestration** | ✅ Full Support | Kubernetes, Docker Swarm |
| **Monitoring Tools** | ✅ Full Support | Prometheus, Grafana, DataDog, New Relic |
| **CI/CD Tools** | ✅ Full Support | Jenkins, GitLab CI, GitHub Actions |
| **Legacy Applications** | ⚠️ Case-by-case | Requires individual assessment |

#### 3. Dependency Verification Script

Create a script to check ARM64 package availability:

```bash
#!/bin/bash
# check_arm64_compatibility.sh

echo "=== ARM64 Compatibility Check ==="

# Check package manager
if command -v apt &> /dev/null; then
    PKG_MGR="apt"
    echo "Package Manager: APT (Debian/Ubuntu)"
elif command -v yum &> /dev/null; then
    PKG_MGR="yum"
    echo "Package Manager: YUM (RHEL/CentOS)"
elif command -v dnf &> /dev/null; then
    PKG_MGR="dnf"
    echo "Package Manager: DNF (Fedora/RHEL 8+)"
fi

# List installed packages
echo -e "\n=== Checking installed packages for ARM64 availability ==="

case $PKG_MGR in
    "apt")
        dpkg -l | awk '{print $2}' | while read package; do
            if apt-cache madison "$package" 2>/dev/null | grep -q arm64; then
                echo "✅ $package - ARM64 available"
            else
                echo "❌ $package - ARM64 not found"
            fi
        done
        ;;
    "yum"|"dnf")
        rpm -qa --queryformat '%{NAME}\n' | while read package; do
            if $PKG_MGR search "$package" --arch=aarch64 &>/dev/null; then
                echo "✅ $package - ARM64 available"
            else
                echo "❌ $package - ARM64 not found"
            fi
        done
        ;;
esac

echo -e "\n=== Architecture-specific files check ==="
find /usr/bin /usr/local/bin -type f -exec file {} \; 2>/dev/null | grep -E "(x86-64|i386)" | head -10
```

### Performance Baseline Establishment

#### 1. System Performance Metrics

**CPU Performance Baseline:**
```bash
# Install sysbench for benchmarking
sudo apt-get install sysbench  # Ubuntu/Debian
sudo yum install sysbench      # RHEL/CentOS

# CPU benchmark
sysbench cpu --cpu-max-prime=20000 --threads=4 run

# Memory benchmark
sysbench memory --memory-total-size=10G run

# File I/O benchmark
sysbench fileio --file-total-size=2G prepare
sysbench fileio --file-total-size=2G --file-test-mode=rndrw run
sysbench fileio --file-total-size=2G cleanup
```

**Application Performance Baseline:**
```bash
# Web application response time
curl -w "@curl-format.txt" -o /dev/null -s "http://your-app-url"

# Database query performance
mysql -e "SELECT BENCHMARK(1000000, MD5('test'));"

# Custom application metrics
# Document current throughput, latency, resource utilization
```

#### 2. Resource Utilization Analysis

**Monitor Current Usage:**
```bash
# CPU utilization over time
sar -u 1 60 > cpu_baseline.txt

# Memory usage
free -h && cat /proc/meminfo > memory_baseline.txt

# Disk I/O
iostat -x 1 60 > disk_baseline.txt

# Network utilization
sar -n DEV 1 60 > network_baseline.txt
```

### Risk Assessment

#### High Risk Factors
- **Proprietary x86-only software**: Commercial applications without ARM64 versions
- **Legacy compiled binaries**: Old applications without source code
- **Hardware-specific optimizations**: Applications tuned for specific x86 features
- **Complex dependency chains**: Applications with deep x86-specific dependencies

#### Medium Risk Factors
- **Custom compiled software**: Applications that need recompilation
- **Performance-critical workloads**: Applications sensitive to performance changes
- **Third-party integrations**: External services or APIs with architecture dependencies
- **Licensing constraints**: Software licenses tied to specific architectures

#### Low Risk Factors
- **Standard web applications**: Applications using common frameworks
- **Containerized workloads**: Applications already running in containers
- **Interpreted languages**: Python, Node.js, Ruby applications
- **Cloud-native applications**: Applications designed for cloud environments

## Migration Planning

### Migration Strategy Selection

#### 1. Big Bang Migration
**When to Use:**
- Small number of instances (< 10)
- Non-critical development/test environments
- Applications with minimal dependencies
- Short maintenance windows acceptable

**Pros:**
- Quick completion
- Simplified process
- Immediate cost benefits

**Cons:**
- Higher risk
- Potential for extended downtime
- Limited rollback options

#### 2. Phased Migration
**When to Use:**
- Large number of instances
- Production environments
- Complex application architectures
- Risk-averse organizations

**Phases:**
1. **Phase 1**: Development and test environments
2. **Phase 2**: Non-critical production workloads
3. **Phase 3**: Critical production workloads
4. **Phase 4**: Legacy and complex applications

#### 3. Blue-Green Migration
**When to Use:**
- Zero-downtime requirements
- Critical production systems
- Applications with complex state management
- Environments with strict SLAs

**Process:**
1. Build parallel Graviton environment (Green)
2. Test and validate Green environment
3. Switch traffic from x86 (Blue) to Graviton (Green)
4. Monitor and validate
5. Decommission Blue environment

#### 4. Canary Migration
**When to Use:**
- High-traffic applications
- Gradual risk mitigation needed
- Performance validation required
- User experience monitoring critical

**Process:**
1. Deploy small percentage of traffic to Graviton
2. Monitor performance and errors
3. Gradually increase traffic percentage
4. Complete migration when validated

### Migration Timeline Template

#### Week 1-2: Assessment and Planning
- [ ] Complete compatibility assessment
- [ ] Establish performance baselines
- [ ] Create migration plan
- [ ] Set up testing environments
- [ ] Prepare rollback procedures

#### Week 3-4: Development and Testing
- [ ] Build ARM64 versions of applications
- [ ] Set up Graviton test instances
- [ ] Perform functional testing
- [ ] Conduct performance testing
- [ ] Validate monitoring and alerting

#### Week 5-6: Staging Migration
- [ ] Migrate staging environments
- [ ] End-to-end testing
- [ ] Load testing
- [ ] Security testing
- [ ] Documentation updates

#### Week 7-8: Production Migration
- [ ] Execute production migration
- [ ] Monitor performance and stability
- [ ] Validate cost savings
- [ ] Update operational procedures
- [ ] Conduct post-migration review

### Resource Planning

#### Instance Sizing Guidelines

**General Sizing Approach:**
- Start with equivalent vCPU and memory configurations
- Monitor performance and adjust as needed
- Consider that Graviton vCPUs are physical cores

**Sizing Conversion Examples:**
```
x86 Instance -> Graviton Instance (Starting Point)
t3.medium    -> t4g.medium    (2 vCPU, 4 GB)
m5.large     -> m6g.large     (2 vCPU, 8 GB)
c5.xlarge    -> c6g.xlarge    (4 vCPU, 8 GB)
r5.2xlarge   -> r6g.2xlarge   (8 vCPU, 64 GB)
```

**Performance-Based Adjustments:**
- If CPU-bound: Consider smaller Graviton instance due to physical cores
- If memory-bound: Maintain same memory configuration
- If I/O-bound: Consider newer generation for enhanced performance

## Step-by-Step Migration Process

### Phase 1: Environment Preparation

#### 1. Set Up Migration Environment

**Create Migration Workspace:**
```bash
# Create directory structure
mkdir -p ~/graviton-migration/{scripts,configs,backups,logs}
cd ~/graviton-migration

# Set environment variables
export MIGRATION_DATE=$(date +%Y%m%d)
export SOURCE_REGION="us-east-1"
export TARGET_REGION="us-east-1"
export PROJECT_NAME="graviton-migration"
```

**Install Required Tools:**
```bash
# Install AWS CLI v2
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Install Session Manager plugin
curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/ubuntu_64bit/session-manager-plugin.deb" -o "session-manager-plugin.deb"
sudo dpkg -i session-manager-plugin.deb

# Verify installations
aws --version
session-manager-plugin
```

#### 2. Inventory Current Infrastructure

**Create Instance Inventory:**
```bash
#!/bin/bash
# inventory_instances.sh

echo "=== EC2 Instance Inventory ===" > inventory.txt
echo "Date: $(date)" >> inventory.txt
echo "" >> inventory.txt

aws ec2 describe-instances \
    --query 'Reservations[*].Instances[*].[InstanceId,InstanceType,State.Name,Platform,Architecture,LaunchTime,Tags[?Key==`Name`].Value|[0]]' \
    --output table >> inventory.txt

echo "" >> inventory.txt
echo "=== Instance Type Summary ===" >> inventory.txt
aws ec2 describe-instances \
    --query 'Reservations[*].Instances[*].InstanceType' \
    --output text | sort | uniq -c >> inventory.txt
```

**Document Application Architecture:**
```bash
# Create application mapping
cat > application_mapping.yaml << EOF
applications:
  web-frontend:
    instances:
      - i-1234567890abcdef0
      - i-0987654321fedcba0
    instance_type: t3.medium
    target_type: t4g.medium
    dependencies:
      - web-backend
      - database
    
  web-backend:
    instances:
      - i-abcdef1234567890
    instance_type: m5.large
    target_type: m6g.large
    dependencies:
      - database
      - cache
    
  database:
    instances:
      - i-fedcba0987654321
    instance_type: r5.xlarge
    target_type: r6g.xlarge
    dependencies: []
EOF
```

### Phase 2: Application Preparation

#### 1. Build ARM64 Compatible Applications

**For Containerized Applications:**
```bash
# Multi-architecture Docker build
docker buildx create --name multiarch --use
docker buildx build --platform linux/amd64,linux/arm64 -t myapp:latest --push .

# Verify ARM64 image
docker manifest inspect myapp:latest
```

**For Compiled Applications:**
```bash
# Cross-compile for ARM64 (example with Go)
GOOS=linux GOARCH=arm64 go build -o myapp-arm64 main.go

# Cross-compile for ARM64 (example with C/C++)
aarch64-linux-gnu-gcc -o myapp-arm64 main.c
```

**For Interpreted Languages:**
```bash
# Python - ensure all dependencies support ARM64
pip install --platform linux_aarch64 --only-binary=:all: -r requirements.txt --dry-run

# Node.js - check for native modules
npm ls --depth=0 | grep -E "(native|binding|addon)"
```

#### 2. Create ARM64 AMIs

**Using EC2 Image Builder:**
```bash
# Create Image Builder pipeline for ARM64
aws imagebuilder create-image-pipeline \
    --name "graviton-migration-pipeline" \
    --image-recipe-arn "arn:aws:imagebuilder:region:account:image-recipe/graviton-recipe/1.0.0" \
    --infrastructure-configuration-arn "arn:aws:imagebuilder:region:account:infrastructure-configuration/graviton-infra" \
    --distribution-configuration-arn "arn:aws:imagebuilder:region:account:distribution-configuration/graviton-dist"
```

**Manual AMI Creation:**
```bash
# Launch ARM64 instance for AMI creation
aws ec2 run-instances \
    --image-id ami-0abcdef1234567890 \
    --instance-type t4g.micro \
    --key-name my-key-pair \
    --security-group-ids sg-12345678 \
    --subnet-id subnet-12345678 \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=graviton-ami-builder}]'

# Connect and configure the instance
aws ssm start-session --target i-1234567890abcdef0

# Create AMI from configured instance
aws ec2 create-image \
    --instance-id i-1234567890abcdef0 \
    --name "my-app-graviton-$(date +%Y%m%d)" \
    --description "ARM64 version of my application"
```

### Phase 3: Testing Environment Setup

#### 1. Create Graviton Test Instances

**Launch Test Instance:**
```bash
#!/bin/bash
# launch_test_instance.sh

INSTANCE_TYPE="t4g.medium"
AMI_ID="ami-0abcdef1234567890"  # ARM64 AMI
KEY_NAME="my-key-pair"
SECURITY_GROUP="sg-12345678"
SUBNET_ID="subnet-12345678"

aws ec2 run-instances \
    --image-id $AMI_ID \
    --instance-type $INSTANCE_TYPE \
    --key-name $KEY_NAME \
    --security-group-ids $SECURITY_GROUP \
    --subnet-id $SUBNET_ID \
    --tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=graviton-test-$(date +%Y%m%d)},{Key=Environment,Value=test},{Key=Migration,Value=graviton}]" \
    --user-data file://user-data.sh
```

**User Data Script for Initial Setup:**
```bash
#!/bin/bash
# user-data.sh

# Update system
yum update -y

# Install monitoring tools
yum install -y htop iotop sysstat

# Install application dependencies
# Add your specific installation commands here

# Configure monitoring
cat > /etc/cron.d/performance-monitoring << EOF
*/5 * * * * root sar -u 1 1 >> /var/log/cpu-usage.log
*/5 * * * * root free -h >> /var/log/memory-usage.log
EOF

# Start services
systemctl enable --now sysstat
```

#### 2. Deploy and Configure Applications

**Application Deployment Script:**
```bash
#!/bin/bash
# deploy_application.sh

INSTANCE_ID=$1
APPLICATION_NAME=$2

echo "Deploying $APPLICATION_NAME to $INSTANCE_ID"

# Copy application files
aws ssm send-command \
    --instance-ids $INSTANCE_ID \
    --document-name "AWS-RunShellScript" \
    --parameters 'commands=[
        "cd /opt",
        "wget https://releases.example.com/'$APPLICATION_NAME'-arm64.tar.gz",
        "tar -xzf '$APPLICATION_NAME'-arm64.tar.gz",
        "chown -R app:app '$APPLICATION_NAME'",
        "systemctl enable '$APPLICATION_NAME'",
        "systemctl start '$APPLICATION_NAME'"
    ]'

# Verify deployment
sleep 30
aws ssm send-command \
    --instance-ids $INSTANCE_ID \
    --document-name "AWS-RunShellScript" \
    --parameters 'commands=[
        "systemctl status '$APPLICATION_NAME'",
        "curl -f http://localhost:8080/health || echo \"Health check failed\""
    ]'
```

### Phase 4: Performance Testing and Validation

#### 1. Automated Performance Testing

**Performance Test Suite:**
```bash
#!/bin/bash
# performance_test_suite.sh

GRAVITON_INSTANCE=$1
X86_INSTANCE=$2
TEST_DURATION=300  # 5 minutes

echo "=== Performance Comparison Test ==="
echo "Graviton Instance: $GRAVITON_INSTANCE"
echo "x86 Instance: $X86_INSTANCE"
echo "Test Duration: $TEST_DURATION seconds"

# Function to run tests on an instance
run_performance_test() {
    local instance_id=$1
    local test_name=$2
    
    echo "Running $test_name on $instance_id"
    
    aws ssm send-command \
        --instance-ids $instance_id \
        --document-name "AWS-RunShellScript" \
        --parameters "commands=[
            'echo \"=== CPU Test ===\" > /tmp/perf_results.txt',
            'sysbench cpu --cpu-max-prime=20000 --threads=4 run >> /tmp/perf_results.txt',
            'echo \"=== Memory Test ===\" >> /tmp/perf_results.txt',
            'sysbench memory --memory-total-size=1G run >> /tmp/perf_results.txt',
            'echo \"=== Application Load Test ===\" >> /tmp/perf_results.txt',
            'ab -n 1000 -c 10 http://localhost:8080/ >> /tmp/perf_results.txt'
        ]" \
        --output text
}

# Run tests on both instances
run_performance_test $GRAVITON_INSTANCE "Graviton"
run_performance_test $X86_INSTANCE "x86"

# Wait for tests to complete
sleep $((TEST_DURATION + 60))

# Collect results
echo "Collecting results..."
aws ssm send-command \
    --instance-ids $GRAVITON_INSTANCE \
    --document-name "AWS-RunShellScript" \
    --parameters 'commands=["cat /tmp/perf_results.txt"]' \
    --output text > graviton_results.txt

aws ssm send-command \
    --instance-ids $X86_INSTANCE \
    --document-name "AWS-RunShellScript" \
    --parameters 'commands=["cat /tmp/perf_results.txt"]' \
    --output text > x86_results.txt

echo "Performance test completed. Results saved to graviton_results.txt and x86_results.txt"
```

#### 2. Application-Specific Testing

**Web Application Load Testing:**
```bash
# Install Apache Bench and wrk
sudo apt-get install apache2-utils wrk

# Basic load test
ab -n 10000 -c 100 http://graviton-instance:8080/

# Advanced load test with wrk
wrk -t12 -c400 -d30s --latency http://graviton-instance:8080/

# Database performance test
sysbench oltp_read_write \
    --mysql-host=graviton-db-instance \
    --mysql-user=testuser \
    --mysql-password=testpass \
    --mysql-db=testdb \
    --tables=10 \
    --table-size=100000 \
    prepare

sysbench oltp_read_write \
    --mysql-host=graviton-db-instance \
    --mysql-user=testuser \
    --mysql-password=testpass \
    --mysql-db=testdb \
    --tables=10 \
    --table-size=100000 \
    --threads=16 \
    --time=300 \
    run
```
## Testing and Validation

### Functional Testing Checklist

#### Application Functionality
- [ ] **Core Features**: All primary application features work correctly
- [ ] **API Endpoints**: All REST/GraphQL endpoints respond correctly
- [ ] **Database Operations**: CRUD operations function properly
- [ ] **File Operations**: File upload/download and processing work
- [ ] **Authentication**: Login/logout and session management work
- [ ] **Authorization**: Role-based access controls function correctly
- [ ] **Integrations**: Third-party service integrations work
- [ ] **Scheduled Jobs**: Cron jobs and background tasks execute

#### Performance Validation
- [ ] **Response Times**: Application response times meet SLA requirements
- [ ] **Throughput**: Request handling capacity meets or exceeds baseline
- [ ] **Resource Utilization**: CPU, memory, and I/O usage within acceptable ranges
- [ ] **Concurrent Users**: Application handles expected concurrent load
- [ ] **Database Performance**: Query execution times within acceptable limits
- [ ] **Cache Performance**: Caching mechanisms work effectively

#### System Integration Testing
- [ ] **Load Balancer**: Traffic distribution works correctly
- [ ] **Auto Scaling**: Scaling policies trigger appropriately
- [ ] **Monitoring**: All metrics and alerts function correctly
- [ ] **Logging**: Application and system logs are captured properly
- [ ] **Backup/Restore**: Data backup and recovery procedures work
- [ ] **Security**: Security controls and compliance requirements met

### Performance Benchmarking

#### Comprehensive Performance Test Script
```bash
#!/bin/bash
# comprehensive_performance_test.sh

GRAVITON_HOST=$1
X86_HOST=$2
TEST_RESULTS_DIR="./test_results_$(date +%Y%m%d_%H%M%S)"

mkdir -p $TEST_RESULTS_DIR

echo "=== Comprehensive Performance Comparison ==="
echo "Graviton Host: $GRAVITON_HOST"
echo "x86 Host: $X86_HOST"
echo "Results Directory: $TEST_RESULTS_DIR"

# Function to run system benchmarks
run_system_benchmarks() {
    local host=$1
    local arch=$2
    local result_file="$TEST_RESULTS_DIR/${arch}_system_benchmark.txt"
    
    echo "Running system benchmarks on $arch ($host)"
    
    ssh $host << 'EOF' > $result_file
echo "=== System Information ==="
uname -a
lscpu
free -h
df -h

echo -e "\n=== CPU Benchmark ==="
sysbench cpu --cpu-max-prime=20000 --threads=$(nproc) run

echo -e "\n=== Memory Benchmark ==="
sysbench memory --memory-total-size=10G --memory-oper=write run
sysbench memory --memory-total-size=10G --memory-oper=read run

echo -e "\n=== File I/O Benchmark ==="
sysbench fileio --file-total-size=2G prepare
sysbench fileio --file-total-size=2G --file-test-mode=rndrw --time=60 run
sysbench fileio cleanup

echo -e "\n=== Network Benchmark ==="
iperf3 -s -D  # Start server in background
sleep 2
iperf3 -c localhost -t 30
pkill iperf3
EOF
}

# Function to run application benchmarks
run_application_benchmarks() {
    local host=$1
    local arch=$2
    local result_file="$TEST_RESULTS_DIR/${arch}_application_benchmark.txt"
    
    echo "Running application benchmarks on $arch ($host)"
    
    # Web application load test
    echo "=== Web Application Load Test ===" > $result_file
    ab -n 10000 -c 100 http://$host:8080/ >> $result_file 2>&1
    
    echo -e "\n=== Advanced Load Test ===" >> $result_file
    wrk -t12 -c400 -d60s --latency http://$host:8080/ >> $result_file 2>&1
    
    # Database benchmark (if applicable)
    if nc -z $host 3306; then
        echo -e "\n=== Database Benchmark ===" >> $result_file
        sysbench oltp_read_write \
            --mysql-host=$host \
            --mysql-user=testuser \
            --mysql-password=testpass \
            --mysql-db=testdb \
            --threads=16 \
            --time=60 \
            run >> $result_file 2>&1
    fi
}

# Run benchmarks
run_system_benchmarks $GRAVITON_HOST "graviton"
run_system_benchmarks $X86_HOST "x86"

run_application_benchmarks $GRAVITON_HOST "graviton"
run_application_benchmarks $X86_HOST "x86"

# Generate comparison report
python3 << EOF > $TEST_RESULTS_DIR/comparison_report.txt
import re
import os

def parse_benchmark_results(file_path):
    if not os.path.exists(file_path):
        return {}
    
    with open(file_path, 'r') as f:
        content = f.read()
    
    results = {}
    
    # Parse CPU benchmark
    cpu_match = re.search(r'events per second:\s+(\d+\.\d+)', content)
    if cpu_match:
        results['cpu_events_per_sec'] = float(cpu_match.group(1))
    
    # Parse memory benchmark
    mem_match = re.search(r'(\d+\.\d+) MiB/sec', content)
    if mem_match:
        results['memory_throughput'] = float(mem_match.group(1))
    
    # Parse web server benchmark
    rps_match = re.search(r'Requests per second:\s+(\d+\.\d+)', content)
    if rps_match:
        results['requests_per_sec'] = float(rps_match.group(1))
    
    return results

graviton_results = parse_benchmark_results('$TEST_RESULTS_DIR/graviton_system_benchmark.txt')
x86_results = parse_benchmark_results('$TEST_RESULTS_DIR/x86_system_benchmark.txt')

print("=== Performance Comparison Report ===")
print(f"Generated: $(date)")
print()

for metric in graviton_results:
    if metric in x86_results:
        graviton_val = graviton_results[metric]
        x86_val = x86_results[metric]
        improvement = ((graviton_val - x86_val) / x86_val) * 100
        print(f"{metric}:")
        print(f"  Graviton: {graviton_val:.2f}")
        print(f"  x86:      {x86_val:.2f}")
        print(f"  Change:   {improvement:+.1f}%")
        print()
EOF

echo "Performance testing completed. Results available in: $TEST_RESULTS_DIR"
```

### Monitoring and Alerting Validation

#### CloudWatch Metrics Verification
```bash
#!/bin/bash
# verify_monitoring.sh

GRAVITON_INSTANCE_ID=$1

echo "Verifying CloudWatch metrics for instance: $GRAVITON_INSTANCE_ID"

# Check basic EC2 metrics
aws cloudwatch get-metric-statistics \
    --namespace AWS/EC2 \
    --metric-name CPUUtilization \
    --dimensions Name=InstanceId,Value=$GRAVITON_INSTANCE_ID \
    --start-time $(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%S) \
    --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
    --period 300 \
    --statistics Average

# Check custom application metrics
aws cloudwatch get-metric-statistics \
    --namespace Custom/Application \
    --metric-name ResponseTime \
    --dimensions Name=InstanceId,Value=$GRAVITON_INSTANCE_ID \
    --start-time $(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%S) \
    --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
    --period 300 \
    --statistics Average

# Verify alarms
aws cloudwatch describe-alarms \
    --alarm-names "High-CPU-$GRAVITON_INSTANCE_ID" \
    --query 'MetricAlarms[0].StateValue'
```

## Troubleshooting

### Common Migration Issues and Solutions

#### 1. Architecture Compatibility Issues

**Problem**: Application fails to start with "cannot execute binary file: Exec format error"
```bash
# Diagnosis
file /path/to/binary
# Output: ELF 64-bit LSB executable, x86-64, version 1 (SYSV)

# Solution: Rebuild for ARM64
GOOS=linux GOARCH=arm64 go build -o binary-arm64 main.go
# Or use multi-arch container
docker buildx build --platform linux/arm64 -t myapp:arm64 .
```

**Problem**: Shared library not found or incompatible
```bash
# Diagnosis
ldd /path/to/application
# Output: libexample.so.1 => not found

# Solution: Install ARM64 version of library
sudo apt-get install libexample1:arm64
# Or compile from source for ARM64
```

#### 2. Performance Issues

**Problem**: Application performance is worse than expected
```bash
# Diagnosis: Check CPU utilization patterns
top -p $(pgrep myapp)
htop

# Check for CPU throttling
cat /proc/cpuinfo | grep MHz

# Solution: Optimize for ARM64 architecture
# Enable ARM64-specific compiler optimizations
gcc -march=armv8-a -mtune=cortex-a72 -O3 source.c

# For Java applications, use ARM64-optimized JVM
sudo apt-get install openjdk-11-jdk-headless
export JAVA_OPTS="-XX:+UseG1GC -XX:+UseStringDeduplication"
```

**Problem**: Memory usage higher than expected
```bash
# Diagnosis
free -h
cat /proc/meminfo
pmap -x $(pgrep myapp)

# Solution: Tune memory allocation
# For Java applications
export JAVA_OPTS="-Xmx2g -Xms1g -XX:NewRatio=3"

# For Python applications
export PYTHONMALLOC=malloc
```

#### 3. Network and Connectivity Issues

**Problem**: Network performance degradation
```bash
# Diagnosis
iperf3 -c target-host
netstat -i
ss -tuln

# Solution: Enable enhanced networking
aws ec2 modify-instance-attribute \
    --instance-id i-1234567890abcdef0 \
    --ena-support

# Verify enhanced networking
aws ec2 describe-instances \
    --instance-ids i-1234567890abcdef0 \
    --query 'Reservations[0].Instances[0].EnaSupport'
```

#### 4. Storage and I/O Issues

**Problem**: Disk I/O performance issues
```bash
# Diagnosis
iostat -x 1 10
iotop -o

# Solution: Optimize EBS settings
# Use GP3 volumes with provisioned IOPS
aws ec2 modify-volume \
    --volume-id vol-1234567890abcdef0 \
    --volume-type gp3 \
    --iops 3000 \
    --throughput 125

# Enable EBS optimization
aws ec2 modify-instance-attribute \
    --instance-id i-1234567890abcdef0 \
    --ebs-optimized
```

### Debugging Tools and Techniques

#### System-Level Debugging
```bash
# CPU and process monitoring
htop
atop
pidstat -u 1

# Memory analysis
free -h
vmstat 1
cat /proc/meminfo

# I/O monitoring
iotop
iostat -x 1
lsof +D /path/to/directory

# Network debugging
ss -tuln
netstat -rn
tcpdump -i eth0 host target-host
```

#### Application-Level Debugging
```bash
# Java applications
jstack <pid>  # Thread dump
jmap -histo <pid>  # Memory histogram
jstat -gc <pid> 1s  # Garbage collection stats

# Python applications
py-spy top --pid <pid>  # CPU profiling
memory_profiler  # Memory profiling

# Node.js applications
node --inspect app.js  # Enable debugging
clinic doctor -- node app.js  # Performance analysis
```

#### Container Debugging
```bash
# Docker container inspection
docker stats container-name
docker exec -it container-name /bin/bash
docker logs container-name

# Kubernetes debugging
kubectl describe pod pod-name
kubectl logs pod-name
kubectl exec -it pod-name -- /bin/bash
```

### Performance Optimization Tips

#### ARM64-Specific Optimizations

**Compiler Optimizations:**
```bash
# GCC optimizations for ARM64
CFLAGS="-march=armv8-a -mtune=cortex-a72 -O3 -ffast-math"

# Rust optimizations
[profile.release]
target-cpu = "cortex-a72"
lto = true
codegen-units = 1
```

**Language-Specific Optimizations:**

**Java:**
```bash
# JVM tuning for Graviton
export JAVA_OPTS="-XX:+UseG1GC -XX:+UseStringDeduplication -XX:+OptimizeStringConcat"
```

**Python:**
```bash
# Use optimized Python builds
sudo apt-get install python3.9-dev
pip install --upgrade pip setuptools wheel
```

**Node.js:**
```bash
# Use ARM64-optimized Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
```

## Post-Migration Optimization

### Cost Monitoring and Optimization

#### Set Up Cost Monitoring
```bash
# Create cost budget for Graviton instances
aws budgets create-budget \
    --account-id 123456789012 \
    --budget '{
        "BudgetName": "Graviton-Migration-Budget",
        "BudgetLimit": {
            "Amount": "1000",
            "Unit": "USD"
        },
        "TimeUnit": "MONTHLY",
        "BudgetType": "COST",
        "CostFilters": {
            "Service": ["Amazon Elastic Compute Cloud - Compute"]
        }
    }'

# Set up cost anomaly detection
aws ce create-anomaly-detector \
    --anomaly-detector '{
        "DetectorName": "Graviton-Cost-Anomaly",
        "MonitorType": "DIMENSIONAL",
        "DimensionKey": "SERVICE",
        "MatchOptions": ["EQUALS"],
        "MonitorSpecification": "Amazon Elastic Compute Cloud - Compute"
    }'
```

#### Cost Analysis Script
```bash
#!/bin/bash
# cost_analysis.sh

echo "=== Graviton Migration Cost Analysis ==="

# Get current month costs
aws ce get-cost-and-usage \
    --time-period Start=$(date -d "$(date +%Y-%m-01)" +%Y-%m-%d),End=$(date +%Y-%m-%d) \
    --granularity MONTHLY \
    --metrics BlendedCost \
    --group-by Type=DIMENSION,Key=INSTANCE_TYPE \
    --filter '{
        "Dimensions": {
            "Key": "SERVICE",
            "Values": ["Amazon Elastic Compute Cloud - Compute"]
        }
    }' \
    --query 'ResultsByTime[0].Groups[?starts_with(Keys[0], `t4g`) || starts_with(Keys[0], `m6g`) || starts_with(Keys[0], `c6g`) || starts_with(Keys[0], `r6g`)]'

echo -e "\n=== Savings Calculation ==="
# Compare with previous period (before migration)
aws ce get-cost-and-usage \
    --time-period Start=$(date -d "$(date +%Y-%m-01) -1 month" +%Y-%m-%d),End=$(date -d "$(date +%Y-%m-01) -1 day" +%Y-%m-%d) \
    --granularity MONTHLY \
    --metrics BlendedCost \
    --group-by Type=DIMENSION,Key=INSTANCE_TYPE \
    --filter '{
        "Dimensions": {
            "Key": "SERVICE",
            "Values": ["Amazon Elastic Compute Cloud - Compute"]
        }
    }'
```

### Performance Monitoring

#### Continuous Performance Monitoring
```bash
#!/bin/bash
# setup_continuous_monitoring.sh

# Install CloudWatch agent
wget https://s3.amazonaws.com/amazoncloudwatch-agent/amazon_linux/amd64/latest/amazon-cloudwatch-agent.rpm
sudo rpm -U ./amazon-cloudwatch-agent.rpm

# Configure CloudWatch agent for detailed monitoring
cat > /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json << 'EOF'
{
    "metrics": {
        "namespace": "Custom/Graviton",
        "metrics_collected": {
            "cpu": {
                "measurement": [
                    "cpu_usage_idle",
                    "cpu_usage_iowait",
                    "cpu_usage_user",
                    "cpu_usage_system"
                ],
                "metrics_collection_interval": 60
            },
            "disk": {
                "measurement": [
                    "used_percent"
                ],
                "metrics_collection_interval": 60,
                "resources": [
                    "*"
                ]
            },
            "diskio": {
                "measurement": [
                    "io_time",
                    "read_bytes",
                    "write_bytes",
                    "reads",
                    "writes"
                ],
                "metrics_collection_interval": 60,
                "resources": [
                    "*"
                ]
            },
            "mem": {
                "measurement": [
                    "mem_used_percent"
                ],
                "metrics_collection_interval": 60
            },
            "netstat": {
                "measurement": [
                    "tcp_established",
                    "tcp_time_wait"
                ],
                "metrics_collection_interval": 60
            }
        }
    }
}
EOF

# Start CloudWatch agent
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
    -a fetch-config \
    -m ec2 \
    -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json \
    -s
```

### Ongoing Optimization

#### Regular Performance Reviews
```bash
#!/bin/bash
# weekly_performance_review.sh

echo "=== Weekly Graviton Performance Review ==="
echo "Date: $(date)"

# Check instance utilization
aws cloudwatch get-metric-statistics \
    --namespace AWS/EC2 \
    --metric-name CPUUtilization \
    --start-time $(date -u -d '7 days ago' +%Y-%m-%dT%H:%M:%S) \
    --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
    --period 3600 \
    --statistics Average,Maximum \
    --dimensions Name=InstanceType,Value=t4g.medium

# Check for right-sizing opportunities
aws compute-optimizer get-ec2-instance-recommendations \
    --filters name=RecommendationSourceType,values=Ec2Instance \
    --query 'instanceRecommendations[?currentInstanceType==`t4g.medium`]'

# Generate recommendations
echo "Recommendations:"
echo "1. Review instances with consistently low CPU utilization"
echo "2. Consider upgrading instances with high CPU utilization"
echo "3. Evaluate newer Graviton generations for better performance"
```

## Additional Resources

### Official AWS Documentation

#### Core Graviton Resources
- **[AWS Graviton Processor Overview](https://aws.amazon.com/ec2/graviton/)** - Main landing page for Graviton information
- **[Getting Started with Graviton](https://aws.amazon.com/ec2/graviton/getting-started/)** - Quick start guide and tutorials
- **[Graviton Performance Testing Guide](https://docs.aws.amazon.com/whitepapers/latest/aws-graviton-performance-testing/aws-graviton-performance-testing.html)** - Comprehensive performance testing methodology
- **[EC2 Instance Types Documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html)** - Complete instance type specifications

#### Migration and Optimization Guides
- **[Optimizing Costs with Graviton](https://docs.aws.amazon.com/prescriptive-guidance/latest/optimize-costs-microsoft-workloads/net-graviton.html)** - Cost optimization strategies
- **[Container Migration to Graviton](https://aws.amazon.com/blogs/containers/how-to-build-your-containers-for-arm-and-save-with-graviton-and-spot-instances-on-amazon-ecs/)** - Container-specific migration guidance
- **[Lambda Functions on Graviton](https://aws.amazon.com/blogs/aws/aws-lambda-functions-powered-by-aws-graviton2-processor-run-your-functions-on-arm-and-get-up-to-34-better-price-performance/)** - Serverless migration guide

### Tools and Utilities

#### AWS Native Tools
- **[AWS Pricing Calculator](https://calculator.aws)** - Cost estimation and comparison
- **[AWS Compute Optimizer](https://aws.amazon.com/compute-optimizer/)** - Right-sizing recommendations
- **[Graviton Savings Dashboard](https://aws.amazon.com/ec2/graviton/graviton-savings-dashboard/)** - Savings visualization tool
- **[AWS Migration Hub](https://aws.amazon.com/migration-hub/)** - Migration planning and tracking

#### Third-Party Tools
- **[Sysbench](https://github.com/akopytov/sysbench)** - System performance benchmarking
- **[Apache Bench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)** - Web server load testing
- **[wrk](https://github.com/wg/wrk)** - Modern HTTP benchmarking tool
- **[iperf3](https://iperf.fr/)** - Network performance testing

### Community Resources

#### GitHub Repositories
- **[AWS Graviton Getting Started](https://github.com/aws/aws-graviton-getting-started)** - Code samples and best practices
- **[Graviton2 Performance Testing](https://github.com/aws-samples/graviton2-performance-testing)** - Performance testing scripts
- **[ARM64 Software Ecosystem](https://github.com/aws/aws-graviton-getting-started/blob/main/software.md)** - ARM64 software compatibility list

#### Blogs and Articles
- **[AWS Architecture Blog](https://aws.amazon.com/blogs/architecture/)** - Architecture patterns and best practices
- **[AWS Compute Blog](https://aws.amazon.com/blogs/compute/)** - Compute service updates and tutorials
- **[AWS Cost Optimization Blog](https://aws.amazon.com/blogs/aws-cost-management/)** - Cost optimization strategies

### Training and Certification

#### AWS Training Resources
- **[AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)** - Architecture best practices
- **[AWS Cost Optimization](https://aws.amazon.com/aws-cost-management/aws-cost-optimization/)** - Cost management training
- **[EC2 Deep Dive](https://aws.amazon.com/training/classroom/deep-dive-amazon-ec2/)** - Advanced EC2 training

#### Hands-On Labs
- **[AWS Workshops](https://workshops.aws/)** - Interactive learning experiences
- **[Graviton Fast Start](https://aws.amazon.com/ec2/graviton/fast-start/)** - Quick deployment templates
- **[AWS Samples](https://github.com/aws-samples)** - Sample applications and code

### Support and Community

#### AWS Support
- **[AWS Support Center](https://console.aws.amazon.com/support/)** - Technical support cases
- **[AWS re:Post](https://repost.aws/)** - Community Q&A platform
- **[AWS Forums](https://forums.aws.amazon.com/)** - Community discussions

#### Professional Services
- **[AWS Professional Services](https://aws.amazon.com/professional-services/)** - Migration assistance
- **[AWS Partner Network](https://aws.amazon.com/partners/)** - Certified migration partners
- **[AWS Training Partners](https://aws.amazon.com/training/teams/)** - Training and certification

---

## Conclusion

Migrating from x86 to AWS Graviton instances can provide significant cost savings (15-40%) while maintaining or improving performance. Success depends on thorough planning, comprehensive testing, and following ARM64 best practices.

### Key Success Factors:
1. **Thorough Assessment**: Understand your applications and dependencies
2. **Comprehensive Testing**: Validate functionality and performance before production
3. **Phased Approach**: Migrate incrementally to minimize risk
4. **Continuous Monitoring**: Track performance and costs post-migration
5. **Ongoing Optimization**: Regularly review and optimize your Graviton deployment

### Next Steps:
1. Start with development and test environments
2. Use the provided scripts and checklists
3. Leverage AWS tools for assessment and migration
4. Engage AWS support or partners for complex migrations
5. Share learnings with your team and organization

Remember that Graviton migration is not just a cost optimization exercise—it's an opportunity to modernize your infrastructure and adopt cloud-native best practices that will benefit your organization long-term.

For questions or additional support, refer to the resources section or engage with the AWS community through re:Post and forums.
