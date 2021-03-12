---
title: Использование начальных сборок размещения в ASP.NET Core
author: rick-anderson
description: Узнайте, как улучшить приложение ASP.NET Core из внешней сборки, используя реализацию IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: b39215a70f990afeb7d3fe0a62981113b154354e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588247"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>Использование начальных сборок размещения в ASP.NET Core

Автор: [Павел Крымец](https://github.com/pakrym) (Pavel Krymets)

::: moniker range=">= aspnetcore-3.0"

Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (размещение при запуске) позволяет добавлять в приложение улучшения из внешней сборки при запуске. Например, внешняя библиотека может использовать реализацию размещения при запуске, чтобы доставить дополнительные поставщики конфигурации или службы для приложения.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>Атрибут HostingStartup

Атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) указывает на наличие начальной сборки размещения, которая будет активирована во время выполнения.

Входная сборка или сборка, содержащая класс `Startup`, автоматически сканируется на наличие атрибута `HostingStartup`. Список сборок, в котором будет выполняться поиск атрибута `HostingStartup`, загружается во время выполнения из конфигурации в [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). Список сборок, которые необходимо исключить из обнаружения, загружается из [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).

В следующем примере пространство имен для начальной сборки размещения — `StartupEnhancement`. Класс, содержащий код запуска размещения, — `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Атрибут `HostingStartup` обычно находится в файле класса реализации начальной сборки размещения `IHostingStartup`.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Обнаружение загруженных начальных сборок размещения

Для обнаружения загруженных сборок размещения при запуске включите ведение журнала и проверьте журналы приложения. В журнал вносятся ошибки, возникающие при загрузке сборок. Загруженные начальные сборки размещения регистрируются на уровне отладки, также регистрируются все ошибки.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Отключение автоматической загрузки начальных сборок размещения

Чтобы отключить автоматическую загрузку начальных сборок размещения, используйте один из следующих подходов:

* Чтобы предотвратить загрузку всех начальных сборок размещения, установите значение `true` или `1` для одного из следующих параметров:

  * Параметр конфигурации узла "Запретить размещение при запуске".

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * Переменная среды `ASPNETCORE_PREVENTHOSTINGSTARTUP`.

* Чтобы предотвратить загрузку конкретных сборок размещения, установите строку, содержащую разделенный точками с запятой список сборок размещения, которые необходимо исключить при запуске, в качестве значения одного из следующих параметров:

  * Параметр конфигурации узла "Исключаемые сборки размещения при запуске".

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * Переменная среды `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.

Если заданы параметры конфигурации узла и переменная среды, на поведение влияют параметры узла.

При отключении сборок размещения при запуске с использованием параметра узла или переменной среды сборка отключается на глобальном уровне, и также могут быть отключены некоторые характеристики приложения.

## <a name="project"></a>Проект

Создайте размещение при запуске для любого из указанных ниже типов проектов:

* [Библиотека классов](#class-library)
* [Консольное приложение без точки входа](#console-app-without-an-entry-point)

### <a name="class-library"></a>Библиотека классов

Расширение размещения при запуске можно указать в библиотеке классов. Библиотека содержит атрибут `HostingStartup`.

[Пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) включает приложение Razor Pages *HostingStartupApp* и библиотеку классов *HostingStartupLibrary*. Библиотека классов:

* Содержит класс размещения при запуске `ServiceKeyInjection`, который реализует `IHostingStartup`. `ServiceKeyInjection` добавляет пару строк службы в конфигурацию приложения с помощью поставщика конфигурации, размещаемой в памяти ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Включает атрибут `HostingStartup`, определяющий пространство имен и класс размещения при запуске.

Метод `ServiceKeyInjection` класса <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> использует <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> для добавления улучшений в приложение.

*HostingStartupLibrary/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Страница индекса приложения считывает и отображает значения конфигурации для двух ключей, устанавливаемых начальной сборкой размещения библиотеки класса:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) также включает проект пакета NuGet, предоставляющий отдельное размещение при запуске *HostingStartupPackage*. Характеристики пакета аналогичны характеристикам библиотеки классов, приведенным ранее. Пакет:

* Содержит класс размещения при запуске `ServiceKeyInjection`, который реализует `IHostingStartup`. `ServiceKeyInjection` добавляет пару строк службы в конфигурацию приложения.
* Включает атрибут `HostingStartup`.

*HostingStartupPackage/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Страница индекса приложения считывает и отображает значения конфигурации для двух ключей, устанавливаемых начальной сборкой размещения пакета:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Консольное приложение без точки входа

*Этот подход может использоваться только для приложений .NET Core, но не для приложений .NET Framework.*

Расширение динамического размещения при запуске, для активации которого не требуется ссылка во время компиляции, может быть реализовано в консольном приложении без точки входа, содержащем атрибут `HostingStartup`. При публикации консольного приложения создается начальная сборка размещения, которая может использоваться в хранилище среды выполнения.

В этом процессе используется консольное приложение без точки входа, так как:

* Файл зависимостей необходим для функционирования размещения при запуске в начальной сборке размещения. Файл зависимостей является ресурсом исполняемого приложения, который создается путем публикации приложения, а не библиотеки.
* Библиотеку невозможно добавить непосредственно в [хранилище пакетов среды выполнения](/dotnet/core/deploying/runtime-store), для которого требуется запускаемый проект для общей среды выполнения.

В ходе создания динамического размещения при запуске:

* Начальная сборка размещения создается из консольного приложения без точки входа, которое:
  * содержит класс с реализацией `IHostingStartup`;
  * содержит атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) для определения класса реализации `IHostingStartup`.
