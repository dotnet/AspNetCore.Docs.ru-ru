---
title: Поставщики хранилища ключей в ASP.NET Core
author: rick-anderson
description: Узнайте о поставщиках хранилища ключей в ASP.NET Core и о том, как настроить места хранения ключей.
ms.author: riande
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
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: e4cf10d09c1629afb298aef0c2b86ad3bf7b646c
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057373"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="24b61-103">Поставщики хранилища ключей в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24b61-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="24b61-104">Система защиты данных [по умолчанию использует механизм обнаружения,](xref:security/data-protection/configuration/default-settings) чтобы определить, где должны храниться криптографические ключи.</span><span class="sxs-lookup"><span data-stu-id="24b61-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="24b61-105">Разработчик может переопределить механизм обнаружения по умолчанию и указать расположение вручную.</span><span class="sxs-lookup"><span data-stu-id="24b61-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="24b61-106">Если указать явное расположение сохраняемости ключа, система защиты данных отменяет регистрацию шифрования ключа по умолчанию в механизме RESTful, поэтому ключи больше не шифруются.</span><span class="sxs-lookup"><span data-stu-id="24b61-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="24b61-107">Рекомендуется дополнительно [указать явный механизм шифрования ключей](xref:security/data-protection/implementation/key-encryption-at-rest) для развертывания в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="24b61-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="24b61-108">Файловая система</span><span class="sxs-lookup"><span data-stu-id="24b61-108">File system</span></span>

<span data-ttu-id="24b61-109">Чтобы настроить репозиторий ключей на основе файловой системы, вызовите подпрограммы настройки [персисткэйстофилесистем](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) , как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="24b61-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="24b61-110">Укажите [DirectoryInfo](/dotnet/api/system.io.directoryinfo) , указывающий на репозиторий, в котором должны храниться ключи:</span><span class="sxs-lookup"><span data-stu-id="24b61-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="24b61-111">`DirectoryInfo`Может указывать на каталог на локальном компьютере или на папку в общей сетевой папке.</span><span class="sxs-lookup"><span data-stu-id="24b61-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="24b61-112">Если указывает на каталог на локальном компьютере (и сценарий заключается в том, что доступ к этому репозиторию требуется получить только для приложений на локальном компьютере), рассмотрите возможность использования [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (в Windows) для шифрования неактивных ключей.</span><span class="sxs-lookup"><span data-stu-id="24b61-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="24b61-113">В противном случае рассмотрите возможность использования [сертификата X. 509](xref:security/data-protection/implementation/key-encryption-at-rest) для шифрования неактивных ключей.</span><span class="sxs-lookup"><span data-stu-id="24b61-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="24b61-114">Хранилище Azure</span><span class="sxs-lookup"><span data-stu-id="24b61-114">Azure Storage</span></span>

