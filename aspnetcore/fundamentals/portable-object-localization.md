---
title: Настройка локализации переносных объектов в ASP.NET Core
author: sebastienros
description: Эта статья содержит вводные сведения о файлах переносимых объектов и описывает действия по их использованию в приложении ASP.NET Core с помощью платформы Orchard Core.
ms.author: scaddie
ms.date: 09/26/2017
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
uid: fundamentals/portable-object-localization
ms.openlocfilehash: ed41657b364c7f845491b3e452db8a4d5c5aa389
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587181"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="452d8-103">Настройка локализации переносных объектов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="452d8-103">Configure portable object localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="452d8-104">Авторы: [Себастьен Рос](https://github.com/sebastienros) (Sébastien Ros), [Скотт Эдди](https://twitter.com/Scott_Addie) (Scott Addie) и [Хишам Бин Атея](https://github.com/hishamco) (Hisham Bin Ateya)</span><span class="sxs-lookup"><span data-stu-id="452d8-104">By [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) and [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="452d8-105">Эта статья описывает действия по использованию файлов переносимых объектов в приложении ASP.NET Core с помощью платформы [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="452d8-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="452d8-106">**Примечание.** Orchard Core не является продуктом корпорации Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="452d8-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="452d8-107">Поэтому корпорация Майкрософт не предоставляет поддержку для этого компонента.</span><span class="sxs-lookup"><span data-stu-id="452d8-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="452d8-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="452d8-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="452d8-109">Что такое файл переносимых объектов?</span><span class="sxs-lookup"><span data-stu-id="452d8-109">What is a PO file?</span></span>

<span data-ttu-id="452d8-110">Файлы переносимых объектов (PO) распространяются как текстовые файлы со строками, переведенными на заданный язык.</span><span class="sxs-lookup"><span data-stu-id="452d8-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="452d8-111">Файлы PO обеспечивают определенные преимущества по сравнению с файлами *RESX*, например:</span><span class="sxs-lookup"><span data-stu-id="452d8-111">Some advantages of using PO files instead of *.resx* files include:</span></span>
- <span data-ttu-id="452d8-112">Файлы PO поддерживают преобразование во множественную форму, файлы *RESX* — нет.</span><span class="sxs-lookup"><span data-stu-id="452d8-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="452d8-113">Файлы PO не компилируются подобно файлам *RESX*.</span><span class="sxs-lookup"><span data-stu-id="452d8-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="452d8-114">Поэтому потребность в специальных инструментах и действиях сборки отпадает.</span><span class="sxs-lookup"><span data-stu-id="452d8-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="452d8-115">Файлы PO хорошо работают со средствами редактирования, предназначенными для совместной работы по сети.</span><span class="sxs-lookup"><span data-stu-id="452d8-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="452d8-116">Пример</span><span class="sxs-lookup"><span data-stu-id="452d8-116">Example</span></span>

