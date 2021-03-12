---
title: Создание вспомогательных функций тегов в ASP.NET Core
author: rick-anderson
description: Сведения о разработке вспомогательных функций тегов в ASP.NET Core.
ms.author: riande
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
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: e0c9f450e4eded49694cbbb0e9fa2614a221ab14
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586830"
---
# <a name="author-tag-helpers-in-aspnet-core"></a><span data-ttu-id="c54ae-103">Создание вспомогательных функций тегов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c54ae-103">Author Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="c54ae-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="c54ae-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c54ae-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c54ae-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started-with-tag-helpers"></a><span data-ttu-id="c54ae-106">Начало работы с вспомогательными функциями тегов</span><span class="sxs-lookup"><span data-stu-id="c54ae-106">Get started with Tag Helpers</span></span>

<span data-ttu-id="c54ae-107">В этом учебнике приводятся основные сведения о программировании вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="c54ae-107">This tutorial provides an introduction to programming Tag Helpers.</span></span> <span data-ttu-id="c54ae-108">В статье [Общие сведения о вспомогательных функциях тегов](intro.md) описываются преимущества, предоставляемые вспомогательными функциями тегов.</span><span class="sxs-lookup"><span data-stu-id="c54ae-108">[Introduction to Tag Helpers](intro.md) describes the benefits that Tag Helpers provide.</span></span>

<span data-ttu-id="c54ae-109">Вспомогательная функция тега — это любой класс, реализующий интерфейс `ITagHelper`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-109">A tag helper is any class that implements the `ITagHelper` interface.</span></span> <span data-ttu-id="c54ae-110">Однако при разработке вспомогательной функции тега обычно производится наследование `TagHelper`, так как это дает доступ к методу `Process`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-110">However, when you author a tag helper, you generally derive from `TagHelper`, doing so gives you access to the `Process` method.</span></span>

1. <span data-ttu-id="c54ae-111">Создайте проект ASP.NET Core с именем **AuthoringTagHelpers**.</span><span class="sxs-lookup"><span data-stu-id="c54ae-111">Create a new ASP.NET Core project called **AuthoringTagHelpers**.</span></span> <span data-ttu-id="c54ae-112">Проверка подлинности для этого проекта не потребуется.</span><span class="sxs-lookup"><span data-stu-id="c54ae-112">You won't need authentication for this project.</span></span>

1. <span data-ttu-id="c54ae-113">Создайте папку, в которой будут помещаться вспомогательные функции тегов, с именем *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="c54ae-113">Create a folder to hold the Tag Helpers called *TagHelpers*.</span></span> <span data-ttu-id="c54ae-114">Папка *TagHelpers\*\*не* является обязательной; она создается согласно принятому соглашению.</span><span class="sxs-lookup"><span data-stu-id="c54ae-114">The *TagHelpers* folder is *not* required, but it's a reasonable convention.</span></span> <span data-ttu-id="c54ae-115">Теперь приступим к написанию простейших вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="c54ae-115">Now let's get started writing some simple tag helpers.</span></span>

## <a name="a-minimal-tag-helper"></a><span data-ttu-id="c54ae-116">Простейшая вспомогательная функция тега</span><span class="sxs-lookup"><span data-stu-id="c54ae-116">A minimal Tag Helper</span></span>

<span data-ttu-id="c54ae-117">В этом разделе вы напишете вспомогательную функцию тега, которая обновляет тег электронной почты.</span><span class="sxs-lookup"><span data-stu-id="c54ae-117">In this section, you write a tag helper that updates an email tag.</span></span> <span data-ttu-id="c54ae-118">Пример:</span><span class="sxs-lookup"><span data-stu-id="c54ae-118">For example:</span></span>

```html
<email>Support</email>
```

<span data-ttu-id="c54ae-119">Сервер будет использовать эту вспомогательную функцию тега для преобразования разметки следующим образом:</span><span class="sxs-lookup"><span data-stu-id="c54ae-119">The server will use our email tag helper to convert that markup into the following:</span></span>

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

<span data-ttu-id="c54ae-120">Это тег привязки, который преобразует элемент в ссылку с адресом электронной почты.</span><span class="sxs-lookup"><span data-stu-id="c54ae-120">That is, an anchor tag that makes this an email link.</span></span> <span data-ttu-id="c54ae-121">Он может потребоваться, если вы создаете подсистему ведения блогов и вам нужно реализовать отправку электронной почты в отдел маркетинга, службу технической поддержки и на другие адреса в одном домене.</span><span class="sxs-lookup"><span data-stu-id="c54ae-121">You might want to do this if you are writing a blog engine and need it to send email for marketing, support, and other contacts, all to the same domain.</span></span>

