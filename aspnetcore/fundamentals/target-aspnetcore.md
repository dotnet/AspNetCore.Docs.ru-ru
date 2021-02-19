---
title: Использование интерфейсов API ASP.NET Core в библиотеке классов
author: scottaddie
description: Узнайте, как использовать интерфейсы API ASP.NET Core в библиотеке классов.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
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
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: c012658a6f48247af60c8bfd56a7d987f6aa8a68
ms.sourcegitcommit: c1839f2992b003c92cd958244a2e0771ae928786
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2021
ms.locfileid: "93061513"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>Использование интерфейсов API ASP.NET Core в библиотеке классов

Автор: [Скотт Адди](https://github.com/scottaddie) (Scott Addie)

В этом документе приводятся рекомендации по использованию интерфейсов API ASP.NET Core в библиотеке классов. Остальные рекомендации по работе с библиотекой см. в статье [Руководство по библиотекам с открытым кодом](/dotnet/standard/library-guidance/).

## <a name="determine-which-aspnet-core-versions-to-support"></a>Определение поддерживаемых версий ASP.NET Core

ASP.NET Core соответствует [политике поддержки .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Ознакомьтесь с политикой поддержки, чтобы узнать, какие версии ASP.NET Core поддерживаются в библиотеке. Библиотека должна:

* по возможности предоставлять поддержку всех версий ASP.NET Core, классифицированную как *долгосрочная поддержка* (LTS).
* не обязательно предоставлять поддержку версий ASP.NET Core, классифицированную как *окончание жизненного цикла* (EOL).

По мере того как предварительные выпуски ASP.NET Core становятся доступными, критические изменения публикуются в репозитории [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) на GitHub. Тестирование совместимости библиотек можно выполнять как разработку функций платформы.

## <a name="use-the-aspnet-core-shared-framework"></a>Использование общей платформы .NET Core

В выпуске .NET Core версии 3.0 многие сборки ASP.NET Core больше не публикуются в NuGet в качестве пакетов. Вместо этого сборки включаются в состав общей платформы `Microsoft.AspNetCore.App`, которая устанавливается вместе с пакетом SDK для .NET Core и установщиками среды выполнения. Список пакетов, которые больше не публикуются, см. в разделе [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references) (Удаление устаревших ссылок на пакеты).

Начиная с .NET Core версии 3.0, проекты, использующие пакет SDK `Microsoft.NET.Sdk.Web` для MSBuild, неявно ссылаются на общую платформу. Проекты, использующие пакет SDK для `Microsoft.NET.Sdk` или `Microsoft.NET.Sdk.Razor`, должны ссылаться на ASP.NET Core, чтобы использовать интерфейсы API ASP.NET Core в общей платформе.

Чтобы сослаться на ASP.NET Core, добавьте в файл проекта следующий элемент `<FrameworkReference>`:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

Такая ссылка на ASP.NET Core поддерживается только для проектов, предназначенных для .NET Core 3.x.

## <a name="include-blazor-extensibility"></a>Включение расширяемости Blazor

Blazor поддерживает [модели размещения](xref:blazor/hosting-models) Server и WebAssembly (WASM). Если не требуется иное, то библиотека [компонентов Razor](xref:blazor/components/index) должна поддерживать обе модели размещения. Библиотека компонентов Razor должна использовать пакет SDK для [Microsoft.NET.Sdk.Razor ](xref:razor-pages/sdk).

### <a name="support-both-hosting-models"></a>Поддержка обеих моделей размещения

Для поддержки использования компонентов Razor из проектов [Blazor Server](xref:blazor/hosting-models#blazor-server) и [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) используйте следующие инструкции для редактора.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Используйте шаблон проекта **библиотеки классов Razor** . Снимите флажок **Support pages and views** (Представления и страницы поддержки).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Во встроенном терминале выполните следующую команду:

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Используйте шаблон проекта **библиотеки классов Razor** .

---

Проект, созданный на основе шаблона, выполняет следующие действия:

* Использует целевые объекты .NET Standard 2.0.
* Задает для свойства `RazorLangVersion` значение `3.0`. `3.0` — это значение по умолчанию для .NET Core 3.x.
* Добавляет следующие ссылки на пакеты:
  * [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components);
  * [Microsoft.AspNetCore.Components.Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web).

Пример:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Поддержка определенной модели размещения

Гораздо менее распространена поддержка одной модели размещения Blazor. Например, для поддержки использования компонентов Razor только из проектов [Blazor Server](xref:blazor/hosting-models#blazor-server) понадобится:

* Целевая версия .NET Core 3.x.
* Добавьте элемент `<FrameworkReference>` для общей платформы.

Пример:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Дополнительные сведения о библиотеках, содержащих компоненты Razor, см. в статье [Библиотеки классов компонентов Razor для ASP.NET Core](xref:blazor/components/class-libraries).

## <a name="include-mvc-extensibility"></a>Включение расширяемости MVC

В этом разделе приведены рекомендации по библиотекам, включая:

* [Представления Razor или Razor Pages](#razor-views-or-razor-pages)
* [Вспомогательные функции тегов](#tag-helpers)
* [Компоненты представлений](#view-components)

В этом разделе не рассматривается настройка различных версий для поддержки нескольких версий MVC. Рекомендации по поддержке нескольких версий ASP.NET Core см. в разделе [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions) (Поддержка нескольких ASP.NET Core версий).

### <a name="razor-views-or-razor-pages"></a>Представления Razor или Razor Pages

Проект, включающий [представления Razor](xref:mvc/views/overview) или [Razor Pages](xref:razor-pages/index), должен использовать [пакет SDK Microsoft.NET.Sdk.Razor ](xref:razor-pages/sdk).

Если проект предназначен для .NET Core 3.x, для него требуется:

* Для свойства MSBuild `AddRazorSupportForMvc` задать значение `true`.
* Элемент `<FrameworkReference>` для общей платформы.

Шаблон проекта **Библиотека классов Razor** должен удовлетворять описанным выше требованиям для проектов, предназначенных для .NET Core 3.x. Для редактора следует использовать следующие инструкции.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Используйте шаблон проекта **библиотеки классов Razor** . Установите флажок **Support pages and views** (Представления и страницы поддержки).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Во встроенном терминале выполните следующую команду:

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

В настоящее время поддержка шаблона проекта отсутствует.

---

Пример:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Если проект предназначен для .NET Standard, требуется ссылка на пакет [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc). Пакет `Microsoft.AspNetCore.Mvc` перемещен в общую платформу ASP.NET Core 3.0 и поэтому больше не публикуется. Пример:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Вспомогательные функции тегов

Проект, содержащий [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) должен использовать пакет SDK для `Microsoft.NET.Sdk`. При использовании .NET Core 3.x добавьте элемент `<FrameworkReference>` для общей платформы. Пример:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Если используется .NET Standard (для поддержки версий, предшествующих ASP.NET Core 3.x), добавьте ссылку на пакет [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor). Пакет `Microsoft.AspNetCore.Mvc.Razor` перемещен в общую платформу и поэтому больше не публикуется. Пример:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Компоненты представлений

Проект, содержащий [вспомогательные функции тегов](xref:mvc/views/view-components), должен использовать пакет SDK для `Microsoft.NET.Sdk`. При использовании .NET Core 3.x добавьте элемент `<FrameworkReference>` для общей платформы. Пример:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Если используется .NET Standard (для поддержки версий, предшествующих ASP.NET Core 3.x), добавьте ссылку на пакет [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures). Пакет `Microsoft.AspNetCore.Mvc.ViewFeatures` перемещен в общую платформу и поэтому больше не публикуется. Пример:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Поддержка нескольких версий ASP.NET Core

Для создания библиотеки, поддерживающей множественные варианты ASP.NET Core, требуется настройка для различных версий. Рассмотрим ситуацию, в которой библиотека вспомогательных функций тегов должна поддерживать следующие варианты ASP.NET Core:

* ASP.NET Core 2.1 для платформы .NET Framework 4.6.1;
* ASP.NET Core 2.x для платформы .NET Framework 2.x;
* ASP.NET Core 3.x для платформы .NET Framework 3.x.

Следующий файл проекта поддерживает эти варианты с помощью свойства `TargetFrameworks`:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

Для предыдущего файла проекта:

* Для всех потребителей добавляется пакет `Markdig`.
* Ссылка на [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) добавляется для пользователей .NET Framework 4.6.1 или более поздней версии или .NET Core 2.x. Версия 2.1.0 пакета работает с ASP.NET Core 2.2 из-за обратной совместимости.
* На общую платформу ссылаются потребители, использующие .NET Core 3.x. Пакет `Microsoft.AspNetCore.Mvc.Razor` включен в общую платформу.

Кроме того, можно использовать .NET Standard 2.0 вместо .NET Core 2.1 и .NET Framework 4.6.1:

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

В предыдущем файле проекта есть следующие предупреждения.

* Так как библиотека содержит только вспомогательные функции тегов, она проще для определенных платформ, на которых выполняется ASP.NET Core: .NET Core и .NET Framework. Вспомогательные функции тегов нельзя использовать с другими совместимыми с .NET Standard 2.0 целевыми платформами, такими как Unity, UWP и Xamarin.
* Некоторые проблемы с использованием .NET Standard 2.0 в .NET Framework были устраненные в .NET Framework версии 4.7.2. Улучшить работу потребителей, использующих .NET Framework 4.6.1–4.7.1, можно, используя .NET Framework 4.6.1.

Если библиотека должна вызывать интерфейсы API для определенных платформ, вместо .NET Standard используйте конкретные реализации .NET. Дополнительные сведения см. в разделе [Настройка для различных версий](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).

## <a name="use-an-api-that-hasnt-changed"></a>Использование API, который еще не изменился

Представьте, что вы обновляете библиотеку ПО промежуточного слоя с .NET Core версии 2.2–3.0. API-интерфейсы ASP.NET Core ПО промежуточного слоя, используемые в библиотеке, не изменились в версиях ASP.NET Core 2.2 и 3.0. Чтобы продолжить поддержку библиотеки ПО промежуточного слоя в .NET Core 3.0, выполните следующие действия.

* Следуйте указаниям в руководстве по работе со [стандартной библиотекой](/dotnet/standard/library-guidance/).
* Добавьте ссылку на пакет для каждого пакета API-интерфейса NuGet, если соответствующая сборка отсутствует в общей платформе.

## <a name="use-an-api-that-changed"></a>Использование измененного API

Представьте, что вы обновляете библиотеку с .NET Core версии 2.2 до 3.0. API-интерфейс ASP.NET Core, используемый в библиотеке, имеет [критические изменения](/dotnet/core/compatibility/breaking-changes) в ASP.NET Core 3.0. Определите, можно ли перезаписывать библиотеку, чтобы не использовать неработающий API во всех версиях.

Если вы можете переписать библиотеку, сделайте это и продолжайте использовать более раннюю целевую платформу (например, .NET Standard 2.0 или .NET Framework 4.6.1) со ссылками на пакеты.

Если вы не можете переписать библиотеку, выполните следующие действия.

* Добавьте целевой объект для .NET Core 3.0.
* Добавьте элемент `<FrameworkReference>` для общей платформы.
* Используйте [директиву препроцессора #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) с соответствующим символом целевой платформы для условной компиляции кода.

Например, синхронные операции чтения и записи по потокам HTTP-запросов и ответов по умолчанию отключены по ASP.NET Core 3.0. По умолчанию ASP.NET Core 2.2 поддерживает синхронное поведение. Рассмотрим библиотеку ПО промежуточного слоя, в которой должны быть включены синхронные операции чтения и записи, когда происходит операция ввода-вывода. Библиотека должна содержать код для включения синхронных функций в соответствующей директиве препроцессора. Пример:

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>Использование API, представленного в версии 3.0

Представьте, что вы хотите использовать API-интерфейс ASP.NET Core, который появился в ASP.NET Core 3.0. Ответьте на следующие вопросы.

1. Работает ли в библиотеке новый API?
1. Может ли библиотека реализовать эту функцию другим способом?

Если для работы библиотеки требуется API и нет способа реализовать его на уровне ниже:

* Используйте только .NET Core 3.x.
* Добавьте элемент `<FrameworkReference>` для общей платформы.

Если библиотека может реализовать эту функцию другим способом:

* Добавьте .NET Core 3.x в качестве целевой платформы.
* Добавьте элемент `<FrameworkReference>` для общей платформы.
* Используйте [директиву препроцессора #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) с соответствующим символом целевой платформы для условной компиляции кода.

Например, следующая вспомогательная функция тега использует интерфейс <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>, представленный в ASP.NET Core 3.0. Потребители, использующие .NET Core 3.0, выполняют путь кода, определенный символом `NETCOREAPP3_0` целевой платформы. Тип параметра конструктора вспомогательной функции тега изменяется на <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> для потребителей .NET Core 2.1 и .NET Framework 4.6.1. Это изменение было необходимо, так как в ASP.NET Core 3.0 свойство `IHostingEnvironment` помечено как устаревшее и в качестве замены рекомендуется использовать `IWebHostEnvironment`.

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

Следующий файл проекта с несколькими целевыми файлами поддерживает этот сценарий вспомогательной функции тега:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>Использование API, удаленного из общей платформы

Чтобы использовать сборку ASP.NET Core, которая была удалена из общей платформы, добавьте соответствующую ссылку на пакет. Список пакетов, удаленных из общей платформы в ASP.NET Core 3.0, см. в разделе [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references) (Удаление устаревших ссылок на пакеты).

Например, чтобы добавить клиент веб-API:

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [Поддержка реализации .NET](/dotnet/standard/net-standard#net-implementation-support)
* [Политики поддержки .NET](https://dotnet.microsoft.com/platform/support/policy)
