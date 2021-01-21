---
title: Новые возможности в ASP.NET Core 5.0
author: rick-anderson
description: Сведения о новых возможностях в ASP.NET Core 5.0.
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
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
- Kestrel
uid: aspnetcore-5.0
ms.openlocfilehash: 5caa412773bf9c8e3bed5ebc529d48b886de6956
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253076"
---
# <a name="whats-new-in-aspnet-core-50"></a>Новые возможности в ASP.NET Core 5.0

В этой статье описываются наиболее важные изменения в ASP.NET Core 5.0 со ссылками на соответствующую документацию.

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a>Улучшения ASP.NET Core MVC и Razor

### <a name="model-binding-datetime-as-utc"></a>Привязка модели DateTime в формате UTC

Привязка модели теперь поддерживает привязку строк времени в формате UTC к `DateTime`. Если запрос содержит строку времени в формате UTC, привязка модели привязывает ее к `DateTime` в формате UTC. Например, следующая строка времени привязывается к `DateTime` в формате UTC: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`

### <a name="model-binding-and-validation-with-c-9-record-types"></a>Привязка модели и проверка с помощью типов записей C# 9

[Типы записей C# 9](/dotnet/csharp/whats-new/csharp-9#record-types) можно использовать с привязкой модели в контроллере MVC или на странице Razor. Типы записей — это хороший способ моделирования передаваемых по сети данных.

Например, в следующем контроллере `PersonController` используется тип записи `Person` с привязкой модели и проверкой формы:

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

Файл `Person/Index.cshtml`:

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a>Усовершенствования DynamicRouteValueTransformer

В ASP.NET Core 3.1 появился <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> в качестве способа использования пользовательской конечной точки для динамического выбора действия контроллера MVC или страницы Razor. Приложения ASP.NET Core 5.0 могут передавать состояние в `DynamicRouteValueTransformer` и фильтровать выбранный набор конечных точек.

### <a name="miscellaneous"></a>Прочее

* Этот атрибут [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) можно применить к свойствам в модели страницы Razor.
* Параметры и свойства, привязанные из текста, считаются обязательными по умолчанию. <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a>Веб-интерфейс API

### <a name="openapi-specification-on-by-default"></a>Спецификация OpenAPI включена по умолчанию

[Спецификация OpenAPI](http://spec.openapis.org/oas/v3.0.3) является отраслевым стандартом для описания API-интерфейсов HTTP и их интеграции в сложные бизнес-процессы или со сторонними производителями. OpenAPI широко поддерживается всеми поставщиками облачных служб и многими реестрами API. В приложениях, создающих документы OpenAPI из веб-API, представлено множество новых возможностей, в которых можно использовать эти API-интерфейсы. В условиях партнерства с издателями проекта [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) с открытым кодом шаблон API для ASP.NET Core содержит зависимость NuGet от [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore). Swashbuckle — это популярный пакет NuGet с открытым кодом, который динамически создает документы OpenAPI. Он выполняет это путем самоанализа через контроллеры API и создания документа OpenAPI во время выполнения или во время сборки, используя интерфейс командной строки Swashbuckle.

В ASP.NET Core 5.0 шаблоны веб-API по умолчанию поддерживают OpenAPI. Чтобы отключить OpenAPI:

* Из командной строки.

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* Из Visual Studio: снимите флажок **Enable OpenAPI support** (Включить поддержку OpenAPI).

Все файлы *.csproj*, созданные для проектов веб-API, содержат ссылку на пакет NuGet [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/).

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

Код, созданный шаблоном, содержит код в `Startup.ConfigureServices`, который активирует создание документа OpenAPI:

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

Метод `Startup.Configure` добавляет ПО промежуточного слоя Swashbuckle, которое активирует:

* процесс создания документа;
* страницу пользовательского интерфейса Swagger по умолчанию в режиме разработки.

Созданный шаблоном код не станет по случайности предоставлять описание API при публикации в рабочей среде.

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a>Импорт Управления API Azure

Если проекты API для ASP.NET Core включают OpenAPI, Visual Studio 2019 версии 16.8 и более поздних публикаций автоматически предлагают дополнительный этап в потоке публикации. Разработчики, использующие [Управление API Azure](xref:tutorials/publish-to-azure-api-management-using-vs), имеют возможность автоматического импорта API-интерфейсов в Управление API Azure во время потока публикации:

![Импорт и публикация Управления API Azure](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a>Улучшенный интерфейс запуска для проектов веб-API

Если OpenAPI включен по умолчанию, интерфейс запуска приложений (F5) для разработчиков веб-API значительно улучшается. В ASP.NET Core 5.0 шаблон веб-API предварительно настроен для загрузки страницы пользовательского интерфейса Swagger. На странице пользовательского интерфейса Swagger содержится документация, добавленная для опубликованного API, и обеспечивается тестирование API-интерфейсов одним щелчком мыши.

![Представление swagger/index.html](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a>Усовершенствования в области производительности

В .NET 5 мы внесли значительные улучшения в производительность среды выполнения Blazor WebAssembly, делая основной акцент на сложной отрисовке пользовательского интерфейса и сериализации JSON. По результатам наших тестов производительности в большинстве сценариев скорость Blazor WebAssembly в .NET 5 в два-три раза выше. Дополнительные сведения см. в блоге ASP.NET [об обновлениях ASP.NET Core в .NET 5 (релиз-кандидат 1)](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).

### <a name="css-isolation"></a>Изоляция CSS

Теперь Blazor поддерживает определение стилей CSS, областью действия которых является данный компонент. Благодаря стилям CSS для отдельных компонентов проще ориентироваться в стилях в приложении и избежать непредвиденных побочных эффектов от применения глобальных стилей. Для получения дополнительной информации см. <xref:blazor/components/css-isolation>.

### <a name="new-inputfile-component"></a>Новый компонент `InputFile`

Компонент `InputFile` позволяет считывать один или несколько файлов, выбранных пользователем для отправки. Для получения дополнительной информации см. <xref:blazor/file-uploads>.

### <a name="new-inputradio-and-inputradiogroup-components"></a>Новые компоненты `InputRadio` и `InputRadioGroup`

Blazor содержит встроенные компоненты `InputRadio` и `InputRadioGroup`, которые упрощают привязку данных к группам переключателей со встроенной проверкой. Для получения дополнительной информации см. <xref:blazor/forms-validation>.

### <a name="component-virtualization"></a>Виртуализация компонентов

Повысьте воспринимаемую производительность отрисовки компонентов с помощью встроенной поддержки виртуализации платформы Blazor. Для получения дополнительной информации см. <xref:blazor/components/virtualization>.

### <a name="ontoggle-event-support"></a>Поддержка события `ontoggle`

События Blazor теперь поддерживают событие DOM `ontoggle`. Для получения дополнительной информации см. <xref:blazor/components/event-handling#event-argument-types>.

### <a name="set-ui-focus-in-no-locblazor-apps"></a>Установка фокуса пользовательского интерфейса в приложениях Blazor

Используйте удобный метод `FocusAsync` со ссылками на элемент, чтобы установить фокус пользовательского интерфейса на этот элемент. Для получения дополнительной информации см. <xref:blazor/components/event-handling#focus-an-element>.

### <a name="custom-validation-class-attributes"></a>Настраиваемые атрибуты класса проверки

Имена настраиваемых классов проверки полезны при интеграции с платформами CSS, такими как Bootstrap. Для получения дополнительной информации см. <xref:blazor/forms-validation#custom-validation-class-attributes>.

### <a name="iasyncdisposable-support"></a>Поддержка IAsyncDisposable

Теперь компоненты Blazor поддерживают интерфейс <xref:System.IAsyncDisposable> для асинхронного выпуска выделенных ресурсов.

### <a name="javascript-isolation-and-object-references"></a>Изоляция  JavaScript и ссылки на объекты

Blazor реализует изоляцию JavaScript в стандартных [модулях JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules). Для получения дополнительной информации см. <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

### <a name="form-components-support-display-name"></a>Поддержка компонентами форм отображаемого имени

Следующие встроенные компоненты поддерживают отображаемые имена с помощью параметра `DisplayName`.

* `InputDate`
* `InputNumber`
* `InputSelect`

Для получения дополнительной информации см. <xref:blazor/forms-validation#display-name-support>.

### <a name="catch-all-route-parameters"></a>Параметры маршрута catch-all

В компонентах поддерживаются параметры маршрута catch-all, которые захватывают пути в нескольких папках. Для получения дополнительной информации см. <xref:blazor/fundamentals/routing#catch-all-route-parameters>.

### <a name="debugging-improvements"></a>Усовершенствования отладки

В ASP.NET Core 5.0 улучшена отладка приложений Blazor WebAssembly. Кроме того, сейчас отладка поддерживается в Visual Studio для Mac. Для получения дополнительной информации см. <xref:blazor/debug>.

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a>Microsoft Identity версии 2.0 и MSAL версии 2.0

Теперь для безопасности Blazor используется Microsoft Identity версии 2.0 ([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) и [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)) и MSAL версии 2.0. Дополнительные сведения см. в статье о [безопасности Blazor и узле Identity](xref:blazor/security/index).

### <a name="protected-browser-storage-for-no-locblazor-server"></a>Защищенное хранилище браузера для Blazor Server

Теперь приложения Blazor Server могут использовать встроенную поддержку для хранения сведений о состоянии приложения в браузере, который уже защищен от незаконного изменения благодаря защите данных в ASP.NET Core. Данные могут храниться либо в локальном хранилище браузера, либо в хранилище сеансов. Для получения дополнительной информации см. <xref:blazor/state-management>.

### <a name="no-locblazor-webassembly-prerendering"></a>Предварительная отрисовка Blazor WebAssembly

Благодаря улучшенной интеграции компонентов в моделях размещения приложения Blazor WebAssembly теперь могут предварительно отрисовывать выходные данные на сервере. <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a>Улучшенная обрезка и компоновка

Blazor WebAssembly выполняет обрезку и компоновку промежуточного языка (IL) во время сборки, чтобы затем удалить ненужный IL из выходных сборок приложения. В выпуске ASP.NET Core 5.0 Blazor WebAssembly выполняет улучшенную обрезку с помощью дополнительных параметров конфигурации. Дополнительные сведения см. в статьях <xref:blazor/host-and-deploy/configure-trimmer> и [Параметры обрезки](/dotnet/core/deploying/trimming-options).

### <a name="browser-compatibility-analyzer"></a>Анализатор совместимости с браузерами

Приложения Blazor WebAssembly предназначены для использования в полной контактной зоне API .NET, но из-за ограничений песочницы браузера поддерживаются не все API-интерфейсы .NET. При выполнении в WebAssembly неподдерживаемые API-интерфейсы вызывают <xref:System.PlatformNotSupportedException>. Анализатор совместимости платформ предупреждает разработчика, когда приложение использует API-интерфейсы, которые не поддерживаются целевыми платформами приложения. Для получения дополнительной информации см. <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.

### <a name="lazy-load-assemblies"></a>Сборки с отложенной загрузкой

Производительность запуска приложения Blazor WebAssembly можно повысить, отложив загрузку некоторых сборок приложений, пока они не потребуются. Для получения дополнительной информации см. <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="updated-globalization-support"></a>Обновленная поддержка глобализации

Поддержка глобализации доступна для Blazor WebAssembly на основе ICU (международных компонентов для Юникода). Для получения дополнительной информации см. <xref:blazor/globalization-localization>.

## <a name="grpc"></a>gRPC

В [gRPC](https://grpc.io/) внесено много усовершенствований производительности. Дополнительные сведения см. в статье [Улучшения производительности gRPC в .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).

Дополнительные сведения о gRPC см. в статье <xref:grpc/index>.

## SignalR

### <a name="no-locsignalr-hub-filters"></a>Фильтры концентраторов SignalR

Фильтры концентраторов SignalR, называемые конвейерами концентраторов в ASP.NET SignalR, — это возможность, которая позволяет выполнять код до и после вызова методов концентратора. Выполнение кода до и после вызова методов концентратора аналогично тому, как ПО промежуточного слоя может выполнять код до и после HTTP-запроса. Распространенные варианты использования включают ведение журнала, обработку ошибок и проверку аргументов.

Подробные сведения см. в статье [Использование фильтров концентраторов в ASP.NET Core SignalR](xref:signalr/hub-filters).

### <a name="no-locsignalr-parallel-hub-invocations"></a>Параллельные вызовы концентраторов SignalR

ASP.NET Core SignalR теперь может обрабатывать параллельные вызовы концентраторов. Поведение по умолчанию можно изменить, чтобы разрешить клиентам одновременно вызывать несколько методов концентратора:

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a>Добавлена поддержка MessagePack в клиент Java для SignalR

Новый пакет [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack) добавляет поддержку MessagePack в клиент Java для SignalR. Чтобы использовать протокол концентратора MessagePack, добавьте `.withHubProtocol(new MessagePackHubProtocol())` в построитель подключений:

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* Перезагружаемые конечные точки с помощью конфигурации. Kestrel может обнаружить изменения в конфигурации, переданные [KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A), отменить привязку к имеющимся конечным точкам и привязать к новым конечным точкам, не требуя перезапуска приложения, если для параметра `reloadOnChange` задано значение `true`. По умолчанию при использовании <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> или <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> Kestrel привязывается к подразделу конфигурации ["Kestrel"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) с включенным параметром `reloadOnChange`. Приложения должны передавать `reloadOnChange: true` при вызове `KestrelServerOptions.Configure` вручную для получения перезагружаемых конечных точек.
* Улучшения заголовков ответов HTTP/2. Дополнительные сведения см. в следующем разделе [Улучшения в области производительности](#performance-improvements).
* Поддержка дополнительных типов конечных точек в транспортировке сокетов. В дополнение к новому API, представленному в <xref:System.Net.Sockets?displayProperty=nameWithType>, транспортировка сокетов по умолчанию в [Kestrel](xref:fundamentals/servers/kestrel) позволяет привязаться как к имеющимся дескрипторам файлов, так и к сокетам домена Unix. Поддержка привязки к имеющимся дескрипторам файлов позволяет использовать имеющуюся интеграцию `Systemd`, не требуя транспортировки `libuv`.
* Декодирование настраиваемого заголовка в Kestrel. Приложения могут указать, какие <xref:System.Text.Encoding> использовать для интерпретации входящих заголовков на основе имени заголовка, а не по умолчанию в UTF-8. Задайте в качестве значения заголовка <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> свойство `Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector`, чтобы указать используемую кодировку:
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a>Параметры для конечной точки Kestrel с помощью конфигурации

Добавлена поддержка настройки параметров для каждой конечной точки Kestrel с помощью [конфигурации](xref:fundamentals/configuration/index). Конфигурации для каждой конечной точки включают:

* используемые протоколы HTTP;
* используемые протоколы TLS;
* выбранный сертификат;
* режим сертификата клиента.

Конфигурация позволяет определить, какой сертификат выбирается на основе указанного имени сервера. Имя сервера входит в состав расширения указания имени сервера для протокола TLS, как указано клиентом. Конфигурация Kestrel также поддерживает префикс подстановочного знака в имени узла.

В следующем примере показано, как задать для каждой конечной точки использование файла конфигурации:

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

Указание имени сервера — это расширение TLS для включения виртуального домена в состав согласования SSL. Это фактически означает, что виртуальное доменное имя или имя узла можно использовать для определения конечной точки сети.

## <a name="performance-improvements"></a>Усовершенствования в области производительности

### <a name="http2"></a>HTTP/2

* Значительное сокращение распределений в пути к коду HTTP/2.
* Поддержка [динамического сжатия HPack](https://tools.ietf.org/html/rfc7541) заголовков ответов HTTP/2 в [Kestrel](xref:fundamentals/servers/kestrel). Дополнительные сведения см. в разделе [Размер таблицы заголовка](xref:fundamentals/servers/kestrel/options#header-table-size) и записи блога [HPACK: возможность Silent Killer в HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).
* Отправка кадров проверки связи HTTP/2. HTTP/2 имеет механизм отправки кадров проверки связи, чтобы неактивное соединение по-прежнему работало. Обеспечение работоспособного соединения особенно полезно при работе с долгосрочными потоками, которые часто находятся в неактивном состоянии, но только периодически видят действия, например потоки gRPC. Приложения могут отправить периодические кадры проверки связи в [Kestrel](xref:fundamentals/servers/kestrel), установив ограничения на <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions>:

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a>Контейнеры

До выпуска .NET 5.0 создание и публикация документа *Dockerfile* для приложения ASP.NET Core, необходимого для получения всего пакета SDK для .NET Core и образа ASP.NET Core. В этом выпуске количество извлекаемых байтов образов пакета SDK сокращено, а байты, полученные для образа ASP.NET Core, как правило, устраняются. Дополнительные сведения об этом см. в [комментарии к проблеме на GitHub](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).

## <a name="authentication-and-authorization"></a>Аутентификация и авторизация

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a>Проверка подлинности Azure Active Directory с помощью Microsoft.Identity.Web

Шаблоны проектов ASP.NET Core теперь интегрируются с <xref:Microsoft.Identity.Web?displayProperty=fullName> для управления проверкой подлинности с помощью [Azure Activity Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD). [Пакет Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) предоставляет:

* Улучшенный интерфейс проверки подлинности с помощью Azure AD.
* Более простой способ доступа к ресурсам Azure от имени пользователей, включая [Microsoft Graph](/graph/overview). См. [пример Microsoft.Identity.Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), который начинается с базового входа и продолжается через мультитенантность с использованием API-интерфейсов Azure, Microsoft Graph и защиту собственных API-интерфейсов. Пакет `Microsoft.Identity.Web` доступен вместе с .NET 5.

### <a name="allow-anonymous-access-to-an-endpoint"></a>Разрешение анонимного доступа к конечной точке

Метод расширения `AllowAnonymous` предоставляет анонимный доступ к конечной точке:

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a>Пользовательская обработка сбоев авторизации

Пользовательская обработка сбоев авторизации стала еще проще благодаря новому интерфейсу [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs), который вызывается [ПО промежуточного слоя](xref:fundamentals/middleware/index) [авторизации](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A). Реализация по умолчанию остается неизменной, но пользовательский обработчик можно зарегистрировать во внедрении зависимостей, что позволяет использовать пользовательские HTTP-ответы в зависимости от причины сбоя авторизации. См. [этот пример](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs), в котором демонстрируется использование `IAuthorizationMiddlewareResultHandler`.

### <a name="authorization-when-using-endpoint-routing"></a>Авторизация при использовании маршрутизации конечных точек

Авторизация при использовании маршрутизации конечных точек теперь получает `HttpContext`, а не экземпляр конечной точки. Это позволяет ПО промежуточного слоя авторизации получить доступ к `RouteData` и другим свойствам `HttpContext`, которые были недоступны через класс <xref:Microsoft.AspNetCore.Http.Endpoint>. Конечную точку можно получить из контекста, используя [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a>Управление доступом на основе ролей с помощью проверки подлинности Kerberos и протокола LDAP в Linux

См. раздел [Проверка подлинности Kerberos и управление доступом на основе ролей (RBAC).](xref:security/authentication/windowsauth#rbac)

## <a name="api-improvements"></a>Усовершенствования API

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a>Методы расширения JSON для HttpRequest и HttpResponse

Данные JSON можно считывать и записывать из `HttpRequest` и `HttpResponse` с помощью новых методов расширения <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> и `WriteAsJsonAsync`. Эти методы расширения используют сериализатор [System.Text.Json](xref:System.Text.Json) для обработки данных JSON. Новый метод расширения `HasJsonContentType` также может проверять, имеет ли запрос тип содержимого JSON.

Методы расширения JSON можно объединять с [маршрутизацией конечных точек](xref:fundamentals/routing) для создания API-интерфейсов JSON в стиле программирования, который мы называем ***маршрут к коду** _. Это новый вариант для разработчиков, которые хотят создавать базовые API-интерфейсы JSON простым способом. Например, веб-приложение, имеющее только несколько конечных точек, может использовать маршрут к коду, а не все функции MVC ASP.NET Core:

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

Дополнительные сведения о новых методах расширения JSON и маршруте к коду см. в [этом видео о .NET](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).

### <a name="systemdiagnosticsactivity"></a>System.Diagnostics.Activity

<xref:System.Diagnostics.Activity?displayProperty=fullName> теперь по умолчанию имеет формат W3C. Благодаря этому распределенная трассировка в ASP.NET Core поддерживает взаимодействие с другими платформами по умолчанию.

### <a name="frombodyattribute"></a>FromBodyAttribute

<xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> теперь поддерживает настройку параметра, который позволяет считать такие параметры или свойства необязательными:

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                          MyModel model)
{
    ...
}
```

