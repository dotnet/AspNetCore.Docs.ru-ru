---
title: Конфигурация в .NET Core
author: rick-anderson
description: Узнайте, как использовать API конфигурации для настройки приложения ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 1/29/2021
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
ms.openlocfilehash: 24b4d5fc11d21dce4d9e0fd2f8f0dd2d45e82baa
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110083"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="dcad1-103">Конфигурация в .NET Core</span><span class="sxs-lookup"><span data-stu-id="dcad1-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="dcad1-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="dcad1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dcad1-105">Настройка в ASP.NET Core выполняется с помощью одного или нескольких [поставщиков конфигурации](#cp).</span><span class="sxs-lookup"><span data-stu-id="dcad1-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="dcad1-106">Поставщики конфигурации получают данные конфигурации в парах "ключ-значение" из различных источников:</span><span class="sxs-lookup"><span data-stu-id="dcad1-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="dcad1-107">файлов параметров, таких как *appsettings.json* ;</span><span class="sxs-lookup"><span data-stu-id="dcad1-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="dcad1-108">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="dcad1-108">Environment variables</span></span>
* <span data-ttu-id="dcad1-109">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="dcad1-109">Azure Key Vault</span></span>
* <span data-ttu-id="dcad1-110">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="dcad1-110">Azure App Configuration</span></span>
* <span data-ttu-id="dcad1-111">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="dcad1-111">Command-line arguments</span></span>
* <span data-ttu-id="dcad1-112">пользовательские поставщики, установленные или созданные;</span><span class="sxs-lookup"><span data-stu-id="dcad1-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="dcad1-113">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="dcad1-113">Directory files</span></span>
* <span data-ttu-id="dcad1-114">объектов .NET в памяти;</span><span class="sxs-lookup"><span data-stu-id="dcad1-114">In-memory .NET objects</span></span>

