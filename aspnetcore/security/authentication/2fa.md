---
title: Двухфакторная проверка подлинности с помощью SMS в ASP.NET Core
author: rick-anderson
description: Узнайте, как настроить двухфакторную проверку подлинности (2FA) с помощью приложения ASP.NET Core.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
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
uid: security/authentication/2fa
ms.openlocfilehash: 1f77f3f4b7e9dd558e9869992e2f1f4d185e5b10
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586856"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="f1d3b-103">Двухфакторная проверка подлинности с помощью SMS в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f1d3b-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="f1d3b-104">[Рик Андерсон (](https://twitter.com/RickAndMSFT) и [Швейцарский разработчики](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="f1d3b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="f1d3b-105">Приложения проверки подлинности с двумя факторами проверки подлинности (2FA), использующие алгоритм одноразового пароля (TOTP) на основе времени, являются рекомендуемым подходом к 2FA.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="f1d3b-106">2FA с использованием TOTP является предпочтительным для SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="f1d3b-107">Дополнительные сведения см. [в статье Включение создания QR-кода для приложений TOTP Authenticator в ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) для ASP.NET Core 2,0 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="f1d3b-108">В этом руководстве показано, как настроить двухфакторную проверку подлинности (2FA) с помощью SMS.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="f1d3b-109">Инструкции предоставляются для [twilio](https://www.twilio.com/) и [аспсмс](https://www.aspsms.com/asp.net/identity/core/testcredits/), но можно использовать любой другой поставщик SMS.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="f1d3b-110">Перед началом работы с этим руководством мы рекомендуем завершить [Подтверждение учетной записи и восстановление пароля](xref:security/authentication/accconfirm) .</span><span class="sxs-lookup"><span data-stu-id="f1d3b-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="f1d3b-111">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/2fa/sample/Web2FA).</span><span class="sxs-lookup"><span data-stu-id="f1d3b-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="f1d3b-112">[Как скачать](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="f1d3b-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="f1d3b-113">Создание проекта ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f1d3b-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="f1d3b-114">Создайте веб-приложение ASP.NET Core с именем `Web2FA` с учетными записями отдельных пользователей.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="f1d3b-115">Следуйте инструкциям в, <xref:security/enforcing-ssl> чтобы настроить и требовать протокол HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="f1d3b-116">Создание учетной записи SMS</span><span class="sxs-lookup"><span data-stu-id="f1d3b-116">Create an SMS account</span></span>

<span data-ttu-id="f1d3b-117">Создайте учетную запись SMS, например из [twilio](https://www.twilio.com/) или [аспсмс](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span><span class="sxs-lookup"><span data-stu-id="f1d3b-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="f1d3b-118">Запишите учетные данные для проверки подлинности (для twilio: accountSid и authToken, для АСПСМС: Userkey и Password).</span><span class="sxs-lookup"><span data-stu-id="f1d3b-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="f1d3b-119">Определение учетных данных поставщика SMS</span><span class="sxs-lookup"><span data-stu-id="f1d3b-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="f1d3b-120">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="f1d3b-120">**Twilio:**</span></span>

<span data-ttu-id="f1d3b-121">На вкладке Панель мониторинга учетной записи Twilio скопируйте **SID учетной записи** и **токен проверки подлинности**.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="f1d3b-122">**АСПСМС:**</span><span class="sxs-lookup"><span data-stu-id="f1d3b-122">**ASPSMS:**</span></span>

<span data-ttu-id="f1d3b-123">В параметрах учетной записи перейдите по адресу **Userkey** и скопируйте его вместе с **паролем**.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="f1d3b-124">Позже мы будем хранить эти значения в с помощью средства Secret-Manager в ключах `SMSAccountIdentification` и `SMSAccountPassword` .</span><span class="sxs-lookup"><span data-stu-id="f1d3b-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="f1d3b-125">Указание SenderID или инициатора</span><span class="sxs-lookup"><span data-stu-id="f1d3b-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="f1d3b-126">**Twilio:** На вкладке числа скопируйте **номер телефона** Twilio.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="f1d3b-127">**Аспсмс:** В меню Источник разблокировки Разблокируйте один или несколько источник или выберите буквенно-цифровой инициатор (не поддерживается всеми сетями).</span><span class="sxs-lookup"><span data-stu-id="f1d3b-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="f1d3b-128">Позже мы будем хранить это значение с помощью средства Secret-Manager в рамках ключа `SMSAccountFrom` .</span><span class="sxs-lookup"><span data-stu-id="f1d3b-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="f1d3b-129">Укажите учетные данные для службы SMS</span><span class="sxs-lookup"><span data-stu-id="f1d3b-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="f1d3b-130">Мы будем использовать [шаблон параметров](xref:fundamentals/configuration/options) для доступа к учетной записи пользователя и параметрам ключа.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="f1d3b-131">Создайте класс для выборки безопасного ключа SMS.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="f1d3b-132">В этом примере `SMSoptions` класс создается в файле *Services/смсоптионс. CS* .</span><span class="sxs-lookup"><span data-stu-id="f1d3b-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="f1d3b-133">Задайте `SMSAccountIdentification` `SMSAccountPassword` и `SMSAccountFrom` с помощью [средства Secret-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="f1d3b-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="f1d3b-134">Пример:</span><span class="sxs-lookup"><span data-stu-id="f1d3b-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="f1d3b-135">Добавьте пакет NuGet для поставщика SMS.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="f1d3b-136">В консоли диспетчера пакетов (PMC) выполните:</span><span class="sxs-lookup"><span data-stu-id="f1d3b-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="f1d3b-137">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="f1d3b-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="f1d3b-138">**АСПСМС:**</span><span class="sxs-lookup"><span data-stu-id="f1d3b-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="f1d3b-139">Добавьте код в файл *Services/мессажесервицес. CS* , чтобы включить SMS.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="f1d3b-140">Используйте раздел Twilio или АСПСМС.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="f1d3b-141">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="f1d3b-141">**Twilio:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="f1d3b-142">**АСПСМС:**</span><span class="sxs-lookup"><span data-stu-id="f1d3b-142">**ASPSMS:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="f1d3b-143">Настройка запуска для использования `SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="f1d3b-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="f1d3b-144">Добавьте в `SMSoptions` контейнер службы в `ConfigureServices` методе в *Startup.CS*:</span><span class="sxs-lookup"><span data-stu-id="f1d3b-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="f1d3b-145">Включение двухфакторной проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="f1d3b-145">Enable two-factor authentication</span></span>

<span data-ttu-id="f1d3b-146">Откройте файл представления views */Manage/index. cshtml* Razor и удалите символы комментария (поэтому разметка не записывается).</span><span class="sxs-lookup"><span data-stu-id="f1d3b-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="f1d3b-147">Вход с использованием двухфакторной проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="f1d3b-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="f1d3b-148">Запуск приложения и регистрация нового пользователя</span><span class="sxs-lookup"><span data-stu-id="f1d3b-148">Run the app and register a new user</span></span>

![Открытие представления регистрации веб-приложения в Microsoft ребро](2fa/_static/login2fa1.png)

* <span data-ttu-id="f1d3b-150">Коснитесь имени пользователя, которое активирует `Index` метод действия в управлении контроллером.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="f1d3b-151">Затем коснитесь ссылки **Добавить** номер телефона.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-151">Then tap the phone number **Add** link.</span></span>

![Управление представлением — Коснитесь ссылки "Добавить"](2fa/_static/login2fa2.png)

* <span data-ttu-id="f1d3b-153">Добавьте номер телефона, который будет получать код проверки, и нажмите кнопку **Отправить проверочный код**.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-153">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![Страница "Добавление номера телефона"](2fa/_static/login2fa3.png)

* <span data-ttu-id="f1d3b-155">Будет получено текстовое сообщение с кодом проверки.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="f1d3b-156">Введите его и нажмите кнопку **Отправить** .</span><span class="sxs-lookup"><span data-stu-id="f1d3b-156">Enter it and tap **Submit**</span></span>

![Страница проверки номера телефона](2fa/_static/login2fa4.png)

<span data-ttu-id="f1d3b-158">Если вы не получаете текстовое сообщение, см. страницу twilio log.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="f1d3b-159">Представление "Управление" показывает, что ваш номер телефона успешно добавлен.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-159">The Manage view shows your phone number was added successfully.</span></span>

![Управление представлением — номер телефона успешно добавлен](2fa/_static/login2fa5.png)

* <span data-ttu-id="f1d3b-161">Коснитесь кнопки **включить** , чтобы включить двухфакторную проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-161">Tap **Enable** to enable two-factor authentication.</span></span>

![Управление представлением — включение двухфакторной проверки подлинности](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="f1d3b-163">Проверить двухфакторную проверку подлинности</span><span class="sxs-lookup"><span data-stu-id="f1d3b-163">Test two-factor authentication</span></span>

* <span data-ttu-id="f1d3b-164">Выйдите из системы.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-164">Log off.</span></span>

* <span data-ttu-id="f1d3b-165">Войдите в систему.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-165">Log in.</span></span>

* <span data-ttu-id="f1d3b-166">Учетная запись пользователя включила двухфакторную проверку подлинности, поэтому необходимо указать второй фактор проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="f1d3b-167">В этом руководстве вы включили проверку телефона.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="f1d3b-168">Встроенные шаблоны также позволяют настроить электронную почту в качестве второго фактора.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="f1d3b-169">Для проверки подлинности, например QR-кодов, можно настроить дополнительные секунды.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="f1d3b-170">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-170">Tap **Submit**.</span></span>

![Отправить представление кода проверки](2fa/_static/login2fa7.png)

* <span data-ttu-id="f1d3b-172">Введите код, полученный в сообщении SMS.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="f1d3b-173">Если установить флажок **Запомнить этот браузер** , вам не нужно будет использовать 2FA для входа в систему при использовании того же устройства и браузера.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="f1d3b-174">Включение 2FA и нажатие кнопки " **Запомнить" Этот браузер** обеспечит надежную защиту 2FA от злоумышленников, пытающихся получить доступ к вашей учетной записи, если у них нет доступа к вашему устройству.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="f1d3b-175">Это можно сделать на любом частном устройстве, которое вы регулярно используете.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="f1d3b-176">Установив флажок  **Запомнить этот браузер**, вы получите дополнительную защиту 2FA от устройств, которые вы не используете регулярно, и вы получите удобный способ, чтобы не выполнять переход на 2FA на своих устройствах.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-176">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![Проверить представление](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="f1d3b-178">Блокировка учетной записи для защиты от атак методом подбора</span><span class="sxs-lookup"><span data-stu-id="f1d3b-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="f1d3b-179">Блокировка учетной записи рекомендуется для 2FA.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="f1d3b-180">Когда пользователь входит в локальную учетную запись или учетную запись социальных сетей, каждая попытка неудачной попытки в 2FA сохраняется.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="f1d3b-181">Если достигнуто максимальное число неудачных попыток доступа, пользователь блокируется (по умолчанию — 5 минут после 5 неудачных попыток доступа).</span><span class="sxs-lookup"><span data-stu-id="f1d3b-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="f1d3b-182">Успешная проверка подлинности сбрасывает количество неудачных попыток доступа и сбрасывает часы.</span><span class="sxs-lookup"><span data-stu-id="f1d3b-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="f1d3b-183">Максимальное количество неудачных попыток доступа и время блокировки можно задать с помощью [максфаиледакцессаттемптс](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) и [дефаултлоккауттимеспан](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span><span class="sxs-lookup"><span data-stu-id="f1d3b-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="f1d3b-184">Следующая настройка блокировки учетной записи настраивается на 10 минут после 10 неудачных попыток доступа:</span><span class="sxs-lookup"><span data-stu-id="f1d3b-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="f1d3b-185">Убедитесь, что [пассвордсигнинасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) задает `lockoutOnFailure` `true` :</span><span class="sxs-lookup"><span data-stu-id="f1d3b-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
