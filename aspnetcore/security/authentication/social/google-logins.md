---
title: Настройка внешнего входа Google в ASP.NET Core
author: rick-anderson
description: В этом руководстве демонстрируется Интеграция проверки подлинности пользователя учетной записи Google с существующим ASP.NET Core приложением.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/18/2021
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
uid: security/authentication/google-logins
ms.openlocfilehash: 181ce87e8085839e0fcc0d77010c588ef7a290b1
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110135"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="699c6-103">Настройка внешнего входа Google в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="699c6-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="699c6-104">Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="699c6-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="699c6-105">В этом руководстве показано, как разрешить пользователям входить в систему с помощью учетной записи Google, используя проект ASP.NET Core 3,0, созданный на [предыдущей странице](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="699c6-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="699c6-106">Создание проекта консоли Google API и идентификатора клиента</span><span class="sxs-lookup"><span data-stu-id="699c6-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="699c6-107">Добавьте в приложение пакет NuGet [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) .</span><span class="sxs-lookup"><span data-stu-id="699c6-107">Add the [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) NuGet package to the app.</span></span>
* <span data-ttu-id="699c6-108">Следуйте указаниям в статье [Интеграция google Sign-In в веб-приложение](https://developers.google.com/identity/sign-in/web/sign-in) (документация Google).</span><span class="sxs-lookup"><span data-stu-id="699c6-108">Follow the guidance in [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/sign-in) (Google documentation).</span></span>
* <span data-ttu-id="699c6-109">На странице **учетные данные** [консоли Google](https://console.developers.google.com/apis/credentials)выберите **создать учетные данные**  >  **идентификатор клиента OAuth**.</span><span class="sxs-lookup"><span data-stu-id="699c6-109">In the **Credentials** page of the [Google console](https://console.developers.google.com/apis/credentials), select **CREATE CREDENTIALS** > **OAuth client ID**.</span></span>
* <span data-ttu-id="699c6-110">В диалоговом окне **Тип приложения** выберите **веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="699c6-110">In the **Application type** dialog, select **Web application**.</span></span> <span data-ttu-id="699c6-111">Укажите **имя** приложения.</span><span class="sxs-lookup"><span data-stu-id="699c6-111">Provide a **Name** for the app.</span></span>
* <span data-ttu-id="699c6-112">В разделе " **зарегистрированные URI перенаправления** " выберите **Добавить URI** , чтобы задать URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="699c6-112">In the **Authorized redirect URIs** section, select **ADD URI** to set the redirect URI.</span></span> <span data-ttu-id="699c6-113">Пример URI перенаправления: `https://localhost:{PORT}/signin-google` , где `{PORT}` заполнитель является портом приложения.</span><span class="sxs-lookup"><span data-stu-id="699c6-113">Example redirect URI: `https://localhost:{PORT}/signin-google`, where the `{PORT}` placeholder is the app's port.</span></span>
* <span data-ttu-id="699c6-114">Нажмите кнопку **создать** .</span><span class="sxs-lookup"><span data-stu-id="699c6-114">Select the **CREATE** button.</span></span>
* <span data-ttu-id="699c6-115">Сохраните **идентификатор клиента** и **секрет клиента** для использования в конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="699c6-115">Save the **Client ID** and **Client Secret** for use in the app's configuration.</span></span>
* <span data-ttu-id="699c6-116">При развертывании сайта выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="699c6-116">When deploying the site, either:</span></span>
  * <span data-ttu-id="699c6-117">Обновите URI перенаправления приложения в **консоли Google** до развернутого URI перенаправления приложения.</span><span class="sxs-lookup"><span data-stu-id="699c6-117">Update the app's redirect URI in the **Google Console** to the app's deployed redirect URI.</span></span>
  * <span data-ttu-id="699c6-118">Создайте новую регистрацию Google API в **консоли Google** для рабочего приложения с помощью его URI перенаправления рабочей среды.</span><span class="sxs-lookup"><span data-stu-id="699c6-118">Create a new Google API registration in the **Google Console** for the production app with its production redirect URI.</span></span>

## <a name="store-the-google-client-id-and-secret"></a><span data-ttu-id="699c6-119">Хранение идентификатора и секрета клиента Google</span><span class="sxs-lookup"><span data-stu-id="699c6-119">Store the Google client ID and secret</span></span>

<span data-ttu-id="699c6-120">Храните конфиденциальные параметры, такие как идентификатор клиента Google и секретные значения, с помощью [диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="699c6-120">Store sensitive settings such as the Google client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="699c6-121">Для этого примера выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="699c6-121">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="699c6-122">Инициализируйте проект для хранения секретных данных согласно инструкциям в разделе [Включение секретного хранилища](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="699c6-122">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="699c6-123">Храните конфиденциальные параметры в локальном хранилище секретов с секретными ключами `Authentication:Google:ClientId` и `Authentication:Google:ClientSecret` :</span><span class="sxs-lookup"><span data-stu-id="699c6-123">Store the sensitive settings in the local secret store with the secret keys `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="699c6-124">Вы можете управлять учетными данными и использованием API в [консоли API](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="699c6-124">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="699c6-125">Настройка проверки подлинности Google</span><span class="sxs-lookup"><span data-stu-id="699c6-125">Configure Google authentication</span></span>

<span data-ttu-id="699c6-126">Добавьте службу Google в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="699c6-126">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="699c6-127">Вход с учетными данными Google</span><span class="sxs-lookup"><span data-stu-id="699c6-127">Sign in with Google</span></span>

* <span data-ttu-id="699c6-128">Запустите приложение и нажмите кнопку **войти**.</span><span class="sxs-lookup"><span data-stu-id="699c6-128">Run the app and click **Log in**.</span></span> <span data-ttu-id="699c6-129">Появится возможность войти в систему с помощью Google.</span><span class="sxs-lookup"><span data-stu-id="699c6-129">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="699c6-130">Нажмите кнопку **Google** , которая перенаправляет на Google для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="699c6-130">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="699c6-131">После ввода учетных данных Google вы будете перенаправлены обратно на веб-сайт.</span><span class="sxs-lookup"><span data-stu-id="699c6-131">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="699c6-132"><xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Дополнительные сведения о параметрах конфигурации, поддерживаемых проверкой подлинности Google, см. в справочнике по API.</span><span class="sxs-lookup"><span data-stu-id="699c6-132">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="699c6-133">Это можно использовать для запроса различных сведений о пользователе.</span><span class="sxs-lookup"><span data-stu-id="699c6-133">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="699c6-134">Изменение URI обратного вызова по умолчанию</span><span class="sxs-lookup"><span data-stu-id="699c6-134">Change the default callback URI</span></span>

<span data-ttu-id="699c6-135">Сегмент URI `/signin-google` задается в качестве обратного вызова по умолчанию для поставщика проверки подлинности Google.</span><span class="sxs-lookup"><span data-stu-id="699c6-135">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="699c6-136">Вы можете изменить URI обратного вызова по умолчанию при настройке по промежуточного слоя проверки подлинности Google с помощью свойства унаследовано <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType> ) <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> класса.</span><span class="sxs-lookup"><span data-stu-id="699c6-136">You can change the default callback URI while configuring the Google authentication middleware via the inherited <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType>) property of the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="699c6-137">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="699c6-137">Troubleshooting</span></span>

* <span data-ttu-id="699c6-138">Если вход не работает и вы не получаете никаких ошибок, переключитесь в режим разработки, чтобы упростить отладку.</span><span class="sxs-lookup"><span data-stu-id="699c6-138">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="699c6-139">Если Identity не настроен с помощью вызова `services.AddIdentity` в `ConfigureServices` , попытка проверки подлинности результатов в *ArgumentException: необходимо указать параметр "сигнинсчеме"*.</span><span class="sxs-lookup"><span data-stu-id="699c6-139">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="699c6-140">Шаблон проекта, используемый в этом руководстве, гарантирует, что это будет сделано.</span><span class="sxs-lookup"><span data-stu-id="699c6-140">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="699c6-141">Если база данных сайта не была создана путем применения первоначальной миграции, то при обработке ошибки запроса возникнет *Ошибка операции с базой данных* .</span><span class="sxs-lookup"><span data-stu-id="699c6-141">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="699c6-142">Выберите **Применить миграции** , чтобы создать базу данных, и обновите страницу, чтобы продолжить работу после ошибки.</span><span class="sxs-lookup"><span data-stu-id="699c6-142">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="699c6-143">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="699c6-143">Next steps</span></span>

* <span data-ttu-id="699c6-144">В этой статье показано, как можно пройти проверку подлинности в Google.</span><span class="sxs-lookup"><span data-stu-id="699c6-144">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="699c6-145">Аналогичный подход можно использовать для проверки подлинности с другими поставщиками, перечисленными на [предыдущей странице](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="699c6-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="699c6-146">После публикации приложения в Azure сбросьте его `ClientSecret` в консоли Google API.</span><span class="sxs-lookup"><span data-stu-id="699c6-146">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="699c6-147">Задайте `Authentication:Google:ClientId` `Authentication:Google:ClientSecret` Параметры приложения и в портал Azure.</span><span class="sxs-lookup"><span data-stu-id="699c6-147">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="699c6-148">Система конфигурации настроена на чтение ключей из переменных среды.</span><span class="sxs-lookup"><span data-stu-id="699c6-148">The configuration system is set up to read keys from environment variables.</span></span>
