---
title: Размещение образов ASP.NET Core с помощью DOCKER по протоколу HTTPS
author: rick-anderson
description: Сведения о размещении образов ASP.NET Core с помощью DOCKER по протоколу HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
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
uid: security/docker-https
ms.openlocfilehash: 3af2aff477604eb19ac211753f848d08d0c67c72
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588644"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="5e70a-103">Размещение образов ASP.NET Core с помощью DOCKER по протоколу HTTPS</span><span class="sxs-lookup"><span data-stu-id="5e70a-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="5e70a-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="5e70a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5e70a-105">По [умолчанию ASP.NET Core использует протокол HTTPS](./enforcing-ssl.md).</span><span class="sxs-lookup"><span data-stu-id="5e70a-105">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="5e70a-106">[Протокол HTTPS](https://en.wikipedia.org/wiki/HTTPS) использует [Сертификаты](https://en.wikipedia.org/wiki/Public_key_certificate) для доверия, удостоверений и шифрования.</span><span class="sxs-lookup"><span data-stu-id="5e70a-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="5e70a-107">В этом документе объясняется, как запускать предварительно созданные образы контейнеров с помощью протокола HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5e70a-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="5e70a-108">Сценарии разработки см. [в статье Разработка приложений ASP.NET Core с помощью DOCKER по протоколу HTTPS](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md) .</span><span class="sxs-lookup"><span data-stu-id="5e70a-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="5e70a-109">Для работы с этим примером требуется [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) или [клиент Docker](https://www.docker.com/products/docker) более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="5e70a-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5e70a-110">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="5e70a-110">Prerequisites</span></span>

