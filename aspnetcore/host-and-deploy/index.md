---
title: Размещение и развертывание ASP.NET Core
author: rick-anderson
description: Сведения о настройке сред размещения и развертывании приложений ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/index
ms.openlocfilehash: 05d04a9c95910c805ea28578aba21a0658dd779a
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252972"
---
# <a name="host-and-deploy-aspnet-core"></a>Размещение и развертывание ASP.NET Core

::: moniker range=">= aspnetcore-2.2"

В общем при развертывании приложения ASP.NET Core в среде внешнего размещения выполняются следующие действия.

* Развертывание опубликованного приложения в папке на сервере размещения.
* Настройка диспетчера процессов, который запускает приложение при поступлении запросов и перезапускает его после аварийного завершения или после перезагрузки сервера.
* Для конфигурации настройте такой прокси-сервер, который перенаправляет запросы в приложение.

## <a name="publish-to-a-folder"></a>Публикация в папку

Команда интерфейса командной строки [dotnet publish](/dotnet/core/tools/dotnet-publish) компилирует код приложения и копирует файлы, необходимые для его выполнения, в папку *publish*. При развертывании из Visual Studio шаг `dotnet publish` выполняется автоматически перед копированием файлов место развертывания.

## <a name="publish-settings-files"></a>Файлы параметров публикации

Файлы `*.json` публикуются по умолчанию. Чтобы опубликовать другие файлы параметров, укажите их в элементе [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) в файле проекта. В следующем примере публикуются XML-файлы:

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a>Содержимое папки

Папка *publish* содержит один или несколько файлов сборки и зависимости приложения, а также может включать среду выполнения .NET.

Приложения .NET Core могут публиковаться как *автономные развертывания* или *развертывания, зависящие от платформы*. Если приложение автономное, в папку *publish* добавляются файлы сборки, содержащие среду выполнения .NET. Если приложение зависит от платформы, файлы среды выполнения .NET не добавляются, так как приложение ссылается на версию .NET, установленную на сервере. По умолчанию используется модель развертывания с зависимостью от платформы. Дополнительные сведения см. в статье [Развертывание приложений .NET Core](/dotnet/core/deploying/).

В дополнение к *EXE*- и *DLL*-файлам папка *публикации* для приложения ASP.NET Core обычно содержит файлы конфигурации, статические ресурсы и представления MVC. Для получения дополнительной информации см. <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Настройка диспетчер процессов

