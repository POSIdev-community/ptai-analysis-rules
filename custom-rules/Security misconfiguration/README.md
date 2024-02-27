## Security misconfiguration

Директория для правил, которые находят код, вносящий ошибки конфигурации ПО.

Список правил:

1. [Отключение проверки цепочки сертификатов](./Отключение%20проверки%20цепочки%20сертификатов.json)

   Универсальное правило для всех языков и типов конфигурационных файлов.
   Отключение проверки цепочки сертификатов при установлении HTTPS-соединения помогает злоумышленникам вклиниться в канал связи и управлять данными либо читать их.
   Параметр `SSL Verify` не должен иметь значение `false`. См. [OWASP TOP 10:A5](https://owasp.org/Top10/A05_2021-Security_Misconfiguration).

   Python:
    ```python
    # Disable SSL verify
    session = Session()
    session.verify = False
    # -----------
    # Clone repo (example № 2)
    clone_command = ["git", "-c", "http.sslVerify=false", "clone"]
   ```

   YAML:
   ```yaml
   - script: |
      git config http.sslVerify "false"
   ```
