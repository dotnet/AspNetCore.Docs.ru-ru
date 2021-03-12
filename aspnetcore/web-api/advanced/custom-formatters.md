---
title: Пользовательские модули форматирования для веб-API в ASP.NET Core
author: rick-anderson
description: Узнайте, как создавать и использовать пользовательские модули форматирования для веб-интерфейсов API в ASP.NET Core.
ms.author: riande
ms.date: 06/25/2020
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
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 91c9c6513d7c8df671e283508ecc276768d79539
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587831"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="ac7ed-103">Пользовательские модули форматирования для веб-API в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac7ed-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="ac7ed-104">[Kirk Ларкин](https://twitter.com/serpent5) и [Tom Dykstra)](https://github.com/tdykstra).</span><span class="sxs-lookup"><span data-stu-id="ac7ed-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Tom Dykstra](https://github.com/tdykstra).</span></span>

<span data-ttu-id="ac7ed-105">ASP.NET Core MVC поддерживает обмен данными в веб-API с помощью форматировщиков ввода и вывода.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="ac7ed-106">Форматировщики ввода используются [привязкой модели](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="ac7ed-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="ac7ed-107">Модули форматирования выходных данных используются для [форматирования ответов](xref:web-api/advanced/formatting).</span><span class="sxs-lookup"><span data-stu-id="ac7ed-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="ac7ed-108">Платформа предоставляет встроенные форматировщики ввода и вывода для JSON и XML.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="ac7ed-109">Доступен только встроенный форматировщик вывода для обычного текста, но не форматировщик ввода для обычного текста.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="ac7ed-110">В этой статье показано, как добавить поддержку дополнительных форматов, создав пользовательские модули форматирования.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="ac7ed-111">Пример пользовательского модуля форматирования ввода обычного текста см. в разделе [текстплаининпутформаттер](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) на GitHub.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-111">For an example of a custom plain text input formatter, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="ac7ed-112">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac7ed-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="ac7ed-113">Когда следует использовать пользовательские модули форматирования</span><span class="sxs-lookup"><span data-stu-id="ac7ed-113">When to use custom formatters</span></span>

<span data-ttu-id="ac7ed-114">Используйте пользовательский модуль форматирования, чтобы добавить поддержку для типа содержимого, который не обрабатывается встроенными модулями форматирования.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-114">Use a custom formatter to add support for a content type that isn't handled by the built-in formatters.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="ac7ed-115">Общие сведения об использовании пользовательского модуля форматирования</span><span class="sxs-lookup"><span data-stu-id="ac7ed-115">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="ac7ed-116">Чтобы создать пользовательский модуль форматирования, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ac7ed-116">To create a custom formatter:</span></span>

* <span data-ttu-id="ac7ed-117">Для сериализации данных, отправляемых клиенту, создайте класс модуля форматирования выходных данных.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-117">For serializing data sent to the client, create an output formatter class.</span></span>
* <span data-ttu-id="ac7ed-118">Для десериализации данных, полученных от клиента, создайте класс модуля форматирования ввода.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-118">For deserializing data received from the client, create an input formatter class.</span></span>
* <span data-ttu-id="ac7ed-119">Добавьте экземпляры классов модулей форматирования в `InputFormatters` коллекции и `OutputFormatters` в <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="ac7ed-119">Add instances of formatter classes to the `InputFormatters` and `OutputFormatters` collections in <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="ac7ed-120">Создание пользовательского класса модуля форматирования</span><span class="sxs-lookup"><span data-stu-id="ac7ed-120">How to create a custom formatter class</span></span>

<span data-ttu-id="ac7ed-121">Чтобы создать модуль форматирования, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-121">To create a formatter:</span></span>

* <span data-ttu-id="ac7ed-122">Наследуйте класс от подходящего базового класса.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-122">Derive the class from the appropriate base class.</span></span> <span data-ttu-id="ac7ed-123">Пример приложения является производным от <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> и <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="ac7ed-123">The sample app derives from <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> and <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter>.</span></span>
* <span data-ttu-id="ac7ed-124">Укажите в конструкторе допустимые типы передаваемых данных и кодировки.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-124">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="ac7ed-125">Переопределите методы <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> и <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A>.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-125">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> and <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> methods.</span></span>
* <span data-ttu-id="ac7ed-126">Переопределите методы <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> и `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-126">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> and `WriteResponseBodyAsync` methods.</span></span>

<span data-ttu-id="ac7ed-127">В следующем коде показан `VcardOutputFormatter` класс из [примера](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples):</span><span class="sxs-lookup"><span data-stu-id="ac7ed-127">The following code shows the `VcardOutputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples):</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="ac7ed-128">Наследование от подходящего базового класса</span><span class="sxs-lookup"><span data-stu-id="ac7ed-128">Derive from the appropriate base class</span></span>

<span data-ttu-id="ac7ed-129">Для типов текстовых файлов (например, vCard) следует наследовать от <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> базового класса или.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-129">For text media types (for example, vCard), derive from the <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> or <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> base class.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

<span data-ttu-id="ac7ed-130">Для двоичных типов является производным от <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> базового класса или.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-130">For binary types, derive from the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> or <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="ac7ed-131">Указание допустимых типов передаваемых данных и кодировок.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-131">Specify valid media types and encodings</span></span>

<span data-ttu-id="ac7ed-132">Укажите в конструкторе допустимые типы передаваемых данных и кодировки, добавив их в коллекции `SupportedMediaTypes` и `SupportedEncodings`.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-132">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

<span data-ttu-id="ac7ed-133">Класс модуля форматирования **не** может использовать внедрение конструктора для своих зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-133">A formatter class can **not** use constructor injection for its dependencies.</span></span> <span data-ttu-id="ac7ed-134">Например, `ILogger<VcardOutputFormatter>` нельзя добавить в конструктор в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-134">For example, `ILogger<VcardOutputFormatter>` cannot be added as a parameter to the constructor.</span></span> <span data-ttu-id="ac7ed-135">Для доступа к службам используйте объект контекста, который передается методам.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-135">To access services, use the context object that gets passed in to the methods.</span></span> <span data-ttu-id="ac7ed-136">Пример кода в этой статье и [Пример](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples) показывает, как это сделать.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-136">A code example in this article and the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples) show how to do this.</span></span>

