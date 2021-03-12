---
title: Новые возможности ASP.NET Core 2.0
author: rick-anderson
description: Дополнительные сведения о новых возможностях ASP.NET Core 2.0.
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
uid: aspnetcore-2.0
ms.openlocfilehash: b7515bcd8b15199770a4245469d00d10da5566f8
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605692"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="a79eb-103">Новые возможности ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="a79eb-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="a79eb-104">В этой статье описываются наиболее важные изменения в ASP.NET Core 2.0 со ссылками на соответствующую документацию.</span><span class="sxs-lookup"><span data-stu-id="a79eb-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="razor-pages"></a><span data-ttu-id="a79eb-105">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a79eb-105">Razor Pages</span></span>

<span data-ttu-id="a79eb-106">Razor Pages — это новая функция платформы MVC ASP.NET Core, которая делает создание кодов сценариев для страниц проще и эффективнее.</span><span class="sxs-lookup"><span data-stu-id="a79eb-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="a79eb-107">Дополнительные сведения см. в следующей вводной статье и учебнике.</span><span class="sxs-lookup"><span data-stu-id="a79eb-107">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="a79eb-108">Введение в Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a79eb-108">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="a79eb-109">Начало работы с Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a79eb-109">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="a79eb-110">Метапакет ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a79eb-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="a79eb-111">Новый метапакет ASP.NET Core включает все пакеты, выпущенные и поддерживаемые командами ASP.NET Core и Entity Framework Core, а также внутренние и сторонние зависимости.</span><span class="sxs-lookup"><span data-stu-id="a79eb-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="a79eb-112">Вам больше не придется выбирать отдельный пакет компонентов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a79eb-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="a79eb-113">Все компоненты входят в пакет [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All).</span><span class="sxs-lookup"><span data-stu-id="a79eb-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="a79eb-114">Шаблоны по умолчанию используют именно этот пакет.</span><span class="sxs-lookup"><span data-stu-id="a79eb-114">The default templates use this package.</span></span>

