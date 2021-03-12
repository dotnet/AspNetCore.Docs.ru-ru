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
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>Двухфакторная проверка подлинности с помощью SMS в ASP.NET Core

[Рик Андерсон (](https://twitter.com/RickAndMSFT) и [Швейцарский разработчики](https://github.com/Swiss-Devs)

>[!WARNING]
> Приложения проверки подлинности с двумя факторами проверки подлинности (2FA), использующие алгоритм одноразового пароля (TOTP) на основе времени, являются рекомендуемым подходом к 2FA. 2FA с использованием TOTP является предпочтительным для SMS 2FA. Дополнительные сведения см. [в статье Включение создания QR-кода для приложений TOTP Authenticator в ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) для ASP.NET Core 2,0 и более поздних версий.

В этом руководстве показано, как настроить двухфакторную проверку подлинности (2FA) с помощью SMS. Инструкции предоставляются для [twilio](https://www.twilio.com/) и [аспсмс](https://www.aspsms.com/asp.net/identity/core/testcredits/), но можно использовать любой другой поставщик SMS. Перед началом работы с этим руководством мы рекомендуем завершить [Подтверждение учетной записи и восстановление пароля](xref:security/authentication/accconfirm) .

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/2fa/sample/Web2FA). [Как скачать](xref:index#how-to-download-a-sample).

## <a name="create-a-new-aspnet-core-project"></a>Создание проекта ASP.NET Core

Создайте веб-приложение ASP.NET Core с именем `Web2FA` с учетными записями отдельных пользователей. Следуйте инструкциям в, <xref:security/enforcing-ssl> чтобы настроить и требовать протокол HTTPS.

### <a name="create-an-sms-account"></a>Создание учетной записи SMS

Создайте учетную запись SMS, например из [twilio](https://www.twilio.com/) или [аспсмс](https://www.aspsms.com/asp.net/identity/core/testcredits/). Запишите учетные данные для проверки подлинности (для twilio: accountSid и authToken, для АСПСМС: Userkey и Password).

#### <a name="figuring-out-sms-provider-credentials"></a>Определение учетных данных поставщика SMS

**Twilio**

На вкладке Панель мониторинга учетной записи Twilio скопируйте **SID учетной записи** и **токен проверки подлинности**.

**АСПСМС:**

В параметрах учетной записи перейдите по адресу **Userkey** и скопируйте его вместе с **паролем**.

Позже мы будем хранить эти значения в с помощью средства Secret-Manager в ключах `SMSAccountIdentification` и `SMSAccountPassword` .

#### <a name="specifying-senderid--originator"></a>Указание SenderID или инициатора

**Twilio:** На вкладке числа скопируйте **номер телефона** Twilio.

**Аспсмс:** В меню Источник разблокировки Разблокируйте один или несколько источник или выберите буквенно-цифровой инициатор (не поддерживается всеми сетями).

Позже мы будем хранить это значение с помощью средства Secret-Manager в рамках ключа `SMSAccountFrom` .

### <a name="provide-credentials-for-the-sms-service"></a>Укажите учетные данные для службы SMS

Мы будем использовать [шаблон параметров](xref:fundamentals/configuration/options) для доступа к учетной записи пользователя и параметрам ключа.

* Создайте класс для выборки безопасного ключа SMS. В этом примере `SMSoptions` класс создается в файле *Services/смсоптионс. CS* .

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

Задайте `SMSAccountIdentification` `SMSAccountPassword` и `SMSAccountFrom` с помощью [средства Secret-Manager](xref:security/app-secrets). Пример:

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* Добавьте пакет NuGet для поставщика SMS. В консоли диспетчера пакетов (PMC) выполните:

**Twilio**

`Install-Package Twilio`

**АСПСМС:**

`Install-Package ASPSMS`

* Добавьте код в файл *Services/мессажесервицес. CS* , чтобы включить SMS. Используйте раздел Twilio или АСПСМС.

**Twilio**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**АСПСМС:**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>Настройка запуска для использования `SMSoptions`

Добавьте в `SMSoptions` контейнер службы в `ConfigureServices` методе в *Startup.CS*:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>Включение двухфакторной проверки подлинности

Откройте файл представления views */Manage/index. cshtml* Razor и удалите символы комментария (поэтому разметка не записывается).

## <a name="log-in-with-two-factor-authentication"></a>Вход с использованием двухфакторной проверки подлинности

* Запуск приложения и регистрация нового пользователя

![Открытие представления регистрации веб-приложения в Microsoft ребро](2fa/_static/login2fa1.png)

* Коснитесь имени пользователя, которое активирует `Index` метод действия в управлении контроллером. Затем коснитесь ссылки **Добавить** номер телефона.

![Управление представлением — Коснитесь ссылки "Добавить"](2fa/_static/login2fa2.png)

* Добавьте номер телефона, который будет получать код проверки, и нажмите кнопку **Отправить проверочный код**.

![Страница "Добавление номера телефона"](2fa/_static/login2fa3.png)

* Будет получено текстовое сообщение с кодом проверки. Введите его и нажмите кнопку **Отправить** .

![Страница проверки номера телефона](2fa/_static/login2fa4.png)

Если вы не получаете текстовое сообщение, см. страницу twilio log.

* Представление "Управление" показывает, что ваш номер телефона успешно добавлен.

![Управление представлением — номер телефона успешно добавлен](2fa/_static/login2fa5.png)

* Коснитесь кнопки **включить** , чтобы включить двухфакторную проверку подлинности.

![Управление представлением — включение двухфакторной проверки подлинности](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>Проверить двухфакторную проверку подлинности

* Выйдите из системы.

* Войдите в систему.

* Учетная запись пользователя включила двухфакторную проверку подлинности, поэтому необходимо указать второй фактор проверки подлинности. В этом руководстве вы включили проверку телефона. Встроенные шаблоны также позволяют настроить электронную почту в качестве второго фактора. Для проверки подлинности, например QR-кодов, можно настроить дополнительные секунды. Нажмите кнопку **Отправить**.

![Отправить представление кода проверки](2fa/_static/login2fa7.png)

* Введите код, полученный в сообщении SMS.

* Если установить флажок **Запомнить этот браузер** , вам не нужно будет использовать 2FA для входа в систему при использовании того же устройства и браузера. Включение 2FA и нажатие кнопки " **Запомнить" Этот браузер** обеспечит надежную защиту 2FA от злоумышленников, пытающихся получить доступ к вашей учетной записи, если у них нет доступа к вашему устройству. Это можно сделать на любом частном устройстве, которое вы регулярно используете. Установив флажок  **Запомнить этот браузер**, вы получите дополнительную защиту 2FA от устройств, которые вы не используете регулярно, и вы получите удобный способ, чтобы не выполнять переход на 2FA на своих устройствах.

![Проверить представление](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>Блокировка учетной записи для защиты от атак методом подбора

Блокировка учетной записи рекомендуется для 2FA. Когда пользователь входит в локальную учетную запись или учетную запись социальных сетей, каждая попытка неудачной попытки в 2FA сохраняется. Если достигнуто максимальное число неудачных попыток доступа, пользователь блокируется (по умолчанию — 5 минут после 5 неудачных попыток доступа). Успешная проверка подлинности сбрасывает количество неудачных попыток доступа и сбрасывает часы. Максимальное количество неудачных попыток доступа и время блокировки можно задать с помощью [максфаиледакцессаттемптс](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) и [дефаултлоккауттимеспан](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan). Следующая настройка блокировки учетной записи настраивается на 10 минут после 10 неудачных попыток доступа:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

Убедитесь, что [пассвордсигнинасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) задает `lockoutOnFailure` `true` :

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
