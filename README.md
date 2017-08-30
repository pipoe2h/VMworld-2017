# VMworld-2017
This repository includes all the content used for the Tech Talk "Automating and Integrating Cisco ACI with VMware vRA using Ansible" in VMworld 2017 Europe

## Demo
This demo shows the end to end about:
* How to use Postman to run API calls against Cisco ACI or any other API (Cisco ACI API calls are available in the resources section.
* How to use Ansible URI core module to run the same API calls delegating on Ansible roles.
* Integrating VMware vRealize Orchestrator with the Ansible automation server to leverage the developed automation.
* Publishing the VMware vRealize Orchestrator workflow as a XaaS (Everything-as-a-Service) blueprint in VMware vRealize Automation.

### Postman
* Download the Postman collection of Cisco ACI API calls from the resources section at the bottom. You have detailed information about how to use the Postman collection in the collection website.
* The first API call to use is the authentication one to retrieve the required tokens.
* The second API call called "List Tenants" retrieves the Cisco ACI tenants available. This call uses the tokens stored in the previous step as headers: ***Cookie: APIC-cookie=token***, and ***APIC-challenge: urlToken***.
### Ansible Automation server
* Clone this repo in your Ansible server.
```shell
git clone https://github.com/pipoe2h/VMworld-2017.git
```
* Navigate to the ansible folder and edit the hosts file with your APIC address.
```shell
cd VMworld-2017/ansible
```
* The examples folder give you a role template you can use as a baseline for your future roles, and a configuration file called VMworld2017-IaC.yml with the definition of the Cisco ACI objects (Infrastructure as Code) to create on our requests.
```yaml
aci_tenant:
  - tenant_name: VMworld2017US
  - tenant_name: VMworld2017EU

aci_vrf:
  - vrf_name: VMworld2017US
    tenant_name: VMworld2017US
  - vrf_name: VMworld2017EU
    tenant_name: VMworld2017EU
```
* The roles folder includes two pre-created roles for authentication and VRF creation:
  - The authentication role requires the environment variables ***APIC_USERNAME*** and ***APIC_PASSWORD***.
  ```shell
  export APIC_USERNAME=<aci_user> APIC_PASSWORD=<aci_user_password>
  ```
  - The VRF role has the dependency of an ACI tenant, since we don't have any (despite the default ones) we will create a new Ansible role called ***vmworld.aci_tenant-create*** to meet the tenant dependency.
  ```shell
  ansible-galaxy init vmworld.aci_tenant-create --offline -p roles
  ```
* Overwrite the tasks file main.yml in the ***vmworld.aci_tenant-create*** role with the tasks file main.yml from the ***vmworld.aci_vrf-create role***
```shell
cp roles/vmworld.aci_vrf-create/tasks/main.yml roles/vmworld.aci_tenant-create/tasks/main.yml
```
* Edit the tasks file main.yml for the ***vmworld.aci_tenant-create*** role and replace as shown below. (Note: the URL and body have been captured using the *API Inspector* feature in ACI. Open the *API Inspector* and trigger the creation of a new ACI tenant, in the Inspector window you will be able to see the POST request with the URL and Payload values)
```shell
vim roles/vmworld.aci_tenant-create/tasks/main.yml
```
``` yaml
---
- name: ACI - Creating Tenant
  uri:
    url: https://{{ inventory_hostname }}/api/mo/uni.json
    method: POST
    headers:
      Cookie: "{{ token }}"
      APIC-challenge: "{{ urlToken }}"
    validate_certs: no
    body_format: json
    body: >
      {
        "fvTenant": {
                "attributes": {
                        "name": "{{ item.tenant_name }}",
                },
        }
      }
  register: tenant
  with_items:
    - "{{ aci_tenant }}"
```
* Update the **playbook.yml** file in the ansible folder and include the new role.
```shell
vim playbook.yml
```
```yaml
---
- name: VMworld 2017 demo
  hosts: apic
  connection: local
  roles:
    - vmworld.aci_login
    - vmworld.aci_tenant-create
    - vmworld.aci_vrf-create
```
* Now that we have all the pieces, you can run the Ansible playbook passing the VMworld2017-IaC.yml configuration file as extra vars.
```shell
ansible-playbook playbook.yml -e @examples/VMworld2017-IaC.yml
```
### VMware vRealize Orchestrator
* You can download the vRO package with the used workflows from the resources section.
* The workflow is quite simple. This is a wrapper with the ***Run SSH command*** workflow available out-of-the-box in vRO, and a script file where we capture three inputs to produce the SSH command to execute.
### VMware vRealize Automation
* Create a XaaS blueprint for the vRO workflow above and publish that into your vRA service catalogue.

## Resources
* [Postman VMworld 2017 Collection](https://documenter.getpostman.com/view/911382/vmworld-2017/6n5zCzu)
* [VMware vRealize Orchestrator vmworld2017eu package](https://github.com/pipoe2h/VMworld-2017/raw/master/vro/com.joseluisgomez.vmworld2017eu.package)
* [Cisco APIC Rest API Configuration Guide Book](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/sw/2-x/rest_cfg/2_1_x/b_Cisco_APIC_REST_API_Configuration_Guide.pdf)