<span data-ttu-id="5e70a-111">Для выполнения некоторых инструкций в этом документе требуется [пакет SDK для .NET Core 2,2](https://dotnet.microsoft.com/download) или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="5e70a-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="5e70a-112">Сертификаты</span><span class="sxs-lookup"><span data-stu-id="5e70a-112">Certificates</span></span>

<span data-ttu-id="5e70a-113">Сертификат из [центра](https://wikipedia.org/wiki/Certificate_authority) сертификации необходим для [размещения в рабочей среде](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) для домена.</span><span class="sxs-lookup"><span data-stu-id="5e70a-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="5e70a-114">[Let's Encrypt](https://letsencrypt.org/) — Это центр сертификации, предлагающий бесплатные сертификаты.</span><span class="sxs-lookup"><span data-stu-id="5e70a-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="5e70a-115">В этом документе используются [самозаверяющие сертификаты разработки](https://en.wikipedia.org/wiki/Self-signed_certificate) для размещения предварительно созданных образов `localhost` .</span><span class="sxs-lookup"><span data-stu-id="5e70a-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="5e70a-116">Инструкции аналогичны использованию рабочих сертификатов.</span><span class="sxs-lookup"><span data-stu-id="5e70a-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="5e70a-117">Используйте [DotNet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) для создания самозаверяющих сертификатов для разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="5e70a-117">Use [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) to create self-signed certificates for development and testing.</span></span>

<span data-ttu-id="5e70a-118">Для производственных сертификатов:</span><span class="sxs-lookup"><span data-stu-id="5e70a-118">For production certs:</span></span>

* <span data-ttu-id="5e70a-119">`dotnet dev-certs`Средство не требуется.</span><span class="sxs-lookup"><span data-stu-id="5e70a-119">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="5e70a-120">Сертификаты не обязательно хранить в расположении, используемом в инструкциях.</span><span class="sxs-lookup"><span data-stu-id="5e70a-120">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="5e70a-121">Должно работать любое расположение, хотя не рекомендуется хранить сертификаты в каталоге сайта.</span><span class="sxs-lookup"><span data-stu-id="5e70a-121">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="5e70a-122">Инструкции, приведенные в следующем разделе, содержат сведения о подключении сертификатов к контейнерам с помощью `-v` параметра командной строки DOCKER.</span><span class="sxs-lookup"><span data-stu-id="5e70a-122">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="5e70a-123">Вы можете добавить сертификаты в образы контейнеров с помощью `COPY` команды в *Dockerfile*, но это не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="5e70a-123">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="5e70a-124">Копирование сертификатов в образ не рекомендуется по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="5e70a-124">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="5e70a-125">Использование одного и того же образа для тестирования с помощью сертификатов разработчика затрудняется.</span><span class="sxs-lookup"><span data-stu-id="5e70a-125">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="5e70a-126">Использование одного и того же образа для размещения с производственными сертификатами затрудняется.</span><span class="sxs-lookup"><span data-stu-id="5e70a-126">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="5e70a-127">Существует значительный риск раскрытия сертификата.</span><span class="sxs-lookup"><span data-stu-id="5e70a-127">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="5e70a-128">Запуск предварительно созданных образов контейнеров с помощью HTTPS</span><span class="sxs-lookup"><span data-stu-id="5e70a-128">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="5e70a-129">Используйте следующие инструкции для настройки операционной системы.</span><span class="sxs-lookup"><span data-stu-id="5e70a-129">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="5e70a-130">Windows с контейнерами Linux</span><span class="sxs-lookup"><span data-stu-id="5e70a-130">Windows using Linux containers</span></span>

<span data-ttu-id="5e70a-131">Создать сертификат и настроить локальный компьютер:</span><span class="sxs-lookup"><span data-stu-id="5e70a-131">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="5e70a-132">В предыдущих командах замените `{ password here }` паролем.</span><span class="sxs-lookup"><span data-stu-id="5e70a-132">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="5e70a-133">Запустите образ контейнера с ASP.NET Core, настроенным для HTTPS, в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="5e70a-133">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="5e70a-134">При использовании [PowerShell](/powershell/scripting/overview)замените `%USERPROFILE%` на `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="5e70a-134">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="5e70a-135">Пароль должен совпадать с паролем, используемым для сертификата.</span><span class="sxs-lookup"><span data-stu-id="5e70a-135">The password must match the password used for the certificate.</span></span>


<span data-ttu-id="5e70a-136">Примечание. в этом случае сертификат должен быть `.pfx` файлом.</span><span class="sxs-lookup"><span data-stu-id="5e70a-136">Note: The certificate in this case must be a `.pfx` file.</span></span>  <span data-ttu-id="5e70a-137">`.crt` `.key` Использование файла или с паролем или без него не поддерживается в образце контейнера.</span><span class="sxs-lookup"><span data-stu-id="5e70a-137">Utilizing a `.crt` or `.key` file with or without the password isn't supported with the sample container.</span></span>  <span data-ttu-id="5e70a-138">Например, при указании `.crt` файла контейнер может вернуть сообщения об ошибке, например "серверный протокол SSL должен использовать сертификат со связанным закрытым ключом".</span><span class="sxs-lookup"><span data-stu-id="5e70a-138">For example, when specifying a `.crt` file, the container may return error messages such as 'The server mode SSL must use a certificate with the associated private key.'.</span></span> <span data-ttu-id="5e70a-139">При использовании [WSL](/windows/wsl/about)Проверьте путь подключения, чтобы убедиться, что сертификат загружается правильно.</span><span class="sxs-lookup"><span data-stu-id="5e70a-139">When using [WSL](/windows/wsl/about), validate the mount path to ensure that the certificate loads correctly.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="5e70a-140">macOS или Linux</span><span class="sxs-lookup"><span data-stu-id="5e70a-140">macOS or Linux</span></span>

<span data-ttu-id="5e70a-141">Создать сертификат и настроить локальный компьютер:</span><span class="sxs-lookup"><span data-stu-id="5e70a-141">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="5e70a-142">`dotnet dev-certs https --trust` поддерживается только в macOS и Windows.</span><span class="sxs-lookup"><span data-stu-id="5e70a-142">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="5e70a-143">Необходимо доверять сертификатам в Linux так, как это поддерживается дистрибутивом.</span><span class="sxs-lookup"><span data-stu-id="5e70a-143">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="5e70a-144">Вероятно, вам нужно доверять сертификату в браузере.</span><span class="sxs-lookup"><span data-stu-id="5e70a-144">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="5e70a-145">В предыдущих командах замените `{ password here }` паролем.</span><span class="sxs-lookup"><span data-stu-id="5e70a-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="5e70a-146">Запустите образ контейнера с ASP.NET Core, настроенным для HTTPS:</span><span class="sxs-lookup"><span data-stu-id="5e70a-146">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="5e70a-147">Пароль должен совпадать с паролем, используемым для сертификата.</span><span class="sxs-lookup"><span data-stu-id="5e70a-147">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="5e70a-148">Windows, использующих контейнеры Windows</span><span class="sxs-lookup"><span data-stu-id="5e70a-148">Windows using Windows containers</span></span>

<span data-ttu-id="5e70a-149">Создать сертификат и настроить локальный компьютер:</span><span class="sxs-lookup"><span data-stu-id="5e70a-149">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="5e70a-150">В предыдущих командах замените `{ password here }` паролем.</span><span class="sxs-lookup"><span data-stu-id="5e70a-150">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="5e70a-151">При использовании [PowerShell](/powershell/scripting/overview)замените `%USERPROFILE%` на `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="5e70a-151">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="5e70a-152">Запустите образ контейнера с ASP.NET Core, настроенным для HTTPS:</span><span class="sxs-lookup"><span data-stu-id="5e70a-152">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="5e70a-153">Пароль должен совпадать с паролем, используемым для сертификата.</span><span class="sxs-lookup"><span data-stu-id="5e70a-153">The password must match the password used for the certificate.</span></span> <span data-ttu-id="5e70a-154">При использовании [PowerShell](/powershell/scripting/overview)замените `%USERPROFILE%` на `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="5e70a-154">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
