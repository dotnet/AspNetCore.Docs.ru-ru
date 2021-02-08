---
title: Конфигурация в .NET Core
author: rick-anderson
description: Узнайте, как использовать API конфигурации для настройки приложения ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: 62c9d1a58e0f771d91e2bc57f39ec5ebb25baaed
ms.sourcegitcommit: 37186f76e4a50d7fb7389026dd0e5e234b51ebb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99541372"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="e81d5-103">Конфигурация в .NET Core</span><span class="sxs-lookup"><span data-stu-id="e81d5-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="e81d5-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="e81d5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e81d5-105">Настройка в ASP.NET Core выполняется с помощью одного или нескольких [поставщиков конфигурации](#cp).</span><span class="sxs-lookup"><span data-stu-id="e81d5-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="e81d5-106">Поставщики конфигурации получают данные конфигурации в парах "ключ-значение" из различных источников:</span><span class="sxs-lookup"><span data-stu-id="e81d5-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="e81d5-107">файлов параметров, таких как *appsettings.json* ;</span><span class="sxs-lookup"><span data-stu-id="e81d5-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="e81d5-108">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="e81d5-108">Environment variables</span></span>
* <span data-ttu-id="e81d5-109">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="e81d5-109">Azure Key Vault</span></span>
* <span data-ttu-id="e81d5-110">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="e81d5-110">Azure App Configuration</span></span>
* <span data-ttu-id="e81d5-111">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="e81d5-111">Command-line arguments</span></span>
* <span data-ttu-id="e81d5-112">пользовательские поставщики, установленные или созданные;</span><span class="sxs-lookup"><span data-stu-id="e81d5-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="e81d5-113">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="e81d5-113">Directory files</span></span>
* <span data-ttu-id="e81d5-114">объектов .NET в памяти;</span><span class="sxs-lookup"><span data-stu-id="e81d5-114">In-memory .NET objects</span></span>

