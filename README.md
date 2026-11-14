# Ответ к заданию «Введение в Terraform» - hw-01.md

### Задание 1

1. Ответ не требуется.

2. Личную конфиденциальную информацию (логины, пароли, ключи, токены) согласно .gitignore допустимо хранить в файле personal.auto.tfvars

3. c99VldfOVAF7MbKH

4. В строке `resource "docker_image" {` пропущено имя. Правильные вариант: `resource "docker_image" "nginx" {`

В строке `resource "docker_container" "nginx" {` имя не должно начинаться с цифры. Правильный вариант: `resource "docker_container" "nginx" {`

В строке `name = "example_${random_password.random_string_FAKE.resulT}"` _FAKE является лишним, так как правильное имя ресурса - `random_string`.
Также `resulT` является недопустимый именем атрибута `result`.
Правильный ваирант: `name  = "example_${random_password.random_string.result}"`

5. Исправленный фрагмент кода:
```
resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = true
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "example_${random_password.random_string.result}"

  ports {
    internal = 80
    external = 9090
  }
}
```

Вывод команды `docker ps`:
```
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                  NAMES
0870de3f3f04   165ddde8751e   "/docker-entrypoint.…"   21 seconds ago   Up 20 seconds   0.0.0.0:9090->80/tcp   example_c99VldfOVAF7MbKH
```

6. Из-за ключа `-auto-approve` после изменения имени на hello_world мы не сможем среагировать на следующее предупреждение `# docker_container.nginx must be replaced` и удалим (заменим) старый контейнер, который, возможно, не хотели бы удалять. Опасность ключа заключается в том, что Terraform применяет изменения автоматически, без запроса подтверждения от пользователя, что при ошибке в конфигурации может привести к ошибке в работе инфраструкутуры или потере данных. Ключ `-auto-approve` используется для ситуаций, когда нет возможности ручного ввода (в скриптах, CI/CD-пайплайнах).

7. Уничтожил ресурсы с помощью `terraform destroy`. Убедился, что ресурсы удалены с помощью `terraform state list`. Содержимоей файла terraform.tfstate: 
```
{
  "version": 4,
  "terraform_version": "1.12.2",
  "serial": 11,
  "lineage": "93d9036e-0dc5-b05d-b9be-26edccf8056d",
  "outputs": {},
  "resources": [],
  "check_results": null
}
```

8. Потому что к ресурсу с именем nginx:latest был применён параметр `keep_locally = true`, который указывает Terraform сохранить Docker-образ локально даже после выполнения terraform destroy.

Строчка из документации, которая об этом рассказывает:

keep_locally (Boolean) If true, then the Docker image won't be deleted on destroy operation. If this is false, it will delete the image from the docker local storage on destroy operation.