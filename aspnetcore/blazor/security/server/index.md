---
title: Защита приложений ASP.NET Core Blazor Server
author: guardrex
description: Сведения о защите приложений Blazor Server как приложений ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 147ebbeb84e1755307d627ef428d92d1b0248c74
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394867"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="5458d-103">Защита приложений ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="5458d-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="5458d-104">Приложения Blazor Server настраиваются для обеспечения безопасности так же, как приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5458d-104">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="5458d-105">Дополнительные сведения см. в статьях в документе <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="5458d-105">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="5458d-106">Разделы в этом обзоре посвящены Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="5458d-106">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="5458d-107">Шаблон проекта Blazor Server</span><span class="sxs-lookup"><span data-stu-id="5458d-107">Blazor Server project template</span></span>

<span data-ttu-id="5458d-108">[Шаблон проекта Blazor Server](xref:blazor/project-structure) можно настроить для проверки подлинности при создании проекта.</span><span class="sxs-lookup"><span data-stu-id="5458d-108">The [Blazor Server project template](xref:blazor/project-structure) can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5458d-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5458d-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5458d-110">Следуйте инструкциям по работе с Visual Studio (<xref:blazor/tooling>), чтобы создать проект Blazor Server с механизмом проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5458d-110">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="5458d-111">Выбрав шаблон **Приложение Blazor Server** в диалоговом окне **Создание веб-приложения ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="5458d-111">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="5458d-112">Откроется диалоговое окно с тем же набором механизмов аутентификации, которые доступны для других проектов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5458d-112">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="5458d-113">**Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="5458d-113">**No Authentication**</span></span>
* <span data-ttu-id="5458d-114">**Учетные записи отдельных пользователей**. Учетные записи пользователей могут храниться:</span><span class="sxs-lookup"><span data-stu-id="5458d-114">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="5458d-115">Внутри приложения с помощью системы [Identity](xref:security/authentication/identity) в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5458d-115">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="5458d-116">в [Azure AD B2C](xref:security/authentication/azure-ad-b2c);</span><span class="sxs-lookup"><span data-stu-id="5458d-116">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="5458d-117">**рабочие или учебные учетные записи**.</span><span class="sxs-lookup"><span data-stu-id="5458d-117">**Work or School Accounts**</span></span>
* <span data-ttu-id="5458d-118">**Проверка подлинности Windows**</span><span class="sxs-lookup"><span data-stu-id="5458d-118">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5458d-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5458d-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5458d-120">Следуйте инструкциям по работе с Visual Studio Code (<xref:blazor/tooling>), чтобы создать проект Blazor Server с механизмом проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5458d-120">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="5458d-121">Допустимые значения аутентификации (`{AUTHENTICATION}`) перечислены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="5458d-121">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="5458d-122">Механизм аутентификации</span><span class="sxs-lookup"><span data-stu-id="5458d-122">Authentication mechanism</span></span> | <span data-ttu-id="5458d-123">Описание</span><span class="sxs-lookup"><span data-stu-id="5458d-123">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="5458d-124">`None` (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="5458d-124">`None` (default)</span></span>         | <span data-ttu-id="5458d-125">без аутентификации;</span><span class="sxs-lookup"><span data-stu-id="5458d-125">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="5458d-126">Пользователи, сохраненные в приложении с помощью ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="5458d-126">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="5458d-127">Пользователи, сохраненные в [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="5458d-127">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="5458d-128">Корпоративная аутентификация для отдельного клиента</span><span class="sxs-lookup"><span data-stu-id="5458d-128">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="5458d-129">Корпоративная аутентификация для нескольких клиентов</span><span class="sxs-lookup"><span data-stu-id="5458d-129">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="5458d-130">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="5458d-130">Windows Authentication</span></span> |

<span data-ttu-id="5458d-131">С помощью параметра `-o|--output` команда использует значение, указанное для заполнителя `{APP NAME}`, чтобы:</span><span class="sxs-lookup"><span data-stu-id="5458d-131">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="5458d-132">Создайте папку для проекта.</span><span class="sxs-lookup"><span data-stu-id="5458d-132">Create a folder for the project.</span></span>
* <span data-ttu-id="5458d-133">Дайте проекту имя.</span><span class="sxs-lookup"><span data-stu-id="5458d-133">Name the project.</span></span>

<span data-ttu-id="5458d-134">См. сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5458d-134">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5458d-135">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5458d-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5458d-136">Следуйте инструкциям по работе с Visual Studio для Mac (<xref:blazor/tooling>).</span><span class="sxs-lookup"><span data-stu-id="5458d-136">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="5458d-137">На шаге **Настройка нового приложения Blazor Server** выберите **Индивидуальная проверка подлинности (в приложении)** из раскрывающегося списка **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="5458d-137">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="5458d-138">Приложение будет создано для отдельных пользователей, сохраненных в приложении с помощью ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="5458d-138">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5458d-139">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="5458d-139">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="5458d-140">Создайте новый проект Blazor Server с механизмом проверки подлинности, используя следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="5458d-140">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="5458d-141">Допустимые значения аутентификации (`{AUTHENTICATION}`) перечислены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="5458d-141">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="5458d-142">Механизм аутентификации</span><span class="sxs-lookup"><span data-stu-id="5458d-142">Authentication mechanism</span></span> | <span data-ttu-id="5458d-143">Описание</span><span class="sxs-lookup"><span data-stu-id="5458d-143">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="5458d-144">`None` (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="5458d-144">`None` (default)</span></span>         | <span data-ttu-id="5458d-145">без аутентификации;</span><span class="sxs-lookup"><span data-stu-id="5458d-145">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="5458d-146">Пользователи, сохраненные в приложении с помощью ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="5458d-146">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="5458d-147">Пользователи, сохраненные в [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="5458d-147">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="5458d-148">Корпоративная аутентификация для отдельного клиента</span><span class="sxs-lookup"><span data-stu-id="5458d-148">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="5458d-149">Корпоративная аутентификация для нескольких клиентов</span><span class="sxs-lookup"><span data-stu-id="5458d-149">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="5458d-150">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="5458d-150">Windows Authentication</span></span> |

<span data-ttu-id="5458d-151">С помощью параметра `-o|--output` команда использует значение, указанное для заполнителя `{APP NAME}`, чтобы:</span><span class="sxs-lookup"><span data-stu-id="5458d-151">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="5458d-152">Создайте папку для проекта.</span><span class="sxs-lookup"><span data-stu-id="5458d-152">Create a folder for the project.</span></span>
* <span data-ttu-id="5458d-153">Дайте проекту имя.</span><span class="sxs-lookup"><span data-stu-id="5458d-153">Name the project.</span></span>

<span data-ttu-id="5458d-154">Дополнительные сведения</span><span class="sxs-lookup"><span data-stu-id="5458d-154">For more information:</span></span>

* <span data-ttu-id="5458d-155">Сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) см. в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5458d-155">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="5458d-156">Выполните команду help для шаблона Blazor Server (`blazorserver`) в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="5458d-156">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-identity"></a><span data-ttu-id="5458d-157">Формирование шаблонов Identity</span><span class="sxs-lookup"><span data-stu-id="5458d-157">Scaffold Identity</span></span>

<span data-ttu-id="5458d-158">Сформируйте шаблоны Identity для проекта Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="5458d-158">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="5458d-159">[Без существующей авторизации](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="5458d-159">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="5458d-160">[С авторизацией](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="5458d-160">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-claims-and-tokens-from-external-providers"></a><span data-ttu-id="5458d-161">Дополнительные утверждения и маркеры от внешних поставщиков</span><span class="sxs-lookup"><span data-stu-id="5458d-161">Additional claims and tokens from external providers</span></span>

<span data-ttu-id="5458d-162">Сведения о хранении дополнительных утверждений от внешних поставщиков см. в этой статье: <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="5458d-162">To store additional claims from external providers, see <xref:security/authentication/social/additional-claims>.</span></span>

## <a name="azure-app-service-on-linux-with-identity-server"></a><span data-ttu-id="5458d-163">Служба приложений Azure в Linux с сервером Identity</span><span class="sxs-lookup"><span data-stu-id="5458d-163">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="5458d-164">При развертывании в Службе приложений Azure в Linux с сервером Identity нужно указать издателя явно.</span><span class="sxs-lookup"><span data-stu-id="5458d-164">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="5458d-165">Для получения дополнительной информации см. <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span><span class="sxs-lookup"><span data-stu-id="5458d-165">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5458d-166">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5458d-166">Additional resources</span></span>

* [<span data-ttu-id="5458d-167">Краткое руководство. Добавление входа через Майкрософт в веб-приложение ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5458d-167">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="5458d-168">Краткое руководство. Защита веб-API ASP.NET Core с помощью платформы удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="5458d-168">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="5458d-169"><xref:host-and-deploy/proxy-load-balancer>: Включает рекомендации по следующим вопросам:</span><span class="sxs-lookup"><span data-stu-id="5458d-169"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="5458d-170">Использование ПО промежуточного слоя перенаправленных заголовков для сохранения сведений о схеме HTTPS на прокси-серверах и во внутренних сетях.</span><span class="sxs-lookup"><span data-stu-id="5458d-170">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="5458d-171">Дополнительные сценарии и варианты использования, включая ручную настройку схемы, изменение пути запроса для правильной маршрутизации запроса и перенаправление схемы запроса для обратных прокси-серверов Linux и обратных прокси-серверов, отличных от IIS.</span><span class="sxs-lookup"><span data-stu-id="5458d-171">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