### <a name="override-canreadtype-and-canwritetype"></a><span data-ttu-id="ac7ed-137">Переопределение Канреадтипе и Канвритетипе</span><span class="sxs-lookup"><span data-stu-id="ac7ed-137">Override CanReadType and CanWriteType</span></span>

<span data-ttu-id="ac7ed-138">Укажите тип для десериализации или сериализации из путем переопределения `CanReadType` `CanWriteType` методов или.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-138">Specify the type to deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="ac7ed-139">Например, создание текста vCard из `Contact` типа и наоборот.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-139">For example, creating vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="ac7ed-140">Метод CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="ac7ed-140">The CanWriteResult method</span></span>

<span data-ttu-id="ac7ed-141">В некоторых сценариях `CanWriteResult` должен быть переопределен, а не `CanWriteType` .</span><span class="sxs-lookup"><span data-stu-id="ac7ed-141">In some scenarios, `CanWriteResult` must be overridden rather than `CanWriteType`.</span></span> <span data-ttu-id="ac7ed-142">Используйте `CanWriteResult`, если выполняются указанные ниже условия.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-142">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="ac7ed-143">Метод действия возвращает класс модели.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-143">The action method returns a model class.</span></span>
* <span data-ttu-id="ac7ed-144">Существуют производные классы, которые могут возвращаться во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-144">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="ac7ed-145">Производный класс, возвращенный действием, должен быть известен во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-145">The derived class returned by the action must be known at runtime.</span></span>

<span data-ttu-id="ac7ed-146">Например, предположим, что метод действия:</span><span class="sxs-lookup"><span data-stu-id="ac7ed-146">For example, suppose the action method:</span></span>

* <span data-ttu-id="ac7ed-147">Сигнатура возвращает `Person` тип.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-147">Signature returns a `Person` type.</span></span>
* <span data-ttu-id="ac7ed-148">Может возвращать `Student` `Instructor` тип или, производный от `Person` .</span><span class="sxs-lookup"><span data-stu-id="ac7ed-148">Can return a `Student` or `Instructor` type that derives from `Person`.</span></span> 

<span data-ttu-id="ac7ed-149">Чтобы модуль форматирования обрабатывал только `Student` объекты, проверьте тип <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> в объекте контекста, предоставленном `CanWriteResult` методу.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-149">For the formatter to handle only `Student` objects, check the type of <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="ac7ed-150">Когда метод действия возвращает `IActionResult` :</span><span class="sxs-lookup"><span data-stu-id="ac7ed-150">When the action method returns `IActionResult`:</span></span>

