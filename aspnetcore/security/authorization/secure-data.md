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
ms.openlocfilehash: 662456af59c453df66ca48139a6de40d0e2cbf0d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589196"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="cdac7-104">Создание ASP.NET Core веб-приложения с данными пользователя, защищенными с помощью авторизации</span><span class="sxs-lookup"><span data-stu-id="cdac7-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="cdac7-105">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Джо Одетт](https://twitter.com/joeaudette) (Joe Audette)</span><span class="sxs-lookup"><span data-stu-id="cdac7-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="cdac7-106">Просмотреть [этот PDF-файл](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="cdac7-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cdac7-107">В этом руководстве показано, как создать веб-приложение ASP.NET Core с данными пользователя, защищенными с помощью авторизации.</span><span class="sxs-lookup"><span data-stu-id="cdac7-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="cdac7-108">Отображается список контактов, которые были созданы пользователями, прошедшими проверку подлинности (зарегистрированные).</span><span class="sxs-lookup"><span data-stu-id="cdac7-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="cdac7-109">Существует три группы безопасности:</span><span class="sxs-lookup"><span data-stu-id="cdac7-109">There are three security groups:</span></span>

* <span data-ttu-id="cdac7-110">**Зарегистрированные пользователи** могут просматривать все утвержденные данные, а также изменять и удалять свои данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="cdac7-111">**Руководители** могут утверждать или отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="cdac7-112">Пользователям видны только утвержденные контакты.</span><span class="sxs-lookup"><span data-stu-id="cdac7-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="cdac7-113">**Администраторы** могут утверждать, отклонять и изменять и удалять любые данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="cdac7-114">Изображения в этом документе не полностью соответствуют последним шаблонам.</span><span class="sxs-lookup"><span data-stu-id="cdac7-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="cdac7-115">На следующем рисунке пользователь Рик ( `rick@example.com` ) вошел в.</span><span class="sxs-lookup"><span data-stu-id="cdac7-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="cdac7-116">Рик может просматривать только утвержденные контакты и **редактировать** / **Удалить** / **создать новые** ссылки для своих контактов.</span><span class="sxs-lookup"><span data-stu-id="cdac7-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="cdac7-117">Ссылки **Edit** и **Delete** отображаются только в последней записи, созданной Рик.</span><span class="sxs-lookup"><span data-stu-id="cdac7-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="cdac7-118">Другие пользователи не увидят последнюю запись, пока руководитель или администратор не изменит состояние на "утверждено".</span><span class="sxs-lookup"><span data-stu-id="cdac7-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Снимок экрана, показывающий Рик, выполнивший вход](secure-data/_static/rick.png)

<span data-ttu-id="cdac7-120">На следующем рисунке `manager@contoso.com` Вход выполнен и в роли руководителя:</span><span class="sxs-lookup"><span data-stu-id="cdac7-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Снимок экрана, показывающий manager@contoso.com Вход](secure-data/_static/manager1.png)

<span data-ttu-id="cdac7-122">На следующем рисунке показано представление сведений об менеджерах для контакта:</span><span class="sxs-lookup"><span data-stu-id="cdac7-122">The following image shows the managers details view of a contact:</span></span>

![Представление контакта руководителя](secure-data/_static/manager.png)

<span data-ttu-id="cdac7-124">Кнопки **утвердить** и **отклонить** отображаются только для руководителей и администраторов.</span><span class="sxs-lookup"><span data-stu-id="cdac7-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="cdac7-125">На следующем рисунке `admin@contoso.com` Вход выполнен и в роли администратора:</span><span class="sxs-lookup"><span data-stu-id="cdac7-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Снимок экрана, показывающий admin@contoso.com Вход](secure-data/_static/admin.png)

<span data-ttu-id="cdac7-127">Администратор имеет все привилегии.</span><span class="sxs-lookup"><span data-stu-id="cdac7-127">The administrator has all privileges.</span></span> <span data-ttu-id="cdac7-128">Она может читать, изменять и удалять контакты, а также изменять состояние контактов.</span><span class="sxs-lookup"><span data-stu-id="cdac7-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="cdac7-129">Приложение было создано с помощью [формирования шаблонов](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) следующей `Contact` модели:</span><span class="sxs-lookup"><span data-stu-id="cdac7-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="cdac7-130">Этот пример содержит следующие обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="cdac7-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="cdac7-131">`ContactIsOwnerAuthorizationHandler`: Гарантирует, что пользователь может изменять только свои данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="cdac7-132">`ContactManagerAuthorizationHandler`: Позволяет руководителям утверждать или отклонять контакты.</span><span class="sxs-lookup"><span data-stu-id="cdac7-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="cdac7-133">`ContactAdministratorsAuthorizationHandler`: Позволяет администраторам утверждать или отклонять контакты, а также изменять и удалять контакты.</span><span class="sxs-lookup"><span data-stu-id="cdac7-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cdac7-134">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="cdac7-134">Prerequisites</span></span>

<span data-ttu-id="cdac7-135">Этот учебник расширен.</span><span class="sxs-lookup"><span data-stu-id="cdac7-135">This tutorial is advanced.</span></span> <span data-ttu-id="cdac7-136">Вы должны быть знакомы с:</span><span class="sxs-lookup"><span data-stu-id="cdac7-136">You should be familiar with:</span></span>