1. <span data-ttu-id="c54ae-122">Добавьте приведенный ниже класс `EmailTagHelper` в папку *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="c54ae-122">Add the following `EmailTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * <span data-ttu-id="c54ae-123">Для вспомогательных функций тегов используется соглашение об именовании в отношении элементов имени корневого класса (за исключением части *TagHelper* этого имени).</span><span class="sxs-lookup"><span data-stu-id="c54ae-123">Tag helpers use a naming convention that targets elements of the root class name (minus the *TagHelper* portion of the class name).</span></span> <span data-ttu-id="c54ae-124">В этом примере корневое имя **емаилтагхелпер** — *e-mail*, поэтому `<email>` тег будет целевым.</span><span class="sxs-lookup"><span data-stu-id="c54ae-124">In this example, the root name of **EmailTagHelper** is *email*, so the `<email>` tag will be targeted.</span></span> <span data-ttu-id="c54ae-125">Это соглашение об именовании подходит для большинства вспомогательных функций тегов, позднее мы покажем, как переопределить его.</span><span class="sxs-lookup"><span data-stu-id="c54ae-125">This naming convention should work for most tag helpers, later on I'll show how to override it.</span></span>

   * <span data-ttu-id="c54ae-126">Класс `EmailTagHelper` является производным от `TagHelper`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-126">The `EmailTagHelper` class derives from `TagHelper`.</span></span> <span data-ttu-id="c54ae-127">Класс `TagHelper` предоставляет методы и свойства для написания вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="c54ae-127">The `TagHelper` class provides methods and properties for writing Tag Helpers.</span></span>

   * <span data-ttu-id="c54ae-128">Переопределенный `Process` метод управляет действиями, выполняемыми вспомогательной функцией тега.</span><span class="sxs-lookup"><span data-stu-id="c54ae-128">The overridden `Process` method controls what the tag helper does when executed.</span></span> <span data-ttu-id="c54ae-129">Класс `TagHelper` также предоставляет асинхронную версию (`ProcessAsync`) с теми же параметрами.</span><span class="sxs-lookup"><span data-stu-id="c54ae-129">The `TagHelper` class also provides an asynchronous version (`ProcessAsync`) with the same parameters.</span></span>

   * <span data-ttu-id="c54ae-130">Параметр контекста метода `Process` (и `ProcessAsync`) содержит сведения, связанные с выполнением текущего HTML-тега.</span><span class="sxs-lookup"><span data-stu-id="c54ae-130">The context parameter to `Process` (and `ProcessAsync`) contains information associated with the execution of the current HTML tag.</span></span>

   * <span data-ttu-id="c54ae-131">Выходной параметр метода `Process` (и `ProcessAsync`) содержит элемент HTML с отслеживанием состояния, который представляет источник, используемый для формирования HTML-тега и содержимого.</span><span class="sxs-lookup"><span data-stu-id="c54ae-131">The output parameter to `Process` (and `ProcessAsync`) contains a stateful HTML element representative of the original source used to generate an HTML tag and content.</span></span>

   * <span data-ttu-id="c54ae-132">В нашем случае имя класса имеет суффикс **TagHelper**, который *не* является обязательным, но считается рекомендуемым соглашением.</span><span class="sxs-lookup"><span data-stu-id="c54ae-132">Our class name has a suffix of **TagHelper**, which is *not* required, but it's considered a best practice convention.</span></span> <span data-ttu-id="c54ae-133">Класс можно объявить следующим образом:</span><span class="sxs-lookup"><span data-stu-id="c54ae-133">You could declare the class as:</span></span>

   ```csharp
   public class Email : TagHelper
   ```

1. <span data-ttu-id="c54ae-134">Чтобы сделать `EmailTagHelper` класс доступным для всех наших Razor представлений, добавьте `addTagHelper` директиву в файл *views/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c54ae-134">To make the `EmailTagHelper` class available to all our Razor views, add the `addTagHelper` directive to the *Views/_ViewImports.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   <span data-ttu-id="c54ae-135">В приведенном выше коде используется синтаксис с подстановочным знаком, который указывает, что будут доступны все вспомогательные функции тегов в сборке.</span><span class="sxs-lookup"><span data-stu-id="c54ae-135">The code above uses the wildcard syntax to specify all the tag helpers in our assembly will be available.</span></span> <span data-ttu-id="c54ae-136">Первая строка после `@addTagHelper` указывает на загружаемую вспомогательную функцию тега (символ "\*" соответствует всем вспомогательным функциям тегов), а вторая строка ("AuthoringTagHelpers") указывает на сборку, в которой находится вспомогательная функция тега.</span><span class="sxs-lookup"><span data-stu-id="c54ae-136">The first string after `@addTagHelper` specifies the tag helper to load (Use "\*" for all tag helpers), and the second string "AuthoringTagHelpers" specifies the assembly the tag helper is in.</span></span> <span data-ttu-id="c54ae-137">Кроме того, обратите внимание, что вторая строка помещается в вспомогательные функции тегов ASP.NET Core MVC с помощью подстановочного знака (эти вспомогательные методы обсуждаются в разделе Общие сведения о [вспомогательных](intro.md)средствах тегов). Это `@addTagHelper` директива, которая делает вспомогательную функцию тега доступной для Razor представления.</span><span class="sxs-lookup"><span data-stu-id="c54ae-137">Also, note that the second line brings in the ASP.NET Core MVC tag helpers using the wildcard syntax (those helpers are discussed in [Introduction to Tag Helpers](intro.md).) It's the `@addTagHelper` directive that makes the tag helper available to the Razor view.</span></span> <span data-ttu-id="c54ae-138">Вы также можете указать полное имя вспомогательной функции тега, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="c54ae-138">Alternatively, you can provide the fully qualified name (FQN) of a tag helper as shown below:</span></span>

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

<span data-ttu-id="c54ae-139">Чтобы добавить вспомогательную функцию тега в представление с помощью полного имени, сначала добавьте полное имя (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), а затем — **имя сборки** (*AuthoringTagHelpers*, не обязательно `namespace`).</span><span class="sxs-lookup"><span data-stu-id="c54ae-139">To add a tag helper to a view using a FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the **assembly name** (*AuthoringTagHelpers*, not necessarily the `namespace`).</span></span> <span data-ttu-id="c54ae-140">Большинство разработчиков предпочитают использовать синтаксис с подстановочным знаком.</span><span class="sxs-lookup"><span data-stu-id="c54ae-140">Most developers will prefer to use the wildcard syntax.</span></span> <span data-ttu-id="c54ae-141">В статье [Общие сведения о вспомогательных функциях тегов](intro.md) подробно рассматривается добавление и удаление вспомогательных функций тегов, их иерархия и синтаксис с подстановочным знаком.</span><span class="sxs-lookup"><span data-stu-id="c54ae-141">[Introduction to Tag Helpers](intro.md) goes into detail on tag helper adding, removing, hierarchy, and wildcard syntax.</span></span>

1. <span data-ttu-id="c54ae-142">Внесите следующие изменения в разметку в файле *Views/Home/Contact.cshtml*: </span><span class="sxs-lookup"><span data-stu-id="c54ae-142">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="c54ae-143">Запустите приложение и просмотрите исходный текст HTML в любимом браузере, чтобы проверить, были ли теги электронной почты заменены разметкой привязки (например, `<a>Support</a>`).</span><span class="sxs-lookup"><span data-stu-id="c54ae-143">Run the app and use your favorite browser to view the HTML source so you can verify that the email tags are replaced with anchor markup (For example, `<a>Support</a>`).</span></span> <span data-ttu-id="c54ae-144">Строки *Support* и *Marketing* отображаются как ссылки, но у них нет атрибута `href`, поэтому ссылки будут нерабочими.</span><span class="sxs-lookup"><span data-stu-id="c54ae-144">*Support* and *Marketing* are rendered as a links, but they don't have an `href` attribute to make them functional.</span></span> <span data-ttu-id="c54ae-145">Мы исправим это в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="c54ae-145">We'll fix that in the next section.</span></span>

## <a name="setattribute-and-setcontent"></a><span data-ttu-id="c54ae-146">SetAttribute и SetContent</span><span class="sxs-lookup"><span data-stu-id="c54ae-146">SetAttribute and SetContent</span></span>

<span data-ttu-id="c54ae-147">В этом разделе мы изменим функцию `EmailTagHelper` так, чтобы она создавала правильный тег привязки для адреса электронной почты.</span><span class="sxs-lookup"><span data-stu-id="c54ae-147">In this section, we'll update the `EmailTagHelper` so that it will create a valid anchor tag for email.</span></span> <span data-ttu-id="c54ae-148">Мы будем обновлять его для получения сведений из Razor представления (в виде `mail-to` атрибута) и использовать его для создания привязки.</span><span class="sxs-lookup"><span data-stu-id="c54ae-148">We'll update it to take information from a Razor view (in the form of a `mail-to` attribute) and use that in generating the anchor.</span></span>

<span data-ttu-id="c54ae-149">Измените класс `EmailTagHelper` следующим образом:</span><span class="sxs-lookup"><span data-stu-id="c54ae-149">Update the `EmailTagHelper` class with the following:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* <span data-ttu-id="c54ae-150">Имена классов и свойств, указанные в стиле Pascal, для вспомогательных функций тегов преобразуются в [кебаб-стиль](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="c54ae-150">Pascal-cased class and property names for tag helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="c54ae-151">Поэтому для использования атрибута `MailTo` следует использовать эквивалент `<email mail-to="value"/>`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-151">Therefore, to use the `MailTo` attribute, you'll use `<email mail-to="value"/>` equivalent.</span></span>

* <span data-ttu-id="c54ae-152">В последней строке задается готовое содержимое для нашей простейшей функциональной вспомогательной функции тега.</span><span class="sxs-lookup"><span data-stu-id="c54ae-152">The last line sets the completed content for our minimally functional tag helper.</span></span>

* <span data-ttu-id="c54ae-153">В выделенной строке показан синтаксис добавления атрибутов:</span><span class="sxs-lookup"><span data-stu-id="c54ae-153">The highlighted line shows the syntax for adding attributes:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

<span data-ttu-id="c54ae-154">Такой подход работает для атрибута href, если его в настоящее время нет в коллекции атрибутов.</span><span class="sxs-lookup"><span data-stu-id="c54ae-154">That approach works for the attribute "href" as long as it doesn't currently exist in the attributes collection.</span></span> <span data-ttu-id="c54ae-155">Вы также можете добавить атрибут вспомогательной функции тега в конец коллекции атрибутов тегов с помощью метода `output.Attributes.Add`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-155">You can also use the `output.Attributes.Add` method to add a tag helper attribute to the end of the collection of tag attributes.</span></span>

1. <span data-ttu-id="c54ae-156">Внесите следующие изменения в разметку в файле *Views/Home/Contact.cshtml*: </span><span class="sxs-lookup"><span data-stu-id="c54ae-156">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. <span data-ttu-id="c54ae-157">Запустите приложение и убедитесь в том, что ссылки создаются правильно.</span><span class="sxs-lookup"><span data-stu-id="c54ae-157">Run the app and verify that it generates the correct links.</span></span>

<a name="self-closing"></a>

   > [!NOTE]
   > <span data-ttu-id="c54ae-158">Если бы вам нужно было написать самозакрывающийся тег электронной почты (`<email mail-to="Rick" />`), выходной тег также был бы самозакрывающимся.</span><span class="sxs-lookup"><span data-stu-id="c54ae-158">If you were to write the email tag self-closing (`<email mail-to="Rick" />`), the final output would also be self-closing.</span></span> <span data-ttu-id="c54ae-159">Чтобы получить возможность создавать теги с помощью только открывающего тега (`<email mail-to="Rick">`), класс необходимо пометить следующим образом:</span><span class="sxs-lookup"><span data-stu-id="c54ae-159">To enable the ability to write the tag with only a start tag (`<email mail-to="Rick">`) you must mark the class with the following:</span></span>
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   <span data-ttu-id="c54ae-160">При использовании самозакрывающейся вспомогательной функции тега электронной почты результат будет следующим: `<a href="mailto:Rick@contoso.com" />`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-160">With a self-closing email tag helper, the output would be `<a href="mailto:Rick@contoso.com" />`.</span></span> <span data-ttu-id="c54ae-161">Самозакрывающиеся теги привязки не являются допустимым кодом HTML, поэтому создавать их, как правило, нежелательно, но вам может потребоваться создать самозакрывающуюся вспомогательную функцию тега.</span><span class="sxs-lookup"><span data-stu-id="c54ae-161">Self-closing anchor tags are not valid HTML, so you wouldn't want to create one, but you might want to create a tag helper that's self-closing.</span></span> <span data-ttu-id="c54ae-162">Вспомогательные функции тегов задают тип свойства `TagMode` после считывания тега.</span><span class="sxs-lookup"><span data-stu-id="c54ae-162">Tag helpers set the type of the `TagMode` property after reading a tag.</span></span>

### <a name="processasync"></a><span data-ttu-id="c54ae-163">ProcessAsync</span><span class="sxs-lookup"><span data-stu-id="c54ae-163">ProcessAsync</span></span>

<span data-ttu-id="c54ae-164">В этом разделе мы напишем асинхронную вспомогательную функцию тега электронной почты.</span><span class="sxs-lookup"><span data-stu-id="c54ae-164">In this section, we'll write an asynchronous email helper.</span></span>

1. <span data-ttu-id="c54ae-165">Замените класс `EmailTagHelper` на следующий код:</span><span class="sxs-lookup"><span data-stu-id="c54ae-165">Replace the `EmailTagHelper` class with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   <span data-ttu-id="c54ae-166">**Примечания.**</span><span class="sxs-lookup"><span data-stu-id="c54ae-166">**Notes:**</span></span>

   * <span data-ttu-id="c54ae-167">В этой версии используется асинхронный метод `ProcessAsync`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-167">This version uses the asynchronous `ProcessAsync` method.</span></span> <span data-ttu-id="c54ae-168">Асинхронный метод `GetChildContentAsync` возвращает объект `Task`, содержащий `TagHelperContent`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-168">The asynchronous `GetChildContentAsync` returns a `Task` containing the `TagHelperContent`.</span></span>

   * <span data-ttu-id="c54ae-169">Для получения содержимого элемента HTML используйте параметр `output`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-169">Use the `output` parameter to get contents of the HTML element.</span></span>

1. <span data-ttu-id="c54ae-170">Чтобы вспомогательная функция тега могла получить целевой адрес электронной почты, внесите следующее изменение в файл *Views/Home/Contact.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c54ae-170">Make the following change to the *Views/Home/Contact.cshtml* file so the tag helper can get the target email.</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="c54ae-171">Запустите приложение и убедитесь в том, что ссылки электронной почты создаются правильно.</span><span class="sxs-lookup"><span data-stu-id="c54ae-171">Run the app and verify that it generates valid email links.</span></span>

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a><span data-ttu-id="c54ae-172">RemoveAll, PreContent.SetHtmlContent и PostContent.SetHtmlContent</span><span class="sxs-lookup"><span data-stu-id="c54ae-172">RemoveAll, PreContent.SetHtmlContent and PostContent.SetHtmlContent</span></span>

1. <span data-ttu-id="c54ae-173">Добавьте приведенный ниже класс `BoldTagHelper` в папку *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="c54ae-173">Add the following `BoldTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * <span data-ttu-id="c54ae-174">Атрибут `[HtmlTargetElement]` передает параметр атрибута, который указывает, что любой элемент HTML, содержащий атрибут HTML с именем bold, будет соответствовать условиям и метод переопределения `Process` в классе будет выполняться.</span><span class="sxs-lookup"><span data-stu-id="c54ae-174">The `[HtmlTargetElement]` attribute passes an attribute parameter that specifies that any HTML element that contains an HTML attribute named "bold" will match, and the `Process` override method in the class will run.</span></span> <span data-ttu-id="c54ae-175">В этом примере метод `Process` удаляет атрибут bold и заключает содержащую разметку в теги `<strong></strong>`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-175">In our sample, the `Process` method removes the "bold" attribute and surrounds the containing markup with `<strong></strong>`.</span></span>

   * <span data-ttu-id="c54ae-176">Так как заменять существующее содержимое тега не нужно, открывающий тег `<strong>` должен записываться с помощью метода `PreContent.SetHtmlContent`, а закрывающий тег `</strong>` — с помощью метода `PostContent.SetHtmlContent`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-176">Because you don't want to replace the existing tag content, you must write the opening `<strong>` tag with the `PreContent.SetHtmlContent` method and the closing `</strong>` tag with the `PostContent.SetHtmlContent` method.</span></span>

