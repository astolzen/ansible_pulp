# Pulp Automate Install on RHEL 7
V 0.02 - 6/2/2017

This Playbook will fully automate the setup of a pulp server that syncs al necessary RPM and Docker Repos used by Openshift 3.4. 

## Idea
I want to be able to setup/destroy multi-node Openshift Test/demo environments (local on one Notebook using any given Hypervisor) without being online. All RPMs and Docker Packets should be served by a local Pulp Server VM.

### Steps:
- create Pulp Server and sync Repos using this Playbook
- create a bunch of VMs for the OSCP environment (using whatever orchestration)
- use the existing OSCP-Ansible Installer to roll out a multi node Openshift environment and pull all needed packets from the pulp server


## Concept
- Install plain RHEL7-VM from DVD or Template
- Install Pulp (automated via this Ansible PB)
- Sync Openshift Repos, Sync Docker Repos (yet to be automated)
- Offline Install Openshift-Environment that grabs all necessary RPM and Docker Repos from Pulp Server

## Howto

### 0. Requirements
- Host (Notebook/Worstation) with Ansible installed.
- VM Plattform on this or any other Host to later run the Pulp VM
- **You need a valid RHEL subscription** that will be applied to the VM

### 1. RHEL Install
- Download "Red Hat Enterprise Linux 7.3 Binary DVD"
- Create VM (using any hypervisor), make sure to have at least 80 GB in /var
- Install RHEL7 Minimal
- **or** clone an already existing RHEL7-VM
- note VMs IP Adress. 
- use ssh-copy-id root@{{pulpvm}} to allow your ansible running node to access the VM without password

### 2. Config
- Andjust hosts file: Insert IP of your Pulp Server, add the hostename in the _hostname=_ variable. Set _is_already_subscribed_ to True, **only** whent the system is already subscribed and has all required repos enabled
- run ansible playbook "pulpinstall" to set up pulp server
- customize repovars.yml and add repos you like to sync
- run ansible playbook "pulprepos.yml" to create repos as defined in repovars plus schedules for RPM repos and do an intial sync


## Current 
- Script will subscribe the host, install Pulp, set up Pulp DB, enable all services and do a basic config
- admin-access will be set to htpasswd-Auth with user admin:admin
- pulp-admin will be preconfigured with admin:admin-Access
- pulprepos PB will create repos and sync

## TBD (future)
- Modularize Playbook
- Modularize VARs
- include user defined RPM/Docker Repos into auto sync setup
- add lazy sync option
- Add Option to roll out Pulp-Server on Clouds (Azure, Aws, Google) alternatively to local VNMs


