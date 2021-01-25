---
title: Библиотеки классов компонентов Razor в ASP.NET Core
author: guardrex
description: Сведения о включении компонентов в приложения Blazor из внешней библиотеки компонентов.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 14370f9bbf45079fd3654d3e55af4178691cf4f5
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252556"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a>Библиотеки классов компонентов Razor в ASP.NET Core

Автор: [Саймон Тиммс](https://github.com/stimms) (Simon Timms)

Компоненты можно совместно использовать в проектах посредством [библиотеки классов Razor](xref:razor-pages/ui-class). *Библиотека классов компонентов Razor* может включаться из следующих источников:

* другого проекта в решении;
* пакета NuGet;
* связанной библиотеки .NET.

Так же как компоненты являются обычными типами .NET, компоненты, предоставляемые RCL, представляют собой обычные сборки .NET.

## <a name="create-an-rcl"></a>Создание библиотеки RCL

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Создайте новый проект.
1. Выберите **Библиотека классов Razor** . Выберите **Далее**.
1. В диалоговом окне **Создать библиотеку классов Razor** щелкните **Создать**.
1. В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию. В примерах в этой статье используется имя проекта `ComponentLibrary`. Выберите **Создать**.
1. Добавьте библиотеку RCL в решение.
   1. Щелкните решение правой кнопкой мыши. Выберите **Добавить** > **Существующий проект**.
   1. Перейдите к файлу проекта RCL.
   1. Выберите файл проекта RCL (`.csproj`).
1. Добавьте ссылку на RCL из приложения:
   1. Щелкните проект приложения правой кнопкой мыши. Выберите **Добавить** > **Ссылка**.
   1. Выберите проект RCL. Нажмите кнопку **ОК**.

> [!NOTE]
> Если при создании библиотеки RCL на основе шаблона был установлен флажок **Представления и страницы поддержки**, также добавьте в корневой каталог созданного проекта файл `_Imports.razor` со следующим содержимым, чтобы включить разработку компонента Razor:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Вручную добавьте файл в корневой каталог созданного проекта.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

1. Используйте шаблон **Библиотека классов Razor** (`razorclasslib`) с командой [`dotnet new`](/dotnet/core/tools/dotnet-new) в командной оболочке. В приведенном ниже примере создается библиотека RCL с именем `ComponentLibrary`. Папка с `ComponentLibrary` создается автоматически при выполнении команды.

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > Если при создании библиотеки RCL на основе шаблона используется параметр `-s|--support-pages-and-views`, также добавьте в корневой каталог созданного проекта файл `_Imports.razor` со следующим содержимым, чтобы включить разработку компонента Razor:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Вручную добавьте файл в корневой каталог созданного проекта.

1. Чтобы добавить библиотеку в существующий проект, используйте команду [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) в командной оболочке. В приведенном ниже примере в приложение добавляется библиотека RCL. Выполните следующую команду из папки проекта приложения, указав путь к библиотеке:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Использование компонента из библиотеки

Чтобы использовать компоненты, определенные в библиотеке в другом проекте, используйте один из описанных ниже подходов.

* Используйте полное имя типа с пространством имен.
* Используйте директиву [`@using`](xref:mvc/views/razor#using) Razor. Отдельные компоненты можно добавлять по имени.

В приведенном ниже примере `ComponentLibrary` — это библиотека компонентов, содержащая компонент `Component1` (`Component1.razor`). Компонент `Component1` — это пример компонента, автоматически добавляемого шаблоном проекта RCL при создании библиотеки.

Сослаться на компонент `Component1`, используя его пространство имен, можно так:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

Кроме того, можно перенести библиотеку в область с директивой [`@using`](xref:mvc/views/razor#using) и использовать компонент без его пространства имен:

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

Кроме того, чтобы сделать компоненты библиотеки доступными для всего проекта, включите директиву `@using ComponentLibrary` в файл `_Import.razor` верхнего уровня. Чтобы применить пространство имен к одному компоненту или набору компонентов в папке, добавьте директиву в файл `_Import.razor` на любом уровне.

::: moniker range=">= aspnetcore-5.0"

Для компонентов библиотеки, которые используют [изоляцию CSS](xref:blazor/components/css-isolation), нет необходимости явно связывать таблицы стилей отдельных компонентов библиотеки в приложении, использующем библиотеку. Стили компонентов автоматически становятся доступными для использующего приложения.

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

NEXT PARAGRAPH: RECAST TO 'CAN ALSO ADOPT ...'

-->

Чтобы предоставить дополнительные стили компонентов библиотеки из таблиц стилей в папке `wwwroot` библиотеки, свяжите таблицы стилей в файле `wwwroot/index.html` (Blazor WebAssembly) или файле `Pages/_Host.cshtml` (Blazor Server) использующего приложения:

```html
<head>
    ...
    <link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />
</head>
```

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Чтобы предоставить класс CSS `Component1` из `my-component`, свяжите его с таблицей стилей в файле `wwwroot/index.html` приложения (Blazor WebAssembly) или в файле `Pages/_Host.cshtml` (Blazor Server):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a>Создание библиотеки классов компонентов Razor со статическими ресурсами

Библиотека RCL может включать в себя статические ресурсы. Такие ресурсы доступны любому приложению, использующему библиотеку. Для получения дополнительной информации см. <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a>Предоставление компонентов и статических ресурсов нескольким размещенным приложениям Blazor

Для получения дополнительной информации см. <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a>Анализатор совместимости с браузерами для Blazor WebAssembly

Приложения Blazor WebAssembly предназначены для использования в полной контактной зоне API .NET, но из-за ограничений песочницы браузера поддерживаются не все API-интерфейсы .NET. При выполнении в WebAssembly неподдерживаемые API-интерфейсы вызывают <xref:System.PlatformNotSupportedException>. Анализатор совместимости платформ предупреждает разработчика, когда приложение использует API-интерфейсы, которые не поддерживаются целевыми платформами приложения. Это означает, что в приложениях Blazor WebAssembly проверяется поддержка API-интерфейсов в браузерах. Добавление заметок к API-интерфейсам .NET Framework для анализатора совместимости является непрерывным процессом, поэтому в настоящее время снабжены заметками не все API-интерфейсы .NET Framework.

Проекты библиотеки классов Blazor WebAssembly и Razor *автоматически* включают проверки совместимости браузера, добавляя `browser` в качестве поддерживаемой платформы с элементом MSBuild `SupportedPlatform`. Разработчики библиотек могут вручную добавить элемент `SupportedPlatform` в файл проекта библиотеки, чтобы включить эту функцию:

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

При создании библиотеки укажите, что конкретный API не поддерживается в браузерах, задав для `browser` значение <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

Дополнительные сведения см. в статье [Аннотация API-интерфейсов как неподдерживаемых на конкретных платформах (репозиторий GitHub dotnet/designs](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Изоляция Blazor JavaScript и ссылки на объекты

Blazor реализует изоляцию JavaScript в стандартных [модулях JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules). Изоляция JavaScript обеспечивает следующие преимущества:

* Импортированный JavaScript больше не засоряет глобальное пространство имен.
* Пользователям библиотеки и компонентов не требуется импортировать связанный код JavaScript вручную.

Для получения дополнительной информации см. <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a>Сборка, упаковка и отправка в NuGet

Так как библиотеки компонентов являются стандартными библиотеками .NET, их упаковка и передача в NuGet не отличается от упаковки и передачи любых других библиотек. Упаковка выполняется с помощью команды [`dotnet pack`](/dotnet/core/tools/dotnet-pack) в командной оболочке:

```dotnetcli
dotnet pack
```

Чтобы отправить пакет в NuGet, используйте команду [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) в командной оболочке.

## <a name="additional-resources"></a>Дополнительные ресурсы

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Добавление файла конфигурации фильтрации XML для промежуточного языка (IL) в библиотеку](xref:blazor/host-and-deploy/configure-trimmer)
* [Поддержка изоляции CSS с помощью библиотек классов Razor](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Добавление файла конфигурации компоновщика XML для промежуточного языка (IL) в библиотеку](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
