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
      instances = {
        "t3.micro" = data.aws_ami.amazon_linux.id
        "t2.micro" = data.aws_ami.amazon_linux.id
      }
    }    

    resource "aws_instance" "TestUbuntu" {
      for_each = local.instances
      ami      = each.value
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
