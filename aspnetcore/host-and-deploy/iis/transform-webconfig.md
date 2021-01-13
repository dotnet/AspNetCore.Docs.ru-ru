---
title: Преобразование web.config
author: rick-anderson
description: Узнайте, как преобразовать файл web.config при публикации приложения ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: d264aaee7889ec1c8ee0fe6b1f52ccc4cf355745
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854630"
---
# <a name="transform-webconfig"></a><span data-ttu-id="9c9fa-103">Преобразование web.config</span><span class="sxs-lookup"><span data-stu-id="9c9fa-103">Transform web.config</span></span>

<span data-ttu-id="9c9fa-104">Автор: [Виджай Рамакришнан](https://github.com/vijayrkn) (Vijay Ramakrishnan)</span><span class="sxs-lookup"><span data-stu-id="9c9fa-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn)</span></span>

<span data-ttu-id="9c9fa-105">Преобразования файла *web.config* можно применять автоматически при публикации приложения на основе следующих данных:</span><span class="sxs-lookup"><span data-stu-id="9c9fa-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* <span data-ttu-id="9c9fa-106">[Конфигурация сборки](#build-configuration).</span><span class="sxs-lookup"><span data-stu-id="9c9fa-106">[Build configuration](#build-configuration)</span></span>
* [<span data-ttu-id="9c9fa-107">Profile</span><span class="sxs-lookup"><span data-stu-id="9c9fa-107">Profile</span></span>](#profile)
* [<span data-ttu-id="9c9fa-108">Среда</span><span class="sxs-lookup"><span data-stu-id="9c9fa-108">Environment</span></span>](#environment)
* [<span data-ttu-id="9c9fa-109">Пользовательский</span><span class="sxs-lookup"><span data-stu-id="9c9fa-109">Custom</span></span>](#custom)

<span data-ttu-id="9c9fa-110">Такие преобразования выполняются в любом из следующих сценариев создания *web.config*:</span><span class="sxs-lookup"><span data-stu-id="9c9fa-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="9c9fa-111">Автоматическое создание в пакете SDK `Microsoft.NET.Sdk.Web`.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="9c9fa-112">Размещение разработчиком в [корневом каталоге содержимого](xref:fundamentals/index#content-root) приложения.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-112">Provided by the developer in the [content root](xref:fundamentals/index#content-root) of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="9c9fa-113">Конфигурация построения</span><span class="sxs-lookup"><span data-stu-id="9c9fa-113">Build configuration</span></span>

<span data-ttu-id="9c9fa-114">Первыми выполняются преобразования конфигурации сборки.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="9c9fa-115">Добавьте файл *web.{КОНФИГУРАЦИЯ}.config* для каждой [конфигурации сборки для отладки или выпуска](/dotnet/core/tools/dotnet-publish#options), чтобы потребовать преобразование *web.config*.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="9c9fa-116">В следующем примере задается переменная для конкретной конфигурации в файле *web.Release.config*.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-116">In the following example, a configuration-specific environment variable is set in *web.Release.config*:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="9c9fa-117">Это преобразование применяется, если настроена конфигурация *выпуска* (Release).</span><span class="sxs-lookup"><span data-stu-id="9c9fa-117">The transform is applied when the configuration is set to *Release*:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="9c9fa-118">Свойство MSBuild для этой конфигурации имеет значение `$(Configuration)`.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="9c9fa-119">Профиль</span><span class="sxs-lookup"><span data-stu-id="9c9fa-119">Profile</span></span>

<span data-ttu-id="9c9fa-120">Во вторую очередь, после [конфигурации сборки](#build-configuration), выполняются преобразования профиля.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="9c9fa-121">Добавьте файл *web.{ПРОФИЛЬ}.config* для каждой конфигурации профиля, которой требуется преобразование *web.config*.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="9c9fa-122">В следующем примере в файле *web.FolderProfile.config* устанавливается переменная среды для конкретного профиля публикации папки.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="9c9fa-123">Это преобразование применяется, если используется профиль *FolderProfile*.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-123">The transform is applied when the profile is *FolderProfile*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="9c9fa-124">Свойство MSBuild для имени профиля имеет значение `$(PublishProfile)`.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="9c9fa-125">Если профиль не передан, используется имя профиля по умолчанию **FileSystem**, а следовательно применяется файл *web.FileSystem.config*, если он присутствует в корневом каталоге содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="9c9fa-126">Среда</span><span class="sxs-lookup"><span data-stu-id="9c9fa-126">Environment</span></span>

<span data-ttu-id="9c9fa-127">Третьими, после преобразований [конфигурации сборки](#build-configuration) и [профиля](#profile), выполняются преобразования среды.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="9c9fa-128">Добавьте файл *web.{СРЕДА}.config* для каждой [среды](xref:fundamentals/environments), которой требуется преобразование *web.config*.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="9c9fa-129">В следующем примере в файле *web.Production.config* устанавливается переменная среды для определенной рабочей среды:</span><span class="sxs-lookup"><span data-stu-id="9c9fa-129">In the following example, an environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="9c9fa-130">Это преобразование применяется, если используется среда *Production*.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-130">The transform is applied when the environment is *Production*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="9c9fa-131">Свойство MSBuild для этой среды имеет значение `$(EnvironmentName)`.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="9c9fa-132">Если вы используете профиль публикации для публикации из Visual Studio, ознакомьтесь со статьей <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="9c9fa-133">Переменная среды `ASPNETCORE_ENVIRONMENT` автоматически добавляется в файл *web.config*, если указано имя среды.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="9c9fa-134">Другой</span><span class="sxs-lookup"><span data-stu-id="9c9fa-134">Custom</span></span>

<span data-ttu-id="9c9fa-135">И, наконец, после преобразований [конфигурации сборки](#build-configuration), [профиля](#profile) и [среды](#environment) выполняются пользовательские преобразования.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="9c9fa-136">Добавьте файл *web.{ПОЛЬЗОВАТЕЛЬСКОЕ_ИМЯ}.config* для каждой пользовательской конфигурации, которой требуется преобразование *web.config*.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="9c9fa-137">В следующем примере в файле *custom.transform* устанавливается переменная среды для пользовательского преобразования.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-137">In the following example, a custom transform environment variable is set in *custom.transform*:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="9c9fa-138">Это преобразование применяется, если в команду [dotnet publish](/dotnet/core/tools/dotnet-publish) передано свойство `CustomTransformFileName`.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="9c9fa-139">Свойство MSBuild для имени профиля имеет значение `$(CustomTransformFileName)`.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="9c9fa-140">Предотвращение преобразования web.config</span><span class="sxs-lookup"><span data-stu-id="9c9fa-140">Prevent web.config transformation</span></span>

<span data-ttu-id="9c9fa-141">Чтобы избежать преобразования файла *web.config*, настройте свойство MSBuild `$(IsWebConfigTransformDisabled)` следующим образом.</span><span class="sxs-lookup"><span data-stu-id="9c9fa-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="9c9fa-142">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="9c9fa-142">Additional resources</span></span>

* <span data-ttu-id="9c9fa-143">[Синтаксис преобразования файла Web.config для развертывания проектов веб-приложений](/previous-versions/dd465326(v=vs.100))</span><span class="sxs-lookup"><span data-stu-id="9c9fa-143">[Web.config Transformation Syntax for Web Application Project Deployment](/previous-versions/dd465326(v=vs.100))</span></span>
* <span data-ttu-id="9c9fa-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110)) (Синтаксис преобразования файла Web.config для развертывания проектов веб-приложений с помощью Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="9c9fa-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