<span data-ttu-id="e81d5-115">В этом разделе приведены сведения о конфигурации в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e81d5-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="e81d5-116">Сведения об использовании конфигурации в консольных приложениях см. в статье [Конфигурация .NET](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="e81d5-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="e81d5-117">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e81d5-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="e81d5-118">Конфигурация по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e81d5-118">Default configuration</span></span>

<span data-ttu-id="e81d5-119">Веб-приложения ASP.NET Core, созданные с помощью [dotnet new](/dotnet/core/tools/dotnet-new) или Visual Studio, создают следующий код:</span><span class="sxs-lookup"><span data-stu-id="e81d5-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="e81d5-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> предоставляет конфигурацию по умолчанию для приложения в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="e81d5-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="e81d5-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  добавляет существующий `IConfiguration` в качестве источника.</span><span class="sxs-lookup"><span data-stu-id="e81d5-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="e81d5-122">В случае конфигурации по умолчанию добавляет конфигурацию [узла](#hvac) и задает ее в качестве первого источника для конфигурации _приложения_.</span><span class="sxs-lookup"><span data-stu-id="e81d5-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="e81d5-123">Файл [appsettings.json](#appsettingsjson), использующий [поставщик конфигурации JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e81d5-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="e81d5-124">Файл *appsettings.* `Environment` *.json*, использующий [поставщик конфигурации JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e81d5-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="e81d5-125">Например, *appsettings*.***Production\*\*_._json* и *appsettings*.\*\*\*Development** _._json\*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="e81d5-126">[Секреты приложения](xref:security/app-secrets), когда приложение выполняется в среде `Development`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="e81d5-127">Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="e81d5-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="e81d5-128">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line).</span><span class="sxs-lookup"><span data-stu-id="e81d5-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="e81d5-129">Поставщики конфигурации, добавленные позже, переопределяют предыдущие параметры ключа.</span><span class="sxs-lookup"><span data-stu-id="e81d5-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="e81d5-130">Например, если `MyKey` задан в *appsettings.json* и в среде, используется значение среды.</span><span class="sxs-lookup"><span data-stu-id="e81d5-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="e81d5-131">При использовании поставщиков конфигурации по умолчанию [поставщик конфигурации командной строки](#clcp) переопределяет остальных поставщиков.</span><span class="sxs-lookup"><span data-stu-id="e81d5-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="e81d5-132">Дополнительные сведения о `CreateDefaultBuilder` см. в разделе [Параметры сборщика по умолчанию](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="e81d5-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="e81d5-133">В следующем коде отображаются включенные поставщики конфигурации в том порядке, в котором они были добавлены:</span><span class="sxs-lookup"><span data-stu-id="e81d5-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="e81d5-134">Рассмотрим следующий файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="e81d5-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="e81d5-135">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="e81d5-136"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> по умолчанию загружает конфигурацию в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="e81d5-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="e81d5-137">*appsettings.* `Environment` *.json* : Например, файлы *appsettings*.***Production\*\*_._json* и *appsettings*.\*\*\*Development** _._json\*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="e81d5-138">Версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="e81d5-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="e81d5-139">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="e81d5-140">Значения в *appsettings*.`Environment`.*json* переопределяют ключи в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e81d5-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="e81d5-141">Например, по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="e81d5-141">For example, by default:</span></span>

* <span data-ttu-id="e81d5-142">В среде разработки конфигурация *appsettings*.***Development** _._json* перезаписывает значения, найденные в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e81d5-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="e81d5-143">В рабочей среде конфигурация *appsettings*.***Production** _._json* перезаписывает значения, найденные в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e81d5-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="e81d5-144">Например, при развертывании приложения в Azure.</span><span class="sxs-lookup"><span data-stu-id="e81d5-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="e81d5-145">Привязка иерархических данных конфигурации с помощью шаблона параметров</span><span class="sxs-lookup"><span data-stu-id="e81d5-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="e81d5-146">С помощью конфигурации [по умолчанию](#default) файлы *appsettings.json* и *appsettings.* `Environment` *.json* включаются с помощью [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="e81d5-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="e81d5-147">Изменения, внесенные в файлы *appsettings.json* и *appsettings.* `Environment` *.json* ***после*** запуска приложения, считываются [поставщиком конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="e81d5-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="e81d5-148">Сведения о добавлении дополнительных файлов конфигурации JSON см. в разделе [Поставщик конфигурации JSON](#jcp) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="e81d5-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="e81d5-149">Объединение коллекций служб</span><span class="sxs-lookup"><span data-stu-id="e81d5-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="e81d5-150">Безопасность и секреты пользователей</span><span class="sxs-lookup"><span data-stu-id="e81d5-150">Security and user secrets</span></span>

<span data-ttu-id="e81d5-151">Рекомендации по данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-151">Configuration data guidelines:</span></span>

* <span data-ttu-id="e81d5-152">Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.</span><span class="sxs-lookup"><span data-stu-id="e81d5-152">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="e81d5-153">Хранить секреты во время разработки можно с помощью [диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="e81d5-153">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="e81d5-154">Не используйте секреты рабочей среды в средах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="e81d5-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="e81d5-155">Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.</span><span class="sxs-lookup"><span data-stu-id="e81d5-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="e81d5-156">[По умолчанию](#default) источник конфигурации секрета пользователя регистрируется после источников конфигурации JSON.</span><span class="sxs-lookup"><span data-stu-id="e81d5-156">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="e81d5-157">Таким образом, ключи секретов пользователя имеют приоритет над ключами в *appsettings.json* и *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-157">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="e81d5-158">Дополнительные сведения о хранении паролей или других конфиденциальных данных:</span><span class="sxs-lookup"><span data-stu-id="e81d5-158">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="e81d5-159"><xref:security/app-secrets>. Содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="e81d5-159"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="e81d5-160">Диспетчер секретов использует [поставщик конфигурации файла](#fcp) для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.</span><span class="sxs-lookup"><span data-stu-id="e81d5-160">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="e81d5-161">В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e81d5-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="e81d5-162">Для получения дополнительной информации см. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-162">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="e81d5-163">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="e81d5-163">Environment variables</span></span>

<span data-ttu-id="e81d5-164">При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пар "ключ-значение" в переменных среды после чтения *appsettings.json* , *appsettings.* `Environment` *.json* и [секретов пользователя](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="e81d5-164">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="e81d5-165">Поэтому значения ключей, считанные из среды, переопределяют значения, считанные из *appsettings.json* , *appsettings.* `Environment` *.json* и секретов пользователя.</span><span class="sxs-lookup"><span data-stu-id="e81d5-165">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="e81d5-166">Следующие команды `set`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-166">The following `set` commands:</span></span>

* <span data-ttu-id="e81d5-167">Задают ключи и значения среды в [предыдущем примере](#appsettingsjson) в Windows.</span><span class="sxs-lookup"><span data-stu-id="e81d5-167">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="e81d5-168">Проверяют параметры при использовании [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="e81d5-168">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="e81d5-169">Команда `dotnet run` должна выполняться в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="e81d5-169">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="e81d5-170">Предыдущие параметры среды:</span><span class="sxs-lookup"><span data-stu-id="e81d5-170">The preceding environment settings:</span></span>

* <span data-ttu-id="e81d5-171">Задаются только в процессах, запускаемых из командного окна, в котором они были установлены.</span><span class="sxs-lookup"><span data-stu-id="e81d5-171">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="e81d5-172">Не будут считываться браузерами, запущенными в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e81d5-172">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="e81d5-173">Следующие команды [setx](/windows-server/administration/windows-commands/setx) можно использовать для задания ключей и значений среды в Windows.</span><span class="sxs-lookup"><span data-stu-id="e81d5-173">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="e81d5-174">В отличие от `set`, параметры `setx` сохраняются.</span><span class="sxs-lookup"><span data-stu-id="e81d5-174">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="e81d5-175">`/M` задает переменную в системной среде.</span><span class="sxs-lookup"><span data-stu-id="e81d5-175">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="e81d5-176">Если параметр `/M` не используется, задается переменная среды пользователя.</span><span class="sxs-lookup"><span data-stu-id="e81d5-176">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="e81d5-177">Чтобы проверить, что предыдущие команды переопределяют *appsettings.json* и *appsettings.* `Environment` *.json*, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="e81d5-177">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="e81d5-178">В Visual Studio: Выйдите из среды Visual Studio и перезапустите ее.</span><span class="sxs-lookup"><span data-stu-id="e81d5-178">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="e81d5-179">В CLI: Откройте новое командное окно и введите `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-179">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="e81d5-180">Вызовите <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> со строкой, чтобы указать префикс для переменных среды:</span><span class="sxs-lookup"><span data-stu-id="e81d5-180">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="e81d5-181">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="e81d5-181">In the preceding code:</span></span>

* <span data-ttu-id="e81d5-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` добавляется после [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="e81d5-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="e81d5-183">Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="e81d5-183">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="e81d5-184">Переменные среды, установленные с префиксом `MyCustomPrefix_`, переопределяют [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="e81d5-184">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="e81d5-185">Сюда входят переменные среды без префикса.</span><span class="sxs-lookup"><span data-stu-id="e81d5-185">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="e81d5-186">Префикс отделяется при создании пары конфигурации "ключ-значение".</span><span class="sxs-lookup"><span data-stu-id="e81d5-186">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="e81d5-187">Следующие команды проверяют пользовательский префикс:</span><span class="sxs-lookup"><span data-stu-id="e81d5-187">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="e81d5-188">[Конфигурация по умолчанию](#default) загружает переменные среды и аргументы командной строки с префиксом `DOTNET_` и `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-188">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="e81d5-189">Префиксы `DOTNET_` и `ASPNETCORE_` используются ASP.NET Core для [конфигурации узла и приложения](xref:fundamentals/host/generic-host#host-configuration), но не для конфигурации пользователя.</span><span class="sxs-lookup"><span data-stu-id="e81d5-189">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="e81d5-190">Дополнительные сведения о конфигурации узла и приложения см. в разделе [Универсальный узел .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e81d5-190">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="e81d5-191">В [Службе приложений Azure](https://azure.microsoft.com/services/app-service/) выберите **Новый параметр приложения** на странице **Параметры > Конфигурация**.</span><span class="sxs-lookup"><span data-stu-id="e81d5-191">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="e81d5-192">Параметры приложения Службы приложений Azure:</span><span class="sxs-lookup"><span data-stu-id="e81d5-192">Azure App Service application settings are:</span></span>

* <span data-ttu-id="e81d5-193">Шифруются, когда они неактивны, и передаются по зашифрованному каналу.</span><span class="sxs-lookup"><span data-stu-id="e81d5-193">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="e81d5-194">Предоставляются как переменные среды.</span><span class="sxs-lookup"><span data-stu-id="e81d5-194">Exposed as environment variables.</span></span>

<span data-ttu-id="e81d5-195">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="e81d5-195">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="e81d5-196">Сведения о строках подключения к базе данных Azure см. в разделе [Префиксы строк подключения](#constr).</span><span class="sxs-lookup"><span data-stu-id="e81d5-196">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="e81d5-197">Именование переменных среды</span><span class="sxs-lookup"><span data-stu-id="e81d5-197">Naming of environment variables</span></span>

<span data-ttu-id="e81d5-198">Имена переменных среды отражают структуру файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e81d5-198">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="e81d5-199">Каждый элемент в иерархии отделяется двойным символом подчеркивания (предпочтительно) или двоеточием.</span><span class="sxs-lookup"><span data-stu-id="e81d5-199">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="e81d5-200">Если структура элемента включает массив, индекс массива должен рассматриваться как дополнительное имя элемента в этом пути.</span><span class="sxs-lookup"><span data-stu-id="e81d5-200">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="e81d5-201">Рассмотрим следующий файл *appsettings.json* и его эквивалентные значения, представленные в виде переменных среды:</span><span class="sxs-lookup"><span data-stu-id="e81d5-201">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

**appsettings.json**

```json
{
    "SmtpServer": "smtp.example.com",
    "Logging": [
        {
            "Name": "ToEmail",
            "Level": "Critical",
            "Args": {
                "FromAddress": "MySystem@example.com",
                "ToAddress": "SRE@example.com"
            }
        },
        {
            "Name": "ToConsole",
            "Level": "Information"
        }
    ]
}
```

<span data-ttu-id="e81d5-202">**Переменные среды**</span><span class="sxs-lookup"><span data-stu-id="e81d5-202">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="e81d5-203">Переменные среды, заданные в launchSettings.json</span><span class="sxs-lookup"><span data-stu-id="e81d5-203">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="e81d5-204">Переменные среды, заданные в *launchSettings.json*, переопределяют переменные, заданные в системной среде.</span><span class="sxs-lookup"><span data-stu-id="e81d5-204">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="e81d5-205">Командная строка</span><span class="sxs-lookup"><span data-stu-id="e81d5-205">Command-line</span></span>

<span data-ttu-id="e81d5-206">При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пар "ключ-значение" аргументов командной строки после следующих источников конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-206">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="e81d5-207">Файлы *appsettings.json* и *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-207">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="e81d5-208">[Секреты приложения](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="e81d5-208">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="e81d5-209">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="e81d5-209">Environment variables.</span></span>

<span data-ttu-id="e81d5-210">По [умолчанию](#default) значения конфигурации, заданные для значений конфигурации переопределения в командной строке, задаются для всех остальных поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-210">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="e81d5-211">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="e81d5-211">Command-line arguments</span></span>

<span data-ttu-id="e81d5-212">Следующая команда задает ключи и значения с помощью `=`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-212">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="e81d5-213">Следующая команда задает ключи и значения с помощью `/`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-213">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="e81d5-214">Следующая команда задает ключи и значения с помощью `--`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-214">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="e81d5-215">Значение ключа:</span><span class="sxs-lookup"><span data-stu-id="e81d5-215">The key value:</span></span>

* <span data-ttu-id="e81d5-216">Должно соответствовать `=`, или ключ должен иметь префикс `--` или `/`, когда значение следует за пробелом.</span><span class="sxs-lookup"><span data-stu-id="e81d5-216">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="e81d5-217">Является обязательным, если используется параметр `=`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-217">Isn't required if `=` is used.</span></span> <span data-ttu-id="e81d5-218">Например, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-218">For example, `MySetting=`.</span></span>

<span data-ttu-id="e81d5-219">В рамках одной и той же команды не смешивайте пары "ключ-значение" аргумента командной строки, которые используют `=` с парами "ключ-значение" с пробелом.</span><span class="sxs-lookup"><span data-stu-id="e81d5-219">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="e81d5-220">Сопоставления переключений</span><span class="sxs-lookup"><span data-stu-id="e81d5-220">Switch mappings</span></span>

<span data-ttu-id="e81d5-221">Сопоставление параметров позволяет указать логику замены имен **ключей**.</span><span class="sxs-lookup"><span data-stu-id="e81d5-221">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="e81d5-222">Предоставьте словарь замены параметров для метода <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-222">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="e81d5-223">В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки.</span><span class="sxs-lookup"><span data-stu-id="e81d5-223">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="e81d5-224">Если ключ в командной строке находится в словаре, значение словаря передается обратно, чтобы установить пару "ключ-значение" в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-224">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="e81d5-225">Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).</span><span class="sxs-lookup"><span data-stu-id="e81d5-225">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="e81d5-226">Правила ключей из словаря сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="e81d5-226">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="e81d5-227">Параметры должны начинаться с `-` или `--`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-227">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="e81d5-228">Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.</span><span class="sxs-lookup"><span data-stu-id="e81d5-228">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="e81d5-229">Чтобы использовать словарь сопоставлений параметров, передайте его в вызов `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-229">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="e81d5-230">В следующем коде показаны ключевые значения для замененных ключей:</span><span class="sxs-lookup"><span data-stu-id="e81d5-230">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="e81d5-231">Следующая команда проверяет замену ключа:</span><span class="sxs-lookup"><span data-stu-id="e81d5-231">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="e81d5-232">Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны.</span><span class="sxs-lookup"><span data-stu-id="e81d5-232">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="e81d5-233">Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные параметры, и нет возможности передать словарь сопоставления параметров в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-233">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e81d5-234">Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.</span><span class="sxs-lookup"><span data-stu-id="e81d5-234">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="e81d5-235">Иерархическая модель конфигурации</span><span class="sxs-lookup"><span data-stu-id="e81d5-235">Hierarchical configuration data</span></span>

<span data-ttu-id="e81d5-236">API конфигурации считывает иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-236">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="e81d5-237">[Пример скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="e81d5-237">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="e81d5-238">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-238">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="e81d5-239">Предпочтительный способ чтения иерархических данных конфигурации — использование шаблона параметров.</span><span class="sxs-lookup"><span data-stu-id="e81d5-239">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="e81d5-240">Дополнительные сведения см. в разделе [Привязка иерархических данных конфигурации](#optpat) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="e81d5-240">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="e81d5-241">Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-241"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="e81d5-242">Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="e81d5-242">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="e81d5-243">Ключи и значения конфигурации</span><span class="sxs-lookup"><span data-stu-id="e81d5-243">Configuration keys and values</span></span>

<span data-ttu-id="e81d5-244">Ключи конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-244">Configuration keys:</span></span>

* <span data-ttu-id="e81d5-245">Не учитывают регистр.</span><span class="sxs-lookup"><span data-stu-id="e81d5-245">Are case-insensitive.</span></span> <span data-ttu-id="e81d5-246">Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.</span><span class="sxs-lookup"><span data-stu-id="e81d5-246">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="e81d5-247">Если ключ и значение заданы более чем в одном поставщике конфигурации, используется значение из последнего добавленного поставщика.</span><span class="sxs-lookup"><span data-stu-id="e81d5-247">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="e81d5-248">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="e81d5-248">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="e81d5-249">Иерархические ключи</span><span class="sxs-lookup"><span data-stu-id="e81d5-249">Hierarchical keys</span></span>
  * <span data-ttu-id="e81d5-250">При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="e81d5-250">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="e81d5-251">В переменных среды разделитель-двоеточие может не работать на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="e81d5-251">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="e81d5-252">Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие — `:`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-252">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="e81d5-253">В Azure Key Vault иерархические ключи используют `--` в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="e81d5-253">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="e81d5-254">[Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) автоматически заменяет `--` на `:` при загрузке секретов в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-254">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="e81d5-255"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-255">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="e81d5-256">Привязка массива описана в разделе [Привязка массива к классу](#boa).</span><span class="sxs-lookup"><span data-stu-id="e81d5-256">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="e81d5-257">Значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-257">Configuration values:</span></span>

* <span data-ttu-id="e81d5-258">Представляют собой строки.</span><span class="sxs-lookup"><span data-stu-id="e81d5-258">Are strings.</span></span>
* <span data-ttu-id="e81d5-259">Значение NULL не может храниться в конфигурации или быть привязанным к объектам.</span><span class="sxs-lookup"><span data-stu-id="e81d5-259">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="e81d5-260">Поставщики конфигурации</span><span class="sxs-lookup"><span data-stu-id="e81d5-260">Configuration providers</span></span>

<span data-ttu-id="e81d5-261">В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e81d5-261">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="e81d5-262">Поставщик</span><span class="sxs-lookup"><span data-stu-id="e81d5-262">Provider</span></span> | <span data-ttu-id="e81d5-263">Предоставляет конфигурацию из</span><span class="sxs-lookup"><span data-stu-id="e81d5-263">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="e81d5-264">Поставщик конфигурации Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e81d5-264">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="e81d5-265">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="e81d5-265">Azure Key Vault</span></span> |
| [<span data-ttu-id="e81d5-266">Поставщик конфигурации приложения Azure</span><span class="sxs-lookup"><span data-stu-id="e81d5-266">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="e81d5-267">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="e81d5-267">Azure App Configuration</span></span> |
| [<span data-ttu-id="e81d5-268">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="e81d5-268">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="e81d5-269">Параметры командной строки</span><span class="sxs-lookup"><span data-stu-id="e81d5-269">Command-line parameters</span></span> |
| [<span data-ttu-id="e81d5-270">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="e81d5-270">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="e81d5-271">Источник пользователя</span><span class="sxs-lookup"><span data-stu-id="e81d5-271">Custom source</span></span> |
| [<span data-ttu-id="e81d5-272">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="e81d5-272">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="e81d5-273">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="e81d5-273">Environment variables</span></span> |
| [<span data-ttu-id="e81d5-274">Поставщик конфигурации файла</span><span class="sxs-lookup"><span data-stu-id="e81d5-274">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="e81d5-275">Файлы INI, JSON и XML</span><span class="sxs-lookup"><span data-stu-id="e81d5-275">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="e81d5-276">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="e81d5-276">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="e81d5-277">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="e81d5-277">Directory files</span></span> |
| [<span data-ttu-id="e81d5-278">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="e81d5-278">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="e81d5-279">Коллекции оперативной памяти</span><span class="sxs-lookup"><span data-stu-id="e81d5-279">In-memory collections</span></span> |
| [<span data-ttu-id="e81d5-280">Секреты пользователя</span><span class="sxs-lookup"><span data-stu-id="e81d5-280">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="e81d5-281">Файл в каталоге профиля пользователя</span><span class="sxs-lookup"><span data-stu-id="e81d5-281">File in the user profile directory</span></span> |

<span data-ttu-id="e81d5-282">Источники конфигурации считываются в том порядке, в котором указываются их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-282">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="e81d5-283">Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.</span><span class="sxs-lookup"><span data-stu-id="e81d5-283">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="e81d5-284">Типичная последовательность поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-284">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="e81d5-285">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="e81d5-285">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="e81d5-286">Секреты пользователя</span><span class="sxs-lookup"><span data-stu-id="e81d5-286">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="e81d5-287">Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="e81d5-287">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="e81d5-288">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e81d5-288">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="e81d5-289">Общепринятой практикой является добавление поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="e81d5-289">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="e81d5-290">Предыдущая последовательность поставщиков используется в [конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="e81d5-290">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="e81d5-291">Префиксы строк подключения</span><span class="sxs-lookup"><span data-stu-id="e81d5-291">Connection string prefixes</span></span>

<span data-ttu-id="e81d5-292">API конфигурации имеет особые правила обработки для четырех переменных среды строки подключения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-292">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="e81d5-293">Эти строки подключения участвуют в настройке строк подключения Azure для среды приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-293">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="e81d5-294">Переменные среды с префиксами, указанными в таблице, загружаются в приложение с [конфигурацией по умолчанию](#default) или если префикс не предоставлен для `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-294">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="e81d5-295">Префикс строки подключения</span><span class="sxs-lookup"><span data-stu-id="e81d5-295">Connection string prefix</span></span> | <span data-ttu-id="e81d5-296">Поставщик</span><span class="sxs-lookup"><span data-stu-id="e81d5-296">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="e81d5-297">Поставщик пользователя</span><span class="sxs-lookup"><span data-stu-id="e81d5-297">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="e81d5-298">MySQL</span><span class="sxs-lookup"><span data-stu-id="e81d5-298">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="e81d5-299">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="e81d5-299">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="e81d5-300">SQL Server</span><span class="sxs-lookup"><span data-stu-id="e81d5-300">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="e81d5-301">Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="e81d5-301">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="e81d5-302">Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="e81d5-302">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="e81d5-303">Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).</span><span class="sxs-lookup"><span data-stu-id="e81d5-303">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="e81d5-304">Ключ переменной среды</span><span class="sxs-lookup"><span data-stu-id="e81d5-304">Environment variable key</span></span> | <span data-ttu-id="e81d5-305">Преобразованный ключ конфигурации</span><span class="sxs-lookup"><span data-stu-id="e81d5-305">Converted configuration key</span></span> | <span data-ttu-id="e81d5-306">Запись конфигурации поставщика</span><span class="sxs-lookup"><span data-stu-id="e81d5-306">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e81d5-307">Запись конфигурации не создана.</span><span class="sxs-lookup"><span data-stu-id="e81d5-307">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e81d5-308">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-308">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e81d5-309">Значение: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="e81d5-309">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e81d5-310">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-310">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e81d5-311">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="e81d5-311">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e81d5-312">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-312">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e81d5-313">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="e81d5-313">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="e81d5-314">Поставщик конфигурации файла</span><span class="sxs-lookup"><span data-stu-id="e81d5-314">File configuration provider</span></span>

<span data-ttu-id="e81d5-315"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы.</span><span class="sxs-lookup"><span data-stu-id="e81d5-315"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="e81d5-316">Следующие поставщики конфигурации являются производными от `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-316">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="e81d5-317">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="e81d5-317">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="e81d5-318">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="e81d5-318">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="e81d5-319">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="e81d5-319">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="e81d5-320">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="e81d5-320">INI configuration provider</span></span>

<span data-ttu-id="e81d5-321"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-321">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="e81d5-322">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-322">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="e81d5-323">В приведенном выше коде параметры в *MyIniConfig.ini* и *MyIniConfig*.`Environment`.*ini* переопределяются параметрами в следующих поставщиках:</span><span class="sxs-lookup"><span data-stu-id="e81d5-323">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* <span data-ttu-id="e81d5-324">[Поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="e81d5-324">[Environment variables configuration provider](#evcp)</span></span>
* <span data-ttu-id="e81d5-325">[Поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="e81d5-325">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e81d5-326">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-326">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="e81d5-327">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-327">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="e81d5-328">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="e81d5-328">JSON configuration provider</span></span>

<span data-ttu-id="e81d5-329"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ-значение" JSON-файла.</span><span class="sxs-lookup"><span data-stu-id="e81d5-329">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="e81d5-330">Перегрузки могут указывать:</span><span class="sxs-lookup"><span data-stu-id="e81d5-330">Overloads can specify:</span></span>

* <span data-ttu-id="e81d5-331">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="e81d5-331">Whether the file is optional.</span></span>
* <span data-ttu-id="e81d5-332">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="e81d5-332">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="e81d5-333">Рассмотрим следующий код.</span><span class="sxs-lookup"><span data-stu-id="e81d5-333">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="e81d5-334">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="e81d5-334">The preceding code:</span></span>

* <span data-ttu-id="e81d5-335">Настраивает поставщик конфигурации JSON для загрузки файла *MyConfig.json* со следующими параметрами:</span><span class="sxs-lookup"><span data-stu-id="e81d5-335">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="e81d5-336">`optional: true`. Файл является необязательным.</span><span class="sxs-lookup"><span data-stu-id="e81d5-336">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="e81d5-337">`reloadOnChange: true`: При сохранении изменений файл перезагружается.</span><span class="sxs-lookup"><span data-stu-id="e81d5-337">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="e81d5-338">Считывает [поставщиков конфигурации по умолчанию](#default) до файла *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-338">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="e81d5-339">Параметры в файле *MyConfig.json* переопределяют параметр в поставщиках конфигурации по умолчанию, включая [поставщик конфигурация переменных среды](#evcp) и [поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="e81d5-339">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e81d5-340">Обычно ***не*** требуется, чтобы пользовательские файлы JSON переопределяли значения, заданные в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="e81d5-340">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e81d5-341">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-341">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="e81d5-342">В приведенном выше коде параметры в файлах *MyConfig.json* и *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-342">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="e81d5-343">Переопределяют параметры в файлах *appsettings.json* и *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-343">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="e81d5-344">Переопределяются параметрами в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="e81d5-344">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e81d5-345">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-345">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="e81d5-346">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-346">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="e81d5-347">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="e81d5-347">XML configuration provider</span></span>

<span data-ttu-id="e81d5-348"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-348">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="e81d5-349">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="e81d5-350">В приведенном выше коде параметры в *MyXMLFile.xml* и *MyXMLFile*.`Environment`.*xml* переопределяются параметрами в следующих поставщиках:</span><span class="sxs-lookup"><span data-stu-id="e81d5-350">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* <span data-ttu-id="e81d5-351">[Поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="e81d5-351">[Environment variables configuration provider](#evcp)</span></span>
* <span data-ttu-id="e81d5-352">[Поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="e81d5-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e81d5-353">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="e81d5-354">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="e81d5-355">Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.</span><span class="sxs-lookup"><span data-stu-id="e81d5-355">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="e81d5-356">Следующий код считывает предыдущий файл конфигурации и отображает ключи и значения:</span><span class="sxs-lookup"><span data-stu-id="e81d5-356">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="e81d5-357">Атрибуты можно использовать для предоставления значений.</span><span class="sxs-lookup"><span data-stu-id="e81d5-357">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="e81d5-358">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-358">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e81d5-359">key:attribute</span><span class="sxs-lookup"><span data-stu-id="e81d5-359">key:attribute</span></span>
* <span data-ttu-id="e81d5-360">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="e81d5-360">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="e81d5-361">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="e81d5-361">Key-per-file configuration provider</span></span>

<span data-ttu-id="e81d5-362"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-362">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="e81d5-363">Ключ является именем файла.</span><span class="sxs-lookup"><span data-stu-id="e81d5-363">The key is the file name.</span></span> <span data-ttu-id="e81d5-364">Значение содержит содержимое файла.</span><span class="sxs-lookup"><span data-stu-id="e81d5-364">The value contains the file's contents.</span></span> <span data-ttu-id="e81d5-365">Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.</span><span class="sxs-lookup"><span data-stu-id="e81d5-365">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="e81d5-366">Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-366">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="e81d5-367">Значение параметра `directoryPath` должно быть абсолютным путем к файлам.</span><span class="sxs-lookup"><span data-stu-id="e81d5-367">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="e81d5-368">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="e81d5-368">Overloads permit specifying:</span></span>

* <span data-ttu-id="e81d5-369">`Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.</span><span class="sxs-lookup"><span data-stu-id="e81d5-369">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="e81d5-370">Обязательно ли указывать каталог и путь к каталогу.</span><span class="sxs-lookup"><span data-stu-id="e81d5-370">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="e81d5-371">Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов.</span><span class="sxs-lookup"><span data-stu-id="e81d5-371">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="e81d5-372">Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-372">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="e81d5-373">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-373">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="e81d5-374">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="e81d5-374">Memory configuration provider</span></span>

<span data-ttu-id="e81d5-375"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-375">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="e81d5-376">Следующий код добавляет коллекцию памяти в систему конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-376">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="e81d5-377">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются перечисленные выше параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-377">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="e81d5-378">В предыдущем коде `config.AddInMemoryCollection(Dict)` добавляется после [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="e81d5-378">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="e81d5-379">Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="e81d5-379">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="e81d5-380">В разделе [Привязка массива](#boa) вы найдете еще один пример использования `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-380">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="e81d5-381">GetValue</span><span class="sxs-lookup"><span data-stu-id="e81d5-381">GetValue</span></span>

<span data-ttu-id="e81d5-382">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип:</span><span class="sxs-lookup"><span data-stu-id="e81d5-382">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="e81d5-383">В приведенном выше коде, если `NumberKey` отсутствует в конфигурации, используется значение по умолчанию `99`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-383">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="e81d5-384">GetSection, GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="e81d5-384">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="e81d5-385">В следующих примерах мы рассмотрим следующий файл *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-385">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="e81d5-386">Следующий код добавляет *MySubsection.json* к поставщикам конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-386">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="e81d5-387">GetSection</span><span class="sxs-lookup"><span data-stu-id="e81d5-387">GetSection</span></span>

<span data-ttu-id="e81d5-388">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) возвращает подраздел конфигурации с указанным ключом подраздела.</span><span class="sxs-lookup"><span data-stu-id="e81d5-388">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="e81d5-389">Следующий код возвращает значения для `section1`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-389">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="e81d5-390">Следующий код возвращает значения для `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-390">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="e81d5-391">Значение `GetSection` никогда не возвращает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-391">`GetSection` never returns `null`.</span></span> <span data-ttu-id="e81d5-392">Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-392">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="e81d5-393">Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется.</span><span class="sxs-lookup"><span data-stu-id="e81d5-393">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="e81d5-394"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.</span><span class="sxs-lookup"><span data-stu-id="e81d5-394">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="e81d5-395">GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="e81d5-395">GetChildren and Exists</span></span>

<span data-ttu-id="e81d5-396">Следующий код вызывает [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) и возвращает значения для `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-396">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="e81d5-397">Приведенный выше код вызывает [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) для проверки существования раздела:</span><span class="sxs-lookup"><span data-stu-id="e81d5-397">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="e81d5-398">Привязка массива</span><span class="sxs-lookup"><span data-stu-id="e81d5-398">Bind an array</span></span>

<span data-ttu-id="e81d5-399">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) поддерживает привязку массивов к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-399">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="e81d5-400">Любой формат массива, который предоставляет сегмент числового ключа способен привязать массив к массиву класса [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="e81d5-400">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="e81d5-401">Рассмотрим *MyArray.json* из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="e81d5-401">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="e81d5-402">Следующий код добавляет *MyArray.json* к поставщикам конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-402">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="e81d5-403">Следующий код считывает конфигурацию и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="e81d5-403">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="e81d5-404">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="e81d5-404">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="e81d5-405">В предыдущих выходных данных индекс 3 имеет значение `value40`, соответствующее `"4": "value40",` в *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-405">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="e81d5-406">Индексы привязанного массива непрерывны и не привязаны к индексу ключа конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-406">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="e81d5-407">Модуль привязки конфигурации не поддерживает привязку значений NULL или создание записей NULL в связанных объектах</span><span class="sxs-lookup"><span data-stu-id="e81d5-407">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="e81d5-408">Следующий код загружает конфигурацию `array:entries` с помощью метода расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>:</span><span class="sxs-lookup"><span data-stu-id="e81d5-408">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="e81d5-409">Следующий код считывает конфигурацию в `arrayDict` `Dictionary` и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="e81d5-409">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="e81d5-410">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="e81d5-410">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="e81d5-411">Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-411">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="e81d5-412">При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы используются для выполнения итерации данных конфигурации при создании объекта.</span><span class="sxs-lookup"><span data-stu-id="e81d5-412">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="e81d5-413">Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.</span><span class="sxs-lookup"><span data-stu-id="e81d5-413">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="e81d5-414">Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который считывает пару "ключ-значение" индекса &num;3.</span><span class="sxs-lookup"><span data-stu-id="e81d5-414">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="e81d5-415">Рассмотрим следующий файл *Value3.json* из примера загрузки:</span><span class="sxs-lookup"><span data-stu-id="e81d5-415">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="e81d5-416">Следующий код включает конфигурацию для *Value3.json* и `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-416">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="e81d5-417">Следующий код считывает предыдущую конфигурацию и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="e81d5-417">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="e81d5-418">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="e81d5-418">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="e81d5-419">Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.</span><span class="sxs-lookup"><span data-stu-id="e81d5-419">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="e81d5-420">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="e81d5-420">Custom configuration provider</span></span>

<span data-ttu-id="e81d5-421">Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="e81d5-421">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="e81d5-422">Поставщик имеет следующие характеристики.</span><span class="sxs-lookup"><span data-stu-id="e81d5-422">The provider has the following characteristics:</span></span>

* <span data-ttu-id="e81d5-423">База данных в памяти EF используется для демонстрационных целей.</span><span class="sxs-lookup"><span data-stu-id="e81d5-423">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="e81d5-424">Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-424">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="e81d5-425">Поставщик считывает таблицу базы данных в конфигурации при запуске.</span><span class="sxs-lookup"><span data-stu-id="e81d5-425">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="e81d5-426">Поставщик не запрашивает базу данных для каждого ключа.</span><span class="sxs-lookup"><span data-stu-id="e81d5-426">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="e81d5-427">Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-427">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="e81d5-428">Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e81d5-428">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="e81d5-429">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-429">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="e81d5-430">Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.</span><span class="sxs-lookup"><span data-stu-id="e81d5-430">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="e81d5-431">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-431">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="e81d5-432">Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-432">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="e81d5-433">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-433">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="e81d5-434">Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-434">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="e81d5-435">Поставщик конфигурации инициализирует пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="e81d5-435">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="e81d5-436">Поскольку [конфигурационные ключи не учитывают регистр](#keys), словарь, используемый для инициализации базы данных, создается с помощью функции сравнения без учета регистра ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="e81d5-436">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="e81d5-437">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-437">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="e81d5-438">Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-438">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="e81d5-439">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-439">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="e81d5-440">В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-440">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="e81d5-441">Доступ к конфигурации во время запуска</span><span class="sxs-lookup"><span data-stu-id="e81d5-441">Access configuration in Startup</span></span>

<span data-ttu-id="e81d5-442">В следующем коде отображаются данные конфигурации в методах `Startup`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-442">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="e81d5-443">Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="e81d5-443">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="e81d5-444">Конфигурация доступа в RazorPages</span><span class="sxs-lookup"><span data-stu-id="e81d5-444">Access configuration in Razor Pages</span></span>

<span data-ttu-id="e81d5-445">В следующем коде отображаются данные конфигурации в RazorPage:</span><span class="sxs-lookup"><span data-stu-id="e81d5-445">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="e81d5-446">В следующем коде `MyOptions` добавляется в контейнер службы с помощью интерфейса <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> и привязывается к конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-446">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="e81d5-447">В следующей разметке для разрешения и вывода значений параметров используется директива Razor [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="e81d5-447">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="e81d5-448">Доступ к конфигурации в файле представления MVC</span><span class="sxs-lookup"><span data-stu-id="e81d5-448">Access configuration in a MVC view file</span></span>

<span data-ttu-id="e81d5-449">В следующем коде отображаются данные конфигурации в представлении MVC:</span><span class="sxs-lookup"><span data-stu-id="e81d5-449">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="e81d5-450">Настройка параметров с помощью делегата</span><span class="sxs-lookup"><span data-stu-id="e81d5-450">Configure options with a delegate</span></span>

<span data-ttu-id="e81d5-451">Параметры, настроенные в делегате, переопределяют значения, заданные в поставщиках конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-451">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="e81d5-452">Настройка параметров с помощью делегата демонстрируется в примере 2 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-452">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="e81d5-453">В приведенном ниже коде в контейнер службы добавляется служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-453">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="e81d5-454">Она использует делегат для настройки значений для `MyOptions`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-454">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="e81d5-455">Следующий код отображает значения параметров:</span><span class="sxs-lookup"><span data-stu-id="e81d5-455">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="e81d5-456">В предыдущем примере значения `Option1` и `Option2` задаются в файле *appsettings.json* , а затем переопределяются настроенным делегатом.</span><span class="sxs-lookup"><span data-stu-id="e81d5-456">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="e81d5-457">Конфигурация узла и приложения</span><span class="sxs-lookup"><span data-stu-id="e81d5-457">Host versus app configuration</span></span>

<span data-ttu-id="e81d5-458">Перед настройкой и запуском приложения настройте и запустите *узел*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-458">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="e81d5-459">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="e81d5-459">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="e81d5-460">Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="e81d5-460">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="e81d5-461">Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-461">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="e81d5-462">Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-462">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="e81d5-463">Конфигурация узла по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e81d5-463">Default host configuration</span></span>

<span data-ttu-id="e81d5-464">Подробные сведения о конфигурации по умолчанию при использовании [веб-узла](xref:fundamentals/host/web-host) см. в [разделе о версии ASP.NET Core 2.2 в этой статье](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="e81d5-464">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="e81d5-465">Существуют следующие способы предоставления конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="e81d5-465">Host configuration is provided from:</span></span>
  * <span data-ttu-id="e81d5-466">Переменные среды с префиксом `DOTNET_` (например, `DOTNET_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="e81d5-466">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="e81d5-467">Префикс (`DOTNET_`) исключается при загрузке пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-467">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="e81d5-468">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e81d5-468">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="e81d5-469">Устанавливается конфигурация веб-узла по умолчанию (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="e81d5-469">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="e81d5-470">Kestrel используется в качестве веб-сервера и настраивается с помощью поставщиков конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-470">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="e81d5-471">Добавьте ПО промежуточного слоя фильтрации узлов.</span><span class="sxs-lookup"><span data-stu-id="e81d5-471">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="e81d5-472">Если переменной среды `ASPNETCORE_FORWARDEDHEADERS_ENABLED` присвоено значение `true`, добавьте ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="e81d5-472">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="e81d5-473">Включите интеграцию служб IIS.</span><span class="sxs-lookup"><span data-stu-id="e81d5-473">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="e81d5-474">Другая конфигурация</span><span class="sxs-lookup"><span data-stu-id="e81d5-474">Other configuration</span></span>

<span data-ttu-id="e81d5-475">Этот раздел относится только к *конфигурации приложений*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-475">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="e81d5-476">Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="e81d5-476">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="e81d5-477">*launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="e81d5-477">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="e81d5-478">В <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-478">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="e81d5-479">В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.</span><span class="sxs-lookup"><span data-stu-id="e81d5-479">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="e81d5-480">*web.config* — это файл конфигурации сервера, описанный в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="e81d5-480">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="e81d5-481">Переменные среды, заданные в *launchSettings.json*, переопределяют переменные, заданные в системной среде.</span><span class="sxs-lookup"><span data-stu-id="e81d5-481">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="e81d5-482">См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-482">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="e81d5-483">Добавление конфигурации из внешней сборки</span><span class="sxs-lookup"><span data-stu-id="e81d5-483">Add configuration from an external assembly</span></span>

<span data-ttu-id="e81d5-484">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-484">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="e81d5-485">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-485">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e81d5-486">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e81d5-486">Additional resources</span></span>

* [<span data-ttu-id="e81d5-487">Исходный код конфигурации</span><span class="sxs-lookup"><span data-stu-id="e81d5-487">Configuration source code</span></span>](https://github.com/dotnet/runtime/tree/master/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e81d5-488">Конфигурация приложения в ASP.NET Core основана на парах "ключ — значение", установленных *поставщиками конфигурации*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-488">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="e81d5-489">Поставщики конфигурации получают данные конфигурации в парах "ключ — значение" из различных источников:</span><span class="sxs-lookup"><span data-stu-id="e81d5-489">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="e81d5-490">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="e81d5-490">Azure Key Vault</span></span>
* <span data-ttu-id="e81d5-491">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="e81d5-491">Azure App Configuration</span></span>
* <span data-ttu-id="e81d5-492">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="e81d5-492">Command-line arguments</span></span>
* <span data-ttu-id="e81d5-493">а также пользовательские поставщики (устанавливаемые или создаваемые).</span><span class="sxs-lookup"><span data-stu-id="e81d5-493">Custom providers (installed or created)</span></span>
* <span data-ttu-id="e81d5-494">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="e81d5-494">Directory files</span></span>
* <span data-ttu-id="e81d5-495">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="e81d5-495">Environment variables</span></span>
* <span data-ttu-id="e81d5-496">объектов .NET в памяти;</span><span class="sxs-lookup"><span data-stu-id="e81d5-496">In-memory .NET objects</span></span>
* <span data-ttu-id="e81d5-497">файлов параметров;</span><span class="sxs-lookup"><span data-stu-id="e81d5-497">Settings files</span></span>

<span data-ttu-id="e81d5-498">Пакеты конфигурации для распространенных вариантов провайдеров конфигурации ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) включаются в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="e81d5-498">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="e81d5-499">В приведенных ниже и представленных в образце приложения примерах кода используется пространство имен <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="e81d5-499">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="e81d5-500">*Шаблон параметров* является расширением конфигурации основных понятий, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="e81d5-500">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="e81d5-501">Параметры используют классы для представления групп связанных настроек.</span><span class="sxs-lookup"><span data-stu-id="e81d5-501">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="e81d5-502">Для получения дополнительной информации см. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="e81d5-503">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e81d5-503">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="e81d5-504">Конфигурация узла и приложения</span><span class="sxs-lookup"><span data-stu-id="e81d5-504">Host versus app configuration</span></span>

<span data-ttu-id="e81d5-505">Перед настройкой и запуском приложения настройте и запустите *узел*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-505">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="e81d5-506">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="e81d5-506">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="e81d5-507">Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="e81d5-507">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="e81d5-508">Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-508">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="e81d5-509">Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-509">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="e81d5-510">Другая конфигурация</span><span class="sxs-lookup"><span data-stu-id="e81d5-510">Other configuration</span></span>

<span data-ttu-id="e81d5-511">Этот раздел относится только к *конфигурации приложений*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-511">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="e81d5-512">Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="e81d5-512">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="e81d5-513">*launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="e81d5-513">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="e81d5-514">В <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-514">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="e81d5-515">В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.</span><span class="sxs-lookup"><span data-stu-id="e81d5-515">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="e81d5-516">*web.config* — это файл конфигурации сервера, описанный в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="e81d5-516">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="e81d5-517">См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-517">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="e81d5-518">Конфигурация по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e81d5-518">Default configuration</span></span>

<span data-ttu-id="e81d5-519">Веб-приложения на основе шаблонов [dotnet new](/dotnet/core/tools/dotnet-new) ASP.NET Core вызывают <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> при создании узла.</span><span class="sxs-lookup"><span data-stu-id="e81d5-519">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="e81d5-520">`CreateDefaultBuilder` предоставляет конфигурацию по умолчанию для приложения в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="e81d5-520">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="e81d5-521">Приведенные ниже сведения относятся к приложениям, использующим [веб-узел](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="e81d5-521">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="e81d5-522">Подробные сведения о конфигурации по умолчанию при использовании [универсального узла](xref:fundamentals/host/generic-host) см. в [последней версии этой статьи](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="e81d5-522">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="e81d5-523">Существуют следующие способы предоставления конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="e81d5-523">Host configuration is provided from:</span></span>
  * <span data-ttu-id="e81d5-524">Переменные среды с префиксом `ASPNETCORE_` (например, `ASPNETCORE_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e81d5-524">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="e81d5-525">Префикс (`ASPNETCORE_`) исключается при загрузке пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-525">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="e81d5-526">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e81d5-526">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="e81d5-527">Конфигурация приложения предоставляется из следующих ресурсов:</span><span class="sxs-lookup"><span data-stu-id="e81d5-527">App configuration is provided from:</span></span>
  * <span data-ttu-id="e81d5-528">Файл *appsettings.json* , использующий [поставщик конфигурации файлов](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e81d5-528">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="e81d5-529">Файл *appsettings.{Environment}.json*, использующий [поставщик конфигурации файлов](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e81d5-529">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="e81d5-530">[секреты пользователя](xref:security/app-secrets), когда приложение выполняется в среде `Development` с использованием начальных сборок;</span><span class="sxs-lookup"><span data-stu-id="e81d5-530">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="e81d5-531">Переменные среды, использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e81d5-531">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="e81d5-532">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e81d5-532">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="e81d5-533">Безопасность</span><span class="sxs-lookup"><span data-stu-id="e81d5-533">Security</span></span>

<span data-ttu-id="e81d5-534">Применяйте описанные ниже методики для защиты конфиденциальных данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-534">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="e81d5-535">Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.</span><span class="sxs-lookup"><span data-stu-id="e81d5-535">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="e81d5-536">Не используйте секреты рабочей среды в средах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="e81d5-536">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="e81d5-537">Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.</span><span class="sxs-lookup"><span data-stu-id="e81d5-537">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="e81d5-538">Дополнительные сведения см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="e81d5-538">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="e81d5-539"><xref:security/app-secrets>. Содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="e81d5-539"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="e81d5-540">Диспетчер секретов использует поставщик конфигурации файла для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.</span><span class="sxs-lookup"><span data-stu-id="e81d5-540">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="e81d5-541">Поставщик конфигурации файлов описан ниже в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="e81d5-541">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="e81d5-542">В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e81d5-542">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="e81d5-543">Для получения дополнительной информации см. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-543">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="e81d5-544">Иерархическая модель конфигурации</span><span class="sxs-lookup"><span data-stu-id="e81d5-544">Hierarchical configuration data</span></span>

<span data-ttu-id="e81d5-545">API конфигурации способен поддерживать иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-545">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="e81d5-546">В следующем файле JSON существуют четыре ключа в структурированной иерархии двух разделов.</span><span class="sxs-lookup"><span data-stu-id="e81d5-546">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="e81d5-547">При считывании файла в конфигурацию для сохранения исходной иерархической структуры данных источника конфигурации создаются уникальные ключи.</span><span class="sxs-lookup"><span data-stu-id="e81d5-547">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="e81d5-548">Разделы и ключи преобразовываются в плоскую структуру с использованием двоеточия (`:`) для сохранения исходной структуры.</span><span class="sxs-lookup"><span data-stu-id="e81d5-548">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="e81d5-549">section0:key0</span><span class="sxs-lookup"><span data-stu-id="e81d5-549">section0:key0</span></span>
* <span data-ttu-id="e81d5-550">section0:key1</span><span class="sxs-lookup"><span data-stu-id="e81d5-550">section0:key1</span></span>
* <span data-ttu-id="e81d5-551">section1:key0</span><span class="sxs-lookup"><span data-stu-id="e81d5-551">section1:key0</span></span>
* <span data-ttu-id="e81d5-552">section1:key1</span><span class="sxs-lookup"><span data-stu-id="e81d5-552">section1:key1</span></span>

<span data-ttu-id="e81d5-553">Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-553"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="e81d5-554">Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="e81d5-554">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="e81d5-555">Соглашения</span><span class="sxs-lookup"><span data-stu-id="e81d5-555">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="e81d5-556">Источники и поставщики</span><span class="sxs-lookup"><span data-stu-id="e81d5-556">Sources and providers</span></span>

<span data-ttu-id="e81d5-557">При запуске приложения источники конфигурации считываются в порядке, в котором были указаны их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-557">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="e81d5-558">Поставщики конфигурации, которые реализуют обнаружение изменений, имеют возможность перезагрузить конфигурацию при изменении базовых параметров.</span><span class="sxs-lookup"><span data-stu-id="e81d5-558">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="e81d5-559">Так, поставщик файла конфигурации (подробнее о нем далее в этой статье) и [поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) реализуют обнаружение изменений.</span><span class="sxs-lookup"><span data-stu-id="e81d5-559">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="e81d5-560">Объект <xref:Microsoft.Extensions.Configuration.IConfiguration> доступен в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e81d5-561"><xref:Microsoft.Extensions.Configuration.IConfiguration> можно внедрить в <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Razor Pages или <xref:Microsoft.AspNetCore.Mvc.Controller> MVC, чтобы получить конфигурацию для класса.</span><span class="sxs-lookup"><span data-stu-id="e81d5-561"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="e81d5-562">В следующих примерах поле `_config` используется для доступа к значениям конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-562">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="e81d5-563">Поставщики конфигурации не могут использовать контейнер DI, так как он недоступен при настройке узла.</span><span class="sxs-lookup"><span data-stu-id="e81d5-563">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="e81d5-564">Клавиши</span><span class="sxs-lookup"><span data-stu-id="e81d5-564">Keys</span></span>

<span data-ttu-id="e81d5-565">В ключах конфигурации приняты следующие соглашения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-565">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="e81d5-566">В ключах не учитывается регистр символов.</span><span class="sxs-lookup"><span data-stu-id="e81d5-566">Keys are case-insensitive.</span></span> <span data-ttu-id="e81d5-567">Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.</span><span class="sxs-lookup"><span data-stu-id="e81d5-567">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="e81d5-568">Если значение для одного и того же ключа установлено одним и тем же или разными поставщиками конфигурации, последним значением, установленным на ключе, является используемое значение.</span><span class="sxs-lookup"><span data-stu-id="e81d5-568">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="e81d5-569">Дополнительные сведения о повторяющихся ключах JSON см. в [этой проблеме на GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="e81d5-569">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="e81d5-570">Иерархические ключи</span><span class="sxs-lookup"><span data-stu-id="e81d5-570">Hierarchical keys</span></span>
  * <span data-ttu-id="e81d5-571">При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="e81d5-571">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="e81d5-572">В переменных среды разделитель-двоеточие может не работать на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="e81d5-572">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="e81d5-573">Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие.</span><span class="sxs-lookup"><span data-stu-id="e81d5-573">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="e81d5-574">В хранилище ключей Azure иерархические ключи используют `--` (два дефиса) в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="e81d5-574">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="e81d5-575">Чтобы заменить дефисы двоеточием, при загрузке секретов в конфигурацию приложения напишите код.</span><span class="sxs-lookup"><span data-stu-id="e81d5-575">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="e81d5-576"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-576">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="e81d5-577">Привязка массива описана в разделе [Привязка массива к классу](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="e81d5-577">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="e81d5-578">Значения</span><span class="sxs-lookup"><span data-stu-id="e81d5-578">Values</span></span>

<span data-ttu-id="e81d5-579">В значениях конфигурации учитываются следующие соглашения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-579">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="e81d5-580">Значения являются строками.</span><span class="sxs-lookup"><span data-stu-id="e81d5-580">Values are strings.</span></span>
* <span data-ttu-id="e81d5-581">Значение NULL не может храниться в конфигурации или быть привязанным к объектам.</span><span class="sxs-lookup"><span data-stu-id="e81d5-581">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="e81d5-582">Поставщики</span><span class="sxs-lookup"><span data-stu-id="e81d5-582">Providers</span></span>

<span data-ttu-id="e81d5-583">В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e81d5-583">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="e81d5-584">Поставщик</span><span class="sxs-lookup"><span data-stu-id="e81d5-584">Provider</span></span> | <span data-ttu-id="e81d5-585">Предоставляет конфигурацию из &hellip;</span><span class="sxs-lookup"><span data-stu-id="e81d5-585">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="e81d5-586">[Поставщик конфигурации хранилища ключей Azure](xref:security/key-vault-configuration) (раздел *Безопасность*)</span><span class="sxs-lookup"><span data-stu-id="e81d5-586">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="e81d5-587">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="e81d5-587">Azure Key Vault</span></span> |
| <span data-ttu-id="e81d5-588">[Поставщик конфигурации приложений Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (документация Azure)</span><span class="sxs-lookup"><span data-stu-id="e81d5-588">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="e81d5-589">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="e81d5-589">Azure App Configuration</span></span> |
| [<span data-ttu-id="e81d5-590">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="e81d5-590">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="e81d5-591">Параметры командной строки</span><span class="sxs-lookup"><span data-stu-id="e81d5-591">Command-line parameters</span></span> |
| [<span data-ttu-id="e81d5-592">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="e81d5-592">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="e81d5-593">Источник пользователя</span><span class="sxs-lookup"><span data-stu-id="e81d5-593">Custom source</span></span> |
| [<span data-ttu-id="e81d5-594">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="e81d5-594">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="e81d5-595">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="e81d5-595">Environment variables</span></span> |
| [<span data-ttu-id="e81d5-596">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="e81d5-596">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="e81d5-597">Файлы (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="e81d5-597">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="e81d5-598">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="e81d5-598">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="e81d5-599">Справочные файлы</span><span class="sxs-lookup"><span data-stu-id="e81d5-599">Directory files</span></span> |
| [<span data-ttu-id="e81d5-600">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="e81d5-600">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="e81d5-601">Коллекции оперативной памяти</span><span class="sxs-lookup"><span data-stu-id="e81d5-601">In-memory collections</span></span> |
| <span data-ttu-id="e81d5-602">[Секреты пользователей](xref:security/app-secrets) (раздел *Безопасность*)</span><span class="sxs-lookup"><span data-stu-id="e81d5-602">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="e81d5-603">Файл в каталоге профиля пользователя</span><span class="sxs-lookup"><span data-stu-id="e81d5-603">File in the user profile directory</span></span> |

<span data-ttu-id="e81d5-604">Источники конфигурации считываются в том порядке, в котором при запуске указываются их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-604">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="e81d5-605">Поставщики конфигурации в этом разделе описаны в алфавитном порядке, а не в необходимом вам порядке в коде.</span><span class="sxs-lookup"><span data-stu-id="e81d5-605">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="e81d5-606">Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.</span><span class="sxs-lookup"><span data-stu-id="e81d5-606">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="e81d5-607">Типичная последовательность поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-607">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="e81d5-608">Файлы ( *appsettings.json* , *appsettings.{Environment}.json*, где `{Environment}` — это текущая среда размещения приложения)</span><span class="sxs-lookup"><span data-stu-id="e81d5-608">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. <span data-ttu-id="e81d5-609">[Azure Key Vault](xref:security/key-vault-configuration);</span><span class="sxs-lookup"><span data-stu-id="e81d5-609">[Azure Key Vault](xref:security/key-vault-configuration)</span></span>
1. <span data-ttu-id="e81d5-610">[Секреты пользователя](xref:security/app-secrets) (только в среде разработки)</span><span class="sxs-lookup"><span data-stu-id="e81d5-610">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="e81d5-611">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="e81d5-611">Environment variables</span></span>
1. <span data-ttu-id="e81d5-612">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="e81d5-612">Command-line arguments</span></span>

<span data-ttu-id="e81d5-613">Общепринятой практикой является размещение поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="e81d5-613">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="e81d5-614">Предыдущая последовательность поставщиков используется при инициализации нового построителя узла с помощью `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-614">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e81d5-615">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="e81d5-615">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="e81d5-616">Настройка построителя узла с помощью UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="e81d5-616">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="e81d5-617">Чтобы настроить построитель узла, вызовите <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> в построителе узла с конфигурацией.</span><span class="sxs-lookup"><span data-stu-id="e81d5-617">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="e81d5-618">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="e81d5-618">ConfigureAppConfiguration</span></span>

<span data-ttu-id="e81d5-619">Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать поставщики конфигурации приложения в дополнение к тем, которые автоматически добавлены `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-619">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="e81d5-620">Переопределение предыдущей конфигурации с помощью аргументов командной строки</span><span class="sxs-lookup"><span data-stu-id="e81d5-620">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="e81d5-621">Чтобы предоставить конфигурацию приложения, которую можно переопределить с помощью аргументов командной строки, вызовите метод `AddCommandLine` последним:</span><span class="sxs-lookup"><span data-stu-id="e81d5-621">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="e81d5-622">Удаление поставщиков, добавленных CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="e81d5-622">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="e81d5-623">Чтобы удалить поставщиков, добавленных `CreateDefaultBuilder`, сначала вызовите [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) в [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources):</span><span class="sxs-lookup"><span data-stu-id="e81d5-623">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="e81d5-624">Использование конфигурации во время запуска приложения</span><span class="sxs-lookup"><span data-stu-id="e81d5-624">Consume configuration during app startup</span></span>

<span data-ttu-id="e81d5-625">Конфигурация, предоставленная приложению в `ConfigureAppConfiguration`, доступна во время запуска приложения, включая `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-625">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e81d5-626">Дополнительные сведения см. в разделе [Доступ к конфигурации во время запуска](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="e81d5-626">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="e81d5-627">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="e81d5-627">Command-line Configuration Provider</span></span>

<span data-ttu-id="e81d5-628"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пары "ключ — значение" аргумента командной строки в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-628">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="e81d5-629">Чтобы активировать конфигурацию командной строки, вызывается метод расширения <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> для экземпляра <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-629">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e81d5-630">`AddCommandLine` вызывается автоматически при вызове `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-630">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="e81d5-631">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="e81d5-631">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="e81d5-632">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="e81d5-632">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="e81d5-633">дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="e81d5-633">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="e81d5-634">[Секреты пользователя](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="e81d5-634">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="e81d5-635">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="e81d5-635">Environment variables.</span></span>

<span data-ttu-id="e81d5-636">`CreateDefaultBuilder` добавляет последним поставщика конфигурации командной строки.</span><span class="sxs-lookup"><span data-stu-id="e81d5-636">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="e81d5-637">Аргументы командной строки передаются в набор конфигурации переопределения среды выполнения, установленной другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="e81d5-637">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="e81d5-638">`CreateDefaultBuilder` действует, когда создается узел.</span><span class="sxs-lookup"><span data-stu-id="e81d5-638">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="e81d5-639">Поэтому конфигурация командной строки, активированная с помощью `CreateDefaultBuilder`, может повлиять на настройку узла.</span><span class="sxs-lookup"><span data-stu-id="e81d5-639">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="e81d5-640">Для приложений на основе шаблонов ASP.NET Core метод `AddCommandLine` уже был вызван методом `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-640">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e81d5-641">Чтобы добавить дополнительные поставщики конфигурации и обеспечить возможность переопределения конфигурации от этих поставщиков с помощью аргументов командной строки, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration` и вызовите `AddCommandLine` в последнюю очередь.</span><span class="sxs-lookup"><span data-stu-id="e81d5-641">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="e81d5-642">**Пример**</span><span class="sxs-lookup"><span data-stu-id="e81d5-642">**Example**</span></span>

<span data-ttu-id="e81d5-643">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-643">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="e81d5-644">Откройте командную строку в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="e81d5-644">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="e81d5-645">Поставьте аргумент командной строки в команду `dotnet run`: `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-645">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="e81d5-646">После запуска приложения откройте браузер в приложении по адресу `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-646">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="e81d5-647">Обратите внимание, что вывод содержит пару "ключ — значение" для аргумента командной строки конфигурации, предоставленного для `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-647">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="e81d5-648">Аргументы</span><span class="sxs-lookup"><span data-stu-id="e81d5-648">Arguments</span></span>

<span data-ttu-id="e81d5-649">Значение должно соответствовать знаку равенства (`=`), или ключ должен иметь префикс (`--` или `/`), когда значение следует за пробелом.</span><span class="sxs-lookup"><span data-stu-id="e81d5-649">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="e81d5-650">Значение не требуется, если используется знак равенства (например, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="e81d5-650">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="e81d5-651">Префикс ключа</span><span class="sxs-lookup"><span data-stu-id="e81d5-651">Key prefix</span></span>               | <span data-ttu-id="e81d5-652">Пример</span><span class="sxs-lookup"><span data-stu-id="e81d5-652">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="e81d5-653">Без префикса</span><span class="sxs-lookup"><span data-stu-id="e81d5-653">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="e81d5-654">Два дефиса (`--`)</span><span class="sxs-lookup"><span data-stu-id="e81d5-654">Two dashes (`--`)</span></span>        | <span data-ttu-id="e81d5-655">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="e81d5-655">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="e81d5-656">Прямая косая черта (`/`)</span><span class="sxs-lookup"><span data-stu-id="e81d5-656">Forward slash (`/`)</span></span>      | <span data-ttu-id="e81d5-657">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="e81d5-657">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="e81d5-658">В рамках одной и той же команды не смешивайте пары "ключ — значение" аргумента командной строки, которые используют знак равенства, с парами "ключ — значение", которые используют пробел.</span><span class="sxs-lookup"><span data-stu-id="e81d5-658">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="e81d5-659">Примеры команд.</span><span class="sxs-lookup"><span data-stu-id="e81d5-659">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="e81d5-660">Сопоставления переключений</span><span class="sxs-lookup"><span data-stu-id="e81d5-660">Switch mappings</span></span>

<span data-ttu-id="e81d5-661">Сопоставление параметров позволяет указать логику замены имен ключей.</span><span class="sxs-lookup"><span data-stu-id="e81d5-661">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="e81d5-662">Когда вручную создается конфигурация с помощью <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, методу <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> можно предоставить словарь сопоставления переключений.</span><span class="sxs-lookup"><span data-stu-id="e81d5-662">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="e81d5-663">В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки.</span><span class="sxs-lookup"><span data-stu-id="e81d5-663">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="e81d5-664">Если ключ в командной строке находится в словаре, значение словаря (замена ключа) передается обратно, чтобы установить пару "ключ — значение" в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-664">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="e81d5-665">Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).</span><span class="sxs-lookup"><span data-stu-id="e81d5-665">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="e81d5-666">Правила ключей из словаря сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="e81d5-666">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="e81d5-667">Переключения должны начинаться с дефиса (`-`) или двойного дефиса (`--`).</span><span class="sxs-lookup"><span data-stu-id="e81d5-667">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="e81d5-668">Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.</span><span class="sxs-lookup"><span data-stu-id="e81d5-668">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="e81d5-669">Создайте словарь сопоставления переключений.</span><span class="sxs-lookup"><span data-stu-id="e81d5-669">Create a switch mappings dictionary.</span></span> <span data-ttu-id="e81d5-670">В следующем примере создаются два сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="e81d5-670">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="e81d5-671">При сборке узла вызовите `AddCommandLine` со словарем сопоставлений переключений:</span><span class="sxs-lookup"><span data-stu-id="e81d5-671">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="e81d5-672">Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны.</span><span class="sxs-lookup"><span data-stu-id="e81d5-672">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="e81d5-673">Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные переключения, и нет возможности передать словарь сопоставления переключений в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-673">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e81d5-674">Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.</span><span class="sxs-lookup"><span data-stu-id="e81d5-674">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="e81d5-675">Созданный словарь сопоставлений переключений содержит данные, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="e81d5-675">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="e81d5-676">Ключ</span><span class="sxs-lookup"><span data-stu-id="e81d5-676">Key</span></span>       | <span data-ttu-id="e81d5-677">Значение</span><span class="sxs-lookup"><span data-stu-id="e81d5-677">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="e81d5-678">Если ключи сопоставления переключений используются при запуске приложения, конфигурация принимает значение конфигурации в ключе, предоставленном в словаре.</span><span class="sxs-lookup"><span data-stu-id="e81d5-678">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="e81d5-679">После выполнения предыдущей команды конфигурация содержит значения, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="e81d5-679">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="e81d5-680">Ключ</span><span class="sxs-lookup"><span data-stu-id="e81d5-680">Key</span></span>               | <span data-ttu-id="e81d5-681">Значение</span><span class="sxs-lookup"><span data-stu-id="e81d5-681">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="e81d5-682">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="e81d5-682">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="e81d5-683"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пары "ключ — значение" переменной среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-683">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="e81d5-684">Чтобы активировать конфигурацию переменных среды, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-684">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="e81d5-685">[Служба приложений Azure](https://azure.microsoft.com/services/app-service/) позволяет задать переменные среды на портале Azure, который может переопределить конфигурацию приложения, используя поставщик конфигурации переменных среды.</span><span class="sxs-lookup"><span data-stu-id="e81d5-685">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="e81d5-686">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="e81d5-686">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="e81d5-687">`AddEnvironmentVariables` используется для загрузки переменных среды, имеющих префикс `ASPNETCORE_`, для [конфигурации узла](#host-versus-app-configuration) при инициализации нового построителя узла с [веб-узлом](xref:fundamentals/host/web-host) и вызове `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-687">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="e81d5-688">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="e81d5-688">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="e81d5-689">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="e81d5-689">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="e81d5-690">конфигурация приложения на основе переменных среды без префикса путем вызова `AddEnvironmentVariables` без префикса;</span><span class="sxs-lookup"><span data-stu-id="e81d5-690">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="e81d5-691">дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="e81d5-691">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="e81d5-692">[Секреты пользователя](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="e81d5-692">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="e81d5-693">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="e81d5-693">Command-line arguments.</span></span>

<span data-ttu-id="e81d5-694">Поставщик конфигурации переменных среды вызывается после выполнения настройки с помощью секретов пользователя и файлов *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-694">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="e81d5-695">Вызов поставщика в этой позиции разрешает чтение переменных среды выполнения, чтобы переопределить конфигурацию, заданную секретом пользователя и файлом *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-695">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="e81d5-696">Чтобы добавить конфигурацию приложения из дополнительных переменных среды, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration`, а затем вызовите `AddEnvironmentVariables` с префиксом:</span><span class="sxs-lookup"><span data-stu-id="e81d5-696">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="e81d5-697">Вызовите `AddEnvironmentVariables` последним, чтобы разрешить переменным среды с заданным префиксом переопределять значения от других поставщиков.</span><span class="sxs-lookup"><span data-stu-id="e81d5-697">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="e81d5-698">**Пример**</span><span class="sxs-lookup"><span data-stu-id="e81d5-698">**Example**</span></span>

<span data-ttu-id="e81d5-699">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-699">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="e81d5-700">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-700">Run the sample app.</span></span> <span data-ttu-id="e81d5-701">Откройте в приложении браузер с адресом `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-701">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="e81d5-702">Обратите внимание, что выходные данные содержат пару "ключ — значение" для переменной среды `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-702">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="e81d5-703">Значение отражает среду, в которой выполняется приложение, обычно при локальном запуске это `Development`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-703">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="e81d5-704">Чтобы список переменных среды, отображаемый приложением, был коротким, приложение фильтрует переменные среды.</span><span class="sxs-lookup"><span data-stu-id="e81d5-704">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="e81d5-705">См. пример файла *Pages/Index.cshtml.cs* приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-705">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="e81d5-706">Чтобы просмотреть все переменные среды, доступные для приложения, измените значение `FilteredConfiguration` в *Pages/Index.cshtml.cs* на следующее:</span><span class="sxs-lookup"><span data-stu-id="e81d5-706">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="e81d5-707">Префиксы</span><span class="sxs-lookup"><span data-stu-id="e81d5-707">Prefixes</span></span>

<span data-ttu-id="e81d5-708">Переменные среды, которые загружаются в конфигурацию приложения, фильтруются при добавлении префикса к методу `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-708">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="e81d5-709">Например, чтобы отфильтровать переменные среды по префиксу `CUSTOM_`, введите префикс поставщику конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-709">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="e81d5-710">Префикс отделяется при создании пары конфигурации "ключ — значение".</span><span class="sxs-lookup"><span data-stu-id="e81d5-710">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="e81d5-711">При создании построителя узла конфигурация узла предоставляется переменными среды.</span><span class="sxs-lookup"><span data-stu-id="e81d5-711">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="e81d5-712">Дополнительные сведения о префиксе, используемом для этих переменных среды, см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="e81d5-712">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="e81d5-713">**Префиксы строк подключения**</span><span class="sxs-lookup"><span data-stu-id="e81d5-713">**Connection string prefixes**</span></span>

<span data-ttu-id="e81d5-714">API конфигурации имеет специальные правила обработки для четырех строк подключения переменных среды, связанных с настройкой строк подключения Azure для среды приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-714">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="e81d5-715">Если префикс не указан в `AddEnvironmentVariables`, переменные среды с префиксами, указанными в таблице, загружаются в приложение.</span><span class="sxs-lookup"><span data-stu-id="e81d5-715">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="e81d5-716">Префикс строки подключения</span><span class="sxs-lookup"><span data-stu-id="e81d5-716">Connection string prefix</span></span> | <span data-ttu-id="e81d5-717">Поставщик</span><span class="sxs-lookup"><span data-stu-id="e81d5-717">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="e81d5-718">Поставщик пользователя</span><span class="sxs-lookup"><span data-stu-id="e81d5-718">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="e81d5-719">MySQL</span><span class="sxs-lookup"><span data-stu-id="e81d5-719">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="e81d5-720">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="e81d5-720">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="e81d5-721">SQL Server</span><span class="sxs-lookup"><span data-stu-id="e81d5-721">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="e81d5-722">Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="e81d5-722">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="e81d5-723">Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="e81d5-723">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="e81d5-724">Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).</span><span class="sxs-lookup"><span data-stu-id="e81d5-724">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="e81d5-725">Ключ переменной среды</span><span class="sxs-lookup"><span data-stu-id="e81d5-725">Environment variable key</span></span> | <span data-ttu-id="e81d5-726">Преобразованный ключ конфигурации</span><span class="sxs-lookup"><span data-stu-id="e81d5-726">Converted configuration key</span></span> | <span data-ttu-id="e81d5-727">Запись конфигурации поставщика</span><span class="sxs-lookup"><span data-stu-id="e81d5-727">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e81d5-728">Запись конфигурации не создана.</span><span class="sxs-lookup"><span data-stu-id="e81d5-728">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e81d5-729">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e81d5-730">Значение: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="e81d5-730">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e81d5-731">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-731">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e81d5-732">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="e81d5-732">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e81d5-733">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-733">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e81d5-734">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="e81d5-734">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="e81d5-735">**Пример**</span><span class="sxs-lookup"><span data-stu-id="e81d5-735">**Example**</span></span>

<span data-ttu-id="e81d5-736">На сервере создается пользовательская переменная среды строки подключения:</span><span class="sxs-lookup"><span data-stu-id="e81d5-736">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="e81d5-737">Имя: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="e81d5-737">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="e81d5-738">Значение: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="e81d5-738">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="e81d5-739">Если `IConfiguration` вставляется и назначается полю с именем `_config`, считайте значение:</span><span class="sxs-lookup"><span data-stu-id="e81d5-739">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="e81d5-740">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="e81d5-740">File Configuration Provider</span></span>

<span data-ttu-id="e81d5-741"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы.</span><span class="sxs-lookup"><span data-stu-id="e81d5-741"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="e81d5-742">Следующие поставщики конфигурации предназначены для определенных типов файлов:</span><span class="sxs-lookup"><span data-stu-id="e81d5-742">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="e81d5-743">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="e81d5-743">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="e81d5-744">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="e81d5-744">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="e81d5-745">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="e81d5-745">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="e81d5-746">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="e81d5-746">INI Configuration Provider</span></span>

<span data-ttu-id="e81d5-747"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-747">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="e81d5-748">Чтобы активировать конфигурацию INI-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-748">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e81d5-749">Двоеточие можно использовать как разделитель раздела в конфигурации файла INI.</span><span class="sxs-lookup"><span data-stu-id="e81d5-749">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="e81d5-750">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="e81d5-750">Overloads permit specifying:</span></span>

* <span data-ttu-id="e81d5-751">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="e81d5-751">Whether the file is optional.</span></span>
* <span data-ttu-id="e81d5-752">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="e81d5-752">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="e81d5-753"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="e81d5-753">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="e81d5-754">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-754">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="e81d5-755">Общий пример конфигурации INI-файла.</span><span class="sxs-lookup"><span data-stu-id="e81d5-755">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="e81d5-756">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-756">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e81d5-757">section0:key0</span><span class="sxs-lookup"><span data-stu-id="e81d5-757">section0:key0</span></span>
* <span data-ttu-id="e81d5-758">section0:key1</span><span class="sxs-lookup"><span data-stu-id="e81d5-758">section0:key1</span></span>
* <span data-ttu-id="e81d5-759">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="e81d5-759">section1:subsection:key</span></span>
* <span data-ttu-id="e81d5-760">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="e81d5-760">section2:subsection0:key</span></span>
* <span data-ttu-id="e81d5-761">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="e81d5-761">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="e81d5-762">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="e81d5-762">JSON Configuration Provider</span></span>

<span data-ttu-id="e81d5-763"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ — значение" JSON-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-763">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="e81d5-764">Чтобы активировать конфигурацию JSON-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-764">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e81d5-765">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="e81d5-765">Overloads permit specifying:</span></span>

* <span data-ttu-id="e81d5-766">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="e81d5-766">Whether the file is optional.</span></span>
* <span data-ttu-id="e81d5-767">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="e81d5-767">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="e81d5-768"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="e81d5-768">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="e81d5-769">`AddJsonFile` автоматически вызывается дважды при инициализации нового построителя узла с `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-769">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e81d5-770">Метод вызывается для загрузки конфигурации из:</span><span class="sxs-lookup"><span data-stu-id="e81d5-770">The method is called to load configuration from:</span></span>

* <span data-ttu-id="e81d5-771">*appsettings.json* : Этот файл считывается первым.</span><span class="sxs-lookup"><span data-stu-id="e81d5-771">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="e81d5-772">Версия файла среды может переопределить значения, предоставленные файлом *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e81d5-772">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="e81d5-773">*appsettings.{Environment}.json*: Версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="e81d5-773">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="e81d5-774">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="e81d5-774">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="e81d5-775">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="e81d5-775">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="e81d5-776">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="e81d5-776">Environment variables.</span></span>
* <span data-ttu-id="e81d5-777">[Секреты пользователя](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="e81d5-777">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="e81d5-778">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="e81d5-778">Command-line arguments.</span></span>

<span data-ttu-id="e81d5-779">Сначала устанавливается поставщик конфигурации JSON-файлов.</span><span class="sxs-lookup"><span data-stu-id="e81d5-779">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="e81d5-780">Таким образом, секреты пользователя, переменные среды и аргументы командной строки переопределят конфигурацию, заданную файлами *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-780">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="e81d5-781">Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать конфигурацию приложения для файлов, отличных от *appsettings.json* и *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-781">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="e81d5-782">**Пример**</span><span class="sxs-lookup"><span data-stu-id="e81d5-782">**Example**</span></span>

<span data-ttu-id="e81d5-783">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает два вызова `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-783">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="e81d5-784">Первый вызов `AddJsonFile` загружает конфигурацию из файла *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="e81d5-784">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="e81d5-785">Второй вызов `AddJsonFile` загружает конфигурацию из *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-785">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="e81d5-786">Для *appsettings.Development.json* в примере приложения загружается следующий файл:</span><span class="sxs-lookup"><span data-stu-id="e81d5-786">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="e81d5-787">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-787">Run the sample app.</span></span> <span data-ttu-id="e81d5-788">Откройте в приложении браузер с адресом `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-788">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="e81d5-789">Выходные данные содержат пары "ключ-значение" для конфигурации в зависимости от среды приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-789">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="e81d5-790">Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Debug` при запуске приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="e81d5-790">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="e81d5-791">Запустите пример приложения еще раз в рабочей среде:</span><span class="sxs-lookup"><span data-stu-id="e81d5-791">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="e81d5-792">Откройте файл *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-792">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="e81d5-793">В профиле `ConfigurationSample` измените значение переменной среды `ASPNETCORE_ENVIRONMENT` на `Production`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-793">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="e81d5-794">Сохраните файл и запустите приложение с помощью `dotnet run` в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="e81d5-794">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="e81d5-795">Параметры в *appsettings.Development.json* больше не переопределяют параметры в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e81d5-795">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="e81d5-796">Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Warning`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-796">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="e81d5-797">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="e81d5-797">XML Configuration Provider</span></span>

<span data-ttu-id="e81d5-798"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-798">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="e81d5-799">Чтобы активировать конфигурацию XML-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-799">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e81d5-800">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="e81d5-800">Overloads permit specifying:</span></span>

* <span data-ttu-id="e81d5-801">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="e81d5-801">Whether the file is optional.</span></span>
* <span data-ttu-id="e81d5-802">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="e81d5-802">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="e81d5-803"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="e81d5-803">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="e81d5-804">Корневой узел файла конфигурации не учитывается при создании пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-804">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="e81d5-805">Не указывайте в файле Document Type Definition (определение типа документа, DTD) или пространство имен.</span><span class="sxs-lookup"><span data-stu-id="e81d5-805">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="e81d5-806">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-806">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="e81d5-807">XML-файлы конфигурации могут использовать имена отдельных элементов для повторяющихся разделов.</span><span class="sxs-lookup"><span data-stu-id="e81d5-807">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="e81d5-808">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-808">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e81d5-809">section0:key0</span><span class="sxs-lookup"><span data-stu-id="e81d5-809">section0:key0</span></span>
* <span data-ttu-id="e81d5-810">section0:key1</span><span class="sxs-lookup"><span data-stu-id="e81d5-810">section0:key1</span></span>
* <span data-ttu-id="e81d5-811">section1:key0</span><span class="sxs-lookup"><span data-stu-id="e81d5-811">section1:key0</span></span>
* <span data-ttu-id="e81d5-812">section1:key1</span><span class="sxs-lookup"><span data-stu-id="e81d5-812">section1:key1</span></span>

<span data-ttu-id="e81d5-813">Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.</span><span class="sxs-lookup"><span data-stu-id="e81d5-813">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="e81d5-814">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-814">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e81d5-815">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="e81d5-815">section:section0:key:key0</span></span>
* <span data-ttu-id="e81d5-816">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="e81d5-816">section:section0:key:key1</span></span>
* <span data-ttu-id="e81d5-817">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="e81d5-817">section:section1:key:key0</span></span>
* <span data-ttu-id="e81d5-818">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="e81d5-818">section:section1:key:key1</span></span>

<span data-ttu-id="e81d5-819">Атрибуты можно использовать для предоставления значений.</span><span class="sxs-lookup"><span data-stu-id="e81d5-819">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="e81d5-820">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-820">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e81d5-821">key:attribute</span><span class="sxs-lookup"><span data-stu-id="e81d5-821">key:attribute</span></span>
* <span data-ttu-id="e81d5-822">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="e81d5-822">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="e81d5-823">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="e81d5-823">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="e81d5-824"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-824">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="e81d5-825">Ключ является именем файла.</span><span class="sxs-lookup"><span data-stu-id="e81d5-825">The key is the file name.</span></span> <span data-ttu-id="e81d5-826">Значение содержит содержимое файла.</span><span class="sxs-lookup"><span data-stu-id="e81d5-826">The value contains the file's contents.</span></span> <span data-ttu-id="e81d5-827">Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.</span><span class="sxs-lookup"><span data-stu-id="e81d5-827">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="e81d5-828">Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-828">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="e81d5-829">Значение параметра `directoryPath` должно быть абсолютным путем к файлам.</span><span class="sxs-lookup"><span data-stu-id="e81d5-829">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="e81d5-830">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="e81d5-830">Overloads permit specifying:</span></span>

* <span data-ttu-id="e81d5-831">`Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.</span><span class="sxs-lookup"><span data-stu-id="e81d5-831">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="e81d5-832">Обязательно ли указывать каталог и путь к каталогу.</span><span class="sxs-lookup"><span data-stu-id="e81d5-832">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="e81d5-833">Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов.</span><span class="sxs-lookup"><span data-stu-id="e81d5-833">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="e81d5-834">Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-834">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="e81d5-835">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-835">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="e81d5-836">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="e81d5-836">Memory Configuration Provider</span></span>

<span data-ttu-id="e81d5-837"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-837">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="e81d5-838">Чтобы активировать конфигурацию коллекции в памяти, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-838">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e81d5-839">Поставщик конфигурации может инициализироваться значением `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-839">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="e81d5-840">Чтобы указать конфигурацию приложения, при сборке узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-840">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="e81d5-841">В следующем примере создается словарь конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-841">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="e81d5-842">Словарь используется с вызовом `AddInMemoryCollection` для предоставления конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e81d5-842">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="e81d5-843">GetValue</span><span class="sxs-lookup"><span data-stu-id="e81d5-843">GetValue</span></span>

<span data-ttu-id="e81d5-844">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип, не являющийся коллекцией.</span><span class="sxs-lookup"><span data-stu-id="e81d5-844">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="e81d5-845">Перегрузка принимает значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e81d5-845">An overload accepts a default value.</span></span>

<span data-ttu-id="e81d5-846">В следующем примере происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="e81d5-846">The following example:</span></span>

* <span data-ttu-id="e81d5-847">Из конфигурации извлекается строковое значение с ключом `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-847">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="e81d5-848">Если `NumberKey` отсутствует в ключах конфигурации, используется значение по умолчанию `99`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-848">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="e81d5-849">Значение получает тип `int`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-849">Types the value as an `int`.</span></span>
* <span data-ttu-id="e81d5-850">Значение сохраняется в свойстве `NumberConfig` для использования на странице.</span><span class="sxs-lookup"><span data-stu-id="e81d5-850">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="e81d5-851">GetSection, GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="e81d5-851">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="e81d5-852">В следующих примерах рассмотрим файл JSON.</span><span class="sxs-lookup"><span data-stu-id="e81d5-852">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="e81d5-853">Четыре ключа находятся в двух разделах, один из которых содержит пару из подразделов.</span><span class="sxs-lookup"><span data-stu-id="e81d5-853">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="e81d5-854">Когда файл считывается в конфигурацию, для сохранения значений конфигурации создаются следующие уникальные иерархические ключи.</span><span class="sxs-lookup"><span data-stu-id="e81d5-854">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="e81d5-855">section0:key0</span><span class="sxs-lookup"><span data-stu-id="e81d5-855">section0:key0</span></span>
* <span data-ttu-id="e81d5-856">section0:key1</span><span class="sxs-lookup"><span data-stu-id="e81d5-856">section0:key1</span></span>
* <span data-ttu-id="e81d5-857">section1:key0</span><span class="sxs-lookup"><span data-stu-id="e81d5-857">section1:key0</span></span>
* <span data-ttu-id="e81d5-858">section1:key1</span><span class="sxs-lookup"><span data-stu-id="e81d5-858">section1:key1</span></span>
* <span data-ttu-id="e81d5-859">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="e81d5-859">section2:subsection0:key0</span></span>
* <span data-ttu-id="e81d5-860">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="e81d5-860">section2:subsection0:key1</span></span>
* <span data-ttu-id="e81d5-861">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="e81d5-861">section2:subsection1:key0</span></span>
* <span data-ttu-id="e81d5-862">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="e81d5-862">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="e81d5-863">GetSection</span><span class="sxs-lookup"><span data-stu-id="e81d5-863">GetSection</span></span>

<span data-ttu-id="e81d5-864">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) извлекает подраздел конфигурации с указанным ключом подраздела.</span><span class="sxs-lookup"><span data-stu-id="e81d5-864">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="e81d5-865">Чтобы вернуть <xref:Microsoft.Extensions.Configuration.IConfigurationSection>, содержащий только пары "ключ — значение" в `section1`, вызовите `GetSection` и укажите имя раздела.</span><span class="sxs-lookup"><span data-stu-id="e81d5-865">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="e81d5-866">`configSection` не содержит значение, только ключ и путь.</span><span class="sxs-lookup"><span data-stu-id="e81d5-866">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="e81d5-867">Аналогично, чтобы получить значения для ключей в `section2:subsection0`, вызовите `GetSection` и укажите путь к разделу.</span><span class="sxs-lookup"><span data-stu-id="e81d5-867">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="e81d5-868">Значение `GetSection` никогда не возвращает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-868">`GetSection` never returns `null`.</span></span> <span data-ttu-id="e81d5-869">Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-869">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="e81d5-870">Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется.</span><span class="sxs-lookup"><span data-stu-id="e81d5-870">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="e81d5-871"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.</span><span class="sxs-lookup"><span data-stu-id="e81d5-871">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="e81d5-872">GetChildren</span><span class="sxs-lookup"><span data-stu-id="e81d5-872">GetChildren</span></span>

<span data-ttu-id="e81d5-873">Вызов [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) в `section2` получает значение `IEnumerable<IConfigurationSection>`, которое включает:</span><span class="sxs-lookup"><span data-stu-id="e81d5-873">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="e81d5-874">Exists</span><span class="sxs-lookup"><span data-stu-id="e81d5-874">Exists</span></span>

<span data-ttu-id="e81d5-875">Используйте [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*), чтобы определить, существует ли раздел конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-875">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="e81d5-876">Учитывая данные примера, `sectionExists` является `false`, потому что в данных конфигурации нет `section2:subsection2`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-876">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="e81d5-877">Привязка к графу объектов</span><span class="sxs-lookup"><span data-stu-id="e81d5-877">Bind to an object graph</span></span>

<span data-ttu-id="e81d5-878"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> способна связывать весь граф объекта POCO.</span><span class="sxs-lookup"><span data-stu-id="e81d5-878"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="e81d5-879">Как и при привязке простого объекта, привязываются только открытые свойства чтения и записи.</span><span class="sxs-lookup"><span data-stu-id="e81d5-879">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="e81d5-880">Образец содержит модель `TvShow`, в графе объектов которого находятся классы `Metadata` и `Actors` (*Модели/TvShow.cs*).</span><span class="sxs-lookup"><span data-stu-id="e81d5-880">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="e81d5-881">В примере приложения есть файл *tvshow.xml*, содержащий данные конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-881">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="e81d5-882">Конфигурация привязана ко всему ​​графу объектов `TvShow` с помощью метода `Bind`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-882">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="e81d5-883">Привязанный экземпляр присваивается свойству для подготовки к просмотру.</span><span class="sxs-lookup"><span data-stu-id="e81d5-883">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="e81d5-884">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) привязывает и возвращает указанный тип.</span><span class="sxs-lookup"><span data-stu-id="e81d5-884">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="e81d5-885">Метод `Get<T>` может быть более удобным, чем использование `Bind`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-885">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="e81d5-886">Следующий код показывает, как использовать `Get<T>` с предыдущим примером:</span><span class="sxs-lookup"><span data-stu-id="e81d5-886">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="e81d5-887">Привязка массива к классу</span><span class="sxs-lookup"><span data-stu-id="e81d5-887">Bind an array to a class</span></span>

<span data-ttu-id="e81d5-888">*Пример приложения демонстрирует концепции, описанные в этом разделе.*</span><span class="sxs-lookup"><span data-stu-id="e81d5-888">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="e81d5-889"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-889">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="e81d5-890">Любой формат массива, который предоставляет сегмент числового ключа (`:0:`, `:1:`, &hellip; `:{n}:`), способен привязать массив к массиву класса POCO.</span><span class="sxs-lookup"><span data-stu-id="e81d5-890">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="e81d5-891">Привязка предоставляется соглашением.</span><span class="sxs-lookup"><span data-stu-id="e81d5-891">Binding is provided by convention.</span></span> <span data-ttu-id="e81d5-892">Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.</span><span class="sxs-lookup"><span data-stu-id="e81d5-892">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="e81d5-893">**Обработка массива в оперативной памяти**</span><span class="sxs-lookup"><span data-stu-id="e81d5-893">**In-memory array processing**</span></span>

<span data-ttu-id="e81d5-894">Рассмотрите ключи и значения конфигурации, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="e81d5-894">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="e81d5-895">Ключ</span><span class="sxs-lookup"><span data-stu-id="e81d5-895">Key</span></span>             | <span data-ttu-id="e81d5-896">Значение</span><span class="sxs-lookup"><span data-stu-id="e81d5-896">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="e81d5-897">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="e81d5-897">array:entries:0</span></span> | <span data-ttu-id="e81d5-898">value0</span><span class="sxs-lookup"><span data-stu-id="e81d5-898">value0</span></span> |
| <span data-ttu-id="e81d5-899">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="e81d5-899">array:entries:1</span></span> | <span data-ttu-id="e81d5-900">value1</span><span class="sxs-lookup"><span data-stu-id="e81d5-900">value1</span></span> |
| <span data-ttu-id="e81d5-901">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="e81d5-901">array:entries:2</span></span> | <span data-ttu-id="e81d5-902">value2</span><span class="sxs-lookup"><span data-stu-id="e81d5-902">value2</span></span> |
| <span data-ttu-id="e81d5-903">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="e81d5-903">array:entries:4</span></span> | <span data-ttu-id="e81d5-904">value4</span><span class="sxs-lookup"><span data-stu-id="e81d5-904">value4</span></span> |
| <span data-ttu-id="e81d5-905">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="e81d5-905">array:entries:5</span></span> | <span data-ttu-id="e81d5-906">value5</span><span class="sxs-lookup"><span data-stu-id="e81d5-906">value5</span></span> |

<span data-ttu-id="e81d5-907">Эти ключи и значения загружаются в пример приложения с помощью поставщика конфигурации памяти.</span><span class="sxs-lookup"><span data-stu-id="e81d5-907">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="e81d5-908">Массив пропускает значения для индекса &num;3.</span><span class="sxs-lookup"><span data-stu-id="e81d5-908">The array skips a value for index &num;3.</span></span> <span data-ttu-id="e81d5-909">Связующее свойство конфигурации не способно связывать нулевые значения или создавать нулевые записи в связанных объектах, что становится ясным в тот момент, когда демонстрируется результат привязки этого массива к объекту.</span><span class="sxs-lookup"><span data-stu-id="e81d5-909">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="e81d5-910">В примере приложения класс POCO доступен для хранения привязанных данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-910">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="e81d5-911">Данные конфигурации, связанные с объектом.</span><span class="sxs-lookup"><span data-stu-id="e81d5-911">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="e81d5-912">Синтаксис [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) также может использоваться для получения более компактного кода:</span><span class="sxs-lookup"><span data-stu-id="e81d5-912">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="e81d5-913">Связанный объект, экземпляр `ArrayExample`, получает данные массива из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-913">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="e81d5-914">Индекс `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="e81d5-914">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="e81d5-915">Значение `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="e81d5-915">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="e81d5-916">0</span><span class="sxs-lookup"><span data-stu-id="e81d5-916">0</span></span>                            | <span data-ttu-id="e81d5-917">value0</span><span class="sxs-lookup"><span data-stu-id="e81d5-917">value0</span></span>                       |
| <span data-ttu-id="e81d5-918">1</span><span class="sxs-lookup"><span data-stu-id="e81d5-918">1</span></span>                            | <span data-ttu-id="e81d5-919">value1</span><span class="sxs-lookup"><span data-stu-id="e81d5-919">value1</span></span>                       |
| <span data-ttu-id="e81d5-920">2</span><span class="sxs-lookup"><span data-stu-id="e81d5-920">2</span></span>                            | <span data-ttu-id="e81d5-921">value2</span><span class="sxs-lookup"><span data-stu-id="e81d5-921">value2</span></span>                       |
| <span data-ttu-id="e81d5-922">3</span><span class="sxs-lookup"><span data-stu-id="e81d5-922">3</span></span>                            | <span data-ttu-id="e81d5-923">value4</span><span class="sxs-lookup"><span data-stu-id="e81d5-923">value4</span></span>                       |
| <span data-ttu-id="e81d5-924">4</span><span class="sxs-lookup"><span data-stu-id="e81d5-924">4</span></span>                            | <span data-ttu-id="e81d5-925">value5</span><span class="sxs-lookup"><span data-stu-id="e81d5-925">value5</span></span>                       |

<span data-ttu-id="e81d5-926">Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-926">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="e81d5-927">При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы просто используются для выполнения итерации данных конфигурации при создании объекта.</span><span class="sxs-lookup"><span data-stu-id="e81d5-927">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="e81d5-928">Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.</span><span class="sxs-lookup"><span data-stu-id="e81d5-928">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="e81d5-929">Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который создает правильную пару "ключ — значение" в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-929">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="e81d5-930">Если в образец включен дополнительный поставщик конфигурации JSON с отсутствующей парой "ключ — значение", то `ArrayExample.Entries` подойдет полному массиву конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-930">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="e81d5-931">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-931">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="e81d5-932">В `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="e81d5-932">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="e81d5-933">Пары "ключ — значение", показанные в таблице, загружаются в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-933">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="e81d5-934">Ключ</span><span class="sxs-lookup"><span data-stu-id="e81d5-934">Key</span></span>             | <span data-ttu-id="e81d5-935">Значение</span><span class="sxs-lookup"><span data-stu-id="e81d5-935">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="e81d5-936">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="e81d5-936">array:entries:3</span></span> | <span data-ttu-id="e81d5-937">value3</span><span class="sxs-lookup"><span data-stu-id="e81d5-937">value3</span></span> |

<span data-ttu-id="e81d5-938">Если экземпляр класса `ArrayExample` связан после того, как поставщик конфигурации JSON включает запись для индекса &num;3, то массив `ArrayExample.Entries` включит значение.</span><span class="sxs-lookup"><span data-stu-id="e81d5-938">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="e81d5-939">Индекс `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="e81d5-939">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="e81d5-940">Значение `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="e81d5-940">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="e81d5-941">0</span><span class="sxs-lookup"><span data-stu-id="e81d5-941">0</span></span>                            | <span data-ttu-id="e81d5-942">value0</span><span class="sxs-lookup"><span data-stu-id="e81d5-942">value0</span></span>                       |
| <span data-ttu-id="e81d5-943">1</span><span class="sxs-lookup"><span data-stu-id="e81d5-943">1</span></span>                            | <span data-ttu-id="e81d5-944">value1</span><span class="sxs-lookup"><span data-stu-id="e81d5-944">value1</span></span>                       |
| <span data-ttu-id="e81d5-945">2</span><span class="sxs-lookup"><span data-stu-id="e81d5-945">2</span></span>                            | <span data-ttu-id="e81d5-946">value2</span><span class="sxs-lookup"><span data-stu-id="e81d5-946">value2</span></span>                       |
| <span data-ttu-id="e81d5-947">3</span><span class="sxs-lookup"><span data-stu-id="e81d5-947">3</span></span>                            | <span data-ttu-id="e81d5-948">value3</span><span class="sxs-lookup"><span data-stu-id="e81d5-948">value3</span></span>                       |
| <span data-ttu-id="e81d5-949">4</span><span class="sxs-lookup"><span data-stu-id="e81d5-949">4</span></span>                            | <span data-ttu-id="e81d5-950">value4</span><span class="sxs-lookup"><span data-stu-id="e81d5-950">value4</span></span>                       |
| <span data-ttu-id="e81d5-951">5</span><span class="sxs-lookup"><span data-stu-id="e81d5-951">5</span></span>                            | <span data-ttu-id="e81d5-952">value5</span><span class="sxs-lookup"><span data-stu-id="e81d5-952">value5</span></span>                       |

<span data-ttu-id="e81d5-953">**Обработка массива JSON**</span><span class="sxs-lookup"><span data-stu-id="e81d5-953">**JSON array processing**</span></span>

<span data-ttu-id="e81d5-954">Если JSON-файл содержит массив, ключи конфигурации будут созданы для элементов массива с индексом раздела, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="e81d5-954">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="e81d5-955">В следующем файле конфигурации `subsection` — это массив.</span><span class="sxs-lookup"><span data-stu-id="e81d5-955">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="e81d5-956">Поставщик конфигурации JSON считывает данные конфигурации в следующие пары "ключ — значение".</span><span class="sxs-lookup"><span data-stu-id="e81d5-956">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="e81d5-957">Ключ</span><span class="sxs-lookup"><span data-stu-id="e81d5-957">Key</span></span>                     | <span data-ttu-id="e81d5-958">Значение</span><span class="sxs-lookup"><span data-stu-id="e81d5-958">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="e81d5-959">json_array:key</span><span class="sxs-lookup"><span data-stu-id="e81d5-959">json_array:key</span></span>          | <span data-ttu-id="e81d5-960">valueA</span><span class="sxs-lookup"><span data-stu-id="e81d5-960">valueA</span></span> |
| <span data-ttu-id="e81d5-961">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="e81d5-961">json_array:subsection:0</span></span> | <span data-ttu-id="e81d5-962">valueB</span><span class="sxs-lookup"><span data-stu-id="e81d5-962">valueB</span></span> |
| <span data-ttu-id="e81d5-963">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="e81d5-963">json_array:subsection:1</span></span> | <span data-ttu-id="e81d5-964">valueC</span><span class="sxs-lookup"><span data-stu-id="e81d5-964">valueC</span></span> |
| <span data-ttu-id="e81d5-965">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="e81d5-965">json_array:subsection:2</span></span> | <span data-ttu-id="e81d5-966">valueD</span><span class="sxs-lookup"><span data-stu-id="e81d5-966">valueD</span></span> |

<span data-ttu-id="e81d5-967">В примере приложения для привязки пар "ключ — значение" конфигурации доступен следующий класс POCO.</span><span class="sxs-lookup"><span data-stu-id="e81d5-967">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="e81d5-968">После выполнения привязки `JsonArrayExample.Key` содержит значение `valueA`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-968">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="e81d5-969">Подраздел значения хранится в свойстве массива POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-969">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="e81d5-970">Индекс `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="e81d5-970">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="e81d5-971">Значение `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="e81d5-971">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="e81d5-972">0</span><span class="sxs-lookup"><span data-stu-id="e81d5-972">0</span></span>                                   | <span data-ttu-id="e81d5-973">valueB</span><span class="sxs-lookup"><span data-stu-id="e81d5-973">valueB</span></span>                              |
| <span data-ttu-id="e81d5-974">1</span><span class="sxs-lookup"><span data-stu-id="e81d5-974">1</span></span>                                   | <span data-ttu-id="e81d5-975">valueC</span><span class="sxs-lookup"><span data-stu-id="e81d5-975">valueC</span></span>                              |
| <span data-ttu-id="e81d5-976">2</span><span class="sxs-lookup"><span data-stu-id="e81d5-976">2</span></span>                                   | <span data-ttu-id="e81d5-977">valueD</span><span class="sxs-lookup"><span data-stu-id="e81d5-977">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="e81d5-978">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="e81d5-978">Custom configuration provider</span></span>

<span data-ttu-id="e81d5-979">Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="e81d5-979">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="e81d5-980">Поставщик имеет следующие характеристики.</span><span class="sxs-lookup"><span data-stu-id="e81d5-980">The provider has the following characteristics:</span></span>

* <span data-ttu-id="e81d5-981">База данных в памяти EF используется для демонстрационных целей.</span><span class="sxs-lookup"><span data-stu-id="e81d5-981">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="e81d5-982">Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e81d5-982">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="e81d5-983">Поставщик считывает таблицу базы данных в конфигурации при запуске.</span><span class="sxs-lookup"><span data-stu-id="e81d5-983">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="e81d5-984">Поставщик не запрашивает базу данных для каждого ключа.</span><span class="sxs-lookup"><span data-stu-id="e81d5-984">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="e81d5-985">Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="e81d5-985">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="e81d5-986">Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e81d5-986">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="e81d5-987">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-987">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="e81d5-988">Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.</span><span class="sxs-lookup"><span data-stu-id="e81d5-988">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="e81d5-989">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-989">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="e81d5-990">Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-990">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="e81d5-991">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-991">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="e81d5-992">Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-992">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="e81d5-993">Поставщик конфигурации инициализирует пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="e81d5-993">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="e81d5-994">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-994">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="e81d5-995">Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-995">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="e81d5-996">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="e81d5-996">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="e81d5-997">В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="e81d5-997">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="e81d5-998">Доступ к конфигурации во время запуска</span><span class="sxs-lookup"><span data-stu-id="e81d5-998">Access configuration during startup</span></span>

<span data-ttu-id="e81d5-999">Внесите значение `IConfiguration` в конструктор `Startup` для доступа к значениям конфигурации в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-999">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e81d5-1000">Чтобы получить доступ к конфигурации в `Startup.Configure`, либо добавьте значение `IConfiguration` непосредственно в метод, либо используйте экземпляр из конструктора.</span><span class="sxs-lookup"><span data-stu-id="e81d5-1000">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="e81d5-1001">Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="e81d5-1001">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="e81d5-1002">Настройте доступ на странице Razor Pages или в представлении MVC</span><span class="sxs-lookup"><span data-stu-id="e81d5-1002">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="e81d5-1003">Для доступа к параметрам конфигурации на странице Razor Pages или в представлении MVC добавьте [использование директивы](xref:mvc/views/razor#using) ([Справочник по C#: использование директивы](/dotnet/csharp/language-reference/keywords/using-directive)) для [пространства имен Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) и вставьте <xref:Microsoft.Extensions.Configuration.IConfiguration> на страницу или в представление.</span><span class="sxs-lookup"><span data-stu-id="e81d5-1003">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="e81d5-1004">На странице Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="e81d5-1004">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="e81d5-1005">В представлении MVC</span><span class="sxs-lookup"><span data-stu-id="e81d5-1005">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="e81d5-1006">Добавление конфигурации из внешней сборки</span><span class="sxs-lookup"><span data-stu-id="e81d5-1006">Add configuration from an external assembly</span></span>

<span data-ttu-id="e81d5-1007">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="e81d5-1007">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="e81d5-1008">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e81d5-1008">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e81d5-1009">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e81d5-1009">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