## <a name="miscellaneous-improvements"></a>Прочие улучшения

Мы начали применять [заметки, допускающие значение NULL](/dotnet/csharp/nullable-references#attributes-describe-apis), к сборкам ASP.NET Core. Мы планируем добавить заметки к большей части контактной зоны общедоступного API на платформе .NET 5. <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a>Управление активацией класса Startup

Добавлена дополнительная перегрузка <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A>, которая разрешает приложению предоставлять фабричный метод для управления активацией класса `Startup`. Управление активацией класса `Startup` полезно для передачи дополнительных параметров в `Startup`, которые инициализируются вместе с узлом:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a>Автоматическое обновление с помощью dotnet watch

В .NET 5 при запуске [dotnet watch](xref:tutorials/dotnet-watch) в проекте ASP.NET Core запускается браузер по умолчанию и выполняется автоматическое обновление браузера по мере внесения изменений в код. Это означает, что вы можете:

Открыть проект ASP.NET Core в текстовом редакторе.
* Выполните `dotnet watch`.
* Сосредоточиться на изменениях кода, пока инструментарий обрабатывает перестроение, перезапуск и перезагрузку приложения.

### <a name="console-logger-formatter"></a>Форматировщик средства ведения журнала консоли

В библиотеке `Microsoft.Extensions.Logging` для поставщика журнала консоли внесено несколько усовершенствований. Теперь разработчики могут реализовать пользовательский `ConsoleFormatter`, чтобы полностью контролировать форматирование и цветовое выделение выходных данных консоли. API-интерфейсы форматировщика позволяют выполнять обширное форматирование путем реализации подмножества escape-последовательностей VT-100. VT-100 поддерживается в большинстве современных терминалов. Средство ведения журнала консоли может анализировать escape-последовательности неподдерживаемых терминалов, позволяя разработчикам создавать единый форматировщик для всех терминалов.

### <a name="json-console-logger"></a>Средство ведения журнала консоли JSON

Помимо поддержки пользовательских форматировщиков, мы также добавили встроенный форматировщик JSON, который выводит структурированные журналы JSON в консоли. В следующем примере кода показано, как переключиться со средства ведения журнала по умолчанию на JSON:

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

Сообщения журнала, выводимые на консоль, имеют формат JSON:

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
