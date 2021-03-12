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
# <a name="configure-portable-object-localization-in-aspnet-core"></a>Настройка локализации переносных объектов в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Авторы: [Себастьен Рос](https://github.com/sebastienros) (Sébastien Ros), [Скотт Эдди](https://twitter.com/Scott_Addie) (Scott Addie) и [Хишам Бин Атея](https://github.com/hishamco) (Hisham Bin Ateya)

Эта статья описывает действия по использованию файлов переносимых объектов в приложении ASP.NET Core с помощью платформы [Orchard Core](https://github.com/OrchardCMS/OrchardCore).

**Примечание.** Orchard Core не является продуктом корпорации Майкрософт. Поэтому корпорация Майкрософт не предоставляет поддержку для этого компонента.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>Что такое файл переносимых объектов?

Файлы переносимых объектов (PO) распространяются как текстовые файлы со строками, переведенными на заданный язык. Файлы PO обеспечивают определенные преимущества по сравнению с файлами *RESX*, например:
- Файлы PO поддерживают преобразование во множественную форму, файлы *RESX* — нет.
- Файлы PO не компилируются подобно файлам *RESX*. Поэтому потребность в специальных инструментах и действиях сборки отпадает.
- Файлы PO хорошо работают со средствами редактирования, предназначенными для совместной работы по сети.

### <a name="example"></a>Пример

Ниже приведен пример файла PO, содержащего перевод двух строк на французский язык, для одной из которых приведена форма во множественном числе:

*fr.po*

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

В этом примере используется следующий синтаксис:

- `#:`. комментарий, указывающий контекст переводимой строки. Одну и ту же строку можно перевести по-разному в зависимости от характера ее использования.
- `msgid`. непереведенная строка.
- `msgstr`. переведенная строка.

При поддержке преобразования во множественную форму можно определить дополнительные записи.

- `msgid_plural`. непереведенная строка во множественной форме.
- `msgstr[0]`. переведенная строка для случая 0.
- `msgstr[N]`. переведенная строка для случая N.

Спецификацию файла PO см. [здесь](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).

## <a name="configuring-po-file-support-in-aspnet-core"></a>Настройка поддержки файлов PO в ASP.NET Core

Этот пример основан на приложении ASP.NET Core MVC, созданном из шаблона проекта Visual Studio 2017.

### <a name="referencing-the-package"></a>Указание ссылок на пакет

Добавьте ссылку на пакет NuGet `OrchardCore.Localization.Core`.

Файл *CSPROJ* теперь содержит строку следующего вида (номер версии может отличаться):

[!code-xml[](localization/sample/3.x/POLocalization/POLocalization.csproj?range=8)]

### <a name="registering-the-service"></a>Регистрация службы

Добавьте необходимые службы в метод `ConfigureServices` файла *Startup.cs*:

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Добавьте необходимое ПО промежуточного слоя в метод `Configure` файла *Startup.cs*:

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Добавьте следующий код в нужное представление Razor. В этом примере используется *About.cshtml*.

[!code-cshtml[](localization/sample/3.x/POLocalization/Views/Home/About.cshtml)]

Экземпляр `IViewLocalizer` внедряется и используется для перевода текста "Hello world!".

### <a name="creating-a-po-file"></a>Создание файла PO

Создайте файл с именем *\<culture code>.po* в корневой папке приложения. В этом примере файл имеет имя *fr.po*, так как используется французский язык:

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

Данный файл содержит как строку на перевод, так и строку, переведенную на французский язык. При необходимости переводы возвращаются к своим родительским языку и региональным параметрам. В этом примере файл *fr.po* используется, если запрошены язык и региональные параметры `fr-FR` или `fr-CA`.

### <a name="testing-the-application"></a>Тестирование приложения

Запустите приложение и перейдите по URL-адресу `/Home/About`. Отображается текст **Hello world!** .

Перейдите по URL-адресу `/Home/About?culture=fr-FR`. Отображается текст **Bonjour le monde!** .

## <a name="pluralization"></a>Преобразование во множественную форму

Файлы PO поддерживают преобразование во множественную форму, что удобно, когда одну и ту же строку нужно переводить по-разному в зависимости от кратности. Эта задача осложняется тем, что каждый язык имеет собственные правила для выбора строк в зависимости от кратности.

Пакет локализации Orchard предоставляет API для автоматического вызова этих различных форм множественного числа.

### <a name="creating-pluralization-po-files"></a>Создание файлов PO с множественными формами

Добавьте в указанный ранее файл *fr.po* следующее содержимое:

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

Сведения о том, что означает каждая запись в этом примере, см. в разделе [Что такое файл переносимых объектов?](#what-is-a-po-file).

### <a name="adding-a-language-using-different-pluralization-forms"></a>Добавление языка с использованием различных форм множественного числа

В предыдущем примере использовались строки на английском и французском языках. Эти языки имеют всего две формы множественного числа и используют схожие правила. Кратность, равная единице, соответствует первой форме множественного числа. Любая другая кратность соответствует второй форме множественного числа.

Подобные правила действуют не во всех языках. Это показано на примере чешского языка, где форм множественного числа три.

Создайте файл `cs.po`, как показано ниже, и обратите внимание, что для преобразования во множественную форму нужно три разных перевода:

[!code-text[](localization/sample/3.x/POLocalization/cs.po)]

Чтобы принять локализации на чешский язык, добавьте `"cs"` в список поддерживаемых языков и региональных параметров в методе `ConfigureServices`:

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

Измените файл *Views/Home/About.cshtml*, чтобы отобразить локализованные строки во множественном числе для разных кратностей:

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**Примечание.** В реальной ситуации для представления этого числа используется переменная. Здесь мы повторяем один и тот же код с тремя различными значениями, чтобы описать частный случай.

После переключения языка и региональных параметров вы увидите следующее:

Для `/Home/About`:

```html
There is one item.
There are 2 items.
There are 5 items.
```

Для `/Home/About?culture=fr`:

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

Для `/Home/About?culture=cs`:

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

Обратите внимание, что для чешского языка все три перевода различаются. На французском и английском языках для двух последних переведенных строк используется одинаковая конструкция.

## <a name="advanced-tasks"></a>Расширенные задачи

### <a name="contextualizing-strings"></a>Учет контекста строк

Приложения часто содержат переводимые строки в нескольких местах. Одна и та же строка, находящаяся в разных местах приложения (представления Razor или файлы классов), может переводиться по-разному. Файл PO поддерживает понятие контекста файла, которое можно использовать для классификации представляемых строк. В зависимости от контекста файла (или его отсутствия) строку можно перевести по-разному.

Службы локализации PO используют имя полного класса или представление, используемые при переводе строки. Это достигается путем установки значения для записи `msgctxt`.

Рассмотрим небольшое дополнение к предыдущему примеру *fr.po*. Представление Razor, расположенное в файле *Views/Home/About.cshtml*, можно определить в качестве контекста файла, задав зарезервированное значение записи `msgctxt`.

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

При такой настройке `msgctxt` перевод текста отображается при переходе по адресу `/Home/About?culture=fr-FR`. При переходе по адресу `/Home/Contact?culture=fr-FR` перевод не отображается.

Если с заданным контекстом файла не совпадает ни одна из конкретных записей, резервный механизм Orchard Core ищет соответствующий файл PO без контекста. Если предположить, что для *Views/Home/Contact.cshtml* не задан никакой контекст, при переходе по адресу `/Home/Contact?culture=fr-FR` загружается файл PO, как показано ниже:

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>Изменение расположения для файлов PO

Расположение по умолчанию для файлов PO можно изменить в `ConfigureServices`:

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

В этом примере файлы PO загружаются из папки *Localization*.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Реализация настраиваемой логики для поиска файлов локализации

Когда для поиска файлов PO требуется более сложная логика, можно реализовать интерфейс `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` и зарегистрировать его как службу. Это удобно, когда файлы PO могут храниться в различных расположениях или должны находиться в иерархии папок.

### <a name="using-a-different-default-pluralized-language"></a>Использование языка с другими формами множественного числа по умолчанию

Этот пакет содержит метод расширения `Plural`, характерный для двух форм множественного числа. Для языков с большим числом форм множественного числа нужно создать отдельный метод расширения. При наличии метода расширения вам не потребуется предоставлять файл локализации для языка по умолчанию &mdash;, исходные строки уже доступны непосредственно в коде.

Можно использовать более универсальную перегрузку `Plural(int count, string[] pluralForms, params object[] arguments)`, которая принимает строковый массив переводов.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Авторы: [Себастьен Рос](https://github.com/sebastienros) (Sébastien Ros) и [Скотт Эдди](https://twitter.com/Scott_Addie) (Scott Addie)

Эта статья описывает действия по использованию файлов переносимых объектов в приложении ASP.NET Core с помощью платформы [Orchard Core](https://github.com/OrchardCMS/OrchardCore).

**Примечание.** Orchard Core не является продуктом корпорации Майкрософт. Поэтому корпорация Майкрософт не предоставляет поддержку для этого компонента.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>Что такое файл переносимых объектов?

Файлы переносимых объектов (PO) распространяются как текстовые файлы со строками, переведенными на заданный язык. Файлы PO дают определенные преимущества по сравнению с файлами *RESX*, например:
- Файлы PO поддерживают преобразование во множественную форму, файлы *RESX* — нет.
- Файлы PO не компилируются подобно файлам *RESX*. Поэтому потребность в специальных инструментах и действиях сборки отпадает.
- Файлы PO хорошо работают со средствами редактирования, предназначенными для совместной работы по сети.

### <a name="example"></a>Пример

Ниже приведен пример файла PO, содержащего перевод двух строк на французский язык, для одной из которых приведена форма во множественном числе:

*fr.po*

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

В этом примере используется следующий синтаксис:

- `#:`. комментарий, указывающий контекст переводимой строки. Одну и ту же строку можно перевести по-разному в зависимости от характера ее использования.
- `msgid`. непереведенная строка.
- `msgstr`. переведенная строка.

При поддержке преобразования во множественную форму можно определить дополнительные записи.

- `msgid_plural`. непереведенная строка во множественной форме.
- `msgstr[0]`. переведенная строка для случая 0.
- `msgstr[N]`. переведенная строка для случая N.

Спецификацию файла PO см. [здесь](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).

## <a name="configuring-po-file-support-in-aspnet-core"></a>Настройка поддержки файлов PO в ASP.NET Core

Этот пример основан на приложении ASP.NET Core MVC, созданном из шаблона проекта Visual Studio 2017.

### <a name="referencing-the-package"></a>Указание ссылок на пакет

Добавьте ссылку на пакет NuGet `OrchardCore.Localization.Core`.

Файл *CSPROJ* теперь содержит строку следующего вида (номер версии может отличаться):

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>Регистрация службы

Добавьте необходимые службы в метод `ConfigureServices` файла *Startup.cs*:

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Добавьте необходимое ПО промежуточного слоя в метод `Configure` файла *Startup.cs*:

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Добавьте следующий код в нужное представление Razor. В этом примере используется *About.cshtml*.

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

Экземпляр `IViewLocalizer` внедряется и используется для перевода текста "Hello world!".

### <a name="creating-a-po-file"></a>Создание файла PO

Создайте файл с именем *\<culture code>.po* в корневой папке приложения. В этом примере файл имеет имя *fr.po*, так как используется французский язык:

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

Данный файл содержит как строку на перевод, так и строку, переведенную на французский язык. При необходимости переводы возвращаются к своим родительским языку и региональным параметрам. В этом примере файл *fr.po* используется, если запрошены язык и региональные параметры `fr-FR` или `fr-CA`.

### <a name="testing-the-application"></a>Тестирование приложения

Запустите приложение и перейдите по URL-адресу `/Home/About`. Отображается текст **Hello world!** .

Перейдите по URL-адресу `/Home/About?culture=fr-FR`. Отображается текст **Bonjour le monde!** .

## <a name="pluralization"></a>Преобразование во множественную форму

Файлы PO поддерживают преобразование во множественную форму, что удобно, когда одну и ту же строку нужно переводить по-разному в зависимости от кратности. Эта задача осложняется тем, что каждый язык имеет собственные правила для выбора строк в зависимости от кратности.

Пакет локализации Orchard предоставляет API для автоматического вызова этих различных форм множественного числа.

### <a name="creating-pluralization-po-files"></a>Создание файлов PO с множественными формами

Добавьте в указанный ранее файл *fr.po* следующее содержимое:

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

Сведения о том, что означает каждая запись в этом примере, см. в разделе [Что такое файл переносимых объектов?](#what-is-a-po-file).

### <a name="adding-a-language-using-different-pluralization-forms"></a>Добавление языка с использованием различных форм множественного числа

В предыдущем примере использовались строки на английском и французском языках. Эти языки имеют всего две формы множественного числа и используют схожие правила. Кратность, равная единице, соответствует первой форме множественного числа. Любая другая кратность соответствует второй форме множественного числа.

Подобные правила действуют не во всех языках. Это показано на примере чешского языка, где форм множественного числа три.

Создайте файл `cs.po`, как показано ниже, и обратите внимание, что для преобразования во множественную форму нужно три разных перевода:

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

Чтобы принять локализации на чешский язык, добавьте `"cs"` в список поддерживаемых языков и региональных параметров в методе `ConfigureServices`:

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

Измените файл *Views/Home/About.cshtml*, чтобы отобразить локализованные строки во множественном числе для разных кратностей:

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**Примечание.** В реальной ситуации для представления этого числа используется переменная. Здесь мы повторяем один и тот же код с тремя различными значениями, чтобы описать частный случай.

После переключения языка и региональных параметров вы увидите следующее:

Для `/Home/About`:

```html
There is one item.
There are 2 items.
There are 5 items.
```

Для `/Home/About?culture=fr`:

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

Для `/Home/About?culture=cs`:

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

Обратите внимание, что для чешского языка все три перевода различаются. На французском и английском языках для двух последних переведенных строк используется одинаковая конструкция.

## <a name="advanced-tasks"></a>Расширенные задачи

### <a name="contextualizing-strings"></a>Учет контекста строк

Приложения часто содержат переводимые строки в нескольких местах. Одна и та же строка, находящаяся в разных местах приложения (представления Razor или файлы классов), может переводиться по-разному. Файл PO поддерживает понятие контекста файла, которое можно использовать для классификации представляемых строк. В зависимости от контекста файла (или его отсутствия) строку можно перевести по-разному.

Службы локализации PO используют имя полного класса или представление, используемые при переводе строки. Это достигается путем установки значения для записи `msgctxt`.

Рассмотрим небольшое дополнение к предыдущему примеру *fr.po*. Представление Razor, расположенное в файле *Views/Home/About.cshtml*, можно определить в качестве контекста файла, задав зарезервированное значение записи `msgctxt`.

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

При такой настройке `msgctxt` перевод текста отображается при переходе по адресу `/Home/About?culture=fr-FR`. При переходе по адресу `/Home/Contact?culture=fr-FR` перевод не отображается.

Если с заданным контекстом файла не совпадает ни одна из конкретных записей, резервный механизм Orchard Core ищет соответствующий файл PO без контекста. Если предположить, что для *Views/Home/Contact.cshtml* не задан никакой контекст, при переходе по адресу `/Home/Contact?culture=fr-FR` загружается файл PO, как показано ниже:

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>Изменение расположения для файлов PO

Расположение по умолчанию для файлов PO можно изменить в `ConfigureServices`:

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

В этом примере файлы PO загружаются из папки *Localization*.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Реализация настраиваемой логики для поиска файлов локализации

Когда для поиска файлов PO требуется более сложная логика, можно реализовать интерфейс `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` и зарегистрировать его как службу. Это удобно, когда файлы PO могут храниться в различных расположениях или должны находиться в иерархии папок.

### <a name="using-a-different-default-pluralized-language"></a>Использование языка с другими формами множественного числа по умолчанию

Этот пакет содержит метод расширения `Plural`, характерный для двух форм множественного числа. Для языков с большим числом форм множественного числа нужно создать отдельный метод расширения. При наличии метода расширения вам не потребуется предоставлять файл локализации для языка по умолчанию &mdash;, исходные строки уже доступны непосредственно в коде.

Можно использовать более универсальную перегрузку `Plural(int count, string[] pluralForms, params object[] arguments)`, которая принимает строковый массив переводов.

::: moniker-end
