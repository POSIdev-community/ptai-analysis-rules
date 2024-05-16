# Пользовательские PM правила

Чтобы поделиться своим правилом, отройте Pull Request с ним.

Список типов правил:
1. [Information Exposure](./Information%20Exposure) - директория для правил, которые находят код, потенциально приводящий к утечке данных.
1. [Security misconfiguration](./Security%20misconfiguration) - директория для правил, которые находят код, вносящий ошибки конфигурации ПО.
1. [Vulnerable and Outdated Components](./Vulnerable%20and%20Outdated%20Components) - директория для правил, которые находят код, связанный с уязвимыми/устаревшими компонентами либо их применению.


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
1. [NPM. Применение отозванных зависимостей (отзыв по соображениям безопасности) (package-lock.json)](./Vulnerable%20and%20Outdated%20Components/NPM-deprecated_packages.json)

    Разработчик отозвал зависимость. Это может быть по разным причинам, например, разработчик более не развивает проект из-за отсутствия заинтересованности либо в новых версиях фреймворка/языка функционал используемой зависимости полностью или частично уже реализован, либо зависимость содержит уязвимость, которая устранена в следующих версиях.
В случае выявления уязвимости в данной зависимости, она не будет устранена, использующее данную зависимость ПО при таком событии будет уязвимо к атакам хакеров.

    Рекомендация: отказаться от использования указанной зависимости и перейти на другую согласно заявлению разработчика либо реализовать функционал зависимости самостоятельно, чтобы избежать указанной ситуации. См. [OWASP TOP 10:A6](https://owasp.org/Top10/A06_2021-Vulnerable_and_Outdated_Components).
   Пример сработки правила:

   package-lock.json:
   ```json
    {
        "node_modules/axios": {
            "version": "0.19.2",
            "resolved": "https://registry.npmjs.org/axios/-/axios-0.19.2.tgz",
            "integrity": "sha512-fjgm5MvRHLhx+osE2xoekY70AhARk3a6hkN+3Io1jc00jtquGvxYlKlsFUhmUET0V5te6CcZI7lcv2Ym61mjHA==",
            "deprecated": "Critical security vulnerability fixed in v0.21.1. For more information, see https://github.com/axios/axios/pull/3410",
            "dependencies": {
                "follow-redirects": "1.5.10"
            }
        }
    }
   ```
