# AIOps-demo-1

# cvm.tf  
terraform {  
  required_providers {  
    tencentcloud = {  
      source = "tencentcloudstack/tencentcloud"  
    }  
  }  
}  

\# Configure the TencentCloud Provider  
provider "tencentcloud" {  
  secret_id  = "xxx"  
  secret_key = "xxx"  
  region     = "ap-seoul"  
}  

\# Get availability zones  
data "tencentcloud_availability_zones" "default" {}  

\# Get availability images  
data "tencentcloud_images" "default" {  
  image_type = ["PUBLIC_IMAGE"]  
  os_name    = "centos"  
}  

\# Get availability instance types  
data "tencentcloud_instance_types" "default" {  
  cpu_core_count = 2  
}  

\# Create a web server  
resource "tencentcloud_instance" "web" {  
  instance_name              = "web server"  
  availability_zone          = data.tencentcloud_availability_zones.default.zones.0.name  
  image_id                   = data.tencentcloud_images.default.images.0.image_id  
  instance_type              = data.tencentcloud_instance_types.default.instance_types.0.instance_type  
  system_disk_type           = "CLOUD_PREMIUM"  
  system_disk_size           = 50  
  allocate_public_ip         = true  
  internet_max_bandwidth_out = 20  
  security_groups            = [tencentcloud_security_group.default.id]  
  count                      = 1  
}  

\# Create security group  
resource "tencentcloud_security_group" "default" {  
  name        = "web accessibility"  
  description = "make it accessible for both production and stage ports"  
}  

\# Create security group rule allow web request  
resource "tencentcloud_security_group_rule" "web" {  
  security_group_id = tencentcloud_security_group.default.id  
  type              = "ingress"  
  cidr_ip           = "0.0.0.0/0"  
  ip_protocol       = "tcp"  
  port_range        = "80,8080"  
  policy            = "accept"  
}  

\# Create security group rule allow ssh request  
resource "tencentcloud_security_group_rule" "ssh" {  
  security_group_id = tencentcloud_security_group.default.id  
  type              = "ingress"  
  cidr_ip           = "0.0.0.0/0"  
  ip_protocol       = "tcp"  
  port_range        = "22"  
  policy            = "accept"  
}  

PS F:\workspace\vscode\AIOPS-LOCAL\module_2\demo1> terraform apply -auto-approve  
tencentcloud_security_group.default: Refreshing state... [id=sg-qocdtmho]  
data.tencentcloud_instance_types.default: Reading...  
data.tencentcloud_images.default: Reading...  
data.tencentcloud_availability_zones.default: Reading...  
tencentcloud_security_group_rule.web: Refreshing state...  [id=eyJzZ19pZCI6InNnLXFvY2R0bWhvIiwicG9saWN5X3R5cGUiOiJpbmdyZXNzIiwiY2lkcl9pcCI6IjAuMC4wLjAvMCIsInByb3RvY29sIjoidGNwIiwicG9ydF9yYW5nZSI6IjgwLDgwODAiLCJhY3Rpb24iOiJhY2NlcHQiLCJzb3VyY2Vfc2dfaWQiOiIiLCJkZXNjcmlwdGlvbiI6IiJ9]  
tencentcloud_security_group_rule.ssh: Refreshing state... [id=eyJzZ19pZCI6InNnLXFvY2R0bWhvIiwicG9saWN5X3R5cGUiOiJpbmdyZXNzIiwiY2lkcl9pcCI6IjAuMC4wLjAvMCIsInByb3RvY29sIjoidGNwIiwicG9ydF9yYW5nZSI6IjIyIiwiYWN0aW9uIjoiYWNjZXB0Iiwic291cmNlX3NnX2lkIjoiIiwiZGVzY3JpcHRpb24iOiIifQ==]  
data.tencentcloud_images.default: Read complete after 2s [id=289880424]  
data.tencentcloud_availability_zones.default: Read complete after 3s [id=2388801764]  
data.tencentcloud_instance_types.default: Read complete after 6s [id=2235576211]  

Note: Objects have changed outside of Terraform  

Terraform detected the following changes made outside of Terraform since the last "terraform        
apply" which may have affected this plan:  

  \# tencentcloud_security_group.default has been deleted  
  - resource "tencentcloud_security_group" "default" {  
      - id          = "sg-qocdtmho" -> null  
        name        = "web accessibility"  
        \# (2 unchanged attributes hidden)  
    }  


Unless you have made equivalent changes to your configuration, or ignored the relevant attributes   
using ignore_changes, the following plan may include actions to undo or respond to these changes.   

─────────────────────────────────────────────────────────────────────────────────────────────────   

Terraform used the selected providers to generate the following execution plan. Resource actions   
are indicated with the following symbols:  
  + create  

