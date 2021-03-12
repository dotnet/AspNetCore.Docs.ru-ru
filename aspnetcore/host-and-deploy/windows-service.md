---
title: Размещение ASP.NET Core в службе Windows
author: rick-anderson
description: Узнайте, как разместить приложение ASP.NET Core в службе Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/windows-service
ms.openlocfilehash: e5c7dd0e52f1246d3ac6ad9622573db4c276654b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588819"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Размещение ASP.NET Core в службе Windows

::: moniker range=">= aspnetcore-3.0"

Приложение ASP.NET Core можно разместить в Windows в качестве [службы Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) без использования IIS. При размещении в качестве службы Windows приложение автоматически запускается после перезагрузки сервера.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Предварительные требования

* [Пакет SDK для ASP.NET Core 2.1 или более поздней версии](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 или более поздней версии](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a>Шаблон службы рабочей роли

Шаблон службы рабочей роли ASP.NET Core может служить отправной точкой для написания длительно выполняющихся приложений служб. Чтобы использовать шаблон в качестве основы для приложения службы Windows, выполните указанные ниже действия.

1. Создайте приложение службы рабочей роли на основе шаблона .NET Core.
1. Согласно указаниям в разделе [Конфигурация приложения](#app-configuration) измените приложение службы рабочей роли так, чтобы оно могло выполняться как служба Windows.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a>Конфигурация приложения

Приложению требуется ссылка на пакет для [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).

`IHostBuilder.UseWindowsService` вызывается при сборке узла. Если приложение выполняется как служба Windows, метод отвечает за следующие действия:

* Задает для узла время существования `WindowsServiceLifetime`.
* Задает для [корневого каталога содержимого](xref:fundamentals/index#content-root) значение [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory). Дополнительные сведения см. в разделе [Текущий каталог и корневой каталог содержимого](#current-directory-and-content-root).
* Активирует ведение журнала событий.
  * В качестве имени источника по умолчанию используется имя приложения.
  * Уровень ведения журнала по умолчанию — как минимум *Предупреждение* для приложения на базе шаблона ASP.NET Core, вызывающего `CreateDefaultBuilder` для сборки узла.
  * Уровень ведения журнала по умолчанию переопределяется с помощью ключа `Logging:EventLog:LogLevel:Default` в *appsettings.json* /*appsettings.{Environment}.json* или в другом поставщике конфигурации.
  * Только администраторы могут создавать источники событий. Если источник событий создать нельзя, используя имя приложения, для источника *Приложение* регистрируется предупреждение и журналы событий отключаются.

В разделе `CreateHostBuilder` файла *Program.cs*:

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

Этот раздел сопровождают следующие примеры приложений:

* Пример фоновой службы рабочих ролей. Пример приложения, не являющегося веб-приложением, на основе [шаблона службы рабочих ролей](#worker-service-template), который использует [размещенные службы](xref:fundamentals/host/hosted-services) для фоновых задач.
* Пример службы веб-приложений. Пример веб-приложения Razor Pages, который выполняется как служба Windows с [размещенными службами](xref:fundamentals/host/hosted-services) для фоновых задач.

Рекомендации по MVC см. в статьях <xref:mvc/overview> и <xref:migration/22-to-30>.

## <a name="deployment-type"></a>Тип развертывания

Дополнительные сведения и рекомендации по сценариям развертывания см. в статье [Развертывание приложений .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

Для службы на основе веб-приложений, которая использует платформы MVC или Razor Pages, укажите веб-пакет SDK в файле проекта.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Если служба выполняет только фоновые задачи (например, [размещенные службы](xref:fundamentals/host/hosted-services)), укажите пакет SDK рабочей роли в файле проекта:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Зависящее от платформы развертывание (FDD)

Зависящее от платформы развертывание (FDD) требует наличия в целевой системе общей для всей системы версии .NET Core. Если сценарий с FDD используется согласно инструкций в этой статье, пакет SDK создаcт исполняемый файл ( *.exe*), который называется *исполняемым файлом, зависящим от платформы*.

При использовании [веб-пакета SDK](#sdk), файл *web.config*, который обычно создается при публикации приложения ASP.NET Core, не требуется для приложения служб Windows. Отмените создание файла *web.config*, добавив свойство `<IsTransformWebConfigDisabled>` со значением `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Автономное развертывание

Для автономного развертывания необязательно наличие общей платформы в системе размещения. Среда выполнения и зависимости приложения развертываются с приложением.

[Идентификатор среды выполнения](/dotnet/core/rid-catalog) Windows включается в `<PropertyGroup>`, где содержится целевая платформа:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Чтобы выполнить публикацию для нескольких идентификаторов RID, сделайте следующее.

* Укажите список идентификаторов RID, разделив их точкой с запятой.
* Укажите имя свойства [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (множественное число).

Дополнительные сведения см. в [каталоге RID для .NET Core](/dotnet/core/rid-catalog).

## <a name="service-user-account"></a>Учетная запись пользователя службы

Создайте для службы учетную запись пользователя с помощью командлета [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) в административной оболочке PowerShell 6.

Обновление Windows 10 за октябрь 2018 г. (версия 1809, сборка 10.0.17763) или более поздней версии:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Версия Windows, предшествующая обновлению Windows 10 за октябрь 2018 г. (версия 1809, сборка 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Укажите [надежный пароль](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) при появлении соответствующего запроса.

Параметр срока действия учетной записи <xref:System.DateTime> можно задать с помощью параметра `-AccountExpires`, передаваемого в командлет [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser).

Дополнительные сведения см. в статьях [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) и [Service User Accounts](/windows/desktop/services/service-user-accounts) (Учетные записи пользователей служб).

Альтернативный подход к управлению пользователями при работе с Active Directory заключается в применении управляемых учетных записей служб. Дополнительные сведения см. в [обзоре групповых управляемых учетных записей службы](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Права на вход в качестве службы

Чтобы настроить право *Вход в качестве службы* для учетной записи пользователя службы, сделайте следующее:

1. Откройте редактор локальной политики безопасности, запустив *secpol.msc*.
1. Разверните узел **Локальные политики** и выберите **Назначение прав пользователя**.
1. Откройте политику **Вход в качестве службы**.
1. Щелкните **Добавить пользователя или группу**.
1. Укажите имя объекта (учетная запись пользователя) одним из следующих способов:
   1. Укажите учетную запись пользователя (`{DOMAIN OR COMPUTER NAME\USER}`) в поле для имени объекта и нажмите **ОК**, чтобы назначить политику пользователю.
   1. Выберите **Дополнительно**. Нажмите **Найти**. Выберите учетную запись пользователя из списка. Нажмите кнопку **ОК**. Нажмите **ОК** еще раз, чтобы назначить политику пользователю.
1. Нажмите **ОК** или **Применить**, чтобы сохранить изменения.

## <a name="create-and-manage-the-windows-service"></a>Создание службы Windows и управление ею

### <a name="create-a-service"></a>Создание службы

Зарегистрируйте службу с помощью команды PowerShell. В административной оболочке PowerShell 6 выполните следующие команды:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`. Путь к папке приложения на узле (например, `d:\myservice`). Не включайте исполняемый файл приложения в путь. Завершающая косая черта не требуется.
* `{DOMAIN OR COMPUTER NAME\USER}`. Учетная запись пользователя службы (например, `Contoso\ServiceUser`).
* `{SERVICE NAME}`. Имя службы (например, `MyService`).
* `{EXE FILE PATH}`. Путь к исполняемому файлу приложения (например, `d:\myservice\myservice.exe`). Включите имя исполняемого файла с расширением.
* `{DESCRIPTION}`. Описание службы (например, `My sample service`).
* `{DISPLAY NAME}`. Отображаемое имя службы (например, `My Service`).

### <a name="start-a-service"></a>Запуск службы

Запустите службу с помощью следующей команды PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Команде потребуется несколько секунд, чтобы запустить службу.

### <a name="determine-a-services-status"></a>Определение состояния службы

Чтобы проверить состояние службы, используйте следующую команду PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Состояние отображается одним из следующих значений:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Остановка службы

Остановите службу с помощью следующей команды PowerShell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Удаление службы

После небольшой задержки для остановки службы удалите службу с помощью следующей команды Powershell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a>Сценарии использования прокси-сервера и подсистемы балансировки нагрузки

Для служб, которые взаимодействуют с запросами из Интернета или корпоративной сети и размещаются за прокси-сервером или подсистемой балансировки нагрузки, может потребоваться дополнительная настройка. Для получения дополнительной информации см. <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Настройка конечных точек

По умолчанию платформа ASP.NET Core привязана к `http://localhost:5000`. Настройте URL-адрес и порт, задав переменную среды `ASPNETCORE_URLS`.

Дополнительные сведения о подходах к настройке URL-адресов и портов см. в соответствующей статье сервера:

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <xref:fundamentals/servers/kestrel/endpoints>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

* <xref:fundamentals/servers/kestrel#endpoint-configuration>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <xref:fundamentals/servers/httpsys#configure-windows-server>

В предыдущем руководстве рассматривается поддержка конечных точек HTTPS. Например, настройте приложение для HTTPS, если проверка подлинности используется со службой Windows.

> [!NOTE]
> Использование сертификата разработки ASP.NET Core HTTPS для защиты конечной точки службы не поддерживается.

## <a name="current-directory-and-content-root"></a>Текущий каталог и корневой каталог содержимого

Для службы Windows <xref:System.IO.Directory.GetCurrentDirectory%2A> возвращает текущий рабочий каталог *C:\\WINDOWS\\system32*. Папка *system32* не подходит для хранения файлов службы (например, файлов параметров). Используйте один из следующих методов для сохранения ресурсов и файлов параметров службы и доступа к ним.

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Использование ContentRootPath или ContentRootFileProvider

Используйте [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) или <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> для поиска ресурсов приложения.

Когда приложение запускается как служба, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> задает для свойства <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> значение [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).

Файлы стандартных параметров приложения *appsettings.json* и *appsettings.{Environment}.json* загружаются из корневого каталога содержимого приложения путем вызова [CreateDefaultBuilder во время создания узла](xref:fundamentals/host/generic-host#set-up-a-host).

Для других файлов параметров, загруженных с помощью кода разработчика в <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>, не нужно вызывать <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>. В следующем примере файл *custom_settings. JSON* существует в корневом каталоге содержимого приложения и загружается без явного указания базового пути:

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

Не пытайтесь использовать <xref:System.IO.Directory.GetCurrentDirectory%2A>, чтобы получить путь к ресурсу, так как приложение службы Windows возвращает папку *C:\\WINDOWS\\system32* в качестве текущего каталога.

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Хранение файлов службы в подходящем расположении на диске

Укажите абсолютный путь к папке, содержащей файлы, с помощью <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> при использовании <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>.

## <a name="troubleshoot"></a>Устранение неполадок

Сведения об устранении неполадок в приложении службы Windows см. в статье <xref:test/troubleshoot>.

### <a name="common-errors"></a>Распространенные ошибки

* Используется старая или предварительная версия PowerShell.
* Зарегистрированная служба не использует **опубликованные** выходные данные приложения, возвращенные командой [dotnet publish](/dotnet/core/tools/dotnet-publish). Выходные данные команды [dotnet build](/dotnet/core/tools/dotnet-build) не поддерживаются для развертывания приложений. В зависимости от типа развертывания опубликованные ресурсы находятся в одной из следующих папок:
  * *bin/Release/{TARGET FRAMEWORK}/publish* (зависящее от платформы развертывание);
  * *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (автономное развертывание).
* Служба не находится в состоянии выполнения.
* Пути к используемым приложением ресурсам (например, к сертификатам) неверные. Базовый путь к службе Windows — *c:\\Windows\\System32*.
* У пользователя отсутствуют права для *входа в систему в качестве службы*.
* Пароль был указан неверно или его срок действия истек при выполнении команды PowerShell `New-Service`.
* Для приложения требуется выполнить проверку подлинности в ASP.NET Core, однако оно не настроено для безопасных подключений (HTTPS).
* Порт URL-адреса запроса неправильно указан или настроен в приложении.

### <a name="system-and-application-event-logs"></a>Журналы событий системы и приложений

Получите доступ к журналам событий системы и приложений:

1. Откройте меню "Пуск", выполните поиск по фразе *Просмотр событий* и выберите приложение **Просмотр событий**.
1. В **средстве просмотра событий** откройте узел **Журналы Windows**.
1. Выберите **Система**, чтобы открыть журнал системных событий. Выберите **Приложение**, чтобы открыть журнал событий приложения.
1. Проверьте здесь наличие ошибок, связанных с проблемным приложением.

### <a name="run-the-app-at-a-command-prompt"></a>Запуск приложения в командной строке

Многие ошибки запуска не создают полезные сведения в журналах событий. Для некоторых ошибок можно найти причину, запустив приложение в командной строке на компьютере размещения. Чтобы записать дополнительные сведения из приложения, снизьте [уровень ведения журнала](xref:fundamentals/logging/index#log-level) или запустите приложение в [среде разработки](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Очистка кэшей пакетов

Приложения-функции могут перестать работать сразу после обновления пакета SDK для .NET Core на компьютере разработки или обновления версии пакетов в самом приложении. В некоторых случаях в результате важного обновления несогласованные версии пакетов могут привести к нарушению работы приложения. Большинство этих проблем можно исправить следующим образом:

1. Удалите папки *bin* и *obj*.
1. Очистите кэши пакетов, выполнив команду [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) из командной оболочки.

   Очистку кэшей пакетов также можно выполнить с помощью средства [nuget.exe](https://www.nuget.org/downloads) или команды `nuget locals all -clear`. *NuGet.exe* не входит в пакет установки операционной системы Windows для настольных компьютеров и его нужно получить отдельно на [веб-сайте NuGet](https://www.nuget.org/downloads).

1. Восстановите и перестройте проект.
1. Удалите все файлы из папки развертывания на сервере, прежде чем повторно развернуть приложение.

### <a name="slow-or-hanging-app"></a>Медленное или зависающее приложение

*Аварийный дамп* — это моментальный снимок системной памяти, который может помочь определить причину аварийного завершения, сбоя запуска или медленной работы приложения.

#### <a name="app-crashes-or-encounters-an-exception"></a>Аварийное завершение работы приложения или исключение

Получите и проанализируйте дамп из [отчетов об ошибках Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Создайте папку для хранения файлов аварийного дампа в `c:\dumps`.
1. Запустите [скрипт PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) с именем исполняемого файла приложения:

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Запустите приложение в условиях, вызывающих аварийное завершение.
1. После аварийного завершения запустите [скрипт PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

Когда приложение аварийно завершит работу и сбор дампов будет выполнен, приложение сможет завершить работу обычным образом. Скрипт PowerShell настраивает отчеты об ошибках Windows для сбора до пяти дампов для приложения.

> [!WARNING]
> Аварийные дампы могут занимать много места на диске (до нескольких гигабайтов каждый).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Приложение перестает отвечать на запросы, не запускается или работает в обычном режиме

Когда приложение *перестает отвечать на запросы* (но аварийное завершение не происходит), не запускается или работает в обычном режиме, см. раздел [Файлы дампа пользовательского режима: выбор лучшего инструмента](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), чтобы выбрать подходящий инструмент для создания дампа.

#### <a name="analyze-the-dump"></a>Анализ дампа

Дамп можно проанализировать несколькими способами. Дополнительные сведения см. в разделе [Анализ файла дампа пользовательского режима](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Дополнительные ресурсы

::: moniker-end

::: moniker range=">= aspnetcore-5.0"
* [Конфигурация конечных точек Kestrel](xref:fundamentals/servers/kestrel/endpoints) (включает конфигурацию HTTPS и поддержку SNI)
::: moniker-end
::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"
* [Конфигурация конечных точек Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (включает конфигурацию HTTPS и поддержку SNI)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Приложение ASP.NET Core можно разместить в Windows в качестве [службы Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) без использования IIS. При размещении в качестве службы Windows приложение автоматически запускается после перезагрузки сервера.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Предварительные требования

* [Пакет SDK для ASP.NET Core 2.1 или более поздней версии](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 или более поздней версии](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Конфигурация приложения

Приложение требует ссылки на пакеты для [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) и [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Для тестирования и отладки при работе вне службы добавьте код, чтобы определить, как выполняется приложение: в качестве службы или консольного приложения. Проверьте, присоединен ли отладчик или присутствует ли параметр `--console`. Если одно из условий имеет значение true (приложение выполняется не в качестве службы), вызовите <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Если условия имеют значение false (приложение выполняется в качестве службы), сделайте следующее:

* Вызовите <xref:System.IO.Directory.SetCurrentDirectory*> и используйте путь к расположению для публикации приложения. Не вызывайте <xref:System.IO.Directory.GetCurrentDirectory*> для получения пути, так как при вызове <xref:System.IO.Directory.GetCurrentDirectory*> приложение службы Windows возвращает папку *C:\\WINDOWS\\system32*. Дополнительные сведения см. в разделе [Текущий каталог и корневой каталог содержимого](#current-directory-and-content-root). Этот шаг выполняется до того, как приложение будет настроено в `CreateWebHostBuilder`.
* Вызовите <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>, чтобы запустить приложение в качестве службы.

Так как [поставщик конфигурации командной строки](xref:fundamentals/configuration/index#command-line-configuration-provider) требует указания пар "имя-значение" для аргументов командной строки, параметр `--console` удаляется из аргументов, прежде чем <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> получит их.

Для записи данных в журнал событий Windows добавьте поставщик EventLog в <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Задайте уровень ведения журнала с помощью ключа `Logging:LogLevel:Default` в файле *appsettings.Production.json*.

В следующем примере `RunAsCustomService` вызывается вместо <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> для обработки событий времени существования в приложении. Дополнительные сведения см. в разделе [Обработка событий запуска и остановки](#handle-starting-and-stopping-events).

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Тип развертывания

Дополнительные сведения и рекомендации по сценариям развертывания см. в статье [Развертывание приложений .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

Для службы на основе веб-приложений, которая использует платформы MVC или Razor Pages, укажите веб-пакет SDK в файле проекта.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Если служба выполняет только фоновые задачи (например, [размещенные службы](xref:fundamentals/host/hosted-services)), укажите пакет SDK рабочей роли в файле проекта:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Зависящее от платформы развертывание (FDD)

Зависящее от платформы развертывание (FDD) требует наличия в целевой системе общей для всей системы версии .NET Core. Если сценарий с FDD используется согласно инструкций в этой статье, пакет SDK создаcт исполняемый файл ( *.exe*), который называется *исполняемым файлом, зависящим от платформы*.

[Идентификатор среды выполнения](/dotnet/core/rid-catalog) Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) содержит целевую версию платформы. В следующем примере для RID задано значение `win7-x64`. Свойству `<SelfContained>` задано значение `false`. Эти свойства указывают пакету SDK создать исполняемый файл ( *.exe*) для Windows и приложение, которое зависит от общей платформы .NET Core.

Файл *web.config*, который обычно создается при публикации приложения ASP.NET Core, не требуется для приложения служб Windows. Отмените создание файла *web.config*, добавив свойство `<IsTransformWebConfigDisabled>` со значением `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Автономное развертывание

Для автономного развертывания необязательно наличие общей платформы в системе размещения. Среда выполнения и зависимости приложения развертываются с приложением.

[Идентификатор среды выполнения](/dotnet/core/rid-catalog) Windows включается в `<PropertyGroup>`, где содержится целевая платформа:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Чтобы выполнить публикацию для нескольких идентификаторов RID, сделайте следующее.

* Укажите список идентификаторов RID, разделив их точкой с запятой.
* Укажите имя свойства [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (множественное число).

Дополнительные сведения см. в [каталоге RID для .NET Core](/dotnet/core/rid-catalog).

Для свойства `<SelfContained>` задано значение `true`:

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Учетная запись пользователя службы

Создайте для службы учетную запись пользователя с помощью командлета [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) в административной оболочке PowerShell 6.

Обновление Windows 10 за октябрь 2018 г. (версия 1809, сборка 10.0.17763) или более поздней версии:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Версия Windows, предшествующая обновлению Windows 10 за октябрь 2018 г. (версия 1809, сборка 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Укажите [надежный пароль](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) при появлении соответствующего запроса.

Параметр срока действия учетной записи <xref:System.DateTime> можно задать с помощью параметра `-AccountExpires`, передаваемого в командлет [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser).

Дополнительные сведения см. в статьях [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) и [Service User Accounts](/windows/desktop/services/service-user-accounts) (Учетные записи пользователей служб).

Альтернативный подход к управлению пользователями при работе с Active Directory заключается в применении управляемых учетных записей служб. Дополнительные сведения см. в [обзоре групповых управляемых учетных записей службы](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Права на вход в качестве службы

Чтобы настроить право *Вход в качестве службы* для учетной записи пользователя службы, сделайте следующее:

1. Откройте редактор локальной политики безопасности, запустив *secpol.msc*.
1. Разверните узел **Локальные политики** и выберите **Назначение прав пользователя**.
1. Откройте политику **Вход в качестве службы**.
1. Щелкните **Добавить пользователя или группу**.
1. Укажите имя объекта (учетная запись пользователя) одним из следующих способов:
   1. Укажите учетную запись пользователя (`{DOMAIN OR COMPUTER NAME\USER}`) в поле для имени объекта и нажмите **ОК**, чтобы назначить политику пользователю.
   1. Выберите **Дополнительно**. Нажмите **Найти**. Выберите учетную запись пользователя из списка. Нажмите кнопку **ОК**. Нажмите **ОК** еще раз, чтобы назначить политику пользователю.
1. Нажмите **ОК** или **Применить**, чтобы сохранить изменения.

## <a name="create-and-manage-the-windows-service"></a>Создание службы Windows и управление ею

### <a name="create-a-service"></a>Создание службы

Зарегистрируйте службу с помощью команды PowerShell. В административной оболочке PowerShell 6 выполните следующие команды:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`. Путь к папке приложения на узле (например, `d:\myservice`). Не включайте исполняемый файл приложения в путь. Завершающая косая черта не требуется.
* `{DOMAIN OR COMPUTER NAME\USER}`. Учетная запись пользователя службы (например, `Contoso\ServiceUser`).
* `{SERVICE NAME}`. Имя службы (например, `MyService`).
* `{EXE FILE PATH}`. Путь к исполняемому файлу приложения (например, `d:\myservice\myservice.exe`). Включите имя исполняемого файла с расширением.
* `{DESCRIPTION}`. Описание службы (например, `My sample service`).
* `{DISPLAY NAME}`. Отображаемое имя службы (например, `My Service`).

### <a name="start-a-service"></a>Запуск службы

Запустите службу с помощью следующей команды PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Команде потребуется несколько секунд, чтобы запустить службу.

### <a name="determine-a-services-status"></a>Определение состояния службы

Чтобы проверить состояние службы, используйте следующую команду PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Состояние отображается одним из следующих значений:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Остановка службы

Остановите службу с помощью следующей команды PowerShell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Удаление службы

После небольшой задержки для остановки службы удалите службу с помощью следующей команды Powershell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Обработка событий запуска и остановки

Чтобы обработать события <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> и <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, сделайте следующее:

1. Создайте класс, производный от <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService>, с методами `OnStarting`, `OnStarted` и `OnStopping`:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Создайте метод расширения для <xref:Microsoft.AspNetCore.Hosting.IWebHost>, который передает `CustomWebHostService` в <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. В `Program.Main` вызовите метод расширения `RunAsCustomService` вместо <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:

   ```csharp
   host.RunAsCustomService();
   ```

   Чтобы узнать расположение <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> в `Program.Main`, см. пример кода из раздела [Тип развертывания](#deployment-type).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Сценарии использования прокси-сервера и подсистемы балансировки нагрузки

Для служб, которые взаимодействуют с запросами из Интернета или корпоративной сети и размещаются за прокси-сервером или подсистемой балансировки нагрузки, может потребоваться дополнительная настройка. Для получения дополнительной информации см. <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Настройка конечных точек

По умолчанию платформа ASP.NET Core привязана к `http://localhost:5000`. Настройте URL-адрес и порт, задав переменную среды `ASPNETCORE_URLS`.

Дополнительные сведения о подходах к настройке URL-адресов и портов см. в соответствующей статье сервера:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

В предыдущем руководстве рассматривается поддержка конечных точек HTTPS. Например, настройте приложение для HTTPS, если проверка подлинности используется со службой Windows.

> [!NOTE]
> Использование сертификата разработки ASP.NET Core HTTPS для защиты конечной точки службы не поддерживается.

## <a name="current-directory-and-content-root"></a>Текущий каталог и корневой каталог содержимого

Для службы Windows <xref:System.IO.Directory.GetCurrentDirectory*> возвращает текущий рабочий каталог *C:\\WINDOWS\\system32*. Папка *system32* не подходит для хранения файлов службы (например, файлов параметров). Используйте один из следующих методов для сохранения ресурсов и файлов параметров службы и доступа к ним.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Указание папки приложения в качестве пути корневого каталога содержимого

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> — это тот же путь, который предоставляется аргументу `binPath` при создании службы. Чтобы не вызывать метод `GetCurrentDirectory` для создания путей к файлам параметров, вызовите <xref:System.IO.Directory.SetCurrentDirectory*> с указанным путем к [корневому каталогу содержимого](xref:fundamentals/index#content-root) приложения.

В `Program.Main` определите путь к папке с исполняемым файлом службы и используйте этот путь, чтобы создать корневой каталог содержимого приложения.

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Хранение файлов службы в подходящем расположении на диске

Укажите абсолютный путь к папке, содержащей файлы, с помощью <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> при использовании <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>.

## <a name="troubleshoot"></a>Устранение неполадок

Сведения об устранении неполадок в приложении службы Windows см. в статье <xref:test/troubleshoot>.

### <a name="common-errors"></a>Распространенные ошибки

* Используется старая или предварительная версия PowerShell.
* Зарегистрированная служба не использует **опубликованные** выходные данные приложения, возвращенные командой [dotnet publish](/dotnet/core/tools/dotnet-publish). Выходные данные команды [dotnet build](/dotnet/core/tools/dotnet-build) не поддерживаются для развертывания приложений. В зависимости от типа развертывания опубликованные ресурсы находятся в одной из следующих папок:
  * *bin/Release/{TARGET FRAMEWORK}/publish* (зависящее от платформы развертывание);
  * *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (автономное развертывание).
* Служба не находится в состоянии выполнения.
* Пути к используемым приложением ресурсам (например, к сертификатам) неверные. Базовый путь к службе Windows — *c:\\Windows\\System32*.
* У пользователя отсутствуют права для *входа в систему в качестве службы*.
* Пароль был указан неверно или его срок действия истек при выполнении команды PowerShell `New-Service`.
* Для приложения требуется выполнить проверку подлинности в ASP.NET Core, однако оно не настроено для безопасных подключений (HTTPS).
* Порт URL-адреса запроса неправильно указан или настроен в приложении.

### <a name="system-and-application-event-logs"></a>Журналы событий системы и приложений

Получите доступ к журналам событий системы и приложений:

1. Откройте меню "Пуск", выполните поиск по фразе *Просмотр событий* и выберите приложение **Просмотр событий**.
1. В **средстве просмотра событий** откройте узел **Журналы Windows**.
1. Выберите **Система**, чтобы открыть журнал системных событий. Выберите **Приложение**, чтобы открыть журнал событий приложения.
1. Проверьте здесь наличие ошибок, связанных с проблемным приложением.

### <a name="run-the-app-at-a-command-prompt"></a>Запуск приложения в командной строке

Многие ошибки запуска не создают полезные сведения в журналах событий. Для некоторых ошибок можно найти причину, запустив приложение в командной строке на компьютере размещения. Чтобы записать дополнительные сведения из приложения, снизьте [уровень ведения журнала](xref:fundamentals/logging/index#log-level) или запустите приложение в [среде разработки](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Очистка кэшей пакетов

Приложения-функции могут перестать работать сразу после обновления пакета SDK для .NET Core на компьютере разработки или обновления версии пакетов в самом приложении. В некоторых случаях в результате важного обновления несогласованные версии пакетов могут привести к нарушению работы приложения. Большинство этих проблем можно исправить следующим образом:

1. Удалите папки *bin* и *obj*.
1. Очистите кэши пакетов, выполнив команду [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) из командной оболочки.

   Очистку кэшей пакетов также можно выполнить с помощью средства [nuget.exe](https://www.nuget.org/downloads) или команды `nuget locals all -clear`. *NuGet.exe* не входит в пакет установки операционной системы Windows для настольных компьютеров и его нужно получить отдельно на [веб-сайте NuGet](https://www.nuget.org/downloads).

1. Восстановите и перестройте проект.
1. Удалите все файлы из папки развертывания на сервере, прежде чем повторно развернуть приложение.

### <a name="slow-or-hanging-app"></a>Медленное или зависающее приложение

*Аварийный дамп* — это моментальный снимок системной памяти, который может помочь определить причину аварийного завершения, сбоя запуска или медленной работы приложения.

#### <a name="app-crashes-or-encounters-an-exception"></a>Аварийное завершение работы приложения или исключение

Получите и проанализируйте дамп из [отчетов об ошибках Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Создайте папку для хранения файлов аварийного дампа в `c:\dumps`.
1. Запустите [скрипт PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) с именем исполняемого файла приложения:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Запустите приложение в условиях, вызывающих аварийное завершение.
1. После аварийного завершения запустите [скрипт PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Когда приложение аварийно завершит работу и сбор дампов будет выполнен, приложение сможет завершить работу обычным образом. Скрипт PowerShell настраивает отчеты об ошибках Windows для сбора до пяти дампов для приложения.

> [!WARNING]
> Аварийные дампы могут занимать много места на диске (до нескольких гигабайтов каждый).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Приложение перестает отвечать на запросы, не запускается или работает в обычном режиме

Когда приложение *перестает отвечать на запросы* (но аварийное завершение не происходит), не запускается или работает в обычном режиме, см. раздел [Файлы дампа пользовательского режима: выбор лучшего инструмента](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), чтобы выбрать подходящий инструмент для создания дампа.

#### <a name="analyze-the-dump"></a>Анализ дампа

Дамп можно проанализировать несколькими способами. Дополнительные сведения см. в разделе [Анализ файла дампа пользовательского режима](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Конфигурация конечных точек Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (включает конфигурацию HTTPS и поддержку SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Приложение ASP.NET Core можно разместить в Windows в качестве [службы Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) без использования IIS. При размещении в качестве службы Windows приложение автоматически запускается после перезагрузки сервера.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Предварительные требования

* [Пакет SDK для ASP.NET Core 2.1 или более поздней версии](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 или более поздней версии](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Конфигурация приложения

Приложение требует ссылки на пакеты для [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) и [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Для тестирования и отладки при работе вне службы добавьте код, чтобы определить, как выполняется приложение: в качестве службы или консольного приложения. Проверьте, присоединен ли отладчик или присутствует ли параметр `--console`. Если одно из условий имеет значение true (приложение выполняется не в качестве службы), вызовите <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Если условия имеют значение false (приложение выполняется в качестве службы), сделайте следующее:

* Вызовите <xref:System.IO.Directory.SetCurrentDirectory*> и используйте путь к расположению для публикации приложения. Не вызывайте <xref:System.IO.Directory.GetCurrentDirectory*> для получения пути, так как при вызове <xref:System.IO.Directory.GetCurrentDirectory*> приложение службы Windows возвращает папку *C:\\WINDOWS\\system32*. Дополнительные сведения см. в разделе [Текущий каталог и корневой каталог содержимого](#current-directory-and-content-root). Этот шаг выполняется до того, как приложение будет настроено в `CreateWebHostBuilder`.
* Вызовите <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>, чтобы запустить приложение в качестве службы.

Так как [поставщик конфигурации командной строки](xref:fundamentals/configuration/index#command-line-configuration-provider) требует указания пар "имя-значение" для аргументов командной строки, параметр `--console` удаляется из аргументов, прежде чем <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> получит их.

Для записи данных в журнал событий Windows добавьте поставщик EventLog в <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Задайте уровень ведения журнала с помощью ключа `Logging:LogLevel:Default` в файле *appsettings.Production.json*.

В следующем примере `RunAsCustomService` вызывается вместо <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> для обработки событий времени существования в приложении. Дополнительные сведения см. в разделе [Обработка событий запуска и остановки](#handle-starting-and-stopping-events).

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Тип развертывания

Дополнительные сведения и рекомендации по сценариям развертывания см. в статье [Развертывание приложений .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

Для службы на основе веб-приложений, которая использует платформы MVC или Razor Pages, укажите веб-пакет SDK в файле проекта.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Если служба выполняет только фоновые задачи (например, [размещенные службы](xref:fundamentals/host/hosted-services)), укажите пакет SDK рабочей роли в файле проекта:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Зависящее от платформы развертывание (FDD)

Зависящее от платформы развертывание (FDD) требует наличия в целевой системе общей для всей системы версии .NET Core. Если сценарий с FDD используется согласно инструкций в этой статье, пакет SDK создаcт исполняемый файл ( *.exe*), который называется *исполняемым файлом, зависящим от платформы*.

[Идентификатор среды выполнения](/dotnet/core/rid-catalog) Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) содержит целевую версию платформы. В следующем примере для RID задано значение `win7-x64`. Свойству `<SelfContained>` задано значение `false`. Эти свойства указывают пакету SDK создать исполняемый файл ( *.exe*) для Windows и приложение, которое зависит от общей платформы .NET Core.

Свойству `<UseAppHost>` задано значение `true`. Это свойство предоставляет службу с путем активации (исполняемый файл, *EXE*) для FDD.

Файл *web.config*, который обычно создается при публикации приложения ASP.NET Core, не требуется для приложения служб Windows. Отмените создание файла *web.config*, добавив свойство `<IsTransformWebConfigDisabled>` со значением `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Автономное развертывание

Для автономного развертывания необязательно наличие общей платформы в системе размещения. Среда выполнения и зависимости приложения развертываются с приложением.

[Идентификатор среды выполнения](/dotnet/core/rid-catalog) Windows включается в `<PropertyGroup>`, где содержится целевая платформа:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Чтобы выполнить публикацию для нескольких идентификаторов RID, сделайте следующее.

* Укажите список идентификаторов RID, разделив их точкой с запятой.
* Укажите имя свойства [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (множественное число).

Дополнительные сведения см. в [каталоге RID для .NET Core](/dotnet/core/rid-catalog).

Для свойства `<SelfContained>` задано значение `true`:

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Учетная запись пользователя службы

Создайте для службы учетную запись пользователя с помощью командлета [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) в административной оболочке PowerShell 6.

Обновление Windows 10 за октябрь 2018 г. (версия 1809, сборка 10.0.17763) или более поздней версии:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Версия Windows, предшествующая обновлению Windows 10 за октябрь 2018 г. (версия 1809, сборка 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Укажите [надежный пароль](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) при появлении соответствующего запроса.

Параметр срока действия учетной записи <xref:System.DateTime> можно задать с помощью параметра `-AccountExpires`, передаваемого в командлет [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser).

Дополнительные сведения см. в статьях [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) и [Service User Accounts](/windows/desktop/services/service-user-accounts) (Учетные записи пользователей служб).

Альтернативный подход к управлению пользователями при работе с Active Directory заключается в применении управляемых учетных записей служб. Дополнительные сведения см. в [обзоре групповых управляемых учетных записей службы](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Права на вход в качестве службы

Чтобы настроить право *Вход в качестве службы* для учетной записи пользователя службы, сделайте следующее:

1. Откройте редактор локальной политики безопасности, запустив *secpol.msc*.
1. Разверните узел **Локальные политики** и выберите **Назначение прав пользователя**.
1. Откройте политику **Вход в качестве службы**.
1. Щелкните **Добавить пользователя или группу**.
1. Укажите имя объекта (учетная запись пользователя) одним из следующих способов:
   1. Укажите учетную запись пользователя (`{DOMAIN OR COMPUTER NAME\USER}`) в поле для имени объекта и нажмите **ОК**, чтобы назначить политику пользователю.
   1. Выберите **Дополнительно**. Нажмите **Найти**. Выберите учетную запись пользователя из списка. Нажмите кнопку **ОК**. Нажмите **ОК** еще раз, чтобы назначить политику пользователю.
1. Нажмите **ОК** или **Применить**, чтобы сохранить изменения.

## <a name="create-and-manage-the-windows-service"></a>Создание службы Windows и управление ею

### <a name="create-a-service"></a>Создание службы

Зарегистрируйте службу с помощью команды PowerShell. В административной оболочке PowerShell 6 выполните следующие команды:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`. Путь к папке приложения на узле (например, `d:\myservice`). Не включайте исполняемый файл приложения в путь. Завершающая косая черта не требуется.
* `{DOMAIN OR COMPUTER NAME\USER}`. Учетная запись пользователя службы (например, `Contoso\ServiceUser`).
* `{SERVICE NAME}`. Имя службы (например, `MyService`).
* `{EXE FILE PATH}`. Путь к исполняемому файлу приложения (например, `d:\myservice\myservice.exe`). Включите имя исполняемого файла с расширением.
* `{DESCRIPTION}`. Описание службы (например, `My sample service`).
* `{DISPLAY NAME}`. Отображаемое имя службы (например, `My Service`).

### <a name="start-a-service"></a>Запуск службы

Запустите службу с помощью следующей команды PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Команде потребуется несколько секунд, чтобы запустить службу.

### <a name="determine-a-services-status"></a>Определение состояния службы

Чтобы проверить состояние службы, используйте следующую команду PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Состояние отображается одним из следующих значений:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Остановка службы

Остановите службу с помощью следующей команды PowerShell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Удаление службы

После небольшой задержки для остановки службы удалите службу с помощью следующей команды Powershell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Обработка событий запуска и остановки

Чтобы обработать события <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> и <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, сделайте следующее:

1. Создайте класс, производный от <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService>, с методами `OnStarting`, `OnStarted` и `OnStopping`:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Создайте метод расширения для <xref:Microsoft.AspNetCore.Hosting.IWebHost>, который передает `CustomWebHostService` в <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. В `Program.Main` вызовите метод расширения `RunAsCustomService` вместо <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:

   ```csharp
   host.RunAsCustomService();
   ```

   Чтобы узнать расположение <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> в `Program.Main`, см. пример кода из раздела [Тип развертывания](#deployment-type).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Сценарии использования прокси-сервера и подсистемы балансировки нагрузки

Для служб, которые взаимодействуют с запросами из Интернета или корпоративной сети и размещаются за прокси-сервером или подсистемой балансировки нагрузки, может потребоваться дополнительная настройка. Для получения дополнительной информации см. <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Настройка конечных точек

По умолчанию платформа ASP.NET Core привязана к `http://localhost:5000`. Настройте URL-адрес и порт, задав переменную среды `ASPNETCORE_URLS`.

Дополнительные сведения о подходах к настройке URL-адресов и портов см. в соответствующей статье сервера:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

В предыдущем руководстве рассматривается поддержка конечных точек HTTPS. Например, настройте приложение для HTTPS, если проверка подлинности используется со службой Windows.

> [!NOTE]
> Использование сертификата разработки ASP.NET Core HTTPS для защиты конечной точки службы не поддерживается.

## <a name="current-directory-and-content-root"></a>Текущий каталог и корневой каталог содержимого

Для службы Windows <xref:System.IO.Directory.GetCurrentDirectory*> возвращает текущий рабочий каталог *C:\\WINDOWS\\system32*. Папка *system32* не подходит для хранения файлов службы (например, файлов параметров). Используйте один из следующих методов для сохранения ресурсов и файлов параметров службы и доступа к ним.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Указание папки приложения в качестве пути корневого каталога содержимого

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> — это тот же путь, который предоставляется аргументу `binPath` при создании службы. Чтобы не вызывать метод `GetCurrentDirectory` для создания путей к файлам параметров, вызовите <xref:System.IO.Directory.SetCurrentDirectory*> с указанным путем к [корневому каталогу содержимого](xref:fundamentals/index#content-root) приложения.

В `Program.Main` определите путь к папке с исполняемым файлом службы и используйте этот путь, чтобы создать корневой каталог содержимого приложения.

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Хранение файлов службы в подходящем расположении на диске

Укажите абсолютный путь к папке, содержащей файлы, с помощью <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> при использовании <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>.

## <a name="troubleshoot"></a>Устранение неполадок

Сведения об устранении неполадок в приложении службы Windows см. в статье <xref:test/troubleshoot>.

### <a name="common-errors"></a>Распространенные ошибки

* Используется старая или предварительная версия PowerShell.
* Зарегистрированная служба не использует **опубликованные** выходные данные приложения, возвращенные командой [dotnet publish](/dotnet/core/tools/dotnet-publish). Выходные данные команды [dotnet build](/dotnet/core/tools/dotnet-build) не поддерживаются для развертывания приложений. В зависимости от типа развертывания опубликованные ресурсы находятся в одной из следующих папок:
  * *bin/Release/{TARGET FRAMEWORK}/publish* (зависящее от платформы развертывание);
  * *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (автономное развертывание).
* Служба не находится в состоянии выполнения.
* Пути к используемым приложением ресурсам (например, к сертификатам) неверные. Базовый путь к службе Windows — *c:\\Windows\\System32*.
* У пользователя отсутствуют права для *входа в систему в качестве службы*.
* Пароль был указан неверно или его срок действия истек при выполнении команды PowerShell `New-Service`.
* Для приложения требуется выполнить проверку подлинности в ASP.NET Core, однако оно не настроено для безопасных подключений (HTTPS).
* Порт URL-адреса запроса неправильно указан или настроен в приложении.

### <a name="system-and-application-event-logs"></a>Журналы событий системы и приложений

Получите доступ к журналам событий системы и приложений:

1. Откройте меню "Пуск", выполните поиск по фразе *Просмотр событий* и выберите приложение **Просмотр событий**.
1. В **средстве просмотра событий** откройте узел **Журналы Windows**.
1. Выберите **Система**, чтобы открыть журнал системных событий. Выберите **Приложение**, чтобы открыть журнал событий приложения.
1. Проверьте здесь наличие ошибок, связанных с проблемным приложением.

### <a name="run-the-app-at-a-command-prompt"></a>Запуск приложения в командной строке

Многие ошибки запуска не создают полезные сведения в журналах событий. Для некоторых ошибок можно найти причину, запустив приложение в командной строке на компьютере размещения. Чтобы записать дополнительные сведения из приложения, снизьте [уровень ведения журнала](xref:fundamentals/logging/index#log-level) или запустите приложение в [среде разработки](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Очистка кэшей пакетов

Приложения-функции могут перестать работать сразу после обновления пакета SDK для .NET Core на компьютере разработки или обновления версии пакетов в самом приложении. В некоторых случаях в результате важного обновления несогласованные версии пакетов могут привести к нарушению работы приложения. Большинство этих проблем можно исправить следующим образом:

1. Удалите папки *bin* и *obj*.
1. Очистите кэши пакетов, выполнив команду [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) из командной оболочки.

   Очистку кэшей пакетов также можно выполнить с помощью средства [nuget.exe](https://www.nuget.org/downloads) или команды `nuget locals all -clear`. *NuGet.exe* не входит в пакет установки операционной системы Windows для настольных компьютеров и его нужно получить отдельно на [веб-сайте NuGet](https://www.nuget.org/downloads).

1. Восстановите и перестройте проект.
1. Удалите все файлы из папки развертывания на сервере, прежде чем повторно развернуть приложение.

### <a name="slow-or-hanging-app"></a>Медленное или зависающее приложение

*Аварийный дамп* — это моментальный снимок системной памяти, который может помочь определить причину аварийного завершения, сбоя запуска или медленной работы приложения.

#### <a name="app-crashes-or-encounters-an-exception"></a>Аварийное завершение работы приложения или исключение

Получите и проанализируйте дамп из [отчетов об ошибках Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Создайте папку для хранения файлов аварийного дампа в `c:\dumps`.
1. Запустите [скрипт PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) с именем исполняемого файла приложения:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Запустите приложение в условиях, вызывающих аварийное завершение.
1. После аварийного завершения запустите [скрипт PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Когда приложение аварийно завершит работу и сбор дампов будет выполнен, приложение сможет завершить работу обычным образом. Скрипт PowerShell настраивает отчеты об ошибках Windows для сбора до пяти дампов для приложения.

> [!WARNING]
> Аварийные дампы могут занимать много места на диске (до нескольких гигабайтов каждый).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Приложение перестает отвечать на запросы, не запускается или работает в обычном режиме

Когда приложение *перестает отвечать на запросы* (но аварийное завершение не происходит), не запускается или работает в обычном режиме, см. раздел [Файлы дампа пользовательского режима: выбор лучшего инструмента](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), чтобы выбрать подходящий инструмент для создания дампа.

#### <a name="analyze-the-dump"></a>Анализ дампа

Дамп можно проанализировать несколькими способами. Дополнительные сведения см. в разделе [Анализ файла дампа пользовательского режима](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Конфигурация конечных точек Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (включает конфигурацию HTTPS и поддержку SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
