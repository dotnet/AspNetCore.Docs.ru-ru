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
# <a name="resource-based-authorization-in-aspnet-core"></a>Авторизация на основе ресурсов в ASP.NET Core

Стратегия авторизации зависит от ресурса, к которому осуществляется доступ. Рассмотрим документ со свойством Author. Обновление документа разрешено только автору. Следовательно, документ необходимо извлечь из хранилища данных, прежде чем может выполняться оценка авторизации.

Оценка атрибутов выполняется перед привязкой данных и перед выполнением обработчика страницы или действия, которое загружает документ. По этим причинам декларативная авторизация с `[Authorize]` атрибутом недостаточна. Вместо этого можно вызвать пользовательский метод авторизации &mdash; с помощью стиля, известного как *императивная авторизация*.

::: moniker range=">= aspnetcore-3.0"
[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/3_0) ([описание скачивания](xref:index#how-to-download-a-sample)).
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/2_2) ([описание скачивания](xref:index#how-to-download-a-sample)).
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/1_1) ([описание скачивания](xref:index#how-to-download-a-sample)).
::: moniker-end

[Создание приложения ASP.NET Core с данными пользователя, защищенными с помощью авторизации,](xref:security/authorization/secure-data) содержит пример приложения, использующего авторизацию на основе ресурсов.

## <a name="use-imperative-authorization"></a>Использовать императивную авторизацию

Авторизация реализуется как служба [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) и регистрируется в коллекции служб в `Startup` классе. Служба становится доступной посредством [внедрения зависимостей](xref:fundamentals/dependency-injection) в обработчики страниц или действия.

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService` имеет две `AuthorizeAsync` перегрузки метода: один принимает ресурс и имя политики, а другой принимает ресурс и список требований для вычисления.

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

В следующем примере защищаемый ресурс загружается в пользовательский `Document` объект. `AuthorizeAsync`Вызывается перегрузка, чтобы определить, разрешено ли текущему пользователю изменять предоставленный документ. Пользовательская политика авторизации "Едитполици" разбивается на решение. Дополнительные сведения о создании политик авторизации см. в разделе [Настраиваемая авторизация на основе политик](xref:security/authorization/policies) .

> [!NOTE]
> В следующих примерах кода предполагается, что проверка подлинности выполнена и задано `User` свойство.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a>Написание обработчика на основе ресурсов

Написание обработчика для авторизации на основе ресурсов не сильно отличается от [написания обработчика простых требований](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler). Создайте настраиваемый класс требований и Реализуйте класс обработчика требований. Дополнительные сведения о создании класса требований см. в разделе [требования](xref:security/authorization/policies#requirements).

Класс обработчика задает как требование, так и тип ресурса. Например, обработчик, использующий `SameAuthorRequirement` и ресурс, `Document` выглядит следующим образом:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

В предыдущем примере Представьте, что `SameAuthorRequirement` является особым случаем для более универсального `SpecificAuthorRequirement` класса. `SpecificAuthorRequirement`Класс (не показан) содержит `Name` свойство, представляющее имя автора. `Name`Свойство может быть установлено для текущего пользователя.

Зарегистрируйте требование и обработчик в `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a>Требования к эксплуатации

Если вы принимаете решения на основе результатов операций CRUD (создание, чтение, обновление, удаление), используйте вспомогательный класс [оператионаусоризатионрекуиремент](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) . Этот класс позволяет написать отдельный обработчик, а не отдельный класс для каждого типа операции. Чтобы использовать его, укажите некоторые имена операций:

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

Обработчик реализуется следующим образом, используя `OperationAuthorizationRequirement` требование и `Document` ресурс:

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

Предыдущий обработчик проверяет операцию с помощью ресурса, удостоверения пользователя и `Name` свойства требования.

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a>Запрос и запрет с помощью обработчика рабочих ресурсов

В этом разделе показано, как обрабатываются результаты запроса и запрета действий, а также методы и запрета различий.

Для вызова обработчика рабочих ресурсов укажите операцию при вызове `AuthorizeAsync` обработчика страницы или действия. В следующем примере определяется, разрешено ли пользователю, прошедшему проверку подлинности, просматривать предоставленный документ.

> [!NOTE]
> В следующих примерах кода предполагается, что проверка подлинности выполнена и задано `User` свойство.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

Если авторизация выполнена, возвращается страница для просмотра документа. Если авторизация завершается неудачей, но пользователь прошел проверку подлинности, то при возврате `ForbidResult` сообщается о неудачной проверке подлинности. `ChallengeResult`Возвращается, если необходимо выполнить проверку подлинности. Для интерактивных клиентов браузера может быть целесообразно перенаправить пользователя на страницу входа.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

Если авторизация выполнена, возвращается представление документа. Если авторизация завершается неудачно, возвращается `ChallengeResult` уведомление о любом промежуточном по проверки подлинности, сбой авторизации, и по промежуточного слоя может принять соответствующий ответ. Соответствующий ответ может возвращать код состояния 401 или 403. Для интерактивных клиентов браузера это может означать перенаправление пользователя на страницу входа.

::: moniker-end