1. <span data-ttu-id="c54ae-177">Измените представление *About.cshtml* так, чтобы оно содержало значение атрибута `bold`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-177">Modify the *About.cshtml* view to contain a `bold` attribute value.</span></span> <span data-ttu-id="c54ae-178">Ниже приведен готовый код.</span><span class="sxs-lookup"><span data-stu-id="c54ae-178">The completed code is shown below.</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. <span data-ttu-id="c54ae-179">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="c54ae-179">Run the app.</span></span> <span data-ttu-id="c54ae-180">Для проверки исходного кода и разметки можно использовать любой браузер на ваш выбор.</span><span class="sxs-lookup"><span data-stu-id="c54ae-180">You can use your favorite browser to inspect the source and verify the markup.</span></span>

   <span data-ttu-id="c54ae-181">Представленный выше атрибут `[HtmlTargetElement]` предназначен только для разметки HTML, которая предоставляет имя атрибута bold.</span><span class="sxs-lookup"><span data-stu-id="c54ae-181">The `[HtmlTargetElement]` attribute above only targets HTML markup that provides an attribute name of "bold".</span></span> <span data-ttu-id="c54ae-182">Элемент `<bold>` не был изменен вспомогательной функцией тега.</span><span class="sxs-lookup"><span data-stu-id="c54ae-182">The `<bold>` element wasn't modified by the tag helper.</span></span>

