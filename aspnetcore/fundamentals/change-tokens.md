---
title: Обнаружение изменений с помощью токенов изменений в ASP.NET Core
author: rick-anderson
description: Сведения об использовании токенов изменений для отслеживания изменений.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
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
uid: fundamentals/change-tokens
ms.openlocfilehash: df2be1b89ad9681ff70dd71cb3026786f59c8b2a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589391"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>Обнаружение изменений с помощью токенов изменений в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

*Токен изменений* — это низкоуровневый стандартный блок общего назначения, используемый для отслеживания изменений.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/change-tokens/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Интерфейс IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken> распространяет уведомления о том, что произошло изменение. `IChangeToken` находится в пространстве имен <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. Пакет [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet явно предоставляется приложениям ASP.NET Core.

`IChangeToken` имеет два свойства:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> указывает, выполняет ли токен обратные вызовы с упреждением. Если для `ActiveChangedCallbacks` задано значение `false`, обратный вызов не выполняется, а приложению нужно опросить `HasChanged` на предмет изменений. Кроме того, отмена токена может никогда не произойти в случае отсутствия изменений или отключения либо удаления базового прослушивателя изменений.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> получает значение, указывающее, произошло ли изменение.

Интерфейс `IChangeToken` включает метод [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), который регистрирует обратный вызов, выполняемый при изменении токена. Перед выполнением обратного вызова нужно задать `HasChanged`.

## <a name="changetoken-class"></a>Класс ChangeToken

<xref:Microsoft.Extensions.Primitives.ChangeToken> — это статический класс, который распространяет уведомления о том, что произошло изменение. `ChangeToken` находится в пространстве имен <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. Пакет [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet явно предоставляется приложениям ASP.NET Core.

Метод [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) регистрирует `Action` для вызова при изменении токена.

* `Func<IChangeToken>` создает токен.
* `Action` вызывается при изменении токена.

Перегрузка [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) принимает дополнительный параметр `TState`, передаваемый в `Action` объекта-получатель токена.

`OnChange` возвращает <xref:System.IDisposable>. При вызове <xref:System.IDisposable.Dispose*> токен прекращает прослушивать дальнейшие изменения и освобождает свои ресурсы.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Примеры использования токенов изменений в ASP.NET Core

Токены изменений используются в ключевых областях ASP.NET Core для отслеживания изменений в объектах:

* Чтобы отслеживать изменения в файлах, метод <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> интерфейса <xref:Microsoft.Extensions.FileProviders.IFileProvider> создает `IChangeToken` для указанных файлов или папки.
* Токены `IChangeToken` можно добавлять в записи кэша, чтобы активировать вытеснения кэша при изменении.
* Для изменений `TOptions` используемая по умолчанию реализация <xref:Microsoft.Extensions.Options.OptionsMonitor`1> интерфейса <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> имеет перегрузку, которая принимает один или несколько экземпляров <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>. Каждый экземпляр возвращает `IChangeToken`, чтобы зарегистрировать обратный вызов уведомления об изменении для отслеживания изменений параметров.

## <a name="monitor-for-configuration-changes"></a>Отслеживание изменений конфигурации

По умолчанию шаблоны ASP.NET Core используют [файлы конфигурации JSON](xref:fundamentals/configuration/index#json-configuration-provider) ( *appsettings.json* , *appsettings.Development.json* и *appsettings.Production.json*) для загрузки параметров конфигурации приложения.

Эти файлы настраиваются с помощью метода расширения [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) в <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, который принимает параметр `reloadOnChange`. `reloadOnChange` указывает, нужно ли перезагружать конфигурацию при изменении файла. Этот параметр отображается в удобном методе <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> класса <xref:Microsoft.Extensions.Hosting.Host>:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Конфигурация на основе файла представлена <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource` использует <xref:Microsoft.Extensions.FileProviders.IFileProvider> для отслеживания файлов.

По умолчанию `IFileMonitor` предоставляется объектом <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, который использует <xref:System.IO.FileSystemWatcher> для отслеживания изменений в файле конфигурации.

Этот пример приложения демонстрирует две реализации для отслеживания изменений конфигурации. Если какой-либо из файлов *appsettings* был изменен, каждая реализация отслеживания файлов выполняет пользовательский код &mdash; пример приложения записывает сообщение на консоль.

`FileSystemWatcher` файла конфигурации может активировать несколько обратных вызовов токена для одного изменения файла конфигурации. Чтобы пользовательский код выполнялся один раз при активации нескольких обратных вызовов токена, реализация в примере проверяет хэши файлов. В примере используется хэширование файлов SHA1. Повторная попытка реализуется посредством экспоненциальной задержки. Повторная попытка нужна, так как может возникать блокировка файлов, препятствующая вычислению нового хэша в файле.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Токен изменений для простого запуска

Зарегистрируйте обратный вызов `Action` объекта-получателя токена для уведомлений об изменениях в токене перезагрузки конфигурации.

В `Startup.Configure`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` предоставляет токен. Обратный вызов является методом `InvokeChanged`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

`state` обратного вызова используется для передачи `IWebHostEnvironment`. Это удобно для определения правильного JSON-файла конфигурации *appsettings*, который требуется отслеживать (например, *appsettings.Development.json* при работе в среде разработки). Хэши файлов используются для предотвращения многократного выполнения оператора `WriteConsole` из-за нескольких обратных вызовов токена при всего одном изменении файла конфигурации.

Эта система выполняется, пока запущено приложение, и не может быть отключена пользователем.

### <a name="monitor-configuration-changes-as-a-service"></a>Отслеживание изменений конфигурации как служба

Этот пример реализует следующее:

* Базовое отслеживание токена запуска.
* Отслеживание как служба.
* Механизм для включения и отключения отслеживания.

Этот пример задает интерфейс `IConfigurationMonitor`.

*Extensions/ConfigurationMonitor.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Конструктор реализованного класса `ConfigurationMonitor` регистрирует обратный вызов для уведомлений об изменениях:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` предоставляет токен. `InvokeChanged` является методом обратного вызова. `state` в этом экземпляре является ссылкой на экземпляр `IConfigurationMonitor`, используемый для доступа к состоянию мониторинга. Используются два свойства:

* `MonitoringEnabled`. Указывает, нужно ли обратному вызову выполнять свой настраиваемый код.
* `CurrentState`. Описывает текущее состояние отслеживания для использования в пользовательском интерфейсе.

Метод `InvokeChanged` похож на описанный ранее подход, за исключением того, что он:

* не выполняет свой код, если только `MonitoringEnabled` не имеет значение `true`;
* выводит текущее значение `state` в своих выходных данных `WriteConsole`.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Экземпляр `ConfigurationMonitor` регистрируется как служба в `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Страница индекса позволяет пользователю управлять отслеживанием конфигурации. Экземпляр `IConfigurationMonitor` внедряется в `IndexModel`.

*Pages/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Монитор конфигурации (`_monitor`) позволяет включить или отключить мониторинг и задать текущее состояние для обратной связи пользовательского интерфейса:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

При активации `OnPostStartMonitoring` отслеживание включается, а текущее состояние сбрасывается. При активации `OnPostStopMonitoring` отслеживание отключается, а состояние указывает на отсутствие отслеживания.

Кнопки пользовательского интерфейса для включения и отключения отслеживания.

*Pages/Index.cshtml*:

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Отслеживание изменений кэшированных файлов

Содержимое файла можно кэшировать в памяти с помощью <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. Кэширование в памяти описано в разделе [Кэш в памяти](xref:performance/caching/memory). *Устаревшие* (просроченные) данные возвращаются из кэша при изменении источника исходных данных без каких-либо дополнительных действий, таких как описанная ниже реализация.

Например, если не учитывать состояние кэшированного исходного файла при продлении [скользящего срока действия](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration), это приведет к появлению в кэше устаревших данных файлов. Каждый запрос к данным продляет скользящий срок действия, но этот файл никогда не загружается в кэш. Любые функции приложения, использующие кэшированное содержимое, могут получить устаревшее содержимое.

Использование токенов изменений в сценарии кэширования файлов предотвращает появление устаревшего содержимого файлов в кэше. Этот пример демонстрирует реализацию данного подхода.

`GetFileContent` в примере используется для:

* возврата содержимого файла;
* реализации алгоритма повторных попыток с экспоненциальной задержкой, чтобы охватить ситуации, когда блокировка файла временно препятствует его считыванию.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Для обработки поиска кэшированных файлов создается `FileService`. Направленный в службу вызов метода `GetFileContent` пытается получить содержимое файла из кэша в памяти и вернуть его вызывающему объекту (*Services/FileService.cs*).

Если кэшированное содержимое не удается найти по ключу кэша, выполняются следующие действия:

1. Содержимое файла получается с помощью `GetFileContent`.
1. Токен изменений извлекается из файла поставщика с помощью [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). При изменении файла активируется обратный вызов токена.
1. Содержимое файла кэшируется с использованием [скользящего срока действия](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration). Токен изменений подключается к [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) для исключения записи кэша, если файл изменяется во время его кэширования.

В следующем примере файлы хранятся в [корневом каталоге содержимого](xref:fundamentals/index#content-root) приложения. `IWebHostEnvironment.ContentRootFileProvider` используется для получения <xref:Microsoft.Extensions.FileProviders.IFileProvider> с указанием на приложение `IWebHostEnvironment.ContentRootPath`. Для получения `filePath` используется [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

`FileService` регистрируется в контейнере службы вместе со службой кэширования памяти.

В `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Страничная модель загружает содержимое файла с помощью службы.

На странице индексов используется метод `OnGet` (*Pages/Index.cshtml.cs*):

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Класс CompositeChangeToken

Чтобы представить один или несколько экземпляров `IChangeToken` в одном объекте, используйте класс <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

`HasChanged` для составного токена указывает `true`, если `HasChanged` любой из представленных токенов имеет значение `true`. `ActiveChangeCallbacks` для составного токена указывает `true`, если `ActiveChangeCallbacks` любой из представленных токенов имеет значение `true`. Если возникает несколько одновременных событий изменения, обратный вызов составного изменения выполняется один раз.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*Токен изменений* — это низкоуровневый стандартный блок общего назначения, используемый для отслеживания изменений.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/change-tokens/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Интерфейс IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken> распространяет уведомления о том, что произошло изменение. `IChangeToken` находится в пространстве имен <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. Для приложений, которые не используют метапакет [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), создайте ссылку на пакет NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).

`IChangeToken` имеет два свойства:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> указывает, выполняет ли токен обратные вызовы с упреждением. Если для `ActiveChangedCallbacks` задано значение `false`, обратный вызов не выполняется, а приложению нужно опросить `HasChanged` на предмет изменений. Кроме того, отмена токена может никогда не произойти в случае отсутствия изменений или отключения либо удаления базового прослушивателя изменений.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> получает значение, указывающее, произошло ли изменение.

Интерфейс `IChangeToken` включает метод [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), который регистрирует обратный вызов, выполняемый при изменении токена. Перед выполнением обратного вызова нужно задать `HasChanged`.

## <a name="changetoken-class"></a>Класс ChangeToken

<xref:Microsoft.Extensions.Primitives.ChangeToken> — это статический класс, который распространяет уведомления о том, что произошло изменение. `ChangeToken` находится в пространстве имен <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. Для приложений, которые не используют метапакет [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), создайте ссылку на пакет NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).