<span data-ttu-id="a79eb-115">Дополнительные сведения см. в статье [Метапакет Microsoft.AspNetCore.All для ASP.NET Core 2.0](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="a79eb-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="a79eb-116">Хранилище среды выполнения</span><span class="sxs-lookup"><span data-stu-id="a79eb-116">Runtime Store</span></span>

<span data-ttu-id="a79eb-117">Приложения, использующие метапакет `Microsoft.AspNetCore.All`, автоматически получают все преимущества нового хранилища среды выполнения .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a79eb-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="a79eb-118">Хранилище содержит все ресурсы среды выполнения, необходимые для запуска приложений ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="a79eb-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="a79eb-119">При использовании метапакета `Microsoft.AspNetCore.All` приложение не развертывает никакие ресурсы из указанных по ссылке пакетов NuGet ASP.NET Core, так как эти пакеты уже присутствуют в целевой системе.</span><span class="sxs-lookup"><span data-stu-id="a79eb-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="a79eb-120">Кроме того, для сокращения времени запуска приложения ресурсы в хранилище среды выполнения подвергаются предварительной компиляции.</span><span class="sxs-lookup"><span data-stu-id="a79eb-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="a79eb-121">Дополнительные сведения см. в статье [Хранилище среды выполнения](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="a79eb-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="a79eb-122">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="a79eb-122">.NET Standard 2.0</span></span>

<span data-ttu-id="a79eb-123">Пакеты ASP.NET 2.0 предназначены для .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="a79eb-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="a79eb-124">На эти пакеты могут ссылаться другие библиотеки .NET Standard 2.0; кроме того, они могут выполняться в реализациях .NET, совместимых с .NET Standard 2.0, включая .NET Core 2.0 и .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="a79eb-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="a79eb-125">Метапакет `Microsoft.AspNetCore.All` работает только с .NET Core 2.0, так как предназначен для использования с хранилищем среды выполнения .NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="a79eb-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="a79eb-126">Изменения в конфигурации</span><span class="sxs-lookup"><span data-stu-id="a79eb-126">Configuration update</span></span>

<span data-ttu-id="a79eb-127">В ASP.NET Core 2.0 экземпляр `IConfiguration` добавляется в контейнер служб по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a79eb-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="a79eb-128">`IConfiguration` в контейнере служб упрощает для приложений задачу получения значений конфигурации из контейнера.</span><span class="sxs-lookup"><span data-stu-id="a79eb-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="a79eb-129">Сведения о состоянии плановой документации см. в статье о [проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span><span class="sxs-lookup"><span data-stu-id="a79eb-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="a79eb-130">Изменения в ведении журналов</span><span class="sxs-lookup"><span data-stu-id="a79eb-130">Logging update</span></span>

<span data-ttu-id="a79eb-131">В ASP.NET 2.0 Core ведение журнала по умолчанию включено в систему внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a79eb-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="a79eb-132">Добавление поставщиков и настройка фильтрации выполняются в файле *Program.cs*, а не в файле *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="a79eb-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="a79eb-133">А `ILoggerFactory` по умолчанию поддерживает такой способ фильтрации, который позволяет использовать один гибкий подход и для перекрестной фильтрации по поставщикам, и для фильтрации по отдельному поставщику.</span><span class="sxs-lookup"><span data-stu-id="a79eb-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="a79eb-134">Дополнительные сведения см. в статье [Введение в ведение журналов](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a79eb-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="a79eb-135">Изменения в проверке подлинности</span><span class="sxs-lookup"><span data-stu-id="a79eb-135">Authentication update</span></span>

<span data-ttu-id="a79eb-136">Новая модель проверки подлинности облегчает настройку проверки подлинности для приложения с использованием внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a79eb-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="a79eb-137">Доступны новые шаблоны для настройки проверки подлинности в веб-приложениях и веб-API с использованием [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span><span class="sxs-lookup"><span data-stu-id="a79eb-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="a79eb-138">Сведения о состоянии плановой документации см. в статье о [проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span><span class="sxs-lookup"><span data-stu-id="a79eb-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="identity-update"></a><span data-ttu-id="a79eb-139">Обновление Identity</span><span class="sxs-lookup"><span data-stu-id="a79eb-139">Identity update</span></span>

<span data-ttu-id="a79eb-140">Мы упростили сборку защищенных веб-API с использованием Identity в ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="a79eb-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="a79eb-141">Теперь маркеры доступа для обращения к веб-API можно получить с помощью [библиотеки проверки подлинности Microsoft (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span><span class="sxs-lookup"><span data-stu-id="a79eb-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="a79eb-142">Дополнительные сведения об изменениях в проверке подлинности в версии 2.0 см. в следующих ресурсах.</span><span class="sxs-lookup"><span data-stu-id="a79eb-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="a79eb-143">Подтверждение учетной записи и восстановление пароля в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a79eb-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="a79eb-144">Включение создания QR-кодов для приложений проверки подлинности в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a79eb-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="a79eb-145">Миграция проверки подлинности и Identity в ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="a79eb-145">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="a79eb-146">Шаблоны SPA</span><span class="sxs-lookup"><span data-stu-id="a79eb-146">SPA templates</span></span>

<span data-ttu-id="a79eb-147">Доступны шаблоны проектов одностраничных приложений (Single Page Application, SPA) Angular, Aurelia, Knockout.js, React.js и React.js с Redux.</span><span class="sxs-lookup"><span data-stu-id="a79eb-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="a79eb-148">Шаблон Angular обновлен до Angular 4.</span><span class="sxs-lookup"><span data-stu-id="a79eb-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="a79eb-149">Шаблоны Angular и React доступны по умолчанию. Сведения о получении других шаблонов см. в разделе [Создание проекта SPA](xref:client-side/spa-services#create-a-new-project).</span><span class="sxs-lookup"><span data-stu-id="a79eb-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#create-a-new-project).</span></span> <span data-ttu-id="a79eb-150">Сведения о сборке SPA в ASP.NET Core, см. в разделе <xref:client-side/spa-services>.</span><span class="sxs-lookup"><span data-stu-id="a79eb-150">For information about how to build a SPA in ASP.NET Core, see <xref:client-side/spa-services>.</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="a79eb-151">Усовершенствования Kestrel</span><span class="sxs-lookup"><span data-stu-id="a79eb-151">Kestrel improvements</span></span>

<span data-ttu-id="a79eb-152">Веб-сервер Kestrel получил новые функции, необходимые серверу с выходом в Интернет.</span><span class="sxs-lookup"><span data-stu-id="a79eb-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="a79eb-153">Добавлено несколько параметров конфигурации ограничений для сервера в новое свойство `Limits` класса `KestrelServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="a79eb-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="a79eb-154">Вы можете добавлять следующие ограничения:</span><span class="sxs-lookup"><span data-stu-id="a79eb-154">Add limits for the following:</span></span>

* <span data-ttu-id="a79eb-155">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="a79eb-155">Maximum client connections</span></span>
* <span data-ttu-id="a79eb-156">максимальный размер текста запроса;</span><span class="sxs-lookup"><span data-stu-id="a79eb-156">Maximum request body size</span></span>
* <span data-ttu-id="a79eb-157">минимальная скорость передачи данных в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="a79eb-157">Minimum request body data rate</span></span>

<span data-ttu-id="a79eb-158">Дополнительные сведения см. в статье [Реализация веб-сервера Kestrel в ASP.NET Core](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="a79eb-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="a79eb-159">WebListener переименован в HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a79eb-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="a79eb-160">Пакеты `Microsoft.AspNetCore.Server.WebListener` и `Microsoft.Net.Http.Server` объединены в новый пакет `Microsoft.AspNetCore.Server.HttpSys`.</span><span class="sxs-lookup"><span data-stu-id="a79eb-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="a79eb-161">Соответственно обновлены и пространства имен.</span><span class="sxs-lookup"><span data-stu-id="a79eb-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="a79eb-162">Дополнительные сведения см. в статье [Реализация веб-сервера HTTP.sys в ASP.NET Core](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="a79eb-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="a79eb-163">Расширенная поддержка заголовков HTTP</span><span class="sxs-lookup"><span data-stu-id="a79eb-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="a79eb-164">Теперь при использовании MVC для передачи `FileStreamResult` или `FileContentResult` можно указывать дату `ETag` или `LastModified` для передаваемого содержимого.</span><span class="sxs-lookup"><span data-stu-id="a79eb-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="a79eb-165">Для возвращаемого содержимого эти значения можно задать, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="a79eb-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="a79eb-166">Файл, возвращаемый вашим посетителям, будет иметь соответствующие заголовки HTTP для значений `ETag` и `LastModified`.</span><span class="sxs-lookup"><span data-stu-id="a79eb-166">The file returned to your visitors has the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="a79eb-167">Если посетители вашего приложение запросят содержимое с заголовком типа "Запрос диапазона", ASP.NET Core распознает и обработает этот заголовок.</span><span class="sxs-lookup"><span data-stu-id="a79eb-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="a79eb-168">Запрошенное содержимое может доставляться частично, в случае чего ASP.NET Core соответствующим образом пропустит и вернет только запрошенный набор байтов.</span><span class="sxs-lookup"><span data-stu-id="a79eb-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="a79eb-169">При этом прописывать в методах специальные обработчики для адаптации или реализации этой функции не требуется, все будет сделано автоматически.</span><span class="sxs-lookup"><span data-stu-id="a79eb-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="a79eb-170">Запуск внешнего размещения и Application Insights</span><span class="sxs-lookup"><span data-stu-id="a79eb-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="a79eb-171">Среды внешнего размещения теперь внедряют зависимости дополнительных пакетов и выполняют код во время запуска приложения; при этом приложению не нужно явно принимать зависимость или вызывать какие-либо методы.</span><span class="sxs-lookup"><span data-stu-id="a79eb-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="a79eb-172">Эту функцию можно использовать для того, чтобы выделить в определенных средах какие-то уникальные для них компоненты без предварительной настройки самого приложения.</span><span class="sxs-lookup"><span data-stu-id="a79eb-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="a79eb-173">В ASP.NET Core 2.0 эта функция используется для автоматического включения диагностики Application Insights при отладке в Visual Studio и (после включения) при запуске службы приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="a79eb-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="a79eb-174">В связи с этим шаблоны проектов больше не добавляют пакеты и код Application Insights по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a79eb-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="a79eb-175">Сведения о состоянии плановой документации см. в статье о [проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span><span class="sxs-lookup"><span data-stu-id="a79eb-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="a79eb-176">Автоматическое использование маркеров защиты от подделки</span><span class="sxs-lookup"><span data-stu-id="a79eb-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="a79eb-177">ASP.NET Core всегда помогает в создании HTML-кода для содержимого, но в новой версии сделан еще один шаг к предотвращению атак с подделкой межсайтовых запросов (XSRF).</span><span class="sxs-lookup"><span data-stu-id="a79eb-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="a79eb-178">Теперь ASP.NET Core будет выдавать маркеры защиты от подделки по умолчанию и проверять их при отправке форм и выполнении страниц без дополнительной конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a79eb-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="a79eb-179">Для получения дополнительной информации см. <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="a79eb-179">For more information, see <xref:security/anti-request-forgery>.</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="a79eb-180">Автоматическая предварительная компиляция</span><span class="sxs-lookup"><span data-stu-id="a79eb-180">Automatic precompilation</span></span>

<span data-ttu-id="a79eb-181">При публикации по умолчанию включается предварительная компиляция представления Razor, что сокращает размер выходных данных публикации и время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="a79eb-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="a79eb-182">Дополнительные сведения см. в статье [Компиляция и предварительная компиляция представлений Razor в ASP.NET Core](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="a79eb-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="razor-support-for-c-71"></a><span data-ttu-id="a79eb-183">Поддержка C# 7.1 в Razor</span><span class="sxs-lookup"><span data-stu-id="a79eb-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="a79eb-184">Для работы с новым компилятором Roslyn был обновлен и обработчик представлений Razor.</span><span class="sxs-lookup"><span data-stu-id="a79eb-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="a79eb-185">Добавлена поддержка таких функций C# 7.1 как выражения по умолчанию, выводимые имена кортежей и сопоставление шаблонов с универсальными шаблонами.</span><span class="sxs-lookup"><span data-stu-id="a79eb-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="a79eb-186">Чтобы использовать C# 7.1 в проекте, добавьте в файл проекта следующее свойство и перезагрузите решение.</span><span class="sxs-lookup"><span data-stu-id="a79eb-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="a79eb-187">Сведения о состоянии компонентов C# 7.1 см. в статье о [репозитории Roslyn GitHub](https://github.com/dotnet/roslyn/blob/main/docs/Language%20Feature%20Status.md).</span><span class="sxs-lookup"><span data-stu-id="a79eb-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/main/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="a79eb-188">Изменения в другой документации к версии 2.0</span><span class="sxs-lookup"><span data-stu-id="a79eb-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="a79eb-189">Профили публикации Visual Studio для развертывания приложений ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a79eb-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="a79eb-190">Управление ключами</span><span class="sxs-lookup"><span data-stu-id="a79eb-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="a79eb-191">Настройка проверки подлинности Facebook</span><span class="sxs-lookup"><span data-stu-id="a79eb-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="a79eb-192">Настройка проверки подлинности Twitter</span><span class="sxs-lookup"><span data-stu-id="a79eb-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="a79eb-193">Настройка проверки подлинности Google</span><span class="sxs-lookup"><span data-stu-id="a79eb-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="a79eb-194">Настройка проверки подлинности учетной записи Майкрософт</span><span class="sxs-lookup"><span data-stu-id="a79eb-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="a79eb-195">Руководство по миграции</span><span class="sxs-lookup"><span data-stu-id="a79eb-195">Migration guidance</span></span>

<span data-ttu-id="a79eb-196">Инструкции по миграции приложений с ASP.NET Core 1.x в ASP.NET 2.0 см. в следующих ресурсах.</span><span class="sxs-lookup"><span data-stu-id="a79eb-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="a79eb-197">Миграция с ASP.NET Core 1.x на ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="a79eb-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="a79eb-198">Миграция проверки подлинности и Identity в ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="a79eb-198">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="a79eb-199">Дополнительные сведения</span><span class="sxs-lookup"><span data-stu-id="a79eb-199">Additional Information</span></span>

<span data-ttu-id="a79eb-200">Полный список изменений см. в статье [Заметки о выпуске ASP.NET Core 2.0](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span><span class="sxs-lookup"><span data-stu-id="a79eb-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span></span>

<span data-ttu-id="a79eb-201">Чтобы отслеживать ход работы и планы команды разработчиков ASP.NET Core, смотрите выпуски [ASP.NET Community Standup](https://live.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="a79eb-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
