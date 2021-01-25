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
ms.openlocfilehash: 5031273c3395be4365b3a6d239ebce7aaf9b66ac
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658642"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="1bb40-103">Защита приложений ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="1bb40-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="1bb40-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="1bb40-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1bb40-105">Приложения Blazor Server настраиваются для обеспечения безопасности так же, как приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb40-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="1bb40-106">Дополнительные сведения см. в статьях в документе <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="1bb40-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="1bb40-107">Разделы в этом обзоре посвящены Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="1bb40-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="1bb40-108">Шаблон проекта Blazor Server</span><span class="sxs-lookup"><span data-stu-id="1bb40-108">Blazor Server project template</span></span>

<span data-ttu-id="1bb40-109">Шаблон проекта Blazor Server можно настроить для проверки подлинности при создании проекта.</span><span class="sxs-lookup"><span data-stu-id="1bb40-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bb40-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bb40-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1bb40-111">Следуйте инструкциям по работе с Visual Studio (<xref:blazor/tooling>), чтобы создать проект Blazor Server с механизмом проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="1bb40-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="1bb40-112">Выбрав шаблон **Приложение Blazor Server** в диалоговом окне **Создание веб-приложения ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="1bb40-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="1bb40-113">Откроется диалоговое окно с тем же набором механизмов аутентификации, которые доступны для других проектов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb40-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="1bb40-114">**Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="1bb40-114">**No Authentication**</span></span>
* <span data-ttu-id="1bb40-115">**Учетные записи отдельных пользователей**. Учетные записи пользователей могут храниться:</span><span class="sxs-lookup"><span data-stu-id="1bb40-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="1bb40-116">Внутри приложения с помощью системы [Identity](xref:security/authentication/identity) в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb40-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="1bb40-117">в [Azure AD B2C](xref:security/authentication/azure-ad-b2c);</span><span class="sxs-lookup"><span data-stu-id="1bb40-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="1bb40-118">**рабочие или учебные учетные записи**.</span><span class="sxs-lookup"><span data-stu-id="1bb40-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="1bb40-119">**Проверка подлинности Windows**</span><span class="sxs-lookup"><span data-stu-id="1bb40-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bb40-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bb40-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1bb40-121">Следуйте инструкциям по работе с Visual Studio Code (<xref:blazor/tooling>), чтобы создать проект Blazor Server с механизмом проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="1bb40-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="1bb40-122">Допустимые значения аутентификации (`{AUTHENTICATION}`) перечислены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="1bb40-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="1bb40-123">Механизм аутентификации</span><span class="sxs-lookup"><span data-stu-id="1bb40-123">Authentication mechanism</span></span> | <span data-ttu-id="1bb40-124">Описание</span><span class="sxs-lookup"><span data-stu-id="1bb40-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="1bb40-125">`None` (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="1bb40-125">`None` (default)</span></span>         | <span data-ttu-id="1bb40-126">без аутентификации;</span><span class="sxs-lookup"><span data-stu-id="1bb40-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="1bb40-127">Пользователи, сохраненные в приложении с помощью ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="1bb40-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="1bb40-128">Пользователи, сохраненные в [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="1bb40-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="1bb40-129">Корпоративная аутентификация для отдельного клиента</span><span class="sxs-lookup"><span data-stu-id="1bb40-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="1bb40-130">Корпоративная аутентификация для нескольких клиентов</span><span class="sxs-lookup"><span data-stu-id="1bb40-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="1bb40-131">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="1bb40-131">Windows Authentication</span></span> |

<span data-ttu-id="1bb40-132">С помощью параметра `-o|--output` команда использует значение, указанное для заполнителя `{APP NAME}`, чтобы:</span><span class="sxs-lookup"><span data-stu-id="1bb40-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="1bb40-133">Создайте папку для проекта.</span><span class="sxs-lookup"><span data-stu-id="1bb40-133">Create a folder for the project.</span></span>
* <span data-ttu-id="1bb40-134">Дайте проекту имя.</span><span class="sxs-lookup"><span data-stu-id="1bb40-134">Name the project.</span></span>

<span data-ttu-id="1bb40-135">См. сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb40-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1bb40-136">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="1bb40-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1bb40-137">Следуйте инструкциям по работе с Visual Studio для Mac (<xref:blazor/tooling>).</span><span class="sxs-lookup"><span data-stu-id="1bb40-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="1bb40-138">На шаге **Настройка нового приложения Blazor Server** выберите **Индивидуальная проверка подлинности (в приложении)** из раскрывающегося списка **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="1bb40-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="1bb40-139">Приложение будет создано для отдельных пользователей, сохраненных в приложении с помощью ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="1bb40-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1bb40-140">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="1bb40-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1bb40-141">Создайте новый проект Blazor Server с механизмом проверки подлинности, используя следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="1bb40-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="1bb40-142">Допустимые значения аутентификации (`{AUTHENTICATION}`) перечислены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="1bb40-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="1bb40-143">Механизм аутентификации</span><span class="sxs-lookup"><span data-stu-id="1bb40-143">Authentication mechanism</span></span> | <span data-ttu-id="1bb40-144">Описание</span><span class="sxs-lookup"><span data-stu-id="1bb40-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="1bb40-145">`None` (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="1bb40-145">`None` (default)</span></span>         | <span data-ttu-id="1bb40-146">без аутентификации;</span><span class="sxs-lookup"><span data-stu-id="1bb40-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="1bb40-147">Пользователи, сохраненные в приложении с помощью ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="1bb40-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="1bb40-148">Пользователи, сохраненные в [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="1bb40-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="1bb40-149">Корпоративная аутентификация для отдельного клиента</span><span class="sxs-lookup"><span data-stu-id="1bb40-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="1bb40-150">Корпоративная аутентификация для нескольких клиентов</span><span class="sxs-lookup"><span data-stu-id="1bb40-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="1bb40-151">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="1bb40-151">Windows Authentication</span></span> |

<span data-ttu-id="1bb40-152">С помощью параметра `-o|--output` команда использует значение, указанное для заполнителя `{APP NAME}`, чтобы:</span><span class="sxs-lookup"><span data-stu-id="1bb40-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="1bb40-153">Создайте папку для проекта.</span><span class="sxs-lookup"><span data-stu-id="1bb40-153">Create a folder for the project.</span></span>
* <span data-ttu-id="1bb40-154">Дайте проекту имя.</span><span class="sxs-lookup"><span data-stu-id="1bb40-154">Name the project.</span></span>

<span data-ttu-id="1bb40-155">Дополнительные сведения</span><span class="sxs-lookup"><span data-stu-id="1bb40-155">For more information:</span></span>

* <span data-ttu-id="1bb40-156">Сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) см. в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb40-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="1bb40-157">Выполните команду help для шаблона Blazor Server (`blazorserver`) в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="1bb40-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="1bb40-158">Формирование шаблонов Identity</span><span class="sxs-lookup"><span data-stu-id="1bb40-158">Scaffold Identity</span></span>

<span data-ttu-id="1bb40-159">Сформируйте шаблоны Identity для проекта Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="1bb40-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="1bb40-160">[Без существующей авторизации](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="1bb40-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="1bb40-161">[С авторизацией](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="1bb40-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="azure-app-service-on-linux-with-no-locidentity-server"></a><span data-ttu-id="1bb40-162">Служба приложений Azure в Linux с сервером Identity</span><span class="sxs-lookup"><span data-stu-id="1bb40-162">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="1bb40-163">При развертывании в Службе приложений Azure в Linux с сервером Identity нужно указать издателя явно.</span><span class="sxs-lookup"><span data-stu-id="1bb40-163">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="1bb40-164">Для получения дополнительной информации см. <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span><span class="sxs-lookup"><span data-stu-id="1bb40-164">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1bb40-165">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1bb40-165">Additional resources</span></span>

* [<span data-ttu-id="1bb40-166">Краткое руководство. Добавление входа через Майкрософт в веб-приложение ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1bb40-166">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="1bb40-167">Краткое руководство. Защита веб-API ASP.NET Core с помощью платформы удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="1bb40-167">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="1bb40-168"><xref:host-and-deploy/proxy-load-balancer>: Включает рекомендации по следующим вопросам:</span><span class="sxs-lookup"><span data-stu-id="1bb40-168"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="1bb40-169">Использование ПО промежуточного слоя перенаправленных заголовков для сохранения сведений о схеме HTTPS на прокси-серверах и во внутренних сетях.</span><span class="sxs-lookup"><span data-stu-id="1bb40-169">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="1bb40-170">Дополнительные сценарии и варианты использования, включая ручную настройку схемы, изменение пути запроса для правильной маршрутизации запроса и перенаправление схемы запроса для обратных прокси-серверов Linux и обратных прокси-серверов, отличных от IIS.</span><span class="sxs-lookup"><span data-stu-id="1bb40-170">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
