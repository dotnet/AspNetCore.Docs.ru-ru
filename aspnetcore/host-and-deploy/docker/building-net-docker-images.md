---
title: Образы Docker для ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать опубликованные образы Docker для ASP.NET Core из реестра Docker. Извлечение и создание образов.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2021
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 2cd21722082af88e536bc1001b606ee96e7cf59b
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972058"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="65445-104">Образы Docker для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="65445-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="65445-105">В этом руководстве показано, как запустить приложение ASP.NET Core в контейнерах Docker.</span><span class="sxs-lookup"><span data-stu-id="65445-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="65445-106">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="65445-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="65445-107">сведения об образах Docker для ASP .NET Core;</span><span class="sxs-lookup"><span data-stu-id="65445-107">Learn about ASP.NET Core Docker images</span></span>
> * <span data-ttu-id="65445-108">скачивание примера приложения ASP.NET Core;</span><span class="sxs-lookup"><span data-stu-id="65445-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="65445-109">локальное выполнение примера приложения;</span><span class="sxs-lookup"><span data-stu-id="65445-109">Run the sample app locally</span></span>
> * <span data-ttu-id="65445-110">выполнение примера приложения в контейнерах Linux;</span><span class="sxs-lookup"><span data-stu-id="65445-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="65445-111">выполнение примера приложения в контейнерах Windows;</span><span class="sxs-lookup"><span data-stu-id="65445-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="65445-112">локальная сборка и развертывание.</span><span class="sxs-lookup"><span data-stu-id="65445-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="65445-113">Образы Docker для .NET Core</span><span class="sxs-lookup"><span data-stu-id="65445-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="65445-114">Для работы с этим руководством следует скачать пример приложения ASP.NET Core и запустить его в контейнерах Docker.</span><span class="sxs-lookup"><span data-stu-id="65445-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="65445-115">Этот пример поддерживается в контейнерах Linux и Windows.</span><span class="sxs-lookup"><span data-stu-id="65445-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="65445-116">Пример файла Dockerfile использует [функцию многоэтапной сборки Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) для сборки и выполнения в разных контейнерах.</span><span class="sxs-lookup"><span data-stu-id="65445-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="65445-117">Контейнеры для сборки и выполнения создаются на основе образов, предоставленных корпорацией Майкрософт в Docker Hub:</span><span class="sxs-lookup"><span data-stu-id="65445-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  <span data-ttu-id="65445-118">Этот образ используется в этом примере для создания приложения.</span><span class="sxs-lookup"><span data-stu-id="65445-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="65445-119">Образ содержит пакет SDK для .NET, который включает программы командной строки (CLI).</span><span class="sxs-lookup"><span data-stu-id="65445-119">The image contains the .NET SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="65445-120">Он оптимизирован для локальной разработки, отладки и модульного тестирования.</span><span class="sxs-lookup"><span data-stu-id="65445-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="65445-121">Установленные средства разработки и компиляции делают этот образ относительно большим.</span><span class="sxs-lookup"><span data-stu-id="65445-121">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  <span data-ttu-id="65445-122">Этот образ используется в этом примере для создания приложения.</span><span class="sxs-lookup"><span data-stu-id="65445-122">The sample uses this image for building the app.</span></span> <span data-ttu-id="65445-123">Образ содержит пакет SDK для .NET Core, который включает программы командной строки (CLI).</span><span class="sxs-lookup"><span data-stu-id="65445-123">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="65445-124">Он оптимизирован для локальной разработки, отладки и модульного тестирования.</span><span class="sxs-lookup"><span data-stu-id="65445-124">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="65445-125">Установленные средства разработки и компиляции делают этот образ относительно большим.</span><span class="sxs-lookup"><span data-stu-id="65445-125">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