Terraform will perform the following actions:  

  \# tencentcloud_instance.web[0] will be created  
  + resource "tencentcloud_instance" "web" {  
      + allocate_public_ip                      = true  
      + availability_zone                       = "ap-seoul-1"  
      + cpu                                     = (known after apply)  
      + create_time                             = (known after apply)  
      + disable_api_termination                 = false  
      + disable_monitor_service                 = false  
      + disable_security_service                = false  
      + expired_time                            = (known after apply)  
      + force_delete                            = false  
      + id                                      = (known after apply)  
      + image_id                                = "img-eb30mz89"  
      + instance_charge_type                    = "POSTPAID_BY_HOUR"  
      + instance_charge_type_prepaid_renew_flag = (known after apply)  
      + instance_name                           = "web server"  
      + instance_status                         = (known after apply)  
      + instance_type                           = "M8.MEDIUM16"  
      + internet_charge_type                    = (known after apply)  
      + internet_max_bandwidth_out              = 20  
      + key_ids                                 = (known after apply)  
      + key_name                                = (known after apply)  
      + memory                                  = (known after apply)  
      + orderly_security_groups                 = (known after apply)  
      + os_name                                 = (known after apply)  
      + private_ip                              = (known after apply)  
      + project_id                              = 0  
      + public_ip                               = (known after apply)  
      + running_flag                            = true  
      + security_groups                         = (known after apply)  
      + subnet_id                               = (known after apply)  
      + system_disk_id                          = (known after apply)  
      + system_disk_size                        = 50  
      + system_disk_type                        = "CLOUD_PREMIUM"  
      + uuid                                    = (known after apply)  
      + vpc_id                                  = (known after apply)  

      + data_disks (known after apply)  
    }  

  \# tencentcloud_security_group.default will be created  
  + resource "tencentcloud_security_group" "default" {  
      + description = "make it accessible for both production and stage ports"  
      + id          = (known after apply)  
      + name        = "web accessibility"  
      + project_id  = (known after apply)  
    }  

  \# tencentcloud_security_group_rule.ssh will be created  
  + resource "tencentcloud_security_group_rule" "ssh" {  
      + cidr_ip           = "0.0.0.0/0"  
      + description       = (known after apply)  
      + id                = (known after apply)  
      + ip_protocol       = "tcp"  
      + policy            = "accept"  
      + port_range        = "22"  
      + security_group_id = (known after apply)  
      + source_sgid       = (known after apply)  
      + type              = "ingress"  

      + address_template (known after apply)  

      + protocol_template (known after apply)  
    }  

  \# tencentcloud_security_group_rule.web will be created  
  + resource "tencentcloud_security_group_rule" "web" {  
      + cidr_ip           = "0.0.0.0/0"  
      + description       = (known after apply)  
      + id                = (known after apply)  
      + ip_protocol       = "tcp"  
      + policy            = "accept"  
      + port_range        = "80,8080"  
      + security_group_id = (known after apply)  
      + source_sgid       = (known after apply)  
      + type              = "ingress"  

      + address_template (known after apply)  

      + protocol_template (known after apply)  
    }

Plan: 4 to add, 0 to change, 0 to destroy.  
tencentcloud_security_group.default: Creating...  
tencentcloud_security_group.default: Creation complete after 1s [id=sg-0vys3obd]  
tencentcloud_security_group_rule.ssh: Creating...  
tencentcloud_security_group_rule.web: Creating...  
tencentcloud_instance.web[0]: Creating...  
tencentcloud_security_group_rule.ssh: Creation complete after 1s [id=eyJzZ19pZCI6InNnLTB2eXMzb2JkIiwicG9saWN5X3R5cGUiOiJpbmdyZXNzIiwiY2lkcl9pcCI6IjAuMC4wLjAvMCIsInByb3RvY29sIjoidGNwIiwicG9ydF9yYW5nZSI6IjIyIiwiYWN0aW9uIjoiYWNjZXB0Iiwic291cmNlX3NnX2lkIjoiIiwiZGVzY3JpcHRpb24iOiIifQ==]  
tencentcloud_security_group_rule.web: Creation complete after 1s [id=eyJzZ19pZCI6InNnLTB2eXMzb2JkIiwicG9saWN5X3R5cGUiOiJpbmdyZXNzIiwiY2lkcl9pcCI6IjAuMC4wLjAvMCIsInByb3RvY29sIjoidGNwIiwicG9ydF9yYW5nZSI6IjgwLDgwODAiLCJhY3Rpb24iOiJhY2NlcHQiLCJzb3VyY2Vfc2dfaWQiOiIiLCJkZXNjcmlwdGlvbiI6IiJ9]        
tencentcloud_instance.web[0]: Still creating... [10s elapsed]  
tencentcloud_instance.web[0]: Still creating... [20s elapsed]  
tencentcloud_instance.web[0]: Creation complete after 28s [id=ins-93vuaved]  
╷  
│ Warning: Deprecated Resource  
│
│   with data.tencentcloud_availability_zones.default,  
│   on cvm.tf line 17, in data "tencentcloud_availability_zones" "default":  
│   17: data "tencentcloud_availability_zones" "default" {}  
│  
│ This data source will been deprecated in Terraform TencentCloud provider later version. Please    
│ use `tencentcloud_availability_zones_by_product` instead.  
│  
│ (and 7 more similar warnings elsewhere)  
╵  
╷  
│ Warning: Argument is deprecated  
│  
│   with tencentcloud_instance.web,  
│   on cvm.tf line 40, in resource "tencentcloud_instance" "web":  
│   40:   security_groups            = [tencentcloud_security_group.default.id]  
│  
│ It will be deprecated. Use `orderly_security_groups` instead.  
│  
│ (and 2 more similar warnings elsewhere)  
╵  

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.  
PS F:\workspace\vscode\AIOPS-LOCAL\module_2\demo1>   

![image](https://github.com/user-attachments/assets/30fface0-baf8-4ab2-8a71-33225a9298a5)