Приложение ASP.NET Core — это консольное приложение, которое должно запускаться при загрузке сервера и перезапускаться после его аварийного завершения. Для автоматического запуска и перезапуска требуется диспетчер процессов. Далее приведены наиболее распространенные диспетчеры процессов для ASP.NET Core.

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [Службы IIS](xref:host-and-deploy/iis/index)
  * [Служба Windows](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Настройка обратного прокси-сервера

Если приложение использует сервер [Kestrel](xref:fundamentals/servers/kestrel), вы можете использовать [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) или [IIS](xref:host-and-deploy/iis/index) в качестве обратного прокси-сервера. Обратный прокси-сервер получает HTTP-запросы из Интернета и пересылает их в Kestrel.

::: moniker-end 

::: moniker range=">= aspnetcore-5.0"
Любая из этих конфигураций &mdash; с обратным прокси-сервером и без него &mdash; является поддерживаемой конфигурацией для размещения. Дополнительные сведения см. в статье [Использование Kestrel с обратным прокси-сервером](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy).
::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"
Любая из этих конфигураций &mdash; с обратным прокси-сервером и без него &mdash; является поддерживаемой конфигурацией для размещения. Дополнительные сведения см. в статье [Использование Kestrel с обратным прокси-сервером](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).
::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="proxy-server-and-load-balancer-scenarios"></a>Сценарии использования прокси-сервера и подсистемы балансировки нагрузки

Для приложений, размещенных за прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка. Без дополнительной настройки приложение может не иметь доступ к схеме (HTTP/HTTPS) и удаленному IP-адресу, где был сформирован запрос. Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Использование Visual Studio и MSBuild для автоматизации развертывания

Помимо копирования выходных данных из [dotnet publish](/dotnet/core/tools/dotnet-publish) на сервер в процессе развертывания часто требуется выполнение и других задач. Например, может потребоваться включить дополнительные файлы в папку *publish* или исключить их из нее. Visual Studio использует для веб-развертывания [MSBuild](/visualstudio/msbuild/msbuild) и настраивает MSBuild для решения многих других задач в процессе развертывания. Дополнительные сведения см. в статье <xref:host-and-deploy/visual-studio-publish-profiles> (Профили публикации в Visual Studio) и книге [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Использование MSBuild и сборки Team Foundation).

Развертывание приложений можно выполнять напрямую из Visual Studio в службу приложений Azure, используя [компонент веб-публикации](xref:tutorials/publish-to-azure-webapp-using-vs) или [встроенную поддержку Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment). Azure DevOps Services поддерживает [непрерывное развертывание в Службе приложений Azure](/azure/devops/pipelines/targets/webapp). Дополнительные сведения см. в разделе [Сборка DevOps с использованием ASP.NET Core и Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Публикация в Azure

См. сведения о публикации приложения в Azure с помощью Visual Studio (<xref:tutorials/publish-to-azure-webapp-using-vs>). Дополнительный пример приведен в статье [Создание веб-приложения ASP.NET Core в Azure](/azure/app-service/app-service-web-get-started-dotnet).

## <a name="publish-with-msdeploy-on-windows"></a>Публикация с помощью MSDeploy в Windows

Инструкции о том, как опубликовать приложение с помощью профиля публикации Visual Studio или из командной строки Windows с помощью команды [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild), см. в статье <xref:host-and-deploy/visual-studio-publish-profiles>.

## <a name="internet-information-services-iis"></a>службы IIS

Сведения о развертывании служб IIS с конфигурацией, предоставляемой файлом *web.config*, см. в статьях в разделе <xref:host-and-deploy/iis/index>.

## <a name="host-in-a-web-farm"></a>Размещение в веб-ферме

Сведения о конфигурации для размещения приложений ASP.NET Core в среде веб-фермы (например, при развертывании множества экземпляров приложения для масштабируемости) см. в разделе <xref:host-and-deploy/web-farm>.

## <a name="host-on-docker"></a>Размещение в Docker

Для получения дополнительной информации см. <xref:host-and-deploy/docker/index>.

## <a name="perform-health-checks"></a>Выполнение проверок работоспособности

Используйте ПО промежуточного слоя для проверки работоспособности приложения и его зависимостей. Для получения дополнительной информации см. <xref:host-and-deploy/health-checks>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:test/troubleshoot>
* [Размещение в ASP.NET](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

В общем при развертывании приложения ASP.NET Core в среде внешнего размещения выполняются следующие действия.

* Развертывание опубликованного приложения в папке на сервере размещения.
* Настройка диспетчера процессов, который запускает приложение при поступлении запросов и перезапускает его после аварийного завершения или после перезагрузки сервера.
* Для конфигурации настройте такой прокси-сервер, который перенаправляет запросы в приложение.

## <a name="publish-to-a-folder"></a>Публикация в папку

Команда интерфейса командной строки [dotnet publish](/dotnet/core/tools/dotnet-publish) компилирует код приложения и копирует файлы, необходимые для его выполнения, в папку *publish*. При развертывании из Visual Studio шаг `dotnet publish` выполняется автоматически перед копированием файлов место развертывания.

### <a name="folder-contents"></a>Содержимое папки

Папка *publish* содержит один или несколько файлов сборки и зависимости приложения, а также может включать среду выполнения .NET.

Приложения .NET Core могут публиковаться как *автономные развертывания* или *развертывания, зависящие от платформы*. Если приложение автономное, в папку *publish* добавляются файлы сборки, содержащие среду выполнения .NET. Если приложение зависит от платформы, файлы среды выполнения .NET не добавляются, так как приложение ссылается на версию .NET, установленную на сервере. По умолчанию используется модель развертывания с зависимостью от платформы. Дополнительные сведения см. в статье [Развертывание приложений .NET Core](/dotnet/core/deploying/).

В дополнение к *EXE*- и *DLL*-файлам папка *публикации* для приложения ASP.NET Core обычно содержит файлы конфигурации, статические ресурсы и представления MVC. Для получения дополнительной информации см. <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Настройка диспетчер процессов

Приложение ASP.NET Core — это консольное приложение, которое должно запускаться при загрузке сервера и перезапускаться после его аварийного завершения. Для автоматического запуска и перезапуска требуется диспетчер процессов. Далее приведены наиболее распространенные диспетчеры процессов для ASP.NET Core.

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [Службы IIS](xref:host-and-deploy/iis/index)
  * [Служба Windows](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Настройка обратного прокси-сервера

Если приложение использует сервер [Kestrel](xref:fundamentals/servers/kestrel), вы можете использовать [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) или [IIS](xref:host-and-deploy/iis/index) в качестве обратного прокси-сервера. Обратный прокси-сервер получает HTTP-запросы из Интернета и пересылает их в Kestrel.

Любая из этих конфигураций &mdash; с обратным прокси-сервером и без него &mdash; является поддерживаемой конфигурацией для размещения. Дополнительные сведения см. в статье [Использование Kestrel с обратным прокси-сервером](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Сценарии использования прокси-сервера и подсистемы балансировки нагрузки

Для приложений, размещенных за прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка. Без дополнительной настройки приложение может не иметь доступ к схеме (HTTP/HTTPS) и удаленному IP-адресу, где был сформирован запрос. Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Использование Visual Studio и MSBuild для автоматизации развертывания

Помимо копирования выходных данных из [dotnet publish](/dotnet/core/tools/dotnet-publish) на сервер в процессе развертывания часто требуется выполнение и других задач. Например, может потребоваться включить дополнительные файлы в папку *publish* или исключить их из нее. Visual Studio использует для веб-развертывания MSBuild и настраивает MSBuild для решения многих других задач в процессе развертывания. Дополнительные сведения см. в статье <xref:host-and-deploy/visual-studio-publish-profiles> (Профили публикации в Visual Studio) и книге [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Использование MSBuild и сборки Team Foundation).

Развертывание приложений можно выполнять напрямую из Visual Studio в службу приложений Azure, используя [компонент веб-публикации](xref:tutorials/publish-to-azure-webapp-using-vs) или [встроенную поддержку Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment). Azure DevOps Services поддерживает [непрерывное развертывание в Службе приложений Azure](/azure/devops/pipelines/targets/webapp). Дополнительные сведения см. в разделе [Сборка DevOps с использованием ASP.NET Core и Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Публикация в Azure

См. сведения о публикации приложения в Azure с помощью Visual Studio (<xref:tutorials/publish-to-azure-webapp-using-vs>). Дополнительный пример приведен в статье [Создание веб-приложения ASP.NET Core в Azure](/azure/app-service/app-service-web-get-started-dotnet).

## <a name="publish-with-msdeploy-on-windows"></a>Публикация с помощью MSDeploy в Windows

Инструкции о том, как опубликовать приложение с помощью профиля публикации Visual Studio или из командной строки Windows с помощью команды [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild), см. в статье <xref:host-and-deploy/visual-studio-publish-profiles>.

## <a name="internet-information-services-iis"></a>службы IIS

Сведения о развертывании служб IIS с конфигурацией, предоставляемой файлом *web.config*, см. в статьях в разделе <xref:host-and-deploy/iis/index>.

## <a name="host-in-a-web-farm"></a>Размещение в веб-ферме

Сведения о конфигурации для размещения приложений ASP.NET Core в среде веб-фермы (например, при развертывании множества экземпляров приложения для масштабируемости) см. в разделе <xref:host-and-deploy/web-farm>.

## <a name="host-on-docker"></a>Размещение в Docker

Для получения дополнительной информации см. <xref:host-and-deploy/docker/index>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:test/troubleshoot>
* [Размещение в ASP.NET](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