::: moniker-end

   <span data-ttu-id="65445-126">Этот образ используется в этом примере для выполнения приложения.</span><span class="sxs-lookup"><span data-stu-id="65445-126">The sample uses this image for running the app.</span></span> <span data-ttu-id="65445-127">Этот образ содержит среду выполнения и библиотеки ASP.NET Core и оптимизирован для запуска приложений в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="65445-127">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="65445-128">Образ нацелен на высокую скорость развертывания и запуска приложений, поэтому он сравнительно невелик, что позволяет оптимизировать производительность сети между реестром Docker и узлом Docker.</span><span class="sxs-lookup"><span data-stu-id="65445-128">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="65445-129">В контейнер копируются только двоичные файлы и содержимое, необходимые для запуска приложений.</span><span class="sxs-lookup"><span data-stu-id="65445-129">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="65445-130">Все содержимое готово к запуску, что гарантирует самый короткий срок от запуска `docker run` до запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="65445-130">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="65445-131">В модели Docker динамическая компиляция кода не требуется.</span><span class="sxs-lookup"><span data-stu-id="65445-131">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="65445-132">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="65445-132">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="65445-133">Пакет SDK для .NET версии 5.0</span><span class="sxs-lookup"><span data-stu-id="65445-133">.NET SDK 5.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [<span data-ttu-id="65445-134">Пакет SDK для .NET Core 3.1</span><span class="sxs-lookup"><span data-stu-id="65445-134">.NET Core SDK 3.1</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="65445-135">Пакет SDK для .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="65445-135">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* <span data-ttu-id="65445-136">Клиент Docker 18.03 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="65445-136">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="65445-137">Дистрибутивах Linux</span><span class="sxs-lookup"><span data-stu-id="65445-137">Linux distributions</span></span>
    * [<span data-ttu-id="65445-138">CentOS</span><span class="sxs-lookup"><span data-stu-id="65445-138">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="65445-139">Debian</span><span class="sxs-lookup"><span data-stu-id="65445-139">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="65445-140">Fedora</span><span class="sxs-lookup"><span data-stu-id="65445-140">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="65445-141">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="65445-141">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="65445-142">macOS</span><span class="sxs-lookup"><span data-stu-id="65445-142">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="65445-143">Windows</span><span class="sxs-lookup"><span data-stu-id="65445-143">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="65445-144">Git</span><span class="sxs-lookup"><span data-stu-id="65445-144">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="65445-145">Скачивание примера приложения</span><span class="sxs-lookup"><span data-stu-id="65445-145">Download the sample app</span></span>

* <span data-ttu-id="65445-146">Скачайте пример, клонировав [репозиторий Docker для .NET](https://github.com/dotnet/dotnet-docker).</span><span class="sxs-lookup"><span data-stu-id="65445-146">Download the sample by cloning the [.NET Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="65445-147">Локальный запуск приложения</span><span class="sxs-lookup"><span data-stu-id="65445-147">Run the app locally</span></span>

* <span data-ttu-id="65445-148">Перейдите в папку проекта *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="65445-148">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="65445-149">Выполните следующую команду, чтобы собрать и запустить приложение локально:</span><span class="sxs-lookup"><span data-stu-id="65445-149">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="65445-150">В браузере перейдите по адресу `http://localhost:5000`, чтобы протестировать приложение.</span><span class="sxs-lookup"><span data-stu-id="65445-150">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="65445-151">Нажмите сочетание клавиш CTRL+C в командной строке, чтобы остановить приложение.</span><span class="sxs-lookup"><span data-stu-id="65445-151">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="65445-152">Выполнение в контейнере Linux</span><span class="sxs-lookup"><span data-stu-id="65445-152">Run in a Linux container</span></span>

* <span data-ttu-id="65445-153">Переключите клиент Docker на [контейнеры Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="65445-153">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="65445-154">Перейдите в папку проекта Dockerfile *dotnet-docker/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="65445-154">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="65445-155">Выполните следующие команды, чтобы собрать и запустить пример в Docker:</span><span class="sxs-lookup"><span data-stu-id="65445-155">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="65445-156">Команда `build` выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="65445-156">The `build` command arguments:</span></span>
  * <span data-ttu-id="65445-157">присваивает образу имя aspnetapp;</span><span class="sxs-lookup"><span data-stu-id="65445-157">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="65445-158">ищет файл Dockerfile в текущей папке (точка в конце).</span><span class="sxs-lookup"><span data-stu-id="65445-158">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="65445-159">Команда run выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="65445-159">The run command arguments:</span></span>
  * <span data-ttu-id="65445-160">создает псевдотерминал и сохраняет это окно открытым, даже если он не подключен</span><span class="sxs-lookup"><span data-stu-id="65445-160">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="65445-161">(действует так же, как `--interactive --tty`);</span><span class="sxs-lookup"><span data-stu-id="65445-161">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="65445-162">автоматически удаляет контейнер при завершении работы;</span><span class="sxs-lookup"><span data-stu-id="65445-162">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="65445-163">сопоставляет порт 5000 на локальном компьютере с портом 80 в контейнере;</span><span class="sxs-lookup"><span data-stu-id="65445-163">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="65445-164">присваивает контейнеру имя aspnetcore_sample;</span><span class="sxs-lookup"><span data-stu-id="65445-164">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="65445-165">указывает образ aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="65445-165">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="65445-166">В браузере перейдите по адресу `http://localhost:5000`, чтобы протестировать приложение.</span><span class="sxs-lookup"><span data-stu-id="65445-166">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="65445-167">Запуск в контейнере Windows</span><span class="sxs-lookup"><span data-stu-id="65445-167">Run in a Windows container</span></span>

* <span data-ttu-id="65445-168">Переключите клиент Docker на [контейнеры Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="65445-168">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="65445-169">Перейдите к папке файла docker: `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="65445-169">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="65445-170">Выполните следующие команды, чтобы собрать и запустить пример в Docker:</span><span class="sxs-lookup"><span data-stu-id="65445-170">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="65445-171">Для контейнеров Windows вам нужен IP-адрес контейнера (адрес `http://localhost:5000` не будет работать):</span><span class="sxs-lookup"><span data-stu-id="65445-171">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="65445-172">Откройте другую командную строку.</span><span class="sxs-lookup"><span data-stu-id="65445-172">Open up another command prompt.</span></span>
  * <span data-ttu-id="65445-173">Запустите `docker ps`, чтобы получить список запущенных контейнеров.</span><span class="sxs-lookup"><span data-stu-id="65445-173">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="65445-174">Убедитесь, что в списке присутствует контейнер "aspnetcore_sample".</span><span class="sxs-lookup"><span data-stu-id="65445-174">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="65445-175">Выполните `docker exec aspnetcore_sample ipconfig`, чтобы отобразить IP-адрес контейнера.</span><span class="sxs-lookup"><span data-stu-id="65445-175">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="65445-176">Эта команда возвращает примерно такой результат:</span><span class="sxs-lookup"><span data-stu-id="65445-176">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="65445-177">Скопируйте IPv4-адрес контейнера (например 172.29.245.43) и вставьте его в адресную строку браузера, чтобы протестировать приложение.</span><span class="sxs-lookup"><span data-stu-id="65445-177">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="65445-178">Локальная сборка и развертывание</span><span class="sxs-lookup"><span data-stu-id="65445-178">Build and deploy manually</span></span>

<span data-ttu-id="65445-179">В некоторых сценариях вам потребуется развернуть приложение в контейнер, скопировав нужные для выполнения ресурсы.</span><span class="sxs-lookup"><span data-stu-id="65445-179">In some scenarios, you might want to deploy an app to a container by copying its assets that are needed at run time.</span></span> <span data-ttu-id="65445-180">В этом разделе показано, как развернуть приложение вручную.</span><span class="sxs-lookup"><span data-stu-id="65445-180">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="65445-181">Перейдите в папку проекта *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="65445-181">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="65445-182">Выполните команду [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="65445-182">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="65445-183">Эта команда выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="65445-183">The command arguments:</span></span>
  * <span data-ttu-id="65445-184">собирает приложение в режиме выпуска (по умолчанию используется режим отладки);</span><span class="sxs-lookup"><span data-stu-id="65445-184">Build the app in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="65445-185">создает ресурсы в папке *published*.</span><span class="sxs-lookup"><span data-stu-id="65445-185">Create the assets in the *published* folder.</span></span>

* <span data-ttu-id="65445-186">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="65445-186">Run the app.</span></span>

  * <span data-ttu-id="65445-187">Windows:</span><span class="sxs-lookup"><span data-stu-id="65445-187">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="65445-188">Linux:</span><span class="sxs-lookup"><span data-stu-id="65445-188">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="65445-189">Перейдите по адресу `http://localhost:5000` на домашнюю страницу приложения.</span><span class="sxs-lookup"><span data-stu-id="65445-189">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="65445-190">Чтобы использовать приложение, опубликованное вручную в контейнере Docker, создайте новый *Dockerfile* и выполните команду `docker build .`, чтобы создать контейнер.</span><span class="sxs-lookup"><span data-stu-id="65445-190">To use the manually published app within a Docker container, create a new *Dockerfile* and use the `docker build .` command to build the container.</span></span>

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="65445-191">Файл Dockerfile</span><span class="sxs-lookup"><span data-stu-id="65445-191">The Dockerfile</span></span>

<span data-ttu-id="65445-192">Представленный здесь файл *Dockerfile* используется в команде `docker build`, которую вы выполняли ранее.</span><span class="sxs-lookup"><span data-stu-id="65445-192">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="65445-193">Она использует `dotnet publish` для создания и развертывания так же, как показано в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="65445-193">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /source/aspnetapp
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="65445-194">Как отмечалось в предыдущем *Dockerfile*, файлы `*.csproj` копируются и восстанавливаются в виде отдельных *слоев*.</span><span class="sxs-lookup"><span data-stu-id="65445-194">In the preceding *Dockerfile*, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="65445-195">При создании образа команда `docker build` использует встроенный кэш.</span><span class="sxs-lookup"><span data-stu-id="65445-195">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="65445-196">Если файлы `*.csproj` не изменялись с момента последнего запуска команды `docker build`, выполнять команду `dotnet restore` повторно не нужно.</span><span class="sxs-lookup"><span data-stu-id="65445-196">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="65445-197">Вместо нее используется встроенный кэш для соответствующего слоя `dotnet restore`.</span><span class="sxs-lookup"><span data-stu-id="65445-197">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="65445-198">Дополнительные сведения см. в статье [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache) (Рекомендации по созданию файлов Dockerfile).</span><span class="sxs-lookup"><span data-stu-id="65445-198">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="65445-199">Файл Dockerfile</span><span class="sxs-lookup"><span data-stu-id="65445-199">The Dockerfile</span></span>

<span data-ttu-id="65445-200">Представленный здесь файл *Dockerfile* используется в команде `docker build`, которую вы выполняли ранее.</span><span class="sxs-lookup"><span data-stu-id="65445-200">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="65445-201">Она использует `dotnet publish` для создания и развертывания так же, как показано в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="65445-201">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="65445-202">Как отмечалось в предыдущем Dockerfile, файлы `*.csproj` копируются и восстанавливаются в виде отдельных *слоев*.</span><span class="sxs-lookup"><span data-stu-id="65445-202">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="65445-203">При создании образа команда `docker build` использует встроенный кэш.</span><span class="sxs-lookup"><span data-stu-id="65445-203">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="65445-204">Если файлы `*.csproj` не изменялись с момента последнего запуска команды `docker build`, выполнять команду `dotnet restore` повторно не нужно.</span><span class="sxs-lookup"><span data-stu-id="65445-204">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="65445-205">Вместо нее используется встроенный кэш для соответствующего слоя `dotnet restore`.</span><span class="sxs-lookup"><span data-stu-id="65445-205">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="65445-206">Дополнительные сведения см. в статье [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache) (Рекомендации по созданию файлов Dockerfile).</span><span class="sxs-lookup"><span data-stu-id="65445-206">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="65445-207">Файл Dockerfile</span><span class="sxs-lookup"><span data-stu-id="65445-207">The Dockerfile</span></span>

<span data-ttu-id="65445-208">Представленный здесь файл *Dockerfile* используется в команде `docker build`, которую вы выполняли ранее.</span><span class="sxs-lookup"><span data-stu-id="65445-208">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span> <span data-ttu-id="65445-209">Она использует `dotnet publish` для создания и развертывания так же, как показано в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="65445-209">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="65445-210">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="65445-210">Additional resources</span></span>

* [<span data-ttu-id="65445-211">Команда Docker build</span><span class="sxs-lookup"><span data-stu-id="65445-211">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="65445-212">Команда Docker run</span><span class="sxs-lookup"><span data-stu-id="65445-212">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="65445-213">[Пример ASP.NET Core для Docker](https://github.com/dotnet/dotnet-docker) (который используется в этом руководстве).</span><span class="sxs-lookup"><span data-stu-id="65445-213">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="65445-214">Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="65445-214">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="65445-215">Работа со средствами Visual Studio для Docker</span><span class="sxs-lookup"><span data-stu-id="65445-215">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="65445-216">Отладка с помощью Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65445-216">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="65445-217">GC с использованием DOCKER и небольших контейнеров</span><span class="sxs-lookup"><span data-stu-id="65445-217">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="65445-218">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="65445-218">Next steps</span></span>

<span data-ttu-id="65445-219">Репозиторий Git помимо примера приложения содержит и документацию.</span><span class="sxs-lookup"><span data-stu-id="65445-219">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="65445-220">Обзор ресурсов, доступных в этом репозитории, см. [в файле README](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="65445-220">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="65445-221">Особый интерес представляют сведения о реализации протокола HTTPS:</span><span class="sxs-lookup"><span data-stu-id="65445-221">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="65445-222">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) (Разработка приложений ASP.NET Core с применением Docker по протоколу HTTPS)</span><span class="sxs-lookup"><span data-stu-id="65445-222">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)</span></span>
