---
title: Авторизация на основе ресурсов в ASP.NET Core
author: scottaddie
description: Узнайте, как реализовать авторизацию на основе ресурсов в приложении ASP.NET Core, если атрибут авторизации не будет достаточным.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
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
uid: security/authorization/resourcebased
ms.openlocfilehash: 61c97be03709f63f57a6383ab0c51ca9a511fbbb
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585777"
---
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="10926-103">Авторизация на основе ресурсов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10926-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="10926-104">Стратегия авторизации зависит от ресурса, к которому осуществляется доступ.</span><span class="sxs-lookup"><span data-stu-id="10926-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="10926-105">Рассмотрим документ со свойством Author.</span><span class="sxs-lookup"><span data-stu-id="10926-105">Consider a document that has an author property.</span></span> <span data-ttu-id="10926-106">Обновление документа разрешено только автору.</span><span class="sxs-lookup"><span data-stu-id="10926-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="10926-107">Следовательно, документ необходимо извлечь из хранилища данных, прежде чем может выполняться оценка авторизации.</span><span class="sxs-lookup"><span data-stu-id="10926-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="10926-108">Оценка атрибутов выполняется перед привязкой данных и перед выполнением обработчика страницы или действия, которое загружает документ.</span><span class="sxs-lookup"><span data-stu-id="10926-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="10926-109">По этим причинам декларативная авторизация с `[Authorize]` атрибутом недостаточна.</span><span class="sxs-lookup"><span data-stu-id="10926-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="10926-110">Вместо этого можно вызвать пользовательский метод авторизации &mdash; с помощью стиля, известного как *императивная авторизация*.</span><span class="sxs-lookup"><span data-stu-id="10926-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization*.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="10926-111">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/3_0) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="10926-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/3_0) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
<span data-ttu-id="10926-112">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/2_2) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="10926-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/2_2) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
<span data-ttu-id="10926-113">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/1_1) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="10926-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/1_1) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

<span data-ttu-id="10926-114">[Создание приложения ASP.NET Core с данными пользователя, защищенными с помощью авторизации,](xref:security/authorization/secure-data) содержит пример приложения, использующего авторизацию на основе ресурсов.</span><span class="sxs-lookup"><span data-stu-id="10926-114">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="10926-115">Использовать императивную авторизацию</span><span class="sxs-lookup"><span data-stu-id="10926-115">Use imperative authorization</span></span>

<span data-ttu-id="10926-116">Авторизация реализуется как служба [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) и регистрируется в коллекции служб в `Startup` классе.</span><span class="sxs-lookup"><span data-stu-id="10926-116">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="10926-117">Служба становится доступной посредством [внедрения зависимостей](xref:fundamentals/dependency-injection) в обработчики страниц или действия.</span><span class="sxs-lookup"><span data-stu-id="10926-117">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="10926-118">`IAuthorizationService` имеет две `AuthorizeAsync` перегрузки метода: один принимает ресурс и имя политики, а другой принимает ресурс и список требований для вычисления.</span><span class="sxs-lookup"><span data-stu-id="10926-118">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

