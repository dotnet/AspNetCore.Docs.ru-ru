---
title: Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory B2C
author: guardrex
description: Узнайте, как защитить изолированное приложение ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory B2C.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 679f14642f4a611a5e65a7f472c68663fc1fb16b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97764713"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="db6ef-103">Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="db6ef-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="db6ef-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="db6ef-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="db6ef-105">В этой статье описывается, как создать [изолированное приложение Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), использующее [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) для аутентификации.</span><span class="sxs-lookup"><span data-stu-id="db6ef-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

<span data-ttu-id="db6ef-106">Создайте арендатор или укажите существующий арендатор B2C для приложения на портале Azure, выполнив инструкции из статьи [Создание арендатора AAD B2C (документация Azure)](/azure/active-directory-b2c/tutorial-create-tenant).</span><span class="sxs-lookup"><span data-stu-id="db6ef-106">Create a tenant or identify an existing B2C tenant for the app to use in the Azure portal by following the guidance in the [Create an AAD B2C tenant (Azure documentation)](/azure/active-directory-b2c/tutorial-create-tenant) article.</span></span> <span data-ttu-id="db6ef-107">Вернитесь к этой статье сразу после создания или идентификации клиента для использования.</span><span class="sxs-lookup"><span data-stu-id="db6ef-107">Return to this article immediately after creating or identifying a tenant to use.</span></span>

<span data-ttu-id="db6ef-108">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="db6ef-108">Record the following information:</span></span>

