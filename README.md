# aws-elastic-ip
Creates an AWS elastic IP address

## Requirements
- AWS credentials and the correct permissions to create the resources

## Outputs
The role provides the following outputs:

`elastic_ip`: This fact can be used in a further play 

## Role Variables
The variables uses in this role are:

| Variable Name | Required | Description | 
|----|----|----|
| `region`| **Yes** | The region that you will deploy into |
| `state` | **Yes** | If present, allocate an EIP or associate an existing EIP with a device <br> If absent, disassociate the EIP from the device and optionally release it. |
| `eip_reuse_existing_ip_allowed` | Optional | Reuse an EIP that is not associated to a device (when available), instead of allocating a new one <br> - Default `no`|
| `eip_release_on_disassociation` | Optional | whether or not to automatically release the EIP when it is disassociated <br> - Default `no` |
| `eip_in_vpc` | Optional | Allocate an EIP inside a VPC or not. Required if specifying an ENI. <br> - Default `no` |
| `eip_device_id` | Optional | The id of the device for the EIP |
| `eip_allow_reassociation` | Optional | Allow EIP already associated with another ENI to be re-associated with the specified instance or interface <br> - Default `no` |
| `eip_private_ip_address` | Optional | The primary or secondary private IP address to associate with the Elastic IP address |
| `eip_public_ip` | Optional | The IP address of a previously allocated EIP. <br> If present and device is specified, the EIP is associated with the device <br> If absent and device is specified, the EIP is disassociated from the device |

## Example Playbook

## Download dependencies

### Create requirements file
Create a `requirements.yml` file with the following contents
```
- src: https://github.com/maishsk/aws-elastic-ip
  version: master
```

### Download dependencies
Run the following command:
`ansible-galaxy install -r requirements.yml --force -p .`

### Create playbook
Create a `main.yaml` file with the following contents:
```
---
- name: Create Elastic IP
  hosts: localhost
  connection: local
  gather_facts: false
  vars_files:
    - vars/vars.yml
  tasks:
  - name: Create Process
    include_role:
      name: "{{ item }}"
    with_items:
      - aws-elastic-ip
    tags: [ 'never', 'create' ]

  - name: Rollback Process
    include_role:
      name: "{{ item }}"
    with_items:
      - aws-elastic-ip
    tags: [ 'never', 'rollback' ]
```
Create a `vars/vars.yml` with the content similar to:

```
region: us-east-2
```

### Running the playbook

To create the Elastic IP

`ansible-playbook main.yml --tags create`

To remove the Elastic IP

`ansible-playbook main.yml --tags rollback -e eip_public_ip=1.1.1.1`

## License
BSD

## Author Information
This role was created by [Maish Saidel-Keesing](https://www.maishsk.com/), author of [The Cloud Walkabout](http://cloudwalkabout.com/).