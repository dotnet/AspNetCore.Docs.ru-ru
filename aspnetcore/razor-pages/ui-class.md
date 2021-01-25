---
title: Многоразовый интерфейс Razor в библиотеках классов в ASP.NET Core
author: Rick-Anderson
description: Описание способов создания многоразового пользовательского интерфейса Razor с помощью частичных представлений в библиотеке классов на платформе ASP.NET Core.
ms.author: riande
ms.date: 01/19/2021
ms.custom: mvc, seodec18
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
uid: razor-pages/ui-class
ms.openlocfilehash: a878a3485ecee0782b21ac69c5ec6ff832b9f06c
ms.sourcegitcommit: cb984e0d7dc23a88c3a4121f23acfaea0acbfe1e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571018"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a>Создание многоразового пользовательского интерфейса с помощью проекта библиотеки классов Razor в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

::: moniker range=">= aspnetcore-3.0"

В библиотеку классов Razor (RCL) можно встроить представления Razor, страницы, контроллеры, модели страниц, [компоненты Razor](xref:blazor/components/class-libraries), [компоненты представления](xref:mvc/views/view-components). RCL можно упаковать и использовать повторно. Приложения могут включать RCL и переопределять содержащиеся в нем представления и страницы. При обнаружении представления, частичного представления или страницы Razor и в веб-приложении, и в RCL приоритет имеет разметка Razor (файл *.cshtml*) в веб-приложении.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-no-locrazor-ui"></a>Создание библиотеки классов с пользовательским интерфейсом Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В Visual Studio выберите **Создать проект**.
* Выберите **Библиотека классов Razor** > **Далее**.
* Назовите библиотеку (например, "RazorClassLib") и нажмите кнопку **Создать**. Чтобы избежать конфликта имени файла с созданной библиотекой представлений, проверьте, что имя библиотеки не заканчивается на `.Views`.
* Если должны поддерживаться представления, установите флажок **Представления и страницы поддержки**. По умолчанию поддерживаются только страницы Razor Pages. Выберите **Создать**.

По умолчанию для шаблона библиотеки классов Razor (RCL) используется разработка компонентов Razor. При выборе параметра **Представления и страницы поддержки** поддерживаются страницы и представления.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Выполните из командной строки команду `dotnet new razorclasslib`. Пример:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

По умолчанию для шаблона библиотеки классов Razor (RCL) используется разработка компонентов Razor. Передайте параметр `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`) для включения поддержки страниц и представлений.

Дополнительные сведения см. в разделе [dotnet new](/dotnet/core/tools/dotnet-new). Чтобы избежать конфликта имени файла с созданной библиотекой представлений, проверьте, что имя библиотеки не заканчивается на `.Views`.

---

Добавьте файлы Razor в библиотеку RCL.