* <span data-ttu-id="ac7ed-151">Нет необходимости использовать `CanWriteResult` .</span><span class="sxs-lookup"><span data-stu-id="ac7ed-151">It's not necessary to use `CanWriteResult`.</span></span>
* <span data-ttu-id="ac7ed-152">`CanWriteType`Метод получает тип среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-152">The `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a><span data-ttu-id="ac7ed-153">Переопределение Реадрекуестбодясинк и Вритереспонсебодясинк</span><span class="sxs-lookup"><span data-stu-id="ac7ed-153">Override ReadRequestBodyAsync and WriteResponseBodyAsync</span></span>

<span data-ttu-id="ac7ed-154">Десериализация или сериализация выполняется в `ReadRequestBodyAsync` или `WriteResponseBodyAsync` .</span><span class="sxs-lookup"><span data-stu-id="ac7ed-154">Deserialization or serialization is performed in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="ac7ed-155">В следующем примере показано, как получить службы из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-155">The following example shows how to get services from the dependency injection container.</span></span> <span data-ttu-id="ac7ed-156">Не удается получить службы из параметров конструктора.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-156">Services can't be obtained from constructor parameters.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="ac7ed-157">Настройка использования пользовательского модуля форматирования в MVC</span><span class="sxs-lookup"><span data-stu-id="ac7ed-157">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="ac7ed-158">Для использования пользовательского модуля форматирования добавьте экземпляр его класса в коллекцию `InputFormatters` или `OutputFormatters`.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-158">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

<span data-ttu-id="ac7ed-159">Модули форматирования обрабатываются в порядке добавления.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-159">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="ac7ed-160">Первый модуль имеет приоритет.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-160">The first one takes precedence.</span></span>

## <a name="the-complete-vcardinputformatter-class"></a><span data-ttu-id="ac7ed-161">Полный `VcardInputFormatter` класс</span><span class="sxs-lookup"><span data-stu-id="ac7ed-161">The complete `VcardInputFormatter` class</span></span>

<span data-ttu-id="ac7ed-162">В следующем коде показан `VcardInputFormatter` класс из [примера](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples):</span><span class="sxs-lookup"><span data-stu-id="ac7ed-162">The following code shows the `VcardInputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples):</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a><span data-ttu-id="ac7ed-163">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="ac7ed-163">Test the app</span></span>

<span data-ttu-id="ac7ed-164">[Запустите пример приложения для этой статьи, в](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples)котором реализованы базовые модули форматирования входных и выходных данных vCard.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-164">[Run the sample app for this article](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples), which implements basic vCard input and output formatters.</span></span> <span data-ttu-id="ac7ed-165">Приложение считывает и записывает визитные карточки следующего вида:</span><span class="sxs-lookup"><span data-stu-id="ac7ed-165">The app reads and writes vCards similar to the following:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

<span data-ttu-id="ac7ed-166">Чтобы просмотреть выходные данные vCard, запустите приложение и отправьте запрос GET с заголовком Accept `text/vcard` в `https://localhost:5001/api/contacts` .</span><span class="sxs-lookup"><span data-stu-id="ac7ed-166">To see vCard output, run the app and send a Get request with Accept header `text/vcard` to `https://localhost:5001/api/contacts`.</span></span>

<span data-ttu-id="ac7ed-167">Чтобы добавить vCard в коллекцию контактов в памяти, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-167">To add a vCard to the in-memory collection of contacts:</span></span>

* <span data-ttu-id="ac7ed-168">Отправка `Post` запроса `/api/contacts` с помощью такого средства, как POST.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-168">Send a `Post` request to `/api/contacts` with a tool like Postman.</span></span>
* <span data-ttu-id="ac7ed-169">В качестве заголовка `Content-Type` установите `text/vcard`.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-169">Set the `Content-Type` header to `text/vcard`.</span></span>
* <span data-ttu-id="ac7ed-170">Задайте `vCard` текст в тексте, отформатированный как в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="ac7ed-170">Set `vCard` text in the body, formatted like the preceding example.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac7ed-171">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ac7ed-171">Additional resources</span></span>

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
