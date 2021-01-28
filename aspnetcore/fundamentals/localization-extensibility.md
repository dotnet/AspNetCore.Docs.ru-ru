---
title: Расширяемость локализации
author: hishamco
description: Узнайте, как расширить интерфейсы API локализации в приложениях ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
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
uid: fundamentals/localization-extensibility
ms.openlocfilehash: c41ebc0506a565666963d9b4c70ed73df389c826
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751656"
---
# <a name="localization-extensibility"></a>Расширяемость локализации

Автор: [Хишам Бин Атея](https://github.com/hishamco) (Hisham Bin Ateya)

В этой статье:

* Перечисляет точки расширения в API локализации.
* В этой статье приводятся инструкции по расширениям локализации в приложениях ASP.NET Core.

## <a name="extensible-points-in-localization-apis"></a>Точки расширения в API локализации

API локализации ASP.NET Core созданы с учетом потребностей расширения. Расширяемость позволяет разработчикам настраивать локализацию в соответствии со своими потребностями. Например, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) имеет `POStringLocalizer`. В `POStringLocalizer` приводится подробное описание использования [локализации переносимых объектов](xref:fundamentals/portable-object-localization) для использования файлов `PO` для хранения ресурсов локализации.

В этой статье описаны две основные точки расширения, предоставляемые API локализации. 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a>Поставщики языка и региональных параметров локализации

API локализации ASP.NET Core имеют четыре поставщика по умолчанию, которые могут определить текущий язык и региональные параметры выполняемого запроса:

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

Указанные выше поставщики подробно описаны в документации [ПО промежуточного слоя локализации](xref:fundamentals/localization). Если поставщики по умолчанию не отвечают вашим потребностям, создайте свой поставщик, используя один из следующих подходов:

### <a name="use-customrequestcultureprovider"></a>Используя CustomRequestCultureProvider

<xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> предоставляет пользовательский <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>, использующий простой делегат для определения текущего языка и региональных параметров локализации:

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a>Используя новую реализацию RequestCultureProvider

Можно создать новую реализацию <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>, которая определяет сведения о языке и региональных параметрах запроса из пользовательского источника. Например, пользовательский источник может быть файлом конфигурации или базой данных.

В следующем примере показан класс `AppSettingsRequestCultureProvider`, который расширяет класс <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> для определения сведений о языке и региональных параметрах запроса из *appsettings.json* :

```csharp
public class AppSettingsRequestCultureProvider : RequestCultureProvider
{
    public string CultureKey { get; set; } = "culture";

    public string UICultureKey { get; set; } = "ui-culture";

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
        {
            throw new ArgumentNullException();
        }

        var configuration = httpContext.RequestServices.GetService<IConfigurationRoot>();
        var culture = configuration[CultureKey];
        var uiCulture = configuration[UICultureKey];

        if (culture == null && uiCulture == null)
        {
            return Task.FromResult((ProviderCultureResult)null);
        }

        if (culture != null && uiCulture == null)
        {
            uiCulture = culture;
        }

        if (culture == null && uiCulture != null)
        {
            culture = uiCulture;
        }
        
        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

## <a name="localization-resources"></a>Ресурсы локализации

Для локализации в ASP.NET Core есть <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>. <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> — это реализация <xref:Microsoft.Extensions.Localization.IStringLocalizer>, которая использует `resx` для хранения ресурсов локализации.

Вы не ограничены использованием файлов `resx`. При реализации `IStringLocalizer` можно использовать любой источник данных.

В следующих примерах проектов реализуется <xref:Microsoft.Extensions.Localization.IStringLocalizer>: 

* [EFStringLocalizer](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [JsonStringLocalizer](https://github.com/hishamco/My.Extensions.Localization.Json)
* [SqlLocalizer](https://github.com/damienbod/AspNetCoreLocalization)
