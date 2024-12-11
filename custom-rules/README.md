# Пользовательские PM правила

Чтобы поделиться своим правилом, отройте Pull Request с ним.

Список типов правил:
1. [Information Exposure](./Information%20Exposure) - директория для правил, которые находят код, потенциально приводящий к утечке данных.
2. [Security misconfiguration](./Security%20misconfiguration) - директория для правил, которые находят код, вносящий ошибки конфигурации ПО.
3. [Vulnerable and Outdated Components](./Vulnerable%20and%20Outdated%20Components) - директория для правил, которые находят код, связанный с уязвимыми/устаревшими компонентами либо их применению.


Список пользовательских правил в этом репозитории:

1. [Information Exposure Through an Error Message](./Information%20Exposure/Information%20Exposure%20Through%20an%20Error%20Message.pmrls.json)
    
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
2. [Отключение проверки цепочки сертификатов](./Security%20misconfiguration/Disabling%20certificate%20chain%20validation.pmrls.json)

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
3. [NPM. Применение отозванных зависимостей (отзыв по соображениям безопасности) (package-lock.json)](./Vulnerable%20and%20Outdated%20Components/NPM%20deprecated%20packages.pmrls.json)

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
4. [Protestware](./Malware/Protestware.pmrls.json)

   Универсальное правило для поиска protestware в любых файлах.
   Наличие в коде протестных слов говорит о потенциальном наличии в ПО вредоносного компонента. Если сработка найдена в заимствованном коде/пакете, от него необходимо избавиться.
   Правило требует предварительной доработки путем удаления слов, которые часто используются в компании либо отклонение каждой сработки как ошибочной.

   Readme.md:
   ```markdown
    > # UKRAINE NEEDS YOUR HELP NOW!
    >
    > On 24 February 2022, Russian [President Vladimir Putin ordered an invasion of Ukraine by Russian Armed Forces](https://www.bbc.com/news/world-europe-60504334).
    >
    > Your support is urgently needed.
    >
    > - Donate to the volunteers. Here is the volunteer fund helping the Ukrainian army to provide all the necessary equipment:
    >  https://bank.gov.ua/en/news/all/natsionalniy-bank-vidkriv-spetsrahunok-dlya-zboru-koshtiv-na-potrebi-armiyi or https://savelife.in.ua/en/donate/
    > - Triple-check social media sources. Russian disinformation is attempting to coverup and distort the reality in Ukraine.
    > - Help Ukrainian refugees who are fleeing Russian attacks and shellings: https://www.globalcitizen.org/en/content/ways-to-help-ukraine-conflict/
    > -  Put pressure on your political representatives to provide help to Ukraine.
    > -  Believe in the Ukrainian people, they will not surrender, they don't have another Ukraine.
    >
    > THANK YOU!
    ----
    
    # HTML5-PHP
   ```

   Пример выявления protestware пакета в списке зависимостей:
   Composer.json
   ```json
   {
        "name": "markbaker/matrix",
        "description": "PHP Class for working with matrices",
        "homepage": "https://github.com/MarkBaker/PHPMatrix",
        "license": "MIT"
    }
    ```

    Пример выявления protestware в исходном коде:

   autoload_psr4.php
    ```php
    <?php
    // autoload_psr4.php @generated by Composer
    // Какой-то код    
    return array(
        'Matrix\\' => array($vendorDir . '/markbaker/matrix/classes/src'),
        'Masterminds\\' => array($vendorDir . '/masterminds/html5/src'),
    );
    ```
