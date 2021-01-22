---
title: Создание приложения ASP.NET Core с данными пользователя, защищенными с помощью авторизации
author: rick-anderson
description: Узнайте, как создать ASP.NET Core веб-приложение с данными пользователя, защищенными с помощью авторизации. Включает протокол HTTPS, проверку подлинности, безопасность, ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
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
uid: security/authorization/secure-data
ms.openlocfilehash: ebd3c0dc9baa63b30f142773d7a3d621ce4082d9
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689309"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a>Создание ASP.NET Core веб-приложения с данными пользователя, защищенными с помощью авторизации

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Джо Одетт](https://twitter.com/joeaudette) (Joe Audette)

::: moniker range="= aspnetcore-2.0"

Просмотреть [этот PDF-файл](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

В этом руководстве показано, как создать веб-приложение ASP.NET Core с данными пользователя, защищенными с помощью авторизации. Отображается список контактов, которые были созданы пользователями, прошедшими проверку подлинности (зарегистрированные). Существует три группы безопасности:

* **Зарегистрированные пользователи** могут просматривать все утвержденные данные, а также изменять и удалять свои данные.
* **Руководители** могут утверждать или отклонять контактные данные. Пользователям видны только утвержденные контакты.
* **Администраторы** могут утверждать, отклонять и изменять и удалять любые данные.

Изображения в этом документе не полностью соответствуют последним шаблонам.

На следующем рисунке пользователь Рик ( `rick@example.com` ) вошел в. Рик может просматривать только утвержденные контакты и **редактировать** / **Удалить** / **создать новые** ссылки для своих контактов. Ссылки **Edit** и **Delete** отображаются только в последней записи, созданной Рик. Другие пользователи не увидят последнюю запись, пока руководитель или администратор не изменит состояние на "утверждено".

![Снимок экрана, показывающий Рик, выполнивший вход](secure-data/_static/rick.png)

На следующем рисунке `manager@contoso.com` Вход выполнен и в роли руководителя:

![Снимок экрана, показывающий manager@contoso.com Вход](secure-data/_static/manager1.png)

На следующем рисунке показано представление сведений об менеджерах для контакта:

![Представление контакта руководителя](secure-data/_static/manager.png)

Кнопки **утвердить** и **отклонить** отображаются только для руководителей и администраторов.

На следующем рисунке `admin@contoso.com` Вход выполнен и в роли администратора:

![Снимок экрана, показывающий admin@contoso.com Вход](secure-data/_static/admin.png)

Администратор имеет все привилегии. Она может читать, изменять и удалять контакты, а также изменять состояние контактов.

Приложение было создано с помощью [формирования шаблонов](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) следующей `Contact` модели:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Этот пример содержит следующие обработчики авторизации:

* `ContactIsOwnerAuthorizationHandler`: Гарантирует, что пользователь может изменять только свои данные.
* `ContactManagerAuthorizationHandler`: Позволяет руководителям утверждать или отклонять контакты.
* `ContactAdministratorsAuthorizationHandler`: Позволяет администраторам утверждать или отклонять контакты, а также изменять и удалять контакты.

## <a name="prerequisites"></a>Предварительные условия

Этот учебник расширен. Вы должны быть знакомы с:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Аутентификация](xref:security/authentication/identity)
* [Подтверждение учетной записи и восстановление пароля](xref:security/authentication/accconfirm)
* [Авторизация](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Начальное и завершенное приложение

[Скачайте](xref:index#how-to-download-a-sample) [готовое](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) приложение. [Протестируйте](#test-the-completed-app) готовое приложение, чтобы ознакомиться с его функциями безопасности.

### <a name="the-starter-app"></a>Начальное приложение

[Скачайте](xref:index#how-to-download-a-sample) [Начальное](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) приложение.

Запустите приложение, коснитесь ссылки **ContactManager** и убедитесь, что вы можете создать, изменить и удалить контакт. Сведения о создании начального приложения см. в разделе [Создание начального приложения](#create-the-starter-app).

## <a name="secure-user-data"></a>Защита пользовательских данных

Следующие разделы содержат все основные шаги по созданию безопасного приложения для данных пользователей. Может оказаться полезным ссылаться на завершенный проект.

### <a name="tie-the-contact-data-to-the-user"></a>Связать контактные данные с пользователем

Используйте [Identity](xref:security/authentication/identity) идентификатор пользователя ASP.NET, чтобы убедиться, что пользователи могут изменять данные, но не данные других пользователей. Добавьте `OwnerID` и `ContactStatus` в `Contact` модель:

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` Идентификатор пользователя из `AspNetUser` таблицы в [Identity](xref:security/authentication/identity) базе данных. `Status`Поле определяет, можно ли просматривать контакт обычными пользователями.

Создайте новую миграцию и обновите базу данных:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a>Добавление служб ролей в Identity

Добавление [аддролес](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) для добавления служб ролей:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a>Требовать прошедших проверку пользователей

Настройте политику резервной проверки подлинности, чтобы требовать проверку подлинности пользователей:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

Выделенный выше код задает [политику резервной проверки подлинности](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy). Для применения политики резервной проверки подлинности требуется **_ALL_* _ Users, за исключением Razor страниц, контроллеров или методов действий с атрибутом проверки подлинности. Например, Razor страницы, контроллеры или методы действий с `[AllowAnonymous]` или `[Authorize(PolicyName="MyPolicy")]` используют примененный атрибут проверки подлинности, а не политику резервной проверки подлинности.

<xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> Добавляет <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> к текущему экземпляру, который обеспечивает проверку подлинности текущего пользователя.

Резервная политика проверки подлинности:

_ Применяется ко всем запросам, которые не указывают политику проверки подлинности явным образом. Для запросов, обслуживаемых маршрутизацией конечных точек, сюда входят все конечные точки, не указывающие атрибут авторизации. Для запросов, обслуживаемых другим по промежуточного слоя, после по промежуточного слоя авторизации, например [статических файлов](xref:fundamentals/static-files), эта политика будет применена ко всем запросам.

Настройка политики резервной проверки подлинности на требование проверки подлинности пользователей защищает вновь добавленные Razor страницы и контроллеры. Необходимость проверки подлинности по умолчанию более безопасна, чем использование новых контроллеров и Razor страниц для включения `[Authorize]` атрибута.

<xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>Класс также содержит <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> . `DefaultPolicy`— Это политика, используемая с `[Authorize]` атрибутом, если не указана политика. `[Authorize]` не содержит именованную политику, в отличие от `[Authorize(PolicyName="MyPolicy")]` .

Дополнительные сведения о политиках см. в разделе <xref:security/authorization/policies> .

Альтернативный способ для контроллеров и Razor страниц MVC, требующих проверки подлинности всех пользователей, — Добавление фильтра авторизации:

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

В приведенном выше коде используется фильтр авторизации, установка политики резервирования использует маршрутизацию конечных точек. Настройка политики резервирования является предпочтительным способом требовать проверку подлинности всех пользователей.

Добавьте [allowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) на `Index` страницы и, `Privacy` чтобы анонимные пользователи могли получить сведения о сайте перед регистрацией:

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>Настройка тестовой учетной записи

`SeedData`Класс создает две учетные записи: Administrator и Manager. Используйте [средство диспетчера секретов](xref:security/app-secrets) , чтобы задать пароль для этих учетных записей. Задайте пароль из каталога проекта (каталога, содержащего *Program.CS*):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Если надежный пароль не указан, при вызове вызывается исключение `SeedData.Initialize` .

Обновите `Main` , чтобы использовать тестовый пароль:

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Создание тестовых учетных записей и обновление контактов

Обновите `Initialize` метод в `SeedData` классе, чтобы создать тестовые учетные записи:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

Добавьте идентификатор пользователя администратора и `ContactStatus` в список контактов. Сделайте одно из контактов "Отправлено" и одно "Отклонено". Добавьте идентификатор пользователя и состояние во все контакты. Отображается только один контакт:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Создание обработчиков авторизации владельца, руководителя и администратора

Создайте `ContactIsOwnerAuthorizationHandler` класс в папке *authorization* . `ContactIsOwnerAuthorizationHandler`Проверяет, принадлежит ли ресурс пользователю, работающему с ресурсом.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`Контекст вызовов [. Считается успешной](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , если текущий пользователь, прошедший проверку подлинности, является владельцем контакта. Обычно обработчики авторизации:

* Возврат `context.Succeed` при соблюдении требований.
* Возврат `Task.CompletedTask` , если требования не выполнены. `Task.CompletedTask` не является успешным или неудачным, &mdash; что позволяет запускать другие обработчики авторизации.

Если необходимо явное завершение, возвращаемый [контекст. Не пройдено](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Приложение позволяет владельцам контактов изменять, удалять и создавать собственные данные. `ContactIsOwnerAuthorizationHandler` не требуется проверять операцию, переданную в параметре требования.

### <a name="create-a-manager-authorization-handler"></a>Создание обработчика авторизации диспетчера

Создайте `ContactManagerAuthorizationHandler` класс в папке *authorization* . `ContactManagerAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, диспетчером. Только руководители могут утверждать или отклонять изменения содержимого (новые или измененные).

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Создание обработчика авторизации администратора

Создайте `ContactAdministratorsAuthorizationHandler` класс в папке *authorization* . `ContactAdministratorsAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, администратором. Администратор может выполнять все операции.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Регистрация обработчиков авторизации

Службы, использующие Entity Framework Core, должны быть зарегистрированы для [внедрения зависимостей](xref:fundamentals/dependency-injection) с помощью [аддскопед](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). `ContactIsOwnerAuthorizationHandler`Использует ASP.NET Core [Identity](xref:security/authentication/identity) , который построен на Entity Framework Core. Зарегистрируйте обработчики в коллекции служб, чтобы они были доступны `ContactsController` через [внедрение зависимостей](xref:fundamentals/dependency-injection). Добавьте следующий код в конец `ConfigureServices` :

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` и `ContactManagerAuthorizationHandler` добавляются в виде Singleton. Они являются Singleton-классом, поскольку не используют EF, а вся необходимая информация находится в `Context` параметре `HandleRequirementAsync` метода.

## <a name="support-authorization"></a>Поддержка авторизации

В этом разделе вы обновите Razor страницы и добавите класс требований к операциям.

### <a name="review-the-contact-operations-requirements-class"></a>Ознакомьтесь с классом требований к операциям

Проверьте `ContactOperations` класс. Этот класс содержит требования, которые поддерживает приложение:

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a>Создание базового класса для Razor страниц контактов

Создайте базовый класс, содержащий службы, используемые на Razor страницах контактов. Базовый класс помещает код инициализации в одно расположение:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

Предыдущий код:

* Добавляет `IAuthorizationService` службу для доступа к обработчикам авторизации.
* Добавляет Identity `UserManager` службу.
* Добавьте `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Обновление Креатемодел

Обновите конструктор модели страницы Create, чтобы использовать `DI_BasePageModel` базовый класс:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Обновите `CreateModel.OnPostAsync` метод, чтобы:

* Добавьте в модель идентификатор пользователя `Contact` .
* Вызовите обработчик авторизации, чтобы убедиться, что пользователь имеет разрешение на создание контактов.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Обновление Индексмодел

Обновите `OnGetAsync` метод, чтобы только утвержденные контакты отображались для обычных пользователей:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Обновление Едитмодел

Добавьте обработчик авторизации, чтобы убедиться, что пользователь владеет контактом. Так как авторизация ресурса проверяется, `[Authorize]` атрибут недостаточно. Приложение не имеет доступа к ресурсу при оценке атрибутов. Авторизация на основе ресурсов должна быть принудительной. Проверки должны выполняться после того, как приложение будет иметь доступ к ресурсу, загрузив его в модель страницы или загрузив в сам обработчик. Вы часто обращаетесь к ресурсу, передавая ключ ресурса.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Обновление Делетемодел

Обновите модель страницы удаления, чтобы использовать обработчик авторизации для проверки наличия у пользователя разрешения на удаление контакта.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Внедрение службы авторизации в представления

В настоящее время в пользовательском интерфейсе отображаются ссылки Edit и DELETE для контактов, которые пользователь не может изменить.

Вставьте службу авторизации в файл *pages/_ViewImports. cshtml* , чтобы он был доступен для всех представлений:

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

Предыдущая разметка добавляет несколько `using` инструкций.

Обновите ссылки **Edit** и **Delete** в *pages/contacts/index. cshtml* , чтобы они отображались только для пользователей с соответствующими разрешениями:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Скрытие ссылок от пользователей, не имеющих разрешений на изменение данных, не защищает приложение. Скрытие ссылок делает приложение более удобным для пользователей, отображая только допустимые ссылки. Пользователи могут обращаться к созданным URL-адресам для вызова операций правки и удаления данных, которыми они не владеют. RazorСтраница или контроллер должны применять проверки доступа для защиты данных.

### <a name="update-details"></a>Сведения об обновлении

Обновите представление сведений, чтобы руководители могли утверждать или отклонять контакты:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

Обновите модель страницы сведений:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Добавление или удаление пользователя для роли

Сведения об [этой ошибке](https://github.com/dotnet/AspNetCore.Docs/issues/8502) см. в следующих статьях:

* Удаление привилегий у пользователя. Например, отзвука пользователя в приложении разговора.
* Добавление привилегий для пользователя.

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a>Различия между запросом и запретом

Это приложение задает политику по умолчанию, [требующую проверки подлинности пользователей](#rau). Следующий код позволяет анонимным пользователям. Анонимным пользователям разрешено показывать различия между запросом и запретом.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

В предыдущем коде:

* Если пользователь **не** прошел проверку подлинности, `ChallengeResult` возвращается значение. Когда `ChallengeResult` возвращается, пользователь перенаправляется на страницу входа.
* Если пользователь прошел проверку подлинности, но не авторизован, `ForbidResult` возвращается значение. Когда `ForbidResult` возвращается значение, пользователь перенаправляется на страницу отказ в доступе.

## <a name="test-the-completed-app"></a>Тестирование завершенного приложения

Если вы еще не установили пароль для заполненных учетных записей пользователей, используйте [средство диспетчера секретов](xref:security/app-secrets#secret-manager) , чтобы задать пароль:

* Выберите надежный пароль: используйте восемь или более символов и по крайней мере одну прописную букву, цифру и символ. Например, `Passw0rd!` соответствует требованиям к надежному паролю.
* Выполните следующую команду из папки проекта, где `<PW>` — пароль:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

Если у приложения есть контакты:

* Удалите все записи в `Contact` таблице.
* Перезапустите приложение, чтобы заполнить базу данных.

Простой способ тестирования завершенного приложения — запуск трех различных браузеров (или режиме инкогнито и нечастных сеансов). В одном браузере Зарегистрируйте нового пользователя (например, `test@example.com` ). Войдите в каждый браузер с другим пользователем. Проверьте следующие операции:

* Зарегистрированные пользователи могут просматривать все утвержденные контактные данные.
* Зарегистрированные пользователи могут изменять и удалять собственные данные.
* Руководители могут утверждать и отклонять контактные данные. В `Details` представлении отображаются кнопки **утвердить** и **отклонить** .
* Администраторы могут утверждать, отклонять и изменять и удалять все данные.

| User                | Заполнено приложением | Параметры                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Нет                | Изменение или удаление собственных данных.                |
| manager@contoso.com | Да               | Утвердите, отклоните и измените или удалите собственные данные. |
| admin@contoso.com   | Да               | Утвердите или отклоните и измените или удалите все данные. |

Создайте контакт в браузере администратора. Скопируйте URL-адрес для DELETE и Edit из контакта администратора. Вставьте эти ссылки в браузер тестового пользователя, чтобы убедиться, что тестовая пользователь не может выполнить эти операции.

## <a name="create-the-starter-app"></a>Создание начального приложения

* Создание Razor страницы приложения с именем "ContactManager"
  * Создайте приложение с **учетными записями отдельных пользователей**.
  * Назовите его "ContactManager", чтобы пространство имен совпадало с пространством имен, используемым в примере.
  * `-uld` Указывает LocalDB вместо SQLite

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Добавление *моделей/Contact. CS*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Формирование шаблонов `Contact` модели.
* Создайте начальную миграцию и обновите базу данных:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

Если при выполнении команды возникла ошибка `dotnet aspnet-codegenerator razorpage` , см. [эту ошибку в GitHub](https://github.com/aspnet/Scaffolding/issues/984).

* Обновите привязку **ContactManager** в файле *pages/shared/_layout. cshtml* :

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* Тестирование приложения путем создания, изменения и удаления контакта

### <a name="seed-the-database"></a>Заполнение базы данных

Добавьте класс [сиддата](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) в папку *Data* :

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

Вызов `SeedData.Initialize` из `Main` :

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

Проверьте, что приложение заполнено базой данных. Если в базе данных Contact есть какие бы то ни было строки, метод SEED не выполняется.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

В этом руководстве показано, как создать веб-приложение ASP.NET Core с данными пользователя, защищенными с помощью авторизации. Отображается список контактов, которые были созданы пользователями, прошедшими проверку подлинности (зарегистрированные). Существует три группы безопасности:

* **Зарегистрированные пользователи** могут просматривать все утвержденные данные, а также изменять и удалять свои данные.
* **Руководители** могут утверждать или отклонять контактные данные. Пользователям видны только утвержденные контакты.
* **Администраторы** могут утверждать, отклонять и изменять и удалять любые данные.

На следующем рисунке пользователь Рик ( `rick@example.com` ) вошел в. Рик может просматривать только утвержденные контакты и **редактировать** / **Удалить** / **создать новые** ссылки для своих контактов. Ссылки **Edit** и **Delete** отображаются только в последней записи, созданной Рик. Другие пользователи не увидят последнюю запись, пока руководитель или администратор не изменит состояние на "утверждено".

![Снимок экрана, показывающий Рик, выполнивший вход](secure-data/_static/rick.png)

На следующем рисунке `manager@contoso.com` Вход выполнен и в роли руководителя:

![Снимок экрана, показывающий manager@contoso.com Вход](secure-data/_static/manager1.png)

На следующем рисунке показано представление сведений об менеджерах для контакта:

![Представление контакта руководителя](secure-data/_static/manager.png)

Кнопки **утвердить** и **отклонить** отображаются только для руководителей и администраторов.

На следующем рисунке `admin@contoso.com` Вход выполнен и в роли администратора:

![Снимок экрана, показывающий admin@contoso.com Вход](secure-data/_static/admin.png)

Администратор имеет все привилегии. Она может читать, изменять и удалять контакты, а также изменять состояние контактов.

Приложение было создано с помощью [формирования шаблонов](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) следующей `Contact` модели:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Этот пример содержит следующие обработчики авторизации:

* `ContactIsOwnerAuthorizationHandler`: Гарантирует, что пользователь может изменять только свои данные.
* `ContactManagerAuthorizationHandler`: Позволяет руководителям утверждать или отклонять контакты.
* `ContactAdministratorsAuthorizationHandler`: Позволяет администраторам утверждать или отклонять контакты, а также изменять и удалять контакты.

## <a name="prerequisites"></a>Предварительные условия

Этот учебник расширен. Вы должны быть знакомы с:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Аутентификация](xref:security/authentication/identity)
* [Подтверждение учетной записи и восстановление пароля](xref:security/authentication/accconfirm)
* [Авторизация](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Начальное и завершенное приложение

[Скачайте](xref:index#how-to-download-a-sample) [готовое](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) приложение. [Протестируйте](#test-the-completed-app) готовое приложение, чтобы ознакомиться с его функциями безопасности.

### <a name="the-starter-app"></a>Начальное приложение

[Скачайте](xref:index#how-to-download-a-sample) [Начальное](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) приложение.

Запустите приложение, коснитесь ссылки **ContactManager** и убедитесь, что вы можете создать, изменить и удалить контакт.

## <a name="secure-user-data"></a>Защита пользовательских данных

Следующие разделы содержат все основные шаги по созданию безопасного приложения для данных пользователей. Может оказаться полезным ссылаться на завершенный проект.

### <a name="tie-the-contact-data-to-the-user"></a>Связать контактные данные с пользователем

Используйте [Identity](xref:security/authentication/identity) идентификатор пользователя ASP.NET, чтобы убедиться, что пользователи могут изменять данные, но не данные других пользователей. Добавьте `OwnerID` и `ContactStatus` в `Contact` модель:

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` Идентификатор пользователя из `AspNetUser` таблицы в [Identity](xref:security/authentication/identity) базе данных. `Status`Поле определяет, можно ли просматривать контакт обычными пользователями.

Создайте новую миграцию и обновите базу данных:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a>Добавление служб ролей в Identity

Добавление [аддролес](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) для добавления служб ролей:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a>Требовать прошедших проверку пользователей

Задайте политику проверки подлинности по умолчанию, чтобы требовать проверку подлинности пользователей:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Можно отказаться от проверки подлинности на Razor уровне страницы, контроллера или метода действия с помощью `[AllowAnonymous]` атрибута. Настройка политики проверки подлинности по умолчанию для обязательной проверки подлинности пользователей защищает вновь добавленные Razor страницы и контроллеры. Необходимость проверки подлинности по умолчанию более безопасна, чем использование новых контроллеров и Razor страниц для включения `[Authorize]` атрибута.

Добавьте [allowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) в индекс, About и Contact Pages, чтобы анонимные пользователи могли получить сведения о сайте перед регистрацией.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>Настройка тестовой учетной записи

`SeedData`Класс создает две учетные записи: Administrator и Manager. Используйте [средство диспетчера секретов](xref:security/app-secrets) , чтобы задать пароль для этих учетных записей. Задайте пароль из каталога проекта (каталога, содержащего *Program.CS*):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Если надежный пароль не указан, при вызове вызывается исключение `SeedData.Initialize` .

Обновите `Main` , чтобы использовать тестовый пароль:

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Создание тестовых учетных записей и обновление контактов

Обновите `Initialize` метод в `SeedData` классе, чтобы создать тестовые учетные записи:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

Добавьте идентификатор пользователя администратора и `ContactStatus` в список контактов. Сделайте одно из контактов "Отправлено" и одно "Отклонено". Добавьте идентификатор пользователя и состояние во все контакты. Отображается только один контакт:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Создание обработчиков авторизации владельца, руководителя и администратора

Создайте папку *авторизации* и создайте `ContactIsOwnerAuthorizationHandler` в ней класс. `ContactIsOwnerAuthorizationHandler`Проверяет, принадлежит ли ресурс пользователю, работающему с ресурсом.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`Контекст вызовов [. Считается успешной](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , если текущий пользователь, прошедший проверку подлинности, является владельцем контакта. Обычно обработчики авторизации:

* Возврат `context.Succeed` при соблюдении требований.
* Возврат `Task.CompletedTask` , если требования не выполнены. `Task.CompletedTask` не является успешным или неудачным, &mdash; что позволяет запускать другие обработчики авторизации.

Если необходимо явное завершение, возвращаемый [контекст. Не пройдено](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Приложение позволяет владельцам контактов изменять, удалять и создавать собственные данные. `ContactIsOwnerAuthorizationHandler` не требуется проверять операцию, переданную в параметре требования.

### <a name="create-a-manager-authorization-handler"></a>Создание обработчика авторизации диспетчера

Создайте `ContactManagerAuthorizationHandler` класс в папке *authorization* . `ContactManagerAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, диспетчером. Только руководители могут утверждать или отклонять изменения содержимого (новые или измененные).

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Создание обработчика авторизации администратора

Создайте `ContactAdministratorsAuthorizationHandler` класс в папке *authorization* . `ContactAdministratorsAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, администратором. Администратор может выполнять все операции.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Регистрация обработчиков авторизации

Службы, использующие Entity Framework Core, должны быть зарегистрированы для [внедрения зависимостей](xref:fundamentals/dependency-injection) с помощью [аддскопед](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). `ContactIsOwnerAuthorizationHandler`Использует ASP.NET Core [Identity](xref:security/authentication/identity) , который построен на Entity Framework Core. Зарегистрируйте обработчики в коллекции служб, чтобы они были доступны `ContactsController` через [внедрение зависимостей](xref:fundamentals/dependency-injection). Добавьте следующий код в конец `ConfigureServices` :

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` и `ContactManagerAuthorizationHandler` добавляются в виде Singleton. Они являются Singleton-классом, поскольку не используют EF, а вся необходимая информация находится в `Context` параметре `HandleRequirementAsync` метода.

## <a name="support-authorization"></a>Поддержка авторизации

В этом разделе вы обновите Razor страницы и добавите класс требований к операциям.

### <a name="review-the-contact-operations-requirements-class"></a>Ознакомьтесь с классом требований к операциям

Проверьте `ContactOperations` класс. Этот класс содержит требования, которые поддерживает приложение:

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a>Создание базового класса для Razor страниц контактов

Создайте базовый класс, содержащий службы, используемые на Razor страницах контактов. Базовый класс помещает код инициализации в одно расположение:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

Предыдущий код:

* Добавляет `IAuthorizationService` службу для доступа к обработчикам авторизации.
* Добавляет Identity `UserManager` службу.
* Добавьте `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Обновление Креатемодел

Обновите конструктор модели страницы Create, чтобы использовать `DI_BasePageModel` базовый класс:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Обновите `CreateModel.OnPostAsync` метод, чтобы:

* Добавьте в модель идентификатор пользователя `Contact` .
* Вызовите обработчик авторизации, чтобы убедиться, что пользователь имеет разрешение на создание контактов.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Обновление Индексмодел

Обновите `OnGetAsync` метод, чтобы только утвержденные контакты отображались для обычных пользователей:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Обновление Едитмодел

Добавьте обработчик авторизации, чтобы убедиться, что пользователь владеет контактом. Так как авторизация ресурса проверяется, `[Authorize]` атрибут недостаточно. Приложение не имеет доступа к ресурсу при оценке атрибутов. Авторизация на основе ресурсов должна быть принудительной. Проверки должны выполняться после того, как приложение будет иметь доступ к ресурсу, загрузив его в модель страницы или загрузив в сам обработчик. Вы часто обращаетесь к ресурсу, передавая ключ ресурса.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Обновление Делетемодел

Обновите модель страницы удаления, чтобы использовать обработчик авторизации для проверки наличия у пользователя разрешения на удаление контакта.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Внедрение службы авторизации в представления

В настоящее время в пользовательском интерфейсе отображаются ссылки Edit и DELETE для контактов, которые пользователь не может изменить.

Вставьте службу авторизации в файл *views/_ViewImports. cshtml* , чтобы она была доступна для всех представлений:

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

Предыдущая разметка добавляет несколько `using` инструкций.

Обновите ссылки **Edit** и **Delete** в *pages/contacts/index. cshtml* , чтобы они отображались только для пользователей с соответствующими разрешениями:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Скрытие ссылок от пользователей, не имеющих разрешений на изменение данных, не защищает приложение. Скрытие ссылок делает приложение более удобным для пользователей, отображая только допустимые ссылки. Пользователи могут обращаться к созданным URL-адресам для вызова операций правки и удаления данных, которыми они не владеют. RazorСтраница или контроллер должны применять проверки доступа для защиты данных.

### <a name="update-details"></a>Сведения об обновлении

Обновите представление сведений, чтобы руководители могли утверждать или отклонять контакты:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

Обновите модель страницы сведений:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Добавление или удаление пользователя для роли

Сведения об [этой ошибке](https://github.com/dotnet/AspNetCore.Docs/issues/8502) см. в следующих статьях:

* Удаление привилегий у пользователя. Например, отзвука пользователя в приложении разговора.
* Добавление привилегий для пользователя.

## <a name="test-the-completed-app"></a>Тестирование завершенного приложения

Если вы еще не установили пароль для заполненных учетных записей пользователей, используйте [средство диспетчера секретов](xref:security/app-secrets#secret-manager) , чтобы задать пароль:

* Выберите надежный пароль: используйте восемь или более символов и по крайней мере одну прописную букву, цифру и символ. Например, `Passw0rd!` соответствует требованиям к надежному паролю.
* Выполните следующую команду из папки проекта, где `<PW>` — пароль:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* Удаление и обновление базы данных

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* Перезапустите приложение, чтобы заполнить базу данных.

Простой способ тестирования завершенного приложения — запуск трех различных браузеров (или режиме инкогнито и нечастных сеансов). В одном браузере Зарегистрируйте нового пользователя (например, `test@example.com` ). Войдите в каждый браузер с другим пользователем. Проверьте следующие операции:

* Зарегистрированные пользователи могут просматривать все утвержденные контактные данные.
* Зарегистрированные пользователи могут изменять и удалять собственные данные.
* Руководители могут утверждать и отклонять контактные данные. В `Details` представлении отображаются кнопки **утвердить** и **отклонить** .
* Администраторы могут утверждать, отклонять и изменять и удалять все данные.

| User                | Заполнено приложением | Параметры                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Нет                | Изменение или удаление собственных данных.                |
| manager@contoso.com | Да               | Утвердите, отклоните и измените или удалите собственные данные. |
| admin@contoso.com   | Да               | Утвердите или отклоните и измените или удалите все данные. |

Создайте контакт в браузере администратора. Скопируйте URL-адрес для DELETE и Edit из контакта администратора. Вставьте эти ссылки в браузер тестового пользователя, чтобы убедиться, что тестовая пользователь не может выполнить эти операции.

## <a name="create-the-starter-app"></a>Создание начального приложения

* Создание Razor страницы приложения с именем "ContactManager"
  * Создайте приложение с **учетными записями отдельных пользователей**.
  * Назовите его "ContactManager", чтобы пространство имен совпадало с пространством имен, используемым в примере.
  * `-uld` Указывает LocalDB вместо SQLite

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Добавление *моделей/Contact. CS*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Формирование шаблонов `Contact` модели.
* Создайте начальную миграцию и обновите базу данных:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* Обновите привязку **ContactManager** в файле *pages/_layout. cshtml* :

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* Тестирование приложения путем создания, изменения и удаления контакта

### <a name="seed-the-database"></a>Заполнение базы данных

Добавьте класс [сиддата](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) в папку *Data* .

Вызов `SeedData.Initialize` из `Main` :

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

Проверьте, что приложение заполнено базой данных. Если в базе данных Contact есть какие бы то ни было строки, метод SEED не выполняется.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Дополнительные ресурсы

* [Сборка веб-приложения .NET Core и базы данных SQL в службе приложений Azure](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET Core лаборатории авторизации](https://github.com/blowdart/AspNetAuthorizationWorkshop). В этом лабораторном занятии подробно рассматриваются функции безопасности, представленные в этом руководстве.
* <xref:security/authorization/introduction>
* [Настраиваемая авторизация на основе политик](xref:security/authorization/policies)