* Консольное приложение публикуется для получения зависимостей начальной загрузки. После публикации консольного приложения неиспользуемые зависимости исключаются из файла зависимостей.
* Файл зависимостей изменяется, чтобы задать расположение среды выполнения начальной сборки размещения.
* Начальная сборка размещения и соответствующий файл зависимостей размещаются в хранилище пакетов среды выполнения. Чтобы можно было обнаружить начальную сборку размещения и ее файл зависимостей, они указываются в паре переменных среды.

Консольное приложение ссылается на пакет [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

Атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) определяет класс как реализацию `IHostingStartup` для загрузки и выполнения при построении <xref:Microsoft.AspNetCore.Hosting.IWebHost>. В следующем примере используется пространство имен `StartupEnhancement` и класс `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Класс реализует `IHostingStartup`. Метод класса <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> использует <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> для добавления улучшений в приложение. `IHostingStartup.Configure` в начальной сборке размещения вызывается средой выполнения до `Startup.Configure` в пользовательском коде, что позволяет пользовательскому коду перезаписать конфигурацию, предоставленную начальной сборкой размещения.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

При создании проекта `IHostingStartup` файл зависимостей ( *.deps.json*) задает расположение `runtime` для сборки в папке *bin*:

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Показана только часть файла. Имя сборки в примере — `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Конфигурация, предоставляемая размещением при запуске

Существует два подхода к подготовке конфигурации в зависимости от того, какой конфигурацией вы хотите отдать приоритет — конфигурации размещения при запуске или конфигурации приложения:

1. Задайте конфигурацию приложению, используя <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> для загрузки конфигурации после выполнения делегатов приложения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>. При таком подходе конфигурация размещения при запуске будет иметь приоритет над конфигурацией приложения.
1. Задайте конфигурацию приложению, используя <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> для загрузки конфигурации до выполнения делегатов приложения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>. При таком подходе конфигурация приложения будет иметь приоритет над конфигурацией размещения при запуске.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Указание начальных сборок размещения

Для библиотеки класса или размещения при запуске с помощью консольного приложения укажите имя начальной сборки размещения в переменной среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`. Переменная среды — это список сборок, разделенный точками с запятой.

Только начальные сборки размещения проверяются на наличие атрибута `HostingStartup`. Для примера приложения *HostingStartupApp* необходимо установить следующее значение для переменной среды, чтобы обнаружить сборки размещения при запуске, описанные ранее:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Начальную сборку размещения также можно задать с помощью параметра конфигурации узла "Начальные сборки размещения".

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

При наличии нескольких начальных сборок размещения их методы <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> выполняются в порядке расположения сборок.

## <a name="activation"></a>Активация

Ниже приведены варианты активации размещения при запуске:

* [Хранилище среды выполнения.](#runtime-store) Для активации не требуется ссылка во время компиляции. Пример приложения помещает начальную сборку размещения и файлы зависимостей в папку *deployment*, чтобы облегчить развертывание размещения при запуске в среде с несколькими компьютерами. Папка *deployment* также включает сценарий PowerShell, который создает или изменяет переменные среды в системе развертывания, чтобы включить размещение при запуске.
* Ссылки во время компиляции, необходимые для активации
  * [Пакет NuGet](#nuget-package)
  * [Папка bin проекта](#project-bin-folder)

### <a name="runtime-store"></a>Хранилище среды выполнения

Реализация размещения при запуске помещается в [хранилище среды выполнения](/dotnet/core/deploying/runtime-store). Ссылка на сборку во время компиляции не требуется расширенному приложению.

После начальной сборки размещения создается хранилище среды выполнения с помощью файла манифеста проекта и команды [dotnet store](/dotnet/core/tools/dotnet-store).

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

В примере приложения (проект *RuntimeStore*) используется такая команда:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Чтобы среда выполнения могла обнаружить хранилище среды выполнения, расположение хранилища среды выполнения добавляется к переменной среды `DOTNET_SHARED_STORE`.

**Изменение и размещение файла зависимостей размещения при запуске**

Чтобы активировать расширение без ссылки на пакет расширения, укажите дополнительные зависимости в среде выполнения с помощью `additionalDeps`. `additionalDeps` предоставляет следующие возможности:

* Расширять граф библиотеки приложения, предоставляя набор дополнительных файлов *.deps.json* для объединения с собственным файлом *.deps.json* приложения при запуске.
* Обнаруживать и скачивать начальную сборку размещения.

Рекомендуемые действия при создании файла дополнительных зависимостей.

 1. Выполните команду `dotnet publish` для файла манифеста хранилища среды выполнения, ссылка на который создавалась в предыдущем разделе.
 1. Удалите ссылку на манифест из библиотек и раздела `runtime` итогового файла *deps.json*.

В примере проекта свойство `store.manifest/1.0.0` удаляется из разделов `targets` и `libraries`.

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Поместите файл *.deps.json* в следующее расположение:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`. Расположение, добавляемое к переменной среды `DOTNET_ADDITIONAL_DEPS`.
* `{SHARED FRAMEWORK NAME}`. Общая платформа, требуемая для этого файла дополнительных зависимостей.
* `{SHARED FRAMEWORK VERSION}`. Минимальная версия общей платформы.
* `{ENHANCEMENT ASSEMBLY NAME}`. Имя сборки расширения.