Метод [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) регистрирует `Action` для вызова при изменении токена.

* `Func<IChangeToken>` создает токен.
* `Action` вызывается при изменении токена.

Перегрузка [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) принимает дополнительный параметр `TState`, передаваемый в `Action` объекта-получатель токена.

`OnChange` возвращает <xref:System.IDisposable>. При вызове <xref:System.IDisposable.Dispose*> токен прекращает прослушивать дальнейшие изменения и освобождает свои ресурсы.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Примеры использования токенов изменений в ASP.NET Core

Токены изменений используются в ключевых областях ASP.NET Core для отслеживания изменений в объектах:

* Чтобы отслеживать изменения в файлах, метод <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> интерфейса <xref:Microsoft.Extensions.FileProviders.IFileProvider> создает `IChangeToken` для указанных файлов или папки.
* Токены `IChangeToken` можно добавлять в записи кэша, чтобы активировать вытеснения кэша при изменении.
* Для изменений `TOptions` используемая по умолчанию реализация <xref:Microsoft.Extensions.Options.OptionsMonitor`1> интерфейса <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> имеет перегрузку, которая принимает один или несколько экземпляров <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>. Каждый экземпляр возвращает `IChangeToken`, чтобы зарегистрировать обратный вызов уведомления об изменении для отслеживания изменений параметров.

## <a name="monitor-for-configuration-changes"></a>Отслеживание изменений конфигурации

По умолчанию шаблоны ASP.NET Core используют [файлы конфигурации JSON](xref:fundamentals/configuration/index#json-configuration-provider) ( *appsettings.json* , *appsettings.Development.json* и *appsettings.Production.json*) для загрузки параметров конфигурации приложения.

Эти файлы настраиваются с помощью метода расширения [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) в <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, который принимает параметр `reloadOnChange`. `reloadOnChange` указывает, нужно ли перезагружать конфигурацию при изменении файла. Этот параметр отображается в удобном методе <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> класса <xref:Microsoft.AspNetCore.WebHost>:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Конфигурация на основе файла представлена <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource` использует <xref:Microsoft.Extensions.FileProviders.IFileProvider> для отслеживания файлов.

