# saphana_deployment
Deploy SAP HANA System Replication with HA on Azure using Red Hat System Roles for SAP and Ansible roles and modules

## First, let’s take a look at the playbooks to deploy SAP HANA System Replication.

Yaml file site.yml is the main playbook, which consists of 3 playbooks respectively to provision Azure VMs as SAP HANA nodes (provision_azure_vms.yml) with the credentials, and then set up the Azure VMs (configure_azure_vms.yml) before SAP HANA deployment, finally deploy SAP HANA System Replication (deploy_sap_hana_hsr.yml).
````
site.yml
├── provision_azure_vms.yml
├── configure_azure_vms.yml
└── deploy_sap_hana_hsr.yml
````
- provision_azure_vms.yml takes care of Azure VM provisioning. The difference from the previous blog is that the role azure_rm_availabilityset is added to create Azure Availability sets which provide VM redundancy and availability.
- Once Azure VMs are provisioned, playbook site.yml will trigger configure_azure_vms.yml to configure the VMs and the bootstrap server in the OS level.
- As the last piece, deploy_sap_hana_hsr.yml is used to prepare SAP HANA nodes, install SAP HANA, configure SAP HANA System Replication, and set up High Availability, i.e. Red Hat pacemaker.

## Now, let's check out the file structure
````
.
├── ansible.cfg
├── configure_azure_vms.yml -------- Configure the VMs and the bootstrap server in the OS level
├── deploy_bootstrap.yml ----------- Deploy bootstrap server on Azure
├── deploy_sap_hana_hsr.yml -------- Deploy SAP HANA System Replication, and set up pacemaker
├── files
│   └── azure_cred.yml ------------- Store Ansible vault for Azure credentials
├── group_vars
│   └── hana.yml ------------------- Group vars for hana group
├── host_vars
│   ├── saphana01.yml -------------- Host vars for saphana01
│   └── saphana02.yml -------------- Host vars for saphana02
├── inventory
├── provision_azure_vms.yml -------- Provision Azure VMs
├── README.md
├── roles
│   └── requirements.yml ----------- Store the the dependent Ansible roles for SAP and Linux System Roles
└── site.yml ----------------------- Main playbook
````

Now, it is time to kick off the playbook:
````
# ansible-playbook site.yml
````

Note: While running the playbook make sure that your console will not be disconnected due to SSH connection timeout, otherwise, you may need to clean up the VMs and start over again.

## Summary
As you can see System Roles for SAP and Ansible modules can speed up your SAP HANA deployment. These help to manage complex processes in the automated way, which is a perfect solution to use in infrastructure workflows. You can easily integrate them with other building tools for continuous integration and deployment of systems to work efficiently and enhance your productivity.