<span data-ttu-id="452d8-117">Ниже приведен пример файла PO, содержащего перевод двух строк на французский язык, для одной из которых приведена форма во множественном числе:</span><span class="sxs-lookup"><span data-stu-id="452d8-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="452d8-118">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="452d8-118">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="452d8-119">В этом примере используется следующий синтаксис:</span><span class="sxs-lookup"><span data-stu-id="452d8-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="452d8-120">`#:`. комментарий, указывающий контекст переводимой строки.</span><span class="sxs-lookup"><span data-stu-id="452d8-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="452d8-121">Одну и ту же строку можно перевести по-разному в зависимости от характера ее использования.</span><span class="sxs-lookup"><span data-stu-id="452d8-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="452d8-122">`msgid`. непереведенная строка.</span><span class="sxs-lookup"><span data-stu-id="452d8-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="452d8-123">`msgstr`. переведенная строка.</span><span class="sxs-lookup"><span data-stu-id="452d8-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="452d8-124">При поддержке преобразования во множественную форму можно определить дополнительные записи.</span><span class="sxs-lookup"><span data-stu-id="452d8-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="452d8-125">`msgid_plural`. непереведенная строка во множественной форме.</span><span class="sxs-lookup"><span data-stu-id="452d8-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="452d8-126">`msgstr[0]`. переведенная строка для случая 0.</span><span class="sxs-lookup"><span data-stu-id="452d8-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="452d8-127">`msgstr[N]`. переведенная строка для случая N.</span><span class="sxs-lookup"><span data-stu-id="452d8-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="452d8-128">Спецификацию файла PO см. [здесь](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="452d8-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="452d8-129">Настройка поддержки файлов PO в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="452d8-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="452d8-130">Этот пример основан на приложении ASP.NET Core MVC, созданном из шаблона проекта Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="452d8-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="452d8-131">Указание ссылок на пакет</span><span class="sxs-lookup"><span data-stu-id="452d8-131">Referencing the package</span></span>

<span data-ttu-id="452d8-132">Добавьте ссылку на пакет NuGet `OrchardCore.Localization.Core`.</span><span class="sxs-lookup"><span data-stu-id="452d8-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

<span data-ttu-id="452d8-133">Файл *CSPROJ* теперь содержит строку следующего вида (номер версии может отличаться):</span><span class="sxs-lookup"><span data-stu-id="452d8-133">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/3.x/POLocalization/POLocalization.csproj?range=8)]

### <a name="registering-the-service"></a><span data-ttu-id="452d8-134">Регистрация службы</span><span class="sxs-lookup"><span data-stu-id="452d8-134">Registering the service</span></span>

<span data-ttu-id="452d8-135">Добавьте необходимые службы в метод `ConfigureServices` файла *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="452d8-135">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="452d8-136">Добавьте необходимое ПО промежуточного слоя в метод `Configure` файла *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="452d8-136">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="452d8-137">Добавьте следующий код в нужное представление Razor.</span><span class="sxs-lookup"><span data-stu-id="452d8-137">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="452d8-138">В этом примере используется *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="452d8-138">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/3.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="452d8-139">Экземпляр `IViewLocalizer` внедряется и используется для перевода текста "Hello world!".</span><span class="sxs-lookup"><span data-stu-id="452d8-139">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="452d8-140">Создание файла PO</span><span class="sxs-lookup"><span data-stu-id="452d8-140">Creating a PO file</span></span>

<span data-ttu-id="452d8-141">Создайте файл с именем *\<culture code>.po* в корневой папке приложения.</span><span class="sxs-lookup"><span data-stu-id="452d8-141">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="452d8-142">В этом примере файл имеет имя *fr.po*, так как используется французский язык:</span><span class="sxs-lookup"><span data-stu-id="452d8-142">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

<span data-ttu-id="452d8-143">Данный файл содержит как строку на перевод, так и строку, переведенную на французский язык.</span><span class="sxs-lookup"><span data-stu-id="452d8-143">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="452d8-144">При необходимости переводы возвращаются к своим родительским языку и региональным параметрам.</span><span class="sxs-lookup"><span data-stu-id="452d8-144">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="452d8-145">В этом примере файл *fr.po* используется, если запрошены язык и региональные параметры `fr-FR` или `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="452d8-145">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="452d8-146">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="452d8-146">Testing the application</span></span>

<span data-ttu-id="452d8-147">Запустите приложение и перейдите по URL-адресу `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="452d8-147">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="452d8-148">Отображается текст **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="452d8-148">The text **Hello world!**</span></span> <span data-ttu-id="452d8-149">.</span><span class="sxs-lookup"><span data-stu-id="452d8-149">is displayed.</span></span>

