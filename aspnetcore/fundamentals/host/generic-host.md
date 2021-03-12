---
title: Универсальный узел .NET в ASP.NET Core
author: rick-anderson
description: Используйте универсальный узел .NET в приложениях ASP.NET Core.  Универсальный узел отвечает за запуск приложения и управление временем существования.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: ceb766cae1f8a1735f5b578a44f0c481927e47a5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586752"
---
# <a name="net-generic-host-in-aspnet-core"></a>Универсальный узел .NET в ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

Шаблоны ASP.NET Core создают .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).

В этой статье приведены сведения об использовании универсального узла .NET в ASP.NET Core. Сведения об использовании универсального узла .NET в консольных приложениях см. в статье [Универсальный узел .NET](/dotnet/core/extensions/generic-host).

## <a name="host-definition"></a>Определение узла

*Узел* — это объект, который инкапсулирует все ресурсы приложения, такие как:

* Внедрение зависимостей
* Ведение журнала
* Параметр Configuration
* Реализации `IHostedService`

После запуска узла он вызывает <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> в каждой реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, зарегистрированной в коллекции размещенных служб контейнера службы. В веб-приложении одна из реализаций `IHostedService` является веб-службой, которая запускает [реализацию сервера HTTP](xref:fundamentals/index#servers).

Основной причиной включения всех взаимозависимых ресурсов приложения в один объект является управление жизненным циклом: контроль запуска и корректного завершения работы приложения.

## <a name="set-up-a-host"></a>Создание узла

Узел обычно настраивается, собирается и выполняется кодом в классе `Program`. Метод `Main`:

* Вызывает метод `CreateHostBuilder` для создания и настройки объекта построителя.
* Вызывает методы `Build` и `Run` в объекте построителя.

Веб-шаблоны ASP.NET Core создают следующий код для создания узла:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

Следующий код создают нагрузку, отличную от HTTP, с одной реализацией `IHostedService`, добавленной в контейнер внедрения зависимостей.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

При использовании рабочей нагрузки HTTP метод `Main` остается прежним, но `CreateHostBuilder` вызывает `ConfigureWebHostDefaults`:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Если приложение использует Entity Framework Core, не изменяйте имя или сигнатуру метода `CreateHostBuilder`. [Инструменты Entity Framework Core](/ef/core/miscellaneous/cli/) ищут метод `CreateHostBuilder`, который настраивает узел без необходимости запускать приложение. Подробные сведения см. в статье [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation) (Создание экземпляра DbContext во время разработки).

## <a name="default-builder-settings"></a>Параметры построителя по умолчанию

Метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:

* В качестве [корневого каталога содержимого](xref:fundamentals/index#content-root) задает путь, возвращенный методом <xref:System.IO.Directory.GetCurrentDirectory*>.
* Загружает конфигурацию узла из:
  * Переменные среды с префиксом `DOTNET_`.
  * аргументы командной строки.
* Загружает конфигурацию приложения из:
  * *appsettings.json*.
  * *appsettings.{Environment}.json*;
  * [секреты пользователя](xref:security/app-secrets), когда приложение выполняется в среде `Development`;
  * Переменные среды.
  * аргументы командной строки.
* Добавляет следующие [регистраторы](xref:fundamentals/logging/index):
  * Консоль
  * Отладка
  * EventSource
  * Журнал событий (только при запуске в Windows)
* Включает [проверку области](xref:fundamentals/dependency-injection#scope-validation) и [проверку зависимостей](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), если это среда разработки.

Метод <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>:

* Загружает конфигурацию узла из переменных среды с префиксом `ASPNETCORE_`.
* Задает сервер [Kestrel](xref:fundamentals/servers/kestrel) в качестве веб-сервера и настраивает его с помощью поставщиков конфигурации размещения приложения. Параметры сервера Kestrel по умолчанию см. в разделе <xref:fundamentals/servers/kestrel/options>.
* Добавляет [ПО промежуточного слоя фильтрации узлов](xref:fundamentals/servers/kestrel/host-filtering).
* Добавляет [Параметры ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer#forwarded-headers), если `ASPNETCORE_FORWARDEDHEADERS_ENABLED` равно `true`.
* Обеспечивает интеграцию служб IIS. Параметры IIS по умолчанию см. в разделе <xref:host-and-deploy/iis/index#iis-options>.

Разделы [Параметры для всех типов приложений](#settings-for-all-app-types) и [Параметры для веб-приложений](#settings-for-web-apps) далее в этой статье описывают, как переопределить параметры построителя по умолчанию.

## <a name="framework-provided-services"></a>Платформенные службы

Следующие службы регистрируются автоматически.

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

Дополнительные сведения о службах, предоставляемых платформой, см. в разделе <xref:fundamentals/dependency-injection#framework-provided-services>.

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

Внедрите <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (прежнее название — `IApplicationLifetime`) в любой класс для выполнения задач после запуска и корректного завершения работы. Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов обработчика событий запуска и завершения работы приложения. Этот интерфейс также включает метод `StopApplication`.

Ниже приведен пример реализации `IHostedService`, которая регистрирует события `IHostApplicationLifetime`:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

Реализация <xref:Microsoft.Extensions.Hosting.IHostLifetime> контролирует, когда узел запускается и останавливается. Используется последняя зарегистрированная реализация.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` — это реализация `IHostLifetime` по умолчанию. `ConsoleLifetime`:

* Прослушивает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM и вызывает метод <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> для запуска процесса завершения работы.
* разблокирует расширения, такие как [RunAsync](#runasync) и [WaitForShutdownAsync](#waitforshutdownasync).

## <a name="ihostenvironment"></a>IHostEnvironment

Внедряет службу <xref:Microsoft.Extensions.Hosting.IHostEnvironment> в класс, чтобы получить сведения о следующих параметрах.

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [ContentRootPath](#contentroot)

Веб-приложения реализуют интерфейс `IWebHostEnvironment`, который наследует `IHostEnvironment` и добавляет [WebRootPath](#webroot).

## <a name="host-configuration"></a>Конфигурация узла

Конфигурация узла используется для свойств реализации <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.

Конфигурация узла доступна из [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) внутри <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>. После `ConfigureAppConfiguration``HostBuilderContext.Configuration` заменяется конфигурацией приложения.

Чтобы добавить конфигурацию узла, вызовите <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> в `IHostBuilder`. Метод `ConfigureHostConfiguration` может вызываться несколько раз с накоплением результатов. Узел использует значение, заданное последним для данного ключа.

Поставщик переменных среды с префиксом `DOTNET_` и аргументы командной строки включены в `CreateDefaultBuilder`. Для веб-приложений добавляется поставщик переменных среды с префиксом `ASPNETCORE_`. Префикс удаляется при чтении переменных среды. Например, значение переменной среды для `ASPNETCORE_ENVIRONMENT` становится значением конфигурации узла для ключа `environment`.

В следующем примере создается конфигурация узла:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Конфигурация приложения

Конфигурация приложения создается путем вызова метода <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> в `IHostBuilder`. Метод `ConfigureAppConfiguration` может вызываться несколько раз с накоплением результатов. Приложение использует значение, заданное последним для данного ключа. 

Конфигурация, созданная с помощью `ConfigureAppConfiguration`, доступна в свойствах [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) для последующих операций и как служба из внедрения зависимостей. Конфигурация узла также добавляется к конфигурации приложения.

Дополнительные сведения см. в разделе [Конфигурация в ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).

## <a name="settings-for-all-app-types"></a>Параметры для всех типов приложений

В этом разделе перечислены параметры узла, которые применяются к рабочим нагрузкам HTTP и остальным. По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`. Дополнительные сведения см. в разделе [Параметры построителя по умолчанию](#default-builder-settings).

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

Свойство [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) задается в конфигурации узла во время создания узла.

**Ключ**: `applicationName`  
**Тип**: `string`  
**По умолчанию**: Имя сборки, содержащей точку входа приложения.  
**Переменная среды**: `<PREFIX_>APPLICATIONNAME`

Чтобы задать это значение, используйте переменную среды. 

### <a name="contentroot"></a>ContentRoot

Свойство [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) определяет, где узел начинает поиск файлов содержимого. Если путь не существует, узел не запускается.

**Ключ**: `contentRoot`  
**Тип**: `string`  
**По умолчанию**: папка, в которой находится сборка приложения.  
**Переменная среды**: `<PREFIX_>CONTENTROOT`

Чтобы задать это значение, используйте переменную среды или вызов `UseContentRoot` в `IHostBuilder`:

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

Дополнительные сведения можно найти в разделе

* [Корневой каталог содержимого](xref:fundamentals/index#content-root)
* [Корневой каталог документов](#webroot)

### <a name="environmentname"></a>EnvironmentName

Свойству [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) может быть присвоено любое значение. В платформе определены значения `Development`, `Staging` и `Production`. Регистр символов в значениях не учитывается.

**Ключ**: `environment`  
**Тип**: `string`  
**По умолчанию**: `Production`  
**Переменная среды**: `<PREFIX_>ENVIRONMENT`

Чтобы задать это значение, используйте переменную среды или вызов `UseEnvironment` в `IHostBuilder`:

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) задает время ожидания для <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>. Значение по умолчанию — пять секунд.  Во время ожидания узел:

* Активирует [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).
* Пытается остановить размещенные службы, записывая в журнал ошибки для служб, которые не удалось остановить.

Если время ожидания истекает до остановки всех размещенных служб, активные службы останавливаются при завершении работы приложения. Службы останавливаются даже в том случае, если еще не завершили обработку. Если службе требуется дополнительное время для остановки, увеличьте время ожидания.

**Ключ**: `shutdownTimeoutSeconds`  
**Тип**: `int`  
**По умолчанию**: 5 секунд  
**Переменная среды**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

Чтобы задать это значение, используйте переменную среды или настройте `HostOptions`. В следующем примере устанавливается время ожидания в 20 секунд:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a>Отключение перезагрузки конфигурации приложения при изменении

[По умолчанию](xref:fundamentals/configuration/index#default) при изменении файла выполняется перезагрузка *appsettings.json* и *appsettings.{Environment}.json*. Чтобы отключить эту функцию перезагрузки в ASP.NET Core 5.0 или более поздней версии, присвойте ключу `hostBuilder:reloadConfigOnChange` значение `false`.

**Ключ**: `hostBuilder:reloadConfigOnChange`  
**Тип**: `bool` (`true` или `1`)  
**По умолчанию**: `true`  
**Аргумент командной строки**: `hostBuilder:reloadConfigOnChange`  
**Переменная среды**: `<PREFIX_>hostBuilder:reloadConfigOnChange`

> [!WARNING]
> Двоеточие (`:`) не работает в качестве разделителя с иерархическими ключами переменных среды на всех платформах. Дополнительную информацию см. в разделе [Переменные среды](xref:fundamentals/configuration/index#environment-variables).

## <a name="settings-for-web-apps"></a>Параметры для веб-приложений

Некоторые параметры узла применяются только к рабочим нагрузкам HTTP. По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.

Методы расширения в `IWebHostBuilder` доступны для этих параметров. В примерах кода, которые показывают, как вызывать методы расширения, предполагается, что `webBuilder` является экземпляром `IWebHostBuilder`, как показано в следующем примере:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>CaptureStartupErrors

Если задано значение `false`, ошибки во время запуска приводят к завершению работы узла. Если задано значение `true`, узел перехватывает исключения во время запуска и пытается запустить сервер.

**Ключ**: `captureStartupErrors`  
**Тип**: `bool` (`true` или `1`)  
**По умолчанию**: `false`, если только приложение не работает с сервером Kestrel за службами IIS; в этом случае значение по умолчанию — `true`.  
**Переменная среды**: `<PREFIX_>CAPTURESTARTUPERRORS`

Чтобы задать это значение, используйте конфигурацию или вызов `CaptureStartupErrors`:

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

Если этот параметр включен или если среда имеет значение `Development`, приложение перехватывает подробные ошибки.

**Ключ**: `detailedErrors`  
**Тип**: `bool` (`true` или `1`)  
**По умолчанию**: `false`  
**Переменная среды**: `<PREFIX_>_DETAILEDERRORS`

Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Разделенная точками с запятой строка начальных сборок размещения, загружаемых при запуске. Хотя значением по умолчанию этого параметра конфигурации является пустая строка, начальные сборки размещения всегда включают в себя сборку приложения. Если начальные сборки размещения указаны, они добавляются к сборке приложения для загрузки во время построения приложением общих служб при запуске.

**Ключ**: `hostingStartupAssemblies`  
**Тип**: `string`  
**По умолчанию**: Пустая строка  
**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

Разделенная точками с запятой строка начальных сборок размещения, которые необходимо исключить при запуске.

**Ключ**: `hostingStartupExcludeAssemblies`  
**Тип**: `string`  
**По умолчанию**: Пустая строка  
**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

Порт перенаправления HTTPS. Используется при [принудительном применении HTTPS](xref:security/enforcing-ssl).

**Ключ**: `https_port`  
**Тип**: `string`  
**По умолчанию**: значение по умолчанию не задано.  
**Переменная среды**: `<PREFIX_>HTTPS_PORT`

Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls

Указывает, должен ли узел ожидать передачи данных по URL-адресам, настроенным с помощью `IWebHostBuilder`, вместо URL-адресов, настроенных с помощью реализации `IServer`.

**Ключ**: `preferHostingUrls`  
**Тип**: `bool` (`true` или `1`)  
**По умолчанию**: `true`  
**Переменная среды**: `<PREFIX_>_PREFERHOSTINGURLS`

Чтобы задать это значение, используйте переменную среды или вызов `PreferHostingUrls`:

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

Запрещает автоматическую загрузку начальных сборок размещения, включая начальные сборки размещения, настроенные сборкой приложения. Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.

**Ключ**: `preventHostingStartup`  
**Тип**: `bool` (`true` или `1`)  
**По умолчанию**: `false`  
**Переменная среды**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`

Чтобы задать это значение, используйте переменную среды или вызов `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

Сборка, в которой необходимо искать класс `Startup`.

**Ключ**: `startupAssembly`  
**Тип**: `string`  
**По умолчанию**: сборка приложения  
**Переменная среды**: `<PREFIX_>STARTUPASSEMBLY`

Чтобы задать это значение, используйте переменную среды или вызов `UseStartup`. `UseStartup` может использовать имя сборки (`string`) или тип (`TStartup`). При вызове нескольких методов `UseStartup` приоритет имеет последний.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>URL-адреса

Разделенный точками с запятой список IP-адресов или адресов узлов с портами и протоколами, по которым сервер должен ожидать получения запросов. Например, `http://localhost:123`. Используйте символ "\*", чтобы указать, что сервер должен ожидать получения запросов через определенный порт и по определенному протоколу по любому IP-адресу или имени узла (например, `http://*:5000`). Протокол (`http://` или `https://`) должен указываться для каждого URL-адреса. Поддерживаемые форматы зависят от сервера.

**Ключ**: `urls`  
**Тип**: `string`  
**Значения по умолчанию**: `http://localhost:5000` и `https://localhost:5001`  
**Переменная среды**: `<PREFIX_>URLS`

Чтобы задать это значение, используйте переменную среды или вызов `UseUrls`:

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel имеет собственный интерфейс API настройки конечных точек. Для получения дополнительной информации см. <xref:fundamentals/servers/kestrel/endpoints>.

### <a name="webroot"></a>WebRoot

Свойство [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) определяет относительный путь к статическим ресурсам приложения. Если этот путь не существует, используется фиктивный поставщик файлов.  

**Ключ:** `webroot`  
**Тип**: `string`  
**По умолчанию**: Значение по умолчанию — `wwwroot`. Наличие пути *{корневой_каталог_содержимого}/wwwroot* обязательно.  
**Переменная среды**: `<PREFIX_>WEBROOT`

Чтобы задать это значение, используйте переменную среды или вызов `UseWebRoot` в `IWebHostBuilder`:

```csharp
webBuilder.UseWebRoot("public");
```

Дополнительные сведения можно найти в разделе

* [Корневой каталог документов](xref:fundamentals/index#web-root)
* [ContentRoot](#contentroot)

## <a name="manage-the-host-lifetime"></a>Управление временем существования узла

Вызывает методы в реализации <xref:Microsoft.Extensions.Hosting.IHost> для запуска и остановки приложения. Эти методы влияют на все реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, зарегистрированные в контейнере службы.

### <a name="run"></a>Выполнить

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> запускает приложение и блокирует вызывающий поток, пока работа узла не будет завершена.

### <a name="runasync"></a>RunAsync

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> запускает приложение и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы.

### <a name="runconsoleasync"></a>RunConsoleAsync

Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> включает поддержку консоли, собирает и запускает узел и ожидает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM для завершения работы.

### <a name="start"></a>Начало

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> запускает узел синхронно.

### <a name="startasync"></a>StartAsync

Метод <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> запускает узел и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы. 

Метод <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> вызывается в начале `StartAsync`, который ждет, пока он не будет завершен, прежде чем продолжить. Можно отложить запуск до получения сигнала от внешнего события.

### <a name="stopasync"></a>StopAsync

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> пытается остановить узел в течение указанного периода ожидания.

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> блокирует вызывающий поток до завершения работы, активированного IHostLifetime, например через <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> возвращает объект <xref:System.Threading.Tasks.Task>, который завершается после активации завершения работы через токен, и вызывает метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

### <a name="external-control"></a>Внешнее управление

Прямое управление временем существования узла может осуществляться с помощью методов, которые могут быть вызваны извне:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Шаблоны ASP.NET Core создают .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).

В этой статье приведены сведения об использовании универсального узла .NET в ASP.NET Core. Сведения об использовании универсального узла .NET в консольных приложениях см. в статье [Универсальный узел .NET](/dotnet/core/extensions/generic-host).

## <a name="host-definition"></a>Определение узла

*Узел* — это объект, который инкапсулирует все ресурсы приложения, такие как:

* Внедрение зависимостей
* Ведение журнала
* Параметр Configuration
* Реализации `IHostedService`

После запуска узла он вызывает <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> в каждой реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, зарегистрированной в коллекции размещенных служб контейнера службы. В веб-приложении одна из реализаций `IHostedService` является веб-службой, которая запускает [реализацию сервера HTTP](xref:fundamentals/index#servers).

Основной причиной включения всех взаимозависимых ресурсов приложения в один объект является управление жизненным циклом: контроль запуска и корректного завершения работы приложения.

## <a name="set-up-a-host"></a>Создание узла

Узел обычно настраивается, собирается и выполняется кодом в классе `Program`. Метод `Main`:

* Вызывает метод `CreateHostBuilder` для создания и настройки объекта построителя.
* Вызывает методы `Build` и `Run` в объекте построителя.

Веб-шаблоны ASP.NET Core создают следующий код для создания универсального узла:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

Следующий код создает универсальный узел, используя рабочую нагрузку, отличную от HTTP. Реализация `IHostedService` добавляется в контейнер DI:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

При использовании рабочей нагрузки HTTP метод `Main` остается прежним, но `CreateHostBuilder` вызывает `ConfigureWebHostDefaults`:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Приведенный выше код создается шаблонами ASP.NET Core.

Если приложение использует Entity Framework Core, не изменяйте имя или сигнатуру метода `CreateHostBuilder`. [Инструменты Entity Framework Core](/ef/core/miscellaneous/cli/) ищут метод `CreateHostBuilder`, который настраивает узел без необходимости запускать приложение. Подробные сведения см. в статье [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation) (Создание экземпляра DbContext во время разработки).

## <a name="default-builder-settings"></a>Параметры построителя по умолчанию

Метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>:

* В качестве [корневого каталога содержимого](xref:fundamentals/index#content-root) задает путь, возвращенный методом <xref:System.IO.Directory.GetCurrentDirectory%2A>.
* Загружает конфигурацию узла из:
  * Переменные среды с префиксом `DOTNET_`.
  * аргументы командной строки.
* Загружает конфигурацию приложения из:
  * *appsettings.json*.
  * *appsettings.{Environment}.json*;
  * [секреты пользователя](xref:security/app-secrets), когда приложение выполняется в среде `Development`;
  * Переменные среды.
  * аргументы командной строки.
* Добавляет следующие [регистраторы](xref:fundamentals/logging/index):
  * Консоль
  * Отладка
  * EventSource
  * Журнал событий (только при запуске в Windows)
* Включает [проверку области](xref:fundamentals/dependency-injection#scope-validation) и [проверку зависимостей](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), если это среда разработки.

Метод `ConfigureWebHostDefaults`:

* Загружает конфигурацию узла из переменных среды с префиксом `ASPNETCORE_`.
* Задает сервер [Kestrel](xref:fundamentals/servers/kestrel) в качестве веб-сервера и настраивает его с помощью поставщиков конфигурации размещения приложения. Параметры сервера Kestrel по умолчанию см. в разделе <xref:fundamentals/servers/kestrel#kestrel-options>.
* Добавляет [ПО промежуточного слоя фильтрации узлов](xref:fundamentals/servers/kestrel#host-filtering).
* Добавляет [Параметры ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer#forwarded-headers), если `ASPNETCORE_FORWARDEDHEADERS_ENABLED` равно `true`.
* Обеспечивает интеграцию служб IIS. Параметры IIS по умолчанию см. в разделе <xref:host-and-deploy/iis/index#iis-options>.

Разделы [Параметры для всех типов приложений](#settings-for-all-app-types) и [Параметры для веб-приложений](#settings-for-web-apps) далее в этой статье описывают, как переопределить параметры построителя по умолчанию.

## <a name="framework-provided-services"></a>Платформенные службы

Следующие службы регистрируются автоматически.

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

Дополнительные сведения о службах, предоставляемых платформой, см. в разделе <xref:fundamentals/dependency-injection#framework-provided-services>.

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

Внедрите <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (прежнее название — `IApplicationLifetime`) в любой класс для выполнения задач после запуска и корректного завершения работы. Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов обработчика событий запуска и завершения работы приложения. Этот интерфейс также включает метод `StopApplication`.

Ниже приведен пример реализации `IHostedService`, которая регистрирует события `IHostApplicationLifetime`:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

Реализация <xref:Microsoft.Extensions.Hosting.IHostLifetime> контролирует, когда узел запускается и останавливается. Используется последняя зарегистрированная реализация.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` — это реализация `IHostLifetime` по умолчанию. `ConsoleLifetime`:

* Прослушивает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM и вызывает метод <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> для запуска процесса завершения работы.
* разблокирует расширения, такие как [RunAsync](#runasync) и [WaitForShutdownAsync](#waitforshutdownasync).

## <a name="ihostenvironment"></a>IHostEnvironment

Внедряет службу <xref:Microsoft.Extensions.Hosting.IHostEnvironment> в класс, чтобы получить сведения о следующих параметрах.

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [ContentRootPath](#contentroot)

Веб-приложения реализуют интерфейс `IWebHostEnvironment`, который наследует `IHostEnvironment` и добавляет [WebRootPath](#webroot).

## <a name="host-configuration"></a>Конфигурация узла

Конфигурация узла используется для свойств реализации <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.

Конфигурация узла доступна из [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) внутри <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>. После `ConfigureAppConfiguration``HostBuilderContext.Configuration` заменяется конфигурацией приложения.

Чтобы добавить конфигурацию узла, вызовите <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> в `IHostBuilder`. Метод `ConfigureHostConfiguration` может вызываться несколько раз с накоплением результатов. Узел использует значение, заданное последним для данного ключа.

Поставщик переменных среды с префиксом `DOTNET_` и аргументы командной строки включены в `CreateDefaultBuilder`. Для веб-приложений добавляется поставщик переменных среды с префиксом `ASPNETCORE_`. Префикс удаляется при чтении переменных среды. Например, значение переменной среды для `ASPNETCORE_ENVIRONMENT` становится значением конфигурации узла для ключа `environment`.

В следующем примере создается конфигурация узла:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Конфигурация приложения

Конфигурация приложения создается путем вызова метода <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> в `IHostBuilder`. Метод `ConfigureAppConfiguration` может вызываться несколько раз с накоплением результатов. Приложение использует значение, заданное последним для данного ключа. 

Конфигурация, созданная с помощью `ConfigureAppConfiguration`, доступна в свойствах [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) для последующих операций и как служба из внедрения зависимостей. Конфигурация узла также добавляется к конфигурации приложения.

Дополнительные сведения см. в разделе [Конфигурация в ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).

## <a name="settings-for-all-app-types"></a>Параметры для всех типов приложений

В этом разделе перечислены параметры узла, которые применяются к рабочим нагрузкам HTTP и остальным. По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

Свойство [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) задается в конфигурации узла во время создания узла.

**Ключ**: `applicationName`  
**Тип**: `string`  
**По умолчанию**: Имя сборки, содержащей точку входа приложения.  
**Переменная среды**: `<PREFIX_>APPLICATIONNAME`

Чтобы задать это значение, используйте переменную среды. 

### <a name="contentroot"></a>ContentRoot

Свойство [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) определяет, где узел начинает поиск файлов содержимого. Если путь не существует, узел не запускается.

**Ключ**: `contentRoot`  
**Тип**: `string`  
**По умолчанию**: папка, в которой находится сборка приложения.  
**Переменная среды**: `<PREFIX_>CONTENTROOT`

Чтобы задать это значение, используйте переменную среды или вызов `UseContentRoot` в `IHostBuilder`:

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

Дополнительные сведения можно найти в разделе

* [Корневой каталог содержимого](xref:fundamentals/index#content-root)
* [Корневой каталог документов](#webroot)

### <a name="environmentname"></a>EnvironmentName

Свойству [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) может быть присвоено любое значение. В платформе определены значения `Development`, `Staging` и `Production`. Регистр символов в значениях не учитывается.

**Ключ**: `environment`  
**Тип**: `string`  
**По умолчанию**: `Production`  
**Переменная среды**: `<PREFIX_>ENVIRONMENT`

Чтобы задать это значение, используйте переменную среды или вызов `UseEnvironment` в `IHostBuilder`:

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) задает время ожидания для <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>. Значение по умолчанию — пять секунд.  Во время ожидания узел:

* Активирует [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).
* Пытается остановить размещенные службы, записывая в журнал ошибки для служб, которые не удалось остановить.

Если время ожидания истекает до остановки всех размещенных служб, активные службы останавливаются при завершении работы приложения. Службы останавливаются даже в том случае, если еще не завершили обработку. Если службе требуется дополнительное время для остановки, увеличьте время ожидания.

**Ключ**: `shutdownTimeoutSeconds`  
**Тип**: `int`  
**По умолчанию**: 5 секунд  
**Переменная среды**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

Чтобы задать это значение, используйте переменную среды или настройте `HostOptions`. В следующем примере устанавливается время ожидания в 20 секунд:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a>Параметры для веб-приложений

Некоторые параметры узла применяются только к рабочим нагрузкам HTTP. По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.

Методы расширения в `IWebHostBuilder` доступны для этих параметров. В примерах кода, которые показывают, как вызывать методы расширения, предполагается, что `webBuilder` является экземпляром `IWebHostBuilder`, как показано в следующем примере:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>CaptureStartupErrors

Если задано значение `false`, ошибки во время запуска приводят к завершению работы узла. Если задано значение `true`, узел перехватывает исключения во время запуска и пытается запустить сервер.

**Ключ**: `captureStartupErrors`  
**Тип**: `bool` (`true` или `1`)  
**По умолчанию**: `false`, если только приложение не работает с сервером Kestrel за службами IIS; в этом случае значение по умолчанию — `true`.  
**Переменная среды**: `<PREFIX_>CAPTURESTARTUPERRORS`

Чтобы задать это значение, используйте конфигурацию или вызов `CaptureStartupErrors`:

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

Если этот параметр включен или если среда имеет значение `Development`, приложение перехватывает подробные ошибки.

**Ключ**: `detailedErrors`  
**Тип**: `bool` (`true` или `1`)  
**По умолчанию**: `false`  
**Переменная среды**: `<PREFIX_>_DETAILEDERRORS`

Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Разделенная точками с запятой строка начальных сборок размещения, загружаемых при запуске. Хотя значением по умолчанию этого параметра конфигурации является пустая строка, начальные сборки размещения всегда включают в себя сборку приложения. Если начальные сборки размещения указаны, они добавляются к сборке приложения для загрузки во время построения приложением общих служб при запуске.

**Ключ**: `hostingStartupAssemblies`  
**Тип**: `string`  
**По умолчанию**: Пустая строка  
**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

Разделенная точками с запятой строка начальных сборок размещения, которые необходимо исключить при запуске.

**Ключ**: `hostingStartupExcludeAssemblies`  
**Тип**: `string`  
**По умолчанию**: Пустая строка  
**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

Порт перенаправления HTTPS. Используется при [принудительном применении HTTPS](xref:security/enforcing-ssl).

**Ключ**: `https_port`  
**Тип**: `string`  
**По умолчанию**: значение по умолчанию не задано.  
**Переменная среды**: `<PREFIX_>HTTPS_PORT`

Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls

Указывает, должен ли узел ожидать передачи данных по URL-адресам, настроенным с помощью `IWebHostBuilder`, вместо URL-адресов, настроенных с помощью реализации `IServer`.

**Ключ**: `preferHostingUrls`  
**Тип**: `bool` (`true` или `1`)  
**По умолчанию**: `true`  
**Переменная среды**: `<PREFIX_>_PREFERHOSTINGURLS`

Чтобы задать это значение, используйте переменную среды или вызов `PreferHostingUrls`:

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

Запрещает автоматическую загрузку начальных сборок размещения, включая начальные сборки размещения, настроенные сборкой приложения. Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.

**Ключ**: `preventHostingStartup`  
**Тип**: `bool` (`true` или `1`)  
**По умолчанию**: `false`  
**Переменная среды**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`

Чтобы задать это значение, используйте переменную среды или вызов `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

Сборка, в которой необходимо искать класс `Startup`.

**Ключ**: `startupAssembly`  
**Тип**: `string`  
**По умолчанию**: сборка приложения  
**Переменная среды**: `<PREFIX_>STARTUPASSEMBLY`

Чтобы задать это значение, используйте переменную среды или вызов `UseStartup`. `UseStartup` может использовать имя сборки (`string`) или тип (`TStartup`). При вызове нескольких методов `UseStartup` приоритет имеет последний.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>URL-адреса

Разделенный точками с запятой список IP-адресов или адресов узлов с портами и протоколами, по которым сервер должен ожидать получения запросов. Например, `http://localhost:123`. Используйте символ "\*", чтобы указать, что сервер должен ожидать получения запросов через определенный порт и по определенному протоколу по любому IP-адресу или имени узла (например, `http://*:5000`). Протокол (`http://` или `https://`) должен указываться для каждого URL-адреса. Поддерживаемые форматы зависят от сервера.

**Ключ**: `urls`  
**Тип**: `string`  
**Значения по умолчанию**: `http://localhost:5000` и `https://localhost:5001`  
**Переменная среды**: `<PREFIX_>URLS`

Чтобы задать это значение, используйте переменную среды или вызов `UseUrls`:

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel имеет собственный интерфейс API настройки конечных точек. Для получения дополнительной информации см. <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>WebRoot

Свойство [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) определяет относительный путь к статическим ресурсам приложения. Если этот путь не существует, используется фиктивный поставщик файлов.  

**Ключ:** `webroot`  
**Тип**: `string`  
**По умолчанию**: Значение по умолчанию — `wwwroot`. Наличие пути *{корневой_каталог_содержимого}/wwwroot* обязательно.  
**Переменная среды**: `<PREFIX_>WEBROOT`

Чтобы задать это значение, используйте переменную среды или вызов `UseWebRoot` в `IWebHostBuilder`:

```csharp
webBuilder.UseWebRoot("public");
```

Дополнительные сведения можно найти в разделе

* [Корневой каталог документов](xref:fundamentals/index#web-root)
* [ContentRoot](#contentroot)

## <a name="manage-the-host-lifetime"></a>Управление временем существования узла

Вызывает методы в реализации <xref:Microsoft.Extensions.Hosting.IHost> для запуска и остановки приложения. Эти методы влияют на все реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, зарегистрированные в контейнере службы.

### <a name="run"></a>Выполнить

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> запускает приложение и блокирует вызывающий поток, пока работа узла не будет завершена.

### <a name="runasync"></a>RunAsync

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> запускает приложение и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы.

### <a name="runconsoleasync"></a>RunConsoleAsync

Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> включает поддержку консоли, собирает и запускает узел и ожидает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM для завершения работы.

### <a name="start"></a>Начало

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> запускает узел синхронно.

### <a name="startasync"></a>StartAsync

Метод <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> запускает узел и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы. 

Метод <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> вызывается в начале `StartAsync`, который ждет, пока он не будет завершен, прежде чем продолжить. Можно отложить запуск до получения сигнала от внешнего события.

### <a name="stopasync"></a>StopAsync

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> пытается остановить узел в течение указанного периода ожидания.

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> блокирует вызывающий поток до завершения работы, активированного IHostLifetime, например через <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> возвращает объект <xref:System.Threading.Tasks.Task>, который завершается после активации завершения работы через токен, и вызывает метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

### <a name="external-control"></a>Внешнее управление

Прямое управление временем существования узла может осуществляться с помощью методов, которые могут быть вызваны извне:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Приложения ASP.NET Core настраивают и запускают узел. Узел отвечает за запуск приложения и управление временем существования.

В этой статье рассматривается универсальный узел ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), используемый для приложений, которые не обрабатывают запросы HTTP.

Универсальный узел предназначен для отделения конвейера HTTP от API веб-узла, чтобы можно было иметь больше сценариев узла. Обмен сообщениями, фоновые задачи и другие рабочие нагрузки, не связанные с HTTP, используют перекрестные возможности универсальных узлов, такие как конфигурация, внедрение зависимости (DI) и ведение журналов.

Универсальный узел является новой возможностью ASP.NET Core 2.1 и не подходит для сценариев с веб-размещением. Сведения о сценариях с веб-размещением см. в статье о [веб-узле](xref:fundamentals/host/web-host). Универсальный узел заменит веб-узел в будущих версиях. Он будет выступать основным узлом API для сценариев HTTP и "не HTTP".

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

При выполнении примера приложения в [Visual Studio Code](https://code.visualstudio.com/) используйте *внешний или встроенный терминал*. Не выполняйте пример в `internalConsole`.

Настройка консоли в Visual Studio Code:

1. Откройте файл *.vscode/launch.json*.
1. В разделе конфигурации **.NET Core Launch (console)** найдите параметр **console**. Измените значение на `externalTerminal` или `integratedTerminal`.

## <a name="introduction"></a>Вступление

Библиотека универсального узла находится в пространстве имен <xref:Microsoft.Extensions.Hosting> и включена в пакет [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/). Пакет `Microsoft.Extensions.Hosting` входит в состав [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 или более поздней версии).

<xref:Microsoft.Extensions.Hosting.IHostedService> — это точка входа для выполнения кода. Каждая реализация <xref:Microsoft.Extensions.Hosting.IHostedService> выполняется в порядке [регистрации служб в ConfigureServices](#configureservices). Метод <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> вызывается в каждом <xref:Microsoft.Extensions.Hosting.IHostedService> при запуске узла, а метод <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> вызывается в порядке, обратном регистрации, когда узел корректно завершает работу.

## <a name="set-up-a-host"></a>Создание узла

<xref:Microsoft.Extensions.Hosting.IHostBuilder> является основным компонентом, который библиотеки и приложения используют для инициализации, построения и запуска узла:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a>Параметры

<xref:Microsoft.Extensions.Hosting.HostOptions> настраивает параметры для <xref:Microsoft.Extensions.Hosting.IHost>.

### <a name="shutdown-timeout"></a>Время ожидания завершения работы

<xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> задает время ожидания для <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>. Значение по умолчанию — пять секунд.

Следующий пример конфигурации в `Program.Main` увеличивает значение времени ожидания завершения работы по умолчанию с 5 до 20 секунд.

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a>Службы по умолчанию

Во время инициализации узла регистрируются следующие службы:

* [Окружение](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* [Конфигурация](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)
* <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHost>
* [Параметры](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)
* [Ведение журнала](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)

## <a name="host-configuration"></a>Конфигурация узла

Существуют следующие подходы для создания конфигурации узла:

* вызов методов расширения в <xref:Microsoft.Extensions.Hosting.IHostBuilder> для задания [корневой папки содержимого](#content-root) и [среды](#environment);
* чтение конфигурации из поставщиков конфигурации в <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.

### <a name="extension-methods"></a>Методы расширения

### <a name="application-key-name"></a>Ключ приложения (имя)

Свойство [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) задается в конфигурации узла во время создания узла. Чтобы явно задать значение, используйте [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey).

**Ключ:** `applicationName`  
**Тип**: `string`  
**По умолчанию**: имя сборки, содержащей точку входа приложения.  
**Задается с помощью**: `HostBuilderContext.HostingEnvironment.ApplicationName`  
**Переменная среды**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>`[необязательно и определяется пользователем](#configurehostconfiguration))

### <a name="content-root"></a>Корневой каталог содержимого

Этот параметр определяет, где узел начинает искать файлы содержимого.

**Ключ:** `contentRoot`  
**Тип**: `string`  
**По умолчанию**: папка, в которой находится сборка приложения.  
**Задается с помощью**: `UseContentRoot`  
**Переменная среды**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>`[необязательно и определяется пользователем](#configurehostconfiguration))

Если путь не существует, узел не запускается.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

Дополнительные сведения можно найти в разделе [Корневой каталог содержимого](xref:fundamentals/index#content-root).

### <a name="environment"></a>Среда

Задает [среду](xref:fundamentals/environments) приложения.

**Ключ:** `environment`  
**Тип**: `string`  
**По умолчанию**: `Production`  
**Задается с помощью**: `UseEnvironment`  
**Переменная среды**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>`[необязательно и определяется пользователем](#configurehostconfiguration))

В качестве среды можно указать любое значение. В платформе определены значения `Development`, `Staging` и `Production`. Регистр символов в значениях не учитывается.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a>ConfigureHostConfiguration

Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> использует <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, чтобы создать экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> для узла. Конфигурация узла применяется для инициализации <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> в целях использования в процессе сборки приложения.

Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> может вызываться несколько раз с накоплением результатов. Узел использует значение, заданное последним для данного ключа.

Поставщики не включены по умолчанию. Необходимо явно указать поставщики конфигурации, требуемые приложению в <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, в том числе:

* конфигурация файла (например, из файла *hostsettings.json*);
* конфигурация переменной среды;
* конфигурация аргумента командной строки;
* другие необходимые поставщики конфигурации.

Конфигурация файла узла включается путем указания основного пути приложения с помощью `SetBasePath` и последующего вызова одного из [поставщиков конфигурации файла](xref:fundamentals/configuration/index#file-configuration-provider). В примере приложения используется JSON-файл *hostsettings.json* и вызывается метод <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> для использования параметров конфигурации узла файла.

Чтобы добавить [конфигурацию переменной среды](xref:fundamentals/configuration/index#environment-variables-configuration-provider) узла, вызовите метод <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> в построителе узла. `AddEnvironmentVariables` принимает необязательный определяемый пользователем префикс. В примере приложения используется префикс `PREFIX_`. Префикс удаляется при чтении переменных среды. После настройки узла в примере приложения значение переменной среды для `PREFIX_ENVIRONMENT` становится значением конфигурации узла для ключа `environment`.

Во время разработки с использованием [Visual Studio](https://visualstudio.microsoft.com) или запуска приложения с помощью `dotnet run` переменные среды можно задать в файле *Properties/launchSettings.json*. В [Visual Studio Code](https://code.visualstudio.com/) переменные среды можно задавать в файле *.vscode/launch.json* во время разработки. Для получения дополнительной информации см. <xref:fundamentals/environments>.

[Конфигурация командной строки](xref:fundamentals/configuration/index#command-line-configuration-provider) добавляется путем вызова метода <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>. Конфигурация командной строки добавляется в последнюю очередь, чтобы разрешить аргументам командной строки переопределять конфигурацию, предоставленную предыдущими поставщиками конфигурации.

*hostsettings.json*:

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

Дополнительную конфигурацию можно указать с помощью ключей [applicationName](#application-key-name) и [contentRoot](#content-root).

Пример конфигурации `HostBuilder` с использованием <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Конфигурация приложения создается путем вызова метода <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> в реализации <xref:Microsoft.Extensions.Hosting.IHostBuilder>. Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> использует <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, чтобы создать экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> для приложения. Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> может вызываться несколько раз с накоплением результатов. Приложение использует значение, заданное последним для данного ключа. Конфигурация, созданная с помощью <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, доступна в свойствах [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) для последующих операций и в <xref:Microsoft.Extensions.Hosting.IHost.Services*>.

Конфигурация приложения автоматически получает конфигурацию узла, предоставленную с помощью [ConfigureHostConfiguration](#configurehostconfiguration).

Пример конфигурации приложения с использованием <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

*appsettings.json*:

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

*appsettings.Development.json*:

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

*appsettings.Production.json*:

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

Чтобы переместить файлы параметров в выходной каталог, укажите файлы параметров как [элементы проекта MSBuild](/visualstudio/msbuild/common-msbuild-project-items) в файле проекта. Пример приложения перемещает свои файлы параметров приложения JSON и *hostsettings.json* со следующим элементом `<Content>`:

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> Для таких методов расширения конфигурации, как <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> и <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, необходимы дополнительные пакеты NuGet, такие как [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) и [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables). Если приложение не использует [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), эти пакеты нужно добавить в проект в дополнение к основному пакету [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration). Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.

## <a name="configureservices"></a>ConfigureServices

Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> добавляет службы в контейнер [внедрения зависимостей](xref:fundamentals/dependency-injection) приложения. Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> может вызываться несколько раз с накоплением результатов.

Размещенная служба — это класс с логикой фоновой задачи, реализующий интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService>. Для получения дополнительной информации см. <xref:fundamentals/host/hosted-services>.

[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) использует метод расширения `AddHostedService` для добавления службы для событий времени жизни (`LifetimeEventsHostedService`) и синхронизированной фоновой задачи (`TimedHostedService`):

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a>ConfigureLogging

Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> добавляет делегат для настройки указанного интерфейса <xref:Microsoft.Extensions.Logging.ILoggingBuilder>. Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> может вызываться несколько раз с накоплением результатов.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a>UseConsoleLifetime

Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> прослушивает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>//SIGINT или SIGTERM и вызывает метод <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> для запуска процесса завершения работы. Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> разблокирует расширения, такие как [RunAsync](#runasync) и [WaitForShutdownAsync](#waitforshutdownasync). Класс `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` предварительно зарегистрирован и является реализацией времени жизни по умолчанию. Используется то время жизни, которое зарегистрировано последним.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a>Конфигурация контейнера

Для поддержки подключения к другим контейнерам узел может принимать <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>. Предоставляет фабрику, не являющуюся частью регистрации контейнера внедрения зависимостей, а являющуюся встроенной функцией узла, которая используется для создания конкретных контейнеров внедрения зависимостей. [UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) переопределяет фабрику по умолчанию, используемую для создания поставщика службы приложения.

Пользовательская конфигурация контейнера управляется методом <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>. Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> обеспечивает возможности строго типизированной настройки контейнера на основе базового API узла. Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> может вызываться несколько раз с накоплением результатов.

Создание контейнера службы для приложения:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

Настройка фабрики контейнера службы:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

Использование фабрики и настройка пользовательского контейнера служб для приложения:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a>Расширение среды

Расширяемость узла выполняется с помощью методов расширения класса <xref:Microsoft.Extensions.Hosting.IHostBuilder>. В следующем примере показано, как метод расширения расширяет реализацию класса <xref:Microsoft.Extensions.Hosting.IHostBuilder> с помощью класса [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), пример которого приведен в статье <xref:fundamentals/host/hosted-services>.

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

Приложение устанавливает метод расширения `UseHostedService`, чтобы зарегистрировать размещенную службу, переданную в `T`:

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a>Управление узлом

Реализация <xref:Microsoft.Extensions.Hosting.IHost> отвечает за запуск и остановку реализаций <xref:Microsoft.Extensions.Hosting.IHostedService>, которые зарегистрированы в контейнере службы.

### <a name="run"></a>Выполнить

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> запускает приложение и блокирует вызывающий поток, пока работа узла не будет завершена:

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a>RunAsync

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> запускает приложение и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершение работы:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a>RunConsoleAsync

Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> включает поддержку консоли, собирает и запускает узел и ожидает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM для завершения работы.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a>Start и StopAsync

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> запускает узел синхронно.

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> пытается остановить узел в течение указанного периода ожидания.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a>StartAsync и StopAsync

Метод <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> запускает приложение.

Метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> останавливает приложение.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a>WaitForShutdown

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> запускается с помощью <xref:Microsoft.Extensions.Hosting.IHostLifetime>, например `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (прослушивает <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM). <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> вызывает <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> возвращает объект <xref:System.Threading.Tasks.Task>, который завершается после активации завершения работы через токен, и вызывает метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a>Внешнее управление

Внешнее управление узла может осуществляться с помощью методов, которые могут быть вызваны извне:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

Метод <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> вызывается в начале <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, который ждет, пока он не будет завершен, прежде чем продолжить. Можно отложить запуск до получения сигнала от внешнего события.

## <a name="ihostingenvironment-interface"></a>Интерфейс IHostingEnvironment

Интерфейс <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> предоставляет сведения о среде размещения приложения. Чтобы получить интерфейс <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> для использования его свойств и методов расширения, воспользуйтесь [внедрением конструктора](xref:fundamentals/dependency-injection):

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

Для получения дополнительной информации см. <xref:fundamentals/environments>.

## <a name="iapplicationlifetime-interface"></a>Интерфейс IApplicationLifetime

Интерфейс <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> позволяет выполнять действия после запуска и завершения работы, включая запросы о нормальном завершении работы. Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов <xref:System.Action>, определяющих события запуска и завершения работы.

| Токен отмены | Условие инициации&#8230; |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | Узел полностью запущен. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | Заканчивается нормальное завершение работы узла. Все запросы должны быть обработаны. Завершение работы блокируется до тех пор, пока это событие не завершится. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | Происходит нормальное завершение работы узла. Запросы могут все еще обрабатываться. Завершение работы блокируется до тех пор, пока это событие не завершится. |

Конструктор внедряет службу <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> в любой класс. [Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) использует внедрение в конструкторе в класс `LifetimeEventsHostedService` (реализацию <xref:Microsoft.Extensions.Hosting.IHostedService>) для регистрации событий.

*LifetimeEventsHostedService.cs*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

Метод <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> запрашивает остановку приложения. Следующий класс использует <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> для корректного завершения работы приложения при вызове метода класса `Shutdown`:

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/host/hosted-services>
