---
title: Проверки работоспособности в ASP.NET Core
author: rick-anderson
description: Узнайте, как настроить проверки работоспособности для инфраструктуры ASP.NET Core, например приложений и баз данных.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 06/22/2020
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
uid: host-and-deploy/health-checks
ms.openlocfilehash: 272f1f098ca90434f26d6c057859a00b5519602e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588910"
---
# <a name="health-checks-in-aspnet-core"></a>Проверки работоспособности в ASP.NET Core

Автор: [Гленн Кондрон](https://github.com/glennc) (Glenn Condron)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core предоставляет ПО промежуточного слоя и библиотеки для создания отчетов о работоспособности компонентов инфраструктуры приложения.

Проверки работоспособности предоставляются приложением как конечные точки HTTP. Конечные точки проверки работоспособности можно настроить для различных сценариев в реальном времени мониторинга:

* Проверки работоспособности можно использовать с оркестраторами контейнеров и подсистемами балансировки нагрузки, чтобы проверять состояние приложения. Например, оркестратор контейнеров может реагировать на неуспешную проверку работоспособности, остановив последовательное развертывание или перезапустив контейнер. Балансировщик нагрузки может реагировать на неработоспособное приложение путем перенаправления трафика от неисправного экземпляра к работающему экземпляру.
* Использование памяти, диска и других ресурсов физического сервера можно отслеживать с точки зрения работоспособности.
* Проверки работоспособности позволяют проверять зависимости приложения, такие как базы данных и конечные точки внешних служб, чтобы убедиться в доступности и нормальной работе.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([как скачивать](xref:index#how-to-download-a-sample))

Пример приложения включает примеры сценариев, описанных в этом разделе. Чтобы запустить пример приложения для заданного сценария, используйте команду [dotnet run](/dotnet/core/tools/dotnet-run) из папки проекта в командной строке. См. файл *README.md* приложения и описания сценариев в этом разделе, чтобы получить сведения о том, как использовать пример приложения.

## <a name="prerequisites"></a>Предварительные требования

Проверки работоспособности обычно используются в оркестраторе контейнеров или внешней службе мониторинга для проверки состояния приложения. Перед добавлением проверки работоспособности приложения выберите используемую систему мониторинга. Система мониторинга определяет, какие виды проверки работоспособности создавать и как настроить их конечные точки.

Для приложений ASP.NET Core ссылка на пакет [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) указывается неявно. Чтобы выполнить проверку работоспособности с помощью Entity Framework Core, добавьте ссылку на пакет [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore).

В примере приложения приведен код запуска для демонстрации проверки работоспособности для нескольких сценариев. Сценарий [проверки базы данных](#database-probe) проверяет работоспособность подключения базы данных с помощью [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Сценарий [проверки DbContext](#entity-framework-core-dbcontext-probe) проверяет базу данных с помощью EF Core `DbContext`. Чтобы изучить сценарии для баз данных, пример приложения выполняет следующие действия.

* Создает базу данных и указывает ее строку подключения в файле приложения *appsettings.json* .
* Содержит следующие ссылки на пакеты в своем файле проекта.
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) не поддерживается корпорацией Майкрософт.

В другом сценарии проверки работоспособности демонстрируется способ фильтрации проверок работоспособности по порту управления. Пример приложения требует создать файл *Properties/launchSettings.json*, который включает в себя URL-адрес управления и порт управления. Дополнительные сведения см. в разделе [Фильтр по портам](#filter-by-port).

## <a name="basic-health-probe"></a>Базовая проверка работоспособности

Для многих приложений достаточно конфигурации базовой проверки работоспособности, которая сообщает о доступности приложения для обработки запросов (*жизнеспособности*).

Базовая конфигурация регистрирует службы проверки работоспособности и вызывает ПО промежуточного слоя для получения ответа о работоспособности в конечной точке по URL-адресу. По умолчанию отдельные проверки работоспособности не регистрируются для тестирования какой-либо конкретной зависимости или подсистемы. Приложение считается исправным, если оно способно отвечать на запросы по URL-адресу конечной точки работоспособности. Модуль записи ответа по умолчанию передает состояние (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) в виде обычного текста в ответе клиенту. Это может быть состояние [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) или [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).

Зарегистрируйте службы проверки работоспособности при помощи <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> в `Startup.ConfigureServices`. Создайте конечную точку проверки работоспособности, вызвав `MapHealthChecks` в `Startup.Configure`.

В примере приложения конечная точка проверки работоспособности создается в `/health` (*BasicStartup.cs*):

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

Чтобы запустить сценарий базовой конфигурации с помощью примера приложения, выполните следующую команду из папки проекта в командной строке:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Пример для Docker

[Docker](xref:host-and-deploy/docker/index) предлагает встроенную директиву `HEALTHCHECK`, которую можно вызвать для проверки состояния приложения, использующего базовую конфигурацию проверки работоспособности:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Создание проверки работоспособности

Проверки работоспособности создаются путем реализации интерфейса <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>. Метод <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> возвращает <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, указывая состояние работоспособности как `Healthy`, `Degraded` или `Unhealthy`. Результат записывается в ответ в виде обычного текста с кодом состояния, который можно настроить (конфигурация описана в разделе [Варианты проверки работоспособности](#health-check-options)). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> также может возвращать необязательные пары "ключ-значение".

Следующий класс `ExampleHealthCheck` демонстрирует структуру процесса проверки работоспособности. Логика проверок работоспособности помещается в метод `CheckHealthAsync`. В следующем примере для `true` задается фиктивная переменная `healthCheckResultHealthy`. Если для `healthCheckResultHealthy` задано значение `false`, возвращается состояние [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*).

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a>Зарегистрируйте службы проверки работоспособности

Тип `ExampleHealthCheck` будет добавлен к службам проверки работоспособности с помощью <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> в `Startup.ConfigureServices`:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

Перегрузка <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, показанная в следующем примере, устанавливает состояние сбоя (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) для отчета в ситуации, когда проверка работоспособности сообщает о сбое. Если состояние сбоя имеет значение `null` (по умолчанию), сообщается [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus). Эта перегрузка — полезный сценарий для авторов библиотек, где состояние сбоя от библиотеки особо учитывается приложением при сбое проверки работоспособности, если реализация проверки работоспособности учитывает этот параметр.

*Теги* можно использовать для фильтрации проверок работоспособности (описаны далее в разделе [Фильтрация проверок работоспособности](#filter-health-checks)).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> также может выполнять лямбда-функции. В следующем примере имя проверки работоспособности указано как `Example`; проверка всегда возвращает работоспособное состояние:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

Вызовите <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*>, чтобы передать аргументы реализации проверки работоспособности. В следующем примере `TestHealthCheckWithArgs` принимает целое число и строку для использования при вызове <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>:

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

`TestHealthCheckWithArgs` регистрируется путем вызова `AddTypeActivatedCheck` с целым числом и строкой, передаваемой в реализацию:

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a>Использование маршрутизации при проверках работоспособности

В `Startup.Configure` вызовите `MapHealthChecks` для построителя конечной точки с URL-адресом конечной точки или относительным путем:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a>RequireHost

Вызовите `RequireHost`, чтобы указать один или несколько разрешенных узлов для конечной точки проверки работоспособности. Узлы должны быть указаны в Юникоде, а не Punycode, и могут включать порт. Если коллекция не указана, допускается любой узел.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

Дополнительные сведения см. в разделе [Фильтр по портам](#filter-by-port).

### <a name="require-authorization"></a>RequireAuthorization

Вызовите `RequireAuthorization` для запуска ПО промежуточного слоя для авторизации в конечной точке запроса проверки работоспособности. Перегрузка `RequireAuthorization` принимает одну или несколько политик авторизации. Если политика не указана, используется политика авторизации по умолчанию.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a>Включение запросов о происхождении (CORS)

Хотя выполнение проверок работоспособности вручную из браузера не является распространенным сценарием использования, ПО промежуточного слоя CORS можно включить, вызвав `RequireCors` в конечных точках проверки работоспособности. Перегрузка `RequireCors` принимает делегат построителя политики CORS (`CorsPolicyBuilder`) или имя политики. Если политика не указана, используется политика CORS по умолчанию. Для получения дополнительной информации см. <xref:security/cors>.

## <a name="health-check-options"></a>Варианты проверки работоспособности

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> предоставляет возможность настройки поведения для проверки работоспособности:

* [Фильтрация проверок работоспособности](#filter-health-checks)
* [Настройка кода состояния HTTP](#customize-the-http-status-code)
* [Подавление заголовков кэша](#suppress-cache-headers)
* [Настройка выходных данных](#customize-output)

### <a name="filter-health-checks"></a>Фильтрация проверок работоспособности

По умолчанию ПО промежуточного слоя для проверки работоспособности выполняет все зарегистрированные проверки работоспособности. Чтобы выполнить подмножество проверок работоспособности, укажите функцию, которая возвращает логическое значение через параметр <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>. В следующем примере проверка работоспособности `Bar` отфильтровывается по тегу (`bar_tag`) в условном операторе функции, где `true` возвращается только в том случае, если свойство проверки работоспособности <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> соответствует `foo_tag` или `baz_tag`:

В `Startup.ConfigureServices`:

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

В `Startup.Configure``Predicate` определяет, что проверка работоспособности Bar не выполняется. Выполняется только проверка работоспособности Foo и Baz:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a>Настройка кода состояния HTTP

Используйте <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> для настройки сопоставления состояний работоспособности и кодов состояния HTTP. Следующие назначения <xref:Microsoft.AspNetCore.Http.StatusCodes> являются значениями по умолчанию, используемыми ПО промежуточного слоя. Измените значения кодов состояния в соответствии с требованиями.

В `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a>Подавление заголовков кэша

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> определяет, добавляет ли ПО промежуточного слоя HTTP-заголовки в ответ проверки, чтобы предотвратить кэширование ответов. Если значение равно `false` (по умолчанию), ПО промежуточного слоя задает или переопределяет заголовки `Cache-Control`, `Expires` и `Pragma`, чтобы предотвратить кэширование ответов. Если значение равно `true`, ПО промежуточного слоя не изменяет заголовки кэша в ответе.

В `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a>Настройка выходных данных

В `Startup.Configure` задайте для параметра [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) делегат для записи ответа:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

Делегат по умолчанию записывает минимальный ответ в формате открытого текста со строковым значением [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Следующие пользовательские делегаты выводят настраиваемый ответ JSON.

В первом примере из примера приложения показано, как использовать <xref:System.Text.Json?displayProperty=fullName>:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

Во втором примере показано, как использовать [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

В примере приложения закомментируйте [директиву препроцессора](xref:index#preprocessor-directives-in-sample-code) `SYSTEM_TEXT_JSON` в *CustomWriterStartup.cs*, чтобы включить версию `Newtonsoft.Json` `WriteResponse`.

API проверки работоспособности не предоставляет встроенной поддержки сложных форматов возврата JSON, так как формат зависит от выбранной системы мониторинга. При необходимости настройте ответ в предыдущих примерах. Дополнительные сведения о сериализации JSON с `System.Text.Json` см. в статье о [сериализации и десериализации JSON в .NET](/dotnet/standard/serialization/system-text-json-how-to).

## <a name="database-probe"></a>Проверка базы данных

Проверка работоспособности может задать запрос для выполнения в качестве логического теста для указания того, отвечает ли база данных как обычно.

Пример приложения использует [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), библиотеку проверки работоспособности для приложений ASP.NET Core, чтобы проверить работоспособность базы данных SQL Server. `AspNetCore.Diagnostics.HealthChecks` выполняет запрос `SELECT 1` к базе данных, чтобы подтвердить, что подключение к базе данных находится в работоспособном состоянии.

> [!WARNING]
> При проверке подключения к базе данных с помощью запроса выберите запрос, возвращающий результат быстро. Метод запроса связан с риском перегрузки базы данных и снижения ее производительности. В большинстве случаев тестовый запрос не является обязательным. Достаточно успешного подключения к базе данных. Если вам понадобится выполнить запрос, выберите простой запрос SELECT, например `SELECT 1`.

Добавьте ссылку на пакет [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Укажите допустимую строку подключения к базе данных в файле примера приложения *appsettings.json* . Приложение использует базу данных SQL Server с именем `HealthCheckSample`:

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

Зарегистрируйте службы проверки работоспособности при помощи <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> в `Startup.ConfigureServices`. Пример приложения вызывает метод `AddSqlServer` со строкой подключения базы данных (*DbHealthStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Конечная точка проверки работоспособности создается путем вызова `MapHealthChecks` в `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Чтобы запустить сценарий проверки базы данных с помощью примера приложения, выполните следующую команду из папки проекта в командной строке:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) не поддерживается корпорацией Майкрософт.

## <a name="entity-framework-core-dbcontext-probe"></a>Проверка DbContext в Entity Framework Core

Эта проверка `DbContext` подтверждает, что приложение может взаимодействовать с базой данных, настроенной для EF Core `DbContext`. Проверка `DbContext` поддерживается в приложениях, которые:

* используют [Entity Framework (EF) Core](/ef/core/);
* содержат ссылку на пакет [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` регистрирует проверку работоспособности для `DbContext`. `DbContext` передается методу в качестве `TContext`. Доступна перегрузка, позволяющая настроить состояние ошибки, теги и запрос тестирования.

По умолчанию:

* `DbContextHealthCheck` вызывает метод EF Core `CanConnectAsync`. Вы можете указать, какая операция выполняется в том случае, когда проверка работоспособности производится с помощью перегрузок метода `AddDbContextCheck`.
* Имя проверки работоспособности — это имя типа `TContext`.

В примере приложения `AppDbContext` предоставляется для `AddDbContextCheck` и регистрируется в качестве службы в `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Конечная точка проверки работоспособности создается путем вызова `MapHealthChecks` в `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Прежде чем запускать сценарий проверки `DbContext` с помощью примера приложения, убедитесь, что база данных, указанная в строке подключения, не существует в экземпляре SQL Server. Если база данных существует, удалите ее.

Выполните следующую команду в папке проекта в командной строке:

```dotnetcli
dotnet run --scenario dbcontext
```

После запуска приложения проверьте состояние работоспособности, выполнив запрос к конечной точке `/health` в браузере. Базы данных и `AppDbContext` не существуют, поэтому приложение дает следующий ответ:

```
Unhealthy
```

Заставьте пример приложения создать базу данных. Сделайте запрос к `/createdatabase`. Приложение выдает ответ:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Сделайте запрос к конечной точке `/health`. База данных и контекст существуют, поэтому приложение отвечает:

```
Healthy
```

Заставьте пример приложения удалить базу данных. Сделайте запрос к `/deletedatabase`. Приложение выдает ответ:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Сделайте запрос к конечной точке `/health`. Приложение предоставляет ответ о неработоспособности:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Разделение проверок готовности и активности

В некоторых сценариях размещения используются пары проверок работоспособности, отличающие два состояния приложения:

* *Готовность* указывает, что приложение работает нормально, но не готово к приему запросов.
* *Активность* указывает на сбой приложения, и его необходимо перезапустить.

Рассмотрим следующий пример: Приложение должно загрузить большой файл конфигурации до того, как оно будет готово к обработке запросов. Нам не требуется перезапуск приложения в случае сбоя начальной загрузки, так как приложение может повторить попытку скачивания файла несколько раз. Для описания активности процесса используется *проба активности*, дополнительные проверки не выполняются. Кроме того, мы хотим предотвратить отправку запросов в приложение до успешной загрузки файла конфигурации. Мы используем *пробу активности*, чтобы указать состояние "не готово" до тех пор, пока загрузка не будет завершена и приложение не сможет принимать запросы.

Пример приложения содержит проверку работоспособности, которая сообщает о завершении длительной задачи запуска [размещенной службы](xref:fundamentals/host/hosted-services). `StartupHostedServiceHealthCheck` предоставляет свойство, `StartupTaskCompleted`, которое размещенная служба может задать как `true` после завершения длительной задачи (*StartupHostedServiceHealthCheck.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Длительная фоновая задача запущена [размещенной службой](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*). По завершении задачи `StartupHostedServiceHealthCheck.StartupTaskCompleted` получает значение `true`:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Проверка работоспособности регистрируется через <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> в `Startup.ConfigureServices` вместе с размещенной службой. Поскольку размещенной службе необходимо задать свойство проверки работоспособности, проверка работоспособности также регистрируется в контейнере службы (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Конечная точка проверки работоспособности создается путем вызова `MapHealthChecks` в `Startup.Configure`. В примере приложения конечные точки проверки работоспособности создаются в следующих расположениях:

* В `/health/ready` для проверки готовности. Проверка готовности фильтрует проверки работоспособности по тегу `ready`.
* В `/health/live` для проверки жизнеспособности. Проверка жизнеспособности отфильтровывает `StartupHostedServiceHealthCheck`, возвращая `false` в [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (дополнительные сведения см. в разделе [Фильтрация проверок работоспособности](#filter-health-checks)).

В следующем примере кода происходит следующее:

* Проверка готовности использует все зарегистрированные проверки с тегом ready.
* `Predicate` исключает все проверки и возвращает 200 — OK.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

Чтобы запустить сценарий конфигурации проверок готовности и жизнеспособности с помощью примера приложения, выполните следующую команду из папки проекта в командной строке:

```dotnetcli
dotnet run --scenario liveness
```

В браузере посетите `/health/ready` несколько раз, пока не пройдет 15 секунд. Проверка работоспособности будет передавать состояние *Unhealthy* первые 15 секунд. По истечении 15 секунд конечная точка передает состояние *Healthy*, что означает завершение длительной задачи размещенной службой.

В этом примере также создается издатель проверки работоспособности (реализация <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>), который выполнит первую проверку готовности с задержкой в две секунды. Дополнительные сведения см. в разделе [об издателе проверки работоспособности](#health-check-publisher).

### <a name="kubernetes-example"></a>Пример для Kubernetes

Использование отдельных проверок готовности и жизнеспособности полезно в таких средах, как [Kubernetes](https://kubernetes.io/). В Kubernetes приложениям может потребоваться долго выполнять задачи во время запуска, прежде чем начать принимать запросы; например, проверить доступность основной базы данных. Использование отдельных проверок позволяет оркестратору различать, когда приложение работает, но еще не готово, а когда приложение не удалось запустить. Дополнительные сведения о проверках готовности и жизнеспособности в Kubernetes см. в разделе [Настройка проверок готовности и жизнеспособности](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) в документации по Kubernetes.

В следующем примере показана конфигурация проверки готовности для Kubernetes:

```yml
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Проба на основе метрики с настраиваемым модулем записи ответов

Этот пример демонстрирует проверку работоспособности памяти с настраиваемым модулем записи ответов.

Если приложение использует больше заданного порогового значения памяти (1 ГБ в примере приложения), `MemoryHealthCheck` сообщает о состоянии пониженной функциональности. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> включает сведения от сборщика мусора для приложения (*MemoryHealthCheck.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Зарегистрируйте службы проверки работоспособности при помощи <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> в `Startup.ConfigureServices`. Вместо того чтобы включать проверку работоспособности, передав ее в <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` регистрируется в качестве службы. Все зарегистрированные службы <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> доступны в службах проверки работоспособности и ПО промежуточного слоя. Мы рекомендуем регистрировать службы проверки работоспособности в качестве одноэлементных служб.

В *CustomWriterStartup.CS* примера приложения выполняется следующее:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Конечная точка проверки работоспособности создается путем вызова `MapHealthChecks` в `Startup.Configure`. Делегат `WriteResponse` предоставляется свойству <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> для вывода пользовательского ответа JSON при выполнении проверки работоспособности:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

Делегат `WriteResponse` форматирует `CompositeHealthCheckResult` в объект JSON и выдает выходные данные JSON в качестве ответа проверки работоспособности. Дополнительные сведения см. в разделе [Настройка выходных данных](#customize-output).

Чтобы запустить проверку метрики с настраиваемым модулем записи ответа с помощью примера приложения, выполните следующую команду из папки проекта в командной строке:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) содержит сценарии для проверки работоспособности на основе метрик, включая проверки жизнеспособности для дискового хранилища и максимальных значений.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) не поддерживается корпорацией Майкрософт.

## <a name="filter-by-port"></a>Фильтр по портам

Вызовите `RequireHost` в `MapHealthChecks` с шаблоном URL-адреса, в котором указан порт для ограничения запросов проверки работоспособности указанным портом. Обычно это используется в контейнерной среде с целью предоставления порта для мониторинга служб.

Пример приложения настраивает порт с помощью [поставщика конфигурации переменных среды](xref:fundamentals/configuration/index#environment-variables). Порт устанавливается в файле *launchSettings.json* и передается поставщику конфигурации с помощью переменной среды. Необходимо также настроить на сервере прослушивание запросов через порт управления.

Чтобы использовать пример приложения для демонстрации настройки портов управления, создайте файл *launchSettings.json* в папке *Properties*.

Следующий файл примера приложения *Properties/launchSettings.json* отсутствует в файлах проекта примера приложения и должен быть создан вручную:

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

Зарегистрируйте службы проверки работоспособности при помощи <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> в `Startup.ConfigureServices`. Создайте конечную точку проверки работоспособности, вызвав `MapHealthChecks` в `Startup.Configure`.

В примере приложения вызов `RequireHost` в конечной точке в `Startup.Configure` указывает порт управления из конфигурации:

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

Конечные точки создаются в примере приложения в `Startup.Configure`. В следующем примере кода происходит следующее:

* Проверка готовности использует все зарегистрированные проверки с тегом ready.
* `Predicate` исключает все проверки и возвращает 200 — OK.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> Можно не создавать файл *launchSettings.json* в примере приложения, явно указав порт управления в коде. В *Program.cs*, где создается <xref:Microsoft.Extensions.Hosting.HostBuilder>, добавьте вызов <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> и укажите конечную точку порта управления. В параметре `Configure` файла *ManagementPortStartup.cs* укажите порт управления с помощью `RequireHost`:
>
> *Program.cs*:
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> *ManagementPortStartup.cs*:
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

Чтобы запустить сценарий с портом управления с помощью примера приложения, выполните следующую команду из папки проекта в командной строке:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Распространение библиотеки проверки работоспособности

Чтобы распространять проверки работоспособности в качестве библиотеки, выполните следующие действия.

1. Напишите проверку работоспособности, которая реализует интерфейс <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> как автономный класс. Класс может полагаться на [внедрение зависимостей (DI)](xref:fundamentals/dependency-injection), активацию типов и [именованные параметры](xref:fundamentals/configuration/options) для доступа к данным конфигурации.

   Логика проверки работоспособности `CheckHealthAsync` предусматривает следующее:

   * Использование в методе `data1` и `data2` для выполнения логики проверки работоспособности.
   * Обработку `AccessViolationException`.

   Когда происходит <xref:System.AccessViolationException>, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> возвращается вместе с <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, что позволяет пользователям настраивать состояние сбоя проверки работоспособности.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. Напишите метод расширения с параметрами, который приложение вызывает в своем методе `Startup.Configure`. В следующем примере предполагается следующая сигнатура метода проверки работоспособности:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Эта сигнатура указывает, что `ExampleHealthCheck` требуются дополнительные данные для обработки логики проверки работоспособности. Данные передаются делегату, используемому для создания экземпляра проверки работоспособности, когда проверка работоспособности регистрируется с помощью метода расширения. В следующем примере вызывающая сторона задает:

   * Необязательное имя проверки работоспособности (`name`). Если `null`, используется `example_health_check`.
   * Строковая точка данных для проверки работоспособности (`data1`).
   * Целочисленная точка данных для проверки работоспособности (`data2`). Если `null`, используется `1`.
   * состояние сбоя (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). Значение по умолчанию — `null`. В случае `null` возвращается [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) как состояние сбоя.
   * Теги (`IEnumerable<string>`).

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>Издатель проверки работоспособности

Когда <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> добавляется в контейнер службы, система проверки работоспособности периодически выполняет ваши проверки работоспособности и вызывает `PublishAsync` с полученным результатом. Это полезно в ситуации принудительной передачи данных мониторинга работоспособности, когда ожидается, что каждый процесс периодически вызывает систему мониторинга для определения работоспособности.

Интерфейс <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> содержит один метод:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> позволяет задать следующие параметры:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Исходная задержка, которая срабатывает после запуска приложения и перед выполнением экземпляров <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Задержка применяется при запуске один раз и не распространяется на все последующие итерации. Значение по умолчанию — пять секунд.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Период выполнения <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Значение по умолчанию - 30 секунды.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Если <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> имеет значение `null` (по умолчанию), служба издателя проверки работоспособности выполняет все зарегистрированные проверки работоспособности. Чтобы выполнить ряд проверок работоспособности, укажите функцию, которая отфильтровывает нужный набор. Предикат вычисляется в каждом периоде.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Время ожидания для выполнения проверок работоспособности для всех экземпляров <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Используйте <xref:System.Threading.Timeout.InfiniteTimeSpan>, чтобы выполнить проверки без задержки. Значение по умолчанию - 30 секунды.

В примере приложения `ReadinessPublisher` является реализацией <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Состояние проверки работоспособности записывается для каждой проверки на таком уровне журнала:

* на уровне сведений (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>), если состояние проверки работоспособности — <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>;
* на уровне ошибки (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>), если состояние соответствует <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> или <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

В примере приложения `LivenessProbeStartup` проверка готовности `StartupHostedService` использует задержку запуска в две секунды и выполняет проверку каждые 30 секунд. Чтобы активировать реализацию <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, этот пример регистрирует `ReadinessPublisher` как отдельную службу в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection).

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) содержит издатели для нескольких систем, в том числе [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) не поддерживается корпорацией Майкрософт.

## <a name="restrict-health-checks-with-mapwhen"></a>Ограничение проверок работоспособности с помощью MapWhen

Используйте <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> для условного ветвления конвейера запросов для конечных точек проверки работоспособности.

В следующем примере `MapWhen` выполняет ветвление конвейера запросов для активации ПО промежуточного слоя для проверки работоспособности, если для конечной точки `api/HealthCheck` поступает запрос GET:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

Для получения дополнительной информации см. <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core предоставляет ПО промежуточного слоя и библиотеки для создания отчетов о работоспособности компонентов инфраструктуры приложения.

Проверки работоспособности предоставляются приложением как конечные точки HTTP. Конечные точки проверки работоспособности можно настроить для различных сценариев в реальном времени мониторинга:

* Проверки работоспособности можно использовать с оркестраторами контейнеров и подсистемами балансировки нагрузки, чтобы проверять состояние приложения. Например, оркестратор контейнеров может реагировать на неуспешную проверку работоспособности, остановив последовательное развертывание или перезапустив контейнер. Балансировщик нагрузки может реагировать на неработоспособное приложение путем перенаправления трафика от неисправного экземпляра к работающему экземпляру.
* Использование памяти, диска и других ресурсов физического сервера можно отслеживать с точки зрения работоспособности.
* Проверки работоспособности позволяют проверять зависимости приложения, такие как базы данных и конечные точки внешних служб, чтобы убедиться в доступности и нормальной работе.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([как скачивать](xref:index#how-to-download-a-sample))

Пример приложения включает примеры сценариев, описанных в этом разделе. Чтобы запустить пример приложения для заданного сценария, используйте команду [dotnet run](/dotnet/core/tools/dotnet-run) из папки проекта в командной строке. См. файл *README.md* приложения и описания сценариев в этом разделе, чтобы получить сведения о том, как использовать пример приложения.

## <a name="prerequisites"></a>Предварительные требования

Проверки работоспособности обычно используются в оркестраторе контейнеров или внешней службе мониторинга для проверки состояния приложения. Перед добавлением проверки работоспособности приложения выберите используемую систему мониторинга. Система мониторинга определяет, какие виды проверки работоспособности создавать и как настроить их конечные точки.

Добавьте ссылку на [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) или ссылку на пакет [Microsoft.AspNetCore.Diagnostics.HealthCheck](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks).

В примере приложения приведен код запуска для демонстрации проверки работоспособности для нескольких сценариев. Сценарий [проверки базы данных](#database-probe) проверяет работоспособность подключения базы данных с помощью [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Сценарий [проверки DbContext](#entity-framework-core-dbcontext-probe) проверяет базу данных с помощью EF Core `DbContext`. Чтобы изучить сценарии для баз данных, пример приложения выполняет следующие действия.

* Создает базу данных и указывает ее строку подключения в файле приложения *appsettings.json* .
* Содержит следующие ссылки на пакеты в своем файле проекта.
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) не поддерживается корпорацией Майкрософт.

В другом сценарии проверки работоспособности демонстрируется способ фильтрации проверок работоспособности по порту управления. Пример приложения требует создать файл *Properties/launchSettings.json*, который включает в себя URL-адрес управления и порт управления. Дополнительные сведения см. в разделе [Фильтр по портам](#filter-by-port).

## <a name="basic-health-probe"></a>Базовая проверка работоспособности

Для многих приложений достаточно конфигурации базовой проверки работоспособности, которая сообщает о доступности приложения для обработки запросов (*жизнеспособности*).

Базовая конфигурация регистрирует службы проверки работоспособности и вызывает ПО промежуточного слоя для получения ответа о работоспособности в конечной точке по URL-адресу. По умолчанию отдельные проверки работоспособности не регистрируются для тестирования какой-либо конкретной зависимости или подсистемы. Приложение считается исправным, если оно способно отвечать на запросы по URL-адресу конечной точки работоспособности. Модуль записи ответа по умолчанию передает состояние (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) в виде обычного текста в ответе клиенту. Это может быть состояние [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) или [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).

Зарегистрируйте службы проверки работоспособности при помощи <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> в `Startup.ConfigureServices`. Добавьте конечную точку для ПО промежуточного слоя для проверки работоспособности с помощью <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> в конвейер обработки запросов `Startup.Configure`.

В примере приложения конечная точка проверки работоспособности создается в `/health` (*BasicStartup.cs*):

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

Чтобы запустить сценарий базовой конфигурации с помощью примера приложения, выполните следующую команду из папки проекта в командной строке:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Пример для Docker

[Docker](xref:host-and-deploy/docker/index) предлагает встроенную директиву `HEALTHCHECK`, которую можно вызвать для проверки состояния приложения, использующего базовую конфигурацию проверки работоспособности:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Создание проверки работоспособности

Проверки работоспособности создаются путем реализации интерфейса <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>. Метод <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> возвращает <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, указывая состояние работоспособности как `Healthy`, `Degraded` или `Unhealthy`. Результат записывается в ответ в виде обычного текста с кодом состояния, который можно настроить (конфигурация описана в разделе [Варианты проверки работоспособности](#health-check-options)). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> также может возвращать необязательные пары "ключ-значение".

### <a name="example-health-check"></a>Пример проверки работоспособности

Следующий класс `ExampleHealthCheck` демонстрирует структуру процесса проверки работоспособности. Логика проверок работоспособности помещается в метод `CheckHealthAsync`. В следующем примере для `true` задается фиктивная переменная `healthCheckResultHealthy`. Если для `healthCheckResultHealthy` задано значение `false`, возвращается состояние [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*).

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a>Зарегистрируйте службы проверки работоспособности

Тип `ExampleHealthCheck` добавляется к службам проверки работоспособности в `Startup.ConfigureServices` с помощью <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

Перегрузка <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, показанная в следующем примере, устанавливает состояние сбоя (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) для отчета в ситуации, когда проверка работоспособности сообщает о сбое. Если состояние сбоя имеет значение `null` (по умолчанию), сообщается [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus). Эта перегрузка — полезный сценарий для авторов библиотек, где состояние сбоя от библиотеки особо учитывается приложением при сбое проверки работоспособности, если реализация проверки работоспособности учитывает этот параметр.

*Теги* можно использовать для фильтрации проверок работоспособности (описаны далее в разделе [Фильтрация проверок работоспособности](#filter-health-checks)).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> также может выполнять лямбда-функции. В следующем примере `Startup.ConfigureServices` имя проверки работоспособности указано как `Example`, а проверка всегда возвращает работоспособное состояние:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a>Используйте ПО промежуточного слоя для проверки работоспособности

В `Startup.Configure` вызовите <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> в конвейере обработки с URL-адресом конечной точки или относительным путем:

```csharp
app.UseHealthChecks("/health");
```

Если проверки работоспособности должны прослушивать определенный порт, используйте перегрузку <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>, чтобы задать порт (описаны далее в разделе [Фильтр по портам](#filter-by-port)):

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a>Варианты проверки работоспособности

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> предоставляет возможность настройки поведения для проверки работоспособности:

* [Фильтрация проверок работоспособности](#filter-health-checks)
* [Настройка кода состояния HTTP](#customize-the-http-status-code)
* [Подавление заголовков кэша](#suppress-cache-headers)
* [Настройка выходных данных](#customize-output)

### <a name="filter-health-checks"></a>Фильтрация проверок работоспособности

По умолчанию ПО промежуточного слоя для проверки работоспособности выполняет все зарегистрированные проверки работоспособности. Чтобы выполнить подмножество проверок работоспособности, укажите функцию, которая возвращает логическое значение через параметр <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>. В следующем примере проверка работоспособности `Bar` отфильтровывается по тегу (`bar_tag`) в условном операторе функции, где `true` возвращается только в том случае, если свойство проверки работоспособности <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> соответствует `foo_tag` или `baz_tag`:

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a>Настройка кода состояния HTTP

Используйте <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> для настройки сопоставления состояний работоспособности и кодов состояния HTTP. Следующие назначения <xref:Microsoft.AspNetCore.Http.StatusCodes> являются значениями по умолчанию, используемыми ПО промежуточного слоя. Измените значения кодов состояния в соответствии с требованиями.

В `Startup.Configure`:

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a>Подавление заголовков кэша

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> определяет, добавляет ли ПО промежуточного слоя HTTP-заголовки в ответ проверки, чтобы предотвратить кэширование ответов. Если значение равно `false` (по умолчанию), ПО промежуточного слоя задает или переопределяет заголовки `Cache-Control`, `Expires` и `Pragma`, чтобы предотвратить кэширование ответов. Если значение равно `true`, ПО промежуточного слоя не изменяет заголовки кэша в ответе.

В `Startup.Configure`:

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a>Настройка выходных данных

Параметр <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> возвращает или задает делегат, используемый для записи ответа. Делегат по умолчанию записывает минимальный ответ в формате открытого текста со строковым значением [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).

В `Startup.Configure`:

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

Делегат по умолчанию записывает минимальный ответ в формате открытого текста со строковым значением [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Следующий пользовательский делегат `WriteResponse` выводит настраиваемый ответ JSON:

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

Система проверки работоспособности не предоставляет встроенной поддержки сложных форматов возврата JSON, так как формат зависит от выбранной системы мониторинга. В приведенном выше примере вы можете настроить `JObject` в соответствии со своими потребностями.

## <a name="database-probe"></a>Проверка базы данных

Проверка работоспособности может задать запрос для выполнения в качестве логического теста для указания того, отвечает ли база данных как обычно.

Пример приложения использует [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), библиотеку проверки работоспособности для приложений ASP.NET Core, чтобы проверить работоспособность базы данных SQL Server. `AspNetCore.Diagnostics.HealthChecks` выполняет запрос `SELECT 1` к базе данных, чтобы подтвердить, что подключение к базе данных находится в работоспособном состоянии.

> [!WARNING]
> При проверке подключения к базе данных с помощью запроса выберите запрос, возвращающий результат быстро. Метод запроса связан с риском перегрузки базы данных и снижения ее производительности. В большинстве случаев тестовый запрос не является обязательным. Достаточно успешного подключения к базе данных. Если вам понадобится выполнить запрос, выберите простой запрос SELECT, например `SELECT 1`.

Добавьте ссылку на пакет [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Укажите допустимую строку подключения к базе данных в файле примера приложения *appsettings.json* . Приложение использует базу данных SQL Server с именем `HealthCheckSample`:

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

Зарегистрируйте службы проверки работоспособности при помощи <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> в `Startup.ConfigureServices`. Пример приложения вызывает метод `AddSqlServer` со строкой подключения базы данных (*DbHealthStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Вызовите ПО промежуточного слоя для проверки работоспособности в конвейере обработки приложения в `Startup.Configure`.

```csharp
app.UseHealthChecks("/health");
```

Чтобы запустить сценарий проверки базы данных с помощью примера приложения, выполните следующую команду из папки проекта в командной строке:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) не поддерживается корпорацией Майкрософт.

## <a name="entity-framework-core-dbcontext-probe"></a>Проверка DbContext в Entity Framework Core

Эта проверка `DbContext` подтверждает, что приложение может взаимодействовать с базой данных, настроенной для EF Core `DbContext`. Проверка `DbContext` поддерживается в приложениях, которые:

* используют [Entity Framework (EF) Core](/ef/core/);
* содержат ссылку на пакет [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` регистрирует проверку работоспособности для `DbContext`. `DbContext` передается методу в качестве `TContext`. Доступна перегрузка, позволяющая настроить состояние ошибки, теги и запрос тестирования.

По умолчанию:

* `DbContextHealthCheck` вызывает метод EF Core `CanConnectAsync`. Вы можете указать, какая операция выполняется в том случае, когда проверка работоспособности производится с помощью перегрузок метода `AddDbContextCheck`.
* Имя проверки работоспособности — это имя типа `TContext`.

В примере приложения `AppDbContext` предоставляется для `AddDbContextCheck` и регистрируется в качестве службы в `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

В примере приложения `UseHealthChecks` добавляет ПО промежуточного слоя для проверки работоспособности в `Startup.Configure`.

```csharp
app.UseHealthChecks("/health");
```

Прежде чем запускать сценарий проверки `DbContext` с помощью примера приложения, убедитесь, что база данных, указанная в строке подключения, не существует в экземпляре SQL Server. Если база данных существует, удалите ее.

Выполните следующую команду в папке проекта в командной строке:

```dotnetcli
dotnet run --scenario dbcontext
```

После запуска приложения проверьте состояние работоспособности, выполнив запрос к конечной точке `/health` в браузере. Базы данных и `AppDbContext` не существуют, поэтому приложение дает следующий ответ:

```
Unhealthy
```

Заставьте пример приложения создать базу данных. Сделайте запрос к `/createdatabase`. Приложение выдает ответ:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Сделайте запрос к конечной точке `/health`. База данных и контекст существуют, поэтому приложение отвечает:

```
Healthy
```

Заставьте пример приложения удалить базу данных. Сделайте запрос к `/deletedatabase`. Приложение выдает ответ:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Сделайте запрос к конечной точке `/health`. Приложение предоставляет ответ о неработоспособности:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Разделение проверок готовности и активности

В некоторых сценариях размещения используются пары проверок работоспособности, отличающие два состояния приложения:

* *Готовность* указывает, что приложение работает нормально, но не готово к приему запросов.
* *Активность* указывает на сбой приложения, и его необходимо перезапустить.

Рассмотрим следующий пример: Приложение должно загрузить большой файл конфигурации до того, как оно будет готово к обработке запросов. Нам не требуется перезапуск приложения в случае сбоя начальной загрузки, так как приложение может повторить попытку скачивания файла несколько раз. Для описания активности процесса используется *проба активности*, дополнительные проверки не выполняются. Кроме того, мы хотим предотвратить отправку запросов в приложение до успешной загрузки файла конфигурации. Мы используем *пробу активности*, чтобы указать состояние "не готово" до тех пор, пока загрузка не будет завершена и приложение не сможет принимать запросы.

Пример приложения содержит проверку работоспособности, которая сообщает о завершении длительной задачи запуска [размещенной службы](xref:fundamentals/host/hosted-services). `StartupHostedServiceHealthCheck` предоставляет свойство, `StartupTaskCompleted`, которое размещенная служба может задать как `true` после завершения длительной задачи (*StartupHostedServiceHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Длительная фоновая задача запущена [размещенной службой](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*). По завершении задачи `StartupHostedServiceHealthCheck.StartupTaskCompleted` получает значение `true`:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Проверка работоспособности регистрируется через <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> в `Startup.ConfigureServices` вместе с размещенной службой. Поскольку размещенной службе необходимо задать свойство проверки работоспособности, проверка работоспособности также регистрируется в контейнере службы (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Вызовите ПО промежуточного слоя для проверки работоспособности в конвейере обработки приложения в `Startup.Configure`. В примере приложения создаются конечные точки проверки работоспособности в `/health/ready` для проверки готовности и в `/health/live` для проверки жизнеспособности. Проверка готовности фильтрует проверки работоспособности по тегу `ready`. Проверка жизнеспособности отфильтровывает `StartupHostedServiceHealthCheck`, возвращая `false` в [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (дополнительные сведения см. в разделе [Фильтрация проверок работоспособности](#filter-health-checks)):

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

Чтобы запустить сценарий конфигурации проверок готовности и жизнеспособности с помощью примера приложения, выполните следующую команду из папки проекта в командной строке:

```dotnetcli
dotnet run --scenario liveness
```

В браузере посетите `/health/ready` несколько раз, пока не пройдет 15 секунд. Проверка работоспособности будет передавать состояние *Unhealthy* первые 15 секунд. По истечении 15 секунд конечная точка передает состояние *Healthy*, что означает завершение длительной задачи размещенной службой.

В этом примере также создается издатель проверки работоспособности (реализация <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>), который выполнит первую проверку готовности с задержкой в две секунды. Дополнительные сведения см. в разделе [об издателе проверки работоспособности](#health-check-publisher).

### <a name="kubernetes-example"></a>Пример для Kubernetes

Использование отдельных проверок готовности и жизнеспособности полезно в таких средах, как [Kubernetes](https://kubernetes.io/). В Kubernetes приложениям может потребоваться долго выполнять задачи во время запуска, прежде чем начать принимать запросы; например, проверить доступность основной базы данных. Использование отдельных проверок позволяет оркестратору различать, когда приложение работает, но еще не готово, а когда приложение не удалось запустить. Дополнительные сведения о проверках готовности и жизнеспособности в Kubernetes см. в разделе [Настройка проверок готовности и жизнеспособности](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) в документации по Kubernetes.

В следующем примере показана конфигурация проверки готовности для Kubernetes:

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Проба на основе метрики с настраиваемым модулем записи ответов

Этот пример демонстрирует проверку работоспособности памяти с настраиваемым модулем записи ответов.

Если приложение использует больше заданного порогового значения памяти (1 ГБ в примере приложения), `MemoryHealthCheck` сообщает о неработоспособном состоянии. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> включает сведения от сборщика мусора для приложения (*MemoryHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Зарегистрируйте службы проверки работоспособности при помощи <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> в `Startup.ConfigureServices`. Вместо того чтобы включать проверку работоспособности, передав ее в <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` регистрируется в качестве службы. Все зарегистрированные службы <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> доступны в службах проверки работоспособности и ПО промежуточного слоя. Мы рекомендуем регистрировать службы проверки работоспособности в качестве одноэлементных служб.

В примере приложения (*CustomWriterStartup.CS*) выполняется следующее:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Вызовите ПО промежуточного слоя для проверки работоспособности в конвейере обработки приложения в `Startup.Configure`. Делегат `WriteResponse` передается в свойство `ResponseWriter` для вывода настраиваемого ответа JSON при выполнении проверки работоспособности:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

Метод `WriteResponse` форматирует `CompositeHealthCheckResult` в объект JSON и выдает выходные данные JSON в качестве ответа проверки работоспособности:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Чтобы запустить проверку метрики с настраиваемым модулем записи ответа с помощью примера приложения, выполните следующую команду из папки проекта в командной строке:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) содержит сценарии для проверки работоспособности на основе метрик, включая проверки жизнеспособности для дискового хранилища и максимальных значений.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) не поддерживается корпорацией Майкрософт.

## <a name="filter-by-port"></a>Фильтр по портам

Вызов <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> с портом ограничивает запросы на проверку работоспособности указанным портом. Обычно это используется в контейнерной среде с целью предоставления порта для мониторинга служб.

Пример приложения настраивает порт с помощью [поставщика конфигурации переменных среды](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Порт устанавливается в файле *launchSettings.json* и передается поставщику конфигурации с помощью переменной среды. Необходимо также настроить на сервере прослушивание запросов через порт управления.

Чтобы использовать пример приложения для демонстрации настройки портов управления, создайте файл *launchSettings.json* в папке *Properties*.

Следующий файл примера приложения *Properties/launchSettings.json* отсутствует в файлах проекта примера приложения и должен быть создан вручную:

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

Зарегистрируйте службы проверки работоспособности при помощи <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> в `Startup.ConfigureServices`. Вызов <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> задает порт управления (*ManagementPortStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> Можно избежать создания файла *launchSettings.json* в примере приложения, явно указав URL-адрес и порт управления в коде. В *Program.cs*, где создается <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>, добавьте вызов <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> и укажите обычную конечную точку ответа приложения и порт конечной точки управления. В *ManagementPortStartup.cs*, где вызывается <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>, явно укажите порт управления.
>
> *Program.cs*:
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> *ManagementPortStartup.cs*:
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

Чтобы запустить сценарий с портом управления с помощью примера приложения, выполните следующую команду из папки проекта в командной строке:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Распространение библиотеки проверки работоспособности

Чтобы распространять проверки работоспособности в качестве библиотеки, выполните следующие действия.

1. Напишите проверку работоспособности, которая реализует интерфейс <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> как автономный класс. Класс может полагаться на [внедрение зависимостей (DI)](xref:fundamentals/dependency-injection), активацию типов и [именованные параметры](xref:fundamentals/configuration/options) для доступа к данным конфигурации.

   Логика проверки работоспособности `CheckHealthAsync` предусматривает следующее:

   * Использование в методе `data1` и `data2` для выполнения логики проверки работоспособности.
   * Обработку `AccessViolationException`.

   Когда происходит <xref:System.AccessViolationException>, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> возвращается вместе с <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, что позволяет пользователям настраивать состояние сбоя проверки работоспособности.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. Напишите метод расширения с параметрами, который приложение вызывает в своем методе `Startup.Configure`. В следующем примере предполагается следующая сигнатура метода проверки работоспособности:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Эта сигнатура указывает, что `ExampleHealthCheck` требуются дополнительные данные для обработки логики проверки работоспособности. Данные передаются делегату, используемому для создания экземпляра проверки работоспособности, когда проверка работоспособности регистрируется с помощью метода расширения. В следующем примере вызывающая сторона задает:

   * Необязательное имя проверки работоспособности (`name`). Если `null`, используется `example_health_check`.
   * Строковая точка данных для проверки работоспособности (`data1`).
   * Целочисленная точка данных для проверки работоспособности (`data2`). Если `null`, используется `1`.
   * состояние сбоя (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). Значение по умолчанию — `null`. В случае `null` возвращается [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) как состояние сбоя.
   * Теги (`IEnumerable<string>`).

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>Издатель проверки работоспособности

Когда <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> добавляется в контейнер службы, система проверки работоспособности периодически выполняет ваши проверки работоспособности и вызывает `PublishAsync` с полученным результатом. Это полезно в ситуации принудительной передачи данных мониторинга работоспособности, когда ожидается, что каждый процесс периодически вызывает систему мониторинга для определения работоспособности.

Интерфейс <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> содержит один метод:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> позволяет задать следующие параметры:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Исходная задержка, которая срабатывает после запуска приложения и перед выполнением экземпляров <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Задержка применяется при запуске один раз и не распространяется на все последующие итерации. Значение по умолчанию — пять секунд.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Период выполнения <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Значение по умолчанию - 30 секунды.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Если <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> имеет значение `null` (по умолчанию), служба издателя проверки работоспособности выполняет все зарегистрированные проверки работоспособности. Чтобы выполнить ряд проверок работоспособности, укажите функцию, которая отфильтровывает нужный набор. Предикат вычисляется в каждом периоде.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Время ожидания для выполнения проверок работоспособности для всех экземпляров <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Используйте <xref:System.Threading.Timeout.InfiniteTimeSpan>, чтобы выполнить проверки без задержки. Значение по умолчанию - 30 секунды.

> [!WARNING]
> В выпуске ASP.NET Core 2.2 значение <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> не поддерживается в реализации <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Здесь задается значение <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>. Эта проблема была устранена в ASP.NET Core 3.0.

В примере приложения `ReadinessPublisher` является реализацией <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Состояние проверки работоспособности записывается для каждой проверки на одном из таких уровней журнала:

* на уровне сведений (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>), если состояние проверки работоспособности — <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>;
* на уровне ошибки (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>), если состояние соответствует <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> или <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

В примере приложения `LivenessProbeStartup` проверка готовности `StartupHostedService` использует задержку запуска в две секунды и выполняет проверку каждые 30 секунд. Чтобы активировать реализацию <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, этот пример регистрирует `ReadinessPublisher` как отдельную службу в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection).

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> Следующий вариант позволяет добавлять экземпляр <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> в контейнер службы, когда один или несколько других размещенных служб уже добавлены в приложение. Такой обходной путь не требуется в ASP.NET Core 3.0.
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) содержит издатели для нескольких систем, в том числе [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) не поддерживается корпорацией Майкрософт.

## <a name="restrict-health-checks-with-mapwhen"></a>Ограничение проверок работоспособности с помощью MapWhen

Используйте <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> для условного ветвления конвейера запросов для конечных точек проверки работоспособности.

В следующем примере `MapWhen` выполняет ветвление конвейера запросов для активации ПО промежуточного слоя для проверки работоспособности, если для конечной точки `api/HealthCheck` поступает запрос GET:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

Для получения дополнительной информации см. <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end
