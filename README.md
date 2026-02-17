# Home Lab Virtualized Infrastructure Platform

## Objective
Design and maintain a self-hosted virtualized infrastructure environment to support persistent multi-user application services, network storage, and automated backups. The lab is used to practice production-style systems administration, virtualization, and service reliability.

## Physical Host Hardware

CPU: Ryzen 7 5700G (8-core / 16-thread)
RAM: 48GB DDR4
GPU: RX580 16GB
Motherboard: X470 Gaming Pro
PSU: Corsair TX650W

Storage Layout:
- NVMe 256GB — Proxmox host + primary VM storage
- SSD 120GB — Secondary VM disk
- 2x 2TB HDD — Bulk storage and backup targets

## Virtualization Platform

Hypervisor: Proxmox VE

Proxmox was selected for:
- Type-1 virtualization
- ZFS support and storage flexibility
- Snapshot capability
- Resource controls per VM
- Web-based management

## Virtual Machines

### TrueNAS VM
Purpose: Network storage and data management

Responsibilities:
- Central storage pool
- Data segregation
- Snapshot support
- Backup target for application data

### Debian VM — Application Services
Purpose: Multi-user service hosting

Services:
- Crafty Controller
- Persistent multi-user application servers

Responsibilities:
- Service uptime
- Version upgrades
- Performance tuning
- User access control

## Resource Allocation Strategy

VMs are allocated CPU and RAM based on service load and concurrency requirements. Multi-user application services are given priority CPU scheduling and memory reservations to prevent contention during peak usage.

## Backup Strategy

Automated daily backups of persistent service data are executed at 4:00 AM EST.

Backup system includes:
- Scheduled automated backups
- Retention rotation
- Off-service data copies
- Restore validation tests performed manually

## Automation

Automated backup scripts run daily via scheduled task:
- Service data snapshot
- Archive creation
- Storage rotation
- Logging of job results

## Maintenance Operations

Routine maintenance includes:
- OS patching
- Service updates
- Dependency compatibility checks
- Performance tuning
- Disk utilization monitoring

## Failure Scenario Handling

Tested recovery procedures include:
- Service crash recovery
- World data restoration from backup
- Version rollback after failed upgrade

## Security Controls

- Restricted SSH access
- Firewall rules configured at host and VM level
- Service ports limited to required exposure
- Principle of least privilege for service accounts

## Lessons Learned

- Snapshot testing is critical before major upgrades
- Resource reservation prevents VM starvation
- Backup validation is as important as backup creation

## Planned Improvements

- Full VM snapshot backups
- Off-host backup replication
- Monitoring and alerting integration
- Infrastructure-as-Code provisioning