В примере приложения (проект *RuntimeStore*) файл дополнительных зависимостей помещается в следующее расположение:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

Чтобы среда выполнения могла обнаружить хранилище среды выполнения, расположение файла дополнительных зависимостей добавляется к переменной среды `DOTNET_ADDITIONAL_DEPS`.

В примере приложения (проект *RuntimeStore*) для сборки хранилища среды выполнения и создания файла дополнительных зависимостей используется скрипт [PowerShell](/powershell/scripting/powershell-scripting).

Примеры установки переменных среды для различных операционных систем см. в разделе [Использование нескольких сред](xref:fundamentals/environments).

**Развертывание**

Чтобы упростить развертывание размещения при запуске в среде с несколькими компьютерами, пример приложения создает в опубликованном результате папку *deployment*. Эта папка содержит:

* Хранилище среды выполнения размещения при запуске.
* Файл зависимостей размещения при запуске.
* Скрипт PowerShell, который создает или изменяет `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` и `DOTNET_ADDITIONAL_DEPS` для поддержки активации размещения при запуске. Запустите сценарий из командной строки PowerShell с правами администратора в системе развертывания.

### <a name="nuget-package"></a>Пакет NuGet

Расширение размещения при запуске можно указать в пакете NuGet. Пакет содержит атрибут `HostingStartup`. Типы размещения при запуске, предоставляемые пакетом, становятся доступными для приложения с использованием одного из следующих методов:

* Файл проекта расширенного приложения создает ссылку на пакет для размещения при запуске в файле проекта приложения (ссылка во время компиляции). Со ссылкой во время компиляции начальная сборка размещения и все ее зависимости включаются в файл зависимостей приложения ( *.deps.json*). Этот подход применяется к пакету начальной сборки размещения, опубликованному на [nuget.org](https://www.nuget.org/).
* Файл зависимостей размещения при запуске становится доступным для расширенного приложения, как описано в разделе [Хранилище среды выполнения](#runtime-store) (без ссылок во время компиляции).

Дополнительные сведения о пакетах NuGet и хранилище среды выполнения см. в разделах:

* [Создание пакета NuGet с помощью кроссплатформенных средств](/dotnet/core/deploying/creating-nuget-packages)
* [Публикация пакетов](/nuget/create-packages/publish-a-package)
* [Хранилище пакетов среды выполнения](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Папка bin проекта

Расширение размещения при запуске может быть представлено сборкой, разворачиваемой в папке *bin* расширенного приложения. Типы размещения при запуске, предоставляемые сборкой, становятся доступными для приложения с использованием одного из следующих методов:

* Файл проекта расширенного приложения создает ссылку сборки на размещение при запуске (ссылка во время компиляции). Со ссылкой во время компиляции начальная сборка размещения и все ее зависимости включаются в файл зависимостей приложения ( *.deps.json*). Этот подход применяется при вызове сценария развертывания для создания ссылки времени компиляции на сборку запуска размещения (*DLL*-файл) и перемещения сборки в одно из следующих мест:
  * Проект, который будет использовать ее.
  * Расположение, доступное использующему проекту.
* Файл зависимостей размещения при запуске становится доступным для расширенного приложения, как описано в разделе [Хранилище среды выполнения](#runtime-store) (без ссылок во время компиляции).
* При нацеливании на .NET Framework сборка загружается в контексте загрузки по умолчанию, что для .NET Framework означает, что сборка находится в одном из следующих расположений:
  * Базовый путь приложения. Папка *bin*, в которой находится исполняемый файл приложения ( *.exe*).
  * Глобальный кэш сборок. В глобальном кэше сборок сохраняются сборки, которые могут использоваться несколькими приложениями .NET Framework. Дополнительные сведения см. в разделе [Практическое руководство. Установка сборки в глобальный кэш сборок](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) в документации по .NET Framework.

## <a name="sample-code"></a>Пример кода

В [примере кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([инструкции по скачиванию примера](xref:index#how-to-download-a-sample)) показаны сценарии реализации размещения при запуске:

* В каждой из двух сборок начального размещения (библиотеки классов) устанавливаются две пары "ключ-значение", хранящиеся в памяти:
  * Пакет NuGet (*HostingStartupPackage*)
  * Библиотека классов (*HostingStartupLibrary*)
* Размещения при запуске активируется из сборки среды выполнения, развертываемой из хранилища (*StartupDiagnostics*). Эта сборка добавляет два вида ПО промежуточного слоя, которые предоставляют диагностические сведения о следующих показателях:
  * Зарегистрированные службы
  * Адрес (схема, узел, базовый путь, путь, строка запроса)
  * Подключение (удаленный IP-адрес, удаленный порт, локальный IP-адрес, локальный порт, сертификат клиента)
  * Заголовки запросов
  * Переменные среды

Для выполнения образца:

**Активация из библиотеки классов**

1. Скомпилируйте пакет *HostingStartupPackage* с помощью команды [dotnet pack](/dotnet/core/tools/dotnet-pack).
1. Добавьте имя сборки пакета *HostingStartupPackage* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. Скомпилируйте и запустите приложение. Ссылка на пакет появится в расширенном приложении (ссылка во время компиляции). Объект `<PropertyGroup>` в файле проекта приложения определяет выходные данные проекта пакета ( *../ HostingStartupPackage/bin/Debug*) в качестве источника пакета. Это позволяет приложению использовать пакет без отправки пакета на сайт [nuget.org](https://www.nuget.org/). Дополнительные сведения см. в примечаниях в файле проекта HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Обратите внимание, что ключевые значения конфигурации службы, отображаемой на индексной странице, соответствуют значениям, установленным с помощью метода пакета `ServiceKeyInjection.Configure`.

Если вы внесли изменения в проект *HostingStartupPackage* и перекомпилировали его, очистите локальные кэши пакета NuGet, чтобы убедиться, что *HostingStartupApp* получает обновленный пакет, а не устаревший пакет из локального кэша. Чтобы очистить локальные кэши NuGet, выполните следующую команду [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):

```dotnetcli
dotnet nuget locals all --clear
```

**Активации из библиотеки классов**

1. Скомпилируйте библиотеку классов *HostingStartupLibrary* с помощью команды [dotnet build](/dotnet/core/tools/dotnet-build).
1. Добавьте имя сборки библиотеки классов *HostingStartupLibrary* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. Разверните сборку библиотеки классов в папку *bin* приложения. Для этого скопируйте файл *HostingStartupLibrary.dll* из выходной папки компиляции библиотеки в папку *bin/Debug* приложения.
1. Скомпилируйте и запустите приложение. `<ItemGroup>` в файле проекта приложения ссылается на сборку библиотеки класса ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (ссылка во время компиляции). Дополнительные сведения см. в примечаниях в файле проекта HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. Обратите внимание, что ключевые значения конфигурации службы, отображаемой на индексной странице, соответствуют значениям, установленным с помощью метода библиотеки класса `ServiceKeyInjection.Configure`.

**Активация из сборки среды выполнения, развернутой в хранилище**

1. В проекте *StartupDiagnostics* используется [PowerShell](/powershell/scripting/powershell-scripting) для изменения файла *StartupDiagnostics.deps.json*. PowerShell устанавливается по умолчанию в Windows начиная с Windows 7 с пакетом обновления 1 (SP1) и Windows Server 2008 R2 с пакетом обновления 1 (SP1). Для установки PowerShell на других платформах см. раздел [Установка разных версий PowerShell](/powershell/scripting/install/installing-powershell).
1. Выполните сценарий *build.ps1*, находящийся в папке *RuntimeStore*. Скрипт выполняет следующее:
   * Генерирует пакет `StartupDiagnostics` в папке *obj\packages*.
   * Создает хранилище среды выполнения для `StartupDiagnostics` в папке *store*. Для размещения при запуске, развернутом в Windows, команда `dotnet store` в сценарии использует [идентификатор среды выполнения (RID)](/dotnet/core/rid-catalog) `win7-x64`. При выполнении размещения при запуске для другой среды выполнения укажите соответствующий RID в строке 37 скрипта. Хранилище среды выполнения для `StartupDiagnostics` позже будет перемещено в хранилище среды выполнения пользователя или системы на компьютере, где будет израсходована сборка. Пользовательское место установки хранилища среды выполнения для сборки `StartupDiagnostics` — *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.
   * Генерирует `additionalDeps` для `StartupDiagnostics` в папке *additionalDeps*. Дополнительные зависимости позднее будут перенесены в дополнительные зависимости пользователя или системы. Расположением установки дополнительных зависимостей пользователя `StartupDiagnostics` является *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.
   * Размещает файл *deploy.ps1* в папке для *развертывания*.
1. Выполните сценарий *deploy.ps1*, находящийся в папке *deployment*. Скрипт присоединяет:
   * `StartupDiagnostics` к переменной среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
   * Путь размещения начальных зависимостей (в папке *развертывания* проекта "RuntimeStore") к переменной среды `DOTNET_ADDITIONAL_DEPS`.
   * Путь хранилища среды выполнения (в папке *развертывания* проекта "RuntimeStore") к переменной среды `DOTNET_SHARED_STORE`.
1. Выполните пример приложения.
1. Запросите конечную точку `/services` для просмотра зарегистрированных служб приложения. Запросите конечную точку `/diag` для просмотра диагностических сведений.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (размещение при запуске) позволяет добавлять в приложение улучшения из внешней сборки при запуске. Например, внешняя библиотека может использовать реализацию размещения при запуске, чтобы доставить дополнительные поставщики конфигурации или службы для приложения.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>Атрибут HostingStartup

Атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) указывает на наличие начальной сборки размещения, которая будет активирована во время выполнения.

Входная сборка или сборка, содержащая класс `Startup`, автоматически сканируется на наличие атрибута `HostingStartup`. Список сборок, в котором будет выполняться поиск атрибута `HostingStartup`, загружается во время выполнения из конфигурации в [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). Список сборок, которые необходимо исключить из обнаружения, загружается из [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey). Дополнительные сведения см. в руководстве по [настройке Размещение начальных сборок](xref:fundamentals/host/web-host#hosting-startup-assemblies) и [Веб-узел: исключаемые начальные сборки размещения](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).

В следующем примере пространство имен для начальной сборки размещения — `StartupEnhancement`. Класс, содержащий код запуска размещения, — `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Атрибут `HostingStartup` обычно находится в файле класса реализации начальной сборки размещения `IHostingStartup`.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Обнаружение загруженных начальных сборок размещения

Для обнаружения загруженных сборок размещения при запуске включите ведение журнала и проверьте журналы приложения. В журнал вносятся ошибки, возникающие при загрузке сборок. Загруженные начальные сборки размещения регистрируются на уровне отладки, также регистрируются все ошибки.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Отключение автоматической загрузки начальных сборок размещения

Чтобы отключить автоматическую загрузку начальных сборок размещения, используйте один из следующих подходов:

* Чтобы предотвратить загрузку всех начальных сборок размещения, установите значение `true` или `1` для одного из следующих параметров:
  * Параметр конфигурации узла [Запретить размещение при запуске](xref:fundamentals/host/web-host#prevent-hosting-startup).
  * Переменная среды `ASPNETCORE_PREVENTHOSTINGSTARTUP`.
* Чтобы предотвратить загрузку конкретных сборок размещения, установите строку, содержащую разделенный точками с запятой список сборок размещения, которые необходимо исключить при запуске, в качестве значения одного из следующих параметров:
  * Параметр конфигурации узла [Исключаемые сборки размещения при запуске](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).
  * Переменная среды `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.

Если заданы параметры конфигурации узла и переменная среды, на поведение влияют параметры узла.

При отключении сборок размещения при запуске с использованием параметра узла или переменной среды сборка отключается на глобальном уровне, и также могут быть отключены некоторые характеристики приложения.

## <a name="project"></a>Проект

Создайте размещение при запуске для любого из указанных ниже типов проектов:

* [Библиотека классов](#class-library)
* [Консольное приложение без точки входа](#console-app-without-an-entry-point)

### <a name="class-library"></a>Библиотека классов

Расширение размещения при запуске можно указать в библиотеке классов. Библиотека содержит атрибут `HostingStartup`.

[Пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) включает приложение Razor Pages *HostingStartupApp* и библиотеку классов *HostingStartupLibrary*. Библиотека классов:

* Содержит класс размещения при запуске `ServiceKeyInjection`, который реализует `IHostingStartup`. `ServiceKeyInjection` добавляет пару строк службы в конфигурацию приложения с помощью поставщика конфигурации, размещаемой в памяти ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Включает атрибут `HostingStartup`, определяющий пространство имен и класс размещения при запуске.

Метод `ServiceKeyInjection` класса <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> использует <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> для добавления улучшений в приложение.

*HostingStartupLibrary/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Страница индекса приложения считывает и отображает значения конфигурации для двух ключей, устанавливаемых начальной сборкой размещения библиотеки класса:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) также включает проект пакета NuGet, предоставляющий отдельное размещение при запуске *HostingStartupPackage*. Характеристики пакета аналогичны характеристикам библиотеки классов, приведенным ранее. Пакет:

* Содержит класс размещения при запуске `ServiceKeyInjection`, который реализует `IHostingStartup`. `ServiceKeyInjection` добавляет пару строк службы в конфигурацию приложения.
* Включает атрибут `HostingStartup`.

*HostingStartupPackage/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Страница индекса приложения считывает и отображает значения конфигурации для двух ключей, устанавливаемых начальной сборкой размещения пакета:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Консольное приложение без точки входа

*Этот подход может использоваться только для приложений .NET Core, но не для приложений .NET Framework.*

Расширение динамического размещения при запуске, для активации которого не требуется ссылка во время компиляции, может быть реализовано в консольном приложении без точки входа, содержащем атрибут `HostingStartup`. При публикации консольного приложения создается начальная сборка размещения, которая может использоваться в хранилище среды выполнения.

В этом процессе используется консольное приложение без точки входа, так как:

* Файл зависимостей необходим для функционирования размещения при запуске в начальной сборке размещения. Файл зависимостей является ресурсом исполняемого приложения, который создается путем публикации приложения, а не библиотеки.
* Библиотеку невозможно добавить непосредственно в [хранилище пакетов среды выполнения](/dotnet/core/deploying/runtime-store), для которого требуется запускаемый проект для общей среды выполнения.

В ходе создания динамического размещения при запуске:

* Начальная сборка размещения создается из консольного приложения без точки входа, которое:
  * содержит класс с реализацией `IHostingStartup`;
  * содержит атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) для определения класса реализации `IHostingStartup`.
* Консольное приложение публикуется для получения зависимостей начальной загрузки. После публикации консольного приложения неиспользуемые зависимости исключаются из файла зависимостей.
* Файл зависимостей изменяется, чтобы задать расположение среды выполнения начальной сборки размещения.
* Начальная сборка размещения и соответствующий файл зависимостей размещаются в хранилище пакетов среды выполнения. Чтобы можно было обнаружить начальную сборку размещения и ее файл зависимостей, они указываются в паре переменных среды.

Консольное приложение ссылается на пакет [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

Атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) определяет класс как реализацию `IHostingStartup` для загрузки и выполнения при построении <xref:Microsoft.AspNetCore.Hosting.IWebHost>. В следующем примере используется пространство имен `StartupEnhancement` и класс `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Класс реализует `IHostingStartup`. Метод класса <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> использует <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> для добавления улучшений в приложение. `IHostingStartup.Configure` в начальной сборке размещения вызывается средой выполнения до `Startup.Configure` в пользовательском коде, что позволяет пользовательскому коду перезаписать конфигурацию, предоставленную начальной сборкой размещения.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

При создании проекта `IHostingStartup` файл зависимостей ( *.deps.json*) задает расположение `runtime` для сборки в папке *bin*:

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Показана только часть файла. Имя сборки в примере — `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Конфигурация, предоставляемая размещением при запуске

Существует два подхода к подготовке конфигурации в зависимости от того, какой конфигурацией вы хотите отдать приоритет — конфигурации размещения при запуске или конфигурации приложения:

1. Задайте конфигурацию приложению, используя <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> для загрузки конфигурации после выполнения делегатов приложения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>. При таком подходе конфигурация размещения при запуске будет иметь приоритет над конфигурацией приложения.
1. Задайте конфигурацию приложению, используя <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> для загрузки конфигурации до выполнения делегатов приложения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>. При таком подходе конфигурация приложения будет иметь приоритет над конфигурацией размещения при запуске.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Указание начальных сборок размещения

Для библиотеки класса или размещения при запуске с помощью консольного приложения укажите имя начальной сборки размещения в переменной среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`. Переменная среды — это список сборок, разделенный точками с запятой.

Только начальные сборки размещения проверяются на наличие атрибута `HostingStartup`. Для примера приложения *HostingStartupApp* необходимо установить следующее значение для переменной среды, чтобы обнаружить сборки размещения при запуске, описанные ранее:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Начальную сборку размещения также можно задать с помощью параметра конфигурации узла [Начальные сборки размещения](xref:fundamentals/host/web-host#hosting-startup-assemblies).

При наличии нескольких начальных сборок размещения их методы <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> выполняются в порядке расположения сборок.

## <a name="activation"></a>Активация

Ниже приведены варианты активации размещения при запуске:

* [Хранилище среды выполнения.](#runtime-store) Для активации не требуется ссылка во время компиляции. Пример приложения помещает начальную сборку размещения и файлы зависимостей в папку *deployment*, чтобы облегчить развертывание размещения при запуске в среде с несколькими компьютерами. Папка *deployment* также включает сценарий PowerShell, который создает или изменяет переменные среды в системе развертывания, чтобы включить размещение при запуске.
* Ссылки во время компиляции, необходимые для активации
  * [Пакет NuGet](#nuget-package)
  * [Папка bin проекта](#project-bin-folder)

### <a name="runtime-store"></a>Хранилище среды выполнения

Реализация размещения при запуске помещается в [хранилище среды выполнения](/dotnet/core/deploying/runtime-store). Ссылка на сборку во время компиляции не требуется расширенному приложению.

После начальной сборки размещения создается хранилище среды выполнения с помощью файла манифеста проекта и команды [dotnet store](/dotnet/core/tools/dotnet-store).

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

В примере приложения (проект *RuntimeStore*) используется такая команда:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Чтобы среда выполнения могла обнаружить хранилище среды выполнения, расположение хранилища среды выполнения добавляется к переменной среды `DOTNET_SHARED_STORE`.

**Изменение и размещение файла зависимостей размещения при запуске**

Чтобы активировать расширение без ссылки на пакет расширения, укажите дополнительные зависимости в среде выполнения с помощью `additionalDeps`. `additionalDeps` предоставляет следующие возможности:

* Расширять граф библиотеки приложения, предоставляя набор дополнительных файлов *.deps.json* для объединения с собственным файлом *.deps.json* приложения при запуске.
* Обнаруживать и скачивать начальную сборку размещения.

Рекомендуемые действия при создании файла дополнительных зависимостей.

 1. Выполните команду `dotnet publish` для файла манифеста хранилища среды выполнения, ссылка на который создавалась в предыдущем разделе.
 1. Удалите ссылку на манифест из библиотек и раздела `runtime` итогового файла *deps.json*.

В примере проекта свойство `store.manifest/1.0.0` удаляется из разделов `targets` и `libraries`.

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Поместите файл *.deps.json* в следующее расположение:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`. Расположение, добавляемое к переменной среды `DOTNET_ADDITIONAL_DEPS`.
* `{SHARED FRAMEWORK NAME}`. Общая платформа, требуемая для этого файла дополнительных зависимостей.
* `{SHARED FRAMEWORK VERSION}`. Минимальная версия общей платформы.
* `{ENHANCEMENT ASSEMBLY NAME}`. Имя сборки расширения.

В примере приложения (проект *RuntimeStore*) файл дополнительных зависимостей помещается в следующее расположение:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

Чтобы среда выполнения могла обнаружить хранилище среды выполнения, расположение файла дополнительных зависимостей добавляется к переменной среды `DOTNET_ADDITIONAL_DEPS`.

В примере приложения (проект *RuntimeStore*) для сборки хранилища среды выполнения и создания файла дополнительных зависимостей используется скрипт [PowerShell](/powershell/scripting/powershell-scripting).

Примеры установки переменных среды для различных операционных систем см. в разделе [Использование нескольких сред](xref:fundamentals/environments).

**Развертывание**

Чтобы упростить развертывание размещения при запуске в среде с несколькими компьютерами, пример приложения создает в опубликованном результате папку *deployment*. Эта папка содержит:

* Хранилище среды выполнения размещения при запуске.
* Файл зависимостей размещения при запуске.
* Скрипт PowerShell, который создает или изменяет `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` и `DOTNET_ADDITIONAL_DEPS` для поддержки активации размещения при запуске. Запустите сценарий из командной строки PowerShell с правами администратора в системе развертывания.

### <a name="nuget-package"></a>Пакет NuGet

Расширение размещения при запуске можно указать в пакете NuGet. Пакет содержит атрибут `HostingStartup`. Типы размещения при запуске, предоставляемые пакетом, становятся доступными для приложения с использованием одного из следующих методов:

* Файл проекта расширенного приложения создает ссылку на пакет для размещения при запуске в файле проекта приложения (ссылка во время компиляции). Со ссылкой во время компиляции начальная сборка размещения и все ее зависимости включаются в файл зависимостей приложения ( *.deps.json*). Этот подход применяется к пакету начальной сборки размещения, опубликованному на [nuget.org](https://www.nuget.org/).
* Файл зависимостей размещения при запуске становится доступным для расширенного приложения, как описано в разделе [Хранилище среды выполнения](#runtime-store) (без ссылок во время компиляции).

Дополнительные сведения о пакетах NuGet и хранилище среды выполнения см. в разделах:

* [Создание пакета NuGet с помощью кроссплатформенных средств](/dotnet/core/deploying/creating-nuget-packages)
* [Публикация пакетов](/nuget/create-packages/publish-a-package)
* [Хранилище пакетов среды выполнения](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Папка bin проекта

Расширение размещения при запуске может быть представлено сборкой, разворачиваемой в папке *bin* расширенного приложения. Типы размещения при запуске, предоставляемые сборкой, становятся доступными для приложения с использованием одного из следующих методов:

* Файл проекта расширенного приложения создает ссылку сборки на размещение при запуске (ссылка во время компиляции). Со ссылкой во время компиляции начальная сборка размещения и все ее зависимости включаются в файл зависимостей приложения ( *.deps.json*). Этот подход применяется при вызове сценария развертывания для создания ссылки времени компиляции на сборку запуска размещения (*DLL*-файл) и перемещения сборки в одно из следующих мест:
  * Проект, который будет использовать ее.
  * Расположение, доступное использующему проекту.
* Файл зависимостей размещения при запуске становится доступным для расширенного приложения, как описано в разделе [Хранилище среды выполнения](#runtime-store) (без ссылок во время компиляции).
* При нацеливании на .NET Framework сборка загружается в контексте загрузки по умолчанию, что для .NET Framework означает, что сборка находится в одном из следующих расположений:
  * Базовый путь приложения. Папка *bin*, в которой находится исполняемый файл приложения ( *.exe*).
  * Глобальный кэш сборок. В глобальном кэше сборок сохраняются сборки, которые могут использоваться несколькими приложениями .NET Framework. Дополнительные сведения см. в разделе [Практическое руководство. Установка сборки в глобальный кэш сборок](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) в документации по .NET Framework.

## <a name="sample-code"></a>Пример кода

В [примере кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([инструкции по скачиванию примера](xref:index#how-to-download-a-sample)) показаны сценарии реализации размещения при запуске:

* В каждой из двух сборок начального размещения (библиотеки классов) устанавливаются две пары "ключ-значение", хранящиеся в памяти:
  * Пакет NuGet (*HostingStartupPackage*)
  * Библиотека классов (*HostingStartupLibrary*)
* Размещения при запуске активируется из сборки среды выполнения, развертываемой из хранилища (*StartupDiagnostics*). Эта сборка добавляет два вида ПО промежуточного слоя, которые предоставляют диагностические сведения о следующих показателях:
  * Зарегистрированные службы
  * Адрес (схема, узел, базовый путь, путь, строка запроса)
  * Подключение (удаленный IP-адрес, удаленный порт, локальный IP-адрес, локальный порт, сертификат клиента)
  * Заголовки запросов
  * Переменные среды

Для выполнения образца:

**Активация из библиотеки классов**

1. Скомпилируйте пакет *HostingStartupPackage* с помощью команды [dotnet pack](/dotnet/core/tools/dotnet-pack).
1. Добавьте имя сборки пакета *HostingStartupPackage* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. Скомпилируйте и запустите приложение. Ссылка на пакет появится в расширенном приложении (ссылка во время компиляции). Объект `<PropertyGroup>` в файле проекта приложения определяет выходные данные проекта пакета ( *../ HostingStartupPackage/bin/Debug*) в качестве источника пакета. Это позволяет приложению использовать пакет без отправки пакета на сайт [nuget.org](https://www.nuget.org/). Дополнительные сведения см. в примечаниях в файле проекта HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Обратите внимание, что ключевые значения конфигурации службы, отображаемой на индексной странице, соответствуют значениям, установленным с помощью метода пакета `ServiceKeyInjection.Configure`.

Если вы внесли изменения в проект *HostingStartupPackage* и перекомпилировали его, очистите локальные кэши пакета NuGet, чтобы убедиться, что *HostingStartupApp* получает обновленный пакет, а не устаревший пакет из локального кэша. Чтобы очистить локальные кэши NuGet, выполните следующую команду [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):

```dotnetcli
dotnet nuget locals all --clear
```

**Активации из библиотеки классов**

1. Скомпилируйте библиотеку классов *HostingStartupLibrary* с помощью команды [dotnet build](/dotnet/core/tools/dotnet-build).
1. Добавьте имя сборки библиотеки классов *HostingStartupLibrary* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. Разверните сборку библиотеки классов в папку *bin* приложения. Для этого скопируйте файл *HostingStartupLibrary.dll* из выходной папки компиляции библиотеки в папку *bin/Debug* приложения.
1. Скомпилируйте и запустите приложение. `<ItemGroup>` в файле проекта приложения ссылается на сборку библиотеки класса ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (ссылка во время компиляции). Дополнительные сведения см. в примечаниях в файле проекта HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. Обратите внимание, что ключевые значения конфигурации службы, отображаемой на индексной странице, соответствуют значениям, установленным с помощью метода библиотеки класса `ServiceKeyInjection.Configure`.

**Активация из сборки среды выполнения, развернутой в хранилище**

1. В проекте *StartupDiagnostics* используется [PowerShell](/powershell/scripting/powershell-scripting) для изменения файла *StartupDiagnostics.deps.json*. PowerShell устанавливается по умолчанию в Windows начиная с Windows 7 с пакетом обновления 1 (SP1) и Windows Server 2008 R2 с пакетом обновления 1 (SP1). Для установки PowerShell на других платформах см. раздел [Установка разных версий PowerShell](/powershell/scripting/install/installing-powershell).
1. Выполните сценарий *build.ps1*, находящийся в папке *RuntimeStore*. Скрипт выполняет следующее:
   * Генерирует пакет `StartupDiagnostics` в папке *obj\packages*.
   * Создает хранилище среды выполнения для `StartupDiagnostics` в папке *store*. Для размещения при запуске, развернутом в Windows, команда `dotnet store` в сценарии использует [идентификатор среды выполнения (RID)](/dotnet/core/rid-catalog) `win7-x64`. При выполнении размещения при запуске для другой среды выполнения укажите соответствующий RID в строке 37 скрипта. Хранилище среды выполнения для `StartupDiagnostics` позже будет перемещено в хранилище среды выполнения пользователя или системы на компьютере, где будет израсходована сборка. Пользовательское место установки хранилища среды выполнения для сборки `StartupDiagnostics` — *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.
   * Генерирует `additionalDeps` для `StartupDiagnostics` в папке *additionalDeps*. Дополнительные зависимости позднее будут перенесены в дополнительные зависимости пользователя или системы. Расположением установки дополнительных зависимостей пользователя `StartupDiagnostics` является *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.
   * Размещает файл *deploy.ps1* в папке для *развертывания*.
1. Выполните сценарий *deploy.ps1*, находящийся в папке *deployment*. Скрипт присоединяет:
   * `StartupDiagnostics` к переменной среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
   * Путь размещения начальных зависимостей (в папке *развертывания* проекта "RuntimeStore") к переменной среды `DOTNET_ADDITIONAL_DEPS`.
   * Путь хранилища среды выполнения (в папке *развертывания* проекта "RuntimeStore") к переменной среды `DOTNET_SHARED_STORE`.
1. Выполните пример приложения.
1. Запросите конечную точку `/services` для просмотра зарегистрированных служб приложения. Запросите конечную точку `/diag` для просмотра диагностических сведений.

::: moniker-end
