---
title: Новые возможности ASP.NET Core 2.1
author: isaac2004
description: Дополнительные сведения о новых возможностях ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-2.1
ms.openlocfilehash: 199ca62f71df5c0ee5ca2f78caf53ad8d6be1179
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854565"
---
# <a name="whats-new-in-aspnet-core-21"></a>Новые возможности ASP.NET Core 2.1

В этой статье описываются наиболее важные изменения в ASP.NET Core 2.1 со ссылками на соответствующую документацию.

## SignalR

SignalR переписан для ASP.NET Core 2.1. В ASP.NET Core SignalR внесен ряд усовершенствований:

* Упрощенная модель горизонтального масштабирования.
* Новый клиент JavaScript без зависимости jQuery.
* Новый компактный двоичный протокол на базе MessagePack.
* Поддержка пользовательских протоколов.
* Новая модель потокового ответа.
* Поддержка клиентов на базе только протокола WebSocket.

Дополнительные сведения см. в статье об [ASP.NET SignalR](xref:signalr/introduction).

## <a name="no-locrazor-class-libraries"></a>Библиотеки класса Razor

В ASP.NET Core 2.1 проще создать и включить пользовательский интерфейс на основе Razor в библиотеку, а затем использовать его сразу в нескольких проектах. Новый пакет SDK для Razor позволяет создавать файлы Razor в проекте библиотеки классов, который можно поместить в пакет NuGet. Представления и страницы в библиотеках обнаруживаются автоматически и могут переопределяться приложением. Благодаря интеграции компиляции Razor в сборку:

* Время запуска приложения значительно сократилось.
* Быстрые обновления для представлений и страниц Razor во время выполнения по-прежнему доступны в рамках рабочего процесса последовательной разработки.

Дополнительные сведения см. в разделе [Создание многоразового пользовательского интерфейса с помощью проекта библиотеки классов Razor](xref:razor-pages/ui-class).

## <a name="no-locidentity-ui-library--scaffolding"></a>Библиотека пользовательского интерфейса Identity и формирование шаблонов

ASP.NET Core 2.1 предоставляет [ASP.NET Core Identity](xref:security/authentication/identity) как [библиотеку классов Razor](xref:razor-pages/ui-class). Приложения, включающие Identity, могут применить новый шаблон Identity для выборочного добавления исходного кода из библиотеки классов Identity (RCL) для Razor. Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение. Например, вы можете указать шаблону создать код, используемый при регистрации. Созданный код имеет приоритет над тем же кодом в RCL для Identity.

Приложения, которые **не** включают проверку подлинности, могут применить шаблон Identity, чтобы добавить пакет RCL для Identity. Вы можете выбрать, какой код Identity будет создан.

Дополнительные сведения см. в разделе [Шаблоны Identity в проектах ASP.NET Core](xref:security/authentication/scaffold-identity).

## <a name="https"></a>HTTPS

Учитывая повышенное внимание к безопасности и конфиденциальности, важно использовать HTTPS для веб-приложений. В Интернете применение HTTPS все чаще становится обязательным. Сайты, не использующие HTTPS, считаются небезопасными. Браузеры (Chromium, Mozilla) требуют использования веб-компонентов в контексте безопасности. [Общий регламент по защите данных](xref:security/gdpr) предписывает использовать HTTPS для защиты конфиденциальности пользователей. В то время как использование HTTPS в рабочей среде становится обязательным, применение HTTPS при разработке помогает предотвратить проблемы с развертыванием (например, небезопасные ссылки). ASP.NET Core 2.1 включает ряд усовершенствований, упрощающих использование HTTPS в среде разработки и настройку HTTPS в рабочей среде. Дополнительные сведения см. в разделе [Обязательное использование HTTPS](xref:security/enforcing-ssl).

### <a name="on-by-default"></a>По умолчанию включено

Чтобы вам было проще разрабатывать безопасные веб-сайты, протокол HTTPS теперь включен по умолчанию. Начиная с версии 2.1 Kestrel ожидает передачи данных по `https://localhost:5001`, если присутствует локальный сертификат разработки. Сертификат разработки создается, когда:

* Вы впервые запускаете пакет SDK для .NET Core.
* Вы создаете его вручную с помощью нового средства `dev-certs`.

Запустите `dotnet dev-certs https --trust`, чтобы установить доверие для сертификата.

### <a name="https-redirection-and-enforcement"></a>Перенаправление и принудительное применение HTTPS

Веб-приложения обычно используют оба протокола — HTTP и HTTPS, но перенаправляют весь трафик HTTP на HTTPS. В версии 2.1 появилось специальное ПО промежуточного слоя, которое интеллектуально перенаправляет трафик на HTTPS, учитывая конфигурацию или порты связанного сервера.

Использование протокола HTTPS также можно гарантировать с помощью [протокола HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts). HSTS указывает браузерам всегда переходить на сайт через HTTPS. В ASP.NET Core 2.1 добавлено ПО промежуточного слоя HSTS, которое поддерживает параметры для максимального возраста, дочерних доменов и списка предварительной загрузки HSTS.

### <a name="configuration-for-production"></a>Конфигурация для рабочей среды

В рабочей среде необходимо явно настроить HTTPS. В версии 2.1 добавлена схема конфигурации по умолчанию для настройки HTTPS для Kestrel. Можно настроить приложения, чтобы они использовали:

