---
title: Использование соглашений веб-API
author: pranavkm
description: Сведения о соглашениях веб-API в ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
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
uid: web-api/advanced/conventions
ms.openlocfilehash: 1e6526f46fbd177add3699fb5b667021b741c6a4
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587844"
---
# <a name="use-web-api-conventions"></a><span data-ttu-id="92343-103">Использование соглашений веб-API</span><span class="sxs-lookup"><span data-stu-id="92343-103">Use web API conventions</span></span>

<span data-ttu-id="92343-104">Авторы: [Пранав Кришнамурти (Pranav Krishnamoorthy)](https://github.com/pranavkm) и [Скот Эдди (Scott Addie)](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="92343-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="92343-105">В ASP.NET Core 2.2 и более поздних версий добавлен способ извлечения распространенной [документации по API](xref:tutorials/web-api-help-pages-using-swagger) и ее применения к нескольким действиям, контроллерам или всем контроллерам в рамках сборки.</span><span class="sxs-lookup"><span data-stu-id="92343-105">ASP.NET Core 2.2 and later includes a way to extract common [API documentation](xref:tutorials/web-api-help-pages-using-swagger) and apply it to multiple actions, controllers, or all controllers within an assembly.</span></span> <span data-ttu-id="92343-106">Соглашения веб-API являются заменителем для оформления отдельных действий с помощью [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) .</span><span class="sxs-lookup"><span data-stu-id="92343-106">Web API conventions are a substitute for decorating individual actions with [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span></span>

<span data-ttu-id="92343-107">Соглашение позволяет следующее.</span><span class="sxs-lookup"><span data-stu-id="92343-107">A convention allows you to:</span></span>

* <span data-ttu-id="92343-108">Определять наиболее распространенные типы возврата и коды состояния, возвращаемые из определенного типа действия.</span><span class="sxs-lookup"><span data-stu-id="92343-108">Define the most common return types and status codes returned from a specific type of action.</span></span>
* <span data-ttu-id="92343-109">Выявлять действия, отличающиеся от определенного стандарта.</span><span class="sxs-lookup"><span data-stu-id="92343-109">Identify actions that deviate from the defined standard.</span></span>

<span data-ttu-id="92343-110">ASP.NET Core MVC 2.2 и более поздних версий включает в себя набор соглашений по умолчанию в <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="92343-110">ASP.NET Core MVC 2.2 and later includes a set of default conventions in <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span></span> <span data-ttu-id="92343-111">Соглашения основаны на контроллере (*ValuesController.cs*), предоставляемым в шаблоне проекта **API** ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92343-111">The conventions are based on the controller (*ValuesController.cs*) provided in the ASP.NET Core **API** project template.</span></span> <span data-ttu-id="92343-112">Если ваши действия соответствуют схеме в шаблоне, вы сможете успешно использовать соглашения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="92343-112">If your actions follow the patterns in the template, you should be successful using the default conventions.</span></span> <span data-ttu-id="92343-113">Если соглашения по умолчанию не соответствуют вашим потребностям, см. раздел [Создание соглашений веб-API](#create-web-api-conventions).</span><span class="sxs-lookup"><span data-stu-id="92343-113">If the default conventions don't meet your needs, see [Create web API conventions](#create-web-api-conventions).</span></span>

<span data-ttu-id="92343-114">Во время выполнения <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> понимает соглашения.</span><span class="sxs-lookup"><span data-stu-id="92343-114">At runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> understands conventions.</span></span> <span data-ttu-id="92343-115">`ApiExplorer` является абстракцией MVC для взаимодействия с генераторами документов [OpenAPI](https://www.openapis.org/) (также называется Swagger).</span><span class="sxs-lookup"><span data-stu-id="92343-115">`ApiExplorer` is MVC's abstraction to communicate with [OpenAPI](https://www.openapis.org/) (also known as Swagger) document generators.</span></span> <span data-ttu-id="92343-116">Атрибуты из примененного соглашения связываются с действием и включаются в документацию OpenAPI для действия.</span><span class="sxs-lookup"><span data-stu-id="92343-116">Attributes from the applied convention are associated with an action and are included in the action's OpenAPI documentation.</span></span> <span data-ttu-id="92343-117">[Анализаторы API](xref:web-api/advanced/analyzers) также понимают соглашения.</span><span class="sxs-lookup"><span data-stu-id="92343-117">[API analyzers](xref:web-api/advanced/analyzers) also understand conventions.</span></span> <span data-ttu-id="92343-118">Если ваше действие является нестандартным (например, возвращает код состояния, который не задокументирован примененным соглашением), выводится предупреждение, позволяющее задокументировать код состояния.</span><span class="sxs-lookup"><span data-stu-id="92343-118">If your action is unconventional (for example, it returns a status code that isn't documented by the applied convention), a warning encourages you to document the status code.</span></span>

<span data-ttu-id="92343-119">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/conventions/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="92343-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/conventions/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="apply-web-api-conventions"></a><span data-ttu-id="92343-120">Применение соглашений веб-API</span><span class="sxs-lookup"><span data-stu-id="92343-120">Apply web API conventions</span></span>

<span data-ttu-id="92343-121">Соглашения не являются составными, каждое действие может быть связано только с одним соглашением.</span><span class="sxs-lookup"><span data-stu-id="92343-121">Conventions don't compose; each action may be associated with exactly one convention.</span></span> <span data-ttu-id="92343-122">Более конкретные соглашения имеют приоритет над менее определенными.</span><span class="sxs-lookup"><span data-stu-id="92343-122">More specific conventions take precedence over less specific conventions.</span></span> <span data-ttu-id="92343-123">Если к действию применяются два или более соглашения с одинаковым приоритетом, выбор осуществляется недетерминированным образом.</span><span class="sxs-lookup"><span data-stu-id="92343-123">The selection is non-deterministic when two or more conventions of the same priority apply to an action.</span></span> <span data-ttu-id="92343-124">Существуют следующие варианты применения соглашения к действию (от наиболее конкретного до наименее конкретного):</span><span class="sxs-lookup"><span data-stu-id="92343-124">The following options exist to apply a convention to an action, from the most specific to the least specific:</span></span>

1. <span data-ttu-id="92343-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute`&mdash;Применяется к отдельным действиям и определяет тип соглашения и применяемый метод соглашения.</span><span class="sxs-lookup"><span data-stu-id="92343-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Applies to individual actions and specifies the convention type and the convention method that applies.</span></span>

    <span data-ttu-id="92343-126">В следующем примере метод соглашения `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` типа соглашения по умолчанию применяется к действию `Update`:</span><span class="sxs-lookup"><span data-stu-id="92343-126">In the following example, the default convention type's `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method is applied to the `Update` action:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    <span data-ttu-id="92343-127">Метод соглашения `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` применяет следующие атрибуты к действию:</span><span class="sxs-lookup"><span data-stu-id="92343-127">The `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method applies the following attributes to the action:</span></span>

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    ```

    <span data-ttu-id="92343-128">Дополнительные сведения о `[ProducesDefaultResponseType]` см. в описании [ответа по умолчанию](https://swagger.io/docs/specification/describing-responses/#default).</span><span class="sxs-lookup"><span data-stu-id="92343-128">For more information on `[ProducesDefaultResponseType]`, see [Default Response](https://swagger.io/docs/specification/describing-responses/#default).</span></span>

1. <span data-ttu-id="92343-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`, примененный к контроллеру &mdash;, применяет определенный тип соглашения ко всем действиям в контроллере.</span><span class="sxs-lookup"><span data-stu-id="92343-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to a controller &mdash; Applies the specified convention type to all actions on the controller.</span></span> <span data-ttu-id="92343-130">Метод соглашения снабжен указаниями, определяющими действия, к которым применяется метод соглашения.</span><span class="sxs-lookup"><span data-stu-id="92343-130">A convention method is marked with hints that determine the actions to which the convention method applies.</span></span> <span data-ttu-id="92343-131">Дополнительные сведения об указаниях см. в разделе [Создание соглашений веб-API](#create-web-api-conventions).</span><span class="sxs-lookup"><span data-stu-id="92343-131">For more information on hints, see [Create web API conventions](#create-web-api-conventions)).</span></span>

    <span data-ttu-id="92343-132">В следующем примере набор соглашений по умолчанию применяется ко всем действиям в *ContactsConventionController*:</span><span class="sxs-lookup"><span data-stu-id="92343-132">In the following example, the default set of conventions is applied to all actions in *ContactsConventionController*:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. <span data-ttu-id="92343-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`, примененный к сборке &mdash;, применяет определенный тип соглашения ко всем контроллерам в текущей сборке.</span><span class="sxs-lookup"><span data-stu-id="92343-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to an assembly &mdash; Applies the specified convention type to all controllers in the current assembly.</span></span> <span data-ttu-id="92343-134">Атрибуты уровня сборки рекомендуется применять в файле *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="92343-134">As a recommendation, apply assembly-level attributes in the *Startup.cs* file.</span></span>

    <span data-ttu-id="92343-135">В следующем примере набор соглашений по умолчанию применяется ко всем контроллерам в сборке:</span><span class="sxs-lookup"><span data-stu-id="92343-135">In the following example, the default set of conventions is applied to all controllers in the assembly:</span></span>

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a><span data-ttu-id="92343-136">Создание соглашений веб-API</span><span class="sxs-lookup"><span data-stu-id="92343-136">Create web API conventions</span></span>

<span data-ttu-id="92343-137">Если соглашения API по умолчанию не соответствуют вашим потребностям, создайте собственные соглашения.</span><span class="sxs-lookup"><span data-stu-id="92343-137">If the default API conventions don't meet your needs, create your own conventions.</span></span> <span data-ttu-id="92343-138">Соглашение:</span><span class="sxs-lookup"><span data-stu-id="92343-138">A convention is:</span></span>

* <span data-ttu-id="92343-139">Это статический тип с методами.</span><span class="sxs-lookup"><span data-stu-id="92343-139">A static type with methods.</span></span>
* <span data-ttu-id="92343-140">Может определять [типы ответов](#response-types) и [требования к именованию](#naming-requirements) для действий.</span><span class="sxs-lookup"><span data-stu-id="92343-140">Capable of defining [response types](#response-types) and [naming requirements](#naming-requirements) on actions.</span></span>

### <a name="response-types"></a><span data-ttu-id="92343-141">Типы ответов</span><span class="sxs-lookup"><span data-stu-id="92343-141">Response types</span></span>

<span data-ttu-id="92343-142">Эти методы помечаются атрибутами `[ProducesResponseType]` или `[ProducesDefaultResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="92343-142">These methods are annotated with `[ProducesResponseType]` or `[ProducesDefaultResponseType]` attributes.</span></span> <span data-ttu-id="92343-143">Пример:</span><span class="sxs-lookup"><span data-stu-id="92343-143">For example:</span></span>

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public static void Find(int id)
    {
    }
}
```

<span data-ttu-id="92343-144">Если отсутствуют более конкретные атрибуты метаданных, применение этого соглашения к сборке указывает, что:</span><span class="sxs-lookup"><span data-stu-id="92343-144">If more specific metadata attributes are absent, applying this convention to an assembly enforces that:</span></span>

* <span data-ttu-id="92343-145">Метод соглашение применяется к любому действию с именем `Find`.</span><span class="sxs-lookup"><span data-stu-id="92343-145">The convention method applies to any action named `Find`.</span></span>
* <span data-ttu-id="92343-146">Параметр с именем `id` присутствует для действия `Find`.</span><span class="sxs-lookup"><span data-stu-id="92343-146">A parameter named `id` is present on the `Find` action.</span></span>

### <a name="naming-requirements"></a><span data-ttu-id="92343-147">Требования к именованию</span><span class="sxs-lookup"><span data-stu-id="92343-147">Naming requirements</span></span>

<span data-ttu-id="92343-148">Атрибуты `[ApiConventionNameMatch]` и `[ApiConventionTypeMatch]` можно применить к методу соглашения, определяющему действия, к которым они применяются.</span><span class="sxs-lookup"><span data-stu-id="92343-148">The `[ApiConventionNameMatch]` and `[ApiConventionTypeMatch]` attributes can be applied to the convention method that determines the actions to which they apply.</span></span> <span data-ttu-id="92343-149">Пример:</span><span class="sxs-lookup"><span data-stu-id="92343-149">For example:</span></span>

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

<span data-ttu-id="92343-150">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="92343-150">In the preceding example:</span></span>

* <span data-ttu-id="92343-151">Параметр `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix`, примененный к методу, указывает, что соглашение соответствует любому действию с префиксом Find.</span><span class="sxs-lookup"><span data-stu-id="92343-151">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` option applied to the method indicates that the convention matches any action prefixed with "Find".</span></span> <span data-ttu-id="92343-152">Примеры соответствующих действий: `Find`, `FindPet` и `FindById`.</span><span class="sxs-lookup"><span data-stu-id="92343-152">Examples of matching actions include `Find`, `FindPet`, and `FindById`.</span></span>
* <span data-ttu-id="92343-153">`Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix`, примененный к параметру, указывает, что соглашение соответствует методам только с одним параметром, заканчивающимся на идентификатор суффикса.</span><span class="sxs-lookup"><span data-stu-id="92343-153">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` applied to the parameter indicates that the convention matches methods with exactly one parameter ending in the suffix identifier.</span></span> <span data-ttu-id="92343-154">Это такие параметры, как `id` или `petId`.</span><span class="sxs-lookup"><span data-stu-id="92343-154">Examples include parameters such as `id` or `petId`.</span></span> <span data-ttu-id="92343-155">`ApiConventionTypeMatch` может аналогичным образом применяться к типам для ограничения типа параметра.</span><span class="sxs-lookup"><span data-stu-id="92343-155">`ApiConventionTypeMatch` can be similarly applied to types to constrain the parameter type.</span></span> <span data-ttu-id="92343-156">Аргумент `params[]` указывает на остальные параметры, которым не требуется явное сопоставление.</span><span class="sxs-lookup"><span data-stu-id="92343-156">A `params[]` argument indicates remaining parameters that don't need to be explicitly matched.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="92343-157">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="92343-157">Additional resources</span></span>

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
