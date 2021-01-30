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
# <a name="key-storage-providers-in-aspnet-core"></a>Поставщики хранилища ключей в ASP.NET Core

Система защиты данных [по умолчанию использует механизм обнаружения,](xref:security/data-protection/configuration/default-settings) чтобы определить, где должны храниться криптографические ключи. Разработчик может переопределить механизм обнаружения по умолчанию и указать расположение вручную.

> [!WARNING]
> Если указать явное расположение сохраняемости ключа, система защиты данных отменяет регистрацию шифрования ключа по умолчанию в механизме RESTful, поэтому ключи больше не шифруются. Рекомендуется дополнительно [указать явный механизм шифрования ключей](xref:security/data-protection/implementation/key-encryption-at-rest) для развертывания в рабочей среде.

## <a name="file-system"></a>Файловая система

Чтобы настроить репозиторий ключей на основе файловой системы, вызовите подпрограммы настройки [персисткэйстофилесистем](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) , как показано ниже. Укажите [DirectoryInfo](/dotnet/api/system.io.directoryinfo) , указывающий на репозиторий, в котором должны храниться ключи:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

`DirectoryInfo`Может указывать на каталог на локальном компьютере или на папку в общей сетевой папке. Если указывает на каталог на локальном компьютере (и сценарий заключается в том, что доступ к этому репозиторию требуется получить только для приложений на локальном компьютере), рассмотрите возможность использования [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (в Windows) для шифрования неактивных ключей. В противном случае рассмотрите возможность использования [сертификата X. 509](xref:security/data-protection/implementation/key-encryption-at-rest) для шифрования неактивных ключей.

## <a name="azure-storage"></a>Хранилище Azure

Пакет [Azure. Extensions. AspNetCore. Data Protection. blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) позволяет хранить ключи защиты данных в хранилище BLOB-объектов Azure. Ключи можно совместно использовать в нескольких экземплярах веб-приложения. Приложения могут совместно использовать проверку подлинности cookie или защиту CSRF на нескольких серверах.

Чтобы настроить поставщик хранилища BLOB-объектов Azure, вызовите одну из перегрузок [персисткэйстоазуреблобстораже](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

Если веб-приложение работает как служба Azure, строку подключения можно использовать для проверки подлинности в службе хранилища Azure с помощью [Azure. Storage. blobs](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient).

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
> Строку подключения к учетной записи хранения можно найти на портале Azure в разделе "ключи доступа" или с помощью следующей команды CLI: 
> ```bash
> az storage account show-connection-string --name <account_name> --resource-group <resource_group>
> ```

## <a name="redis"></a>Redis

::: moniker range=">= aspnetcore-2.2"

Пакет [Microsoft. AspNetCore. Data Protection. стаккексчанжередис](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) позволяет хранить ключи защиты данных в кэше Redis. Ключи можно совместно использовать в нескольких экземплярах веб-приложения. Приложения могут совместно использовать проверку подлинности cookie или защиту CSRF на нескольких серверах.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Пакет [Microsoft. AspNetCore. Data Protection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) позволяет хранить ключи защиты данных в кэше Redis. Ключи можно совместно использовать в нескольких экземплярах веб-приложения. Приложения могут совместно использовать проверку подлинности cookie или защиту CSRF на нескольких серверах.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Чтобы настроить в Redis, вызовите одну из перегрузок [персисткэйстостаккексчанжередис](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) :

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

Чтобы настроить в Redis, вызовите одну из перегрузок [персисткэйсторедис](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

Дополнительные сведения см. в следующих разделах:

* [StackExchange. Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [кэш Azure Redis](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [ASP.NET Core примеры защиты](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>Реестр

**Применяется только к развертываниям Windows.**

Иногда приложение может не иметь доступа на запись в файловую систему. Рассмотрим ситуацию, когда приложение работает как учетная запись виртуальной службы (например, удостоверение пула приложений *w3wp.exe*). В таких случаях администратор может предоставить раздел реестра, доступный для удостоверения учетной записи службы. Вызовите метод расширения [персисткэйсторегистри](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) , как показано ниже. Укажите [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) , указывающий на расположение, в котором должны храниться криптографические ключи:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys", true));
}
```

> [!IMPORTANT]
> Мы рекомендуем использовать [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) для шифрования неактивных ключей.

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

Пакет [Microsoft. AspNetCore. Data Protection. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) предоставляет механизм для хранения ключей защиты данных в базе данных с помощью Entity Framework Core. `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore`Пакет NuGet необходимо добавить в файл проекта, но он не является частью файла [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).

Этот пакет позволяет совместно использовать ключи в нескольких экземплярах веб-приложения.

Чтобы настроить поставщик EF Core, вызовите метод [персисткэйстодбконтекст \<TContext> ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) :

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Универсальный параметр, `TContext` ,, должен наследовать от [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) и реализовывать [идатапротектионкэйконтекст](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

Создайте `DataProtectionKeys` таблицу.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Выполните следующие команды в окне **консоли диспетчера пакетов** (PMC):

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Выполните следующие команды в командной оболочке:

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext` параметр `DbContext` определен в предыдущем примере кода. Если вы используете `DbContext` с другим именем, замените `DbContext` имя на `MyKeysContext` .

`DataProtectionKeys`Класс или сущность принимает структуру, показанную в следующей таблице.

| Свойство/поле | Тип CLR | Тип SQL              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`, PK, `IDENTITY(1,1)` , NOT NULL   |
| `FriendlyName` | `string` | `nvarchar(MAX)`, null |
| `Xml`          | `string` | `nvarchar(MAX)`, null |

::: moniker-end

## <a name="custom-key-repository"></a>Пользовательский репозиторий ключей

Если встроенные механизмы не подходят, разработчик может указать собственный механизм сохранения ключей, предоставив пользовательский [иксмлрепоситори](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).