* Несколько конечных точек, включая URL-адреса. Дополнительные сведения см. в руководстве по [реализации веб-сервера Kestrel. конфигурация конечной точки](xref:fundamentals/servers/kestrel#endpoint-configuration).
* Сертификат для использования HTTPS из файла на диске или из хранилища сертификатов.

## <a name="gdpr"></a>Общий регламент по защите данных

ASP.NET Core предоставляет API-интерфейсы и шаблоны, которые помогают соответствовать требованиям [Общего регламента по защите данных в ЕС](https://www.eugdpr.org/). Дополнительные сведения см. в разделе [Поддержка общего регламента по защите данных в ASP.NET Core](xref:security/gdpr). В [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) показано, как использовать и тестировать большинство точек расширения для общего регламента по защите данных и API-интерфейсов, добавленных в шаблоны ASP.NET Core 2.1.

## <a name="integration-tests"></a>Интеграционные тесты

Добавлен новый пакет, который оптимизирует создание и выполнение тестов. Пакет [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) выполняет следующие задачи:

* Копирует файл зависимостей ( *\*.deps*) из протестированного приложения в папку *bin* тестового проекта.
* Задает корневую папку содержимого в корневой папке проекта тестируемого приложения, чтобы можно было найти статические файлы и страницы или представления при выполнении тестов.
* Предоставляет класс [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) для оптимизации начальной загрузки тестируемого приложения на [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).

В следующем тесте используется [xUnit](https://xunit.github.io/) для проверки того, что страница индексов загружается с кодом состояния успешного выполнения и правильным заголовком Content-Type:

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

Дополнительные сведения см. в разделе [Интеграционные тесты](xref:test/integration-tests).

## <a name="apicontroller-actionresultt"></a>[ApiController], ActionResult\<T>

В ASP.NET Core 2.1 добавлены новые соглашения программирования, с которыми проще создавать чистые и выразительные веб-API. `ActionResult<T>` — это новый тип, который разрешает приложениям возвращать либо тип ответа, либо любой другой результат действия (аналогично IActionResult), при этом по-прежнему указывая тип ответа. Также был добавлен атрибут `[ApiController]`, с помощью которого можно принять соглашения и поведения для веб-API.

Дополнительные сведения см. в разделе [Сборка веб-API с использованием ASP.NET Core](xref:web-api/index).

## <a name="ihttpclientfactory"></a>IHttpClientFactory

ASP.NET Core 2.1 содержит новую службу `IHttpClientFactory`, которая упрощает настройку и использование экземпляров `HttpClient` в приложениях. В `HttpClient` уже существует концепция делегирования обработчиков, которые можно связать друг с другом для исходящих HTTP-запросов. Фабрика:

* Упрощает регистрацию экземпляров `HttpClient` каждого именованного клиента.
* Реализует обработчик Polly, который позволяет использовать политики Polly для повтора, размыкателя цепи и т. д.

Дополнительные сведения см. в разделе [Инициирование HTTP-запросов](xref:fundamentals/http-requests).

## <a name="kestrel-libuv-transport-configuration"></a>Конфигурация транспорта Kestrel Libuv

После выпуска ASP.NET Core 2.1 транспорт Kestrel по умолчанию основан не на Libuv, а на управляемых сокетах. Дополнительные сведения см. в руководстве по [реализации веб-сервера Kestrel. Конфигурация транспорта Libuv](xref:fundamentals/servers/kestrel#libuv-transport-configuration).

## <a name="generic-host-builder"></a>Построитель универсальных узлов

Добавлен построитель универсальных узлов (`HostBuilder`). Построитель можно использовать для приложений, которые не обрабатывают HTTP-запросы (обмен сообщениями, фоновые задачи и т. д.).

Дополнительные сведения см. в разделе [Универсальный узел .NET](xref:fundamentals/host/generic-host).

## <a name="updated-spa-templates"></a>Обновленные шаблоны SPA

Обновлены шаблоны одностраничных приложений для Angular, React и React с Redux. Теперь можно использовать стандартные структуры проектов и создавать системы для каждой платформы.

Шаблон Angular основан на Angular CLI, а шаблоны React основаны на create-react-app.

Дополнительные сведения можно найти в разделе

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="no-locrazor-pages-search-for-no-locrazor-assets"></a>Поиск в Razor Pages активов Razor

В версии 2.1 Razor Pages ищет ресурсы Razor (например, макеты и частично выполненные строки) в следующих каталогах в указанном порядке.

1. Текущая папка Pages.
1. */Pages/Shared/*
1. */Views/Shared/*

## <a name="no-locrazor-pages-in-an-area"></a>Razor Pages в области

Razor Pages теперь поддерживает [области](xref:mvc/controllers/areas). Чтобы увидеть пример областей, создайте новое веб-приложение Razor Pages с отдельными учетными записями пользователей. Веб-приложение Razor Pages с отдельными учетными записями пользователей включает */Areas/Identity/Pages*.

## <a name="mvc-compatibility-version"></a>Совместимая версия MVC

Метод <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> позволяет приложению принимать или отклонять потенциально критические изменения в поведении, появившиеся в ASP.NET Core MVC 2.1 или более поздних версий.

Для получения дополнительной информации см. <xref:mvc/compatibility-version>.

## <a name="migrate-from-20-to-21"></a>Миграция с 2.0 на 2.1

См. раздел [Миграция с ASP.NET Core 2.0 на 2.1](xref:migration/20_21).

## <a name="additional-information"></a>Дополнительные сведения

Полный список изменений см. в статье [Заметки о выпуске ASP.NET Core 2.1](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).
