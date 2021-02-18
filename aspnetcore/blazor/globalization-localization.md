---
title: Глобализация и локализация в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как сделать компоненты Razor доступными для пользователей с различными языковыми и региональными параметрами.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/globalization-localization
ms.openlocfilehash: 8baee9bc0a8e569174f33dac6a406b2162d09552
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279919"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a>Глобализация и локализация в ASP.NET Core Blazor

Компоненты Razor можно сделать доступными для пользователей с различными языковыми и региональными параметрами. Доступны следующие сценарии глобализации и локализации .NET:

* система ресурсов NET;
* форматирование чисел и дат в зависимости от языка и региональных параметров.

В настоящее время поддерживается ограниченный набор сценариев локализации ASP.NET Core:

* <xref:Microsoft.Extensions.Localization.IStringLocalizer> и <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *поддерживаются* в приложениях Blazor.
* <xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> и локализация заметок к данным являются сценариями MVC ASP.NET Core и **не поддерживается** в приложениях Blazor.

Для получения дополнительной информации см. <xref:fundamentals/localization>.

## <a name="globalization"></a>Глобализация

Функция [`@bind`](xref:mvc/views/razor#bind) в Blazor выполняет форматирование и синтаксический анализ значений, отображаемых на основе текущего языка и региональных параметров пользователя.

Доступ к текущему языку и региональным параметрам можно получить из свойства <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.

<xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> используется для следующих типов полей (`<input type="{TYPE}" />`):

* `date`
* `number`

Предыдущие типы полей:

* отображаются с использованием соответствующих правил форматирования браузера;
* не могут содержать текст в свободной форме;
* предоставляют характеристики взаимодействия с пользователем в зависимости от реализации браузера.

Следующие типы полей имеют особые требования к форматированию, которые в настоящее время не поддерживаются в Blazor, так как они не поддерживаются всеми основными браузерами.

* `datetime-local`
* `month`
* `week`

[`@bind`](xref:mvc/views/razor#bind) поддерживает параметр `@bind:culture`, чтобы предоставлять <xref:System.Globalization.CultureInfo?displayProperty=fullName> для анализа и форматирования значения. Указание языка и региональных параметров не рекомендуется при использовании типов полей `date` и `number`. Типы `date` и `number` имеют встроенную поддержку Blazor, которая обеспечивает поддержку языка и региональных параметров.

## <a name="localization"></a>Локализация

### Blazor WebAssembly

Приложения Blazor WebAssembly задают язык и региональные параметры с помощью [предпочитаемого языка пользователя](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).

Чтобы явно настроить язык и региональные параметры, задайте <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> и <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> в `Program.Main`.

::: moniker range=">= aspnetcore-5.0"

По умолчанию Blazor WebAssembly содержит минимальные ресурсы глобализации, необходимые для отображения значений, таких как дата и валюта, на языке пользователя и с его региональными параметрами. Для приложений, которые должны поддерживать динамическое изменение языка и региональных параметров, нужно настроить параметр `BlazorWebAssemblyLoadAllGlobalizationData` в файле проекта:

```xml
<PropertyGroup>
  <BlazorWebAssemblyLoadAllGlobalizationData>true</BlazorWebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

Вы также можете задать параметры, передаваемыми в `Blazor.start`, чтобы приложение Blazor WebAssembly запускалось с определенными региональными параметрами. Например, в примере ниже показано, что приложение настроено для запуска с использованием языка и региональных параметров `en-GB`:

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  Blazor.start({
    applicationCulture: 'en-GB'
  });
</script>
```

Значение параметра `applicationCulture` должно соответствовать [языковому тегу в формате BCP-47](https://tools.ietf.org/html/bcp47).

Если приложению не требуется локализация, можно настроить поддержку инвариантных языка и региональных параметров, основанных на языке и региональных параметрах `en-US`:

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

По умолчанию конфигурация компоновщика промежуточного языка (IL) для приложений Blazor WebAssembly исключает сведения об интернационализации, кроме явно запрошенных языковых стандартов. Для получения дополнительной информации см. <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.

::: moniker-end

Хотя язык и региональные параметры, выбираемые Blazor по умолчанию, могут быть достаточными для большинства пользователей, рекомендуется предоставить пользователям возможность указать предпочитаемый языковой стандарт. Пример приложения Blazor WebAssembly с выбором языка и региональных параметров см. в описании примера приложения локализации [`LocSample`](https://github.com/pranavkm/LocSample).

### Blazor Server

Приложения Blazor Server локализуются с помощью [промежуточного слоя локализации](xref:fundamentals/localization#localization-middleware). ПО промежуточного слоя выбирает соответствующие языки и региональные параметры для пользователей, запрашивающих ресурсы из приложения.

Язык и региональные параметры можно задать с помощью одного из следующих подходов.

* [Файлы Cookie](#cookies)
* [Предоставление пользовательского интерфейса для выбора языка и региональных параметров](#provide-ui-to-choose-the-culture)

Дополнительные сведения и примеры см. в разделе <xref:fundamentals/localization>.

#### <a name="cookies"></a>Cookies

Файл cookie локализации может запоминать язык и региональные параметры пользователя. ПО промежуточного слоя локализации считывает файл cookie при последующих запросах, чтобы задать язык и региональные параметры пользователя. 

Использование файла cookie гарантирует, что соединение WebSocket сможет правильно распространить языковые и региональные параметры. Если схема локализации основана на URL-адресе или строке запроса, она может не поддерживать работу с WebSockets и не сможет сохранить язык и региональные параметры. Поэтому рекомендуемым подходом для локализации является использование файла cookie языка и региональных параметров.

Если язык и региональные параметры сохраняются в файле cookie с локализацией, то можно использовать любой метод для назначения языка и региональных параметров. Если у приложения уже есть установленная схема локализации для ASP.NET Core на стороне сервера, продолжайте использовать существующую инфраструктуру локализации приложения и задавайте файл cookie языка и региональных параметров для локализации в схеме приложения.

В следующем примере показано, как задать текущий язык и региональные параметры в файле cookie, который можно прочитать в ПО промежуточного слоя локализации. Создайте Razor выражение в файле `Pages/_Host.cshtml` непосредственно в открывающем теге `<body>`:

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

Локализация обрабатывается приложением в следующей последовательности событий.

1. Браузер отправляет первоначальный HTTP-запрос в приложение.
1. Язык и региональные параметры назначаются в ПО промежуточного слоя локализации.
1. Выражение Razor на странице `_Host` (`_Host.cshtml`) сохраняет язык и региональные параметры в файле cookie как часть ответа.
1. Браузер открывает соединение WebSocket для создания интерактивного сеанса Blazor Server.
1. ПО промежуточного слоя для локализации считывает файл cookie и назначает язык и региональные параметры.
1. Сеанс Blazor Server начинается с нужными языком и региональными параметрами.

При работе с <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage> используйте свойство <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context>:

```razor
@{
    this.Context.Response.Cookies.Append(
        CookieRequestCultureProvider.DefaultCookieName,
        CookieRequestCultureProvider.MakeCookieValue(
            new RequestCulture(
                CultureInfo.CurrentCulture,
                CultureInfo.CurrentUICulture)));
}
```

#### <a name="provide-ui-to-choose-the-culture"></a>Предоставление пользовательского интерфейса для выбора языка и региональных параметров

Чтобы предоставить пользовательский интерфейс, позволяющий пользователю выбрать язык и региональные параметры, рекомендуется *подход на основе перенаправления*. Процесс аналогичен тому, что происходит в веб-приложении, когда пользователь пытается получить доступ к защищенному ресурсу. Пользователь перенаправляется на страницу входа, а затем обратно к исходному ресурсу. 

Приложение сохраняет выбранный язык и региональные параметры пользователя с помощью перенаправления к контроллеру. Контроллер устанавливает выбранный язык и региональные параметры пользователя в файл cookie и перенаправляет пользователя обратно в исходный URI.

Установите конечную точку HTTP на сервере, чтобы задать выбранный язык и региональные параметры пользователя в файле cookie, и выполните перенаправление обратно в исходный URI:

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Для предотвращения атак с открытым перенаправлением используйте результат действия <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A>. Для получения дополнительной информации см. <xref:security/preventing-open-redirects>.

Если приложение не настроено для обработки действий контроллера:

* Добавьте службы MVC в коллекцию служб в `Startup.ConfigureServices`:

  ```csharp
  services.AddControllers();
  ```

* Добавьте маршрутизацию конечных точек контроллера в `Startup.Configure`:

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

В следующем компоненте показан пример выполнения начального перенаправления, когда пользователь выбирает язык и региональные параметры:

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/localization>