1. <span data-ttu-id="c54ae-183">Если закомментировать строку с атрибутом `[HtmlTargetElement]`, функция будет нацелена по умолчанию на теги `<bold>`, то есть на разметку HTML в форме `<bold>`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-183">Comment out the `[HtmlTargetElement]` attribute line and it will default to targeting `<bold>` tags, that is, HTML markup of the form `<bold>`.</span></span> <span data-ttu-id="c54ae-184">Помните, что в соответствии с соглашением об именовании по умолчанию имя класса **Bold** TagHelper соответствует тегам `<bold>`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-184">Remember, the default naming convention will match the class name **Bold** TagHelper to `<bold>` tags.</span></span>

1. <span data-ttu-id="c54ae-185">Запустите приложение и убедитесь в том, что тег `<bold>` обрабатывается вспомогательной функцией тега.</span><span class="sxs-lookup"><span data-stu-id="c54ae-185">Run the app and verify that the `<bold>` tag is processed by the tag helper.</span></span>

<span data-ttu-id="c54ae-186">Если декорировать класс несколькими атрибутами `[HtmlTargetElement]`, к целевым тегам применяется логическая операция ИЛИ.</span><span class="sxs-lookup"><span data-stu-id="c54ae-186">Decorating a class with multiple `[HtmlTargetElement]` attributes results in a logical-OR of the targets.</span></span> <span data-ttu-id="c54ae-187">Например, в приведенном ниже коде условиям будет соответствовать тег bold или атрибут bold.</span><span class="sxs-lookup"><span data-stu-id="c54ae-187">For example, using the code below, a bold tag or a bold attribute will match.</span></span>

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