По умолчанию `IFileMonitor` предоставляется объектом <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, который использует <xref:System.IO.FileSystemWatcher> для отслеживания изменений в файле конфигурации.

Этот пример приложения демонстрирует две реализации для отслеживания изменений конфигурации. Если какой-либо из файлов *appsettings* был изменен, каждая реализация отслеживания файлов выполняет пользовательский код &mdash; пример приложения записывает сообщение на консоль.

`FileSystemWatcher` файла конфигурации может активировать несколько обратных вызовов токена для одного изменения файла конфигурации. Чтобы пользовательский код выполнялся один раз при активации нескольких обратных вызовов токена, реализация в примере проверяет хэши файлов. В примере используется хэширование файлов SHA1. Повторная попытка реализуется посредством экспоненциальной задержки. Повторная попытка нужна, так как может возникать блокировка файлов, препятствующая вычислению нового хэша в файле.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Токен изменений для простого запуска

Зарегистрируйте обратный вызов `Action` объекта-получателя токена для уведомлений об изменениях в токене перезагрузки конфигурации.

В `Startup.Configure`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` предоставляет токен. Обратный вызов является методом `InvokeChanged`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

`state` обратного вызова используется для передачи `IHostingEnvironment`. Это удобно для определения правильного JSON-файла конфигурации *appsettings*, который требуется отслеживать (например, *appsettings.Development.json* при работе в среде разработки). Хэши файлов используются для предотвращения многократного выполнения оператора `WriteConsole` из-за нескольких обратных вызовов токена при всего одном изменении файла конфигурации.

Эта система выполняется, пока запущено приложение, и не может быть отключена пользователем.

### <a name="monitor-configuration-changes-as-a-service"></a>Отслеживание изменений конфигурации как служба

Этот пример реализует следующее:

* Базовое отслеживание токена запуска.
* Отслеживание как служба.
* Механизм для включения и отключения отслеживания.

Этот пример задает интерфейс `IConfigurationMonitor`.

*Extensions/ConfigurationMonitor.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Конструктор реализованного класса `ConfigurationMonitor` регистрирует обратный вызов для уведомлений об изменениях:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` предоставляет токен. `InvokeChanged` является методом обратного вызова. `state` в этом экземпляре является ссылкой на экземпляр `IConfigurationMonitor`, используемый для доступа к состоянию мониторинга. Используются два свойства:

