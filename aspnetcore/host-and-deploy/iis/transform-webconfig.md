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
# <a name="transform-webconfig"></a>Преобразование web.config

Автор: [Виджай Рамакришнан](https://github.com/vijayrkn) (Vijay Ramakrishnan)

Преобразования файла *web.config* можно применять автоматически при публикации приложения на основе следующих данных:

* [Конфигурация сборки](#build-configuration).
* [Profile](#profile)
* [Среда](#environment)
* [Пользовательский](#custom)

Такие преобразования выполняются в любом из следующих сценариев создания *web.config*:

* Автоматическое создание в пакете SDK `Microsoft.NET.Sdk.Web`.
* Размещение разработчиком в [корневом каталоге содержимого](xref:fundamentals/index#content-root) приложения.

## <a name="build-configuration"></a>Конфигурация построения

Первыми выполняются преобразования конфигурации сборки.

Добавьте файл *web.{КОНФИГУРАЦИЯ}.config* для каждой [конфигурации сборки для отладки или выпуска](/dotnet/core/tools/dotnet-publish#options), чтобы потребовать преобразование *web.config*.

В следующем примере задается переменная для конкретной конфигурации в файле *web.Release.config*.

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

Это преобразование применяется, если настроена конфигурация *выпуска* (Release).

```dotnetcli
dotnet publish --configuration Release
```

Свойство MSBuild для этой конфигурации имеет значение `$(Configuration)`.

## <a name="profile"></a>Профиль

Во вторую очередь, после [конфигурации сборки](#build-configuration), выполняются преобразования профиля.

Добавьте файл *web.{ПРОФИЛЬ}.config* для каждой конфигурации профиля, которой требуется преобразование *web.config*.

В следующем примере в файле *web.FolderProfile.config* устанавливается переменная среды для конкретного профиля публикации папки.

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

Это преобразование применяется, если используется профиль *FolderProfile*.

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

Свойство MSBuild для имени профиля имеет значение `$(PublishProfile)`.

Если профиль не передан, используется имя профиля по умолчанию **FileSystem**, а следовательно применяется файл *web.FileSystem.config*, если он присутствует в корневом каталоге содержимого приложения.

## <a name="environment"></a>Среда

Третьими, после преобразований [конфигурации сборки](#build-configuration) и [профиля](#profile), выполняются преобразования среды.

Добавьте файл *web.{СРЕДА}.config* для каждой [среды](xref:fundamentals/environments), которой требуется преобразование *web.config*.

В следующем примере в файле *web.Production.config* устанавливается переменная среды для определенной рабочей среды:

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

Это преобразование применяется, если используется среда *Production*.

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

Свойство MSBuild для этой среды имеет значение `$(EnvironmentName)`.

Если вы используете профиль публикации для публикации из Visual Studio, ознакомьтесь со статьей <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.

Переменная среды `ASPNETCORE_ENVIRONMENT` автоматически добавляется в файл *web.config*, если указано имя среды.

## <a name="custom"></a>Другой

И, наконец, после преобразований [конфигурации сборки](#build-configuration), [профиля](#profile) и [среды](#environment) выполняются пользовательские преобразования.

Добавьте файл *web.{ПОЛЬЗОВАТЕЛЬСКОЕ_ИМЯ}.config* для каждой пользовательской конфигурации, которой требуется преобразование *web.config*.

В следующем примере в файле *custom.transform* устанавливается переменная среды для пользовательского преобразования.

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

Это преобразование применяется, если в команду [dotnet publish](/dotnet/core/tools/dotnet-publish) передано свойство `CustomTransformFileName`.

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

Свойство MSBuild для имени профиля имеет значение `$(CustomTransformFileName)`.

## <a name="prevent-webconfig-transformation"></a>Предотвращение преобразования web.config

Чтобы избежать преобразования файла *web.config*, настройте свойство MSBuild `$(IsWebConfigTransformDisabled)` следующим образом.

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Синтаксис преобразования файла Web.config для развертывания проектов веб-приложений](/previous-versions/dd465326(v=vs.100))
* [Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110)) (Синтаксис преобразования файла Web.config для развертывания проектов веб-приложений с помощью Visual Studio)