<span data-ttu-id="dcad1-115">В этом разделе приведены сведения о конфигурации в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dcad1-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="dcad1-116">Сведения об использовании конфигурации в консольных приложениях см. в статье [Конфигурация .NET](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="dcad1-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="dcad1-117">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dcad1-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="dcad1-118">Конфигурация по умолчанию</span><span class="sxs-lookup"><span data-stu-id="dcad1-118">Default configuration</span></span>

<span data-ttu-id="dcad1-119">Веб-приложения ASP.NET Core, созданные с помощью [dotnet new](/dotnet/core/tools/dotnet-new) или Visual Studio, создают следующий код:</span><span class="sxs-lookup"><span data-stu-id="dcad1-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="dcad1-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> предоставляет конфигурацию по умолчанию для приложения в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="dcad1-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="dcad1-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  добавляет существующий `IConfiguration` в качестве источника.</span><span class="sxs-lookup"><span data-stu-id="dcad1-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="dcad1-122">В случае конфигурации по умолчанию добавляет конфигурацию [узла](#hvac) и задает ее в качестве первого источника для конфигурации _приложения_.</span><span class="sxs-lookup"><span data-stu-id="dcad1-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="dcad1-123">Файл [appsettings.json](#appsettingsjson), использующий [поставщик конфигурации JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dcad1-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="dcad1-124">Файл *appsettings.* `Environment` *.json*, использующий [поставщик конфигурации JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dcad1-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="dcad1-125">Например, *appsettings*.***Production\*\*_._json* и *appsettings*.\*\*\*Development** _._json\*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="dcad1-126">[Секреты приложения](xref:security/app-secrets), когда приложение выполняется в среде `Development`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="dcad1-127">Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="dcad1-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="dcad1-128">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line).</span><span class="sxs-lookup"><span data-stu-id="dcad1-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="dcad1-129">Поставщики конфигурации, добавленные позже, переопределяют предыдущие параметры ключа.</span><span class="sxs-lookup"><span data-stu-id="dcad1-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="dcad1-130">Например, если `MyKey` задан в *appsettings.json* и в среде, используется значение среды.</span><span class="sxs-lookup"><span data-stu-id="dcad1-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="dcad1-131">При использовании поставщиков конфигурации по умолчанию [поставщик конфигурации командной строки](#clcp) переопределяет остальных поставщиков.</span><span class="sxs-lookup"><span data-stu-id="dcad1-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="dcad1-132">Дополнительные сведения о `CreateDefaultBuilder` см. в разделе [Параметры сборщика по умолчанию](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="dcad1-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="dcad1-133">В следующем коде отображаются включенные поставщики конфигурации в том порядке, в котором они были добавлены:</span><span class="sxs-lookup"><span data-stu-id="dcad1-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="dcad1-134">Рассмотрим следующий файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="dcad1-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="dcad1-135">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="dcad1-136"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> по умолчанию загружает конфигурацию в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="dcad1-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="dcad1-137">*appsettings.* `Environment` *.json* : Например, файлы *appsettings*.***Production\*\*_._json* и *appsettings*.\*\*\*Development** _._json\*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="dcad1-138">Версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="dcad1-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="dcad1-139">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="dcad1-140">Значения в *appsettings*.`Environment`.*json* переопределяют ключи в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="dcad1-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="dcad1-141">Например, по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="dcad1-141">For example, by default:</span></span>

* <span data-ttu-id="dcad1-142">В среде разработки конфигурация *appsettings*.***Development** _._json* перезаписывает значения, найденные в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="dcad1-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="dcad1-143">В рабочей среде конфигурация *appsettings*.***Production** _._json* перезаписывает значения, найденные в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="dcad1-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="dcad1-144">Например, при развертывании приложения в Azure.</span><span class="sxs-lookup"><span data-stu-id="dcad1-144">For example, when deploying the app to Azure.</span></span>

<span data-ttu-id="dcad1-145">Если необходимо гарантировать значение конфигурации, см. раздел о [GetValue](#getvalue).</span><span class="sxs-lookup"><span data-stu-id="dcad1-145">If a configuration value must be guaranteed, see [GetValue](#getvalue).</span></span> <span data-ttu-id="dcad1-146">Предыдущий пример поддерживает только считывание строк и не поддерживает значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="dcad1-146">The preceding example only reads strings and doesn’t support a default value</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="dcad1-147">Привязка иерархических данных конфигурации с помощью шаблона параметров</span><span class="sxs-lookup"><span data-stu-id="dcad1-147">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="dcad1-148">С помощью конфигурации [по умолчанию](#default) файлы *appsettings.json* и *appsettings.* `Environment` *.json* включаются с помощью [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="dcad1-148">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="dcad1-149">Изменения, внесенные в файлы *appsettings.json* и *appsettings.* `Environment` *.json* ***после*** запуска приложения, считываются [поставщиком конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="dcad1-149">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="dcad1-150">Сведения о добавлении дополнительных файлов конфигурации JSON см. в разделе [Поставщик конфигурации JSON](#jcp) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="dcad1-150">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="dcad1-151">Объединение коллекций служб</span><span class="sxs-lookup"><span data-stu-id="dcad1-151">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="dcad1-152">Безопасность и секреты пользователей</span><span class="sxs-lookup"><span data-stu-id="dcad1-152">Security and user secrets</span></span>

<span data-ttu-id="dcad1-153">Рекомендации по данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-153">Configuration data guidelines:</span></span>

* <span data-ttu-id="dcad1-154">Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.</span><span class="sxs-lookup"><span data-stu-id="dcad1-154">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="dcad1-155">Хранить секреты во время разработки можно с помощью [диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="dcad1-155">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="dcad1-156">Не используйте секреты рабочей среды в средах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="dcad1-156">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="dcad1-157">Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.</span><span class="sxs-lookup"><span data-stu-id="dcad1-157">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="dcad1-158">[По умолчанию](#default) источник конфигурации секрета пользователя регистрируется после источников конфигурации JSON.</span><span class="sxs-lookup"><span data-stu-id="dcad1-158">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="dcad1-159">Таким образом, ключи секретов пользователя имеют приоритет над ключами в *appsettings.json* и *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-159">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="dcad1-160">Дополнительные сведения о хранении паролей или других конфиденциальных данных:</span><span class="sxs-lookup"><span data-stu-id="dcad1-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="dcad1-161"><xref:security/app-secrets>. Содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="dcad1-161"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="dcad1-162">Диспетчер секретов использует [поставщик конфигурации файла](#fcp) для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.</span><span class="sxs-lookup"><span data-stu-id="dcad1-162">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="dcad1-163">В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dcad1-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="dcad1-164">Для получения дополнительной информации см. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="dcad1-165">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="dcad1-165">Environment variables</span></span>

<span data-ttu-id="dcad1-166">При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пар "ключ-значение" в переменных среды после чтения *appsettings.json* , *appsettings.* `Environment` *.json* и [секретов пользователя](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="dcad1-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="dcad1-167">Поэтому значения ключей, считанные из среды, переопределяют значения, считанные из *appsettings.json* , *appsettings.* `Environment` *.json* и секретов пользователя.</span><span class="sxs-lookup"><span data-stu-id="dcad1-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="dcad1-168">Следующие команды `set`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-168">The following `set` commands:</span></span>

* <span data-ttu-id="dcad1-169">Задают ключи и значения среды в [предыдущем примере](#appsettingsjson) в Windows.</span><span class="sxs-lookup"><span data-stu-id="dcad1-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="dcad1-170">Проверяют параметры при использовании [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="dcad1-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="dcad1-171">Команда `dotnet run` должна выполняться в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="dcad1-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="dcad1-172">Предыдущие параметры среды:</span><span class="sxs-lookup"><span data-stu-id="dcad1-172">The preceding environment settings:</span></span>

* <span data-ttu-id="dcad1-173">Задаются только в процессах, запускаемых из командного окна, в котором они были установлены.</span><span class="sxs-lookup"><span data-stu-id="dcad1-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="dcad1-174">Не будут считываться браузерами, запущенными в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dcad1-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="dcad1-175">Следующие команды [setx](/windows-server/administration/windows-commands/setx) можно использовать для задания ключей и значений среды в Windows.</span><span class="sxs-lookup"><span data-stu-id="dcad1-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="dcad1-176">В отличие от `set`, параметры `setx` сохраняются.</span><span class="sxs-lookup"><span data-stu-id="dcad1-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="dcad1-177">`/M` задает переменную в системной среде.</span><span class="sxs-lookup"><span data-stu-id="dcad1-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="dcad1-178">Если параметр `/M` не используется, задается переменная среды пользователя.</span><span class="sxs-lookup"><span data-stu-id="dcad1-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="dcad1-179">Чтобы проверить, что предыдущие команды переопределяют *appsettings.json* и *appsettings.* `Environment` *.json*, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="dcad1-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="dcad1-180">В Visual Studio: Выйдите из среды Visual Studio и перезапустите ее.</span><span class="sxs-lookup"><span data-stu-id="dcad1-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="dcad1-181">В CLI: Откройте новое командное окно и введите `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="dcad1-182">Вызовите <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> со строкой, чтобы указать префикс для переменных среды:</span><span class="sxs-lookup"><span data-stu-id="dcad1-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="dcad1-183">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="dcad1-183">In the preceding code:</span></span>

* <span data-ttu-id="dcad1-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` добавляется после [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="dcad1-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="dcad1-185">Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="dcad1-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="dcad1-186">Переменные среды, установленные с префиксом `MyCustomPrefix_`, переопределяют [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="dcad1-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="dcad1-187">Сюда входят переменные среды без префикса.</span><span class="sxs-lookup"><span data-stu-id="dcad1-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="dcad1-188">Префикс отделяется при создании пары конфигурации "ключ-значение".</span><span class="sxs-lookup"><span data-stu-id="dcad1-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="dcad1-189">Следующие команды проверяют пользовательский префикс:</span><span class="sxs-lookup"><span data-stu-id="dcad1-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="dcad1-190">[Конфигурация по умолчанию](#default) загружает переменные среды и аргументы командной строки с префиксом `DOTNET_` и `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="dcad1-191">Префиксы `DOTNET_` и `ASPNETCORE_` используются ASP.NET Core для [конфигурации узла и приложения](xref:fundamentals/host/generic-host#host-configuration), но не для конфигурации пользователя.</span><span class="sxs-lookup"><span data-stu-id="dcad1-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="dcad1-192">Дополнительные сведения о конфигурации узла и приложения см. в разделе [Универсальный узел .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="dcad1-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="dcad1-193">В [Службе приложений Azure](https://azure.microsoft.com/services/app-service/) выберите **Новый параметр приложения** на странице **Параметры > Конфигурация**.</span><span class="sxs-lookup"><span data-stu-id="dcad1-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="dcad1-194">Параметры приложения Службы приложений Azure:</span><span class="sxs-lookup"><span data-stu-id="dcad1-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="dcad1-195">Шифруются, когда они неактивны, и передаются по зашифрованному каналу.</span><span class="sxs-lookup"><span data-stu-id="dcad1-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="dcad1-196">Предоставляются как переменные среды.</span><span class="sxs-lookup"><span data-stu-id="dcad1-196">Exposed as environment variables.</span></span>

<span data-ttu-id="dcad1-197">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="dcad1-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="dcad1-198">Сведения о строках подключения к базе данных Azure см. в разделе [Префиксы строк подключения](#constr).</span><span class="sxs-lookup"><span data-stu-id="dcad1-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="dcad1-199">Именование переменных среды</span><span class="sxs-lookup"><span data-stu-id="dcad1-199">Naming of environment variables</span></span>

<span data-ttu-id="dcad1-200">Имена переменных среды отражают структуру файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="dcad1-200">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="dcad1-201">Каждый элемент в иерархии отделяется двойным символом подчеркивания (предпочтительно) или двоеточием.</span><span class="sxs-lookup"><span data-stu-id="dcad1-201">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="dcad1-202">Если структура элемента включает массив, индекс массива должен рассматриваться как дополнительное имя элемента в этом пути.</span><span class="sxs-lookup"><span data-stu-id="dcad1-202">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="dcad1-203">Рассмотрим следующий файл *appsettings.json* и его эквивалентные значения, представленные в виде переменных среды:</span><span class="sxs-lookup"><span data-stu-id="dcad1-203">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

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

<span data-ttu-id="dcad1-204">**Переменные среды**</span><span class="sxs-lookup"><span data-stu-id="dcad1-204">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-generated-launchsettingsjson"></a><span data-ttu-id="dcad1-205">Переменные среды, заданные в созданном файле launchSettings.json</span><span class="sxs-lookup"><span data-stu-id="dcad1-205">Environment variables set in generated launchSettings.json</span></span>

<span data-ttu-id="dcad1-206">Переменные среды, заданные в *launchSettings.json*, переопределяют переменные, заданные в системной среде.</span><span class="sxs-lookup"><span data-stu-id="dcad1-206">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span> <span data-ttu-id="dcad1-207">Например, веб-шаблоны ASP.NET Core позволяют создать файл *launchSettings.json*, который задает следующую конфигурацию конечной точки:</span><span class="sxs-lookup"><span data-stu-id="dcad1-207">For example, the ASP.NET Core web templates generate a *launchSettings.json* file that sets the endpoint configuration to:</span></span>

```json
"applicationUrl": "https://localhost:5001;http://localhost:5000"
```

<span data-ttu-id="dcad1-208">При настройке `applicationUrl` устанавливается переменная среды `ASPNETCORE_URLS` и переопределяются значения, заданные в среде.</span><span class="sxs-lookup"><span data-stu-id="dcad1-208">Configuring the `applicationUrl` sets the `ASPNETCORE_URLS` environment variable and overrides values set in the environment.</span></span>

### <a name="escape-environment-variables-on-linux"></a><span data-ttu-id="dcad1-209">Экранирование переменных среды в Linux</span><span class="sxs-lookup"><span data-stu-id="dcad1-209">Escape environment variables on Linux</span></span>

<span data-ttu-id="dcad1-210">В Linux значения переменных среды URL-адресов нужно экранировать, чтобы их можно было проанализировать с помощью `systemd`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-210">On Linux, the value of URL environment variables must be escaped so `systemd` can parse it.</span></span> <span data-ttu-id="dcad1-211">Используйте средство Linux `systemd-escape`, которое приостанавливает `http:--localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-211">Use the linux tool `systemd-escape` which yields `http:--localhost:5001`</span></span>
 
 ```cmd
 groot@terminus:~$ systemd-escape http://localhost:5001
 http:--localhost:5001
 ```

### <a name="display-environment-variables"></a><span data-ttu-id="dcad1-212">Отображение переменных среды</span><span class="sxs-lookup"><span data-stu-id="dcad1-212">Display environment variables</span></span>

<span data-ttu-id="dcad1-213">Следующий код позволяет отобразить переменные среды и значения при запуске приложения, что может быть полезно при отладке параметров среды:</span><span class="sxs-lookup"><span data-stu-id="dcad1-213">The following code displays the environment variables and values on application startup, which can be helpful when debugging environment settings:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples_snippets/5.x/Program.cs?name=snippet)]

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="dcad1-214">Командная строка</span><span class="sxs-lookup"><span data-stu-id="dcad1-214">Command-line</span></span>

<span data-ttu-id="dcad1-215">При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пар "ключ-значение" аргументов командной строки после следующих источников конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-215">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="dcad1-216">Файлы *appsettings.json* и *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-216">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="dcad1-217">[Секреты приложения](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="dcad1-217">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="dcad1-218">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="dcad1-218">Environment variables.</span></span>

<span data-ttu-id="dcad1-219">По [умолчанию](#default) значения конфигурации, заданные для значений конфигурации переопределения в командной строке, задаются для всех остальных поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-219">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="dcad1-220">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="dcad1-220">Command-line arguments</span></span>

<span data-ttu-id="dcad1-221">Следующая команда задает ключи и значения с помощью `=`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-221">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="dcad1-222">Следующая команда задает ключи и значения с помощью `/`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-222">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="dcad1-223">Следующая команда задает ключи и значения с помощью `--`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-223">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="dcad1-224">Значение ключа:</span><span class="sxs-lookup"><span data-stu-id="dcad1-224">The key value:</span></span>

* <span data-ttu-id="dcad1-225">Должно соответствовать `=`, или ключ должен иметь префикс `--` или `/`, когда значение следует за пробелом.</span><span class="sxs-lookup"><span data-stu-id="dcad1-225">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="dcad1-226">Является обязательным, если используется параметр `=`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-226">Isn't required if `=` is used.</span></span> <span data-ttu-id="dcad1-227">Например, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-227">For example, `MySetting=`.</span></span>

<span data-ttu-id="dcad1-228">В рамках одной и той же команды не смешивайте пары "ключ-значение" аргумента командной строки, которые используют `=` с парами "ключ-значение" с пробелом.</span><span class="sxs-lookup"><span data-stu-id="dcad1-228">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="dcad1-229">Сопоставления переключений</span><span class="sxs-lookup"><span data-stu-id="dcad1-229">Switch mappings</span></span>

<span data-ttu-id="dcad1-230">Сопоставление параметров позволяет указать логику замены имен **ключей**.</span><span class="sxs-lookup"><span data-stu-id="dcad1-230">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="dcad1-231">Предоставьте словарь замены параметров для метода <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-231">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="dcad1-232">В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки.</span><span class="sxs-lookup"><span data-stu-id="dcad1-232">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="dcad1-233">Если ключ в командной строке находится в словаре, значение словаря передается обратно, чтобы установить пару "ключ-значение" в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-233">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="dcad1-234">Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).</span><span class="sxs-lookup"><span data-stu-id="dcad1-234">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="dcad1-235">Правила ключей из словаря сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="dcad1-235">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="dcad1-236">Параметры должны начинаться с `-` или `--`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-236">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="dcad1-237">Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.</span><span class="sxs-lookup"><span data-stu-id="dcad1-237">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="dcad1-238">Чтобы использовать словарь сопоставлений параметров, передайте его в вызов `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-238">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="dcad1-239">В следующем коде показаны ключевые значения для замененных ключей:</span><span class="sxs-lookup"><span data-stu-id="dcad1-239">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="dcad1-240">Следующая команда проверяет замену ключа:</span><span class="sxs-lookup"><span data-stu-id="dcad1-240">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="dcad1-241">Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны.</span><span class="sxs-lookup"><span data-stu-id="dcad1-241">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="dcad1-242">Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные параметры, и нет возможности передать словарь сопоставления параметров в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-242">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="dcad1-243">Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.</span><span class="sxs-lookup"><span data-stu-id="dcad1-243">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="dcad1-244">Иерархическая модель конфигурации</span><span class="sxs-lookup"><span data-stu-id="dcad1-244">Hierarchical configuration data</span></span>

<span data-ttu-id="dcad1-245">API конфигурации считывает иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-245">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="dcad1-246">[Пример скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="dcad1-246">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="dcad1-247">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-247">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="dcad1-248">Предпочтительный способ чтения иерархических данных конфигурации — использование шаблона параметров.</span><span class="sxs-lookup"><span data-stu-id="dcad1-248">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="dcad1-249">Дополнительные сведения см. в разделе [Привязка иерархических данных конфигурации](#optpat) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="dcad1-249">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="dcad1-250">Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-250"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="dcad1-251">Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="dcad1-251">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="dcad1-252">Ключи и значения конфигурации</span><span class="sxs-lookup"><span data-stu-id="dcad1-252">Configuration keys and values</span></span>

<span data-ttu-id="dcad1-253">Ключи конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-253">Configuration keys:</span></span>

* <span data-ttu-id="dcad1-254">Не учитывают регистр.</span><span class="sxs-lookup"><span data-stu-id="dcad1-254">Are case-insensitive.</span></span> <span data-ttu-id="dcad1-255">Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.</span><span class="sxs-lookup"><span data-stu-id="dcad1-255">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="dcad1-256">Если ключ и значение заданы более чем в одном поставщике конфигурации, используется значение из последнего добавленного поставщика.</span><span class="sxs-lookup"><span data-stu-id="dcad1-256">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="dcad1-257">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="dcad1-257">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="dcad1-258">Иерархические ключи</span><span class="sxs-lookup"><span data-stu-id="dcad1-258">Hierarchical keys</span></span>
  * <span data-ttu-id="dcad1-259">При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="dcad1-259">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="dcad1-260">В переменных среды разделитель-двоеточие может не работать на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="dcad1-260">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="dcad1-261">Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие — `:`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-261">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="dcad1-262">В Azure Key Vault иерархические ключи используют `--` в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="dcad1-262">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="dcad1-263">[Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) автоматически заменяет `--` на `:` при загрузке секретов в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-263">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="dcad1-264"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-264">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="dcad1-265">Привязка массива описана в разделе [Привязка массива к классу](#boa).</span><span class="sxs-lookup"><span data-stu-id="dcad1-265">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="dcad1-266">Значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-266">Configuration values:</span></span>

* <span data-ttu-id="dcad1-267">Представляют собой строки.</span><span class="sxs-lookup"><span data-stu-id="dcad1-267">Are strings.</span></span>
* <span data-ttu-id="dcad1-268">Значение NULL не может храниться в конфигурации или быть привязанным к объектам.</span><span class="sxs-lookup"><span data-stu-id="dcad1-268">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="dcad1-269">Поставщики конфигурации</span><span class="sxs-lookup"><span data-stu-id="dcad1-269">Configuration providers</span></span>

<span data-ttu-id="dcad1-270">В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dcad1-270">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="dcad1-271">Поставщик</span><span class="sxs-lookup"><span data-stu-id="dcad1-271">Provider</span></span> | <span data-ttu-id="dcad1-272">Предоставляет конфигурацию из</span><span class="sxs-lookup"><span data-stu-id="dcad1-272">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="dcad1-273">Поставщик конфигурации Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="dcad1-273">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="dcad1-274">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="dcad1-274">Azure Key Vault</span></span> |
| [<span data-ttu-id="dcad1-275">Поставщик конфигурации приложения Azure</span><span class="sxs-lookup"><span data-stu-id="dcad1-275">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="dcad1-276">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="dcad1-276">Azure App Configuration</span></span> |
| [<span data-ttu-id="dcad1-277">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="dcad1-277">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="dcad1-278">Параметры командной строки</span><span class="sxs-lookup"><span data-stu-id="dcad1-278">Command-line parameters</span></span> |
| [<span data-ttu-id="dcad1-279">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="dcad1-279">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="dcad1-280">Источник пользователя</span><span class="sxs-lookup"><span data-stu-id="dcad1-280">Custom source</span></span> |
| [<span data-ttu-id="dcad1-281">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="dcad1-281">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="dcad1-282">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="dcad1-282">Environment variables</span></span> |
| [<span data-ttu-id="dcad1-283">Поставщик конфигурации файла</span><span class="sxs-lookup"><span data-stu-id="dcad1-283">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="dcad1-284">Файлы INI, JSON и XML</span><span class="sxs-lookup"><span data-stu-id="dcad1-284">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="dcad1-285">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="dcad1-285">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="dcad1-286">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="dcad1-286">Directory files</span></span> |
| [<span data-ttu-id="dcad1-287">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="dcad1-287">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="dcad1-288">Коллекции оперативной памяти</span><span class="sxs-lookup"><span data-stu-id="dcad1-288">In-memory collections</span></span> |
| [<span data-ttu-id="dcad1-289">Секреты пользователя</span><span class="sxs-lookup"><span data-stu-id="dcad1-289">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="dcad1-290">Файл в каталоге профиля пользователя</span><span class="sxs-lookup"><span data-stu-id="dcad1-290">File in the user profile directory</span></span> |

<span data-ttu-id="dcad1-291">Источники конфигурации считываются в том порядке, в котором указываются их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-291">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="dcad1-292">Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.</span><span class="sxs-lookup"><span data-stu-id="dcad1-292">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="dcad1-293">Типичная последовательность поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-293">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="dcad1-294">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="dcad1-294">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="dcad1-295">Секреты пользователя</span><span class="sxs-lookup"><span data-stu-id="dcad1-295">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="dcad1-296">Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="dcad1-296">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="dcad1-297">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dcad1-297">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="dcad1-298">Общепринятой практикой является добавление поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="dcad1-298">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="dcad1-299">Предыдущая последовательность поставщиков используется в [конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="dcad1-299">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="dcad1-300">Префиксы строк подключения</span><span class="sxs-lookup"><span data-stu-id="dcad1-300">Connection string prefixes</span></span>

<span data-ttu-id="dcad1-301">API конфигурации имеет особые правила обработки для четырех переменных среды строки подключения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-301">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="dcad1-302">Эти строки подключения участвуют в настройке строк подключения Azure для среды приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-302">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="dcad1-303">Переменные среды с префиксами, указанными в таблице, загружаются в приложение с [конфигурацией по умолчанию](#default) или если префикс не предоставлен для `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-303">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="dcad1-304">Префикс строки подключения</span><span class="sxs-lookup"><span data-stu-id="dcad1-304">Connection string prefix</span></span> | <span data-ttu-id="dcad1-305">Поставщик</span><span class="sxs-lookup"><span data-stu-id="dcad1-305">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="dcad1-306">Поставщик пользователя</span><span class="sxs-lookup"><span data-stu-id="dcad1-306">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="dcad1-307">MySQL</span><span class="sxs-lookup"><span data-stu-id="dcad1-307">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="dcad1-308">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="dcad1-308">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="dcad1-309">SQL Server</span><span class="sxs-lookup"><span data-stu-id="dcad1-309">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="dcad1-310">Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="dcad1-310">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="dcad1-311">Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="dcad1-311">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="dcad1-312">Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).</span><span class="sxs-lookup"><span data-stu-id="dcad1-312">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="dcad1-313">Ключ переменной среды</span><span class="sxs-lookup"><span data-stu-id="dcad1-313">Environment variable key</span></span> | <span data-ttu-id="dcad1-314">Преобразованный ключ конфигурации</span><span class="sxs-lookup"><span data-stu-id="dcad1-314">Converted configuration key</span></span> | <span data-ttu-id="dcad1-315">Запись конфигурации поставщика</span><span class="sxs-lookup"><span data-stu-id="dcad1-315">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dcad1-316">Запись конфигурации не создана.</span><span class="sxs-lookup"><span data-stu-id="dcad1-316">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dcad1-317">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-317">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="dcad1-318">Значение: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="dcad1-318">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dcad1-319">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-319">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="dcad1-320">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="dcad1-320">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dcad1-321">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-321">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="dcad1-322">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="dcad1-322">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="dcad1-323">Поставщик конфигурации файла</span><span class="sxs-lookup"><span data-stu-id="dcad1-323">File configuration provider</span></span>

<span data-ttu-id="dcad1-324"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы.</span><span class="sxs-lookup"><span data-stu-id="dcad1-324"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="dcad1-325">Следующие поставщики конфигурации являются производными от `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-325">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="dcad1-326">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="dcad1-326">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="dcad1-327">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="dcad1-327">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="dcad1-328">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="dcad1-328">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="dcad1-329">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="dcad1-329">INI configuration provider</span></span>

<span data-ttu-id="dcad1-330"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-330">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="dcad1-331">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-331">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="dcad1-332">В приведенном выше коде параметры в *MyIniConfig.ini* и *MyIniConfig*.`Environment`.*ini* переопределяются параметрами в следующих поставщиках:</span><span class="sxs-lookup"><span data-stu-id="dcad1-332">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* <span data-ttu-id="dcad1-333">[Поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="dcad1-333">[Environment variables configuration provider](#evcp)</span></span>
* <span data-ttu-id="dcad1-334">[Поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="dcad1-334">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="dcad1-335">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-335">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="dcad1-336">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-336">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="dcad1-337">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="dcad1-337">JSON configuration provider</span></span>

<span data-ttu-id="dcad1-338"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ-значение" JSON-файла.</span><span class="sxs-lookup"><span data-stu-id="dcad1-338">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="dcad1-339">Перегрузки могут указывать:</span><span class="sxs-lookup"><span data-stu-id="dcad1-339">Overloads can specify:</span></span>

* <span data-ttu-id="dcad1-340">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="dcad1-340">Whether the file is optional.</span></span>
* <span data-ttu-id="dcad1-341">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="dcad1-341">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="dcad1-342">Рассмотрим следующий код.</span><span class="sxs-lookup"><span data-stu-id="dcad1-342">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="dcad1-343">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="dcad1-343">The preceding code:</span></span>

* <span data-ttu-id="dcad1-344">Настраивает поставщик конфигурации JSON для загрузки файла *MyConfig.json* со следующими параметрами:</span><span class="sxs-lookup"><span data-stu-id="dcad1-344">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="dcad1-345">`optional: true`. Файл является необязательным.</span><span class="sxs-lookup"><span data-stu-id="dcad1-345">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="dcad1-346">`reloadOnChange: true`: При сохранении изменений файл перезагружается.</span><span class="sxs-lookup"><span data-stu-id="dcad1-346">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="dcad1-347">Считывает [поставщиков конфигурации по умолчанию](#default) до файла *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-347">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="dcad1-348">Параметры в файле *MyConfig.json* переопределяют параметр в поставщиках конфигурации по умолчанию, включая [поставщик конфигурация переменных среды](#evcp) и [поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="dcad1-348">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="dcad1-349">Обычно ***не*** требуется, чтобы пользовательские файлы JSON переопределяли значения, заданные в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="dcad1-349">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="dcad1-350">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-350">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="dcad1-351">В приведенном выше коде параметры в файлах *MyConfig.json* и *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-351">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="dcad1-352">Переопределяют параметры в файлах *appsettings.json* и *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-352">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="dcad1-353">Переопределяются параметрами в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="dcad1-353">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="dcad1-354">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-354">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="dcad1-355">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-355">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="dcad1-356">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="dcad1-356">XML configuration provider</span></span>

<span data-ttu-id="dcad1-357"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-357">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="dcad1-358">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-358">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="dcad1-359">В приведенном выше коде параметры в *MyXMLFile.xml* и *MyXMLFile*.`Environment`.*xml* переопределяются параметрами в следующих поставщиках:</span><span class="sxs-lookup"><span data-stu-id="dcad1-359">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* <span data-ttu-id="dcad1-360">[Поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="dcad1-360">[Environment variables configuration provider](#evcp)</span></span>
* <span data-ttu-id="dcad1-361">[Поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="dcad1-361">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="dcad1-362">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-362">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="dcad1-363">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-363">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="dcad1-364">Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.</span><span class="sxs-lookup"><span data-stu-id="dcad1-364">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="dcad1-365">Следующий код считывает предыдущий файл конфигурации и отображает ключи и значения:</span><span class="sxs-lookup"><span data-stu-id="dcad1-365">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="dcad1-366">Атрибуты можно использовать для предоставления значений.</span><span class="sxs-lookup"><span data-stu-id="dcad1-366">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="dcad1-367">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-367">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="dcad1-368">key:attribute</span><span class="sxs-lookup"><span data-stu-id="dcad1-368">key:attribute</span></span>
* <span data-ttu-id="dcad1-369">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="dcad1-369">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="dcad1-370">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="dcad1-370">Key-per-file configuration provider</span></span>

<span data-ttu-id="dcad1-371"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-371">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="dcad1-372">Ключ является именем файла.</span><span class="sxs-lookup"><span data-stu-id="dcad1-372">The key is the file name.</span></span> <span data-ttu-id="dcad1-373">Значение содержит содержимое файла.</span><span class="sxs-lookup"><span data-stu-id="dcad1-373">The value contains the file's contents.</span></span> <span data-ttu-id="dcad1-374">Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.</span><span class="sxs-lookup"><span data-stu-id="dcad1-374">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="dcad1-375">Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-375">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="dcad1-376">Значение параметра `directoryPath` должно быть абсолютным путем к файлам.</span><span class="sxs-lookup"><span data-stu-id="dcad1-376">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="dcad1-377">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="dcad1-377">Overloads permit specifying:</span></span>

* <span data-ttu-id="dcad1-378">`Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.</span><span class="sxs-lookup"><span data-stu-id="dcad1-378">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="dcad1-379">Обязательно ли указывать каталог и путь к каталогу.</span><span class="sxs-lookup"><span data-stu-id="dcad1-379">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="dcad1-380">Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов.</span><span class="sxs-lookup"><span data-stu-id="dcad1-380">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="dcad1-381">Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-381">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="dcad1-382">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-382">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="dcad1-383">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="dcad1-383">Memory configuration provider</span></span>

<span data-ttu-id="dcad1-384"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-384">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="dcad1-385">Следующий код добавляет коллекцию памяти в систему конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-385">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="dcad1-386">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются перечисленные выше параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-386">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="dcad1-387">В предыдущем коде `config.AddInMemoryCollection(Dict)` добавляется после [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="dcad1-387">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="dcad1-388">Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="dcad1-388">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="dcad1-389">В разделе [Привязка массива](#boa) вы найдете еще один пример использования `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-389">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-5.0"

<a name="kestrel"></a>

## <a name="kestrel-endpoint-configuration"></a><span data-ttu-id="dcad1-390">Конфигурация конечных точек Kestrel</span><span class="sxs-lookup"><span data-stu-id="dcad1-390">Kestrel endpoint configuration</span></span>

<span data-ttu-id="dcad1-391">Конкретная конфигурация конечной точки Kestrel переопределяет все конфигурации [межсерверных](xref:fundamentals/servers/index) конечных точек.</span><span class="sxs-lookup"><span data-stu-id="dcad1-391">Kestrel specific endpoint configuration overrides all [cross-server](xref:fundamentals/servers/index) endpoint configurations.</span></span> <span data-ttu-id="dcad1-392">Конфигурации межсерверных конечных точек включают:</span><span class="sxs-lookup"><span data-stu-id="dcad1-392">Cross-server endpoint configurations include:</span></span>

  * <span data-ttu-id="dcad1-393">[UseUrls](xref:fundamentals/host/web-host#server-urls).</span><span class="sxs-lookup"><span data-stu-id="dcad1-393">[UseUrls](xref:fundamentals/host/web-host#server-urls)</span></span>
  * <span data-ttu-id="dcad1-394">`--urls` в [командной строке](xref:fundamentals/configuration/index#command-line);</span><span class="sxs-lookup"><span data-stu-id="dcad1-394">`--urls` on the [command line](xref:fundamentals/configuration/index#command-line)</span></span>
  * <span data-ttu-id="dcad1-395">[переменную среды](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-395">The [environment variable](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="dcad1-396">Обратите внимание на следующий файл *appsettings.json* , используемый в веб-приложении ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="dcad1-396">Consider the following *appsettings.json* file used in an ASP.NET Core web app:</span></span>

[!code-json[](~/fundamentals/configuration/index/samples_snippets/5.x/appsettings.json?highlight=2-8)]

<span data-ttu-id="dcad1-397">Если предыдущая выделенная разметка используется в веб-приложении ASP.NET Core ***и*** приложение запускается в командной строке со следующей конфигурацией межсерверной конечной точки:</span><span class="sxs-lookup"><span data-stu-id="dcad1-397">When the preceding highlighted markup is used in an ASP.NET Core web app ***and*** the app is launched on the command line with the following cross-server endpoint configuration:</span></span>

`dotnet run --urls="https://localhost:7777"`

<span data-ttu-id="dcad1-398">Kestrel привязывается к конечной точке, настроенной специально для Kestrel в файле *appsettings.json* (`https://localhost:9999`, а не `https://localhost:7777`).</span><span class="sxs-lookup"><span data-stu-id="dcad1-398">Kestrel binds to the endpoint configured specifically for Kestrel in the *appsettings.json* file (`https://localhost:9999`) and not `https://localhost:7777`.</span></span>

<span data-ttu-id="dcad1-399">Обратите внимание на конечную точку Kestrel, настроенную в качестве переменной среды:</span><span class="sxs-lookup"><span data-stu-id="dcad1-399">Consider the Kestrel specific endpoint configured as an environment variable:</span></span>

`set Kestrel__Endpoints__Https__Url=https://localhost:8888`

<span data-ttu-id="dcad1-400">В предыдущей переменной среды `Https` — имя конкретной конечной точки Kestrel.</span><span class="sxs-lookup"><span data-stu-id="dcad1-400">In the preceding environment variable, `Https` is the name of the Kestrel specific endpoint.</span></span> <span data-ttu-id="dcad1-401">В предыдущем файле *appsettings.json* также определяется конечная точка Kestrel с именем `Https`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-401">The preceding *appsettings.json* file also defines a Kestrel specific endpoint named `Https`.</span></span> <span data-ttu-id="dcad1-402">По [умолчанию](#default-configuration) переменные среды, использующие [поставщик конфигурации переменных среды](#evcp) считываются после *appsettings.* `Environment` *.json*. Поэтому для конечной точки `Https` используется предыдущая переменная среды.</span><span class="sxs-lookup"><span data-stu-id="dcad1-402">By [default](#default-configuration), environment variables using the [Environment Variables configuration provider](#evcp) are read after *appsettings.*`Environment`*.json*, therefore, the preceding environment variable is used for the `Https` endpoint.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-3.0"

## <a name="getvalue"></a><span data-ttu-id="dcad1-403">GetValue</span><span class="sxs-lookup"><span data-stu-id="dcad1-403">GetValue</span></span>

<span data-ttu-id="dcad1-404">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип:</span><span class="sxs-lookup"><span data-stu-id="dcad1-404">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="dcad1-405">В приведенном выше коде, если `NumberKey` отсутствует в конфигурации, используется значение по умолчанию `99`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-405">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="dcad1-406">GetSection, GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="dcad1-406">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="dcad1-407">В следующих примерах мы рассмотрим следующий файл *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-407">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="dcad1-408">Следующий код добавляет *MySubsection.json* к поставщикам конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-408">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="dcad1-409">GetSection</span><span class="sxs-lookup"><span data-stu-id="dcad1-409">GetSection</span></span>

<span data-ttu-id="dcad1-410">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) возвращает подраздел конфигурации с указанным ключом подраздела.</span><span class="sxs-lookup"><span data-stu-id="dcad1-410">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="dcad1-411">Следующий код возвращает значения для `section1`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-411">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="dcad1-412">Следующий код возвращает значения для `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-412">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="dcad1-413">Значение `GetSection` никогда не возвращает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-413">`GetSection` never returns `null`.</span></span> <span data-ttu-id="dcad1-414">Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-414">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="dcad1-415">Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется.</span><span class="sxs-lookup"><span data-stu-id="dcad1-415">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="dcad1-416"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.</span><span class="sxs-lookup"><span data-stu-id="dcad1-416">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="dcad1-417">GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="dcad1-417">GetChildren and Exists</span></span>

<span data-ttu-id="dcad1-418">Следующий код вызывает [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) и возвращает значения для `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-418">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="dcad1-419">Приведенный выше код вызывает [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) для проверки существования раздела:</span><span class="sxs-lookup"><span data-stu-id="dcad1-419">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="dcad1-420">Привязка массива</span><span class="sxs-lookup"><span data-stu-id="dcad1-420">Bind an array</span></span>

<span data-ttu-id="dcad1-421">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) поддерживает привязку массивов к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-421">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="dcad1-422">Любой формат массива, который предоставляет сегмент числового ключа способен привязать массив к массиву класса [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="dcad1-422">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="dcad1-423">Рассмотрим *MyArray.json* из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="dcad1-423">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="dcad1-424">Следующий код добавляет *MyArray.json* к поставщикам конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-424">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="dcad1-425">Следующий код считывает конфигурацию и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="dcad1-425">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="dcad1-426">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="dcad1-426">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="dcad1-427">В предыдущих выходных данных индекс 3 имеет значение `value40`, соответствующее `"4": "value40",` в *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-427">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="dcad1-428">Индексы привязанного массива непрерывны и не привязаны к индексу ключа конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-428">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="dcad1-429">Модуль привязки конфигурации не поддерживает привязку значений NULL или создание записей NULL в связанных объектах</span><span class="sxs-lookup"><span data-stu-id="dcad1-429">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="dcad1-430">Следующий код загружает конфигурацию `array:entries` с помощью метода расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>:</span><span class="sxs-lookup"><span data-stu-id="dcad1-430">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="dcad1-431">Следующий код считывает конфигурацию в `arrayDict` `Dictionary` и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="dcad1-431">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="dcad1-432">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="dcad1-432">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="dcad1-433">Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-433">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="dcad1-434">При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы используются для выполнения итерации данных конфигурации при создании объекта.</span><span class="sxs-lookup"><span data-stu-id="dcad1-434">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="dcad1-435">Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.</span><span class="sxs-lookup"><span data-stu-id="dcad1-435">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="dcad1-436">Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который считывает пару "ключ-значение" индекса &num;3.</span><span class="sxs-lookup"><span data-stu-id="dcad1-436">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="dcad1-437">Рассмотрим следующий файл *Value3.json* из примера загрузки:</span><span class="sxs-lookup"><span data-stu-id="dcad1-437">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="dcad1-438">Следующий код включает конфигурацию для *Value3.json* и `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-438">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="dcad1-439">Следующий код считывает предыдущую конфигурацию и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="dcad1-439">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="dcad1-440">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="dcad1-440">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="dcad1-441">Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.</span><span class="sxs-lookup"><span data-stu-id="dcad1-441">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="dcad1-442">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="dcad1-442">Custom configuration provider</span></span>

<span data-ttu-id="dcad1-443">Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="dcad1-443">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="dcad1-444">Поставщик имеет следующие характеристики.</span><span class="sxs-lookup"><span data-stu-id="dcad1-444">The provider has the following characteristics:</span></span>

* <span data-ttu-id="dcad1-445">База данных в памяти EF используется для демонстрационных целей.</span><span class="sxs-lookup"><span data-stu-id="dcad1-445">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="dcad1-446">Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-446">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="dcad1-447">Поставщик считывает таблицу базы данных в конфигурации при запуске.</span><span class="sxs-lookup"><span data-stu-id="dcad1-447">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="dcad1-448">Поставщик не запрашивает базу данных для каждого ключа.</span><span class="sxs-lookup"><span data-stu-id="dcad1-448">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="dcad1-449">Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-449">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="dcad1-450">Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.</span><span class="sxs-lookup"><span data-stu-id="dcad1-450">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="dcad1-451">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-451">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="dcad1-452">Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.</span><span class="sxs-lookup"><span data-stu-id="dcad1-452">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="dcad1-453">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-453">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="dcad1-454">Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-454">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="dcad1-455">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-455">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="dcad1-456">Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-456">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="dcad1-457">Поставщик конфигурации инициализирует пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="dcad1-457">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="dcad1-458">Поскольку [конфигурационные ключи не учитывают регистр](#keys), словарь, используемый для инициализации базы данных, создается с помощью функции сравнения без учета регистра ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="dcad1-458">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="dcad1-459">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-459">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="dcad1-460">Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-460">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="dcad1-461">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-461">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="dcad1-462">В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-462">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="dcad1-463">Доступ к конфигурации во время запуска</span><span class="sxs-lookup"><span data-stu-id="dcad1-463">Access configuration in Startup</span></span>

<span data-ttu-id="dcad1-464">В следующем коде отображаются данные конфигурации в методах `Startup`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-464">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="dcad1-465">Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="dcad1-465">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="dcad1-466">Конфигурация доступа в RazorPages</span><span class="sxs-lookup"><span data-stu-id="dcad1-466">Access configuration in Razor Pages</span></span>

<span data-ttu-id="dcad1-467">В следующем коде отображаются данные конфигурации в RazorPage:</span><span class="sxs-lookup"><span data-stu-id="dcad1-467">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="dcad1-468">В следующем коде `MyOptions` добавляется в контейнер службы с помощью интерфейса <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> и привязывается к конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-468">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="dcad1-469">В следующей разметке для разрешения и вывода значений параметров используется директива Razor [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="dcad1-469">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="dcad1-470">Доступ к конфигурации в файле представления MVC</span><span class="sxs-lookup"><span data-stu-id="dcad1-470">Access configuration in a MVC view file</span></span>

<span data-ttu-id="dcad1-471">В следующем коде отображаются данные конфигурации в представлении MVC:</span><span class="sxs-lookup"><span data-stu-id="dcad1-471">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="dcad1-472">Настройка параметров с помощью делегата</span><span class="sxs-lookup"><span data-stu-id="dcad1-472">Configure options with a delegate</span></span>

<span data-ttu-id="dcad1-473">Параметры, настроенные в делегате, переопределяют значения, заданные в поставщиках конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-473">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="dcad1-474">Настройка параметров с помощью делегата демонстрируется в примере 2 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-474">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="dcad1-475">В приведенном ниже коде в контейнер службы добавляется служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-475">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="dcad1-476">Она использует делегат для настройки значений для `MyOptions`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-476">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="dcad1-477">Следующий код отображает значения параметров:</span><span class="sxs-lookup"><span data-stu-id="dcad1-477">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="dcad1-478">В предыдущем примере значения `Option1` и `Option2` задаются в файле *appsettings.json* , а затем переопределяются настроенным делегатом.</span><span class="sxs-lookup"><span data-stu-id="dcad1-478">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="dcad1-479">Конфигурация узла и приложения</span><span class="sxs-lookup"><span data-stu-id="dcad1-479">Host versus app configuration</span></span>

<span data-ttu-id="dcad1-480">Перед настройкой и запуском приложения настройте и запустите *узел*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-480">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="dcad1-481">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="dcad1-481">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="dcad1-482">Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="dcad1-482">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="dcad1-483">Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-483">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="dcad1-484">Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-484">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="dcad1-485">Конфигурация узла по умолчанию</span><span class="sxs-lookup"><span data-stu-id="dcad1-485">Default host configuration</span></span>

<span data-ttu-id="dcad1-486">Подробные сведения о конфигурации по умолчанию при использовании [веб-узла](xref:fundamentals/host/web-host) см. в [разделе о версии ASP.NET Core 2.2 в этой статье](?view=aspnetcore-2.2&preserve-view=true).</span><span class="sxs-lookup"><span data-stu-id="dcad1-486">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2&preserve-view=true).</span></span>

* <span data-ttu-id="dcad1-487">Существуют следующие способы предоставления конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="dcad1-487">Host configuration is provided from:</span></span>
  * <span data-ttu-id="dcad1-488">Переменные среды с префиксом `DOTNET_` (например, `DOTNET_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="dcad1-488">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="dcad1-489">Префикс (`DOTNET_`) исключается при загрузке пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-489">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="dcad1-490">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dcad1-490">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="dcad1-491">Устанавливается конфигурация веб-узла по умолчанию (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="dcad1-491">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="dcad1-492">Kestrel используется в качестве веб-сервера и настраивается с помощью поставщиков конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-492">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="dcad1-493">Добавьте ПО промежуточного слоя фильтрации узлов.</span><span class="sxs-lookup"><span data-stu-id="dcad1-493">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="dcad1-494">Если переменной среды `ASPNETCORE_FORWARDEDHEADERS_ENABLED` присвоено значение `true`, добавьте ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="dcad1-494">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="dcad1-495">Включите интеграцию служб IIS.</span><span class="sxs-lookup"><span data-stu-id="dcad1-495">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="dcad1-496">Другая конфигурация</span><span class="sxs-lookup"><span data-stu-id="dcad1-496">Other configuration</span></span>

<span data-ttu-id="dcad1-497">Этот раздел относится только к *конфигурации приложений*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-497">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="dcad1-498">Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="dcad1-498">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="dcad1-499">*launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="dcad1-499">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="dcad1-500">В <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-500">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="dcad1-501">В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.</span><span class="sxs-lookup"><span data-stu-id="dcad1-501">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="dcad1-502">*web.config* — это файл конфигурации сервера, описанный в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="dcad1-502">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="dcad1-503">Переменные среды, заданные в *launchSettings.json*, переопределяют переменные, заданные в системной среде.</span><span class="sxs-lookup"><span data-stu-id="dcad1-503">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="dcad1-504">См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-504">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="dcad1-505">Добавление конфигурации из внешней сборки</span><span class="sxs-lookup"><span data-stu-id="dcad1-505">Add configuration from an external assembly</span></span>

<span data-ttu-id="dcad1-506">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-506">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="dcad1-507">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-507">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dcad1-508">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="dcad1-508">Additional resources</span></span>

* [<span data-ttu-id="dcad1-509">Исходный код конфигурации</span><span class="sxs-lookup"><span data-stu-id="dcad1-509">Configuration source code</span></span>](https://github.com/dotnet/runtime/tree/master/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dcad1-510">Конфигурация приложения в ASP.NET Core основана на парах "ключ — значение", установленных *поставщиками конфигурации*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-510">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="dcad1-511">Поставщики конфигурации получают данные конфигурации в парах "ключ — значение" из различных источников:</span><span class="sxs-lookup"><span data-stu-id="dcad1-511">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="dcad1-512">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="dcad1-512">Azure Key Vault</span></span>
* <span data-ttu-id="dcad1-513">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="dcad1-513">Azure App Configuration</span></span>
* <span data-ttu-id="dcad1-514">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="dcad1-514">Command-line arguments</span></span>
* <span data-ttu-id="dcad1-515">а также пользовательские поставщики (устанавливаемые или создаваемые).</span><span class="sxs-lookup"><span data-stu-id="dcad1-515">Custom providers (installed or created)</span></span>
* <span data-ttu-id="dcad1-516">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="dcad1-516">Directory files</span></span>
* <span data-ttu-id="dcad1-517">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="dcad1-517">Environment variables</span></span>
* <span data-ttu-id="dcad1-518">объектов .NET в памяти;</span><span class="sxs-lookup"><span data-stu-id="dcad1-518">In-memory .NET objects</span></span>
* <span data-ttu-id="dcad1-519">файлов параметров;</span><span class="sxs-lookup"><span data-stu-id="dcad1-519">Settings files</span></span>

<span data-ttu-id="dcad1-520">Пакеты конфигурации для распространенных вариантов провайдеров конфигурации ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) включаются в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="dcad1-520">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="dcad1-521">В приведенных ниже и представленных в образце приложения примерах кода используется пространство имен <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="dcad1-521">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="dcad1-522">*Шаблон параметров* является расширением конфигурации основных понятий, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="dcad1-522">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="dcad1-523">Параметры используют классы для представления групп связанных настроек.</span><span class="sxs-lookup"><span data-stu-id="dcad1-523">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="dcad1-524">Для получения дополнительной информации см. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-524">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="dcad1-525">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dcad1-525">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="dcad1-526">Конфигурация узла и приложения</span><span class="sxs-lookup"><span data-stu-id="dcad1-526">Host versus app configuration</span></span>

<span data-ttu-id="dcad1-527">Перед настройкой и запуском приложения настройте и запустите *узел*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-527">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="dcad1-528">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="dcad1-528">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="dcad1-529">Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="dcad1-529">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="dcad1-530">Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-530">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="dcad1-531">Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-531">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="dcad1-532">Другая конфигурация</span><span class="sxs-lookup"><span data-stu-id="dcad1-532">Other configuration</span></span>

<span data-ttu-id="dcad1-533">Этот раздел относится только к *конфигурации приложений*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-533">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="dcad1-534">Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="dcad1-534">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="dcad1-535">*launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="dcad1-535">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="dcad1-536">В <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-536">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="dcad1-537">В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.</span><span class="sxs-lookup"><span data-stu-id="dcad1-537">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="dcad1-538">*web.config* — это файл конфигурации сервера, описанный в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="dcad1-538">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="dcad1-539">См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-539">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="dcad1-540">Конфигурация по умолчанию</span><span class="sxs-lookup"><span data-stu-id="dcad1-540">Default configuration</span></span>

<span data-ttu-id="dcad1-541">Веб-приложения на основе шаблонов [dotnet new](/dotnet/core/tools/dotnet-new) ASP.NET Core вызывают <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> при создании узла.</span><span class="sxs-lookup"><span data-stu-id="dcad1-541">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="dcad1-542">`CreateDefaultBuilder` предоставляет конфигурацию по умолчанию для приложения в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="dcad1-542">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="dcad1-543">Приведенные ниже сведения относятся к приложениям, использующим [веб-узел](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="dcad1-543">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="dcad1-544">Подробные сведения о конфигурации по умолчанию при использовании [универсального узла](xref:fundamentals/host/generic-host) см. в [последней версии этой статьи](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="dcad1-544">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="dcad1-545">Существуют следующие способы предоставления конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="dcad1-545">Host configuration is provided from:</span></span>
  * <span data-ttu-id="dcad1-546">Переменные среды с префиксом `ASPNETCORE_` (например, `ASPNETCORE_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dcad1-546">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="dcad1-547">Префикс (`ASPNETCORE_`) исключается при загрузке пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-547">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="dcad1-548">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dcad1-548">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="dcad1-549">Конфигурация приложения предоставляется из следующих ресурсов:</span><span class="sxs-lookup"><span data-stu-id="dcad1-549">App configuration is provided from:</span></span>
  * <span data-ttu-id="dcad1-550">Файл *appsettings.json* , использующий [поставщик конфигурации файлов](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dcad1-550">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="dcad1-551">Файл *appsettings.{Environment}.json*, использующий [поставщик конфигурации файлов](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dcad1-551">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="dcad1-552">[секреты пользователя](xref:security/app-secrets), когда приложение выполняется в среде `Development` с использованием начальных сборок;</span><span class="sxs-lookup"><span data-stu-id="dcad1-552">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="dcad1-553">Переменные среды, использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dcad1-553">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="dcad1-554">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dcad1-554">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="dcad1-555">Безопасность</span><span class="sxs-lookup"><span data-stu-id="dcad1-555">Security</span></span>

<span data-ttu-id="dcad1-556">Применяйте описанные ниже методики для защиты конфиденциальных данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-556">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="dcad1-557">Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.</span><span class="sxs-lookup"><span data-stu-id="dcad1-557">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="dcad1-558">Не используйте секреты рабочей среды в средах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="dcad1-558">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="dcad1-559">Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.</span><span class="sxs-lookup"><span data-stu-id="dcad1-559">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="dcad1-560">Дополнительные сведения см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="dcad1-560">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="dcad1-561"><xref:security/app-secrets>. Содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="dcad1-561"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="dcad1-562">Диспетчер секретов использует поставщик конфигурации файла для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.</span><span class="sxs-lookup"><span data-stu-id="dcad1-562">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="dcad1-563">Поставщик конфигурации файлов описан ниже в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="dcad1-563">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="dcad1-564">В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dcad1-564">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="dcad1-565">Для получения дополнительной информации см. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-565">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="dcad1-566">Иерархическая модель конфигурации</span><span class="sxs-lookup"><span data-stu-id="dcad1-566">Hierarchical configuration data</span></span>

<span data-ttu-id="dcad1-567">API конфигурации способен поддерживать иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-567">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="dcad1-568">В следующем файле JSON существуют четыре ключа в структурированной иерархии двух разделов.</span><span class="sxs-lookup"><span data-stu-id="dcad1-568">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="dcad1-569">При считывании файла в конфигурацию для сохранения исходной иерархической структуры данных источника конфигурации создаются уникальные ключи.</span><span class="sxs-lookup"><span data-stu-id="dcad1-569">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="dcad1-570">Разделы и ключи преобразовываются в плоскую структуру с использованием двоеточия (`:`) для сохранения исходной структуры.</span><span class="sxs-lookup"><span data-stu-id="dcad1-570">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="dcad1-571">section0:key0</span><span class="sxs-lookup"><span data-stu-id="dcad1-571">section0:key0</span></span>
* <span data-ttu-id="dcad1-572">section0:key1</span><span class="sxs-lookup"><span data-stu-id="dcad1-572">section0:key1</span></span>
* <span data-ttu-id="dcad1-573">section1:key0</span><span class="sxs-lookup"><span data-stu-id="dcad1-573">section1:key0</span></span>
* <span data-ttu-id="dcad1-574">section1:key1</span><span class="sxs-lookup"><span data-stu-id="dcad1-574">section1:key1</span></span>

<span data-ttu-id="dcad1-575">Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-575"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="dcad1-576">Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="dcad1-576">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="dcad1-577">Соглашения</span><span class="sxs-lookup"><span data-stu-id="dcad1-577">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="dcad1-578">Источники и поставщики</span><span class="sxs-lookup"><span data-stu-id="dcad1-578">Sources and providers</span></span>

<span data-ttu-id="dcad1-579">При запуске приложения источники конфигурации считываются в порядке, в котором были указаны их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-579">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="dcad1-580">Поставщики конфигурации, которые реализуют обнаружение изменений, имеют возможность перезагрузить конфигурацию при изменении базовых параметров.</span><span class="sxs-lookup"><span data-stu-id="dcad1-580">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="dcad1-581">Так, поставщик файла конфигурации (подробнее о нем далее в этой статье) и [поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) реализуют обнаружение изменений.</span><span class="sxs-lookup"><span data-stu-id="dcad1-581">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="dcad1-582">Объект <xref:Microsoft.Extensions.Configuration.IConfiguration> доступен в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-582"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="dcad1-583"><xref:Microsoft.Extensions.Configuration.IConfiguration> можно внедрить в <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Razor Pages или <xref:Microsoft.AspNetCore.Mvc.Controller> MVC, чтобы получить конфигурацию для класса.</span><span class="sxs-lookup"><span data-stu-id="dcad1-583"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="dcad1-584">В следующих примерах поле `_config` используется для доступа к значениям конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-584">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="dcad1-585">Поставщики конфигурации не могут использовать контейнер DI, так как он недоступен при настройке узла.</span><span class="sxs-lookup"><span data-stu-id="dcad1-585">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="dcad1-586">Клавиши</span><span class="sxs-lookup"><span data-stu-id="dcad1-586">Keys</span></span>

<span data-ttu-id="dcad1-587">В ключах конфигурации приняты следующие соглашения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-587">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="dcad1-588">В ключах не учитывается регистр символов.</span><span class="sxs-lookup"><span data-stu-id="dcad1-588">Keys are case-insensitive.</span></span> <span data-ttu-id="dcad1-589">Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.</span><span class="sxs-lookup"><span data-stu-id="dcad1-589">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="dcad1-590">Если значение для одного и того же ключа установлено одним и тем же или разными поставщиками конфигурации, последним значением, установленным на ключе, является используемое значение.</span><span class="sxs-lookup"><span data-stu-id="dcad1-590">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="dcad1-591">Дополнительные сведения о повторяющихся ключах JSON см. в [этой проблеме на GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="dcad1-591">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="dcad1-592">Иерархические ключи</span><span class="sxs-lookup"><span data-stu-id="dcad1-592">Hierarchical keys</span></span>
  * <span data-ttu-id="dcad1-593">При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="dcad1-593">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="dcad1-594">В переменных среды разделитель-двоеточие может не работать на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="dcad1-594">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="dcad1-595">Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие.</span><span class="sxs-lookup"><span data-stu-id="dcad1-595">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="dcad1-596">В хранилище ключей Azure иерархические ключи используют `--` (два дефиса) в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="dcad1-596">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="dcad1-597">Чтобы заменить дефисы двоеточием, при загрузке секретов в конфигурацию приложения напишите код.</span><span class="sxs-lookup"><span data-stu-id="dcad1-597">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="dcad1-598"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-598">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="dcad1-599">Привязка массива описана в разделе [Привязка массива к классу](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="dcad1-599">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="dcad1-600">Значения</span><span class="sxs-lookup"><span data-stu-id="dcad1-600">Values</span></span>

<span data-ttu-id="dcad1-601">В значениях конфигурации учитываются следующие соглашения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-601">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="dcad1-602">Значения являются строками.</span><span class="sxs-lookup"><span data-stu-id="dcad1-602">Values are strings.</span></span>
* <span data-ttu-id="dcad1-603">Значение NULL не может храниться в конфигурации или быть привязанным к объектам.</span><span class="sxs-lookup"><span data-stu-id="dcad1-603">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="dcad1-604">Поставщики</span><span class="sxs-lookup"><span data-stu-id="dcad1-604">Providers</span></span>

<span data-ttu-id="dcad1-605">В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dcad1-605">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="dcad1-606">Поставщик</span><span class="sxs-lookup"><span data-stu-id="dcad1-606">Provider</span></span> | <span data-ttu-id="dcad1-607">Предоставляет конфигурацию из &hellip;</span><span class="sxs-lookup"><span data-stu-id="dcad1-607">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="dcad1-608">[Поставщик конфигурации хранилища ключей Azure](xref:security/key-vault-configuration) (раздел *Безопасность*)</span><span class="sxs-lookup"><span data-stu-id="dcad1-608">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="dcad1-609">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="dcad1-609">Azure Key Vault</span></span> |
| <span data-ttu-id="dcad1-610">[Поставщик конфигурации приложений Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (документация Azure)</span><span class="sxs-lookup"><span data-stu-id="dcad1-610">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="dcad1-611">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="dcad1-611">Azure App Configuration</span></span> |
| [<span data-ttu-id="dcad1-612">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="dcad1-612">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="dcad1-613">Параметры командной строки</span><span class="sxs-lookup"><span data-stu-id="dcad1-613">Command-line parameters</span></span> |
| [<span data-ttu-id="dcad1-614">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="dcad1-614">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="dcad1-615">Источник пользователя</span><span class="sxs-lookup"><span data-stu-id="dcad1-615">Custom source</span></span> |
| [<span data-ttu-id="dcad1-616">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="dcad1-616">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="dcad1-617">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="dcad1-617">Environment variables</span></span> |
| [<span data-ttu-id="dcad1-618">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="dcad1-618">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="dcad1-619">Файлы (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="dcad1-619">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="dcad1-620">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="dcad1-620">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="dcad1-621">Справочные файлы</span><span class="sxs-lookup"><span data-stu-id="dcad1-621">Directory files</span></span> |
| [<span data-ttu-id="dcad1-622">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="dcad1-622">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="dcad1-623">Коллекции оперативной памяти</span><span class="sxs-lookup"><span data-stu-id="dcad1-623">In-memory collections</span></span> |
| <span data-ttu-id="dcad1-624">[Секреты пользователей](xref:security/app-secrets) (раздел *Безопасность*)</span><span class="sxs-lookup"><span data-stu-id="dcad1-624">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="dcad1-625">Файл в каталоге профиля пользователя</span><span class="sxs-lookup"><span data-stu-id="dcad1-625">File in the user profile directory</span></span> |

<span data-ttu-id="dcad1-626">Источники конфигурации считываются в том порядке, в котором при запуске указываются их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-626">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="dcad1-627">Поставщики конфигурации в этом разделе описаны в алфавитном порядке, а не в необходимом вам порядке в коде.</span><span class="sxs-lookup"><span data-stu-id="dcad1-627">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="dcad1-628">Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.</span><span class="sxs-lookup"><span data-stu-id="dcad1-628">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="dcad1-629">Типичная последовательность поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-629">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="dcad1-630">Файлы ( *appsettings.json* , *appsettings.{Environment}.json*, где `{Environment}` — это текущая среда размещения приложения)</span><span class="sxs-lookup"><span data-stu-id="dcad1-630">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. <span data-ttu-id="dcad1-631">[Azure Key Vault](xref:security/key-vault-configuration);</span><span class="sxs-lookup"><span data-stu-id="dcad1-631">[Azure Key Vault](xref:security/key-vault-configuration)</span></span>
1. <span data-ttu-id="dcad1-632">[Секреты пользователя](xref:security/app-secrets) (только в среде разработки)</span><span class="sxs-lookup"><span data-stu-id="dcad1-632">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="dcad1-633">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="dcad1-633">Environment variables</span></span>
1. <span data-ttu-id="dcad1-634">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="dcad1-634">Command-line arguments</span></span>

<span data-ttu-id="dcad1-635">Общепринятой практикой является размещение поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="dcad1-635">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="dcad1-636">Предыдущая последовательность поставщиков используется при инициализации нового построителя узла с помощью `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-636">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="dcad1-637">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="dcad1-637">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="dcad1-638">Настройка построителя узла с помощью UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="dcad1-638">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="dcad1-639">Чтобы настроить построитель узла, вызовите <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> в построителе узла с конфигурацией.</span><span class="sxs-lookup"><span data-stu-id="dcad1-639">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="dcad1-640">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="dcad1-640">ConfigureAppConfiguration</span></span>

<span data-ttu-id="dcad1-641">Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать поставщики конфигурации приложения в дополнение к тем, которые автоматически добавлены `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-641">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="dcad1-642">Переопределение предыдущей конфигурации с помощью аргументов командной строки</span><span class="sxs-lookup"><span data-stu-id="dcad1-642">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="dcad1-643">Чтобы предоставить конфигурацию приложения, которую можно переопределить с помощью аргументов командной строки, вызовите метод `AddCommandLine` последним:</span><span class="sxs-lookup"><span data-stu-id="dcad1-643">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="dcad1-644">Удаление поставщиков, добавленных CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="dcad1-644">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="dcad1-645">Чтобы удалить поставщиков, добавленных `CreateDefaultBuilder`, сначала вызовите [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) в [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources):</span><span class="sxs-lookup"><span data-stu-id="dcad1-645">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="dcad1-646">Использование конфигурации во время запуска приложения</span><span class="sxs-lookup"><span data-stu-id="dcad1-646">Consume configuration during app startup</span></span>

<span data-ttu-id="dcad1-647">Конфигурация, предоставленная приложению в `ConfigureAppConfiguration`, доступна во время запуска приложения, включая `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-647">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dcad1-648">Дополнительные сведения см. в разделе [Доступ к конфигурации во время запуска](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="dcad1-648">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="dcad1-649">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="dcad1-649">Command-line Configuration Provider</span></span>

<span data-ttu-id="dcad1-650"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пары "ключ — значение" аргумента командной строки в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-650">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="dcad1-651">Чтобы активировать конфигурацию командной строки, вызывается метод расширения <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> для экземпляра <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-651">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="dcad1-652">`AddCommandLine` вызывается автоматически при вызове `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-652">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="dcad1-653">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="dcad1-653">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="dcad1-654">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="dcad1-654">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="dcad1-655">дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="dcad1-655">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="dcad1-656">[Секреты пользователя](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="dcad1-656">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="dcad1-657">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="dcad1-657">Environment variables.</span></span>

<span data-ttu-id="dcad1-658">`CreateDefaultBuilder` добавляет последним поставщика конфигурации командной строки.</span><span class="sxs-lookup"><span data-stu-id="dcad1-658">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="dcad1-659">Аргументы командной строки передаются в набор конфигурации переопределения среды выполнения, установленной другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="dcad1-659">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="dcad1-660">`CreateDefaultBuilder` действует, когда создается узел.</span><span class="sxs-lookup"><span data-stu-id="dcad1-660">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="dcad1-661">Поэтому конфигурация командной строки, активированная с помощью `CreateDefaultBuilder`, может повлиять на настройку узла.</span><span class="sxs-lookup"><span data-stu-id="dcad1-661">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="dcad1-662">Для приложений на основе шаблонов ASP.NET Core метод `AddCommandLine` уже был вызван методом `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-662">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="dcad1-663">Чтобы добавить дополнительные поставщики конфигурации и обеспечить возможность переопределения конфигурации от этих поставщиков с помощью аргументов командной строки, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration` и вызовите `AddCommandLine` в последнюю очередь.</span><span class="sxs-lookup"><span data-stu-id="dcad1-663">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="dcad1-664">**Пример**</span><span class="sxs-lookup"><span data-stu-id="dcad1-664">**Example**</span></span>

<span data-ttu-id="dcad1-665">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-665">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="dcad1-666">Откройте командную строку в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="dcad1-666">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="dcad1-667">Поставьте аргумент командной строки в команду `dotnet run`: `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-667">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="dcad1-668">После запуска приложения откройте браузер в приложении по адресу `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-668">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="dcad1-669">Обратите внимание, что вывод содержит пару "ключ — значение" для аргумента командной строки конфигурации, предоставленного для `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-669">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="dcad1-670">Аргументы</span><span class="sxs-lookup"><span data-stu-id="dcad1-670">Arguments</span></span>

<span data-ttu-id="dcad1-671">Значение должно соответствовать знаку равенства (`=`), или ключ должен иметь префикс (`--` или `/`), когда значение следует за пробелом.</span><span class="sxs-lookup"><span data-stu-id="dcad1-671">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="dcad1-672">Значение не требуется, если используется знак равенства (например, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="dcad1-672">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="dcad1-673">Префикс ключа</span><span class="sxs-lookup"><span data-stu-id="dcad1-673">Key prefix</span></span>               | <span data-ttu-id="dcad1-674">Пример</span><span class="sxs-lookup"><span data-stu-id="dcad1-674">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="dcad1-675">Без префикса</span><span class="sxs-lookup"><span data-stu-id="dcad1-675">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="dcad1-676">Два дефиса (`--`)</span><span class="sxs-lookup"><span data-stu-id="dcad1-676">Two dashes (`--`)</span></span>        | <span data-ttu-id="dcad1-677">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="dcad1-677">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="dcad1-678">Прямая косая черта (`/`)</span><span class="sxs-lookup"><span data-stu-id="dcad1-678">Forward slash (`/`)</span></span>      | <span data-ttu-id="dcad1-679">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="dcad1-679">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="dcad1-680">В рамках одной и той же команды не смешивайте пары "ключ — значение" аргумента командной строки, которые используют знак равенства, с парами "ключ — значение", которые используют пробел.</span><span class="sxs-lookup"><span data-stu-id="dcad1-680">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="dcad1-681">Примеры команд.</span><span class="sxs-lookup"><span data-stu-id="dcad1-681">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="dcad1-682">Сопоставления переключений</span><span class="sxs-lookup"><span data-stu-id="dcad1-682">Switch mappings</span></span>

<span data-ttu-id="dcad1-683">Сопоставление параметров позволяет указать логику замены имен ключей.</span><span class="sxs-lookup"><span data-stu-id="dcad1-683">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="dcad1-684">Когда вручную создается конфигурация с помощью <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, методу <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> можно предоставить словарь сопоставления переключений.</span><span class="sxs-lookup"><span data-stu-id="dcad1-684">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="dcad1-685">В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки.</span><span class="sxs-lookup"><span data-stu-id="dcad1-685">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="dcad1-686">Если ключ в командной строке находится в словаре, значение словаря (замена ключа) передается обратно, чтобы установить пару "ключ — значение" в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-686">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="dcad1-687">Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).</span><span class="sxs-lookup"><span data-stu-id="dcad1-687">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="dcad1-688">Правила ключей из словаря сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="dcad1-688">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="dcad1-689">Переключения должны начинаться с дефиса (`-`) или двойного дефиса (`--`).</span><span class="sxs-lookup"><span data-stu-id="dcad1-689">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="dcad1-690">Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.</span><span class="sxs-lookup"><span data-stu-id="dcad1-690">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="dcad1-691">Создайте словарь сопоставления переключений.</span><span class="sxs-lookup"><span data-stu-id="dcad1-691">Create a switch mappings dictionary.</span></span> <span data-ttu-id="dcad1-692">В следующем примере создаются два сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="dcad1-692">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="dcad1-693">При сборке узла вызовите `AddCommandLine` со словарем сопоставлений переключений:</span><span class="sxs-lookup"><span data-stu-id="dcad1-693">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="dcad1-694">Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны.</span><span class="sxs-lookup"><span data-stu-id="dcad1-694">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="dcad1-695">Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные переключения, и нет возможности передать словарь сопоставления переключений в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-695">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="dcad1-696">Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.</span><span class="sxs-lookup"><span data-stu-id="dcad1-696">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="dcad1-697">Созданный словарь сопоставлений переключений содержит данные, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="dcad1-697">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="dcad1-698">Ключ</span><span class="sxs-lookup"><span data-stu-id="dcad1-698">Key</span></span>       | <span data-ttu-id="dcad1-699">Значение</span><span class="sxs-lookup"><span data-stu-id="dcad1-699">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="dcad1-700">Если ключи сопоставления переключений используются при запуске приложения, конфигурация принимает значение конфигурации в ключе, предоставленном в словаре.</span><span class="sxs-lookup"><span data-stu-id="dcad1-700">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="dcad1-701">После выполнения предыдущей команды конфигурация содержит значения, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="dcad1-701">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="dcad1-702">Ключ</span><span class="sxs-lookup"><span data-stu-id="dcad1-702">Key</span></span>               | <span data-ttu-id="dcad1-703">Значение</span><span class="sxs-lookup"><span data-stu-id="dcad1-703">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="dcad1-704">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="dcad1-704">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="dcad1-705"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пары "ключ — значение" переменной среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-705">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="dcad1-706">Чтобы активировать конфигурацию переменных среды, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-706">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="dcad1-707">[Служба приложений Azure](https://azure.microsoft.com/services/app-service/) позволяет задать переменные среды на портале Azure, который может переопределить конфигурацию приложения, используя поставщик конфигурации переменных среды.</span><span class="sxs-lookup"><span data-stu-id="dcad1-707">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="dcad1-708">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="dcad1-708">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="dcad1-709">`AddEnvironmentVariables` используется для загрузки переменных среды, имеющих префикс `ASPNETCORE_`, для [конфигурации узла](#host-versus-app-configuration) при инициализации нового построителя узла с [веб-узлом](xref:fundamentals/host/web-host) и вызове `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-709">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="dcad1-710">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="dcad1-710">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="dcad1-711">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="dcad1-711">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="dcad1-712">конфигурация приложения на основе переменных среды без префикса путем вызова `AddEnvironmentVariables` без префикса;</span><span class="sxs-lookup"><span data-stu-id="dcad1-712">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="dcad1-713">дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="dcad1-713">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="dcad1-714">[Секреты пользователя](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="dcad1-714">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="dcad1-715">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="dcad1-715">Command-line arguments.</span></span>

<span data-ttu-id="dcad1-716">Поставщик конфигурации переменных среды вызывается после выполнения настройки с помощью секретов пользователя и файлов *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-716">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="dcad1-717">Вызов поставщика в этой позиции разрешает чтение переменных среды выполнения, чтобы переопределить конфигурацию, заданную секретом пользователя и файлом *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-717">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="dcad1-718">Чтобы добавить конфигурацию приложения из дополнительных переменных среды, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration`, а затем вызовите `AddEnvironmentVariables` с префиксом:</span><span class="sxs-lookup"><span data-stu-id="dcad1-718">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="dcad1-719">Вызовите `AddEnvironmentVariables` последним, чтобы разрешить переменным среды с заданным префиксом переопределять значения от других поставщиков.</span><span class="sxs-lookup"><span data-stu-id="dcad1-719">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="dcad1-720">**Пример**</span><span class="sxs-lookup"><span data-stu-id="dcad1-720">**Example**</span></span>

<span data-ttu-id="dcad1-721">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-721">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="dcad1-722">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-722">Run the sample app.</span></span> <span data-ttu-id="dcad1-723">Откройте в приложении браузер с адресом `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-723">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="dcad1-724">Обратите внимание, что выходные данные содержат пару "ключ — значение" для переменной среды `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-724">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="dcad1-725">Значение отражает среду, в которой выполняется приложение, обычно при локальном запуске это `Development`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-725">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="dcad1-726">Чтобы список переменных среды, отображаемый приложением, был коротким, приложение фильтрует переменные среды.</span><span class="sxs-lookup"><span data-stu-id="dcad1-726">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="dcad1-727">См. пример файла *Pages/Index.cshtml.cs* приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-727">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="dcad1-728">Чтобы просмотреть все переменные среды, доступные для приложения, измените значение `FilteredConfiguration` в *Pages/Index.cshtml.cs* на следующее:</span><span class="sxs-lookup"><span data-stu-id="dcad1-728">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="dcad1-729">Префиксы</span><span class="sxs-lookup"><span data-stu-id="dcad1-729">Prefixes</span></span>

<span data-ttu-id="dcad1-730">Переменные среды, которые загружаются в конфигурацию приложения, фильтруются при добавлении префикса к методу `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-730">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="dcad1-731">Например, чтобы отфильтровать переменные среды по префиксу `CUSTOM_`, введите префикс поставщику конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-731">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="dcad1-732">Префикс отделяется при создании пары конфигурации "ключ — значение".</span><span class="sxs-lookup"><span data-stu-id="dcad1-732">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="dcad1-733">При создании построителя узла конфигурация узла предоставляется переменными среды.</span><span class="sxs-lookup"><span data-stu-id="dcad1-733">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="dcad1-734">Дополнительные сведения о префиксе, используемом для этих переменных среды, см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="dcad1-734">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="dcad1-735">**Префиксы строк подключения**</span><span class="sxs-lookup"><span data-stu-id="dcad1-735">**Connection string prefixes**</span></span>

<span data-ttu-id="dcad1-736">API конфигурации имеет специальные правила обработки для четырех строк подключения переменных среды, связанных с настройкой строк подключения Azure для среды приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-736">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="dcad1-737">Если префикс не указан в `AddEnvironmentVariables`, переменные среды с префиксами, указанными в таблице, загружаются в приложение.</span><span class="sxs-lookup"><span data-stu-id="dcad1-737">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="dcad1-738">Префикс строки подключения</span><span class="sxs-lookup"><span data-stu-id="dcad1-738">Connection string prefix</span></span> | <span data-ttu-id="dcad1-739">Поставщик</span><span class="sxs-lookup"><span data-stu-id="dcad1-739">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="dcad1-740">Поставщик пользователя</span><span class="sxs-lookup"><span data-stu-id="dcad1-740">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="dcad1-741">MySQL</span><span class="sxs-lookup"><span data-stu-id="dcad1-741">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="dcad1-742">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="dcad1-742">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="dcad1-743">SQL Server</span><span class="sxs-lookup"><span data-stu-id="dcad1-743">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="dcad1-744">Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="dcad1-744">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="dcad1-745">Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="dcad1-745">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="dcad1-746">Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).</span><span class="sxs-lookup"><span data-stu-id="dcad1-746">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="dcad1-747">Ключ переменной среды</span><span class="sxs-lookup"><span data-stu-id="dcad1-747">Environment variable key</span></span> | <span data-ttu-id="dcad1-748">Преобразованный ключ конфигурации</span><span class="sxs-lookup"><span data-stu-id="dcad1-748">Converted configuration key</span></span> | <span data-ttu-id="dcad1-749">Запись конфигурации поставщика</span><span class="sxs-lookup"><span data-stu-id="dcad1-749">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dcad1-750">Запись конфигурации не создана.</span><span class="sxs-lookup"><span data-stu-id="dcad1-750">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dcad1-751">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-751">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="dcad1-752">Значение: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="dcad1-752">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dcad1-753">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-753">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="dcad1-754">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="dcad1-754">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dcad1-755">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-755">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="dcad1-756">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="dcad1-756">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="dcad1-757">**Пример**</span><span class="sxs-lookup"><span data-stu-id="dcad1-757">**Example**</span></span>

<span data-ttu-id="dcad1-758">На сервере создается пользовательская переменная среды строки подключения:</span><span class="sxs-lookup"><span data-stu-id="dcad1-758">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="dcad1-759">Имя: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="dcad1-759">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="dcad1-760">Значение: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="dcad1-760">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="dcad1-761">Если `IConfiguration` вставляется и назначается полю с именем `_config`, считайте значение:</span><span class="sxs-lookup"><span data-stu-id="dcad1-761">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="dcad1-762">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="dcad1-762">File Configuration Provider</span></span>

<span data-ttu-id="dcad1-763"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы.</span><span class="sxs-lookup"><span data-stu-id="dcad1-763"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="dcad1-764">Следующие поставщики конфигурации предназначены для определенных типов файлов:</span><span class="sxs-lookup"><span data-stu-id="dcad1-764">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="dcad1-765">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="dcad1-765">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="dcad1-766">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="dcad1-766">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="dcad1-767">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="dcad1-767">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="dcad1-768">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="dcad1-768">INI Configuration Provider</span></span>

<span data-ttu-id="dcad1-769"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-769">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="dcad1-770">Чтобы активировать конфигурацию INI-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-770">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="dcad1-771">Двоеточие можно использовать как разделитель раздела в конфигурации файла INI.</span><span class="sxs-lookup"><span data-stu-id="dcad1-771">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="dcad1-772">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="dcad1-772">Overloads permit specifying:</span></span>

* <span data-ttu-id="dcad1-773">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="dcad1-773">Whether the file is optional.</span></span>
* <span data-ttu-id="dcad1-774">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="dcad1-774">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="dcad1-775"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="dcad1-775">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="dcad1-776">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-776">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="dcad1-777">Общий пример конфигурации INI-файла.</span><span class="sxs-lookup"><span data-stu-id="dcad1-777">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="dcad1-778">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-778">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="dcad1-779">section0:key0</span><span class="sxs-lookup"><span data-stu-id="dcad1-779">section0:key0</span></span>
* <span data-ttu-id="dcad1-780">section0:key1</span><span class="sxs-lookup"><span data-stu-id="dcad1-780">section0:key1</span></span>
* <span data-ttu-id="dcad1-781">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="dcad1-781">section1:subsection:key</span></span>
* <span data-ttu-id="dcad1-782">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="dcad1-782">section2:subsection0:key</span></span>
* <span data-ttu-id="dcad1-783">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="dcad1-783">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="dcad1-784">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="dcad1-784">JSON Configuration Provider</span></span>

<span data-ttu-id="dcad1-785"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ — значение" JSON-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-785">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="dcad1-786">Чтобы активировать конфигурацию JSON-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-786">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="dcad1-787">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="dcad1-787">Overloads permit specifying:</span></span>

* <span data-ttu-id="dcad1-788">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="dcad1-788">Whether the file is optional.</span></span>
* <span data-ttu-id="dcad1-789">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="dcad1-789">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="dcad1-790"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="dcad1-790">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="dcad1-791">`AddJsonFile` автоматически вызывается дважды при инициализации нового построителя узла с `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-791">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="dcad1-792">Метод вызывается для загрузки конфигурации из:</span><span class="sxs-lookup"><span data-stu-id="dcad1-792">The method is called to load configuration from:</span></span>

* <span data-ttu-id="dcad1-793">*appsettings.json* : Этот файл считывается первым.</span><span class="sxs-lookup"><span data-stu-id="dcad1-793">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="dcad1-794">Версия файла среды может переопределить значения, предоставленные файлом *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="dcad1-794">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="dcad1-795">*appsettings.{Environment}.json*: Версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="dcad1-795">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="dcad1-796">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="dcad1-796">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="dcad1-797">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="dcad1-797">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="dcad1-798">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="dcad1-798">Environment variables.</span></span>
* <span data-ttu-id="dcad1-799">[Секреты пользователя](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="dcad1-799">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="dcad1-800">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="dcad1-800">Command-line arguments.</span></span>

<span data-ttu-id="dcad1-801">Сначала устанавливается поставщик конфигурации JSON-файлов.</span><span class="sxs-lookup"><span data-stu-id="dcad1-801">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="dcad1-802">Таким образом, секреты пользователя, переменные среды и аргументы командной строки переопределят конфигурацию, заданную файлами *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-802">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="dcad1-803">Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать конфигурацию приложения для файлов, отличных от *appsettings.json* и *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-803">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="dcad1-804">**Пример**</span><span class="sxs-lookup"><span data-stu-id="dcad1-804">**Example**</span></span>

<span data-ttu-id="dcad1-805">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает два вызова `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-805">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="dcad1-806">Первый вызов `AddJsonFile` загружает конфигурацию из файла *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="dcad1-806">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="dcad1-807">Второй вызов `AddJsonFile` загружает конфигурацию из *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-807">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="dcad1-808">Для *appsettings.Development.json* в примере приложения загружается следующий файл:</span><span class="sxs-lookup"><span data-stu-id="dcad1-808">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="dcad1-809">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-809">Run the sample app.</span></span> <span data-ttu-id="dcad1-810">Откройте в приложении браузер с адресом `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-810">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="dcad1-811">Выходные данные содержат пары "ключ-значение" для конфигурации в зависимости от среды приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-811">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="dcad1-812">Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Debug` при запуске приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="dcad1-812">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="dcad1-813">Запустите пример приложения еще раз в рабочей среде:</span><span class="sxs-lookup"><span data-stu-id="dcad1-813">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="dcad1-814">Откройте файл *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-814">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="dcad1-815">В профиле `ConfigurationSample` измените значение переменной среды `ASPNETCORE_ENVIRONMENT` на `Production`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-815">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="dcad1-816">Сохраните файл и запустите приложение с помощью `dotnet run` в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="dcad1-816">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="dcad1-817">Параметры в *appsettings.Development.json* больше не переопределяют параметры в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="dcad1-817">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="dcad1-818">Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Warning`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-818">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="dcad1-819">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="dcad1-819">XML Configuration Provider</span></span>

<span data-ttu-id="dcad1-820"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-820">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="dcad1-821">Чтобы активировать конфигурацию XML-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-821">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="dcad1-822">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="dcad1-822">Overloads permit specifying:</span></span>

* <span data-ttu-id="dcad1-823">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="dcad1-823">Whether the file is optional.</span></span>
* <span data-ttu-id="dcad1-824">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="dcad1-824">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="dcad1-825"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="dcad1-825">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="dcad1-826">Корневой узел файла конфигурации не учитывается при создании пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-826">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="dcad1-827">Не указывайте в файле Document Type Definition (определение типа документа, DTD) или пространство имен.</span><span class="sxs-lookup"><span data-stu-id="dcad1-827">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="dcad1-828">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-828">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="dcad1-829">XML-файлы конфигурации могут использовать имена отдельных элементов для повторяющихся разделов.</span><span class="sxs-lookup"><span data-stu-id="dcad1-829">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="dcad1-830">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-830">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="dcad1-831">section0:key0</span><span class="sxs-lookup"><span data-stu-id="dcad1-831">section0:key0</span></span>
* <span data-ttu-id="dcad1-832">section0:key1</span><span class="sxs-lookup"><span data-stu-id="dcad1-832">section0:key1</span></span>
* <span data-ttu-id="dcad1-833">section1:key0</span><span class="sxs-lookup"><span data-stu-id="dcad1-833">section1:key0</span></span>
* <span data-ttu-id="dcad1-834">section1:key1</span><span class="sxs-lookup"><span data-stu-id="dcad1-834">section1:key1</span></span>

<span data-ttu-id="dcad1-835">Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.</span><span class="sxs-lookup"><span data-stu-id="dcad1-835">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="dcad1-836">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-836">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="dcad1-837">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="dcad1-837">section:section0:key:key0</span></span>
* <span data-ttu-id="dcad1-838">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="dcad1-838">section:section0:key:key1</span></span>
* <span data-ttu-id="dcad1-839">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="dcad1-839">section:section1:key:key0</span></span>
* <span data-ttu-id="dcad1-840">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="dcad1-840">section:section1:key:key1</span></span>

<span data-ttu-id="dcad1-841">Атрибуты можно использовать для предоставления значений.</span><span class="sxs-lookup"><span data-stu-id="dcad1-841">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="dcad1-842">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-842">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="dcad1-843">key:attribute</span><span class="sxs-lookup"><span data-stu-id="dcad1-843">key:attribute</span></span>
* <span data-ttu-id="dcad1-844">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="dcad1-844">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="dcad1-845">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="dcad1-845">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="dcad1-846"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-846">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="dcad1-847">Ключ является именем файла.</span><span class="sxs-lookup"><span data-stu-id="dcad1-847">The key is the file name.</span></span> <span data-ttu-id="dcad1-848">Значение содержит содержимое файла.</span><span class="sxs-lookup"><span data-stu-id="dcad1-848">The value contains the file's contents.</span></span> <span data-ttu-id="dcad1-849">Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.</span><span class="sxs-lookup"><span data-stu-id="dcad1-849">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="dcad1-850">Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-850">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="dcad1-851">Значение параметра `directoryPath` должно быть абсолютным путем к файлам.</span><span class="sxs-lookup"><span data-stu-id="dcad1-851">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="dcad1-852">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="dcad1-852">Overloads permit specifying:</span></span>

* <span data-ttu-id="dcad1-853">`Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.</span><span class="sxs-lookup"><span data-stu-id="dcad1-853">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="dcad1-854">Обязательно ли указывать каталог и путь к каталогу.</span><span class="sxs-lookup"><span data-stu-id="dcad1-854">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="dcad1-855">Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов.</span><span class="sxs-lookup"><span data-stu-id="dcad1-855">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="dcad1-856">Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-856">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="dcad1-857">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-857">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="dcad1-858">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="dcad1-858">Memory Configuration Provider</span></span>

<span data-ttu-id="dcad1-859"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-859">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="dcad1-860">Чтобы активировать конфигурацию коллекции в памяти, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-860">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="dcad1-861">Поставщик конфигурации может инициализироваться значением `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-861">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="dcad1-862">Чтобы указать конфигурацию приложения, при сборке узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-862">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="dcad1-863">В следующем примере создается словарь конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-863">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="dcad1-864">Словарь используется с вызовом `AddInMemoryCollection` для предоставления конфигурации:</span><span class="sxs-lookup"><span data-stu-id="dcad1-864">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="dcad1-865">GetValue</span><span class="sxs-lookup"><span data-stu-id="dcad1-865">GetValue</span></span>

<span data-ttu-id="dcad1-866">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип, не являющийся коллекцией.</span><span class="sxs-lookup"><span data-stu-id="dcad1-866">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="dcad1-867">Перегрузка принимает значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="dcad1-867">An overload accepts a default value.</span></span>

<span data-ttu-id="dcad1-868">В следующем примере происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="dcad1-868">The following example:</span></span>

* <span data-ttu-id="dcad1-869">Из конфигурации извлекается строковое значение с ключом `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-869">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="dcad1-870">Если `NumberKey` отсутствует в ключах конфигурации, используется значение по умолчанию `99`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-870">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="dcad1-871">Значение получает тип `int`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-871">Types the value as an `int`.</span></span>
* <span data-ttu-id="dcad1-872">Значение сохраняется в свойстве `NumberConfig` для использования на странице.</span><span class="sxs-lookup"><span data-stu-id="dcad1-872">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="dcad1-873">GetSection, GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="dcad1-873">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="dcad1-874">В следующих примерах рассмотрим файл JSON.</span><span class="sxs-lookup"><span data-stu-id="dcad1-874">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="dcad1-875">Четыре ключа находятся в двух разделах, один из которых содержит пару из подразделов.</span><span class="sxs-lookup"><span data-stu-id="dcad1-875">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="dcad1-876">Когда файл считывается в конфигурацию, для сохранения значений конфигурации создаются следующие уникальные иерархические ключи.</span><span class="sxs-lookup"><span data-stu-id="dcad1-876">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="dcad1-877">section0:key0</span><span class="sxs-lookup"><span data-stu-id="dcad1-877">section0:key0</span></span>
* <span data-ttu-id="dcad1-878">section0:key1</span><span class="sxs-lookup"><span data-stu-id="dcad1-878">section0:key1</span></span>
* <span data-ttu-id="dcad1-879">section1:key0</span><span class="sxs-lookup"><span data-stu-id="dcad1-879">section1:key0</span></span>
* <span data-ttu-id="dcad1-880">section1:key1</span><span class="sxs-lookup"><span data-stu-id="dcad1-880">section1:key1</span></span>
* <span data-ttu-id="dcad1-881">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="dcad1-881">section2:subsection0:key0</span></span>
* <span data-ttu-id="dcad1-882">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="dcad1-882">section2:subsection0:key1</span></span>
* <span data-ttu-id="dcad1-883">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="dcad1-883">section2:subsection1:key0</span></span>
* <span data-ttu-id="dcad1-884">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="dcad1-884">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="dcad1-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="dcad1-885">GetSection</span></span>

<span data-ttu-id="dcad1-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) извлекает подраздел конфигурации с указанным ключом подраздела.</span><span class="sxs-lookup"><span data-stu-id="dcad1-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="dcad1-887">Чтобы вернуть <xref:Microsoft.Extensions.Configuration.IConfigurationSection>, содержащий только пары "ключ — значение" в `section1`, вызовите `GetSection` и укажите имя раздела.</span><span class="sxs-lookup"><span data-stu-id="dcad1-887">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="dcad1-888">`configSection` не содержит значение, только ключ и путь.</span><span class="sxs-lookup"><span data-stu-id="dcad1-888">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="dcad1-889">Аналогично, чтобы получить значения для ключей в `section2:subsection0`, вызовите `GetSection` и укажите путь к разделу.</span><span class="sxs-lookup"><span data-stu-id="dcad1-889">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="dcad1-890">Значение `GetSection` никогда не возвращает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-890">`GetSection` never returns `null`.</span></span> <span data-ttu-id="dcad1-891">Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-891">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="dcad1-892">Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется.</span><span class="sxs-lookup"><span data-stu-id="dcad1-892">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="dcad1-893"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.</span><span class="sxs-lookup"><span data-stu-id="dcad1-893">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="dcad1-894">GetChildren</span><span class="sxs-lookup"><span data-stu-id="dcad1-894">GetChildren</span></span>

<span data-ttu-id="dcad1-895">Вызов [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) в `section2` получает значение `IEnumerable<IConfigurationSection>`, которое включает:</span><span class="sxs-lookup"><span data-stu-id="dcad1-895">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="dcad1-896">Exists</span><span class="sxs-lookup"><span data-stu-id="dcad1-896">Exists</span></span>

<span data-ttu-id="dcad1-897">Используйте [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*), чтобы определить, существует ли раздел конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-897">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="dcad1-898">Учитывая данные примера, `sectionExists` является `false`, потому что в данных конфигурации нет `section2:subsection2`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-898">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="dcad1-899">Привязка к графу объектов</span><span class="sxs-lookup"><span data-stu-id="dcad1-899">Bind to an object graph</span></span>

<span data-ttu-id="dcad1-900"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> способна связывать весь граф объекта POCO.</span><span class="sxs-lookup"><span data-stu-id="dcad1-900"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="dcad1-901">Как и при привязке простого объекта, привязываются только открытые свойства чтения и записи.</span><span class="sxs-lookup"><span data-stu-id="dcad1-901">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="dcad1-902">Образец содержит модель `TvShow`, в графе объектов которого находятся классы `Metadata` и `Actors` (*Модели/TvShow.cs*).</span><span class="sxs-lookup"><span data-stu-id="dcad1-902">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="dcad1-903">В примере приложения есть файл *tvshow.xml*, содержащий данные конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-903">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="dcad1-904">Конфигурация привязана ко всему ​​графу объектов `TvShow` с помощью метода `Bind`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-904">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="dcad1-905">Привязанный экземпляр присваивается свойству для подготовки к просмотру.</span><span class="sxs-lookup"><span data-stu-id="dcad1-905">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="dcad1-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) привязывает и возвращает указанный тип.</span><span class="sxs-lookup"><span data-stu-id="dcad1-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="dcad1-907">Метод `Get<T>` может быть более удобным, чем использование `Bind`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-907">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="dcad1-908">Следующий код показывает, как использовать `Get<T>` с предыдущим примером:</span><span class="sxs-lookup"><span data-stu-id="dcad1-908">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="dcad1-909">Привязка массива к классу</span><span class="sxs-lookup"><span data-stu-id="dcad1-909">Bind an array to a class</span></span>

<span data-ttu-id="dcad1-910">*Пример приложения демонстрирует концепции, описанные в этом разделе.*</span><span class="sxs-lookup"><span data-stu-id="dcad1-910">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="dcad1-911"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-911">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="dcad1-912">Любой формат массива, который предоставляет сегмент числового ключа (`:0:`, `:1:`, &hellip; `:{n}:`), способен привязать массив к массиву класса POCO.</span><span class="sxs-lookup"><span data-stu-id="dcad1-912">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="dcad1-913">Привязка предоставляется соглашением.</span><span class="sxs-lookup"><span data-stu-id="dcad1-913">Binding is provided by convention.</span></span> <span data-ttu-id="dcad1-914">Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.</span><span class="sxs-lookup"><span data-stu-id="dcad1-914">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="dcad1-915">**Обработка массива в оперативной памяти**</span><span class="sxs-lookup"><span data-stu-id="dcad1-915">**In-memory array processing**</span></span>

<span data-ttu-id="dcad1-916">Рассмотрите ключи и значения конфигурации, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="dcad1-916">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="dcad1-917">Ключ</span><span class="sxs-lookup"><span data-stu-id="dcad1-917">Key</span></span>             | <span data-ttu-id="dcad1-918">Значение</span><span class="sxs-lookup"><span data-stu-id="dcad1-918">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="dcad1-919">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="dcad1-919">array:entries:0</span></span> | <span data-ttu-id="dcad1-920">value0</span><span class="sxs-lookup"><span data-stu-id="dcad1-920">value0</span></span> |
| <span data-ttu-id="dcad1-921">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="dcad1-921">array:entries:1</span></span> | <span data-ttu-id="dcad1-922">value1</span><span class="sxs-lookup"><span data-stu-id="dcad1-922">value1</span></span> |
| <span data-ttu-id="dcad1-923">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="dcad1-923">array:entries:2</span></span> | <span data-ttu-id="dcad1-924">value2</span><span class="sxs-lookup"><span data-stu-id="dcad1-924">value2</span></span> |
| <span data-ttu-id="dcad1-925">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="dcad1-925">array:entries:4</span></span> | <span data-ttu-id="dcad1-926">value4</span><span class="sxs-lookup"><span data-stu-id="dcad1-926">value4</span></span> |
| <span data-ttu-id="dcad1-927">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="dcad1-927">array:entries:5</span></span> | <span data-ttu-id="dcad1-928">value5</span><span class="sxs-lookup"><span data-stu-id="dcad1-928">value5</span></span> |

<span data-ttu-id="dcad1-929">Эти ключи и значения загружаются в пример приложения с помощью поставщика конфигурации памяти.</span><span class="sxs-lookup"><span data-stu-id="dcad1-929">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="dcad1-930">Массив пропускает значения для индекса &num;3.</span><span class="sxs-lookup"><span data-stu-id="dcad1-930">The array skips a value for index &num;3.</span></span> <span data-ttu-id="dcad1-931">Связующее свойство конфигурации не способно связывать нулевые значения или создавать нулевые записи в связанных объектах, что становится ясным в тот момент, когда демонстрируется результат привязки этого массива к объекту.</span><span class="sxs-lookup"><span data-stu-id="dcad1-931">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="dcad1-932">В примере приложения класс POCO доступен для хранения привязанных данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-932">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="dcad1-933">Данные конфигурации, связанные с объектом.</span><span class="sxs-lookup"><span data-stu-id="dcad1-933">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="dcad1-934">Синтаксис [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) также может использоваться для получения более компактного кода:</span><span class="sxs-lookup"><span data-stu-id="dcad1-934">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="dcad1-935">Связанный объект, экземпляр `ArrayExample`, получает данные массива из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-935">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="dcad1-936">Индекс `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="dcad1-936">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="dcad1-937">Значение `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="dcad1-937">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="dcad1-938">0</span><span class="sxs-lookup"><span data-stu-id="dcad1-938">0</span></span>                            | <span data-ttu-id="dcad1-939">value0</span><span class="sxs-lookup"><span data-stu-id="dcad1-939">value0</span></span>                       |
| <span data-ttu-id="dcad1-940">1</span><span class="sxs-lookup"><span data-stu-id="dcad1-940">1</span></span>                            | <span data-ttu-id="dcad1-941">value1</span><span class="sxs-lookup"><span data-stu-id="dcad1-941">value1</span></span>                       |
| <span data-ttu-id="dcad1-942">2</span><span class="sxs-lookup"><span data-stu-id="dcad1-942">2</span></span>                            | <span data-ttu-id="dcad1-943">value2</span><span class="sxs-lookup"><span data-stu-id="dcad1-943">value2</span></span>                       |
| <span data-ttu-id="dcad1-944">3</span><span class="sxs-lookup"><span data-stu-id="dcad1-944">3</span></span>                            | <span data-ttu-id="dcad1-945">value4</span><span class="sxs-lookup"><span data-stu-id="dcad1-945">value4</span></span>                       |
| <span data-ttu-id="dcad1-946">4</span><span class="sxs-lookup"><span data-stu-id="dcad1-946">4</span></span>                            | <span data-ttu-id="dcad1-947">value5</span><span class="sxs-lookup"><span data-stu-id="dcad1-947">value5</span></span>                       |

<span data-ttu-id="dcad1-948">Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-948">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="dcad1-949">При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы просто используются для выполнения итерации данных конфигурации при создании объекта.</span><span class="sxs-lookup"><span data-stu-id="dcad1-949">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="dcad1-950">Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.</span><span class="sxs-lookup"><span data-stu-id="dcad1-950">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="dcad1-951">Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который создает правильную пару "ключ — значение" в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-951">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="dcad1-952">Если в образец включен дополнительный поставщик конфигурации JSON с отсутствующей парой "ключ — значение", то `ArrayExample.Entries` подойдет полному массиву конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-952">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="dcad1-953">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-953">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="dcad1-954">В `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="dcad1-954">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="dcad1-955">Пары "ключ — значение", показанные в таблице, загружаются в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-955">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="dcad1-956">Ключ</span><span class="sxs-lookup"><span data-stu-id="dcad1-956">Key</span></span>             | <span data-ttu-id="dcad1-957">Значение</span><span class="sxs-lookup"><span data-stu-id="dcad1-957">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="dcad1-958">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="dcad1-958">array:entries:3</span></span> | <span data-ttu-id="dcad1-959">value3</span><span class="sxs-lookup"><span data-stu-id="dcad1-959">value3</span></span> |

<span data-ttu-id="dcad1-960">Если экземпляр класса `ArrayExample` связан после того, как поставщик конфигурации JSON включает запись для индекса &num;3, то массив `ArrayExample.Entries` включит значение.</span><span class="sxs-lookup"><span data-stu-id="dcad1-960">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="dcad1-961">Индекс `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="dcad1-961">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="dcad1-962">Значение `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="dcad1-962">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="dcad1-963">0</span><span class="sxs-lookup"><span data-stu-id="dcad1-963">0</span></span>                            | <span data-ttu-id="dcad1-964">value0</span><span class="sxs-lookup"><span data-stu-id="dcad1-964">value0</span></span>                       |
| <span data-ttu-id="dcad1-965">1</span><span class="sxs-lookup"><span data-stu-id="dcad1-965">1</span></span>                            | <span data-ttu-id="dcad1-966">value1</span><span class="sxs-lookup"><span data-stu-id="dcad1-966">value1</span></span>                       |
| <span data-ttu-id="dcad1-967">2</span><span class="sxs-lookup"><span data-stu-id="dcad1-967">2</span></span>                            | <span data-ttu-id="dcad1-968">value2</span><span class="sxs-lookup"><span data-stu-id="dcad1-968">value2</span></span>                       |
| <span data-ttu-id="dcad1-969">3</span><span class="sxs-lookup"><span data-stu-id="dcad1-969">3</span></span>                            | <span data-ttu-id="dcad1-970">value3</span><span class="sxs-lookup"><span data-stu-id="dcad1-970">value3</span></span>                       |
| <span data-ttu-id="dcad1-971">4</span><span class="sxs-lookup"><span data-stu-id="dcad1-971">4</span></span>                            | <span data-ttu-id="dcad1-972">value4</span><span class="sxs-lookup"><span data-stu-id="dcad1-972">value4</span></span>                       |
| <span data-ttu-id="dcad1-973">5</span><span class="sxs-lookup"><span data-stu-id="dcad1-973">5</span></span>                            | <span data-ttu-id="dcad1-974">value5</span><span class="sxs-lookup"><span data-stu-id="dcad1-974">value5</span></span>                       |

<span data-ttu-id="dcad1-975">**Обработка массива JSON**</span><span class="sxs-lookup"><span data-stu-id="dcad1-975">**JSON array processing**</span></span>

<span data-ttu-id="dcad1-976">Если JSON-файл содержит массив, ключи конфигурации будут созданы для элементов массива с индексом раздела, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="dcad1-976">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="dcad1-977">В следующем файле конфигурации `subsection` — это массив.</span><span class="sxs-lookup"><span data-stu-id="dcad1-977">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="dcad1-978">Поставщик конфигурации JSON считывает данные конфигурации в следующие пары "ключ — значение".</span><span class="sxs-lookup"><span data-stu-id="dcad1-978">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="dcad1-979">Ключ</span><span class="sxs-lookup"><span data-stu-id="dcad1-979">Key</span></span>                     | <span data-ttu-id="dcad1-980">Значение</span><span class="sxs-lookup"><span data-stu-id="dcad1-980">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="dcad1-981">json_array:key</span><span class="sxs-lookup"><span data-stu-id="dcad1-981">json_array:key</span></span>          | <span data-ttu-id="dcad1-982">valueA</span><span class="sxs-lookup"><span data-stu-id="dcad1-982">valueA</span></span> |
| <span data-ttu-id="dcad1-983">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="dcad1-983">json_array:subsection:0</span></span> | <span data-ttu-id="dcad1-984">valueB</span><span class="sxs-lookup"><span data-stu-id="dcad1-984">valueB</span></span> |
| <span data-ttu-id="dcad1-985">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="dcad1-985">json_array:subsection:1</span></span> | <span data-ttu-id="dcad1-986">valueC</span><span class="sxs-lookup"><span data-stu-id="dcad1-986">valueC</span></span> |
| <span data-ttu-id="dcad1-987">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="dcad1-987">json_array:subsection:2</span></span> | <span data-ttu-id="dcad1-988">valueD</span><span class="sxs-lookup"><span data-stu-id="dcad1-988">valueD</span></span> |

<span data-ttu-id="dcad1-989">В примере приложения для привязки пар "ключ — значение" конфигурации доступен следующий класс POCO.</span><span class="sxs-lookup"><span data-stu-id="dcad1-989">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="dcad1-990">После выполнения привязки `JsonArrayExample.Key` содержит значение `valueA`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-990">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="dcad1-991">Подраздел значения хранится в свойстве массива POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-991">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="dcad1-992">Индекс `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="dcad1-992">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="dcad1-993">Значение `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="dcad1-993">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="dcad1-994">0</span><span class="sxs-lookup"><span data-stu-id="dcad1-994">0</span></span>                                   | <span data-ttu-id="dcad1-995">valueB</span><span class="sxs-lookup"><span data-stu-id="dcad1-995">valueB</span></span>                              |
| <span data-ttu-id="dcad1-996">1</span><span class="sxs-lookup"><span data-stu-id="dcad1-996">1</span></span>                                   | <span data-ttu-id="dcad1-997">valueC</span><span class="sxs-lookup"><span data-stu-id="dcad1-997">valueC</span></span>                              |
| <span data-ttu-id="dcad1-998">2</span><span class="sxs-lookup"><span data-stu-id="dcad1-998">2</span></span>                                   | <span data-ttu-id="dcad1-999">valueD</span><span class="sxs-lookup"><span data-stu-id="dcad1-999">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="dcad1-1000">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="dcad1-1000">Custom configuration provider</span></span>

<span data-ttu-id="dcad1-1001">Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="dcad1-1001">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="dcad1-1002">Поставщик имеет следующие характеристики.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1002">The provider has the following characteristics:</span></span>

* <span data-ttu-id="dcad1-1003">База данных в памяти EF используется для демонстрационных целей.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1003">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="dcad1-1004">Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1004">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="dcad1-1005">Поставщик считывает таблицу базы данных в конфигурации при запуске.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1005">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="dcad1-1006">Поставщик не запрашивает базу данных для каждого ключа.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1006">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="dcad1-1007">Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1007">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="dcad1-1008">Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1008">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="dcad1-1009">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-1009">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="dcad1-1010">Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1010">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="dcad1-1011">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-1011">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="dcad1-1012">Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1012">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="dcad1-1013">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-1013">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="dcad1-1014">Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1014">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="dcad1-1015">Поставщик конфигурации инициализирует пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1015">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="dcad1-1016">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-1016">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="dcad1-1017">Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1017">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="dcad1-1018">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="dcad1-1018">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="dcad1-1019">В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1019">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="dcad1-1020">Доступ к конфигурации во время запуска</span><span class="sxs-lookup"><span data-stu-id="dcad1-1020">Access configuration during startup</span></span>

<span data-ttu-id="dcad1-1021">Внесите значение `IConfiguration` в конструктор `Startup` для доступа к значениям конфигурации в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1021">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dcad1-1022">Чтобы получить доступ к конфигурации в `Startup.Configure`, либо добавьте значение `IConfiguration` непосредственно в метод, либо используйте экземпляр из конструктора.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1022">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="dcad1-1023">Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="dcad1-1023">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="dcad1-1024">Настройте доступ на странице Razor Pages или в представлении MVC</span><span class="sxs-lookup"><span data-stu-id="dcad1-1024">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="dcad1-1025">Для доступа к параметрам конфигурации на странице Razor Pages или в представлении MVC добавьте [использование директивы](xref:mvc/views/razor#using) ([Справочник по C#: использование директивы](/dotnet/csharp/language-reference/keywords/using-directive)) для [пространства имен Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) и вставьте <xref:Microsoft.Extensions.Configuration.IConfiguration> на страницу или в представление.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1025">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="dcad1-1026">На странице Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="dcad1-1026">In a Razor Pages page:</span></span>

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

<span data-ttu-id="dcad1-1027">В представлении MVC</span><span class="sxs-lookup"><span data-stu-id="dcad1-1027">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="dcad1-1028">Добавление конфигурации из внешней сборки</span><span class="sxs-lookup"><span data-stu-id="dcad1-1028">Add configuration from an external assembly</span></span>

<span data-ttu-id="dcad1-1029">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1029">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="dcad1-1030">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="dcad1-1030">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dcad1-1031">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="dcad1-1031">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