В шаблонах ASP.NET Core предполагается, что содержимое RCL находится в папке *Areas*. Сведения о том, как создать библиотеку RCL с содержимым в папке `~/Pages`, а не `~/Areas/Pages`, см. в разделе [Макет страниц RCL](#rcl-pages-layout).

## <a name="reference-rcl-content"></a>Ссылка на содержимое RCL

На RCL могут ссылаться:

* Пакет NuGet. См. [Создание пакетов NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) и [Создание и публикация пакета NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*. См. [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Переопределение представлений, частичных представлений и страниц

При обнаружении представления, частичного представления или страницы Razor и в веб-приложении, и в RCL приоритет имеет разметка Razor (файл *.cshtml*) в веб-приложении. Например, если вы добавите *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* в WebApp1, Page1 в WebApp1 будет иметь приоритет над Page1 в RCL.

В примере загрузки переименуйте *WebApp1/Areas/MyFeature2* в *WebApp1/Areas/MyFeature*, чтобы протестировать приоритет.

Скопируйте частичное представление *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* в *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Обновите разметку, чтобы указать новое расположение. Скомпилируйте и запустите приложение, чтобы убедиться, что используется версия частичного представления из приложения.

### <a name="rcl-pages-layout"></a>Макет страниц RCL

Чтобы ссылаться на содержимое RCL так, как будто оно находится в папке *Pages* веб-приложения, создайте проект RCL со следующей структурой файлов:

* *RazorUIClassLib/Pages*
* *RazorUIClassLib/Pages/Shared*

Предположим, что папка *RazorUIClassLib/Pages/Shared* содержит два частичных файла: *_Header.cshtml* и *_Footer.cshtml*. В файл *_Layout.cshtml* можно добавить теги `<partial>`:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

Добавьте файл *_ViewStart.cshtml* в папку *Pages* проекта библиотеки RCL, чтобы использовать файл *_Layout.cshtml* из веб-приложения узла:

```cshtml
@{
    Layout = "_Layout";
}
```

## <a name="create-an-rcl-with-static-assets"></a>Создание библиотеки RCL со статическими ресурсами

Для библиотеки RCL могут потребоваться сопутствующие статические ресурсы, на которые может ссылаться либо сама библиотека RCL, либо использующее ее приложение. ASP.NET Core позволяет создавать библиотеки RCL со статическими ресурсами, которые доступны использующим библиотеки приложениям.

Чтобы включить сопутствующие ресурсы в библиотеку RCL, создайте папку *wwwroot* в библиотеке классов и добавьте в нее все необходимые файлы.

При упаковке RCL все сопутствующие ресурсы в папке *wwwroot* автоматически включаются в пакет.

Используйте команду `dotnet pack`, а не NuGet.exe версии `nuget pack`.

### <a name="exclude-static-assets"></a>Исключение статических ресурсов

Чтобы исключить статические ресурсы, добавьте путь исключения в группу свойств `$(DefaultItemExcludes)` в файле проекта. Записи следует разделять точкой с запятой (`;`).

В следующем примере таблица стилей *lib.css* в папке *wwwroot* не считается статическим ресурсом и не включается в опубликованную библиотеку RCL:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Интеграция с TypeScript

Чтобы включить файлы TypeScript в библиотеку RCL, выполните указанные ниже действия.

1. Поместите файлы TypeScript (*TS*) в папку, отличную от *wwwroot*. Например, их можно поместить в папку *Client*.

1. Укажите папку *wwwroot* в качестве выходного пути сборки TypeScript. Задайте свойство `TypescriptOutDir` в группе `PropertyGroup` в файле проекта:

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Включите целевой объект TypeScript в качестве зависимости целевого объекта `ResolveCurrentProjectStaticWebAssets`, добавив следующий целевой объект в группу `PropertyGroup` в файле проекта:

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Использование содержимого из связанной библиотеки RCL

Файлы в папке *wwwroot* библиотеки RCL доступны самой библиотеке RCL или использующему ее приложению по пути `_content/{LIBRARY NAME}/`. Например, для библиотеки с именем *Razor.Class.Lib* путь к статическому содержимому имеет вид `_content/Razor.Class.Lib/`. Если вы создаете пакет NuGet и имя сборки не совпадает с идентификатором пакета, используйте идентификатор пакета в качестве `{LIBRARY NAME}`.

Использующее библиотеку приложение ссылается на предоставляемые ею статические ресурсы с помощью тегов HTML `<script>`, `<style>`, `<img>` и других. Для приложения необходимо включить [поддержку статических файлов](xref:fundamentals/static-files) в `Startup.Configure`:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Когда приложение запускается из выходного пути сборки (`dotnet run`), в среде разработки статические веб-ресурсы включены по умолчанию. Для поддержки ресурсов в других средах при запуске из выходного пути сборки вызовите метод `UseStaticWebAssets` построителя узла в файле *Program.cs*:

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

При запуске приложения из пути публикации (`dotnet publish`) вызывать `UseStaticWebAssets` не нужно.

### <a name="multi-project-development-flow"></a>Процесс разработки нескольких проектов

При запуске использующего библиотеку приложения происходит следующее:

* Ресурсы RCL остаются в исходных папках. Они не перемещаются в приложение.
* Любые изменения в папке *wwwroot* библиотеки RCL отражаются в приложении после перестроения RCL, причем приложение перестраивать не требуется.

При сборке библиотеки RCL создается манифест, в котором описывается расположение статических веб-ресурсов. Использующее библиотеку приложение считывает манифест во время выполнения для получения ресурсов из связанных проектов и пакетов. При добавлении нового ресурса в библиотеку RCL ее необходимо перестроить, чтобы обновить манифест. Только после этого приложение сможет получить доступ к новому ресурсу.

### <a name="publish"></a>Публикация

При публикации приложения сопутствующие ресурсы из всех связанных проектов и пакетов копируются в папку *wwwroot* опубликованного приложения в папке `_content/{LIBRARY NAME}/`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В библиотеку классов Razor (RCL) можно встроить представления Razor, страницы, контроллеры, модели страниц, [компоненты Razor](xref:blazor/components/class-libraries), [компоненты представления](xref:mvc/views/view-components). RCL можно упаковать и использовать повторно. Приложения могут включать RCL и переопределять содержащиеся в нем представления и страницы. При обнаружении представления, частичного представления или страницы Razor и в веб-приложении, и в RCL приоритет имеет разметка Razor (файл *.cshtml*) в веб-приложении.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-no-locrazor-ui"></a>Создание библиотеки классов с пользовательским интерфейсом Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.
* Выберите **Новое веб-приложение ASP.NET Core**.
* Назовите библиотеку (например, "RazorClassLib") и нажмите кнопку **ОК**. Чтобы избежать конфликта имени файла с созданной библиотекой представлений, проверьте, что имя библиотеки не заканчивается на `.Views`.
* Убедитесь, что выбрано **ASP.NET Core 2.1** или более поздней версии.
* Выберите **Библиотека классов Razor** > **ОК**.

Библиотека RCL имеет следующий файл проекта:

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Выполните из командной строки команду `dotnet new razorclasslib`. Пример:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Дополнительные сведения см. в разделе [dotnet new](/dotnet/core/tools/dotnet-new). Чтобы избежать конфликта имени файла с созданной библиотекой представлений, проверьте, что имя библиотеки не заканчивается на `.Views`.

---

Добавьте файлы Razor в библиотеку RCL.

В шаблонах ASP.NET Core предполагается, что содержимое RCL находится в папке *Areas*. Сведения о том, как создать библиотеку RCL с содержимым в папке `~/Pages`, а не `~/Areas/Pages`, см. в разделе [Макет страниц RCL](#rcl-pages-layout).

## <a name="reference-rcl-content"></a>Ссылка на содержимое RCL

На RCL могут ссылаться:

* Пакет NuGet. См. [Создание пакетов NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) и [Создание и публикация пакета NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*. См. [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a>Пошаговое руководство. Создание проекта RCL и его использование в проекте Razor Pages

Вы можете не создавать, а загрузить [целый проект](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) и протестировать его. Образец загрузки содержит дополнительный код и ссылки, что упрощает тестирование проекта. Оставьте свой комментарий о сравнении образцов загрузки с пошаговыми инструкциями в [этой проблеме GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6098).

### <a name="test-the-download-app"></a>Тестирование приложения загрузки

Если вы еще не загрузили завершенное приложение и вместо этого хотите создать проект пошагового руководства, перейдите к [следующему разделу](#create-an-rcl).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Откройте *SLN*-файл в Visual Studio. Запустите приложение.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

В командной строке в каталоге *cli* создайте RCL и веб-приложение.

```dotnetcli
dotnet build
```

Перейдите в каталог *WebApp1* и запустите приложение:

```dotnetcli
dotnet run
```

---

Следуйте инструкциям в разделе [Тестирование WebApp1](#test-webapp1)

## <a name="create-an-rcl"></a>Создание библиотеки RCL

В этом разделе создается библиотека RCL. Файлы Razor будут добавлены в RCL.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Создание проекта RCL:

* В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.
* Выберите **Новое веб-приложение ASP.NET Core**.
* Присвойте приложению имя **RazorUIClassLib** > **ОК**.
* Убедитесь, что выбрано **ASP.NET Core 2.1** или более поздней версии.
* Выберите **Библиотека классов Razor** > **ОК**.
* Добавьте файл частичного представления Razor с именем *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Выполните следующую команду в командной строке:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Предыдущие команды:

* Создают библиотеку RCL `RazorUIClassLib`.
* Создает страницу Razor _Message и добавляет ее в RCL. Параметр `-np` создает страницу без `PageModel`.
* Создают файл [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) и добавляют его в RCL.

Файл *_ViewStart.cshtml* требуется для использования макета проекта Razor Pages (который добавляется в следующем разделе).

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a>Добавление файлов и папок Razor в проект

* Замените разметку в *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* следующим кодом:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Замените разметку в *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* следующим кодом:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` требуется для использования частичного представления (`<partial name="_Message" />`). Вместо включения директивы `@addTagHelper` можно добавить файл *_ViewImports.cshtml*. Пример:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  Дополнительные сведения о файле *_ViewImports.cshtml* см. в разделе [Импорт общих директив](xref:mvc/views/layout#importing-shared-directives).

* Создайте библиотеку классов, чтобы убедиться в отсутствии ошибок компилятора:

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

Выходные данные сборки содержат библиотеки *RazorUIClassLib.dll* и *RazorUIClassLib.Views.dll*. В библиотеке *RazorUIClassLib.Views.dll* находится скомпилированное содержимое Razor.

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a>Использование библиотеки пользовательского интерфейса Razor в проекте Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Создание веб-приложения Razor Pages:

* В **обозревателе решений** щелкните решение правой кнопкой мыши и выберите **Добавить** > **Новый проект**.
* Выберите **Новое веб-приложение ASP.NET Core**.
* Назовите приложение **WebApp1**.
* Убедитесь, что выбрано **ASP.NET Core 2.1** или более поздней версии.
* Выберите **Веб-приложение** > **ОК**.

* В **обозревателе решений** щелкните правой кнопкой мыши **WebApp1** и выберите **Назначить запускаемым проектом**.
* В **обозревателе решений** щелкните правой кнопкой мыши **WebApp1** и выберите **Зависимости сборки** > **Зависимости проекта**.
* Отметьте **RazorUIClassLib** как зависимость от **WebApp1**.
* В **обозревателе решений** щелкните правой кнопкой мыши **WebApp1** и выберите **Добавить** > **Ссылка**.
* В диалоговом окне **Диспетчер ссылок** нажмите **RazorUIClassLib** > **ОК**.

Запустите приложение.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Создайте веб-приложение Razor Pages и файл решения, содержащий приложение Razor Pages и библиотеку RCL:

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Выполните сборку и запустите приложения:

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>Тестирование WebApp1

Перейдите по адресу `/MyFeature/Page1`, чтобы убедиться в том, что используется библиотека классов пользовательского интерфейса Razor.

## <a name="override-views-partial-views-and-pages"></a>Переопределение представлений, частичных представлений и страниц

При обнаружении представления, частичного представления или страницы Razor и в веб-приложении, и в RCL приоритет имеет разметка Razor (файл *.cshtml*) в веб-приложении. Например, если вы добавите *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* в WebApp1, Page1 в WebApp1 будет иметь приоритет над Page1 в RCL.

В примере загрузки переименуйте *WebApp1/Areas/MyFeature2* в *WebApp1/Areas/MyFeature*, чтобы протестировать приоритет.

Скопируйте частичное представление *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* в *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Обновите разметку, чтобы указать новое расположение. Скомпилируйте и запустите приложение, чтобы убедиться, что используется версия частичного представления из приложения.

### <a name="rcl-pages-layout"></a>Макет страниц RCL

Чтобы ссылаться на содержимое RCL так, как будто оно находится в папке *Pages* веб-приложения, создайте проект RCL со следующей структурой файлов:

* *RazorUIClassLib/Pages*
* *RazorUIClassLib/Pages/Shared*

Предположим, что папка *RazorUIClassLib/Pages/Shared* содержит два частичных файла: *_Header.cshtml* и *_Footer.cshtml*. В файл *_Layout.cshtml* можно добавить теги `<partial>`:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

::: moniker range=">= aspnetcore-5.0"

* <xref:blazor/components/class-libraries>
* <xref:blazor/components/css-isolation#razor-class-library-rcl-support>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:blazor/components/class-libraries>

::: moniker-end