<span data-ttu-id="24b61-115">Пакет [Azure. Extensions. AspNetCore. Data Protection. blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) позволяет хранить ключи защиты данных в хранилище BLOB-объектов Azure.</span><span class="sxs-lookup"><span data-stu-id="24b61-115">The [Azure.Extensions.AspNetCore.DataProtection.Blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="24b61-116">Ключи можно совместно использовать в нескольких экземплярах веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="24b61-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="24b61-117">Приложения могут совместно использовать проверку подлинности cookie или защиту CSRF на нескольких серверах.</span><span class="sxs-lookup"><span data-stu-id="24b61-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="24b61-118">Чтобы настроить поставщик хранилища BLOB-объектов Azure, вызовите одну из перегрузок [персисткэйстоазуреблобстораже](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) .</span><span class="sxs-lookup"><span data-stu-id="24b61-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="24b61-119">Если веб-приложение работает как служба Azure, строку подключения можно использовать для проверки подлинности в службе хранилища Azure с помощью [Azure. Storage. blobs](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient).</span><span class="sxs-lookup"><span data-stu-id="24b61-119">If the web app is running as an Azure service, connection string can be used to authenticate to Azure storage by using [Azure.Storage.Blobs](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient).</span></span>

```csharp
string connectionString = "<connection_string>";
string containerName = "my-key-container";
BlobContainerClient container = new BlobContainerClient(connectionString, containerName);

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

> [!NOTE]
> <span data-ttu-id="24b61-120">Строку подключения к учетной записи хранения можно найти на портале Azure в разделе "ключи доступа" или с помощью следующей команды CLI:</span><span class="sxs-lookup"><span data-stu-id="24b61-120">The connection string to your storage account can be found in the Azure Portal under the "Access Keys" section or by running the following CLI command:</span></span> 
> ```bash
> az storage account show-connection-string --name <account_name> --resource-group <resource_group>
> ```

## <a name="redis"></a><span data-ttu-id="24b61-121">Redis</span><span class="sxs-lookup"><span data-stu-id="24b61-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="24b61-122">Пакет [Microsoft. AspNetCore. Data Protection. стаккексчанжередис](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) позволяет хранить ключи защиты данных в кэше Redis.</span><span class="sxs-lookup"><span data-stu-id="24b61-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="24b61-123">Ключи можно совместно использовать в нескольких экземплярах веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="24b61-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="24b61-124">Приложения могут совместно использовать проверку подлинности cookie или защиту CSRF на нескольких серверах.</span><span class="sxs-lookup"><span data-stu-id="24b61-124">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="24b61-125">Пакет [Microsoft. AspNetCore. Data Protection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) позволяет хранить ключи защиты данных в кэше Redis.</span><span class="sxs-lookup"><span data-stu-id="24b61-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="24b61-126">Ключи можно совместно использовать в нескольких экземплярах веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="24b61-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="24b61-127">Приложения могут совместно использовать проверку подлинности cookie или защиту CSRF на нескольких серверах.</span><span class="sxs-lookup"><span data-stu-id="24b61-127">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="24b61-128">Чтобы настроить в Redis, вызовите одну из перегрузок [персисткэйстостаккексчанжередис](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) :</span><span class="sxs-lookup"><span data-stu-id="24b61-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="24b61-129">Чтобы настроить в Redis, вызовите одну из перегрузок [персисткэйсторедис](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) :</span><span class="sxs-lookup"><span data-stu-id="24b61-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="24b61-130">Дополнительные сведения см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="24b61-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="24b61-131">StackExchange. Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="24b61-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="24b61-132">кэш Azure Redis</span><span class="sxs-lookup"><span data-stu-id="24b61-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="24b61-133">ASP.NET Core примеры защиты</span><span class="sxs-lookup"><span data-stu-id="24b61-133">ASP.NET Core DataProtection samples</span></span>](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="24b61-134">Реестр</span><span class="sxs-lookup"><span data-stu-id="24b61-134">Registry</span></span>

<span data-ttu-id="24b61-135">**Применяется только к развертываниям Windows.**</span><span class="sxs-lookup"><span data-stu-id="24b61-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="24b61-136">Иногда приложение может не иметь доступа на запись в файловую систему.</span><span class="sxs-lookup"><span data-stu-id="24b61-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="24b61-137">Рассмотрим ситуацию, когда приложение работает как учетная запись виртуальной службы (например, удостоверение пула приложений *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="24b61-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="24b61-138">В таких случаях администратор может предоставить раздел реестра, доступный для удостоверения учетной записи службы.</span><span class="sxs-lookup"><span data-stu-id="24b61-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="24b61-139">Вызовите метод расширения [персисткэйсторегистри](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) , как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="24b61-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="24b61-140">Укажите [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) , указывающий на расположение, в котором должны храниться криптографические ключи:</span><span class="sxs-lookup"><span data-stu-id="24b61-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys", true));
}
```

> [!IMPORTANT]
> <span data-ttu-id="24b61-141">Мы рекомендуем использовать [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) для шифрования неактивных ключей.</span><span class="sxs-lookup"><span data-stu-id="24b61-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="24b61-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="24b61-142">Entity Framework Core</span></span>

<span data-ttu-id="24b61-143">Пакет [Microsoft. AspNetCore. Data Protection. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) предоставляет механизм для хранения ключей защиты данных в базе данных с помощью Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="24b61-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="24b61-144">`Microsoft.AspNetCore.DataProtection.EntityFrameworkCore`Пакет NuGet необходимо добавить в файл проекта, но он не является частью файла [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="24b61-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="24b61-145">Этот пакет позволяет совместно использовать ключи в нескольких экземплярах веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="24b61-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="24b61-146">Чтобы настроить поставщик EF Core, вызовите метод [персисткэйстодбконтекст \<TContext> ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) :</span><span class="sxs-lookup"><span data-stu-id="24b61-146">To configure the EF Core provider, call the [PersistKeysToDbContext\<TContext>](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="24b61-147">Универсальный параметр, `TContext` ,, должен наследовать от [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) и реализовывать [идатапротектионкэйконтекст](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="24b61-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and implement [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="24b61-148">Создайте `DataProtectionKeys` таблицу.</span><span class="sxs-lookup"><span data-stu-id="24b61-148">Create the `DataProtectionKeys` table.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="24b61-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="24b61-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="24b61-150">Выполните следующие команды в окне **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="24b61-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[<span data-ttu-id="24b61-151">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="24b61-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="24b61-152">Выполните следующие команды в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="24b61-152">Execute the following commands in a command shell:</span></span>

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="24b61-153">`MyKeysContext` параметр `DbContext` определен в предыдущем примере кода.</span><span class="sxs-lookup"><span data-stu-id="24b61-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="24b61-154">Если вы используете `DbContext` с другим именем, замените `DbContext` имя на `MyKeysContext` .</span><span class="sxs-lookup"><span data-stu-id="24b61-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="24b61-155">`DataProtectionKeys`Класс или сущность принимает структуру, показанную в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="24b61-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="24b61-156">Свойство/поле</span><span class="sxs-lookup"><span data-stu-id="24b61-156">Property/Field</span></span> | <span data-ttu-id="24b61-157">Тип CLR</span><span class="sxs-lookup"><span data-stu-id="24b61-157">CLR Type</span></span> | <span data-ttu-id="24b61-158">Тип SQL</span><span class="sxs-lookup"><span data-stu-id="24b61-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="24b61-159">`int`, PK, `IDENTITY(1,1)` , NOT NULL</span><span class="sxs-lookup"><span data-stu-id="24b61-159">`int`, PK, `IDENTITY(1,1)`, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="24b61-160">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="24b61-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="24b61-161">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="24b61-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="24b61-162">Пользовательский репозиторий ключей</span><span class="sxs-lookup"><span data-stu-id="24b61-162">Custom key repository</span></span>

<span data-ttu-id="24b61-163">Если встроенные механизмы не подходят, разработчик может указать собственный механизм сохранения ключей, предоставив пользовательский [иксмлрепоситори](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span><span class="sxs-lookup"><span data-stu-id="24b61-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
