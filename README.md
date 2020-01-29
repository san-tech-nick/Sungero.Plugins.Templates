# Sungero.Plugins.Templates
В этом репозитории находятся шаблоны проектов для разработки плагина подписания к системе DirectumRX.
Шаблоны проектов созданы в Microsoft Visual Studio 2017. 

### Как разработать плагин подписания
1. В проектах `ServerCryptographyPlugin`, `ClientCryptographyPlugin` измените имя сборки на свое (например, на `MyServerCryptographyPlugin`, `MyClientCryptographyPlugin`)
2. Сгенерируйте уникальный идентификатор (Guid) плагина (например на сайте https://www.guidgenerator.com/), пропишите его в свойстве `CryptographyPlugin.Id` класса серверного плагина и в файле `ClientPlugin.targets` клиентского плагина;
3. Реализуйте **серверный плагин**. Для этого: 
    * Реализуйте методы класса `Signer`: `SignData()`, `TryLoadPrivateKey()` и `VerifySignature()`. При необходимости модифицируйте остальные методы класса.
    * При необходимости модифицируйте методы класса `CryptographyPlugin`. Укажите нужный идентификатор алгоритма подписания в данном классе (поле `SignAlgorithmId`).
    * При необходимости создайте свой алгоритм хеширования с помощью класса `HashAlhorithmExample`.
4. Реализуйте **клиентский плагин**. Для этого:
    * Модифицируйте методы класса `Signer`. Укажите нужный идентификатор алгоритма подписания в данном классе (поле `AlgorithmId`).
5. Соберите проект. В папке *out* в корне проекта появятся папки *Client* и *Server*, содержащие файлы клиентского и серверного плагинов соответственно.
6. Дополнительные библиотеки, требующие распространения вместе с плагином, включите в соответствующий проект. Они автоматически должны попасть в zip-архив. 
7. Подключите серверный плагин к DirectumRX:
    * Создайте папку для хранения плагина, например, *D:\Plugins*. При обновлении системы DirectumRX может изменяться содержимое ее папок. Поэтому рекомендуется создать отдельную папку.
    * В конфигурационных файлах *_ConfigSettings.xml* всех серверных компонент в параметре PLUGINS_ZIP_PATH укажите путь к папке с плагинами, например:  
    ```<var name="PLUGINS_ZIP_PATH" value="D:\Plugins" />```
    * Скопируйте архив с серверным плагином в указанную папку.
    * При необходимости передачи настроек в плагин, их нужно прописать в тех же конфигурационных файлах *_ConfigSettings.xml*, где производилась настройка пути к папке с плагинами. Формат секции с настройками: 
      ```
      <block name="PLUGINS">
        <plugin id="<ид_плагина>"
          exampleSetting="Example value"
          otherSetting="Other value" />
      </block>
      ```
    Чтение настроек в плагине выполняется в методе `CryptographyPlugin.ApplySettings()`.
8. Подключите клиентский плагин к DirectumRX. Клиентский плагин используется в веб-агенте при работе веб-клиента DirectumRX:
    * Скопируйте файлы из папки *out\Client* в папку плагинов веб-агента на сервере приложений, например, в  
    ```C:\inetpub\wwwroot\Client\content\WebAgent\plugins\```
    * Запустите утилиту *packages_manifest_updater.exe* из папки *PackagesManifestUpdater* веб-агента на сервере приложений, например, из  
    ```C:\inetpub\wwwroot\Client\content\WebAgent\PackagesManifestUpdater```
### Особенности формирования и проверки электронной подписи в DirectumRX
* Базовая информация об использовании электронной подписи приведена в документации к системе.
* Плагины подписания поддерживаются, начиная с DirectumRX 3.3. Ограничения:
    * Плагины подписания не поддерживаются в десктоп-клиенте.
    * Клиентский плагин подписания поддерживается в Microsoft Windows 7 и выше, Microsoft Windows Server 2008 R2 и выше.
* Электронная подпись формируется в формате CAdES-BES.
* При подписании сертификатом в веб-клиенте само подписание выполняется на стороне клиента, но данные для подписи (подписываемые атрибуты подписи) формируются на сервере приложений.
* Сервер приложений работает в 64-битном окружении, а веб-агент - в 32-битном. Это необходимо учитывать, если для работы плагинов нужны COM-компоненты и их регистрация.
* При необходимости подписание может быть выполнено на стороне сервера. В этом случае закрытый ключ должен быть доступен на сервере.
