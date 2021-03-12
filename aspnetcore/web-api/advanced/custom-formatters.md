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
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Пользовательские модули форматирования для веб-API в ASP.NET Core

[Kirk Ларкин](https://twitter.com/serpent5) и [Tom Dykstra)](https://github.com/tdykstra).

ASP.NET Core MVC поддерживает обмен данными в веб-API с помощью форматировщиков ввода и вывода. Форматировщики ввода используются [привязкой модели](xref:mvc/models/model-binding). Модули форматирования выходных данных используются для [форматирования ответов](xref:web-api/advanced/formatting).

Платформа предоставляет встроенные форматировщики ввода и вывода для JSON и XML. Доступен только встроенный форматировщик вывода для обычного текста, но не форматировщик ввода для обычного текста.

В этой статье показано, как добавить поддержку дополнительных форматов, создав пользовательские модули форматирования. Пример пользовательского модуля форматирования ввода обычного текста см. в разделе [текстплаининпутформаттер](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) на GitHub.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Когда следует использовать пользовательские модули форматирования

Используйте пользовательский модуль форматирования, чтобы добавить поддержку для типа содержимого, который не обрабатывается встроенными модулями форматирования.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Общие сведения об использовании пользовательского модуля форматирования

Чтобы создать пользовательский модуль форматирования, сделайте следующее:

* Для сериализации данных, отправляемых клиенту, создайте класс модуля форматирования выходных данных.
* Для десериализации данных, полученных от клиента, создайте класс модуля форматирования ввода.
* Добавьте экземпляры классов модулей форматирования в `InputFormatters` коллекции и `OutputFormatters` в <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .

## <a name="how-to-create-a-custom-formatter-class"></a>Создание пользовательского класса модуля форматирования

Чтобы создать модуль форматирования, выполните указанные ниже действия.

* Наследуйте класс от подходящего базового класса. Пример приложения является производным от <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> и <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .
* Укажите в конструкторе допустимые типы передаваемых данных и кодировки.
* Переопределите методы <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> и <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A>.
* Переопределите методы <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> и `WriteResponseBodyAsync`.

В следующем коде показан `VcardOutputFormatter` класс из [примера](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples):

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a>Наследование от подходящего базового класса

Для типов текстовых файлов (например, vCard) следует наследовать от <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> базового класса или.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

Для двоичных типов является производным от <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> базового класса или.

### <a name="specify-valid-media-types-and-encodings"></a>Указание допустимых типов передаваемых данных и кодировок.

Укажите в конструкторе допустимые типы передаваемых данных и кодировки, добавив их в коллекции `SupportedMediaTypes` и `SupportedEncodings`.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

Класс модуля форматирования **не** может использовать внедрение конструктора для своих зависимостей. Например, `ILogger<VcardOutputFormatter>` нельзя добавить в конструктор в качестве параметра. Для доступа к службам используйте объект контекста, который передается методам. Пример кода в этой статье и [Пример](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples) показывает, как это сделать.

### <a name="override-canreadtype-and-canwritetype"></a>Переопределение Канреадтипе и Канвритетипе

Укажите тип для десериализации или сериализации из путем переопределения `CanReadType` `CanWriteType` методов или. Например, создание текста vCard из `Contact` типа и наоборот.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a>Метод CanWriteResult

В некоторых сценариях `CanWriteResult` должен быть переопределен, а не `CanWriteType` . Используйте `CanWriteResult`, если выполняются указанные ниже условия.

* Метод действия возвращает класс модели.
* Существуют производные классы, которые могут возвращаться во время выполнения.
* Производный класс, возвращенный действием, должен быть известен во время выполнения.

Например, предположим, что метод действия:

* Сигнатура возвращает `Person` тип.
* Может возвращать `Student` `Instructor` тип или, производный от `Person` . 

Чтобы модуль форматирования обрабатывал только `Student` объекты, проверьте тип <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> в объекте контекста, предоставленном `CanWriteResult` методу. Когда метод действия возвращает `IActionResult` :

* Нет необходимости использовать `CanWriteResult` .
* `CanWriteType`Метод получает тип среды выполнения.

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>Переопределение Реадрекуестбодясинк и Вритереспонсебодясинк

Десериализация или сериализация выполняется в `ReadRequestBodyAsync` или `WriteResponseBodyAsync` . В следующем примере показано, как получить службы из контейнера внедрения зависимостей. Не удается получить службы из параметров конструктора.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Настройка использования пользовательского модуля форматирования в MVC

Для использования пользовательского модуля форматирования добавьте экземпляр его класса в коллекцию `InputFormatters` или `OutputFormatters`.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

Модули форматирования обрабатываются в порядке добавления. Первый модуль имеет приоритет.

## <a name="the-complete-vcardinputformatter-class"></a>Полный `VcardInputFormatter` класс

В следующем коде показан `VcardInputFormatter` класс из [примера](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples):

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a>Тестирование приложения

[Запустите пример приложения для этой статьи, в](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples)котором реализованы базовые модули форматирования входных и выходных данных vCard. Приложение считывает и записывает визитные карточки следующего вида:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

Чтобы просмотреть выходные данные vCard, запустите приложение и отправьте запрос GET с заголовком Accept `text/vcard` в `https://localhost:5001/api/contacts` .

Чтобы добавить vCard в коллекцию контактов в памяти, выполните следующие действия.

* Отправка `Post` запроса `/api/contacts` с помощью такого средства, как POST.
* В качестве заголовка `Content-Type` установите `text/vcard`.
* Задайте `vCard` текст в тексте, отформатированный как в предыдущем примере.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
