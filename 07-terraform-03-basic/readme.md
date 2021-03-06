## Домашнее задание к занятию "7.3. Основы и принцип работы Терраформ"
#### Задача 1. Создадим бэкэнд в S3 (необязательно, но крайне желательно).

Если в рамках предыдущего задания у вас уже есть аккаунт AWS, то давайте продолжим знакомство со взаимодействием терраформа и aws.

    Создайте s3 бакет, iam роль и пользователя от которого будет работать терраформ. Можно создать отдельного пользователя, а можно использовать созданного в рамках предыдущего задания, просто добавьте ему необходимы права, как описано здесь.
    Зарегистрируйте бэкэнд в терраформ проекте как описано по ссылке выше.

#### Задача 2. Инициализируем проект и создаем воркспейсы.

Выполните terraform init:
        если был создан бэкэнд в S3, то терраформ создат файл стейтов в S3 и запись в таблице dynamodb.
        иначе будет создан локальный файл со стейтами.

Создайте два воркспейса stage и prod.

    vagrant@vagrant:/aws$ terraform workspace new stage
    Created and switched to workspace "stage"!
    
    You're now on a new, empty workspace. Workspaces isolate their state,
    so if you run "terraform plan" Terraform will not see any existing state
    for this configuration.
    vagrant@vagrant:/aws$ terraform workspace new prod
    Created and switched to workspace "prod"!
    
    You're now on a new, empty workspace. Workspaces isolate their state,
    so if you run "terraform plan" Terraform will not see any existing state
    for this configuration.
    vagrant@vagrant:/aws$ terraform workspace list
      default
    * prod
      stage

    
вывод команды vagrant@vagrant:/aws$ terraform plan

    # aws_s3_bucket.bucket will be created
      + resource "aws_s3_bucket" "bucket" {
          + acceleration_status         = (known after apply)
          + acl                         = "private"
          + arn                         = (known after apply)
          + bucket                      = "net-bucket-prod"
          + bucket_domain_name          = (known after apply)
          + bucket_regional_domain_name = (known after apply)
          + force_destroy               = false
          + hosted_zone_id              = (known after apply)
          + id                          = (known after apply)
          + region                      = (known after apply)
          + request_payer               = (known after apply)
          + tags                        = {
              + "Environment" = "prod"
              + "Name"        = "Bucket1"
            }
          + tags_all                    = {
              + "Environment" = "prod"
              + "Name"        = "Bucket1"
            }
          + website_domain              = (known after apply)
          + website_endpoint            = (known after apply)
    
          + versioning {
              + enabled    = (known after apply)
              + mfa_delete = (known after apply)
            }
        }

В уже созданный aws_instance добавьте зависимость типа инстанса от вокспейса, что бы в разных ворскспейсах использовались разные instance_type.

    locals {
      web_instance_type_map = {
        stage = "t2.micro"
        prod = "t3.micro"
      }
    }
    
    resource "aws_instance" "TestUbuntu" {
        ami           = data.aws_ami.ubuntu.id
        instance_type = local.web_instance_type_map[terraform.workspace]
        tags = {
            Name = "MyAWS"
        }
    }

Добавим count. Для stage должен создаться один экземпляр ec2, а для prod два.

    provider "aws" {
      region = "us-east-2"
    }
    
    locals {
      web_instance_count_map = {
      stage = 1
      prod = 2
      }
    }
    
    resource "aws_instance" "TestUbuntu" {
      ami           = data.aws_ami.ubuntu.id
      instance_type = local.web_instance_type_map[terraform.workspace]
      tags = {
          Name = "MyAWS ${count.index}"
      }
      count = local.web_instance_count_map[terraform.workspace]
   }
    


Создайте рядом еще один aws_instance, но теперь определите их количество при помощи for_each, а не count.
Что бы при изменении типа инстанса не возникло ситуации, когда не будет ни одного инстанса добавьте параметр жизненного цикла create_before_destroy = true в один из рессурсов aws_instance.
При желании поэкспериментируйте с другими параметрами и рессурсами.

    provider "aws" {
      region = "us-east-2"
    }
    
    locals {
      instance_ids = toset([
        "t3.micro",
        "t2.micro",
      ])
    }

    resource "aws_instance" "TestUbuntu_1" {
      for_each = local.instance_ids
      ami      = data.aws_ami.ubuntu.id
      instance_type = each.key
      lifecycle {
        create_before_destroy = true
      }
      tags = {
        Name = "MyAWS ${each.key}"
      }
    }


В виде результата работы пришлите:

Вывод команды terraform workspace list.
Вывод команды terraform plan для воркспейса prod.

Вывод сильно большой. поэтому вырезал только создаваемые ресурсы

    # aws_instance.TestUbuntu["t2.micro"] will be created
    + resource "aws_instance" "TestUbuntu" {
      + ami                                  = "ami-0e5e17317f99b2932"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t2.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = (known after apply)
      + monitoring                           = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + placement_partition_number           = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Name" = "MyAWS t2.micro"
        }
      + tags_all                             = {
          + "Name" = "MyAWS t2.micro"
        }
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + vpc_security_group_ids               = (known after apply)

      + capacity_reservation_specification {
          + capacity_reservation_preference = (known after apply)

          + capacity_reservation_target {
              + capacity_reservation_id = (known after apply)
            }
        }

      + ebs_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + snapshot_id           = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }

      + enclave_options {
          + enabled = (known after apply)
        }

      + ephemeral_block_device {
          + device_name  = (known after apply)
          + no_device    = (known after apply)
          + virtual_name = (known after apply)
        }

      + metadata_options {
          + http_endpoint               = (known after apply)
          + http_put_response_hop_limit = (known after apply)
          + http_tokens                 = (known after apply)
        }

      + network_interface {
          + delete_on_termination = (known after apply)
          + device_index          = (known after apply)
          + network_interface_id  = (known after apply)
        }

      + root_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }
    }

    #aws_instance.TestUbuntu["t3.micro"] will be created
    + resource "aws_instance" "TestUbuntu" {
      + ami                                  = "ami-0e5e17317f99b2932"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t3.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = (known after apply)
      + monitoring                           = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + placement_partition_number           = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Name" = "MyAWS t3.micro"
        }
      + tags_all                             = {
          + "Name" = "MyAWS t3.micro"
        }
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + vpc_security_group_ids               = (known after apply)

      + capacity_reservation_specification {
          + capacity_reservation_preference = (known after apply)

          + capacity_reservation_target {
              + capacity_reservation_id = (known after apply)
            }
        }

      + ebs_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + snapshot_id           = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }

      + enclave_options {
          + enabled = (known after apply)
        }

      + ephemeral_block_device {
          + device_name  = (known after apply)
          + no_device    = (known after apply)
          + virtual_name = (known after apply)
        }

      + metadata_options {
          + http_endpoint               = (known after apply)
          + http_put_response_hop_limit = (known after apply)
          + http_tokens                 = (known after apply)
        }

      + network_interface {
          + delete_on_termination = (known after apply)
          + device_index          = (known after apply)
          + network_interface_id  = (known after apply)
        }

      + root_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }
    }