* [<span data-ttu-id="cdac7-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cdac7-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="cdac7-138">Аутентификация</span><span class="sxs-lookup"><span data-stu-id="cdac7-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="cdac7-139">Подтверждение учетной записи и восстановление пароля</span><span class="sxs-lookup"><span data-stu-id="cdac7-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="cdac7-140">Авторизация</span><span class="sxs-lookup"><span data-stu-id="cdac7-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="cdac7-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="cdac7-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="cdac7-142">Начальное и завершенное приложение</span><span class="sxs-lookup"><span data-stu-id="cdac7-142">The starter and completed app</span></span>

<span data-ttu-id="cdac7-143">[Скачайте](xref:index#how-to-download-a-sample) [готовое](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples) приложение.</span><span class="sxs-lookup"><span data-stu-id="cdac7-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="cdac7-144">[Протестируйте](#test-the-completed-app) готовое приложение, чтобы ознакомиться с его функциями безопасности.</span><span class="sxs-lookup"><span data-stu-id="cdac7-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="cdac7-145">Начальное приложение</span><span class="sxs-lookup"><span data-stu-id="cdac7-145">The starter app</span></span>

<span data-ttu-id="cdac7-146">[Скачайте](xref:index#how-to-download-a-sample) [Начальное](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/) приложение.</span><span class="sxs-lookup"><span data-stu-id="cdac7-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="cdac7-147">Запустите приложение, коснитесь ссылки **ContactManager** и убедитесь, что вы можете создать, изменить и удалить контакт.</span><span class="sxs-lookup"><span data-stu-id="cdac7-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="cdac7-148">Сведения о создании начального приложения см. в разделе [Создание начального приложения](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="cdac7-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="cdac7-149">Защита пользовательских данных</span><span class="sxs-lookup"><span data-stu-id="cdac7-149">Secure user data</span></span>

<span data-ttu-id="cdac7-150">Следующие разделы содержат все основные шаги по созданию безопасного приложения для данных пользователей.</span><span class="sxs-lookup"><span data-stu-id="cdac7-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="cdac7-151">Может оказаться полезным ссылаться на завершенный проект.</span><span class="sxs-lookup"><span data-stu-id="cdac7-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="cdac7-152">Связать контактные данные с пользователем</span><span class="sxs-lookup"><span data-stu-id="cdac7-152">Tie the contact data to the user</span></span>

<span data-ttu-id="cdac7-153">Используйте [Identity](xref:security/authentication/identity) идентификатор пользователя ASP.NET, чтобы убедиться, что пользователи могут изменять данные, но не данные других пользователей.</span><span class="sxs-lookup"><span data-stu-id="cdac7-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="cdac7-154">Добавьте `OwnerID` и `ContactStatus` в `Contact` модель:</span><span class="sxs-lookup"><span data-stu-id="cdac7-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="cdac7-155">`OwnerID` Идентификатор пользователя из `AspNetUser` таблицы в [Identity](xref:security/authentication/identity) базе данных.</span><span class="sxs-lookup"><span data-stu-id="cdac7-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="cdac7-156">`Status`Поле определяет, можно ли просматривать контакт обычными пользователями.</span><span class="sxs-lookup"><span data-stu-id="cdac7-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="cdac7-157">Создайте новую миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="cdac7-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="cdac7-158">Добавление служб ролей в Identity</span><span class="sxs-lookup"><span data-stu-id="cdac7-158">Add Role services to Identity</span></span>

<span data-ttu-id="cdac7-159">Добавление [аддролес](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) для добавления служб ролей:</span><span class="sxs-lookup"><span data-stu-id="cdac7-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="cdac7-160">Требовать прошедших проверку пользователей</span><span class="sxs-lookup"><span data-stu-id="cdac7-160">Require authenticated users</span></span>

<span data-ttu-id="cdac7-161">Настройте политику резервной проверки подлинности, чтобы требовать проверку подлинности пользователей:</span><span class="sxs-lookup"><span data-stu-id="cdac7-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="cdac7-162">Выделенный выше код задает [политику резервной проверки подлинности](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="cdac7-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="cdac7-163">Политика резервной проверки подлинности требует, чтобы ***все*** пользователи прошли проверку подлинности, за исключением Razor страниц, контроллеров или методов действий с атрибутом проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="cdac7-163">The fallback authentication policy requires ***all*** users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="cdac7-164">Например, Razor страницы, контроллеры или методы действий с `[AllowAnonymous]` или `[Authorize(PolicyName="MyPolicy")]` используют примененный атрибут проверки подлинности, а не политику резервной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="cdac7-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="cdac7-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> добавляет <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> к текущему экземпляру, что обеспечивает проверку подлинности текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="cdac7-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

<span data-ttu-id="cdac7-166">Резервная политика проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="cdac7-166">The fallback authentication policy:</span></span>

* <span data-ttu-id="cdac7-167">Применяется ко всем запросам, которые не указывают политику проверки подлинности явным образом.</span><span class="sxs-lookup"><span data-stu-id="cdac7-167">Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="cdac7-168">Для запросов, обслуживаемых маршрутизацией конечных точек, сюда входят все конечные точки, не указывающие атрибут авторизации.</span><span class="sxs-lookup"><span data-stu-id="cdac7-168">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="cdac7-169">Для запросов, обслуживаемых другим по промежуточного слоя, после по промежуточного слоя авторизации, например [статических файлов](xref:fundamentals/static-files), эта политика будет применена ко всем запросам.</span><span class="sxs-lookup"><span data-stu-id="cdac7-169">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="cdac7-170">Настройка политики резервной проверки подлинности на требование проверки подлинности пользователей защищает вновь добавленные Razor страницы и контроллеры.</span><span class="sxs-lookup"><span data-stu-id="cdac7-170">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="cdac7-171">Необходимость проверки подлинности по умолчанию более безопасна, чем использование новых контроллеров и Razor страниц для включения `[Authorize]` атрибута.</span><span class="sxs-lookup"><span data-stu-id="cdac7-171">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="cdac7-172"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>Класс также содержит <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="cdac7-172">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="cdac7-173">`DefaultPolicy`— Это политика, используемая с `[Authorize]` атрибутом, если не указана политика.</span><span class="sxs-lookup"><span data-stu-id="cdac7-173">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="cdac7-174">`[Authorize]` не содержит именованную политику, в отличие от `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="cdac7-174">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="cdac7-175">Дополнительные сведения о политиках см. в разделе <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="cdac7-175">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="cdac7-176">Альтернативный способ для контроллеров и Razor страниц MVC, требующих проверки подлинности всех пользователей, — Добавление фильтра авторизации:</span><span class="sxs-lookup"><span data-stu-id="cdac7-176">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="cdac7-177">В приведенном выше коде используется фильтр авторизации, установка политики резервирования использует маршрутизацию конечных точек.</span><span class="sxs-lookup"><span data-stu-id="cdac7-177">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="cdac7-178">Настройка политики резервирования является предпочтительным способом требовать проверку подлинности всех пользователей.</span><span class="sxs-lookup"><span data-stu-id="cdac7-178">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="cdac7-179">Добавьте [allowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) на `Index` страницы и, `Privacy` чтобы анонимные пользователи могли получить сведения о сайте перед регистрацией:</span><span class="sxs-lookup"><span data-stu-id="cdac7-179">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="cdac7-180">Настройка тестовой учетной записи</span><span class="sxs-lookup"><span data-stu-id="cdac7-180">Configure the test account</span></span>

<span data-ttu-id="cdac7-181">`SeedData`Класс создает две учетные записи: Administrator и Manager.</span><span class="sxs-lookup"><span data-stu-id="cdac7-181">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="cdac7-182">Используйте [средство диспетчера секретов](xref:security/app-secrets) , чтобы задать пароль для этих учетных записей.</span><span class="sxs-lookup"><span data-stu-id="cdac7-182">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="cdac7-183">Задайте пароль из каталога проекта (каталога, содержащего *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="cdac7-183">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="cdac7-184">Если надежный пароль не указан, при вызове вызывается исключение `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="cdac7-184">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="cdac7-185">Обновите `Main` , чтобы использовать тестовый пароль:</span><span class="sxs-lookup"><span data-stu-id="cdac7-185">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="cdac7-186">Создание тестовых учетных записей и обновление контактов</span><span class="sxs-lookup"><span data-stu-id="cdac7-186">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="cdac7-187">Обновите `Initialize` метод в `SeedData` классе, чтобы создать тестовые учетные записи:</span><span class="sxs-lookup"><span data-stu-id="cdac7-187">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="cdac7-188">Добавьте идентификатор пользователя администратора и `ContactStatus` в список контактов.</span><span class="sxs-lookup"><span data-stu-id="cdac7-188">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="cdac7-189">Сделайте одно из контактов "Отправлено" и одно "Отклонено".</span><span class="sxs-lookup"><span data-stu-id="cdac7-189">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="cdac7-190">Добавьте идентификатор пользователя и состояние во все контакты.</span><span class="sxs-lookup"><span data-stu-id="cdac7-190">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="cdac7-191">Отображается только один контакт:</span><span class="sxs-lookup"><span data-stu-id="cdac7-191">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="cdac7-192">Создание обработчиков авторизации владельца, руководителя и администратора</span><span class="sxs-lookup"><span data-stu-id="cdac7-192">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="cdac7-193">Создайте `ContactIsOwnerAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="cdac7-193">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cdac7-194">`ContactIsOwnerAuthorizationHandler`Проверяет, принадлежит ли ресурс пользователю, работающему с ресурсом.</span><span class="sxs-lookup"><span data-stu-id="cdac7-194">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="cdac7-195">`ContactIsOwnerAuthorizationHandler`Контекст вызовов [. Считается успешной](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , если текущий пользователь, прошедший проверку подлинности, является владельцем контакта.</span><span class="sxs-lookup"><span data-stu-id="cdac7-195">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="cdac7-196">Обычно обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="cdac7-196">Authorization handlers generally:</span></span>

* <span data-ttu-id="cdac7-197">Возврат `context.Succeed` при соблюдении требований.</span><span class="sxs-lookup"><span data-stu-id="cdac7-197">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="cdac7-198">Возврат `Task.CompletedTask` , если требования не выполнены.</span><span class="sxs-lookup"><span data-stu-id="cdac7-198">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="cdac7-199">`Task.CompletedTask` не является успешным или неудачным, &mdash; что позволяет запускать другие обработчики авторизации.</span><span class="sxs-lookup"><span data-stu-id="cdac7-199">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="cdac7-200">Если необходимо явное завершение, возвращаемый [контекст. Не пройдено](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="cdac7-200">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="cdac7-201">Приложение позволяет владельцам контактов изменять, удалять и создавать собственные данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-201">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="cdac7-202">`ContactIsOwnerAuthorizationHandler` не требуется проверять операцию, переданную в параметре требования.</span><span class="sxs-lookup"><span data-stu-id="cdac7-202">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="cdac7-203">Создание обработчика авторизации диспетчера</span><span class="sxs-lookup"><span data-stu-id="cdac7-203">Create a manager authorization handler</span></span>

<span data-ttu-id="cdac7-204">Создайте `ContactManagerAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="cdac7-204">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cdac7-205">`ContactManagerAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, диспетчером.</span><span class="sxs-lookup"><span data-stu-id="cdac7-205">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="cdac7-206">Только руководители могут утверждать или отклонять изменения содержимого (новые или измененные).</span><span class="sxs-lookup"><span data-stu-id="cdac7-206">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="cdac7-207">Создание обработчика авторизации администратора</span><span class="sxs-lookup"><span data-stu-id="cdac7-207">Create an administrator authorization handler</span></span>

<span data-ttu-id="cdac7-208">Создайте `ContactAdministratorsAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="cdac7-208">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cdac7-209">`ContactAdministratorsAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, администратором.</span><span class="sxs-lookup"><span data-stu-id="cdac7-209">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="cdac7-210">Администратор может выполнять все операции.</span><span class="sxs-lookup"><span data-stu-id="cdac7-210">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="cdac7-211">Регистрация обработчиков авторизации</span><span class="sxs-lookup"><span data-stu-id="cdac7-211">Register the authorization handlers</span></span>

<span data-ttu-id="cdac7-212">Службы, использующие Entity Framework Core, должны быть зарегистрированы для [внедрения зависимостей](xref:fundamentals/dependency-injection) с помощью [аддскопед](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="cdac7-212">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="cdac7-213">`ContactIsOwnerAuthorizationHandler`Использует ASP.NET Core [Identity](xref:security/authentication/identity) , который построен на Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="cdac7-213">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="cdac7-214">Зарегистрируйте обработчики в коллекции служб, чтобы они были доступны `ContactsController` через [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cdac7-214">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cdac7-215">Добавьте следующий код в конец `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cdac7-215">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="cdac7-216">`ContactAdministratorsAuthorizationHandler` и `ContactManagerAuthorizationHandler` добавляются в виде Singleton.</span><span class="sxs-lookup"><span data-stu-id="cdac7-216">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="cdac7-217">Они являются Singleton-классом, поскольку не используют EF, а вся необходимая информация находится в `Context` параметре `HandleRequirementAsync` метода.</span><span class="sxs-lookup"><span data-stu-id="cdac7-217">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="cdac7-218">Поддержка авторизации</span><span class="sxs-lookup"><span data-stu-id="cdac7-218">Support authorization</span></span>

<span data-ttu-id="cdac7-219">В этом разделе вы обновите Razor страницы и добавите класс требований к операциям.</span><span class="sxs-lookup"><span data-stu-id="cdac7-219">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="cdac7-220">Ознакомьтесь с классом требований к операциям</span><span class="sxs-lookup"><span data-stu-id="cdac7-220">Review the contact operations requirements class</span></span>

<span data-ttu-id="cdac7-221">Проверьте `ContactOperations` класс.</span><span class="sxs-lookup"><span data-stu-id="cdac7-221">Review the `ContactOperations` class.</span></span> <span data-ttu-id="cdac7-222">Этот класс содержит требования, которые поддерживает приложение:</span><span class="sxs-lookup"><span data-stu-id="cdac7-222">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="cdac7-223">Создание базового класса для Razor страниц контактов</span><span class="sxs-lookup"><span data-stu-id="cdac7-223">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="cdac7-224">Создайте базовый класс, содержащий службы, используемые на Razor страницах контактов.</span><span class="sxs-lookup"><span data-stu-id="cdac7-224">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="cdac7-225">Базовый класс помещает код инициализации в одно расположение:</span><span class="sxs-lookup"><span data-stu-id="cdac7-225">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="cdac7-226">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="cdac7-226">The preceding code:</span></span>

* <span data-ttu-id="cdac7-227">Добавляет `IAuthorizationService` службу для доступа к обработчикам авторизации.</span><span class="sxs-lookup"><span data-stu-id="cdac7-227">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="cdac7-228">Добавляет Identity `UserManager` службу.</span><span class="sxs-lookup"><span data-stu-id="cdac7-228">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="cdac7-229">Добавьте `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="cdac7-229">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="cdac7-230">Обновление Креатемодел</span><span class="sxs-lookup"><span data-stu-id="cdac7-230">Update the CreateModel</span></span>

<span data-ttu-id="cdac7-231">Обновите конструктор модели страницы Create, чтобы использовать `DI_BasePageModel` базовый класс:</span><span class="sxs-lookup"><span data-stu-id="cdac7-231">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="cdac7-232">Обновите `CreateModel.OnPostAsync` метод, чтобы:</span><span class="sxs-lookup"><span data-stu-id="cdac7-232">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="cdac7-233">Добавьте в модель идентификатор пользователя `Contact` .</span><span class="sxs-lookup"><span data-stu-id="cdac7-233">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="cdac7-234">Вызовите обработчик авторизации, чтобы убедиться, что пользователь имеет разрешение на создание контактов.</span><span class="sxs-lookup"><span data-stu-id="cdac7-234">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="cdac7-235">Обновление Индексмодел</span><span class="sxs-lookup"><span data-stu-id="cdac7-235">Update the IndexModel</span></span>

<span data-ttu-id="cdac7-236">Обновите `OnGetAsync` метод, чтобы только утвержденные контакты отображались для обычных пользователей:</span><span class="sxs-lookup"><span data-stu-id="cdac7-236">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="cdac7-237">Обновление Едитмодел</span><span class="sxs-lookup"><span data-stu-id="cdac7-237">Update the EditModel</span></span>

<span data-ttu-id="cdac7-238">Добавьте обработчик авторизации, чтобы убедиться, что пользователь владеет контактом.</span><span class="sxs-lookup"><span data-stu-id="cdac7-238">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="cdac7-239">Так как авторизация ресурса проверяется, `[Authorize]` атрибут недостаточно.</span><span class="sxs-lookup"><span data-stu-id="cdac7-239">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="cdac7-240">Приложение не имеет доступа к ресурсу при оценке атрибутов.</span><span class="sxs-lookup"><span data-stu-id="cdac7-240">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="cdac7-241">Авторизация на основе ресурсов должна быть принудительной.</span><span class="sxs-lookup"><span data-stu-id="cdac7-241">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="cdac7-242">Проверки должны выполняться после того, как приложение будет иметь доступ к ресурсу, загрузив его в модель страницы или загрузив в сам обработчик.</span><span class="sxs-lookup"><span data-stu-id="cdac7-242">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="cdac7-243">Вы часто обращаетесь к ресурсу, передавая ключ ресурса.</span><span class="sxs-lookup"><span data-stu-id="cdac7-243">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="cdac7-244">Обновление Делетемодел</span><span class="sxs-lookup"><span data-stu-id="cdac7-244">Update the DeleteModel</span></span>

<span data-ttu-id="cdac7-245">Обновите модель страницы удаления, чтобы использовать обработчик авторизации для проверки наличия у пользователя разрешения на удаление контакта.</span><span class="sxs-lookup"><span data-stu-id="cdac7-245">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="cdac7-246">Внедрение службы авторизации в представления</span><span class="sxs-lookup"><span data-stu-id="cdac7-246">Inject the authorization service into the views</span></span>

<span data-ttu-id="cdac7-247">В настоящее время в пользовательском интерфейсе отображаются ссылки Edit и DELETE для контактов, которые пользователь не может изменить.</span><span class="sxs-lookup"><span data-stu-id="cdac7-247">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="cdac7-248">Вставьте службу авторизации в файл *pages/_ViewImports. cshtml* , чтобы он был доступен для всех представлений:</span><span class="sxs-lookup"><span data-stu-id="cdac7-248">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="cdac7-249">Предыдущая разметка добавляет несколько `using` инструкций.</span><span class="sxs-lookup"><span data-stu-id="cdac7-249">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="cdac7-250">Обновите ссылки **Edit** и **Delete** в *pages/contacts/index. cshtml* , чтобы они отображались только для пользователей с соответствующими разрешениями:</span><span class="sxs-lookup"><span data-stu-id="cdac7-250">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="cdac7-251">Скрытие ссылок от пользователей, не имеющих разрешений на изменение данных, не защищает приложение.</span><span class="sxs-lookup"><span data-stu-id="cdac7-251">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="cdac7-252">Скрытие ссылок делает приложение более удобным для пользователей, отображая только допустимые ссылки.</span><span class="sxs-lookup"><span data-stu-id="cdac7-252">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="cdac7-253">Пользователи могут обращаться к созданным URL-адресам для вызова операций правки и удаления данных, которыми они не владеют.</span><span class="sxs-lookup"><span data-stu-id="cdac7-253">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="cdac7-254">RazorСтраница или контроллер должны применять проверки доступа для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="cdac7-254">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="cdac7-255">Сведения об обновлении</span><span class="sxs-lookup"><span data-stu-id="cdac7-255">Update Details</span></span>

<span data-ttu-id="cdac7-256">Обновите представление сведений, чтобы руководители могли утверждать или отклонять контакты:</span><span class="sxs-lookup"><span data-stu-id="cdac7-256">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="cdac7-257">Обновите модель страницы сведений:</span><span class="sxs-lookup"><span data-stu-id="cdac7-257">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="cdac7-258">Добавление или удаление пользователя для роли</span><span class="sxs-lookup"><span data-stu-id="cdac7-258">Add or remove a user to a role</span></span>

<span data-ttu-id="cdac7-259">Сведения об [этой ошибке](https://github.com/dotnet/AspNetCore.Docs/issues/8502) см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="cdac7-259">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="cdac7-260">Удаление привилегий у пользователя.</span><span class="sxs-lookup"><span data-stu-id="cdac7-260">Removing privileges from a user.</span></span> <span data-ttu-id="cdac7-261">Например, отзвука пользователя в приложении разговора.</span><span class="sxs-lookup"><span data-stu-id="cdac7-261">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="cdac7-262">Добавление привилегий для пользователя.</span><span class="sxs-lookup"><span data-stu-id="cdac7-262">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="cdac7-263">Различия между запросом и запретом</span><span class="sxs-lookup"><span data-stu-id="cdac7-263">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="cdac7-264">Это приложение задает политику по умолчанию, [требующую проверки подлинности пользователей](#rau).</span><span class="sxs-lookup"><span data-stu-id="cdac7-264">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="cdac7-265">Следующий код позволяет анонимным пользователям.</span><span class="sxs-lookup"><span data-stu-id="cdac7-265">The following code allows anonymous users.</span></span> <span data-ttu-id="cdac7-266">Анонимным пользователям разрешено показывать различия между запросом и запретом.</span><span class="sxs-lookup"><span data-stu-id="cdac7-266">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="cdac7-267">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="cdac7-267">In the preceding code:</span></span>

* <span data-ttu-id="cdac7-268">Если пользователь **не** прошел проверку подлинности, `ChallengeResult` возвращается значение.</span><span class="sxs-lookup"><span data-stu-id="cdac7-268">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="cdac7-269">Когда `ChallengeResult` возвращается, пользователь перенаправляется на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="cdac7-269">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="cdac7-270">Если пользователь прошел проверку подлинности, но не авторизован, `ForbidResult` возвращается значение.</span><span class="sxs-lookup"><span data-stu-id="cdac7-270">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="cdac7-271">Когда `ForbidResult` возвращается значение, пользователь перенаправляется на страницу отказ в доступе.</span><span class="sxs-lookup"><span data-stu-id="cdac7-271">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="cdac7-272">Тестирование завершенного приложения</span><span class="sxs-lookup"><span data-stu-id="cdac7-272">Test the completed app</span></span>

<span data-ttu-id="cdac7-273">Если вы еще не установили пароль для заполненных учетных записей пользователей, используйте [средство диспетчера секретов](xref:security/app-secrets#secret-manager) , чтобы задать пароль:</span><span class="sxs-lookup"><span data-stu-id="cdac7-273">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="cdac7-274">Выберите надежный пароль: используйте восемь или более символов и по крайней мере одну прописную букву, цифру и символ.</span><span class="sxs-lookup"><span data-stu-id="cdac7-274">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="cdac7-275">Например, `Passw0rd!` соответствует требованиям к надежному паролю.</span><span class="sxs-lookup"><span data-stu-id="cdac7-275">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="cdac7-276">Выполните следующую команду из папки проекта, где `<PW>` — пароль:</span><span class="sxs-lookup"><span data-stu-id="cdac7-276">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="cdac7-277">Если у приложения есть контакты:</span><span class="sxs-lookup"><span data-stu-id="cdac7-277">If the app has contacts:</span></span>

* <span data-ttu-id="cdac7-278">Удалите все записи в `Contact` таблице.</span><span class="sxs-lookup"><span data-stu-id="cdac7-278">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="cdac7-279">Перезапустите приложение, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="cdac7-279">Restart the app to seed the database.</span></span>

<span data-ttu-id="cdac7-280">Простой способ тестирования завершенного приложения — запуск трех различных браузеров (или режиме инкогнито и нечастных сеансов).</span><span class="sxs-lookup"><span data-stu-id="cdac7-280">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="cdac7-281">В одном браузере Зарегистрируйте нового пользователя (например, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="cdac7-281">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="cdac7-282">Войдите в каждый браузер с другим пользователем.</span><span class="sxs-lookup"><span data-stu-id="cdac7-282">Sign in to each browser with a different user.</span></span> <span data-ttu-id="cdac7-283">Проверьте следующие операции:</span><span class="sxs-lookup"><span data-stu-id="cdac7-283">Verify the following operations:</span></span>

* <span data-ttu-id="cdac7-284">Зарегистрированные пользователи могут просматривать все утвержденные контактные данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-284">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="cdac7-285">Зарегистрированные пользователи могут изменять и удалять собственные данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-285">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="cdac7-286">Руководители могут утверждать и отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-286">Managers can approve/reject contact data.</span></span> <span data-ttu-id="cdac7-287">В `Details` представлении отображаются кнопки **утвердить** и **отклонить** .</span><span class="sxs-lookup"><span data-stu-id="cdac7-287">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="cdac7-288">Администраторы могут утверждать, отклонять и изменять и удалять все данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-288">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="cdac7-289">Пользователь</span><span class="sxs-lookup"><span data-stu-id="cdac7-289">User</span></span>                | <span data-ttu-id="cdac7-290">Заполнено приложением</span><span class="sxs-lookup"><span data-stu-id="cdac7-290">Seeded by the app</span></span> | <span data-ttu-id="cdac7-291">Варианты</span><span class="sxs-lookup"><span data-stu-id="cdac7-291">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="cdac7-292">Нет</span><span class="sxs-lookup"><span data-stu-id="cdac7-292">No</span></span>                | <span data-ttu-id="cdac7-293">Изменение или удаление собственных данных.</span><span class="sxs-lookup"><span data-stu-id="cdac7-293">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="cdac7-294">Да</span><span class="sxs-lookup"><span data-stu-id="cdac7-294">Yes</span></span>               | <span data-ttu-id="cdac7-295">Утвердите, отклоните и измените или удалите собственные данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-295">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="cdac7-296">Да</span><span class="sxs-lookup"><span data-stu-id="cdac7-296">Yes</span></span>               | <span data-ttu-id="cdac7-297">Утвердите или отклоните и измените или удалите все данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-297">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="cdac7-298">Создайте контакт в браузере администратора.</span><span class="sxs-lookup"><span data-stu-id="cdac7-298">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="cdac7-299">Скопируйте URL-адрес для DELETE и Edit из контакта администратора.</span><span class="sxs-lookup"><span data-stu-id="cdac7-299">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="cdac7-300">Вставьте эти ссылки в браузер тестового пользователя, чтобы убедиться, что тестовая пользователь не может выполнить эти операции.</span><span class="sxs-lookup"><span data-stu-id="cdac7-300">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="cdac7-301">Создание начального приложения</span><span class="sxs-lookup"><span data-stu-id="cdac7-301">Create the starter app</span></span>

* <span data-ttu-id="cdac7-302">Создание Razor страницы приложения с именем "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="cdac7-302">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="cdac7-303">Создайте приложение с **учетными записями отдельных пользователей**.</span><span class="sxs-lookup"><span data-stu-id="cdac7-303">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="cdac7-304">Назовите его "ContactManager", чтобы пространство имен совпадало с пространством имен, используемым в примере.</span><span class="sxs-lookup"><span data-stu-id="cdac7-304">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="cdac7-305">`-uld` Указывает LocalDB вместо SQLite</span><span class="sxs-lookup"><span data-stu-id="cdac7-305">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="cdac7-306">Добавление *моделей/Contact. CS*:</span><span class="sxs-lookup"><span data-stu-id="cdac7-306">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="cdac7-307">Формирование шаблонов `Contact` модели.</span><span class="sxs-lookup"><span data-stu-id="cdac7-307">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="cdac7-308">Создайте начальную миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="cdac7-308">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="cdac7-309">Если при выполнении команды возникла ошибка `dotnet aspnet-codegenerator razorpage` , см. [эту ошибку в GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="cdac7-309">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="cdac7-310">Обновите привязку **ContactManager** в файле *pages/shared/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="cdac7-310">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="cdac7-311">Тестирование приложения путем создания, изменения и удаления контакта</span><span class="sxs-lookup"><span data-stu-id="cdac7-311">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="cdac7-312">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="cdac7-312">Seed the database</span></span>

<span data-ttu-id="cdac7-313">Добавьте класс [сиддата](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) в папку *Data* :</span><span class="sxs-lookup"><span data-stu-id="cdac7-313">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="cdac7-314">Вызов `SeedData.Initialize` из `Main` :</span><span class="sxs-lookup"><span data-stu-id="cdac7-314">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="cdac7-315">Проверьте, что приложение заполнено базой данных.</span><span class="sxs-lookup"><span data-stu-id="cdac7-315">Test that the app seeded the database.</span></span> <span data-ttu-id="cdac7-316">Если в базе данных Contact есть какие бы то ни было строки, метод SEED не выполняется.</span><span class="sxs-lookup"><span data-stu-id="cdac7-316">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="cdac7-317">В этом руководстве показано, как создать веб-приложение ASP.NET Core с данными пользователя, защищенными с помощью авторизации.</span><span class="sxs-lookup"><span data-stu-id="cdac7-317">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="cdac7-318">Отображается список контактов, которые были созданы пользователями, прошедшими проверку подлинности (зарегистрированные).</span><span class="sxs-lookup"><span data-stu-id="cdac7-318">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="cdac7-319">Существует три группы безопасности:</span><span class="sxs-lookup"><span data-stu-id="cdac7-319">There are three security groups:</span></span>

* <span data-ttu-id="cdac7-320">**Зарегистрированные пользователи** могут просматривать все утвержденные данные, а также изменять и удалять свои данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-320">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="cdac7-321">**Руководители** могут утверждать или отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-321">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="cdac7-322">Пользователям видны только утвержденные контакты.</span><span class="sxs-lookup"><span data-stu-id="cdac7-322">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="cdac7-323">**Администраторы** могут утверждать, отклонять и изменять и удалять любые данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-323">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="cdac7-324">На следующем рисунке пользователь Рик ( `rick@example.com` ) вошел в.</span><span class="sxs-lookup"><span data-stu-id="cdac7-324">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="cdac7-325">Рик может просматривать только утвержденные контакты и **редактировать** / **Удалить** / **создать новые** ссылки для своих контактов.</span><span class="sxs-lookup"><span data-stu-id="cdac7-325">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="cdac7-326">Ссылки **Edit** и **Delete** отображаются только в последней записи, созданной Рик.</span><span class="sxs-lookup"><span data-stu-id="cdac7-326">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="cdac7-327">Другие пользователи не увидят последнюю запись, пока руководитель или администратор не изменит состояние на "утверждено".</span><span class="sxs-lookup"><span data-stu-id="cdac7-327">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Снимок экрана, показывающий Рик, выполнивший вход](secure-data/_static/rick.png)

<span data-ttu-id="cdac7-329">На следующем рисунке `manager@contoso.com` Вход выполнен и в роли руководителя:</span><span class="sxs-lookup"><span data-stu-id="cdac7-329">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Снимок экрана, показывающий manager@contoso.com Вход](secure-data/_static/manager1.png)

<span data-ttu-id="cdac7-331">На следующем рисунке показано представление сведений об менеджерах для контакта:</span><span class="sxs-lookup"><span data-stu-id="cdac7-331">The following image shows the managers details view of a contact:</span></span>

![Представление контакта руководителя](secure-data/_static/manager.png)

<span data-ttu-id="cdac7-333">Кнопки **утвердить** и **отклонить** отображаются только для руководителей и администраторов.</span><span class="sxs-lookup"><span data-stu-id="cdac7-333">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="cdac7-334">На следующем рисунке `admin@contoso.com` Вход выполнен и в роли администратора:</span><span class="sxs-lookup"><span data-stu-id="cdac7-334">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Снимок экрана, показывающий admin@contoso.com Вход](secure-data/_static/admin.png)

<span data-ttu-id="cdac7-336">Администратор имеет все привилегии.</span><span class="sxs-lookup"><span data-stu-id="cdac7-336">The administrator has all privileges.</span></span> <span data-ttu-id="cdac7-337">Она может читать, изменять и удалять контакты, а также изменять состояние контактов.</span><span class="sxs-lookup"><span data-stu-id="cdac7-337">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="cdac7-338">Приложение было создано с помощью [формирования шаблонов](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) следующей `Contact` модели:</span><span class="sxs-lookup"><span data-stu-id="cdac7-338">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="cdac7-339">Этот пример содержит следующие обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="cdac7-339">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="cdac7-340">`ContactIsOwnerAuthorizationHandler`: Гарантирует, что пользователь может изменять только свои данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-340">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="cdac7-341">`ContactManagerAuthorizationHandler`: Позволяет руководителям утверждать или отклонять контакты.</span><span class="sxs-lookup"><span data-stu-id="cdac7-341">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="cdac7-342">`ContactAdministratorsAuthorizationHandler`: Позволяет администраторам утверждать или отклонять контакты, а также изменять и удалять контакты.</span><span class="sxs-lookup"><span data-stu-id="cdac7-342">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cdac7-343">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="cdac7-343">Prerequisites</span></span>

<span data-ttu-id="cdac7-344">Этот учебник расширен.</span><span class="sxs-lookup"><span data-stu-id="cdac7-344">This tutorial is advanced.</span></span> <span data-ttu-id="cdac7-345">Вы должны быть знакомы с:</span><span class="sxs-lookup"><span data-stu-id="cdac7-345">You should be familiar with:</span></span>

* [<span data-ttu-id="cdac7-346">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cdac7-346">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="cdac7-347">Аутентификация</span><span class="sxs-lookup"><span data-stu-id="cdac7-347">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="cdac7-348">Подтверждение учетной записи и восстановление пароля</span><span class="sxs-lookup"><span data-stu-id="cdac7-348">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="cdac7-349">Авторизация</span><span class="sxs-lookup"><span data-stu-id="cdac7-349">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="cdac7-350">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="cdac7-350">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="cdac7-351">Начальное и завершенное приложение</span><span class="sxs-lookup"><span data-stu-id="cdac7-351">The starter and completed app</span></span>

<span data-ttu-id="cdac7-352">[Скачайте](xref:index#how-to-download-a-sample) [готовое](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples) приложение.</span><span class="sxs-lookup"><span data-stu-id="cdac7-352">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="cdac7-353">[Протестируйте](#test-the-completed-app) готовое приложение, чтобы ознакомиться с его функциями безопасности.</span><span class="sxs-lookup"><span data-stu-id="cdac7-353">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="cdac7-354">Начальное приложение</span><span class="sxs-lookup"><span data-stu-id="cdac7-354">The starter app</span></span>

<span data-ttu-id="cdac7-355">[Скачайте](xref:index#how-to-download-a-sample) [Начальное](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/) приложение.</span><span class="sxs-lookup"><span data-stu-id="cdac7-355">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="cdac7-356">Запустите приложение, коснитесь ссылки **ContactManager** и убедитесь, что вы можете создать, изменить и удалить контакт.</span><span class="sxs-lookup"><span data-stu-id="cdac7-356">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="cdac7-357">Защита пользовательских данных</span><span class="sxs-lookup"><span data-stu-id="cdac7-357">Secure user data</span></span>

<span data-ttu-id="cdac7-358">Следующие разделы содержат все основные шаги по созданию безопасного приложения для данных пользователей.</span><span class="sxs-lookup"><span data-stu-id="cdac7-358">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="cdac7-359">Может оказаться полезным ссылаться на завершенный проект.</span><span class="sxs-lookup"><span data-stu-id="cdac7-359">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="cdac7-360">Связать контактные данные с пользователем</span><span class="sxs-lookup"><span data-stu-id="cdac7-360">Tie the contact data to the user</span></span>

<span data-ttu-id="cdac7-361">Используйте [Identity](xref:security/authentication/identity) идентификатор пользователя ASP.NET, чтобы убедиться, что пользователи могут изменять данные, но не данные других пользователей.</span><span class="sxs-lookup"><span data-stu-id="cdac7-361">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="cdac7-362">Добавьте `OwnerID` и `ContactStatus` в `Contact` модель:</span><span class="sxs-lookup"><span data-stu-id="cdac7-362">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="cdac7-363">`OwnerID` Идентификатор пользователя из `AspNetUser` таблицы в [Identity](xref:security/authentication/identity) базе данных.</span><span class="sxs-lookup"><span data-stu-id="cdac7-363">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="cdac7-364">`Status`Поле определяет, можно ли просматривать контакт обычными пользователями.</span><span class="sxs-lookup"><span data-stu-id="cdac7-364">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="cdac7-365">Создайте новую миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="cdac7-365">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="cdac7-366">Добавление служб ролей в Identity</span><span class="sxs-lookup"><span data-stu-id="cdac7-366">Add Role services to Identity</span></span>

<span data-ttu-id="cdac7-367">Добавление [аддролес](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) для добавления служб ролей:</span><span class="sxs-lookup"><span data-stu-id="cdac7-367">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="cdac7-368">Требовать прошедших проверку пользователей</span><span class="sxs-lookup"><span data-stu-id="cdac7-368">Require authenticated users</span></span>

<span data-ttu-id="cdac7-369">Задайте политику проверки подлинности по умолчанию, чтобы требовать проверку подлинности пользователей:</span><span class="sxs-lookup"><span data-stu-id="cdac7-369">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="cdac7-370">Можно отказаться от проверки подлинности на Razor уровне страницы, контроллера или метода действия с помощью `[AllowAnonymous]` атрибута.</span><span class="sxs-lookup"><span data-stu-id="cdac7-370">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="cdac7-371">Настройка политики проверки подлинности по умолчанию для обязательной проверки подлинности пользователей защищает вновь добавленные Razor страницы и контроллеры.</span><span class="sxs-lookup"><span data-stu-id="cdac7-371">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="cdac7-372">Необходимость проверки подлинности по умолчанию более безопасна, чем использование новых контроллеров и Razor страниц для включения `[Authorize]` атрибута.</span><span class="sxs-lookup"><span data-stu-id="cdac7-372">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="cdac7-373">Добавьте [allowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) в индекс, About и Contact Pages, чтобы анонимные пользователи могли получить сведения о сайте перед регистрацией.</span><span class="sxs-lookup"><span data-stu-id="cdac7-373">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="cdac7-374">Настройка тестовой учетной записи</span><span class="sxs-lookup"><span data-stu-id="cdac7-374">Configure the test account</span></span>

<span data-ttu-id="cdac7-375">`SeedData`Класс создает две учетные записи: Administrator и Manager.</span><span class="sxs-lookup"><span data-stu-id="cdac7-375">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="cdac7-376">Используйте [средство диспетчера секретов](xref:security/app-secrets) , чтобы задать пароль для этих учетных записей.</span><span class="sxs-lookup"><span data-stu-id="cdac7-376">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="cdac7-377">Задайте пароль из каталога проекта (каталога, содержащего *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="cdac7-377">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="cdac7-378">Если надежный пароль не указан, при вызове вызывается исключение `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="cdac7-378">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="cdac7-379">Обновите `Main` , чтобы использовать тестовый пароль:</span><span class="sxs-lookup"><span data-stu-id="cdac7-379">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="cdac7-380">Создание тестовых учетных записей и обновление контактов</span><span class="sxs-lookup"><span data-stu-id="cdac7-380">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="cdac7-381">Обновите `Initialize` метод в `SeedData` классе, чтобы создать тестовые учетные записи:</span><span class="sxs-lookup"><span data-stu-id="cdac7-381">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="cdac7-382">Добавьте идентификатор пользователя администратора и `ContactStatus` в список контактов.</span><span class="sxs-lookup"><span data-stu-id="cdac7-382">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="cdac7-383">Сделайте одно из контактов "Отправлено" и одно "Отклонено".</span><span class="sxs-lookup"><span data-stu-id="cdac7-383">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="cdac7-384">Добавьте идентификатор пользователя и состояние во все контакты.</span><span class="sxs-lookup"><span data-stu-id="cdac7-384">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="cdac7-385">Отображается только один контакт:</span><span class="sxs-lookup"><span data-stu-id="cdac7-385">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="cdac7-386">Создание обработчиков авторизации владельца, руководителя и администратора</span><span class="sxs-lookup"><span data-stu-id="cdac7-386">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="cdac7-387">Создайте папку *авторизации* и создайте `ContactIsOwnerAuthorizationHandler` в ней класс.</span><span class="sxs-lookup"><span data-stu-id="cdac7-387">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="cdac7-388">`ContactIsOwnerAuthorizationHandler`Проверяет, принадлежит ли ресурс пользователю, работающему с ресурсом.</span><span class="sxs-lookup"><span data-stu-id="cdac7-388">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="cdac7-389">`ContactIsOwnerAuthorizationHandler`Контекст вызовов [. Считается успешной](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , если текущий пользователь, прошедший проверку подлинности, является владельцем контакта.</span><span class="sxs-lookup"><span data-stu-id="cdac7-389">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="cdac7-390">Обычно обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="cdac7-390">Authorization handlers generally:</span></span>

* <span data-ttu-id="cdac7-391">Возврат `context.Succeed` при соблюдении требований.</span><span class="sxs-lookup"><span data-stu-id="cdac7-391">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="cdac7-392">Возврат `Task.CompletedTask` , если требования не выполнены.</span><span class="sxs-lookup"><span data-stu-id="cdac7-392">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="cdac7-393">`Task.CompletedTask` не является успешным или неудачным, &mdash; что позволяет запускать другие обработчики авторизации.</span><span class="sxs-lookup"><span data-stu-id="cdac7-393">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="cdac7-394">Если необходимо явное завершение, возвращаемый [контекст. Не пройдено](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="cdac7-394">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="cdac7-395">Приложение позволяет владельцам контактов изменять, удалять и создавать собственные данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-395">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="cdac7-396">`ContactIsOwnerAuthorizationHandler` не требуется проверять операцию, переданную в параметре требования.</span><span class="sxs-lookup"><span data-stu-id="cdac7-396">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="cdac7-397">Создание обработчика авторизации диспетчера</span><span class="sxs-lookup"><span data-stu-id="cdac7-397">Create a manager authorization handler</span></span>

<span data-ttu-id="cdac7-398">Создайте `ContactManagerAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="cdac7-398">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cdac7-399">`ContactManagerAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, диспетчером.</span><span class="sxs-lookup"><span data-stu-id="cdac7-399">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="cdac7-400">Только руководители могут утверждать или отклонять изменения содержимого (новые или измененные).</span><span class="sxs-lookup"><span data-stu-id="cdac7-400">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="cdac7-401">Создание обработчика авторизации администратора</span><span class="sxs-lookup"><span data-stu-id="cdac7-401">Create an administrator authorization handler</span></span>

<span data-ttu-id="cdac7-402">Создайте `ContactAdministratorsAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="cdac7-402">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cdac7-403">`ContactAdministratorsAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, администратором.</span><span class="sxs-lookup"><span data-stu-id="cdac7-403">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="cdac7-404">Администратор может выполнять все операции.</span><span class="sxs-lookup"><span data-stu-id="cdac7-404">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="cdac7-405">Регистрация обработчиков авторизации</span><span class="sxs-lookup"><span data-stu-id="cdac7-405">Register the authorization handlers</span></span>

<span data-ttu-id="cdac7-406">Службы, использующие Entity Framework Core, должны быть зарегистрированы для [внедрения зависимостей](xref:fundamentals/dependency-injection) с помощью [аддскопед](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="cdac7-406">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="cdac7-407">`ContactIsOwnerAuthorizationHandler`Использует ASP.NET Core [Identity](xref:security/authentication/identity) , который построен на Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="cdac7-407">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="cdac7-408">Зарегистрируйте обработчики в коллекции служб, чтобы они были доступны `ContactsController` через [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cdac7-408">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cdac7-409">Добавьте следующий код в конец `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cdac7-409">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="cdac7-410">`ContactAdministratorsAuthorizationHandler` и `ContactManagerAuthorizationHandler` добавляются в виде Singleton.</span><span class="sxs-lookup"><span data-stu-id="cdac7-410">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="cdac7-411">Они являются Singleton-классом, поскольку не используют EF, а вся необходимая информация находится в `Context` параметре `HandleRequirementAsync` метода.</span><span class="sxs-lookup"><span data-stu-id="cdac7-411">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="cdac7-412">Поддержка авторизации</span><span class="sxs-lookup"><span data-stu-id="cdac7-412">Support authorization</span></span>

<span data-ttu-id="cdac7-413">В этом разделе вы обновите Razor страницы и добавите класс требований к операциям.</span><span class="sxs-lookup"><span data-stu-id="cdac7-413">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="cdac7-414">Ознакомьтесь с классом требований к операциям</span><span class="sxs-lookup"><span data-stu-id="cdac7-414">Review the contact operations requirements class</span></span>

<span data-ttu-id="cdac7-415">Проверьте `ContactOperations` класс.</span><span class="sxs-lookup"><span data-stu-id="cdac7-415">Review the `ContactOperations` class.</span></span> <span data-ttu-id="cdac7-416">Этот класс содержит требования, которые поддерживает приложение:</span><span class="sxs-lookup"><span data-stu-id="cdac7-416">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="cdac7-417">Создание базового класса для Razor страниц контактов</span><span class="sxs-lookup"><span data-stu-id="cdac7-417">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="cdac7-418">Создайте базовый класс, содержащий службы, используемые на Razor страницах контактов.</span><span class="sxs-lookup"><span data-stu-id="cdac7-418">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="cdac7-419">Базовый класс помещает код инициализации в одно расположение:</span><span class="sxs-lookup"><span data-stu-id="cdac7-419">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="cdac7-420">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="cdac7-420">The preceding code:</span></span>

* <span data-ttu-id="cdac7-421">Добавляет `IAuthorizationService` службу для доступа к обработчикам авторизации.</span><span class="sxs-lookup"><span data-stu-id="cdac7-421">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="cdac7-422">Добавляет Identity `UserManager` службу.</span><span class="sxs-lookup"><span data-stu-id="cdac7-422">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="cdac7-423">Добавьте `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="cdac7-423">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="cdac7-424">Обновление Креатемодел</span><span class="sxs-lookup"><span data-stu-id="cdac7-424">Update the CreateModel</span></span>

<span data-ttu-id="cdac7-425">Обновите конструктор модели страницы Create, чтобы использовать `DI_BasePageModel` базовый класс:</span><span class="sxs-lookup"><span data-stu-id="cdac7-425">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="cdac7-426">Обновите `CreateModel.OnPostAsync` метод, чтобы:</span><span class="sxs-lookup"><span data-stu-id="cdac7-426">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="cdac7-427">Добавьте в модель идентификатор пользователя `Contact` .</span><span class="sxs-lookup"><span data-stu-id="cdac7-427">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="cdac7-428">Вызовите обработчик авторизации, чтобы убедиться, что пользователь имеет разрешение на создание контактов.</span><span class="sxs-lookup"><span data-stu-id="cdac7-428">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="cdac7-429">Обновление Индексмодел</span><span class="sxs-lookup"><span data-stu-id="cdac7-429">Update the IndexModel</span></span>

<span data-ttu-id="cdac7-430">Обновите `OnGetAsync` метод, чтобы только утвержденные контакты отображались для обычных пользователей:</span><span class="sxs-lookup"><span data-stu-id="cdac7-430">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="cdac7-431">Обновление Едитмодел</span><span class="sxs-lookup"><span data-stu-id="cdac7-431">Update the EditModel</span></span>

<span data-ttu-id="cdac7-432">Добавьте обработчик авторизации, чтобы убедиться, что пользователь владеет контактом.</span><span class="sxs-lookup"><span data-stu-id="cdac7-432">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="cdac7-433">Так как авторизация ресурса проверяется, `[Authorize]` атрибут недостаточно.</span><span class="sxs-lookup"><span data-stu-id="cdac7-433">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="cdac7-434">Приложение не имеет доступа к ресурсу при оценке атрибутов.</span><span class="sxs-lookup"><span data-stu-id="cdac7-434">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="cdac7-435">Авторизация на основе ресурсов должна быть принудительной.</span><span class="sxs-lookup"><span data-stu-id="cdac7-435">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="cdac7-436">Проверки должны выполняться после того, как приложение будет иметь доступ к ресурсу, загрузив его в модель страницы или загрузив в сам обработчик.</span><span class="sxs-lookup"><span data-stu-id="cdac7-436">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="cdac7-437">Вы часто обращаетесь к ресурсу, передавая ключ ресурса.</span><span class="sxs-lookup"><span data-stu-id="cdac7-437">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="cdac7-438">Обновление Делетемодел</span><span class="sxs-lookup"><span data-stu-id="cdac7-438">Update the DeleteModel</span></span>

<span data-ttu-id="cdac7-439">Обновите модель страницы удаления, чтобы использовать обработчик авторизации для проверки наличия у пользователя разрешения на удаление контакта.</span><span class="sxs-lookup"><span data-stu-id="cdac7-439">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="cdac7-440">Внедрение службы авторизации в представления</span><span class="sxs-lookup"><span data-stu-id="cdac7-440">Inject the authorization service into the views</span></span>

<span data-ttu-id="cdac7-441">В настоящее время в пользовательском интерфейсе отображаются ссылки Edit и DELETE для контактов, которые пользователь не может изменить.</span><span class="sxs-lookup"><span data-stu-id="cdac7-441">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="cdac7-442">Вставьте службу авторизации в файл *views/_ViewImports. cshtml* , чтобы она была доступна для всех представлений:</span><span class="sxs-lookup"><span data-stu-id="cdac7-442">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="cdac7-443">Предыдущая разметка добавляет несколько `using` инструкций.</span><span class="sxs-lookup"><span data-stu-id="cdac7-443">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="cdac7-444">Обновите ссылки **Edit** и **Delete** в *pages/contacts/index. cshtml* , чтобы они отображались только для пользователей с соответствующими разрешениями:</span><span class="sxs-lookup"><span data-stu-id="cdac7-444">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="cdac7-445">Скрытие ссылок от пользователей, не имеющих разрешений на изменение данных, не защищает приложение.</span><span class="sxs-lookup"><span data-stu-id="cdac7-445">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="cdac7-446">Скрытие ссылок делает приложение более удобным для пользователей, отображая только допустимые ссылки.</span><span class="sxs-lookup"><span data-stu-id="cdac7-446">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="cdac7-447">Пользователи могут обращаться к созданным URL-адресам для вызова операций правки и удаления данных, которыми они не владеют.</span><span class="sxs-lookup"><span data-stu-id="cdac7-447">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="cdac7-448">RazorСтраница или контроллер должны применять проверки доступа для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="cdac7-448">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="cdac7-449">Сведения об обновлении</span><span class="sxs-lookup"><span data-stu-id="cdac7-449">Update Details</span></span>

<span data-ttu-id="cdac7-450">Обновите представление сведений, чтобы руководители могли утверждать или отклонять контакты:</span><span class="sxs-lookup"><span data-stu-id="cdac7-450">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="cdac7-451">Обновите модель страницы сведений:</span><span class="sxs-lookup"><span data-stu-id="cdac7-451">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="cdac7-452">Добавление или удаление пользователя для роли</span><span class="sxs-lookup"><span data-stu-id="cdac7-452">Add or remove a user to a role</span></span>

<span data-ttu-id="cdac7-453">Сведения об [этой ошибке](https://github.com/dotnet/AspNetCore.Docs/issues/8502) см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="cdac7-453">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="cdac7-454">Удаление привилегий у пользователя.</span><span class="sxs-lookup"><span data-stu-id="cdac7-454">Removing privileges from a user.</span></span> <span data-ttu-id="cdac7-455">Например, отзвука пользователя в приложении разговора.</span><span class="sxs-lookup"><span data-stu-id="cdac7-455">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="cdac7-456">Добавление привилегий для пользователя.</span><span class="sxs-lookup"><span data-stu-id="cdac7-456">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="cdac7-457">Тестирование завершенного приложения</span><span class="sxs-lookup"><span data-stu-id="cdac7-457">Test the completed app</span></span>

<span data-ttu-id="cdac7-458">Если вы еще не установили пароль для заполненных учетных записей пользователей, используйте [средство диспетчера секретов](xref:security/app-secrets#secret-manager) , чтобы задать пароль:</span><span class="sxs-lookup"><span data-stu-id="cdac7-458">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="cdac7-459">Выберите надежный пароль: используйте восемь или более символов и по крайней мере одну прописную букву, цифру и символ.</span><span class="sxs-lookup"><span data-stu-id="cdac7-459">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="cdac7-460">Например, `Passw0rd!` соответствует требованиям к надежному паролю.</span><span class="sxs-lookup"><span data-stu-id="cdac7-460">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="cdac7-461">Выполните следующую команду из папки проекта, где `<PW>` — пароль:</span><span class="sxs-lookup"><span data-stu-id="cdac7-461">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="cdac7-462">Удаление и обновление базы данных</span><span class="sxs-lookup"><span data-stu-id="cdac7-462">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="cdac7-463">Перезапустите приложение, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="cdac7-463">Restart the app to seed the database.</span></span>

<span data-ttu-id="cdac7-464">Простой способ тестирования завершенного приложения — запуск трех различных браузеров (или режиме инкогнито и нечастных сеансов).</span><span class="sxs-lookup"><span data-stu-id="cdac7-464">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="cdac7-465">В одном браузере Зарегистрируйте нового пользователя (например, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="cdac7-465">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="cdac7-466">Войдите в каждый браузер с другим пользователем.</span><span class="sxs-lookup"><span data-stu-id="cdac7-466">Sign in to each browser with a different user.</span></span> <span data-ttu-id="cdac7-467">Проверьте следующие операции:</span><span class="sxs-lookup"><span data-stu-id="cdac7-467">Verify the following operations:</span></span>

* <span data-ttu-id="cdac7-468">Зарегистрированные пользователи могут просматривать все утвержденные контактные данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-468">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="cdac7-469">Зарегистрированные пользователи могут изменять и удалять собственные данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-469">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="cdac7-470">Руководители могут утверждать и отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-470">Managers can approve/reject contact data.</span></span> <span data-ttu-id="cdac7-471">В `Details` представлении отображаются кнопки **утвердить** и **отклонить** .</span><span class="sxs-lookup"><span data-stu-id="cdac7-471">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="cdac7-472">Администраторы могут утверждать, отклонять и изменять и удалять все данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-472">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="cdac7-473">Пользователь</span><span class="sxs-lookup"><span data-stu-id="cdac7-473">User</span></span>                | <span data-ttu-id="cdac7-474">Заполнено приложением</span><span class="sxs-lookup"><span data-stu-id="cdac7-474">Seeded by the app</span></span> | <span data-ttu-id="cdac7-475">Варианты</span><span class="sxs-lookup"><span data-stu-id="cdac7-475">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="cdac7-476">Нет</span><span class="sxs-lookup"><span data-stu-id="cdac7-476">No</span></span>                | <span data-ttu-id="cdac7-477">Изменение или удаление собственных данных.</span><span class="sxs-lookup"><span data-stu-id="cdac7-477">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="cdac7-478">Да</span><span class="sxs-lookup"><span data-stu-id="cdac7-478">Yes</span></span>               | <span data-ttu-id="cdac7-479">Утвердите, отклоните и измените или удалите собственные данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-479">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="cdac7-480">Да</span><span class="sxs-lookup"><span data-stu-id="cdac7-480">Yes</span></span>               | <span data-ttu-id="cdac7-481">Утвердите или отклоните и измените или удалите все данные.</span><span class="sxs-lookup"><span data-stu-id="cdac7-481">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="cdac7-482">Создайте контакт в браузере администратора.</span><span class="sxs-lookup"><span data-stu-id="cdac7-482">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="cdac7-483">Скопируйте URL-адрес для DELETE и Edit из контакта администратора.</span><span class="sxs-lookup"><span data-stu-id="cdac7-483">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="cdac7-484">Вставьте эти ссылки в браузер тестового пользователя, чтобы убедиться, что тестовая пользователь не может выполнить эти операции.</span><span class="sxs-lookup"><span data-stu-id="cdac7-484">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="cdac7-485">Создание начального приложения</span><span class="sxs-lookup"><span data-stu-id="cdac7-485">Create the starter app</span></span>

* <span data-ttu-id="cdac7-486">Создание Razor страницы приложения с именем "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="cdac7-486">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="cdac7-487">Создайте приложение с **учетными записями отдельных пользователей**.</span><span class="sxs-lookup"><span data-stu-id="cdac7-487">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="cdac7-488">Назовите его "ContactManager", чтобы пространство имен совпадало с пространством имен, используемым в примере.</span><span class="sxs-lookup"><span data-stu-id="cdac7-488">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="cdac7-489">`-uld` Указывает LocalDB вместо SQLite</span><span class="sxs-lookup"><span data-stu-id="cdac7-489">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="cdac7-490">Добавление *моделей/Contact. CS*:</span><span class="sxs-lookup"><span data-stu-id="cdac7-490">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="cdac7-491">Формирование шаблонов `Contact` модели.</span><span class="sxs-lookup"><span data-stu-id="cdac7-491">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="cdac7-492">Создайте начальную миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="cdac7-492">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="cdac7-493">Обновите привязку **ContactManager** в файле *pages/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="cdac7-493">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="cdac7-494">Тестирование приложения путем создания, изменения и удаления контакта</span><span class="sxs-lookup"><span data-stu-id="cdac7-494">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="cdac7-495">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="cdac7-495">Seed the database</span></span>

<span data-ttu-id="cdac7-496">Добавьте класс [сиддата](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) в папку *Data* .</span><span class="sxs-lookup"><span data-stu-id="cdac7-496">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="cdac7-497">Вызов `SeedData.Initialize` из `Main` :</span><span class="sxs-lookup"><span data-stu-id="cdac7-497">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="cdac7-498">Проверьте, что приложение заполнено базой данных.</span><span class="sxs-lookup"><span data-stu-id="cdac7-498">Test that the app seeded the database.</span></span> <span data-ttu-id="cdac7-499">Если в базе данных Contact есть какие бы то ни было строки, метод SEED не выполняется.</span><span class="sxs-lookup"><span data-stu-id="cdac7-499">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="cdac7-500">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="cdac7-500">Additional resources</span></span>

* [<span data-ttu-id="cdac7-501">Сборка веб-приложения .NET Core и базы данных SQL в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="cdac7-501">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="cdac7-502">[ASP.NET Core лаборатории авторизации](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="cdac7-502">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="cdac7-503">В этом лабораторном занятии подробно рассматриваются функции безопасности, представленные в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="cdac7-503">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="cdac7-504">Настраиваемая авторизация на основе политик</span><span class="sxs-lookup"><span data-stu-id="cdac7-504">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