<span data-ttu-id="c54ae-188">Если в один оператор добавляются несколько атрибутов, среда выполнения применяет к ним логическую операцию И.</span><span class="sxs-lookup"><span data-stu-id="c54ae-188">When multiple attributes are added to the same statement, the runtime treats them as a logical-AND.</span></span> <span data-ttu-id="c54ae-189">Например, в приведенном ниже коде элемент HTML будет соответствовать условиям, если он имеет имя bold и имеет атрибут с именем bold (`<bold bold />`).</span><span class="sxs-lookup"><span data-stu-id="c54ae-189">For example, in the code below, an HTML element must be named "bold" with an attribute named "bold" (`<bold bold />`) to match.</span></span>

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

<span data-ttu-id="c54ae-190">С помощью `[HtmlTargetElement]` можно также изменить имя целевого элемента.</span><span class="sxs-lookup"><span data-stu-id="c54ae-190">You can also use the `[HtmlTargetElement]` to change the name of the targeted element.</span></span> <span data-ttu-id="c54ae-191">Например, чтобы вспомогательная функция `BoldTagHelper` предназначалась для тегов `<MyBold>`, используйте следующий атрибут:</span><span class="sxs-lookup"><span data-stu-id="c54ae-191">For example if you wanted the `BoldTagHelper` to target `<MyBold>` tags, you would use the following attribute:</span></span>

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a><span data-ttu-id="c54ae-192">Передача модели во вспомогательную функцию тега</span><span class="sxs-lookup"><span data-stu-id="c54ae-192">Pass a model to a Tag Helper</span></span>

1. <span data-ttu-id="c54ae-193">Добавьте папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="c54ae-193">Add a *Models* folder.</span></span>

