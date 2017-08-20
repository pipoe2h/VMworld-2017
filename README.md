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
### VMware vRealize Orchestrator
### VMware vRealize Automation

## Resources
* [Postman VMworld 2017 Collection](https://documenter.getpostman.com/view/911382/vmworld-2017/6n5zCzu)
* [Cisco APIC Rest API Configuration Guide Book](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/sw/2-x/rest_cfg/2_1_x/b_Cisco_APIC_REST_API_Configuration_Guide.pdf)