<span data-ttu-id="10926-119">В следующем примере защищаемый ресурс загружается в пользовательский `Document` объект.</span><span class="sxs-lookup"><span data-stu-id="10926-119">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="10926-120">`AuthorizeAsync`Вызывается перегрузка, чтобы определить, разрешено ли текущему пользователю изменять предоставленный документ.</span><span class="sxs-lookup"><span data-stu-id="10926-120">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="10926-121">Пользовательская политика авторизации "Едитполици" разбивается на решение.</span><span class="sxs-lookup"><span data-stu-id="10926-121">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="10926-122">Дополнительные сведения о создании политик авторизации см. в разделе [Настраиваемая авторизация на основе политик](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="10926-122">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="10926-123">В следующих примерах кода предполагается, что проверка подлинности выполнена и задано `User` свойство.</span><span class="sxs-lookup"><span data-stu-id="10926-123">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="10926-124">Написание обработчика на основе ресурсов</span><span class="sxs-lookup"><span data-stu-id="10926-124">Write a resource-based handler</span></span>

<span data-ttu-id="10926-125">Написание обработчика для авторизации на основе ресурсов не сильно отличается от [написания обработчика простых требований](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span><span class="sxs-lookup"><span data-stu-id="10926-125">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="10926-126">Создайте настраиваемый класс требований и Реализуйте класс обработчика требований.</span><span class="sxs-lookup"><span data-stu-id="10926-126">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="10926-127">Дополнительные сведения о создании класса требований см. в разделе [требования](xref:security/authorization/policies#requirements).</span><span class="sxs-lookup"><span data-stu-id="10926-127">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="10926-128">Класс обработчика задает как требование, так и тип ресурса.</span><span class="sxs-lookup"><span data-stu-id="10926-128">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="10926-129">Например, обработчик, использующий `SameAuthorRequirement` и ресурс, `Document` выглядит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="10926-129">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="10926-130">В предыдущем примере Представьте, что `SameAuthorRequirement` является особым случаем для более универсального `SpecificAuthorRequirement` класса.</span><span class="sxs-lookup"><span data-stu-id="10926-130">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="10926-131">`SpecificAuthorRequirement`Класс (не показан) содержит `Name` свойство, представляющее имя автора.</span><span class="sxs-lookup"><span data-stu-id="10926-131">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="10926-132">`Name`Свойство может быть установлено для текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="10926-132">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="10926-133">Зарегистрируйте требование и обработчик в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="10926-133">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a><span data-ttu-id="10926-134">Требования к эксплуатации</span><span class="sxs-lookup"><span data-stu-id="10926-134">Operational requirements</span></span>

<span data-ttu-id="10926-135">Если вы принимаете решения на основе результатов операций CRUD (создание, чтение, обновление, удаление), используйте вспомогательный класс [оператионаусоризатионрекуиремент](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) .</span><span class="sxs-lookup"><span data-stu-id="10926-135">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="10926-136">Этот класс позволяет написать отдельный обработчик, а не отдельный класс для каждого типа операции.</span><span class="sxs-lookup"><span data-stu-id="10926-136">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="10926-137">Чтобы использовать его, укажите некоторые имена операций:</span><span class="sxs-lookup"><span data-stu-id="10926-137">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="10926-138">Обработчик реализуется следующим образом, используя `OperationAuthorizationRequirement` требование и `Document` ресурс:</span><span class="sxs-lookup"><span data-stu-id="10926-138">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="10926-139">Предыдущий обработчик проверяет операцию с помощью ресурса, удостоверения пользователя и `Name` свойства требования.</span><span class="sxs-lookup"><span data-stu-id="10926-139">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a><span data-ttu-id="10926-140">Запрос и запрет с помощью обработчика рабочих ресурсов</span><span class="sxs-lookup"><span data-stu-id="10926-140">Challenge and forbid with an operational resource handler</span></span>

<span data-ttu-id="10926-141">В этом разделе показано, как обрабатываются результаты запроса и запрета действий, а также методы и запрета различий.</span><span class="sxs-lookup"><span data-stu-id="10926-141">This section shows how the challenge and forbid action results are processed and how challenge and forbid differ.</span></span>

<span data-ttu-id="10926-142">Для вызова обработчика рабочих ресурсов укажите операцию при вызове `AuthorizeAsync` обработчика страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="10926-142">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="10926-143">В следующем примере определяется, разрешено ли пользователю, прошедшему проверку подлинности, просматривать предоставленный документ.</span><span class="sxs-lookup"><span data-stu-id="10926-143">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="10926-144">В следующих примерах кода предполагается, что проверка подлинности выполнена и задано `User` свойство.</span><span class="sxs-lookup"><span data-stu-id="10926-144">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="10926-145">Если авторизация выполнена, возвращается страница для просмотра документа.</span><span class="sxs-lookup"><span data-stu-id="10926-145">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="10926-146">Если авторизация завершается неудачей, но пользователь прошел проверку подлинности, то при возврате `ForbidResult` сообщается о неудачной проверке подлинности.</span><span class="sxs-lookup"><span data-stu-id="10926-146">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="10926-147">`ChallengeResult`Возвращается, если необходимо выполнить проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="10926-147">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="10926-148">Для интерактивных клиентов браузера может быть целесообразно перенаправить пользователя на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="10926-148">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="10926-149">Если авторизация выполнена, возвращается представление документа.</span><span class="sxs-lookup"><span data-stu-id="10926-149">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="10926-150">Если авторизация завершается неудачно, возвращается `ChallengeResult` уведомление о любом промежуточном по проверки подлинности, сбой авторизации, и по промежуточного слоя может принять соответствующий ответ.</span><span class="sxs-lookup"><span data-stu-id="10926-150">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="10926-151">Соответствующий ответ может возвращать код состояния 401 или 403.</span><span class="sxs-lookup"><span data-stu-id="10926-151">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="10926-152">Для интерактивных клиентов браузера это может означать перенаправление пользователя на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="10926-152">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end