1. <span data-ttu-id="c54ae-194">Добавьте следующий класс `WebsiteContext` в папку *Models*:</span><span class="sxs-lookup"><span data-stu-id="c54ae-194">Add the following `WebsiteContext` class to the *Models* folder:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. <span data-ttu-id="c54ae-195">Добавьте приведенный ниже класс `WebsiteInformationTagHelper` в папку *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="c54ae-195">Add the following `WebsiteInformationTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * <span data-ttu-id="c54ae-196">Как было сказано ранее, имена классов и свойств C# в стиле Pascal для вспомогательных функций тегов преобразуются в [кебаб-стиль](https://wiki.c2.com/?KebabCase).</span><span class="sxs-lookup"><span data-stu-id="c54ae-196">As mentioned previously, tag helpers translates Pascal-cased C# class names and properties for tag helpers into [kebab case](https://wiki.c2.com/?KebabCase).</span></span> <span data-ttu-id="c54ae-197">Таким образом, чтобы использовать `WebsiteInformationTagHelper` в Razor , вы пишете `<website-information />` .</span><span class="sxs-lookup"><span data-stu-id="c54ae-197">Therefore, to use the `WebsiteInformationTagHelper` in Razor, you'll write `<website-information />`.</span></span>

   * <span data-ttu-id="c54ae-198">Целевой элемент не определяется напрямую с помощью атрибута `[HtmlTargetElement]`, поэтому по умолчанию целевым будет элемент `website-information`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-198">You are not explicitly identifying the target element with the `[HtmlTargetElement]` attribute, so the default of `website-information` will be targeted.</span></span> <span data-ttu-id="c54ae-199">Например, вы применяете следующий атрибут (обратите внимание, что он не в стиле кебаб, но соответствует имени класса):</span><span class="sxs-lookup"><span data-stu-id="c54ae-199">If you applied the following attribute (note it's not kebab case but matches the class name):</span></span>

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   <span data-ttu-id="c54ae-200">Ему не будет соответствовать тег в стиле кебаб `<website-information />`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-200">The kebab case tag `<website-information />` wouldn't match.</span></span> <span data-ttu-id="c54ae-201">Чтобы использовать атрибут `[HtmlTargetElement]`, следует применить стиль кебаб, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="c54ae-201">If you want use the `[HtmlTargetElement]` attribute, you would use kebab case as shown below:</span></span>

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * <span data-ttu-id="c54ae-202">Самозакрывающиеся элементы не имеют содержимого.</span><span class="sxs-lookup"><span data-stu-id="c54ae-202">Elements that are self-closing have no content.</span></span> <span data-ttu-id="c54ae-203">В этом примере Razor Разметка будет использовать самозакрывающийся тег, но вспомогательная функция тега будет создавать элемент [section](https://www.w3.org/TR/html5/sections.html#the-section-element) (который не является самостоятельным закрытием и вы пишете содержимое внутри `section` элемента).</span><span class="sxs-lookup"><span data-stu-id="c54ae-203">For this example, the Razor markup will use a self-closing tag, but the tag helper will be creating a [section](https://www.w3.org/TR/html5/sections.html#the-section-element) element (which isn't self-closing and you are writing content inside the `section` element).</span></span> <span data-ttu-id="c54ae-204">Поэтому для записи выходных данных следует присвоить свойству `TagMode` значение `StartTagAndEndTag`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-204">Therefore, you need to set `TagMode` to `StartTagAndEndTag` to write output.</span></span> <span data-ttu-id="c54ae-205">Кроме того, можно закомментировать строку, в которой задается `TagMode`, и написать разметку с закрывающим тегом.</span><span class="sxs-lookup"><span data-stu-id="c54ae-205">Alternatively, you can comment out the line setting `TagMode` and write markup with a closing tag.</span></span> <span data-ttu-id="c54ae-206">(Пример разметки приводится далее в этом руководстве.)</span><span class="sxs-lookup"><span data-stu-id="c54ae-206">(Example markup is provided later in this tutorial.)</span></span>

   * <span data-ttu-id="c54ae-207">Символ `$` (знак доллара) в приведенной ниже строке определяет использование [интерполированной строки](/dotnet/csharp/language-reference/keywords/interpolated-strings).</span><span class="sxs-lookup"><span data-stu-id="c54ae-207">The `$` (dollar sign) in the following line uses an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span></span>

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. <span data-ttu-id="c54ae-208">Добавьте приведенную ниже разметку в представление *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c54ae-208">Add the following markup to the *About.cshtml* view.</span></span> <span data-ttu-id="c54ae-209">Выделенная разметка служит для вывода сведений о веб-сайте.</span><span class="sxs-lookup"><span data-stu-id="c54ae-209">The highlighted markup displays the web site information.</span></span>

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > <span data-ttu-id="c54ae-210">В Razor разметке, показанной ниже:</span><span class="sxs-lookup"><span data-stu-id="c54ae-210">In the Razor markup shown below:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > <span data-ttu-id="c54ae-211">Razor знает, что `info` атрибут является классом, а не строкой, и вы хотите написать код C#.</span><span class="sxs-lookup"><span data-stu-id="c54ae-211">Razor knows the `info` attribute is a class, not a string, and you want to write C# code.</span></span> <span data-ttu-id="c54ae-212">Любой нестроковый атрибут вспомогательной функции тега должен быть записан без символа `@`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-212">Any non-string tag helper attribute should be written without the `@` character.</span></span>

1. <span data-ttu-id="c54ae-213">Запустите приложение и перейдите к представлению About, чтобы просмотреть сведения о веб-сайте.</span><span class="sxs-lookup"><span data-stu-id="c54ae-213">Run the app, and navigate to the About view to see the web site information.</span></span>

   > [!NOTE]
   > <span data-ttu-id="c54ae-214">Вы можете использовать следующую разметку с закрывающим тегом и удалить строку со свойством `TagMode.StartTagAndEndTag` во вспомогательной функции тега:</span><span class="sxs-lookup"><span data-stu-id="c54ae-214">You can use the following markup with a closing tag and remove the line with `TagMode.StartTagAndEndTag` in the tag helper:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a><span data-ttu-id="c54ae-215">Вспомогательная функция тега условия</span><span class="sxs-lookup"><span data-stu-id="c54ae-215">Condition Tag Helper</span></span>

<span data-ttu-id="c54ae-216">Вспомогательная функция тега условия преобразовывает выходные данные для просмотра, если в нее передано значение true.</span><span class="sxs-lookup"><span data-stu-id="c54ae-216">The condition tag helper renders output when passed a true value.</span></span>

1. <span data-ttu-id="c54ae-217">Добавьте приведенный ниже класс `ConditionTagHelper` в папку *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="c54ae-217">Add the following `ConditionTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. <span data-ttu-id="c54ae-218">Замените содержимое файла *Views/Home/Index.cshtml* следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="c54ae-218">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. <span data-ttu-id="c54ae-219">Замените метод `Index` в контроллере `Home` следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c54ae-219">Replace the `Index` method in the `Home` controller with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. <span data-ttu-id="c54ae-220">Запустите приложение и перейдите на домашнюю страницу.</span><span class="sxs-lookup"><span data-stu-id="c54ae-220">Run the app and browse to the home page.</span></span> <span data-ttu-id="c54ae-221">Разметка в условном теге `div` не будет отображаться.</span><span class="sxs-lookup"><span data-stu-id="c54ae-221">The markup in the conditional `div` won't be rendered.</span></span> <span data-ttu-id="c54ae-222">Добавьте строку запроса `?approved=true` к URL-адресу (например, `http://localhost:1235/Home/Index?approved=true`).</span><span class="sxs-lookup"><span data-stu-id="c54ae-222">Append the query string `?approved=true` to the URL (for example, `http://localhost:1235/Home/Index?approved=true`).</span></span> <span data-ttu-id="c54ae-223">`approved` принимает значение true, и условная разметка будет отображаться.</span><span class="sxs-lookup"><span data-stu-id="c54ae-223">`approved` is set to true and the conditional markup will be displayed.</span></span>