<span data-ttu-id="452d8-150">Перейдите по URL-адресу `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="452d8-150">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="452d8-151">Отображается текст **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="452d8-151">The text **Bonjour le monde!**</span></span> <span data-ttu-id="452d8-152">.</span><span class="sxs-lookup"><span data-stu-id="452d8-152">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="452d8-153">Преобразование во множественную форму</span><span class="sxs-lookup"><span data-stu-id="452d8-153">Pluralization</span></span>

<span data-ttu-id="452d8-154">Файлы PO поддерживают преобразование во множественную форму, что удобно, когда одну и ту же строку нужно переводить по-разному в зависимости от кратности.</span><span class="sxs-lookup"><span data-stu-id="452d8-154">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="452d8-155">Эта задача осложняется тем, что каждый язык имеет собственные правила для выбора строк в зависимости от кратности.</span><span class="sxs-lookup"><span data-stu-id="452d8-155">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="452d8-156">Пакет локализации Orchard предоставляет API для автоматического вызова этих различных форм множественного числа.</span><span class="sxs-lookup"><span data-stu-id="452d8-156">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="452d8-157">Создание файлов PO с множественными формами</span><span class="sxs-lookup"><span data-stu-id="452d8-157">Creating pluralization PO files</span></span>

<span data-ttu-id="452d8-158">Добавьте в указанный ранее файл *fr.po* следующее содержимое:</span><span class="sxs-lookup"><span data-stu-id="452d8-158">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="452d8-159">Сведения о том, что означает каждая запись в этом примере, см. в разделе [Что такое файл переносимых объектов?](#what-is-a-po-file).</span><span class="sxs-lookup"><span data-stu-id="452d8-159">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="452d8-160">Добавление языка с использованием различных форм множественного числа</span><span class="sxs-lookup"><span data-stu-id="452d8-160">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="452d8-161">В предыдущем примере использовались строки на английском и французском языках.</span><span class="sxs-lookup"><span data-stu-id="452d8-161">English and French strings were used in the previous example.</span></span> <span data-ttu-id="452d8-162">Эти языки имеют всего две формы множественного числа и используют схожие правила. Кратность, равная единице, соответствует первой форме множественного числа.</span><span class="sxs-lookup"><span data-stu-id="452d8-162">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="452d8-163">Любая другая кратность соответствует второй форме множественного числа.</span><span class="sxs-lookup"><span data-stu-id="452d8-163">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="452d8-164">Подобные правила действуют не во всех языках.</span><span class="sxs-lookup"><span data-stu-id="452d8-164">Not all languages share the same rules.</span></span> <span data-ttu-id="452d8-165">Это показано на примере чешского языка, где форм множественного числа три.</span><span class="sxs-lookup"><span data-stu-id="452d8-165">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="452d8-166">Создайте файл `cs.po`, как показано ниже, и обратите внимание, что для преобразования во множественную форму нужно три разных перевода:</span><span class="sxs-lookup"><span data-stu-id="452d8-166">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/cs.po)]

<span data-ttu-id="452d8-167">Чтобы принять локализации на чешский язык, добавьте `"cs"` в список поддерживаемых языков и региональных параметров в методе `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="452d8-167">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="452d8-168">Измените файл *Views/Home/About.cshtml*, чтобы отобразить локализованные строки во множественном числе для разных кратностей:</span><span class="sxs-lookup"><span data-stu-id="452d8-168">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="452d8-169">**Примечание.** В реальной ситуации для представления этого числа используется переменная.</span><span class="sxs-lookup"><span data-stu-id="452d8-169">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="452d8-170">Здесь мы повторяем один и тот же код с тремя различными значениями, чтобы описать частный случай.</span><span class="sxs-lookup"><span data-stu-id="452d8-170">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="452d8-171">После переключения языка и региональных параметров вы увидите следующее:</span><span class="sxs-lookup"><span data-stu-id="452d8-171">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="452d8-172">Для `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="452d8-172">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="452d8-173">Для `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="452d8-173">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="452d8-174">Для `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="452d8-174">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="452d8-175">Обратите внимание, что для чешского языка все три перевода различаются.</span><span class="sxs-lookup"><span data-stu-id="452d8-175">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="452d8-176">На французском и английском языках для двух последних переведенных строк используется одинаковая конструкция.</span><span class="sxs-lookup"><span data-stu-id="452d8-176">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="452d8-177">Расширенные задачи</span><span class="sxs-lookup"><span data-stu-id="452d8-177">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="452d8-178">Учет контекста строк</span><span class="sxs-lookup"><span data-stu-id="452d8-178">Contextualizing strings</span></span>

<span data-ttu-id="452d8-179">Приложения часто содержат переводимые строки в нескольких местах.</span><span class="sxs-lookup"><span data-stu-id="452d8-179">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="452d8-180">Одна и та же строка, находящаяся в разных местах приложения (представления Razor или файлы классов), может переводиться по-разному.</span><span class="sxs-lookup"><span data-stu-id="452d8-180">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="452d8-181">Файл PO поддерживает понятие контекста файла, которое можно использовать для классификации представляемых строк.</span><span class="sxs-lookup"><span data-stu-id="452d8-181">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="452d8-182">В зависимости от контекста файла (или его отсутствия) строку можно перевести по-разному.</span><span class="sxs-lookup"><span data-stu-id="452d8-182">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="452d8-183">Службы локализации PO используют имя полного класса или представление, используемые при переводе строки.</span><span class="sxs-lookup"><span data-stu-id="452d8-183">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="452d8-184">Это достигается путем установки значения для записи `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="452d8-184">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="452d8-185">Рассмотрим небольшое дополнение к предыдущему примеру *fr.po*.</span><span class="sxs-lookup"><span data-stu-id="452d8-185">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="452d8-186">Представление Razor, расположенное в файле *Views/Home/About.cshtml*, можно определить в качестве контекста файла, задав зарезервированное значение записи `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="452d8-186">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="452d8-187">При такой настройке `msgctxt` перевод текста отображается при переходе по адресу `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="452d8-187">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="452d8-188">При переходе по адресу `/Home/Contact?culture=fr-FR` перевод не отображается.</span><span class="sxs-lookup"><span data-stu-id="452d8-188">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="452d8-189">Если с заданным контекстом файла не совпадает ни одна из конкретных записей, резервный механизм Orchard Core ищет соответствующий файл PO без контекста.</span><span class="sxs-lookup"><span data-stu-id="452d8-189">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="452d8-190">Если предположить, что для *Views/Home/Contact.cshtml* не задан никакой контекст, при переходе по адресу `/Home/Contact?culture=fr-FR` загружается файл PO, как показано ниже:</span><span class="sxs-lookup"><span data-stu-id="452d8-190">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="452d8-191">Изменение расположения для файлов PO</span><span class="sxs-lookup"><span data-stu-id="452d8-191">Changing the location of PO files</span></span>

