---
title: Введение в ASP.NET Core Blazor
author: guardrex
description: Узнайте больше об использовании ASP.NET Core Blazor для создания интерактивного клиентского веб-интерфейса с помощью .NET в приложении ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019, devx-track-js
ms.date: 09/25/2020
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
uid: blazor/index
ms.openlocfilehash: d42136de00ccc9b7565c5ae088e1ecf4560a63c5
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106886"
---
# <a name="introduction-to-aspnet-core-blazor"></a>Введение в ASP.NET Core Blazor

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

*Добро пожаловать в Blazor!*

Платформа Blazor предназначена для создания интерактивного веб-интерфейса на стороне клиента с использованием [.NET](/dotnet/standard/tour) и предоставляет следующие возможности:

* создание многофункциональных интерактивных пользовательских интерфейсов на [C#](/dotnet/csharp/) вместо [JavaScript](https://www.javascript.com);
* совместное использование серверной и клиентской логик приложений, написанных с помощью .NET;
* отображение пользовательского интерфейса в виде HTML-страницы с CSS для широкой поддержки браузеров, в том числе для мобильных устройств.
* интеграция с современными платформами размещения, такими как [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).

Использование .NET для разработки веб-приложений на стороне клиента предоставляет следующие преимущества:

* создавайте код на C#, а не на JavaScript.
* возможность использовать существующую экосистему .NET с [библиотеками .NET](/dotnet/standard/class-libraries);
* сохранение единой логики приложений для сервера и клиента;
* высокая производительность, надежность и безопасность платформы .NET;
* сохранение высокого уровня продуктивности с помощью [Visual Studio](https://visualstudio.microsoft.com) в Windows, Linux и macOS;
* создавайте приложения на основе распространенных языков, платформ и инструментов, которые отличаются стабильностью, широким набором функций и простотой в использовании.

## <a name="components"></a>Компоненты

Приложения Blazor основаны на *компонентах*. Компонентом Blazor называется любой элемент пользовательского интерфейса, например страница, диалоговое окно или форма ввода данных.

Компоненты являются классами C# .NET, встроенными в [сборки .NET](/dotnet/standard/assembly/), которые:

* определяют гибкую логику визуализации пользовательского интерфейса;
* обрабатывают действия пользователя;
* можно вкладывать друг в друга и использовать повторно;
* можно совместно использовать и распространять в виде [библиотек классов Razor](xref:razor-pages/ui-class) или [пакетов NuGet](/nuget/what-is-nuget).

Класс компонента обычно записывается в виде страницы разметки [Razor](xref:mvc/views/razor) с расширением файла `.razor`. Обычно компоненты в Blazor называются *компонентами Razor* . Синтаксис Razor сочетает разметку HTML с кодом C#, позволяя повысить производительность разработчиков. Razor позволяет переключаться между разметкой HTML и кодом C# в одном файле благодаря программной поддержке [IntelliSense](/visualstudio/ide/using-intellisense) в Visual Studio. Синтаксис Razor применяется также для Razor Pages и MVC. В отличие от Razor Pages и MVC, которые построены по модели "запрос-ответ", компоненты используются специально для логики пользовательского интерфейса на стороне клиента и для компоновки.

Blazor использует естественные теги HTML для компоновки пользовательского интерфейса. В следующем примере разметки Razor демонстрируется использование компонента (`Dialog.razor`), который отображает диалоговое окно и обрабатывает событие, когда пользователь нажимает кнопку:

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

В предыдущем примере метод C# `OnYes` инициируется событием кнопки `onclick`. Текст (`ChildContent`) и заголовок (`Title`) диалогового окна предоставляются описанным ниже компонентом, который использует этот компонент в пользовательском интерфейсе.

Компонент `Dialog` вложен в другой компонент с помощью HTML-тега. В следующем примере компонент `Index` (`Pages/Index.razor`) использует предыдущий компонент `Dialog`. Атрибут `Title` тега передает значение заголовка в свойство `Title` компонента `Dialog`.  Текст компонента `Dialog` (`ChildContent`) определяется содержимым элемента `<Dialog>`. Если компонент `Dialog` добавлен в компонент `Index`, [IntelliSense в Visual Studio](/visualstudio/ide/using-intellisense) позволяет ускорить разработку за счет дополнения элементов синтаксиса и параметров.

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

Когда к компоненту `Index` осуществляется доступ в браузере, отображается диалоговое окно. Когда пользователь нажимает кнопку, в консоли средств разработчика браузера отображается сообщение, созданное методом `OnYes`:

![В браузере отображается компонент Dialog как вложенный в компонент Index. В консоли средств разработчика браузера отображается сообщение, создаваемое кодом C#, когда пользователь нажимает кнопку "Да" в пользовательском интерфейсе.](index/_static/dialog.png)

Компоненты преобразуются в хранящееся в памяти представление [модели DOM для браузера](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction), которое называется *деревом отображения*, позволяя гибко и эффективно обновлять пользовательский интерфейс.

## Blazor WebAssembly

Blazor WebAssembly — это [платформа одностраничных приложений (SPA)](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) для создания интерактивных клиентских веб-приложений с использованием .NET. Blazor WebAssembly использует открытые веб-стандарты без подключаемых модулей или перекомпиляции кода на другие языки. Blazor WebAssembly работает во всех современных веб-браузерах, включая браузеры мобильных устройств.

Выполнение кода .NET в веб-браузерах становится возможным благодаря технологии [WebAssembly](https://webassembly.org) (сокращенно `wasm`). WebAssembly — это компактный формат байт-кода, оптимизированный для быстрой загрузки и максимального быстродействия. WebAssembly — это открытый веб-стандарт, который поддерживается в веб-браузерах без подключаемых модулей.

Код WebAssembly может обращаться к любым функциям браузера через JavaScript благодаря поддержке *взаимодействия с JavaScript* (сокращенно *JavaScript interop* или *JS interop*). Код .NET, который обрабатывается через WebAssembly в браузере, выполняется в песочнице для JavaScript этого браузера, которая включает средства защиты от вредоносных действий на клиентском компьютере.

![Blazor WebAssembly выполняет код .NET в браузере с WebAssembly.](index/_static/blazor-webassembly.png)

Когда приложение Blazor WebAssembly создано и запущено в браузере:

* Файлы кода C# и файлы Razor компилируются в сборки .NET.
* Сборки и [среда выполнения .NET](/dotnet/framework/get-started/overview) загружаются в браузер.
* Blazor WebAssembly осуществляет начальную загрузку среды выполнения .NET и настраивает ее для загрузки сборок для приложения. Среда выполнения Blazor WebAssembly использует взаимодействие с JavaScript для обработки операций с моделью DOM и вызовов API браузера.

Размер опубликованного приложения, также называемый *размером полезных данных*, является критически важным фактором для удобства работы с приложением, влияющим на производительность. Крупное приложение скачивается в браузере довольно долго, что ухудшает взаимодействие с пользователем. Blazor WebAssembly оптимизирует размер полезных данных, чтобы сократить время скачивания:

::: moniker range=">= aspnetcore-5.0"

* При публикации неиспользуемый код исключается из приложения [средством обрезки для промежуточного языка](xref:blazor/host-and-deploy/configure-trimmer).
* HTTP-ответы сжимаются;
* среда выполнения и сборки .NET кэшируются в браузере.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* При публикации неиспользуемый код исключается из приложения [компоновщиком промежуточного языка](xref:blazor/host-and-deploy/configure-linker).
* HTTP-ответы сжимаются;
* среда выполнения и сборки .NET кэшируются в браузере.

::: moniker-end

## Blazor Server

Blazor отделяет логику отображения компонентов от того, как применяются обновления пользовательского интерфейса. *Blazor Server* поддерживает размещение компонентов Razor на сервере в приложении ASP.NET Core. Обновления пользовательского интерфейса передаются через подключение [SignalR](xref:signalr/introduction).

Среда выполнения остается на сервере и обрабатывает:

* выполнение кода C# приложения.
* отправка событий пользовательского интерфейса из браузера на сервер;
* применение полученных с сервера обновлений пользовательского интерфейса к отображаемому компоненту.

Подключение, используемое Blazor Server для обмена данными с браузером, также применяется для обработки вызовов взаимодействия JavaScript.

![Blazor Server выполняет код .NET на сервере и взаимодействует с моделью DOM на стороне клиента через подключение SignalR](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Взаимодействие с JavaScript

Для приложений, которым требуются сторонние библиотеки JavaScript и доступ к API браузера, компоненты взаимодействуют с JavaScript. Компоненты могут использовать любую библиотеку или API, которые может использовать JavaScript. Код C# может [вызывать код JavaScript](xref:blazor/call-javascript-from-dotnet), а код JavaScript — [код C#](xref:blazor/call-dotnet-from-javascript).

## <a name="code-sharing-and-net-standard"></a>Совместное использование кода и .NET Standard

Blazor реализует [.NET Standard](/dotnet/standard/net-standard), что позволяет проектам Blazor ссылаться на библиотеки, которые соответствуют спецификациям .NET Standard. .NET Standard — это формальная спецификация API-интерфейсов .NET, которые доступны во всех реализациях .NET. Библиотеки классов .NET Standard можно использовать на разных платформах .NET, таких как Blazor, .NET Framework, .NET Core, Xamarin, Mono и Unity.

API, которые не используются в веб-браузере (например, для доступа к файловой системе, открытия сокетов и работы с потоками), создают исключение <xref:System.PlatformNotSupportedException>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [WebAssembly](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [Руководство по языку C#](/dotnet/csharp/)
* [Справочник по синтаксису Razor для ASP.NET Core](xref:mvc/views/razor)
* [HTML](https://www.w3.org/html/)
* Ссылки на сообщество [Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor)