* <span data-ttu-id="db6ef-109">Экземпляр AAD B2C (например, `https://contoso.b2clogin.com/`, который включает косую черту в конце): Экземпляр — это схема и узел регистрации приложения Azure B2C, которую можно найти в окне **Конечные точки** на странице **Регистрации приложений** на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="db6ef-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash): The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>
* <span data-ttu-id="db6ef-110">Основной домен AAD B2C/домен издателя/домен клиента (например, `contoso.onmicrosoft.com`): домен доступен в качестве **домена издателя** в колонке **Фирменная символика** на портале Azure для зарегистрированного приложения.</span><span class="sxs-lookup"><span data-stu-id="db6ef-110">AAD B2C Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="db6ef-111">Зарегистрируйте приложение AAD B2C:</span><span class="sxs-lookup"><span data-stu-id="db6ef-111">Register an AAD B2C app:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="db6ef-112">В разделе **Azure Active Directory** > **Регистрация приложений** выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="db6ef-112">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="db6ef-113">Укажите **имя** приложения (например, **BlazorИзолированный AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="db6ef-113">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="db6ef-114">Для параметра **Поддерживаемые типы учетных записей** выберите вариант с использованием нескольких клиентов: **Учетные записи в любом каталоге организации или поставщике удостоверений. Выберите этот вариант для проверки подлинности в Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="db6ef-114">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="db6ef-115">Выберите в раскрывающемся списке **URI перенаправления** значение **Одностраничное приложение (SPA)** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="db6ef-115">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="db6ef-116">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="db6ef-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="db6ef-117">Если приложение выполняется на другом порте Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="db6ef-117">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="db6ef-118">Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="db6ef-118">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="db6ef-119">Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="db6ef-119">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="db6ef-120">В этом разделе появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="db6ef-120">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="db6ef-121">Убедитесь, что в разделе **Разрешения** установлен флажок **Предоставьте согласие администратора для разрешений openid и offline_access**.</span><span class="sxs-lookup"><span data-stu-id="db6ef-121">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="db6ef-122">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="db6ef-122">Select **Register**.</span></span>

<span data-ttu-id="db6ef-123">Запишите идентификатор приложения (клиента) (например, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="db6ef-123">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="db6ef-124">В разделе **Аутентификация** > **Конфигурации платформ** > **Одностраничное приложение (SPA)** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="db6ef-124">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="db6ef-125">Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="db6ef-125">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="db6ef-126">В разделе **Неявное предоставление разрешения** убедитесь, что флажки **Маркеры доступа** и **Токены идентификаторов** **не** установлены.</span><span class="sxs-lookup"><span data-stu-id="db6ef-126">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="db6ef-127">Для остальных параметров можно оставить значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="db6ef-127">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="db6ef-128">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="db6ef-128">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="db6ef-129">В разделе **Azure Active Directory** > **Регистрация приложений** выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="db6ef-129">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="db6ef-130">Укажите **имя** приложения (например, **BlazorИзолированный AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="db6ef-130">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="db6ef-131">Для параметра **Поддерживаемые типы учетных записей** выберите вариант с использованием нескольких клиентов: **Учетные записи в любом каталоге организации или поставщике удостоверений. Выберите этот вариант для проверки подлинности в Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="db6ef-131">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="db6ef-132">В раскрывающемся списке **URI перенаправления** оставьте значение **Интернет** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="db6ef-132">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="db6ef-133">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="db6ef-133">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="db6ef-134">Если приложение выполняется на другом порте Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="db6ef-134">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="db6ef-135">Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="db6ef-135">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="db6ef-136">Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="db6ef-136">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="db6ef-137">В этом разделе появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="db6ef-137">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="db6ef-138">Убедитесь, что в разделе **Разрешения** установлен флажок **Предоставьте согласие администратора для разрешений openid и offline_access**.</span><span class="sxs-lookup"><span data-stu-id="db6ef-138">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="db6ef-139">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="db6ef-139">Select **Register**.</span></span>

<span data-ttu-id="db6ef-140">Запишите идентификатор приложения (клиента) (например, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="db6ef-140">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="db6ef-141">В разделе **Аутентификация** > **Конфигурации платформ** > **Интернет** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="db6ef-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="db6ef-142">Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="db6ef-142">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="db6ef-143">В разделе **Неявное предоставление разрешения** установите флажки **Маркеры доступа** и **Токен идентификатора**.</span><span class="sxs-lookup"><span data-stu-id="db6ef-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="db6ef-144">Для остальных параметров можно оставить значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="db6ef-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="db6ef-145">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="db6ef-145">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="db6ef-146">В разделе **Главная** > **Azure AD B2C** > **Потоки пользователя** выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="db6ef-146">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="db6ef-147">Создайте поток пользователя для регистрации и входа в систему</span><span class="sxs-lookup"><span data-stu-id="db6ef-147">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="db6ef-148">Чтобы заполнить `context.User.Identity.Name` в компоненте `LoginDisplay` (`Shared/LoginDisplay.razor`), выберите хотя бы атрибут пользователя **Утверждения приложения** > **Отображаемое имя**.</span><span class="sxs-lookup"><span data-stu-id="db6ef-148">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="db6ef-149">Запишите имя потока пользователя для регистрации и входа, созданное для приложения (например, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="db6ef-149">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="db6ef-150">В пустой папке замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="db6ef-150">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="db6ef-151">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="db6ef-151">Placeholder</span></span>                   | <span data-ttu-id="db6ef-152">Название на портале Azure</span><span class="sxs-lookup"><span data-stu-id="db6ef-152">Azure portal name</span></span>               | <span data-ttu-id="db6ef-153">Пример</span><span class="sxs-lookup"><span data-stu-id="db6ef-153">Example</span></span>                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="db6ef-154">Экземпляр</span><span class="sxs-lookup"><span data-stu-id="db6ef-154">Instance</span></span>                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | <span data-ttu-id="db6ef-155">Идентификатор приложения (клиента)</span><span class="sxs-lookup"><span data-stu-id="db6ef-155">Application (client) ID</span></span>         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="db6ef-156">Поток регистрации/входа пользователей</span><span class="sxs-lookup"><span data-stu-id="db6ef-156">Sign-up/sign-in user flow</span></span>       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="db6ef-157">Основной домен/домен издателя/домен клиента</span><span class="sxs-lookup"><span data-stu-id="db6ef-157">Primary/Publisher/Tenant domain</span></span> | `contoso.onmicrosoft.com`              |

<span data-ttu-id="db6ef-158">Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.</span><span class="sxs-lookup"><span data-stu-id="db6ef-158">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="db6ef-159">На портале Azure **URI перенаправления** в конфигурации платформы приложений настроен для использования порта 5001 для приложений, которые выполняются на сервере Kestrel с параметрами по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="db6ef-159">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="db6ef-160">Если приложение выполняется на случайном порту IIS Express, порт для приложения указан в свойствах приложения на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="db6ef-160">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="db6ef-161">Если порт не был настроен ранее с помощью известного порта приложения, вернитесь к регистрации приложения на портале Azure и обновите URI перенаправления, указав правильный порт.</span><span class="sxs-lookup"><span data-stu-id="db6ef-161">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="db6ef-162">После создания приложения вы сможете:</span><span class="sxs-lookup"><span data-stu-id="db6ef-162">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="db6ef-163">Входить в приложение, используя учетную запись пользователя AAD.</span><span class="sxs-lookup"><span data-stu-id="db6ef-163">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="db6ef-164">Запрашивать маркеры доступа для API Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="db6ef-164">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="db6ef-165">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="db6ef-165">For more information, see:</span></span>
  * [<span data-ttu-id="db6ef-166">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="db6ef-166">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="db6ef-167">[Краткое руководство. Настройка приложения для предоставления интерфейсов веб-API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="db6ef-167">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="db6ef-168">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="db6ef-168">Authentication package</span></span>

<span data-ttu-id="db6ef-169">Когда приложение создается для использования отдельной учетной записи B2C (`IndividualB2C`), оно автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span><span class="sxs-lookup"><span data-stu-id="db6ef-169">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="db6ef-170">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="db6ef-170">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="db6ef-171">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="db6ef-171">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="db6ef-172">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="db6ef-172">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="db6ef-173">Пакет [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) транзитивно добавляет пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в приложение.</span><span class="sxs-lookup"><span data-stu-id="db6ef-173">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="db6ef-174">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="db6ef-174">Authentication service support</span></span>

<span data-ttu-id="db6ef-175">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>, предоставляемого в пакете [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="db6ef-175">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="db6ef-176">Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком Identity (IP).</span><span class="sxs-lookup"><span data-stu-id="db6ef-176">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="db6ef-177">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="db6ef-177">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="db6ef-178">Метод <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="db6ef-178">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="db6ef-179">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="db6ef-179">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="db6ef-180">Конфигурация предоставлена в файле `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="db6ef-180">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="db6ef-181">Пример.</span><span class="sxs-lookup"><span data-stu-id="db6ef-181">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="db6ef-182">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="db6ef-182">Access token scopes</span></span>

<span data-ttu-id="db6ef-183">Шаблон Blazor WebAssembly не обеспечивает автоматическую настройку приложения для запроса маркера доступа для защищенного API.</span><span class="sxs-lookup"><span data-stu-id="db6ef-183">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="db6ef-184">Чтобы настроить маркер доступа как часть потока входа, добавьте область в области маркера доступа по умолчанию <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="db6ef-184">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="db6ef-185">Укажите дополнительные области с помощью `AdditionalScopesToConsent`:</span><span class="sxs-lookup"><span data-stu-id="db6ef-185">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="db6ef-186">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии*:</span><span class="sxs-lookup"><span data-stu-id="db6ef-186">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="db6ef-187">Запрашивание дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="db6ef-187">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="db6ef-188">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="db6ef-188">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="db6ef-189">Режим входа</span><span class="sxs-lookup"><span data-stu-id="db6ef-189">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="db6ef-190">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="db6ef-190">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="db6ef-191">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="db6ef-191">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="db6ef-192">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="db6ef-192">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="db6ef-193">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="db6ef-193">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="db6ef-194">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="db6ef-194">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="db6ef-195">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="db6ef-195">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="db6ef-196">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="db6ef-196">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="db6ef-197">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="db6ef-197">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* <span data-ttu-id="db6ef-198">[Учебник. Создание клиента Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant).</span><span class="sxs-lookup"><span data-stu-id="db6ef-198">[Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant)</span></span>
* [<span data-ttu-id="db6ef-199">Руководство по Зарегистрировать приложение в Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="db6ef-199">Tutorial: Register an application in Azure Active Directory B2C</span></span>](/azure/active-directory-b2c/tutorial-register-applications)
* [<span data-ttu-id="db6ef-200">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="db6ef-200">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
