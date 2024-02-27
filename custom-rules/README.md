# Пользовательские PM правила

Чтобы поделиться своим правилом, отройте Pull Request с ним.

Список типов правил:
1. [Information Exposure](./Information%20Exposure) - директория для правил, которые находят код, потенциально приводящий к утечке данных.
1. [Security misconfiguration](./Security%20misconfiguration) - директория для правил, которые находят код, вносящий ошибки конфигурации ПО.


Список пользовательских правил в этом репозитории:

1. [Information Exposure Through an Error Message](./Information%20Exposure/Information%20Exposure%20Through%20an%20Error%20Message.json)
    
    Правило для примера оформления. Оно срабатывает при вызове функции с именем `printStackTrace()`
    на произвольной переменной. Так как функция используется для получения стек-трейся исключения, 
    ее неосторожное использование может привести к утечкам чувствительной информации такой как структура
    кода. Правило сработает на следующем примере кода:
    
    ```kotlin
    fun handler() {
        try {
            throwException()
        } catch (e: Exception) {
            e.printStackTrace()
        }
    } 
   ```
1. [Отключение проверки цепочки сертификатов](./Security%20misconfiguration/Отключение%20проверки%20цепочки%20сертификатов.json)

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