* `MonitoringEnabled`. Указывает, нужно ли обратному вызову выполнять свой настраиваемый код.
* `CurrentState`. Описывает текущее состояние отслеживания для использования в пользовательском интерфейсе.

Метод `InvokeChanged` похож на описанный ранее подход, за исключением того, что он:

* не выполняет свой код, если только `MonitoringEnabled` не имеет значение `true`;
* выводит текущее значение `state` в своих выходных данных `WriteConsole`.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Экземпляр `ConfigurationMonitor` регистрируется как служба в `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Страница индекса позволяет пользователю управлять отслеживанием конфигурации. Экземпляр `IConfigurationMonitor` внедряется в `IndexModel`.

*Pages/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Монитор конфигурации (`_monitor`) позволяет включить или отключить мониторинг и задать текущее состояние для обратной связи пользовательского интерфейса:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

При активации `OnPostStartMonitoring` отслеживание включается, а текущее состояние сбрасывается. При активации `OnPostStopMonitoring` отслеживание отключается, а состояние указывает на отсутствие отслеживания.

Кнопки пользовательского интерфейса для включения и отключения отслеживания.

*Pages/Index.cshtml*:

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Отслеживание изменений кэшированных файлов

Содержимое файла можно кэшировать в памяти с помощью <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. Кэширование в памяти описано в разделе [Кэш в памяти](xref:performance/caching/memory). *Устаревшие* (просроченные) данные возвращаются из кэша при изменении источника исходных данных без каких-либо дополнительных действий, таких как описанная ниже реализация.

Например, если не учитывать состояние кэшированного исходного файла при продлении [скользящего срока действия](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration), это приведет к появлению в кэше устаревших данных файлов. Каждый запрос к данным продляет скользящий срок действия, но этот файл никогда не загружается в кэш. Любые функции приложения, использующие кэшированное содержимое, могут получить устаревшее содержимое.

Использование токенов изменений в сценарии кэширования файлов предотвращает появление устаревшего содержимого файлов в кэше. Этот пример демонстрирует реализацию данного подхода.

`GetFileContent` в примере используется для:

* возврата содержимого файла;
* реализации алгоритма повторных попыток с экспоненциальной задержкой, чтобы охватить ситуации, когда блокировка файла временно препятствует его считыванию.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Для обработки поиска кэшированных файлов создается `FileService`. Направленный в службу вызов метода `GetFileContent` пытается получить содержимое файла из кэша в памяти и вернуть его вызывающему объекту (*Services/FileService.cs*).

Если кэшированное содержимое не удается найти по ключу кэша, выполняются следующие действия:

1. Содержимое файла получается с помощью `GetFileContent`.
1. Токен изменений извлекается из файла поставщика с помощью [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). При изменении файла активируется обратный вызов токена.
1. Содержимое файла кэшируется с использованием [скользящего срока действия](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration). Токен изменений подключается к [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) для исключения записи кэша, если файл изменяется во время его кэширования.

В следующем примере файлы хранятся в [корневом каталоге содержимого](xref:fundamentals/index#content-root) приложения. [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) используется для получения объекта <xref:Microsoft.Extensions.FileProviders.IFileProvider>, который указывает на <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> приложения. Для получения `filePath` используется [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

`FileService` регистрируется в контейнере службы вместе со службой кэширования памяти.

В `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Страничная модель загружает содержимое файла с помощью службы.

На странице индексов используется метод `OnGet` (*Pages/Index.cshtml.cs*):

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Класс CompositeChangeToken

Чтобы представить один или несколько экземпляров `IChangeToken` в одном объекте, используйте класс <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

`HasChanged` для составного токена указывает `true`, если `HasChanged` любой из представленных токенов имеет значение `true`. `ActiveChangeCallbacks` для составного токена указывает `true`, если `ActiveChangeCallbacks` любой из представленных токенов имеет значение `true`. Если возникает несколько одновременных событий изменения, обратный вызов составного изменения выполняется один раз.

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