<span data-ttu-id="452d8-192">Расположение по умолчанию для файлов PO можно изменить в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="452d8-192">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="452d8-193">В этом примере файлы PO загружаются из папки *Localization*.</span><span class="sxs-lookup"><span data-stu-id="452d8-193">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="452d8-194">Реализация настраиваемой логики для поиска файлов локализации</span><span class="sxs-lookup"><span data-stu-id="452d8-194">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="452d8-195">Когда для поиска файлов PO требуется более сложная логика, можно реализовать интерфейс `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` и зарегистрировать его как службу.</span><span class="sxs-lookup"><span data-stu-id="452d8-195">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="452d8-196">Это удобно, когда файлы PO могут храниться в различных расположениях или должны находиться в иерархии папок.</span><span class="sxs-lookup"><span data-stu-id="452d8-196">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="452d8-197">Использование языка с другими формами множественного числа по умолчанию</span><span class="sxs-lookup"><span data-stu-id="452d8-197">Using a different default pluralized language</span></span>

<span data-ttu-id="452d8-198">Этот пакет содержит метод расширения `Plural`, характерный для двух форм множественного числа.</span><span class="sxs-lookup"><span data-stu-id="452d8-198">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="452d8-199">Для языков с большим числом форм множественного числа нужно создать отдельный метод расширения.</span><span class="sxs-lookup"><span data-stu-id="452d8-199">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="452d8-200">При наличии метода расширения вам не потребуется предоставлять файл локализации для языка по умолчанию &mdash;, исходные строки уже доступны непосредственно в коде.</span><span class="sxs-lookup"><span data-stu-id="452d8-200">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="452d8-201">Можно использовать более универсальную перегрузку `Plural(int count, string[] pluralForms, params object[] arguments)`, которая принимает строковый массив переводов.</span><span class="sxs-lookup"><span data-stu-id="452d8-201">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="452d8-202">Авторы: [Себастьен Рос](https://github.com/sebastienros) (Sébastien Ros) и [Скотт Эдди](https://twitter.com/Scott_Addie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="452d8-202">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="452d8-203">Эта статья описывает действия по использованию файлов переносимых объектов в приложении ASP.NET Core с помощью платформы [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="452d8-203">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="452d8-204">**Примечание.** Orchard Core не является продуктом корпорации Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="452d8-204">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="452d8-205">Поэтому корпорация Майкрософт не предоставляет поддержку для этого компонента.</span><span class="sxs-lookup"><span data-stu-id="452d8-205">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="452d8-206">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="452d8-206">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="452d8-207">Что такое файл переносимых объектов?</span><span class="sxs-lookup"><span data-stu-id="452d8-207">What is a PO file?</span></span>

<span data-ttu-id="452d8-208">Файлы переносимых объектов (PO) распространяются как текстовые файлы со строками, переведенными на заданный язык.</span><span class="sxs-lookup"><span data-stu-id="452d8-208">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="452d8-209">Файлы PO дают определенные преимущества по сравнению с файлами *RESX*, например:</span><span class="sxs-lookup"><span data-stu-id="452d8-209">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="452d8-210">Файлы PO поддерживают преобразование во множественную форму, файлы *RESX* — нет.</span><span class="sxs-lookup"><span data-stu-id="452d8-210">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="452d8-211">Файлы PO не компилируются подобно файлам *RESX*.</span><span class="sxs-lookup"><span data-stu-id="452d8-211">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="452d8-212">Поэтому потребность в специальных инструментах и действиях сборки отпадает.</span><span class="sxs-lookup"><span data-stu-id="452d8-212">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="452d8-213">Файлы PO хорошо работают со средствами редактирования, предназначенными для совместной работы по сети.</span><span class="sxs-lookup"><span data-stu-id="452d8-213">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="452d8-214">Пример</span><span class="sxs-lookup"><span data-stu-id="452d8-214">Example</span></span>

<span data-ttu-id="452d8-215">Ниже приведен пример файла PO, содержащего перевод двух строк на французский язык, для одной из которых приведена форма во множественном числе:</span><span class="sxs-lookup"><span data-stu-id="452d8-215">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="452d8-216">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="452d8-216">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="452d8-217">В этом примере используется следующий синтаксис:</span><span class="sxs-lookup"><span data-stu-id="452d8-217">This example uses the following syntax:</span></span>

- <span data-ttu-id="452d8-218">`#:`. комментарий, указывающий контекст переводимой строки.</span><span class="sxs-lookup"><span data-stu-id="452d8-218">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="452d8-219">Одну и ту же строку можно перевести по-разному в зависимости от характера ее использования.</span><span class="sxs-lookup"><span data-stu-id="452d8-219">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="452d8-220">`msgid`. непереведенная строка.</span><span class="sxs-lookup"><span data-stu-id="452d8-220">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="452d8-221">`msgstr`. переведенная строка.</span><span class="sxs-lookup"><span data-stu-id="452d8-221">`msgstr`: The translated string.</span></span>

<span data-ttu-id="452d8-222">При поддержке преобразования во множественную форму можно определить дополнительные записи.</span><span class="sxs-lookup"><span data-stu-id="452d8-222">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="452d8-223">`msgid_plural`. непереведенная строка во множественной форме.</span><span class="sxs-lookup"><span data-stu-id="452d8-223">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="452d8-224">`msgstr[0]`. переведенная строка для случая 0.</span><span class="sxs-lookup"><span data-stu-id="452d8-224">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="452d8-225">`msgstr[N]`. переведенная строка для случая N.</span><span class="sxs-lookup"><span data-stu-id="452d8-225">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="452d8-226">Спецификацию файла PO см. [здесь](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="452d8-226">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="452d8-227">Настройка поддержки файлов PO в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="452d8-227">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="452d8-228">Этот пример основан на приложении ASP.NET Core MVC, созданном из шаблона проекта Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="452d8-228">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="452d8-229">Указание ссылок на пакет</span><span class="sxs-lookup"><span data-stu-id="452d8-229">Referencing the package</span></span>

<span data-ttu-id="452d8-230">Добавьте ссылку на пакет NuGet `OrchardCore.Localization.Core`.</span><span class="sxs-lookup"><span data-stu-id="452d8-230">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

<span data-ttu-id="452d8-231">Файл *CSPROJ* теперь содержит строку следующего вида (номер версии может отличаться):</span><span class="sxs-lookup"><span data-stu-id="452d8-231">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="452d8-232">Регистрация службы</span><span class="sxs-lookup"><span data-stu-id="452d8-232">Registering the service</span></span>

<span data-ttu-id="452d8-233">Добавьте необходимые службы в метод `ConfigureServices` файла *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="452d8-233">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="452d8-234">Добавьте необходимое ПО промежуточного слоя в метод `Configure` файла *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="452d8-234">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="452d8-235">Добавьте следующий код в нужное представление Razor.</span><span class="sxs-lookup"><span data-stu-id="452d8-235">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="452d8-236">В этом примере используется *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="452d8-236">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="452d8-237">Экземпляр `IViewLocalizer` внедряется и используется для перевода текста "Hello world!".</span><span class="sxs-lookup"><span data-stu-id="452d8-237">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="452d8-238">Создание файла PO</span><span class="sxs-lookup"><span data-stu-id="452d8-238">Creating a PO file</span></span>

<span data-ttu-id="452d8-239">Создайте файл с именем *\<culture code>.po* в корневой папке приложения.</span><span class="sxs-lookup"><span data-stu-id="452d8-239">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="452d8-240">В этом примере файл имеет имя *fr.po*, так как используется французский язык:</span><span class="sxs-lookup"><span data-stu-id="452d8-240">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="452d8-241">Данный файл содержит как строку на перевод, так и строку, переведенную на французский язык.</span><span class="sxs-lookup"><span data-stu-id="452d8-241">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="452d8-242">При необходимости переводы возвращаются к своим родительским языку и региональным параметрам.</span><span class="sxs-lookup"><span data-stu-id="452d8-242">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="452d8-243">В этом примере файл *fr.po* используется, если запрошены язык и региональные параметры `fr-FR` или `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="452d8-243">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="452d8-244">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="452d8-244">Testing the application</span></span>

<span data-ttu-id="452d8-245">Запустите приложение и перейдите по URL-адресу `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="452d8-245">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="452d8-246">Отображается текст **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="452d8-246">The text **Hello world!**</span></span> <span data-ttu-id="452d8-247">.</span><span class="sxs-lookup"><span data-stu-id="452d8-247">is displayed.</span></span>

<span data-ttu-id="452d8-248">Перейдите по URL-адресу `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="452d8-248">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="452d8-249">Отображается текст **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="452d8-249">The text **Bonjour le monde!**</span></span> <span data-ttu-id="452d8-250">.</span><span class="sxs-lookup"><span data-stu-id="452d8-250">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="452d8-251">Преобразование во множественную форму</span><span class="sxs-lookup"><span data-stu-id="452d8-251">Pluralization</span></span>

<span data-ttu-id="452d8-252">Файлы PO поддерживают преобразование во множественную форму, что удобно, когда одну и ту же строку нужно переводить по-разному в зависимости от кратности.</span><span class="sxs-lookup"><span data-stu-id="452d8-252">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="452d8-253">Эта задача осложняется тем, что каждый язык имеет собственные правила для выбора строк в зависимости от кратности.</span><span class="sxs-lookup"><span data-stu-id="452d8-253">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="452d8-254">Пакет локализации Orchard предоставляет API для автоматического вызова этих различных форм множественного числа.</span><span class="sxs-lookup"><span data-stu-id="452d8-254">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="452d8-255">Создание файлов PO с множественными формами</span><span class="sxs-lookup"><span data-stu-id="452d8-255">Creating pluralization PO files</span></span>

<span data-ttu-id="452d8-256">Добавьте в указанный ранее файл *fr.po* следующее содержимое:</span><span class="sxs-lookup"><span data-stu-id="452d8-256">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="452d8-257">Сведения о том, что означает каждая запись в этом примере, см. в разделе [Что такое файл переносимых объектов?](#what-is-a-po-file).</span><span class="sxs-lookup"><span data-stu-id="452d8-257">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="452d8-258">Добавление языка с использованием различных форм множественного числа</span><span class="sxs-lookup"><span data-stu-id="452d8-258">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="452d8-259">В предыдущем примере использовались строки на английском и французском языках.</span><span class="sxs-lookup"><span data-stu-id="452d8-259">English and French strings were used in the previous example.</span></span> <span data-ttu-id="452d8-260">Эти языки имеют всего две формы множественного числа и используют схожие правила. Кратность, равная единице, соответствует первой форме множественного числа.</span><span class="sxs-lookup"><span data-stu-id="452d8-260">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="452d8-261">Любая другая кратность соответствует второй форме множественного числа.</span><span class="sxs-lookup"><span data-stu-id="452d8-261">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="452d8-262">Подобные правила действуют не во всех языках.</span><span class="sxs-lookup"><span data-stu-id="452d8-262">Not all languages share the same rules.</span></span> <span data-ttu-id="452d8-263">Это показано на примере чешского языка, где форм множественного числа три.</span><span class="sxs-lookup"><span data-stu-id="452d8-263">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="452d8-264">Создайте файл `cs.po`, как показано ниже, и обратите внимание, что для преобразования во множественную форму нужно три разных перевода:</span><span class="sxs-lookup"><span data-stu-id="452d8-264">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="452d8-265">Чтобы принять локализации на чешский язык, добавьте `"cs"` в список поддерживаемых языков и региональных параметров в методе `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="452d8-265">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="452d8-266">Измените файл *Views/Home/About.cshtml*, чтобы отобразить локализованные строки во множественном числе для разных кратностей:</span><span class="sxs-lookup"><span data-stu-id="452d8-266">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="452d8-267">**Примечание.** В реальной ситуации для представления этого числа используется переменная.</span><span class="sxs-lookup"><span data-stu-id="452d8-267">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="452d8-268">Здесь мы повторяем один и тот же код с тремя различными значениями, чтобы описать частный случай.</span><span class="sxs-lookup"><span data-stu-id="452d8-268">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="452d8-269">После переключения языка и региональных параметров вы увидите следующее:</span><span class="sxs-lookup"><span data-stu-id="452d8-269">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="452d8-270">Для `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="452d8-270">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="452d8-271">Для `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="452d8-271">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="452d8-272">Для `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="452d8-272">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="452d8-273">Обратите внимание, что для чешского языка все три перевода различаются.</span><span class="sxs-lookup"><span data-stu-id="452d8-273">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="452d8-274">На французском и английском языках для двух последних переведенных строк используется одинаковая конструкция.</span><span class="sxs-lookup"><span data-stu-id="452d8-274">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="452d8-275">Расширенные задачи</span><span class="sxs-lookup"><span data-stu-id="452d8-275">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="452d8-276">Учет контекста строк</span><span class="sxs-lookup"><span data-stu-id="452d8-276">Contextualizing strings</span></span>

<span data-ttu-id="452d8-277">Приложения часто содержат переводимые строки в нескольких местах.</span><span class="sxs-lookup"><span data-stu-id="452d8-277">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="452d8-278">Одна и та же строка, находящаяся в разных местах приложения (представления Razor или файлы классов), может переводиться по-разному.</span><span class="sxs-lookup"><span data-stu-id="452d8-278">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="452d8-279">Файл PO поддерживает понятие контекста файла, которое можно использовать для классификации представляемых строк.</span><span class="sxs-lookup"><span data-stu-id="452d8-279">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="452d8-280">В зависимости от контекста файла (или его отсутствия) строку можно перевести по-разному.</span><span class="sxs-lookup"><span data-stu-id="452d8-280">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="452d8-281">Службы локализации PO используют имя полного класса или представление, используемые при переводе строки.</span><span class="sxs-lookup"><span data-stu-id="452d8-281">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="452d8-282">Это достигается путем установки значения для записи `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="452d8-282">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="452d8-283">Рассмотрим небольшое дополнение к предыдущему примеру *fr.po*.</span><span class="sxs-lookup"><span data-stu-id="452d8-283">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="452d8-284">Представление Razor, расположенное в файле *Views/Home/About.cshtml*, можно определить в качестве контекста файла, задав зарезервированное значение записи `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="452d8-284">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="452d8-285">При такой настройке `msgctxt` перевод текста отображается при переходе по адресу `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="452d8-285">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="452d8-286">При переходе по адресу `/Home/Contact?culture=fr-FR` перевод не отображается.</span><span class="sxs-lookup"><span data-stu-id="452d8-286">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="452d8-287">Если с заданным контекстом файла не совпадает ни одна из конкретных записей, резервный механизм Orchard Core ищет соответствующий файл PO без контекста.</span><span class="sxs-lookup"><span data-stu-id="452d8-287">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="452d8-288">Если предположить, что для *Views/Home/Contact.cshtml* не задан никакой контекст, при переходе по адресу `/Home/Contact?culture=fr-FR` загружается файл PO, как показано ниже:</span><span class="sxs-lookup"><span data-stu-id="452d8-288">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="452d8-289">Изменение расположения для файлов PO</span><span class="sxs-lookup"><span data-stu-id="452d8-289">Changing the location of PO files</span></span>

<span data-ttu-id="452d8-290">Расположение по умолчанию для файлов PO можно изменить в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="452d8-290">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="452d8-291">В этом примере файлы PO загружаются из папки *Localization*.</span><span class="sxs-lookup"><span data-stu-id="452d8-291">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="452d8-292">Реализация настраиваемой логики для поиска файлов локализации</span><span class="sxs-lookup"><span data-stu-id="452d8-292">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="452d8-293">Когда для поиска файлов PO требуется более сложная логика, можно реализовать интерфейс `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` и зарегистрировать его как службу.</span><span class="sxs-lookup"><span data-stu-id="452d8-293">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="452d8-294">Это удобно, когда файлы PO могут храниться в различных расположениях или должны находиться в иерархии папок.</span><span class="sxs-lookup"><span data-stu-id="452d8-294">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="452d8-295">Использование языка с другими формами множественного числа по умолчанию</span><span class="sxs-lookup"><span data-stu-id="452d8-295">Using a different default pluralized language</span></span>

<span data-ttu-id="452d8-296">Этот пакет содержит метод расширения `Plural`, характерный для двух форм множественного числа.</span><span class="sxs-lookup"><span data-stu-id="452d8-296">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="452d8-297">Для языков с большим числом форм множественного числа нужно создать отдельный метод расширения.</span><span class="sxs-lookup"><span data-stu-id="452d8-297">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="452d8-298">При наличии метода расширения вам не потребуется предоставлять файл локализации для языка по умолчанию &mdash;, исходные строки уже доступны непосредственно в коде.</span><span class="sxs-lookup"><span data-stu-id="452d8-298">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="452d8-299">Можно использовать более универсальную перегрузку `Plural(int count, string[] pluralForms, params object[] arguments)`, которая принимает строковый массив переводов.</span><span class="sxs-lookup"><span data-stu-id="452d8-299">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end