> [!NOTE]
> <span data-ttu-id="c54ae-224">Для определения целевого атрибута используйте оператор [nameof](/dotnet/csharp/language-reference/keywords/nameof), а не указывайте строку, как в случае со вспомогательной функцией тега bold:</span><span class="sxs-lookup"><span data-stu-id="c54ae-224">Use the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator to specify the attribute to target rather than specifying a string as you did with the bold tag helper:</span></span>
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> <span data-ttu-id="c54ae-225">Оператор [nameof](/dotnet/csharp/language-reference/keywords/nameof) защищает код в случае его рефакторинга (имя может потребоваться изменить на `RedCondition`).</span><span class="sxs-lookup"><span data-stu-id="c54ae-225">The [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator will protect the code should it ever be refactored (we might want to change the name to `RedCondition`).</span></span>

### <a name="avoid-tag-helper-conflicts"></a><span data-ttu-id="c54ae-226">Как избежать конфликтов между вспомогательными функциями тегов</span><span class="sxs-lookup"><span data-stu-id="c54ae-226">Avoid Tag Helper conflicts</span></span>

<span data-ttu-id="c54ae-227">В этом разделе вы напишете пару вспомогательных функций тегов для автоматического связывания.</span><span class="sxs-lookup"><span data-stu-id="c54ae-227">In this section, you write a pair of auto-linking tag helpers.</span></span> <span data-ttu-id="c54ae-228">Первая функция будет заменять разметку, которая содержит URL-адрес, начинающийся с префикса HTTP, на HTML-тег привязки, содержащий тот же URL-адрес (то есть предоставляющий ссылку на URL-адрес).</span><span class="sxs-lookup"><span data-stu-id="c54ae-228">The first will replace markup containing a URL starting with HTTP to an HTML anchor tag containing the same URL (and thus yielding a link to the URL).</span></span> <span data-ttu-id="c54ae-229">Вторая функция будет делать то же самое для URL-адреса, начинающегося с WWW.</span><span class="sxs-lookup"><span data-stu-id="c54ae-229">The second will do the same for a URL starting with WWW.</span></span>

<span data-ttu-id="c54ae-230">Так как эти две вспомогательные функции тесно связаны друг с другом и в будущем может потребоваться их рефакторинг, мы поместим их в один файл.</span><span class="sxs-lookup"><span data-stu-id="c54ae-230">Because these two helpers are closely related and you may refactor them in the future, we'll keep them in the same file.</span></span>

1. <span data-ttu-id="c54ae-231">Добавьте приведенный ниже класс `AutoLinkerHttpTagHelper` в папку *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="c54ae-231">Add the following `AutoLinkerHttpTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   ><span data-ttu-id="c54ae-232">Для класса `AutoLinkerHttpTagHelper` целевыми являются элементы `p`, и в нем используется [регулярное выражение](/dotnet/standard/base-types/regular-expression-language-quick-reference) для создания привязки.</span><span class="sxs-lookup"><span data-stu-id="c54ae-232">The `AutoLinkerHttpTagHelper` class targets `p` elements and uses [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) to create the anchor.</span></span>

1. <span data-ttu-id="c54ae-233">Добавьте следующую разметку в конец файла *Views/Home/Contact.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c54ae-233">Add the following markup to the end of the *Views/Home/Contact.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. <span data-ttu-id="c54ae-234">Запустите приложение и проверьте, правильно ли вспомогательная функция тега отображает привязку.</span><span class="sxs-lookup"><span data-stu-id="c54ae-234">Run the app and verify that the tag helper renders the anchor correctly.</span></span>

1. <span data-ttu-id="c54ae-235">Обновите класс `AutoLinker`, включив в него функцию `AutoLinkerWwwTagHelper`, которая будет преобразовывать текст с префиксом WWW в тег привязки, который также содержит исходный текст с префиксом WWW.</span><span class="sxs-lookup"><span data-stu-id="c54ae-235">Update the `AutoLinker` class to include the `AutoLinkerWwwTagHelper` which will convert www text to an anchor tag that also contains the original www text.</span></span> <span data-ttu-id="c54ae-236">Обновленный код выделен ниже.</span><span class="sxs-lookup"><span data-stu-id="c54ae-236">The updated code is highlighted below:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. <span data-ttu-id="c54ae-237">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="c54ae-237">Run the app.</span></span> <span data-ttu-id="c54ae-238">Обратите внимание на то, что текст с префиксом WWW отображается как ссылка, а текст с префиксом HTTP — нет.</span><span class="sxs-lookup"><span data-stu-id="c54ae-238">Notice the www text is rendered as a link but the HTTP text isn't.</span></span> <span data-ttu-id="c54ae-239">Если установить точку останова в обоих классах, то можно увидеть, что класс вспомогательной функции тега HTTP выполняется первым.</span><span class="sxs-lookup"><span data-stu-id="c54ae-239">If you put a break point in both classes, you can see that the HTTP tag helper class runs first.</span></span> <span data-ttu-id="c54ae-240">Проблема в том, что выходные данные вспомогательной функции тега кэшируются и, когда выполняется вспомогательная функция тега WWW, она перезаписывает кэшированные выходные данные вспомогательной функции тега HTTP.</span><span class="sxs-lookup"><span data-stu-id="c54ae-240">The problem is that the tag helper output is cached, and when the WWW tag helper is run, it overwrites the cached output from the HTTP tag helper.</span></span> <span data-ttu-id="c54ae-241">Далее в этом руководстве мы узнаем, как управлять очередностью выполнения вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="c54ae-241">Later in the tutorial we'll see how to control the order that tag helpers run in.</span></span> <span data-ttu-id="c54ae-242">Исправим код следующим образом:</span><span class="sxs-lookup"><span data-stu-id="c54ae-242">We'll fix the code with the following:</span></span>

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > <span data-ttu-id="c54ae-243">В первом выпуске вспомогательных функций тегов с автоматическим связыванием для получения содержимого целевых элементов использовался следующий код:</span><span class="sxs-lookup"><span data-stu-id="c54ae-243">In the first edition of the auto-linking tag helpers, you got the content of the target with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > <span data-ttu-id="c54ae-244">То есть вызывался метод `GetChildContentAsync` с передачей объекта `TagHelperOutput` в метод `ProcessAsync`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-244">That is, you call `GetChildContentAsync` using the `TagHelperOutput` passed into the `ProcessAsync` method.</span></span> <span data-ttu-id="c54ae-245">Как было сказано ранее, из-за кэширования выходных данных приоритет имеет последняя выполняемая вспомогательная функция тега.</span><span class="sxs-lookup"><span data-stu-id="c54ae-245">As mentioned previously, because the output is cached, the last tag helper to run wins.</span></span> <span data-ttu-id="c54ae-246">Эта проблема исправлялась с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="c54ae-246">You fixed that problem with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > <span data-ttu-id="c54ae-247">Приведенный выше код проверяет, было ли содержимое изменено. Если да, он получает содержимое из выходного буфера.</span><span class="sxs-lookup"><span data-stu-id="c54ae-247">The code above checks to see if the content has been modified, and if it has, it gets the content from the output buffer.</span></span>

1. <span data-ttu-id="c54ae-248">Запустите приложение и проверьте, правильно ли работают обе ссылки.</span><span class="sxs-lookup"><span data-stu-id="c54ae-248">Run the app and verify that the two links work as expected.</span></span> <span data-ttu-id="c54ae-249">Может показаться, что теперь вспомогательная функция тега с автоматическим связыванием полностью готова и работает правильно, однако есть одна деталь.</span><span class="sxs-lookup"><span data-stu-id="c54ae-249">While it might appear our auto linker tag helper is correct and complete, it has a subtle problem.</span></span> <span data-ttu-id="c54ae-250">Если вспомогательная функция тега WWW выполняется первой, ссылки с префиксом WWW будут неправильными.</span><span class="sxs-lookup"><span data-stu-id="c54ae-250">If the WWW tag helper runs first, the www links won't be correct.</span></span> <span data-ttu-id="c54ae-251">Измените код, добавив перегрузку `Order` для управления очередностью выполнения вспомогательных функций.</span><span class="sxs-lookup"><span data-stu-id="c54ae-251">Update the code by adding the `Order` overload to control the order that the tag runs in.</span></span> <span data-ttu-id="c54ae-252">Свойство `Order` определяет очередность выполнения относительно других вспомогательных функций тегов с тем же целевым элементом.</span><span class="sxs-lookup"><span data-stu-id="c54ae-252">The `Order` property determines the execution order relative to other tag helpers targeting the same element.</span></span> <span data-ttu-id="c54ae-253">Значение очередности по умолчанию — ноль. Экземпляры с более низкими значениями выполняются в первую очередь.</span><span class="sxs-lookup"><span data-stu-id="c54ae-253">The default order value is zero and instances with lower values are executed first.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   <span data-ttu-id="c54ae-254">Приведенный выше код гарантирует, что вспомогательная функция тега HTTP будет выполняться перед вспомогательной функцией тега WWW.</span><span class="sxs-lookup"><span data-stu-id="c54ae-254">The preceding code guarantees that the HTTP tag helper runs before the WWW tag helper.</span></span> <span data-ttu-id="c54ae-255">Измените значение `Order` на `MaxValue` и убедитесь в том, что для тега WWW разметка создается неправильно.</span><span class="sxs-lookup"><span data-stu-id="c54ae-255">Change `Order` to `MaxValue` and verify that the markup generated for the WWW tag is incorrect.</span></span>

## <a name="inspect-and-retrieve-child-content"></a><span data-ttu-id="c54ae-256">Проверка и извлечение содержимого дочернего элемента</span><span class="sxs-lookup"><span data-stu-id="c54ae-256">Inspect and retrieve child content</span></span>

<span data-ttu-id="c54ae-257">Вспомогательные функции тегов предоставляют несколько свойств для извлечения содержимого.</span><span class="sxs-lookup"><span data-stu-id="c54ae-257">The tag helpers provide several properties to retrieve content.</span></span>

* <span data-ttu-id="c54ae-258">Результат выполнения метода `GetChildContentAsync` можно добавить к `output.Content`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-258">The result of `GetChildContentAsync` can be appended to `output.Content`.</span></span>
* <span data-ttu-id="c54ae-259">Результат выполнения метода `GetChildContentAsync` можно проверить с помощью `GetContent`.</span><span class="sxs-lookup"><span data-stu-id="c54ae-259">You can inspect the result of `GetChildContentAsync` with `GetContent`.</span></span>
* <span data-ttu-id="c54ae-260">При изменении `output.Content` тело функции TagHelper будет выполняться или обрабатываться, только если вызвать метод `GetChildContentAsync`, как в нашем примере автоматического связывания.</span><span class="sxs-lookup"><span data-stu-id="c54ae-260">If you modify `output.Content`, the TagHelper body won't be executed or rendered unless you call `GetChildContentAsync` as in our auto-linker sample:</span></span>

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* <span data-ttu-id="c54ae-261">При нескольких вызовах `GetChildContentAsync` возвращается одно и то же значение и тело функции `TagHelper` не выполняется повторно, если только не передать параметр false, который указывает на то, что не следует использовать кэшированный результат.</span><span class="sxs-lookup"><span data-stu-id="c54ae-261">Multiple calls to `GetChildContentAsync` returns the same value and doesn't re-execute the `TagHelper` body unless you pass in a false parameter indicating not to use the cached result.</span></span>

## <a name="load-minified-partial-view-taghelper"></a><span data-ttu-id="c54ae-262">Загрузка функции TagHelper с сокращенным частичным представлением</span><span class="sxs-lookup"><span data-stu-id="c54ae-262">Load minified partial view TagHelper</span></span>

<span data-ttu-id="c54ae-263">В рабочих средах производительность можно улучшить путем загрузки сокращенных частичных представлений.</span><span class="sxs-lookup"><span data-stu-id="c54ae-263">In production environments, performance can be improved by loading minified partial views.</span></span> <span data-ttu-id="c54ae-264">Чтобы воспользоваться сокращенным частичным представлением в рабочей среде:</span><span class="sxs-lookup"><span data-stu-id="c54ae-264">To take advantage of minified partial view in production:</span></span>

* <span data-ttu-id="c54ae-265">Создайте или настройте процесс предварительной сборки, который минимизирует частичные представления.</span><span class="sxs-lookup"><span data-stu-id="c54ae-265">Create/set up a pre-build process that minifies partial views.</span></span>
* <span data-ttu-id="c54ae-266">Используйте следующий код, чтобы загрузить сокращенные частичные представления в средах, не предназначенных для разработки.</span><span class="sxs-lookup"><span data-stu-id="c54ae-266">Use the following code to load  minified partial views in non-development environments.</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
