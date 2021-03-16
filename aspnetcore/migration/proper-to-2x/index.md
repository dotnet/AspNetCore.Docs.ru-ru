---
title: Миграция с ASP.NET на ASP.NET Core
author: isaac2004
description: Здесь вы найдете указания по миграции существующих приложений MVC ASP.NET или веб-API на ASP.NET Core.web
ms.author: scaddie
ms.date: 10/18/2019
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
uid: migration/proper-to-2x/index
ms.openlocfilehash: 7961890becc8f4513e0750f28341c9d4cf94e7ad
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413342"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="a1abc-103">Миграция с ASP.NET на ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1abc-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="a1abc-104">Автор [Айзек Левин](https://isaaclevin.com) (Isaac Levin)</span><span class="sxs-lookup"><span data-stu-id="a1abc-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="a1abc-105">Данная статья служит руководством по миграции приложений ASP.NET на ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1abc-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span> <span data-ttu-id="a1abc-106">Полное руководство по переносу см. в книге [Перенос существующих приложений ASP.NET в .NET Core](https://aka.ms/aspnet-porting-ebook) .</span><span class="sxs-lookup"><span data-stu-id="a1abc-106">See the ebook [Porting existing ASP.NET apps to .NET Core](https://aka.ms/aspnet-porting-ebook) for a comprehensive porting guide.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a1abc-107">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="a1abc-107">Prerequisites</span></span>

[<span data-ttu-id="a1abc-108">Пакет SDK для .NET Core 2.2 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="a1abc-108">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a><span data-ttu-id="a1abc-109">Требуемые версии .NET Framework</span><span class="sxs-lookup"><span data-stu-id="a1abc-109">Target frameworks</span></span>

<span data-ttu-id="a1abc-110">Проекты ASP.NET Core предлагают разработчикам гибкость работы с .NET Core и .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a1abc-110">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="a1abc-111">Определить наиболее подходящую платформу поможет статья [Выбор между .NET Core и .NET Framework для серверных приложений](/dotnet/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="a1abc-111">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="a1abc-112">При разработке для .NET Framework проекты должны ссылаться на отдельные пакеты NuGet.</span><span class="sxs-lookup"><span data-stu-id="a1abc-112">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="a1abc-113">Работа с .NET Core позволяет избавиться от многочисленных явных ссылок на пакеты благодаря [метапакету](xref:fundamentals/metapackage-app) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1abc-113">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="a1abc-114">Установите метапакет `Microsoft.AspNetCore.App` в свой проект.</span><span class="sxs-lookup"><span data-stu-id="a1abc-114">Install the `Microsoft.AspNetCore.App` metapackage in your project:</span></span>

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

<span data-ttu-id="a1abc-115">При использовании метапакета никакие указанные по ссылкам пакеты с приложением не развертываются.</span><span class="sxs-lookup"><span data-stu-id="a1abc-115">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="a1abc-116">Все эти ресурсы входят в хранилище среды выполнения .NET Core и предварительно компилируются для повышения производительности.</span><span class="sxs-lookup"><span data-stu-id="a1abc-116">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="a1abc-117">Дополнительные сведения см. в статье [Метапакет Microsoft.AspNetCore.App для ASP.NET Core 2.1](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a1abc-117">See [Microsoft.AspNetCore.App metapackage for ASP.NET Core](xref:fundamentals/metapackage-app) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="a1abc-118">Различия в структуре пакетов</span><span class="sxs-lookup"><span data-stu-id="a1abc-118">Project structure differences</span></span>

<span data-ttu-id="a1abc-119">В ASP.NET Core упрощен формат файла *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="a1abc-119">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="a1abc-120">Вот некоторые основные изменения.</span><span class="sxs-lookup"><span data-stu-id="a1abc-120">Some notable changes include:</span></span>

- <span data-ttu-id="a1abc-121">Чтобы файлы считались частью проекта, включать их явно теперь не требуется.</span><span class="sxs-lookup"><span data-stu-id="a1abc-121">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="a1abc-122">Это уменьшает вероятность конфликтов слияния XML при работе в больших командах.</span><span class="sxs-lookup"><span data-stu-id="a1abc-122">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="a1abc-123">GUID-ссылки на другие проекты не используются, что повышает удобочитаемость файла.</span><span class="sxs-lookup"><span data-stu-id="a1abc-123">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="a1abc-124">Файл можно редактировать, не выгружая его в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a1abc-124">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Параметр изменения файла CSPROJ в контекстном меню в Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="a1abc-126">Замена файла Global.asax</span><span class="sxs-lookup"><span data-stu-id="a1abc-126">Global.asax file replacement</span></span>

<span data-ttu-id="a1abc-127">В ASP.NET Core появился новый механизм для начальной загрузки приложения.</span><span class="sxs-lookup"><span data-stu-id="a1abc-127">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="a1abc-128">Точкой входа для приложений ASP.NET стал файл *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="a1abc-128">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="a1abc-129">Такие задачи, как конфигурация маршрута, а также регистрации фильтров и областей, теперь выполняются в файле *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="a1abc-129">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="a1abc-130">При этом подходе приложение сопоставляется с сервером, на котором оно развертывается, таким образом, чтобы это не мешало реализации.</span><span class="sxs-lookup"><span data-stu-id="a1abc-130">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="a1abc-131">Чтобы отделить приложение от сервера, был введен [OWIN](https://owin.org/), который обеспечивает более точный способ совместного использования нескольких платформ.</span><span class="sxs-lookup"><span data-stu-id="a1abc-131">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="a1abc-132">OWIN предоставляет конвейер для добавления только необходимых модулей.</span><span class="sxs-lookup"><span data-stu-id="a1abc-132">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="a1abc-133">Среда внешнего размещения принимает функцию [Startup](xref:fundamentals/startup) для настройки служб и конвейера обработки запросов приложения.</span><span class="sxs-lookup"><span data-stu-id="a1abc-133">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="a1abc-134">`Startup` регистрирует набор ПО промежуточного слоя в приложении.</span><span class="sxs-lookup"><span data-stu-id="a1abc-134">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="a1abc-135">Для каждого запроса приложение вызывает каждый из компонентов ПО промежуточного слоя по заглавному указателю связанного списка на существующий набор обработчиков.</span><span class="sxs-lookup"><span data-stu-id="a1abc-135">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="a1abc-136">Каждый компонент ПО промежуточного слоя может добавлять в конвейер обработки запросов один обработчик или несколько.</span><span class="sxs-lookup"><span data-stu-id="a1abc-136">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="a1abc-137">Это достигается путем возвращения ссылки на обработчик, который представляет собой новый заголовок списка.</span><span class="sxs-lookup"><span data-stu-id="a1abc-137">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="a1abc-138">Каждый обработчик отвечает за запоминание и вызов следующего обработчика в списке.</span><span class="sxs-lookup"><span data-stu-id="a1abc-138">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="a1abc-139">При работе с ASP.NET Core точкой входа в приложения становится `Startup`, и зависимость от файла *Global.asax* исчезает.</span><span class="sxs-lookup"><span data-stu-id="a1abc-139">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="a1abc-140">Используя OWIN с .NET Framework, применяйте для конвейера код следующего вида:</span><span class="sxs-lookup"><span data-stu-id="a1abc-140">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="a1abc-141">Он определяет ваши маршруты по умолчанию и изначально предусматривает XmlSerialization по Json.</span><span class="sxs-lookup"><span data-stu-id="a1abc-141">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="a1abc-142">При необходимости добавьте другое ПО промежуточного слоя для этого конвейера (загрузка служб, параметры конфигурации, статические файлы и т. д.).</span><span class="sxs-lookup"><span data-stu-id="a1abc-142">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="a1abc-143">ASP.NET Core использует аналогичный подход, но не требует OWIN для обработки запроса.</span><span class="sxs-lookup"><span data-stu-id="a1abc-143">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="a1abc-144">Вместо этого применяется метод *Program.cs* `Main` (как в консольных приложениях) и `Startup` загружается через него.</span><span class="sxs-lookup"><span data-stu-id="a1abc-144">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="a1abc-145">`Startup` должен включать метод `Configure`.</span><span class="sxs-lookup"><span data-stu-id="a1abc-145">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="a1abc-146">В `Configure` добавьте в конвейер необходимое ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="a1abc-146">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="a1abc-147">В примере ниже (на основе шаблона веб-сайта по умолчанию) используются методы расширения для настройки конвейера с поддержкой следующих компонентов:</span><span class="sxs-lookup"><span data-stu-id="a1abc-147">In the following example (from the default web site template), extension methods configure the pipeline with support for:</span></span>

- <span data-ttu-id="a1abc-148">Страницы ошибок</span><span class="sxs-lookup"><span data-stu-id="a1abc-148">Error pages</span></span>
- <span data-ttu-id="a1abc-149">HTTP Strict Transport Security;</span><span class="sxs-lookup"><span data-stu-id="a1abc-149">HTTP Strict Transport Security</span></span>
- <span data-ttu-id="a1abc-150">перенаправление с HTTP на HTTPS;</span><span class="sxs-lookup"><span data-stu-id="a1abc-150">HTTP redirection to HTTPS</span></span>
- <span data-ttu-id="a1abc-151">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="a1abc-151">ASP.NET Core MVC</span></span>

[!code-csharp[](samples/startup.cs)]

<span data-ttu-id="a1abc-152">Сервер и приложение разделены, что позволит вам в будущем легко перейти на другую платформу.</span><span class="sxs-lookup"><span data-stu-id="a1abc-152">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="a1abc-153">Более подробный справочник по запуску ASP.NET Core и ПО промежуточного слоя см. в статье [Запуск в ASP.NET Core](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="a1abc-153">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="a1abc-154">Конфигурации хранения</span><span class="sxs-lookup"><span data-stu-id="a1abc-154">Store configurations</span></span>

<span data-ttu-id="a1abc-155">ASP.NET поддерживает параметры хранения.</span><span class="sxs-lookup"><span data-stu-id="a1abc-155">ASP.NET supports storing settings.</span></span> <span data-ttu-id="a1abc-156">Эти параметры используются, например, для поддержки среды, в которой развертываются приложения.</span><span class="sxs-lookup"><span data-stu-id="a1abc-156">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="a1abc-157">Как правило, все пользовательские пары ключей и значений хранятся в разделе `<appSettings>` файла *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="a1abc-157">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="a1abc-158">Приложения считывают эти параметры с помощью коллекции `ConfigurationManager.AppSettings` в пространстве имен `System.Configuration`:</span><span class="sxs-lookup"><span data-stu-id="a1abc-158">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="a1abc-159">ASP.NET Core может сохранять данные конфигурации для приложения из любого файла и загружать их в процессе начальной загрузки ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="a1abc-159">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="a1abc-160">По умолчанию в шаблонах проектов используется файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="a1abc-160">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="a1abc-161">Загрузка этого файла в экземпляр `IConfiguration` в вашем приложении выполняется в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a1abc-161">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="a1abc-162">Для получения этих параметров приложение считывает данные из `Configuration`:</span><span class="sxs-lookup"><span data-stu-id="a1abc-162">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="a1abc-163">Существуют расширения, повышающие надежность этого подхода, например, [внедрение зависимостей](xref:fundamentals/dependency-injection) позволяет загружать эти значения в службу.</span><span class="sxs-lookup"><span data-stu-id="a1abc-163">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="a1abc-164">Метод внедрения зависимостей предоставляет строго типизированный набор объектов конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a1abc-164">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> <span data-ttu-id="a1abc-165">Более подробное руководство по конфигурации ASP.NET Core см. в статье [Конфигурация в ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="a1abc-165">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="a1abc-166">Собственные функции внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="a1abc-166">Native dependency injection</span></span>

<span data-ttu-id="a1abc-167">При сборке больших, масштабируемых приложений важно обеспечить слабые взаимозависимости между компонентами и службами.</span><span class="sxs-lookup"><span data-stu-id="a1abc-167">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="a1abc-168">[Внедрение зависимостей](xref:fundamentals/dependency-injection) — популярный способ решения этой задачи и собственный компонент ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1abc-168">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="a1abc-169">В приложениях ASP.NET разработчики используют для внедрения зависимостей стороннюю библиотеку.</span><span class="sxs-lookup"><span data-stu-id="a1abc-169">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="a1abc-170">Одна из таких библиотек, [Unity](https://github.com/unitycontainer/unity), входит в шаблоны и рекомендации Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="a1abc-170">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="a1abc-171">Пример внедрения зависимостей с использованием библиотеки Unity — это реализация `IDependencyResolver` как оболочки для `UnityContainer`:</span><span class="sxs-lookup"><span data-stu-id="a1abc-171">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="a1abc-172">Создайте экземпляр `UnityContainer`, зарегистрируйте свою службу и установите средство разрешения зависимостей `HttpConfiguration` в новый экземпляр `UnityResolver` для вашего контейнера:</span><span class="sxs-lookup"><span data-stu-id="a1abc-172">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="a1abc-173">Там, где необходимо, вставьте `IProductRepository`:</span><span class="sxs-lookup"><span data-stu-id="a1abc-173">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="a1abc-174">Так как внедрение зависимостей является частью ASP.NET Core, вы можете добавить свою службу в метод `ConfigureServices` файла *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a1abc-174">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="a1abc-175">Репозиторий, как и в Unity, можно внедрять где угодно.</span><span class="sxs-lookup"><span data-stu-id="a1abc-175">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="a1abc-176">Дополнительные сведения о внедрении зависимостей см. [здесь](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a1abc-176">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="a1abc-177">Обслуживание статических файлов</span><span class="sxs-lookup"><span data-stu-id="a1abc-177">Serve static files</span></span>

<span data-ttu-id="a1abc-178">Важной частью разработки веб-приложений является возможность обслуживания статических ресурсов на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="a1abc-178">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="a1abc-179">Наиболее распространенные примеры статических файлов — это HTML, каскадные таблицы стилей, Javascript и изображения.</span><span class="sxs-lookup"><span data-stu-id="a1abc-179">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="a1abc-180">Эти файлы необходимо сохранять в место публикации приложения (или CDN) и указывать по ссылкам, чтобы они могли загружаться по запросу.</span><span class="sxs-lookup"><span data-stu-id="a1abc-180">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="a1abc-181">В ASP.NET Core ситуация изменилась.</span><span class="sxs-lookup"><span data-stu-id="a1abc-181">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="a1abc-182">В ASP.NET статические файлы хранятся в разных папках со ссылками в представлениях.</span><span class="sxs-lookup"><span data-stu-id="a1abc-182">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="a1abc-183">В ASP.NET Core, если не заданы другие настройки, статические файлы хранятся на корневом веб-узле ( *&lt;содержимое корневой папки&gt;/wwwroot*).</span><span class="sxs-lookup"><span data-stu-id="a1abc-183">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="a1abc-184">Файлы загружаются в конвейер запросов путем вызова метода расширения `UseStaticFiles` из `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a1abc-184">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="a1abc-185">Для работы с .NET Framework установите пакет NuGet `Microsoft.AspNetCore.StaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="a1abc-185">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="a1abc-186">Например, ресурс изображения в папке *wwwroot/images* доступен для браузера в расположении `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="a1abc-186">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="a1abc-187">Более подробное руководство по обработке статических файлов в ASP.NET Core см. в статье [Статические файлы](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="a1abc-187">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="multi-value-cookies"></a><span data-ttu-id="a1abc-188">Файлы cookie с несколькими значениями</span><span class="sxs-lookup"><span data-stu-id="a1abc-188">Multi-value cookies</span></span>

<span data-ttu-id="a1abc-189">[Файлы cookie с несколькими значениями](xref:System.Web.HttpCookie.Values) не поддерживаются в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1abc-189">[Multi-value cookies](xref:System.Web.HttpCookie.Values) aren't supported in ASP.NET Core.</span></span> <span data-ttu-id="a1abc-190">Создайте один файл cookie для каждого значения.</span><span class="sxs-lookup"><span data-stu-id="a1abc-190">Create one cookie per value.</span></span>

## <a name="authentication-cookies-are-not-compressed-in-aspnet-core"></a><span data-ttu-id="a1abc-191">Файлы cookie проверки подлинности не сжимаются в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1abc-191">Authentication cookies are not compressed in ASP.NET Core</span></span>

[!INCLUDE[](~/includes/cookies-not-compressed.md)]

## <a name="partial-app-migration"></a><span data-ttu-id="a1abc-192">Частичная миграция приложений</span><span class="sxs-lookup"><span data-stu-id="a1abc-192">Partial app migration</span></span>

<span data-ttu-id="a1abc-193">Один из способов выполнить частичную миграцию приложений предусматривает создание вспомогательного приложения IIS и перенос из ASP.NET 4.x в ASP.NET Core только некоторых маршрутов с сохранением структуры URL-адресов приложения.</span><span class="sxs-lookup"><span data-stu-id="a1abc-193">One approach to partial app migration is to create an IIS sub-application and only move certain routes from ASP.NET 4.x to ASP.NET Core while preserving the URL structure the app.</span></span> <span data-ttu-id="a1abc-194">Рассмотрим структуру URL-адресов приложения из файла *applicationHost.config*:</span><span class="sxs-lookup"><span data-stu-id="a1abc-194">For example, consider the URL structure of the app from the *applicationHost.config* file:</span></span>

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

<span data-ttu-id="a1abc-195">Структура каталогов:</span><span class="sxs-lookup"><span data-stu-id="a1abc-195">Directory structure:</span></span>

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="bind-and-input-formatters"></a><span data-ttu-id="a1abc-196">[BIND] и форматировщики входных данных</span><span class="sxs-lookup"><span data-stu-id="a1abc-196">[BIND] and Input Formatters</span></span>

<span data-ttu-id="a1abc-197">В [предыдущих версиях ASP.NET](/aspnet/mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view) использовался атрибут `[Bind]` для защиты от атак чрезмерной передачи данных.</span><span class="sxs-lookup"><span data-stu-id="a1abc-197">[Previous versions of ASP.NET](/aspnet/mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view) used the `[Bind]` attribute to protect against overposting attacks.</span></span> <span data-ttu-id="a1abc-198">[Форматировщики входных данных](xref:mvc/models/model-binding#input-formatters) работают по-разному в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1abc-198">[Input formatters](xref:mvc/models/model-binding#input-formatters) work differently in ASP.NET Core.</span></span> <span data-ttu-id="a1abc-199">Атрибут `[Bind]` больше не предназначен для предотвращения чрезмерной передачи данных при использовании с форматировщиками входных данных для анализа JSON или XML.</span><span class="sxs-lookup"><span data-stu-id="a1abc-199">The `[Bind]` attribute is no longer designed to prevent overposting when used with input formatters to parse JSON or XML.</span></span> <span data-ttu-id="a1abc-200">Эти атрибуты влияют на привязку модели, когда источником данных являются данные формы, опубликованные с типом содержимого `x-www-form-urlencoded`.</span><span class="sxs-lookup"><span data-stu-id="a1abc-200">These attributes affect model binding when the source of data is form data posted with the `x-www-form-urlencoded` content type.</span></span>

<span data-ttu-id="a1abc-201">Для приложений, которые передают данные JSON в контроллеры и используют форматировщики входных данных JSON для анализа данных, рекомендуется заменить атрибут `[Bind]` моделью представления, которая соответствует свойствам, определенным атрибутом `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="a1abc-201">For apps that post JSON information to controllers and use JSON Input Formatters to parse the data, we recommend replacing the `[Bind]` attribute with a view model that matches the properties defined by the `[Bind]` attribute.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a1abc-202">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a1abc-202">Additional resources</span></span>

- [<span data-ttu-id="a1abc-203">Перенос библиотек в .NET Core</span><span class="sxs-lookup"><span data-stu-id="a1abc-203">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
