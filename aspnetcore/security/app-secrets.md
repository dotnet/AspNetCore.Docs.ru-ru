---
title: Надежное хранение секретов приложений в разработке в ASP.NET Core
author: rick-anderson
description: Узнайте, как сохранять и извлекать конфиденциальную информацию во время разработки ASP.NET Core приложения.
ms.author: scaddie
ms.custom: mvc, contperf-fy21q2
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
uid: security/app-secrets
ms.openlocfilehash: b309b834bc7156b901447c8697e6d2b0156a30f1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585816"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="42d0a-103">Надежное хранение секретов приложений в разработке в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42d0a-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="42d0a-104">[Рик Андерсон (](https://twitter.com/RickAndMSFT), [Kirk Ларкин](https://twitter.com/serpent5), [Даниэль Roth)](https://github.com/danroth27)и [Скотт Эдди (](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="42d0a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="42d0a-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/app-secrets/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42d0a-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="42d0a-106">В этом документе объясняется, как управлять конфиденциальными данными для ASP.NET Core приложения на компьютере разработки.</span><span class="sxs-lookup"><span data-stu-id="42d0a-106">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="42d0a-107">Никогда не храните пароли или другие конфиденциальные данные в исходном коде.</span><span class="sxs-lookup"><span data-stu-id="42d0a-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="42d0a-108">Производственные секреты не должны использоваться для разработки или тестирования.</span><span class="sxs-lookup"><span data-stu-id="42d0a-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="42d0a-109">Секреты не должны развертываться вместе с приложением.</span><span class="sxs-lookup"><span data-stu-id="42d0a-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="42d0a-110">Вместо этого доступ к рабочим секретам должен осуществляться через контролируемые средства, такие как переменные среды или Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="42d0a-110">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="42d0a-111">Для хранения и защиты секретов Azure в ходе тестирования и непосредственной работы используйте [Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="42d0a-111">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="42d0a-112">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="42d0a-112">Environment variables</span></span>

<span data-ttu-id="42d0a-113">Переменные среды используются, чтобы избежать хранения секретов приложения в коде или в локальных файлах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="42d0a-113">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="42d0a-114">Переменные среды переопределяют значения конфигурации для всех ранее указанных источников конфигурации.</span><span class="sxs-lookup"><span data-stu-id="42d0a-114">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="42d0a-115">Рассмотрим ASP.NET Core веб-приложение, в котором включена безопасность **отдельных учетных записей пользователей** .</span><span class="sxs-lookup"><span data-stu-id="42d0a-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="42d0a-116">Строка подключения к базе данных по умолчанию включается в *appsettings.json* файл проекта с ключом `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="42d0a-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="42d0a-117">Строка подключения по умолчанию — для LocalDB, которая выполняется в пользовательском режиме и не требует пароля.</span><span class="sxs-lookup"><span data-stu-id="42d0a-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="42d0a-118">Во время развертывания приложения `DefaultConnection` значение ключа может быть переопределено значением переменной среды.</span><span class="sxs-lookup"><span data-stu-id="42d0a-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="42d0a-119">Переменная среды может хранить полную строку подключения с конфиденциальными учетными данными.</span><span class="sxs-lookup"><span data-stu-id="42d0a-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="42d0a-120">Переменные среды обычно хранятся в виде обычного незашифрованного текста.</span><span class="sxs-lookup"><span data-stu-id="42d0a-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="42d0a-121">Если компьютер или процесс скомпрометирован, недоверенные стороны могут получить доступ к переменным среды.</span><span class="sxs-lookup"><span data-stu-id="42d0a-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="42d0a-122">Могут потребоваться дополнительные меры для предотвращения раскрытия секретов пользователя.</span><span class="sxs-lookup"><span data-stu-id="42d0a-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="42d0a-123">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="42d0a-123">Secret Manager</span></span>

<span data-ttu-id="42d0a-124">Средство диспетчера секретов сохраняет конфиденциальные данные во время разработки проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="42d0a-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="42d0a-125">В этом контексте фрагмент конфиденциальных данных является секретом приложения.</span><span class="sxs-lookup"><span data-stu-id="42d0a-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="42d0a-126">Секреты приложения хранятся в отдельном месте из дерева проекта.</span><span class="sxs-lookup"><span data-stu-id="42d0a-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="42d0a-127">Секреты приложения связаны с конкретным проектом или совместно используются в нескольких проектах.</span><span class="sxs-lookup"><span data-stu-id="42d0a-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="42d0a-128">Секреты приложения не возвращаются в систему управления версиями.</span><span class="sxs-lookup"><span data-stu-id="42d0a-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="42d0a-129">Диспетчер секретов не шифрует сохраненные секреты и не должен рассматриваться как доверенное хранилище.</span><span class="sxs-lookup"><span data-stu-id="42d0a-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="42d0a-130">Это только для целей разработки.</span><span class="sxs-lookup"><span data-stu-id="42d0a-130">It's for development purposes only.</span></span> <span data-ttu-id="42d0a-131">Ключи и значения хранятся в файле конфигурации JSON в каталоге профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="42d0a-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="42d0a-132">Как работает средство диспетчера секретов</span><span class="sxs-lookup"><span data-stu-id="42d0a-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="42d0a-133">Средство диспетчера секретов скрывает сведения о реализации, например, где и как хранятся значения.</span><span class="sxs-lookup"><span data-stu-id="42d0a-133">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="42d0a-134">Вы можете использовать это средство, не зная сведений о реализации.</span><span class="sxs-lookup"><span data-stu-id="42d0a-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="42d0a-135">Значения хранятся в файле JSON в папке профиля пользователя на локальном компьютере:</span><span class="sxs-lookup"><span data-stu-id="42d0a-135">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="42d0a-136">Windows</span><span class="sxs-lookup"><span data-stu-id="42d0a-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="42d0a-137">Путь к файловой системе:</span><span class="sxs-lookup"><span data-stu-id="42d0a-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="42d0a-138">Linux и macOS</span><span class="sxs-lookup"><span data-stu-id="42d0a-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="42d0a-139">Путь к файловой системе:</span><span class="sxs-lookup"><span data-stu-id="42d0a-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="42d0a-140">В приведенных выше путях файлов замените на `<user_secrets_id>` `UserSecretsId` значение, указанное в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="42d0a-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="42d0a-141">Не записывайте код, который зависит от расположения или формата данных, сохраненных с помощью диспетчера секретов.</span><span class="sxs-lookup"><span data-stu-id="42d0a-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="42d0a-142">Эти сведения о реализации могут измениться.</span><span class="sxs-lookup"><span data-stu-id="42d0a-142">These implementation details may change.</span></span> <span data-ttu-id="42d0a-143">Например, секретные значения не шифруются, но могут быть в будущем.</span><span class="sxs-lookup"><span data-stu-id="42d0a-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="42d0a-144">Включить хранилище секретов</span><span class="sxs-lookup"><span data-stu-id="42d0a-144">Enable secret storage</span></span>

<span data-ttu-id="42d0a-145">Диспетчер секретов работает с параметрами конфигурации конкретного проекта, хранящимися в профиле пользователя.</span><span class="sxs-lookup"><span data-stu-id="42d0a-145">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="42d0a-146">Средство диспетчера секретов включает `init` команду в пакет SDK для .NET Core 3.0.100 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="42d0a-146">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="42d0a-147">Чтобы использовать секреты пользователя, выполните следующую команду в каталоге проекта:</span><span class="sxs-lookup"><span data-stu-id="42d0a-147">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="42d0a-148">Предыдущая команда добавляет `UserSecretsId` элемент в `PropertyGroup` файл проекта.</span><span class="sxs-lookup"><span data-stu-id="42d0a-148">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="42d0a-149">По умолчанию внутренний текст `UserSecretsId` является идентификатором GUID.</span><span class="sxs-lookup"><span data-stu-id="42d0a-149">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="42d0a-150">Внутренний текст является произвольным, но уникален для проекта.</span><span class="sxs-lookup"><span data-stu-id="42d0a-150">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="42d0a-151">В Visual Studio щелкните правой кнопкой мыши проект в обозреватель решений и выберите в контекстном меню пункт **Управление секретами пользователя** .</span><span class="sxs-lookup"><span data-stu-id="42d0a-151">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="42d0a-152">Этот жест добавляет `UserSecretsId` элемент, заполненный идентификатором GUID, в файл проекта.</span><span class="sxs-lookup"><span data-stu-id="42d0a-152">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="42d0a-153">Задать секрет</span><span class="sxs-lookup"><span data-stu-id="42d0a-153">Set a secret</span></span>

<span data-ttu-id="42d0a-154">Определите секрет приложения, состоящий из ключа и его значения.</span><span class="sxs-lookup"><span data-stu-id="42d0a-154">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="42d0a-155">Секрет связан со `UserSecretsId` значением проекта.</span><span class="sxs-lookup"><span data-stu-id="42d0a-155">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="42d0a-156">Например, выполните следующую команду из каталога, в котором существует файл проекта:</span><span class="sxs-lookup"><span data-stu-id="42d0a-156">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="42d0a-157">В предыдущем примере двоеточие обозначает, что `Movies` является объектным литералом со `ServiceApiKey` свойством.</span><span class="sxs-lookup"><span data-stu-id="42d0a-157">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="42d0a-158">Диспетчер секретов можно использовать и в других каталогах.</span><span class="sxs-lookup"><span data-stu-id="42d0a-158">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="42d0a-159">Используйте `--project` параметр, чтобы указать путь файловой системы, в котором существует файл проекта.</span><span class="sxs-lookup"><span data-stu-id="42d0a-159">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="42d0a-160">Пример:</span><span class="sxs-lookup"><span data-stu-id="42d0a-160">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="42d0a-161">Сведение структуры JSON в Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42d0a-161">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="42d0a-162">Жест **управления пользовательскими секретами** в Visual Studio открывает *secrets.jsдля* файла в текстовом редакторе.</span><span class="sxs-lookup"><span data-stu-id="42d0a-162">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="42d0a-163">Замените содержимое *secrets.jsна* пары "ключ-значение", которые необходимо сохранить.</span><span class="sxs-lookup"><span data-stu-id="42d0a-163">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="42d0a-164">Пример:</span><span class="sxs-lookup"><span data-stu-id="42d0a-164">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="42d0a-165">Структура JSON преобразуется в плоскую структуру после изменений с помощью `dotnet user-secrets remove` или `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="42d0a-165">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="42d0a-166">Например, запуск `dotnet user-secrets remove "Movies:ConnectionString"` сворачивает `Movies` объектный литерал.</span><span class="sxs-lookup"><span data-stu-id="42d0a-166">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="42d0a-167">Измененный файл напоминает следующий код JSON:</span><span class="sxs-lookup"><span data-stu-id="42d0a-167">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="42d0a-168">Задание нескольких секретов</span><span class="sxs-lookup"><span data-stu-id="42d0a-168">Set multiple secrets</span></span>

<span data-ttu-id="42d0a-169">Пакет секретов можно задать с помощью конвейера `set` командной строки.</span><span class="sxs-lookup"><span data-stu-id="42d0a-169">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="42d0a-170">В следующем примере *input.js* содержимое файла передается `set` команде.</span><span class="sxs-lookup"><span data-stu-id="42d0a-170">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="42d0a-171">Windows</span><span class="sxs-lookup"><span data-stu-id="42d0a-171">Windows</span></span>](#tab/windows)

<span data-ttu-id="42d0a-172">Откройте командную оболочку и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="42d0a-172">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="42d0a-173">Linux и macOS</span><span class="sxs-lookup"><span data-stu-id="42d0a-173">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="42d0a-174">Откройте командную оболочку и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="42d0a-174">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="42d0a-175">Доступ к секрету</span><span class="sxs-lookup"><span data-stu-id="42d0a-175">Access a secret</span></span>

<span data-ttu-id="42d0a-176">Чтобы получить доступ к секрету, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="42d0a-176">To access a secret, complete the following steps:</span></span>

1. [<span data-ttu-id="42d0a-177">Регистрация источника конфигурации секретов пользователя</span><span class="sxs-lookup"><span data-stu-id="42d0a-177">Register the user secrets configuration source</span></span>](#register-the-user-secrets-configuration-source)
1. [<span data-ttu-id="42d0a-178">Чтение секрета с помощью API конфигурации</span><span class="sxs-lookup"><span data-stu-id="42d0a-178">Read the secret via the Configuration API</span></span>](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a><span data-ttu-id="42d0a-179">Регистрация источника конфигурации секретов пользователя</span><span class="sxs-lookup"><span data-stu-id="42d0a-179">Register the user secrets configuration source</span></span>

<span data-ttu-id="42d0a-180">[Поставщик конфигурации](/dotnet/core/extensions/configuration-providers) секретов пользователя регистрирует соответствующий источник конфигурации с помощью [API конфигурации](xref:fundamentals/configuration/index).NET.</span><span class="sxs-lookup"><span data-stu-id="42d0a-180">The user secrets [configuration provider](/dotnet/core/extensions/configuration-providers) registers the appropriate configuration source with the .NET [Configuration API](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="42d0a-181">Источник конфигурации секреты пользователя автоматически добавляется в режим разработки при вызове проекта <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="42d0a-181">The user secrets configuration source is automatically added in Development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="42d0a-182">`CreateDefaultBuilder` вызывает <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> , если <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> имеет значение <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="42d0a-182">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="42d0a-183">Если `CreateDefaultBuilder` метод не вызывается, добавьте источник конфигурации секреты пользователя явным образом, вызвав <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> в <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="42d0a-183">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="42d0a-184">Вызывайте `AddUserSecrets` только при запуске приложения в среде разработки, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="42d0a-184">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a><span data-ttu-id="42d0a-185">Чтение секрета с помощью API конфигурации</span><span class="sxs-lookup"><span data-stu-id="42d0a-185">Read the secret via the Configuration API</span></span>

<span data-ttu-id="42d0a-186">Если зарегистрирован источник конфигурации секреты пользователя, API конфигурации .NET может считывать секреты.</span><span class="sxs-lookup"><span data-stu-id="42d0a-186">If the user secrets configuration source is registered, the .NET Configuration API can read the secrets.</span></span> <span data-ttu-id="42d0a-187">[Внедрение конструктора](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) можно использовать для получения доступа к API конфигурации .NET.</span><span class="sxs-lookup"><span data-stu-id="42d0a-187">[Constructor injection](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) can be used to gain access to the .NET Configuration API.</span></span> <span data-ttu-id="42d0a-188">Рассмотрим следующие примеры чтения `Movies:ServiceApiKey` ключа:</span><span class="sxs-lookup"><span data-stu-id="42d0a-188">Consider the following examples of reading the `Movies:ServiceApiKey` key:</span></span>

<span data-ttu-id="42d0a-189">**Класс запуска:**</span><span class="sxs-lookup"><span data-stu-id="42d0a-189">**Startup class:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

<span data-ttu-id="42d0a-190">**Razor Модель страницы страниц:**</span><span class="sxs-lookup"><span data-stu-id="42d0a-190">**Razor Pages page model:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

<span data-ttu-id="42d0a-191">Дополнительные сведения см. в статье [Настройка доступа в конфигурации запуска](xref:fundamentals/configuration/index#access-configuration-in-startup) и [доступа на Razor страницах](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span><span class="sxs-lookup"><span data-stu-id="42d0a-191">For more information, see [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup) and [Access configuration in Razor Pages](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span></span>

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="42d0a-192">Преобразование секретов в POCO</span><span class="sxs-lookup"><span data-stu-id="42d0a-192">Map secrets to a POCO</span></span>

<span data-ttu-id="42d0a-193">Сопоставление целого литерала объекта с POCO (простой класс .NET со свойствами) полезен для статистической обработки связанных свойств.</span><span class="sxs-lookup"><span data-stu-id="42d0a-193">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="42d0a-194">Чтобы связать предыдущие секреты с POCO, используйте функцию [привязки графа объектов](xref:fundamentals/configuration/index#bind-to-an-object-graph) API настройки .NET.</span><span class="sxs-lookup"><span data-stu-id="42d0a-194">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="42d0a-195">Следующий код привязывается к пользовательской `MovieSettings` POCO и обращается к `ServiceApiKey` значению свойства:</span><span class="sxs-lookup"><span data-stu-id="42d0a-195">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="42d0a-196">`Movies:ConnectionString`Секреты и `Movies:ServiceApiKey` сопоставлены с соответствующими свойствами в `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="42d0a-196">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="42d0a-197">Замена строк секретами</span><span class="sxs-lookup"><span data-stu-id="42d0a-197">String replacement with secrets</span></span>

<span data-ttu-id="42d0a-198">Хранение паролей в виде обычного текста является небезопасным.</span><span class="sxs-lookup"><span data-stu-id="42d0a-198">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="42d0a-199">Например, строка подключения к базе данных, хранимая в, *appsettings.json* может включать пароль для указанного пользователя:</span><span class="sxs-lookup"><span data-stu-id="42d0a-199">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="42d0a-200">Более безопасный подход заключается в хранении пароля в качестве секрета.</span><span class="sxs-lookup"><span data-stu-id="42d0a-200">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="42d0a-201">Пример:</span><span class="sxs-lookup"><span data-stu-id="42d0a-201">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="42d0a-202">Удалите `Password` пару "ключ-значение" из строки подключения в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="42d0a-202">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="42d0a-203">Пример:</span><span class="sxs-lookup"><span data-stu-id="42d0a-203">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="42d0a-204">Значение секрета может быть задано для <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> свойства объекта, чтобы завершить строку подключения:</span><span class="sxs-lookup"><span data-stu-id="42d0a-204">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="42d0a-205">Вывод списка секретов</span><span class="sxs-lookup"><span data-stu-id="42d0a-205">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="42d0a-206">Выполните следующую команду из каталога, в котором существует файл проекта:</span><span class="sxs-lookup"><span data-stu-id="42d0a-206">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="42d0a-207">Отображаются следующие результаты:</span><span class="sxs-lookup"><span data-stu-id="42d0a-207">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="42d0a-208">В предыдущем примере двоеточие в именах ключей обозначает иерархию объектов в *secrets.js*.</span><span class="sxs-lookup"><span data-stu-id="42d0a-208">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="42d0a-209">Удаление одного секрета</span><span class="sxs-lookup"><span data-stu-id="42d0a-209">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="42d0a-210">Выполните следующую команду из каталога, в котором существует файл проекта:</span><span class="sxs-lookup"><span data-stu-id="42d0a-210">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="42d0a-211">Файл *secrets.jsв* приложении изменен для удаления пары "ключ-значение", связанной с `MoviesConnectionString` ключом:</span><span class="sxs-lookup"><span data-stu-id="42d0a-211">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="42d0a-212">`dotnet user-secrets list` отображает следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="42d0a-212">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="42d0a-213">Удалить все секреты</span><span class="sxs-lookup"><span data-stu-id="42d0a-213">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="42d0a-214">Выполните следующую команду из каталога, в котором существует файл проекта:</span><span class="sxs-lookup"><span data-stu-id="42d0a-214">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="42d0a-215">Все секреты пользователя для приложения удалены из *secrets.jsв* файле:</span><span class="sxs-lookup"><span data-stu-id="42d0a-215">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="42d0a-216">При выполнении `dotnet user-secrets list` отображается следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="42d0a-216">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="42d0a-217">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="42d0a-217">Additional resources</span></span>

* <span data-ttu-id="42d0a-218">Сведения о доступе к пользовательским секретам из IIS см. в [этой статье](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="42d0a-218">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="42d0a-219">[Рик Андерсон (](https://twitter.com/RickAndMSFT), [Даниэль Roth)](https://github.com/danroth27)и [Скотт Эдди (](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="42d0a-219">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="42d0a-220">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/app-secrets/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42d0a-220">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="42d0a-221">В этом документе объясняется, как управлять конфиденциальными данными для ASP.NET Core приложения на компьютере разработки.</span><span class="sxs-lookup"><span data-stu-id="42d0a-221">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="42d0a-222">Никогда не храните пароли или другие конфиденциальные данные в исходном коде.</span><span class="sxs-lookup"><span data-stu-id="42d0a-222">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="42d0a-223">Производственные секреты не должны использоваться для разработки или тестирования.</span><span class="sxs-lookup"><span data-stu-id="42d0a-223">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="42d0a-224">Секреты не должны развертываться вместе с приложением.</span><span class="sxs-lookup"><span data-stu-id="42d0a-224">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="42d0a-225">Вместо этого доступ к рабочим секретам должен осуществляться через контролируемые средства, такие как переменные среды или Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="42d0a-225">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="42d0a-226">Для хранения и защиты секретов Azure в ходе тестирования и непосредственной работы используйте [Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="42d0a-226">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="42d0a-227">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="42d0a-227">Environment variables</span></span>

<span data-ttu-id="42d0a-228">Переменные среды используются, чтобы избежать хранения секретов приложения в коде или в локальных файлах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="42d0a-228">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="42d0a-229">Переменные среды переопределяют значения конфигурации для всех ранее указанных источников конфигурации.</span><span class="sxs-lookup"><span data-stu-id="42d0a-229">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="42d0a-230">Рассмотрим ASP.NET Core веб-приложение, в котором включена безопасность **отдельных учетных записей пользователей** .</span><span class="sxs-lookup"><span data-stu-id="42d0a-230">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="42d0a-231">Строка подключения к базе данных по умолчанию включается в *appsettings.json* файл проекта с ключом `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="42d0a-231">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="42d0a-232">Строка подключения по умолчанию — для LocalDB, которая выполняется в пользовательском режиме и не требует пароля.</span><span class="sxs-lookup"><span data-stu-id="42d0a-232">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="42d0a-233">Во время развертывания приложения `DefaultConnection` значение ключа может быть переопределено значением переменной среды.</span><span class="sxs-lookup"><span data-stu-id="42d0a-233">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="42d0a-234">Переменная среды может хранить полную строку подключения с конфиденциальными учетными данными.</span><span class="sxs-lookup"><span data-stu-id="42d0a-234">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="42d0a-235">Переменные среды обычно хранятся в виде обычного незашифрованного текста.</span><span class="sxs-lookup"><span data-stu-id="42d0a-235">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="42d0a-236">Если компьютер или процесс скомпрометирован, недоверенные стороны могут получить доступ к переменным среды.</span><span class="sxs-lookup"><span data-stu-id="42d0a-236">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="42d0a-237">Могут потребоваться дополнительные меры для предотвращения раскрытия секретов пользователя.</span><span class="sxs-lookup"><span data-stu-id="42d0a-237">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="42d0a-238">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="42d0a-238">Secret Manager</span></span>

<span data-ttu-id="42d0a-239">Средство диспетчера секретов сохраняет конфиденциальные данные во время разработки проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="42d0a-239">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="42d0a-240">В этом контексте фрагмент конфиденциальных данных является секретом приложения.</span><span class="sxs-lookup"><span data-stu-id="42d0a-240">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="42d0a-241">Секреты приложения хранятся в отдельном месте из дерева проекта.</span><span class="sxs-lookup"><span data-stu-id="42d0a-241">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="42d0a-242">Секреты приложения связаны с конкретным проектом или совместно используются в нескольких проектах.</span><span class="sxs-lookup"><span data-stu-id="42d0a-242">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="42d0a-243">Секреты приложения не возвращаются в систему управления версиями.</span><span class="sxs-lookup"><span data-stu-id="42d0a-243">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="42d0a-244">Диспетчер секретов не шифрует сохраненные секреты и не должен рассматриваться как доверенное хранилище.</span><span class="sxs-lookup"><span data-stu-id="42d0a-244">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="42d0a-245">Это только для целей разработки.</span><span class="sxs-lookup"><span data-stu-id="42d0a-245">It's for development purposes only.</span></span> <span data-ttu-id="42d0a-246">Ключи и значения хранятся в файле конфигурации JSON в каталоге профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="42d0a-246">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="42d0a-247">Как работает средство диспетчера секретов</span><span class="sxs-lookup"><span data-stu-id="42d0a-247">How the Secret Manager tool works</span></span>

<span data-ttu-id="42d0a-248">Средство диспетчера секретов скрывает сведения о реализации, например, где и как хранятся значения.</span><span class="sxs-lookup"><span data-stu-id="42d0a-248">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="42d0a-249">Вы можете использовать это средство, не зная сведений о реализации.</span><span class="sxs-lookup"><span data-stu-id="42d0a-249">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="42d0a-250">Значения хранятся в файле JSON в папке профиля пользователя на локальном компьютере:</span><span class="sxs-lookup"><span data-stu-id="42d0a-250">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="42d0a-251">Windows</span><span class="sxs-lookup"><span data-stu-id="42d0a-251">Windows</span></span>](#tab/windows)

<span data-ttu-id="42d0a-252">Путь к файловой системе:</span><span class="sxs-lookup"><span data-stu-id="42d0a-252">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="42d0a-253">Linux и macOS</span><span class="sxs-lookup"><span data-stu-id="42d0a-253">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="42d0a-254">Путь к файловой системе:</span><span class="sxs-lookup"><span data-stu-id="42d0a-254">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="42d0a-255">В приведенных выше путях файлов замените на `<user_secrets_id>` `UserSecretsId` значение, указанное в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="42d0a-255">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="42d0a-256">Не записывайте код, который зависит от расположения или формата данных, сохраненных с помощью диспетчера секретов.</span><span class="sxs-lookup"><span data-stu-id="42d0a-256">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="42d0a-257">Эти сведения о реализации могут измениться.</span><span class="sxs-lookup"><span data-stu-id="42d0a-257">These implementation details may change.</span></span> <span data-ttu-id="42d0a-258">Например, секретные значения не шифруются, но могут быть в будущем.</span><span class="sxs-lookup"><span data-stu-id="42d0a-258">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="42d0a-259">Включить хранилище секретов</span><span class="sxs-lookup"><span data-stu-id="42d0a-259">Enable secret storage</span></span>

<span data-ttu-id="42d0a-260">Диспетчер секретов работает с параметрами конфигурации конкретного проекта, хранящимися в профиле пользователя.</span><span class="sxs-lookup"><span data-stu-id="42d0a-260">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="42d0a-261">Чтобы использовать секреты пользователя, определите `UserSecretsId` элемент в `PropertyGroup` файле проекта.</span><span class="sxs-lookup"><span data-stu-id="42d0a-261">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="42d0a-262">Внутренний текст `UserSecretsId` является произвольным, но уникален для проекта.</span><span class="sxs-lookup"><span data-stu-id="42d0a-262">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="42d0a-263">Разработчики обычно создают идентификатор GUID для `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="42d0a-263">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="42d0a-264">В Visual Studio щелкните правой кнопкой мыши проект в обозреватель решений и выберите в контекстном меню пункт **Управление секретами пользователя** .</span><span class="sxs-lookup"><span data-stu-id="42d0a-264">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="42d0a-265">Этот жест добавляет `UserSecretsId` элемент, заполненный идентификатором GUID, в файл проекта.</span><span class="sxs-lookup"><span data-stu-id="42d0a-265">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="42d0a-266">Задать секрет</span><span class="sxs-lookup"><span data-stu-id="42d0a-266">Set a secret</span></span>

<span data-ttu-id="42d0a-267">Определите секрет приложения, состоящий из ключа и его значения.</span><span class="sxs-lookup"><span data-stu-id="42d0a-267">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="42d0a-268">Секрет связан со `UserSecretsId` значением проекта.</span><span class="sxs-lookup"><span data-stu-id="42d0a-268">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="42d0a-269">Например, выполните следующую команду из каталога, в котором существует файл проекта:</span><span class="sxs-lookup"><span data-stu-id="42d0a-269">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="42d0a-270">В предыдущем примере двоеточие обозначает, что `Movies` является объектным литералом со `ServiceApiKey` свойством.</span><span class="sxs-lookup"><span data-stu-id="42d0a-270">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="42d0a-271">Диспетчер секретов можно использовать и в других каталогах.</span><span class="sxs-lookup"><span data-stu-id="42d0a-271">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="42d0a-272">Используйте `--project` параметр, чтобы указать путь файловой системы, в котором существует файл проекта.</span><span class="sxs-lookup"><span data-stu-id="42d0a-272">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="42d0a-273">Пример:</span><span class="sxs-lookup"><span data-stu-id="42d0a-273">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="42d0a-274">Сведение структуры JSON в Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42d0a-274">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="42d0a-275">Жест **управления пользовательскими секретами** в Visual Studio открывает *secrets.jsдля* файла в текстовом редакторе.</span><span class="sxs-lookup"><span data-stu-id="42d0a-275">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="42d0a-276">Замените содержимое *secrets.jsна* пары "ключ-значение", которые необходимо сохранить.</span><span class="sxs-lookup"><span data-stu-id="42d0a-276">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="42d0a-277">Пример:</span><span class="sxs-lookup"><span data-stu-id="42d0a-277">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="42d0a-278">Структура JSON преобразуется в плоскую структуру после изменений с помощью `dotnet user-secrets remove` или `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="42d0a-278">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="42d0a-279">Например, запуск `dotnet user-secrets remove "Movies:ConnectionString"` сворачивает `Movies` объектный литерал.</span><span class="sxs-lookup"><span data-stu-id="42d0a-279">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="42d0a-280">Измененный файл напоминает следующий код JSON:</span><span class="sxs-lookup"><span data-stu-id="42d0a-280">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="42d0a-281">Задание нескольких секретов</span><span class="sxs-lookup"><span data-stu-id="42d0a-281">Set multiple secrets</span></span>

<span data-ttu-id="42d0a-282">Пакет секретов можно задать с помощью конвейера `set` командной строки.</span><span class="sxs-lookup"><span data-stu-id="42d0a-282">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="42d0a-283">В следующем примере *input.js* содержимое файла передается `set` команде.</span><span class="sxs-lookup"><span data-stu-id="42d0a-283">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="42d0a-284">Windows</span><span class="sxs-lookup"><span data-stu-id="42d0a-284">Windows</span></span>](#tab/windows)

<span data-ttu-id="42d0a-285">Откройте командную оболочку и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="42d0a-285">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="42d0a-286">Linux и macOS</span><span class="sxs-lookup"><span data-stu-id="42d0a-286">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="42d0a-287">Откройте командную оболочку и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="42d0a-287">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="42d0a-288">Доступ к секрету</span><span class="sxs-lookup"><span data-stu-id="42d0a-288">Access a secret</span></span>

<span data-ttu-id="42d0a-289">[API конфигурации](xref:fundamentals/configuration/index) предоставляет доступ к секретам пользователя.</span><span class="sxs-lookup"><span data-stu-id="42d0a-289">The [Configuration API](xref:fundamentals/configuration/index) provides access to user secrets.</span></span>

<span data-ttu-id="42d0a-290">Если проект предназначен для платформа .NET Framework, установите [Microsoft.Extensions.Configфигурации. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) Пакет NuGet усерсекретс.</span><span class="sxs-lookup"><span data-stu-id="42d0a-290">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="42d0a-291">В ASP.NET Core 2,0 или более поздней версии источник конфигурации секреты пользователя автоматически добавляется в режим разработки при вызове проекта <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="42d0a-291">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="42d0a-292">`CreateDefaultBuilder` вызывает <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> , если <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> имеет значение <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="42d0a-292">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="42d0a-293">Если `CreateDefaultBuilder` метод не вызывается, добавьте источник конфигурации секреты пользователя явным образом, вызвав <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> в `Startup` конструкторе.</span><span class="sxs-lookup"><span data-stu-id="42d0a-293">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="42d0a-294">Вызывайте `AddUserSecrets` только при запуске приложения в среде разработки, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="42d0a-294">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="42d0a-295">Секреты пользователя можно получить с помощью API конфигурации .NET:</span><span class="sxs-lookup"><span data-stu-id="42d0a-295">User secrets can be retrieved via the .NET Configuration API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="42d0a-296">Преобразование секретов в POCO</span><span class="sxs-lookup"><span data-stu-id="42d0a-296">Map secrets to a POCO</span></span>

<span data-ttu-id="42d0a-297">Сопоставление целого литерала объекта с POCO (простой класс .NET со свойствами) полезен для статистической обработки связанных свойств.</span><span class="sxs-lookup"><span data-stu-id="42d0a-297">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="42d0a-298">Чтобы связать предыдущие секреты с POCO, используйте функцию [привязки графа объектов](xref:fundamentals/configuration/index#bind-to-an-object-graph) API настройки .NET.</span><span class="sxs-lookup"><span data-stu-id="42d0a-298">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="42d0a-299">Следующий код привязывается к пользовательской `MovieSettings` POCO и обращается к `ServiceApiKey` значению свойства:</span><span class="sxs-lookup"><span data-stu-id="42d0a-299">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="42d0a-300">`Movies:ConnectionString`Секреты и `Movies:ServiceApiKey` сопоставлены с соответствующими свойствами в `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="42d0a-300">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="42d0a-301">Замена строк секретами</span><span class="sxs-lookup"><span data-stu-id="42d0a-301">String replacement with secrets</span></span>

<span data-ttu-id="42d0a-302">Хранение паролей в виде обычного текста является небезопасным.</span><span class="sxs-lookup"><span data-stu-id="42d0a-302">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="42d0a-303">Например, строка подключения к базе данных, хранимая в, *appsettings.json* может включать пароль для указанного пользователя:</span><span class="sxs-lookup"><span data-stu-id="42d0a-303">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="42d0a-304">Более безопасный подход заключается в хранении пароля в качестве секрета.</span><span class="sxs-lookup"><span data-stu-id="42d0a-304">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="42d0a-305">Пример:</span><span class="sxs-lookup"><span data-stu-id="42d0a-305">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="42d0a-306">Удалите `Password` пару "ключ-значение" из строки подключения в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="42d0a-306">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="42d0a-307">Пример:</span><span class="sxs-lookup"><span data-stu-id="42d0a-307">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="42d0a-308">Значение секрета может быть задано для <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> свойства объекта, чтобы завершить строку подключения:</span><span class="sxs-lookup"><span data-stu-id="42d0a-308">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="42d0a-309">Вывод списка секретов</span><span class="sxs-lookup"><span data-stu-id="42d0a-309">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="42d0a-310">Выполните следующую команду из каталога, в котором существует файл проекта:</span><span class="sxs-lookup"><span data-stu-id="42d0a-310">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="42d0a-311">Отображаются следующие результаты:</span><span class="sxs-lookup"><span data-stu-id="42d0a-311">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="42d0a-312">В предыдущем примере двоеточие в именах ключей обозначает иерархию объектов в *secrets.js*.</span><span class="sxs-lookup"><span data-stu-id="42d0a-312">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="42d0a-313">Удаление одного секрета</span><span class="sxs-lookup"><span data-stu-id="42d0a-313">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="42d0a-314">Выполните следующую команду из каталога, в котором существует файл проекта:</span><span class="sxs-lookup"><span data-stu-id="42d0a-314">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="42d0a-315">Файл *secrets.jsв* приложении изменен для удаления пары "ключ-значение", связанной с `MoviesConnectionString` ключом:</span><span class="sxs-lookup"><span data-stu-id="42d0a-315">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="42d0a-316">При выполнении `dotnet user-secrets list` отображается следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="42d0a-316">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="42d0a-317">Удалить все секреты</span><span class="sxs-lookup"><span data-stu-id="42d0a-317">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="42d0a-318">Выполните следующую команду из каталога, в котором существует файл проекта:</span><span class="sxs-lookup"><span data-stu-id="42d0a-318">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="42d0a-319">Все секреты пользователя для приложения удалены из *secrets.jsв* файле:</span><span class="sxs-lookup"><span data-stu-id="42d0a-319">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="42d0a-320">При выполнении `dotnet user-secrets list` отображается следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="42d0a-320">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="42d0a-321">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="42d0a-321">Additional resources</span></span>

* <span data-ttu-id="42d0a-322">Сведения о доступе к пользовательским секретам из IIS см. в [этой статье](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="42d0a-322">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end