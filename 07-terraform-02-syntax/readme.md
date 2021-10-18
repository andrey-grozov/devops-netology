## Домашнее задание к занятию "7.2. Облачные провайдеры и синтаксис Terraform."
Зачастую разбираться в новых инструментах гораздо интересней понимая то, как они работают изнутри. Поэтому в рамках первого необязательного задания предлагается завести свою учетную запись в AWS (Amazon Web Services) или Yandex.Cloud. Идеально будет познакомится с обоими облаками, потому что они отличаются.

#### Задача 1 (вариант с AWS). Регистрация в aws и знакомство с основами (необязательно, но крайне желательно).
Остальные задания можно будет выполнять и без этого аккаунта, но с ним можно будет увидеть полный цикл процессов.

AWS предоставляет достаточно много бесплатных ресурсов в первых год после регистрации, подробно описано здесь.

    Создайте аккаут aws.
    Установите c aws-cli https://aws.amazon.com/cli/.
    Выполните первичную настройку aws-sli https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html.
    Создайте IAM политику для терраформа c правами
    AmazonEC2FullAccess
    AmazonS3FullAccess
    AmazonDynamoDBFullAccess
    AmazonRDSFullAccess
    CloudWatchFullAccess
    IAMFullAccess
    Добавьте переменные окружения
    export AWS_ACCESS_KEY_ID=(your access key id)
    export AWS_SECRET_ACCESS_KEY=(your secret access key)

Создайте, остановите и удалите ec2 инстанс (любой с пометкой free tier) через веб интерфейс.
В виде результата задания приложите вывод команды aws configure list.

    aws> configure list
          Name                    Value             Type    Location
          ----                    -----             ----    --------
       profile                <not set>             None    None
    access_key     ****************G25U              env
    secret_key     ****************xTfq              env
        region                <not set>             None    None

#### Задача 1 (Вариант с Yandex.Cloud). Регистрация в aws и знакомство с основами (необязательно, но крайне желательно).

Подробная инструкция на русском языке содержится здесь.
Обратите внимание на период бесплатного использования после регистрации аккаунта.
Используйте раздел "Подготовьте облако к работе" для регистрации аккаунта. Далее раздел "Настройте провайдер" для подготовки базового терраформ конфига.
Воспользуйтесь инструкцией на сайте терраформа, что бы не указывать авторизационный токен в коде, а терраформ провайдер брал его из переменных окружений.

#### Задача 2. Созданием aws ec2 или yandex_compute_instance через терраформ.

В каталоге terraform вашего основного репозитория, который был создан в начале курсе, создайте файл main.tf и versions.tf.

Зарегистрируйте провайдер для aws. В файл main.tf добавьте блок provider, а в versions.tf блок terraform с вложенным блоком required_providers. Укажите любой выбранный вами регион внутри блока provider.
либо для yandex.cloud. Подробную инструкцию можно найти здесь.
Внимание! В гит репозиторий нельзя пушить ваши личные ключи доступа к аккаунту. Поэтому в предыдущем задании мы указывали их в виде переменных окружения.
В файле main.tf воспользуйтесь блоком data "aws_ami для поиска ami образа последнего Ubuntu.
В файле main.tf создайте рессурс либо ec2 instance. Постарайтесь указать как можно больше параметров для его определения. Минимальный набор параметров указан в первом блоке Example Usage, но желательно, указать большее количество параметров.
либо yandex_compute_image.

Также в случае использования aws:
Добавьте data-блоки aws_caller_identity и aws_region.
В файл outputs.tf поместить блоки output с данными об используемых в данный момент:
AWS account ID,
AWS user ID,
AWS регион, который используется в данный момент,
Приватный IP ec2 инстансы,
Идентификатор подсети в которой создан инстанс.

Если вы выполнили первый пункт, то добейтесь того, что бы команда terraform plan выполнялась без ошибок.

Вывод terraform plan
    
    vagrant@vagrant:/vagrant/aws$ terraform plan -out myplan
    aws_vpc.my_vpc: Refreshing state... [id=vpc-0d2159bc82166bd26]
    aws_instance.TestUbuntu: Refreshing state... [id=i-05351146f9b397cb2]
    aws_subnet.my_subnet: Refreshing state... [id=subnet-094989cc384bfb999]
    
    Changes to Outputs:
      + user_id = "AIDASXQTORLPZYPIUOGXM"
    
    You can apply this plan to save these new output values to the Terraform state, without changing any real infrastructure.
    
    ──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
    
    Saved the plan to: myplan
    
    To perform exactly these actions, run the following command to apply:
        terraform apply "myplan"

Вывод terraform apply

    vagrant@vagrant:/vagrant/aws$ terraform apply
    aws_vpc.my_vpc: Refreshing state... [id=vpc-0d2159bc82166bd26]
    aws_instance.TestUbuntu: Refreshing state... [id=i-05351146f9b397cb2]
    aws_subnet.my_subnet: Refreshing state... [id=subnet-094989cc384bfb999]
    
    No changes. Your infrastructure matches the configuration.
    
    Terraform has compared your real infrastructure against your configuration and found no differences, so no changes are needed.
    
    Apply complete! Resources: 0 added, 0 changed, 0 destroyed.
    
    Outputs:
    
    account_id = "187945618143"
    instance_ip_addr = "172.31.7.162"
    instance_region = "us-east-2"
    instance_subnet = "subnet-094989cc384bfb999"
    user_id = "AIDASXQTORLPZYPIUOGXM"
В качестве результата задания предоставьте:

Ответ на вопрос: при помощи какого инструмента (из разобранных на прошлом занятии) можно создать свой образ ami?
    
    можно использовать CloudFormation    

Ссылку на репозиторий с исходной конфигурацией терраформа.

    https://github.com/andrey-grozov/devops-netology/tree/main/terraform
