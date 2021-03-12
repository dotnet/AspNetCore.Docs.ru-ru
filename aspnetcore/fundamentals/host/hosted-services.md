---
title: Фоновые задачи с размещенными службами в ASP.NET Core
author: rick-anderson
description: Узнайте, как реализовать фоновые задачи с размещенными службами в ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
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
uid: fundamentals/host/hosted-services
ms.openlocfilehash: c0492c0c5b660e1387b0d0a4f6be405ded49ee92
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587480"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="37b1c-103">Фоновые задачи с размещенными службами в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="37b1c-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="37b1c-104">Автор: [Джау Ли Хуань](https://github.com/huan086) (Jeow Li Huan)</span><span class="sxs-lookup"><span data-stu-id="37b1c-104">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="37b1c-105">В ASP.NET Core фоновые задачи реализуются как *размещенные службы*.</span><span class="sxs-lookup"><span data-stu-id="37b1c-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="37b1c-106">Размещенная служба — это класс с логикой фоновой задачи, реализующий интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="37b1c-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="37b1c-107">Этот раздел содержит три примера размещенных служб:</span><span class="sxs-lookup"><span data-stu-id="37b1c-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="37b1c-108">Фоновая задача, которая выполняется по таймеру.</span><span class="sxs-lookup"><span data-stu-id="37b1c-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="37b1c-109">Размещенная служба, которая активирует [службу с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="37b1c-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="37b1c-110">Служба с заданной областью может использовать [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="37b1c-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="37b1c-111">Очередь фоновых задач, которые выполняются последовательно.</span><span class="sxs-lookup"><span data-stu-id="37b1c-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="37b1c-112">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="37b1c-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="37b1c-113">Шаблон службы рабочей роли</span><span class="sxs-lookup"><span data-stu-id="37b1c-113">Worker Service template</span></span>

<span data-ttu-id="37b1c-114">Шаблон службы рабочей роли ASP.NET Core может служить отправной точкой для написания длительно выполняющихся приложений служб.</span><span class="sxs-lookup"><span data-stu-id="37b1c-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="37b1c-115">Приложение, созданное из шаблона рабочей службы, указывает рабочий пакет SDK в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="37b1c-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="37b1c-116">Чтобы использовать шаблон в качестве основы для приложения размещенных служб, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="37b1c-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="37b1c-117">Пакет</span><span class="sxs-lookup"><span data-stu-id="37b1c-117">Package</span></span>

<span data-ttu-id="37b1c-118">Приложение, основанное на шаблоне рабочей службы, использует пакет SDK для `Microsoft.NET.Sdk.Worker` и имеет явную ссылку на пакет [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="37b1c-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="37b1c-119">Например, ознакомьтесь с файлом проекта в примере приложения (*BackgroundTasksSample csproj*).</span><span class="sxs-lookup"><span data-stu-id="37b1c-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="37b1c-120">Для веб-приложений, использующих пакет SDK `Microsoft.NET.Sdk.Web`, ссылка на пакет [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) указывается неявным образом из общей платформы.</span><span class="sxs-lookup"><span data-stu-id="37b1c-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="37b1c-121">Явная ссылка на пакет в файле проекта приложения не требуется.</span><span class="sxs-lookup"><span data-stu-id="37b1c-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="37b1c-122">Интерфейс IHostedService</span><span class="sxs-lookup"><span data-stu-id="37b1c-122">IHostedService interface</span></span>

<span data-ttu-id="37b1c-123">Интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService> определяет два метода для объектов, которые управляются узлом:</span><span class="sxs-lookup"><span data-stu-id="37b1c-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="37b1c-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` содержит логику для запуска фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="37b1c-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="37b1c-125">*Первым* вызывается `StartAsync`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="37b1c-126">Настраивается конвейер обработки запросов приложения (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="37b1c-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="37b1c-127">Запускается сервер и активируется [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*).</span><span class="sxs-lookup"><span data-stu-id="37b1c-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="37b1c-128">Поведение по умолчанию можно изменить таким образом, чтобы `StartAsync` размещенной службы выполнялся после настройки конвейера приложения и вызова `ApplicationStarted`.</span><span class="sxs-lookup"><span data-stu-id="37b1c-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="37b1c-129">Чтобы изменить поведение по умолчанию, добавьте размещенную службу (`VideosWatcher` в следующем примере) после вызова `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
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
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* <span data-ttu-id="37b1c-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): активируется, когда происходит нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="37b1c-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="37b1c-131">`StopAsync` содержит логику для завершения фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="37b1c-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="37b1c-132">Реализуйте <xref:System.IDisposable> и [методы завершения (деструкторы)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) для освобождения неуправляемых ресурсов.</span><span class="sxs-lookup"><span data-stu-id="37b1c-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="37b1c-133">Токен отмены использует заданное по умолчанию 5-секундное время ожидания, указывающее, что процесс завершения работы больше не должен быть нормальным.</span><span class="sxs-lookup"><span data-stu-id="37b1c-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="37b1c-134">При запросе отмены происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="37b1c-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="37b1c-135">должны быть прерваны все оставшиеся фоновые операции, выполняемые приложением;</span><span class="sxs-lookup"><span data-stu-id="37b1c-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="37b1c-136">должны быть незамедлительно возвращены все методы, вызываемые в `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="37b1c-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="37b1c-137">Однако после запроса отмены выполнение задач не прекращается &mdash; вызывающий объект ожидает завершения всех задач.</span><span class="sxs-lookup"><span data-stu-id="37b1c-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="37b1c-138">Если приложение завершает работу неожиданно (например, при сбое процесса приложения), `StopAsync` может не вызываться.</span><span class="sxs-lookup"><span data-stu-id="37b1c-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="37b1c-139">Поэтому вызов методов или выполнение операций в `StopAsync` может быть невозможным.</span><span class="sxs-lookup"><span data-stu-id="37b1c-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="37b1c-140">Чтобы увеличить время ожидания завершения работы по умолчанию (пять секунд), установите следующие значения:</span><span class="sxs-lookup"><span data-stu-id="37b1c-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="37b1c-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> при использовании универсального узла.</span><span class="sxs-lookup"><span data-stu-id="37b1c-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="37b1c-142">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="37b1c-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="37b1c-143">Параметр конфигурации узла для времени ожидания завершения работы при использовании веб-узла.</span><span class="sxs-lookup"><span data-stu-id="37b1c-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="37b1c-144">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="37b1c-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="37b1c-145">Размещенная служба активируется при запуске приложения и нормально завершает работу при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="37b1c-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="37b1c-146">Если во время выполнения задачи в фоновом режиме возникает ошибка, необходимо вызвать `Dispose`, даже если `StopAsync` не вызывается.</span><span class="sxs-lookup"><span data-stu-id="37b1c-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="37b1c-147">Базовый класс BackgroundService</span><span class="sxs-lookup"><span data-stu-id="37b1c-147">BackgroundService base class</span></span>

<span data-ttu-id="37b1c-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> — это базовый класс для реализации долго выполняющегося интерфейса <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="37b1c-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="37b1c-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) вызывается для запуска фоновой службы.</span><span class="sxs-lookup"><span data-stu-id="37b1c-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="37b1c-150">Реализация возвращает значение <xref:System.Threading.Tasks.Task>, представляющее все время существования фоновой службы.</span><span class="sxs-lookup"><span data-stu-id="37b1c-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="37b1c-151">Дальнейшие службы не запустятся до тех пор, пока [ExecuteAsync не станет асинхронной](https://github.com/dotnet/extensions/issues/2149), например, путем вызова `await`.</span><span class="sxs-lookup"><span data-stu-id="37b1c-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="37b1c-152">Старайтесь не выполнять функцию в течение длительного времени, так как инициализация в `ExecuteAsync` будет заблокирована.</span><span class="sxs-lookup"><span data-stu-id="37b1c-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="37b1c-153">Блоки узлов в [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) ожидают завершения `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="37b1c-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="37b1c-154">Токен отмены активируется при вызове [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*).</span><span class="sxs-lookup"><span data-stu-id="37b1c-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="37b1c-155">При выдаче токена отмены реализация `ExecuteAsync` должна быстро завершиться для корректного завершения работы службы.</span><span class="sxs-lookup"><span data-stu-id="37b1c-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="37b1c-156">В противном случае служба некорректно завершает работу при истечении времени ожидания завершения работы.</span><span class="sxs-lookup"><span data-stu-id="37b1c-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="37b1c-157">Дополнительные сведения см. в разделе об [интерфейсе IHostedService](#ihostedservice-interface).</span><span class="sxs-lookup"><span data-stu-id="37b1c-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="37b1c-158">Фоновые задачи с заданным временем</span><span class="sxs-lookup"><span data-stu-id="37b1c-158">Timed background tasks</span></span>

<span data-ttu-id="37b1c-159">Для фоновых задач с заданным временем используется класс [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="37b1c-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="37b1c-160">Таймер запускает метод `DoWork` задачи.</span><span class="sxs-lookup"><span data-stu-id="37b1c-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="37b1c-161">Таймер отключается методом `StopAsync` и удаляется при удалении контейнера службы методом `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="37b1c-162"><xref:System.Threading.Timer> не ждет завершения предыдущего метода `DoWork`, поэтому приведенный подход может подойти не для всех сценариев.</span><span class="sxs-lookup"><span data-stu-id="37b1c-162">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="37b1c-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) используется для увеличения значений счетчика выполнения в виде атомарной операции. Благодаря этому несколько потоков не будут обновлять `executionCount` одновременно.</span><span class="sxs-lookup"><span data-stu-id="37b1c-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="37b1c-164">Служба регистрируется в `IHostBuilder.ConfigureServices` (*Program.cs*) с использованием метода расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-164">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="37b1c-165">Использование службы с заданной областью в фоновой задаче</span><span class="sxs-lookup"><span data-stu-id="37b1c-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="37b1c-166">Чтобы использовать [службы с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes) в [BackgroundService](#backgroundservice-base-class), создайте область.</span><span class="sxs-lookup"><span data-stu-id="37b1c-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="37b1c-167">Для размещенной службы по умолчанию не создается область.</span><span class="sxs-lookup"><span data-stu-id="37b1c-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="37b1c-168">Служба фоновой задачи с заданной областью содержит логику фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="37b1c-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="37b1c-169">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="37b1c-169">In the following example:</span></span>

* <span data-ttu-id="37b1c-170">Служба является асинхронной.</span><span class="sxs-lookup"><span data-stu-id="37b1c-170">The service is asynchronous.</span></span> <span data-ttu-id="37b1c-171">Метод `DoWork` возвращает значение `Task`.</span><span class="sxs-lookup"><span data-stu-id="37b1c-171">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="37b1c-172">В демонстрационных целях в методе `DoWork` ожидается задержка в десять секунд.</span><span class="sxs-lookup"><span data-stu-id="37b1c-172">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="37b1c-173">В службу вставляется <xref:Microsoft.Extensions.Logging.ILogger>.</span><span class="sxs-lookup"><span data-stu-id="37b1c-173">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="37b1c-174">Размещенная служба создает область для разрешения службы фоновой задачи с заданной областью, чтобы вызвать ее метод `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-174">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="37b1c-175">`DoWork` возвращает объект `Task`, ожидаемый в `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-175">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="37b1c-176">Службы регистрируются в `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="37b1c-176">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="37b1c-177">Размещенная служба регистрируется с использованием метода расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-177">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="37b1c-178">Фоновые задачи в очереди</span><span class="sxs-lookup"><span data-stu-id="37b1c-178">Queued background tasks</span></span>

<span data-ttu-id="37b1c-179">Очередь фоновых задач основана на <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> .NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="37b1c-179">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="37b1c-180">В следующем примере `QueueHostedService`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-180">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="37b1c-181">Метод `BackgroundProcessing` возвращает объект `Task`, ожидаемый в `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-181">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="37b1c-182">Фоновые задачи в очереди выводятся из очереди и выполняются в `BackgroundProcessing`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-182">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="37b1c-183">Рабочие элементы ожидают остановки службы через `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="37b1c-183">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="37b1c-184">Служба `MonitorLoop` обрабатывает задачи постановки в очередь для размещенной службы при выборе на устройстве ввода ключа `w`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-184">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="37b1c-185">В службу `MonitorLoop` внедряется `IBackgroundTaskQueue`.</span><span class="sxs-lookup"><span data-stu-id="37b1c-185">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="37b1c-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` вызывается для постановки рабочего элемента в очередь:</span><span class="sxs-lookup"><span data-stu-id="37b1c-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="37b1c-187">Рабочий элемент имитирует долго выполняющуюся фоновую задачу:</span><span class="sxs-lookup"><span data-stu-id="37b1c-187">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="37b1c-188">Выполняется три 5-секундных задержки (`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="37b1c-188">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="37b1c-189">Оператор `try-catch` перехватывается <xref:System.OperationCanceledException>, если задача отменена.</span><span class="sxs-lookup"><span data-stu-id="37b1c-189">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="37b1c-190">Службы регистрируются в `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="37b1c-190">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="37b1c-191">Размещенная служба регистрируется с использованием метода расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-191">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="37b1c-192">`MonitorLoop` запущен в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-192">`MonitorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="37b1c-193">В ASP.NET Core фоновые задачи реализуются как *размещенные службы*.</span><span class="sxs-lookup"><span data-stu-id="37b1c-193">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="37b1c-194">Размещенная служба — это класс с логикой фоновой задачи, реализующий интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="37b1c-194">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="37b1c-195">Этот раздел содержит три примера размещенных служб:</span><span class="sxs-lookup"><span data-stu-id="37b1c-195">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="37b1c-196">Фоновая задача, которая выполняется по таймеру.</span><span class="sxs-lookup"><span data-stu-id="37b1c-196">Background task that runs on a timer.</span></span>
* <span data-ttu-id="37b1c-197">Размещенная служба, которая активирует [службу с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="37b1c-197">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="37b1c-198">Служба с заданной областью может использовать [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="37b1c-198">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="37b1c-199">Очередь фоновых задач, которые выполняются последовательно.</span><span class="sxs-lookup"><span data-stu-id="37b1c-199">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="37b1c-200">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="37b1c-200">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="37b1c-201">Пакет</span><span class="sxs-lookup"><span data-stu-id="37b1c-201">Package</span></span>

<span data-ttu-id="37b1c-202">Добавьте ссылку на [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) или добавьте ссылку на пакет в пакет [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="37b1c-202">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="37b1c-203">Интерфейс IHostedService</span><span class="sxs-lookup"><span data-stu-id="37b1c-203">IHostedService interface</span></span>

<span data-ttu-id="37b1c-204">Размещенные службы реализуют интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="37b1c-204">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="37b1c-205">Этот интерфейс определяет два метода для объектов, которые управляются узлом:</span><span class="sxs-lookup"><span data-stu-id="37b1c-205">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="37b1c-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` содержит логику для запуска фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="37b1c-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="37b1c-207">При использовании [Web Host](xref:fundamentals/host/web-host)`StartAsync` вызывается после запуска сервера и активации [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*).</span><span class="sxs-lookup"><span data-stu-id="37b1c-207">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="37b1c-208">При использовании [универсального узла](xref:fundamentals/host/generic-host)`StartAsync` вызывается до активации `ApplicationStarted`.</span><span class="sxs-lookup"><span data-stu-id="37b1c-208">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="37b1c-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): активируется, когда происходит нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="37b1c-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="37b1c-210">`StopAsync` содержит логику для завершения фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="37b1c-210">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="37b1c-211">Реализуйте <xref:System.IDisposable> и [методы завершения (деструкторы)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) для освобождения неуправляемых ресурсов.</span><span class="sxs-lookup"><span data-stu-id="37b1c-211">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="37b1c-212">Токен отмены использует заданное по умолчанию 5-секундное время ожидания, указывающее, что процесс завершения работы больше не должен быть нормальным.</span><span class="sxs-lookup"><span data-stu-id="37b1c-212">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="37b1c-213">При запросе отмены происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="37b1c-213">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="37b1c-214">должны быть прерваны все оставшиеся фоновые операции, выполняемые приложением;</span><span class="sxs-lookup"><span data-stu-id="37b1c-214">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="37b1c-215">должны быть незамедлительно возвращены все методы, вызываемые в `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="37b1c-215">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="37b1c-216">Однако после запроса отмены выполнение задач не прекращается &mdash; вызывающий объект ожидает завершения всех задач.</span><span class="sxs-lookup"><span data-stu-id="37b1c-216">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="37b1c-217">Если приложение завершает работу неожиданно (например, при сбое процесса приложения), `StopAsync` может не вызываться.</span><span class="sxs-lookup"><span data-stu-id="37b1c-217">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="37b1c-218">Поэтому вызов методов или выполнение операций в `StopAsync` может быть невозможным.</span><span class="sxs-lookup"><span data-stu-id="37b1c-218">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="37b1c-219">Чтобы увеличить время ожидания завершения работы по умолчанию (пять секунд), установите следующие значения:</span><span class="sxs-lookup"><span data-stu-id="37b1c-219">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="37b1c-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> при использовании универсального узла.</span><span class="sxs-lookup"><span data-stu-id="37b1c-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="37b1c-221">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="37b1c-221">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="37b1c-222">Параметр конфигурации узла для времени ожидания завершения работы при использовании веб-узла.</span><span class="sxs-lookup"><span data-stu-id="37b1c-222">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="37b1c-223">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="37b1c-223">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="37b1c-224">Размещенная служба активируется при запуске приложения и нормально завершает работу при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="37b1c-224">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="37b1c-225">Если во время выполнения задачи в фоновом режиме возникает ошибка, необходимо вызвать `Dispose`, даже если `StopAsync` не вызывается.</span><span class="sxs-lookup"><span data-stu-id="37b1c-225">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="37b1c-226">Фоновые задачи с заданным временем</span><span class="sxs-lookup"><span data-stu-id="37b1c-226">Timed background tasks</span></span>

<span data-ttu-id="37b1c-227">Для фоновых задач с заданным временем используется класс [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="37b1c-227">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="37b1c-228">Таймер запускает метод `DoWork` задачи.</span><span class="sxs-lookup"><span data-stu-id="37b1c-228">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="37b1c-229">Таймер отключается методом `StopAsync` и удаляется при удалении контейнера службы методом `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-229">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="37b1c-230"><xref:System.Threading.Timer> не ждет завершения предыдущего метода `DoWork`, поэтому приведенный подход может подойти не для всех сценариев.</span><span class="sxs-lookup"><span data-stu-id="37b1c-230">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="37b1c-231">Служба зарегистрирована в `Startup.ConfigureServices` с методом расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-231">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="37b1c-232">Использование службы с заданной областью в фоновой задаче</span><span class="sxs-lookup"><span data-stu-id="37b1c-232">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="37b1c-233">Чтобы использовать [службы с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes) в `IHostedService`, создайте область.</span><span class="sxs-lookup"><span data-stu-id="37b1c-233">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="37b1c-234">Для размещенной службы по умолчанию не создается область.</span><span class="sxs-lookup"><span data-stu-id="37b1c-234">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="37b1c-235">Служба фоновой задачи с заданной областью содержит логику фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="37b1c-235">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="37b1c-236">В следующем примере в службу вставляется <xref:Microsoft.Extensions.Logging.ILogger>:</span><span class="sxs-lookup"><span data-stu-id="37b1c-236">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="37b1c-237">Размещенная служба создает область для разрешения службы фоновой задачи с заданной областью, чтобы вызвать ее метод `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-237">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="37b1c-238">Службы регистрируются в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="37b1c-238">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="37b1c-239">Реализация `IHostedService` зарегистрирована с методом расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-239">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="37b1c-240">Фоновые задачи в очереди</span><span class="sxs-lookup"><span data-stu-id="37b1c-240">Queued background tasks</span></span>

<span data-ttu-id="37b1c-241">Очередь фоновых задач основывается на методе <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> в .NET Framework 4.x ([предварительно запланировано встроить в ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="37b1c-241">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="37b1c-242">В `QueueHostedService` фоновые задачи в очереди из очереди выводятся из очереди и выполняются в качестве [BackgroundService](#backgroundservice-base-class) — базового класса для реализации длительного выполнения `IHostedService`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-242">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="37b1c-243">Службы регистрируются в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="37b1c-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="37b1c-244">Реализация `IHostedService` зарегистрирована с методом расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="37b1c-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="37b1c-245">В классе модели страницы индексов:</span><span class="sxs-lookup"><span data-stu-id="37b1c-245">In the Index page model class:</span></span>

* <span data-ttu-id="37b1c-246">`IBackgroundTaskQueue` вставляется в конструктор и присваивается `Queue`.</span><span class="sxs-lookup"><span data-stu-id="37b1c-246">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="37b1c-247"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> вставляется и присваивается `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="37b1c-247">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="37b1c-248">Фабрика используется для создания экземпляров <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, который используется для создания служб с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="37b1c-248">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="37b1c-249">Для использования `AppDbContext` приложения создается область ([служба с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes)) для записи данных из базы данных в `IBackgroundTaskQueue` (отдельная служба).</span><span class="sxs-lookup"><span data-stu-id="37b1c-249">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="37b1c-250">Если нажать кнопку **Добавить задачу** на странице индексов, выполняется метод `OnPostAddTask`.</span><span class="sxs-lookup"><span data-stu-id="37b1c-250">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="37b1c-251">`QueueBackgroundWorkItem` вызывается для постановки рабочего элемента в очередь:</span><span class="sxs-lookup"><span data-stu-id="37b1c-251">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="37b1c-252">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="37b1c-252">Additional resources</span></span>

* [<span data-ttu-id="37b1c-253">Реализация фоновых задач в микрослужбах с помощью IHostedService и класса BackgroundService</span><span class="sxs-lookup"><span data-stu-id="37b1c-253">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="37b1c-254">Выполнение фоновых задач с помощью веб-заданий в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="37b1c-254">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
