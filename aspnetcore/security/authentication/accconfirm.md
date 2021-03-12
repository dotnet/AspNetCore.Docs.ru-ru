---
title: Подтверждение учетной записи и восстановление пароля в ASP.NET Core
author: rick-anderson
description: Узнайте, как создать приложение ASP.NET Core с подтверждением электронной почты и сбросом пароля.
ms.author: riande
ms.date: 03/11/2019
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
uid: security/authentication/accconfirm
ms.openlocfilehash: f71ae5e619b875c03401fa78320582c406875401
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586115"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>Подтверждение учетной записи и восстановление пароля в ASP.NET Core

[Рик Андерсон (](https://twitter.com/RickAndMSFT), [Понант](https://github.com/Ponant)и [Джо аудетте](https://twitter.com/joeaudette)

В этом руководстве показано, как создать приложение ASP.NET Core с подтверждением электронной почты и сбросом пароля. Это руководство **не** является началом статьи. Вы должны быть знакомы с:

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [Аутентификация](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Предварительные требования

[Пакет SDK для .NET Core 3.0 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a>Создание и тестирование веб-приложения с проверкой подлинности

Выполните следующие команды, чтобы создать веб-приложение с проверкой подлинности.

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

Запустите приложение, выберите ссылку **Register** и зарегистрируйте пользователя. После регистрации вы будете перенаправлены на `/Identity/Account/RegisterConfirmation` страницу, содержащую ссылку для имитации подтверждения по электронной почте:

* Выберите `Click here to confirm your account` ссылку.
* Выберите ссылку для **входа** и выполните вход с теми же учетными данными.
* Выберите `Hello YourEmail@provider.com!` ссылку, которая перенаправит вас на `/Identity/Account/Manage/PersonalData` страницу.
* Выберите вкладку **личные данные** слева, а затем щелкните **Удалить**.

### <a name="configure-an-email-provider"></a>Настройка поставщика электронной почты

В этом руководстве [SendGrid](https://sendgrid.com) используется для отправки электронной почты. Для отправки электронной почты требуется учетная запись SendGrid и ключ. Вы можете использовать другие поставщики электронной почты. Для отправки электронной почты рекомендуется использовать SendGrid или другую почтовую службу. Протокол SMTP трудно защитить и настроить правильно.

Для учетной записи SendGrid может потребоваться [Добавить отправителя](https://sendgrid.com/docs/ui/sending-email/senders/).

Создайте класс для выборки ключа защищенной электронной почты. Для этого примера создайте *Services/аусмессажесендероптионс. CS*:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Настройка секретов пользователя SendGrid

Задайте `SendGridUser` и `SendGridKey` с помощью [средства Secret-Manager](xref:security/app-secrets). Пример:

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

В Windows Диспетчер секретов сохраняет пары "ключ-значение" в *secrets.jsдля* файла в `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` каталоге.

Содержимое *secrets.js* файла не шифруется. В следующей разметке показано *secrets.js* файла. `SendGridKey`Значение было удалено.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Дополнительные сведения см. в разделе [шаблон](xref:fundamentals/configuration/options) и [Конфигурация](xref:fundamentals/configuration/index)параметров.

### <a name="install-sendgrid"></a>Установка SendGrid

В этом руководстве показано, как добавлять уведомления по электронной почте через [SendGrid](https://sendgrid.com/), но можно отправлять сообщения электронной почты с помощью SMTP и других механизмов.

Установите `SendGrid` пакет NuGet:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В консоли диспетчера пакетов введите следующую команду:

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

В консоли введите следующую команду:

```dotnetcli
dotnet add package SendGrid
```

---

Ознакомьтесь [со статьей начало работы с SendGrid](https://sendgrid.com/free/) , чтобы зарегистрироваться для получения бесплатной учетной записи SendGrid.

### <a name="implement-iemailsender"></a>Реализация Иемаилсендер

Чтобы реализовать `IEmailSender` , создайте *службы/емаилсендер. CS* с кодом, аналогичным следующему:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Настройка запуска для поддержки электронной почты

Добавьте следующий код в `ConfigureServices` метод в файле *Startup.CS* :

* Добавьте `EmailSender` в качестве временной службы.
* Зарегистрируйте `AuthMessageSenderOptions` экземпляр конфигурации.

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a>Регистерконфирматион шаблонов

Следуйте инструкциям по [формированию Identity шаблонов](xref:security/authentication/scaffold-identity) и шаблонов `RegisterConfirmation` .

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/main/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a>Регистрация, подтверждение электронной почты и сброс пароля

Запустите веб-приложение и протестируйте поток подтверждения учетной записи и восстановления пароля.

* Запуск приложения и регистрация нового пользователя
* Проверьте свою электронную почту на наличие ссылки для подтверждения учетной записи. Если вы не получаете электронное письмо, см. раздел [Отладка электронной почты](#debug) .
* Щелкните ссылку, чтобы подтвердить свою электронную почту.
* Выполните вход, используя свой адрес электронной почты и пароль.
* Выполните выход.

### <a name="test-password-reset"></a>Проверка сброса пароля

* Если вы вошли в этот компьютер, выберите **выход**.
* Щелкните ссылку **войти** и выберите ссылку **забыли пароль?** .
* Введите адрес электронной почты, использованный для регистрации учетной записи.
* Отправляется сообщение электронной почты со ссылкой для сброса пароля. Проверьте свой адрес электронной почты и щелкните ссылку, чтобы сбросить пароль. После успешного сброса пароля можно выполнить вход с помощью электронной почты и нового пароля.

<a name="resend"></a>

## <a name="resend-email-confirmation"></a>Подтверждение повторной отправки электронной почты

В ASP.NET Core 5,0 и более поздних версиях выберите ссылку повторно **Отправить подтверждение по электронной почте** на странице **входа** .

### <a name="change-email-and-activity-timeout"></a>Изменить время ожидания для электронной почты и активности

Время ожидания бездействия по умолчанию составляет 14 дней. Следующий код задает время ожидания простоя в 5 дней:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Изменить все продолжительность существования маркеров защиты данных

Следующий код изменяет все маркеры защиты данных в течение времени ожидания до 3 часов:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

Встроенные Identity маркеры пользователей (см. [AspNetCore/src/ Identity /екстенсионс.коре/СРК/токеноптионс.КС](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) имеют [время ожидания в один день](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Изменение срока существования маркера электронной почты

Срок существования токена [ Identity пользователя](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) по умолчанию — [один день](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). В этом разделе показано, как изменить срок существования маркера электронной почты.

Добавьте пользовательские [датапротектортокенпровидер \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) и <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Добавьте настраиваемый поставщик в контейнер службы:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

<a name="debug"></a>

### <a name="debug-email"></a>Отладка электронной почты

Если вы не можете работать с электронной почтой:

* Установите точку останова в `EmailSender.Execute` для проверки `SendGridClient.SendEmailAsync` .
* Создайте [консольное приложение для отправки электронной почты](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) с помощью аналогичного кода в `EmailSender.Execute` .
* Ознакомьтесь со страницей [действие по электронной почте](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Проверьте папку спама.
* Попробуйте использовать другой псевдоним электронной почты в другом поставщике электронной почты (Microsoft, Yahoo, Gmail и т. д.).
* Попробуйте отправить их в другую учетную запись электронной почты.

В целях **безопасности** рекомендуется **не** использовать рабочие секреты в тестировании и разработке. Если приложение публикуется в Azure, задайте секреты SendGrid как параметры приложения на портале веб-приложения Azure. Система конфигурации настроена на чтение ключей из переменных среды.

## <a name="combine-social-and-local-login-accounts"></a>Объединение социальных и местных учетных записей входа

Для выполнения этого раздела необходимо сначала включить внешний поставщик проверки подлинности. См. статью [Проверка подлинности в Facebook, Google и внешнем поставщике](xref:security/authentication/social/index).

Вы можете объединить локальные и социальные учетные записи, щелкнув ссылку на электронную почту. В следующей последовательности " RickAndMSFT@gmail.com " сначала создается как локальное имя входа, но вы можете сначала создать учетную запись в качестве имени для входа в социальных сетях, а затем добавить локальное имя входа.

![Веб-приложение: RickAndMSFT@gmail.com Проверка подлинности пользователя](accconfirm/_static/rick.png)

Щелкните ссылку **Управление** . Обратите внимание на 0 внешних (социальных) имен, связанных с этой учетной записью.

![Управление представлением](accconfirm/_static/manage.png)

Щелкните ссылку на другую службу входа и примите запросы приложения. На следующем рисунке Facebook является внешним поставщиком проверки подлинности:

![Управление внешними именами входа Просмотр списка Facebook](accconfirm/_static/fb.png)

Две учетные записи были объединены. Вы можете войти с помощью любой учетной записи. Пользователям может потребоваться добавить локальные учетные записи на случай, если служба проверки подлинности в социальных сетях не работает, или более вероятно, что они потеряют доступ к учетной записи социальных сетей.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Включить подтверждение учетной записи после того, как у сайта есть пользователи

Включение подтверждения учетной записи на сайте с пользователями блокирует всех существующих пользователей. Существующие пользователи заблокированы, так как их учетные записи не подтверждены. Чтобы обойти существующую блокировку пользователей, используйте один из следующих подходов.

* Обновите базу данных, чтобы пометить все существующие пользователи как подтвержденные.
* Подтвердите существующих пользователей. Например, пакетная отправка сообщений электронной почты с ссылками для подтверждения.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Предварительные требования

[Пакет SDK для .NET Core 2,2 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a>Создание веб-приложения и шаблона Identity

Выполните следующие команды, чтобы создать веб-приложение с проверкой подлинности.

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run
```

> [!NOTE]
> Если <xref:Microsoft.AspNetCore.Identity.PasswordOptions> настроен в `Startup.ConfigureServices` , для свойства в шаблонных страницах может потребоваться настройка [ `[StringLength]` атрибутов](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` Identity . `InputModel` `Password` Свойство находится в `Areas/Identity/Pages/Account/Register.cshtml.cs` файле после формирования шаблонов Identity .

## <a name="test-new-user-registration"></a>Тестирование регистрации нового пользователя

Запустите приложение, выберите ссылку **Register** и зарегистрируйте пользователя. На этом этапе единственной проверкой адреса электронной почты является [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) атрибут. После отправки регистрации вы вошли в приложение. Далее в этом руководстве код обновляется, поэтому новые пользователи не смогут войти в систему, пока не будет проверена электронная почта.

[!INCLUDE[](~/includes/view-identity-db.md)]

Обратите внимание, что `EmailConfirmed` поле таблицы имеет значение `False` .

Вы можете снова использовать это сообщение на следующем шаге, когда приложение отправит сообщение электронной почты с подтверждением. Щелкните строку правой кнопкой мыши и выберите команду **Удалить**. Удаление псевдонима электронной почты упрощает следующие шаги.

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>Требовать подтверждение по электронной почте

Рекомендуется подтвердить сообщение электронной почты о новой регистрации пользователя. Подтверждение по электронной почте помогает проверить, что они не олицетворяют другого пользователя (т. е. они не зарегистрированы в сообщении электронной почты другого пользователя). Предположим, у вас есть дискуссионный форум, и вы хотите предотвратить yli@example.com регистрацию "" в виде "" nolivetto@contoso.com . Без подтверждения по электронной почте " nolivetto@contoso.com " может получить от приложения нежелательное сообщение электронной почты. Предположим, что пользователь случайно зарегистрировался как " ylo@example.com " и не заметил опечатку "или". Они не смогут использовать восстановление пароля, так как у приложения нет нужной электронной почты. Подтверждение по электронной почте обеспечивает ограниченную защиту от программы-роботы. Подтверждение по электронной почте не обеспечивает защиту от вредоносных пользователей со многими учетными записями электронной почты.

Обычно требуется запретить новым пользователям отправлять данные на ваш веб-сайт, прежде чем они будут иметь подтвержденное сообщение электронной почты.

Обновление `Startup.ConfigureServices`  для запроса подтвержденного адреса электронной почты:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;` запрещает зарегистрированным пользователям входить в систему, пока не будет подтверждено их электронная почта.

### <a name="configure-email-provider"></a>Настройка поставщика электронной почты

В этом руководстве [SendGrid](https://sendgrid.com) используется для отправки электронной почты. Для отправки электронной почты требуется учетная запись SendGrid и ключ. Вы можете использовать другие поставщики электронной почты. ASP.NET Core 2. x включает `System.Net.Mail` , что позволяет отправлять электронную почту из приложения. Для отправки электронной почты рекомендуется использовать SendGrid или другую почтовую службу. Протокол SMTP трудно защитить и настроить правильно.

Создайте класс для выборки ключа защищенной электронной почты. Для этого примера создайте *Services/аусмессажесендероптионс. CS*:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Настройка секретов пользователя SendGrid

Задайте `SendGridUser` и `SendGridKey` с помощью [средства Secret-Manager](xref:security/app-secrets). Пример:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

В Windows Диспетчер секретов сохраняет пары "ключ-значение" в *secrets.jsдля* файла в `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` каталоге.

Содержимое *secrets.js* файла не шифруется. В следующей разметке показано *secrets.js* файла. `SendGridKey`Значение было удалено.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Дополнительные сведения см. в разделе [шаблон](xref:fundamentals/configuration/options) и [Конфигурация](xref:fundamentals/configuration/index)параметров.

### <a name="install-sendgrid"></a>Установка SendGrid

В этом руководстве показано, как добавлять уведомления по электронной почте через [SendGrid](https://sendgrid.com/), но можно отправлять сообщения электронной почты с помощью SMTP и других механизмов.

Установите `SendGrid` пакет NuGet:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В консоли диспетчера пакетов введите следующую команду:

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

В консоли введите следующую команду:

```dotnetcli
dotnet add package SendGrid
```

---

Ознакомьтесь [со статьей начало работы с SendGrid](https://sendgrid.com/free/) , чтобы зарегистрироваться для получения бесплатной учетной записи SendGrid.

### <a name="implement-iemailsender"></a>Реализация Иемаилсендер

Чтобы реализовать `IEmailSender` , создайте *службы/емаилсендер. CS* с кодом, аналогичным следующему:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Настройка запуска для поддержки электронной почты

Добавьте следующий код в `ConfigureServices` метод в файле *Startup.CS* :

* Добавьте `EmailSender` в качестве временной службы.
* Зарегистрируйте `AuthMessageSenderOptions` экземпляр конфигурации.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>Включение подтверждения учетной записи и восстановление пароля

Шаблон содержит код для подтверждения учетной записи и восстановления пароля. Найдите `OnPostAsync` метод в *области (Areas/ Identity /Пажес/аккаунт/регистер.кштмл.КС*).

Запретите автоматический вход новых зарегистрированных пользователей с помощью комментария к следующей строке:

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

Показан полный метод с выделенной измененной строкой:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>Регистрация, подтверждение электронной почты и сброс пароля

Запустите веб-приложение и протестируйте поток подтверждения учетной записи и восстановления пароля.

* Запуск приложения и регистрация нового пользователя
* Проверьте свою электронную почту на наличие ссылки для подтверждения учетной записи. Если вы не получаете электронное письмо, см. раздел [Отладка электронной почты](#debug) .
* Щелкните ссылку, чтобы подтвердить свою электронную почту.
* Выполните вход, используя свой адрес электронной почты и пароль.
* Выполните выход.

### <a name="view-the-manage-page"></a>Просмотр страницы "Управление"

Выберите имя пользователя в браузере: ![ окно браузера с именем пользователя.](accconfirm/_static/un.png)

Страница Управление отображается с выбранной вкладкой **профиль** . В **сообщении электронной почты** отображается флажок, указывающий, что сообщение электронной почты подтверждено.

### <a name="test-password-reset"></a>Проверка сброса пароля

* Если вы вошли в этот компьютер, выберите **выход**.
* Щелкните ссылку **войти** и выберите ссылку **забыли пароль?** .
* Введите адрес электронной почты, использованный для регистрации учетной записи.
* Отправляется сообщение электронной почты со ссылкой для сброса пароля. Проверьте свой адрес электронной почты и щелкните ссылку, чтобы сбросить пароль. После успешного сброса пароля можно выполнить вход с помощью электронной почты и нового пароля.

## <a name="change-email-and-activity-timeout"></a>Изменить время ожидания для электронной почты и активности

Время ожидания бездействия по умолчанию составляет 14 дней. Следующий код задает время ожидания простоя в 5 дней:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Изменить все продолжительность существования маркеров защиты данных

Следующий код изменяет все маркеры защиты данных в течение времени ожидания до 3 часов:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

Встроенные Identity маркеры пользователей (см. [AspNetCore/src/ Identity /екстенсионс.коре/СРК/токеноптионс.КС](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) имеют [время ожидания в один день](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Изменение срока существования маркера электронной почты

Срок существования токена [ Identity пользователя](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) по умолчанию — [один день](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). В этом разделе показано, как изменить срок существования маркера электронной почты.

Добавьте пользовательские [датапротектортокенпровидер \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) и <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Добавьте настраиваемый поставщик в контейнер службы:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>Подтверждение повторной отправки электронной почты

Также см. [эту проблему в GitHub](https://github.com/dotnet/AspNetCore/issues/5410).

<a name="debug"></a>

### <a name="debug-email"></a>Отладка электронной почты

Если вы не можете работать с электронной почтой:

* Установите точку останова в `EmailSender.Execute` для проверки `SendGridClient.SendEmailAsync` .
* Создайте [консольное приложение для отправки электронной почты](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) с помощью аналогичного кода в `EmailSender.Execute` .
* Ознакомьтесь со страницей [действие по электронной почте](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Проверьте папку спама.
* Попробуйте использовать другой псевдоним электронной почты в другом поставщике электронной почты (Microsoft, Yahoo, Gmail и т. д.).
* Попробуйте отправить их в другую учетную запись электронной почты.

В целях **безопасности** рекомендуется **не** использовать рабочие секреты в тестировании и разработке. При публикации приложения в Azure можно задать секреты SendGrid как параметры приложения на портале веб-приложения Azure. Система конфигурации настроена на чтение ключей из переменных среды.

## <a name="combine-social-and-local-login-accounts"></a>Объединение социальных и местных учетных записей входа

Для выполнения этого раздела необходимо сначала включить внешний поставщик проверки подлинности. См. статью [Проверка подлинности в Facebook, Google и внешнем поставщике](xref:security/authentication/social/index).

Вы можете объединить локальные и социальные учетные записи, щелкнув ссылку на электронную почту. В следующей последовательности " RickAndMSFT@gmail.com " сначала создается как локальное имя входа, но вы можете сначала создать учетную запись в качестве имени для входа в социальных сетях, а затем добавить локальное имя входа.

![Веб-приложение: RickAndMSFT@gmail.com Проверка подлинности пользователя](accconfirm/_static/rick.png)

Щелкните ссылку **Управление** . Обратите внимание на 0 внешних (социальных) имен, связанных с этой учетной записью.

![Управление представлением](accconfirm/_static/manage.png)

Щелкните ссылку на другую службу входа и примите запросы приложения. На следующем рисунке Facebook является внешним поставщиком проверки подлинности:

![Управление внешними именами входа Просмотр списка Facebook](accconfirm/_static/fb.png)

Две учетные записи были объединены. Вы можете войти с помощью любой учетной записи. Пользователям может потребоваться добавить локальные учетные записи на случай, если служба проверки подлинности в социальных сетях не работает, или более вероятно, что они потеряют доступ к учетной записи социальных сетей.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Включить подтверждение учетной записи после того, как у сайта есть пользователи

Включение подтверждения учетной записи на сайте с пользователями блокирует всех существующих пользователей. Существующие пользователи заблокированы, так как их учетные записи не подтверждены. Чтобы обойти существующую блокировку пользователей, используйте один из следующих подходов.

* Обновите базу данных, чтобы пометить все существующие пользователи как подтвержденные.
* Подтвердите существующих пользователей. Например, пакетная отправка сообщений электронной почты с ссылками для подтверждения.

::: moniker-end
