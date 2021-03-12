---
title: Высокопроизводительное ведение журналов с помощью LoggerMessage в ASP.NET Core
author: rick-anderson
description: Сведения о том, как использовать LoggerMessage для создания кэшируемых делегатов, которым нужно меньше выделений объектов в сценариях высокопроизводительного ведения журналов.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
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
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: d6fae8d2e056c0a578b64322bb82027523904254
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587402"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a>Высокопроизводительное ведение журналов с помощью LoggerMessage в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Функции <xref:Microsoft.Extensions.Logging.LoggerMessage> создают кэшируемые делегаты, которым нужно меньше выделений объектов и вычислительных ресурсов, чем [методам расширения для средства ведения журнала](xref:Microsoft.Extensions.Logging.LoggerExtensions), таким как <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> и <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>. Для сценариев высокопроизводительного ведения журналов используйте шаблон <xref:Microsoft.Extensions.Logging.LoggerMessage>.

<xref:Microsoft.Extensions.Logging.LoggerMessage> предоставляет следующие преимущества производительности по сравнению с методами расширения для средства ведения журнала:

* Методы расширения для средства ведения журнала требуют "упаковку-преобразование" типов значений, таких как `int`, в `object`. Шаблон <xref:Microsoft.Extensions.Logging.LoggerMessage> позволяет избежать упаковки-преобразования за счет статических полей <xref:System.Action> и методов расширения со строго типизированными параметрами.
* Методы расширения для средства ведения журнала должны анализировать шаблон сообщения (именованную строку формата) при каждой записи сообщения журнала. <xref:Microsoft.Extensions.Logging.LoggerMessage> требует анализировать шаблон всего один раз — при определении сообщения.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/logging/loggermessage/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

Пример приложения показывает функции <xref:Microsoft.Extensions.Logging.LoggerMessage> с базовой системой отслеживания цитат. Это приложение добавляет и удаляет цитаты, используя выполняющуюся в памяти базу данных. По мере выполнения этих операций создаются сообщения журнала с помощью шаблона <xref:Microsoft.Extensions.Logging.LoggerMessage>.

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) создает делегат <xref:System.Action> для внесения сообщения в журнал. Перегрузки <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> позволяют передать до шести параметров типа в именованную строку формата (шаблон).

Строка, предоставляемая методу <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, является шаблоном, а не интерполированной строкой. Заполнители заполняются в том порядке, в котором указаны типы. Имена заполнителей в шаблоне должны быть описательными и согласованными между разными шаблонами. Они выступают в качестве имен свойств в структурированных данных журнала. Мы рекомендуем использовать [стиль Pascal ](/dotnet/standard/design-guidelines/capitalization-conventions) для имен заполнителей. Например, `{Count}`, `{FirstName}`.

Каждое сообщение журнала является <xref:System.Action>, хранящимся в статическом поле, созданном [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*). Например, пример приложения создает поле для описания сообщения журнала для запроса GET для страницы индексов (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

Для <xref:System.Action> укажите:

* уровень ведения журнала;
* уникальный идентификатор события (<xref:Microsoft.Extensions.Logging.EventId>) с именем статического метода расширения;
* шаблон сообщения (именованная строка формата). 

Запрос страницы индексов в примере приложения задает:

* уровень ведения журнала — `Information`;
* идентификатор события — `1` с именем метода `IndexPageRequested`;
* шаблон сообщения (именованная строка формата) — строка.

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

Структурированные хранилища для ведения журнала могут использовать имя события, когда оно предоставляется вместе с идентификатором события, для улучшения ведения журнала. Например, [Serilog](https://github.com/serilog/serilog-extensions-logging) использует имя события.

<xref:System.Action> вызывается с помощью строго типизированного метода расширения. Метод `IndexPageRequested` заносит в журнал сообщение для запроса GET страницы индексов в примере приложения:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested` вызывается для средства ведения журнала в методе `OnGetAsync` в *Pages/Index.cshtml.cs*:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Изучите выходные данные консоли приложения:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Чтобы передать параметры в сообщение журнала, определите до шести типов при создании статического поля. Пример приложения регистрирует строку при добавлении цитаты, определяя тип `string` для поля <xref:System.Action>:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

Шаблон сообщения журнала делегата получает его значения заполнителей из предоставленных типов. Пример приложения определяет делегат для добавления цитаты, где параметр квоты имеет значение`string`:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

Статический метод расширения для добавления цитаты `QuoteAdded` получает значение аргумента цитаты и передает его в делегат <xref:System.Action>:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

В страничной модели страницы индексов (*Pages/Index.cshtml.cs*) для регистрации сообщения в журнале вызывается `QuoteAdded`:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Изучите выходные данные консоли приложения:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

Пример приложения реализует шаблон [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) для удаления цитаты. Для успешной операции удаления регистрируется информационное сообщение. Сообщение об ошибке регистрируется для операции удаления, когда возникает исключение. Сообщение журнала для неудачной операции удаления включает трассировку стека исключений (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

Обратите внимание, как исключение передается делегату в `QuoteDeleteFailed`:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

В страничной модели для страницы индексов успешная операция удаления цитаты вызывает метод `QuoteDeleted` для средства ведения журнала. Если удаляемая цитата не найдена, возникает исключение <xref:System.ArgumentNullException>. Это исключение перехватывается инструкцией [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) и регистрируется путем вызова метода `QuoteDeleteFailed` для средства ведения журнала в блоке [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*Pages/Index.cshtml.cs*).

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

При успешном удалении цитаты изучите выходные данные консоли приложения:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

При неудачном удалении цитаты изучите выходные данные консоли приложения. Обратите внимание, что исключение входит в состав сообщения журнала:

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) создает делегат <xref:System.Func%601> для определения [области журнала](xref:fundamentals/logging/index#log-scopes). Перегрузки <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> позволяют передать до трех параметров типа в именованную строку формата (шаблон).

Как и в случае с методом <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, строка, предоставляемая методу <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>, является шаблоном, а не интерполированной строкой. Заполнители заполняются в том порядке, в котором указаны типы. Имена заполнителей в шаблоне должны быть описательными и согласованными между разными шаблонами. Они выступают в качестве имен свойств в структурированных данных журнала. Мы рекомендуем использовать [стиль Pascal ](/dotnet/standard/design-guidelines/capitalization-conventions) для имен заполнителей. Например, `{Count}`, `{FirstName}`.

Определите [область журнала](xref:fundamentals/logging/index#log-scopes), чтобы применить последовательность сообщений журнала с помощью метода <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>.

Пример приложения имеет кнопку **Clear All** (Очистить все), позволяющую удалить все цитаты в базе данных. Удаление цитат производится по одной за раз. При каждом удалении цитаты для средства ведения журнала вызывается метод `QuoteDeleted`. В эти сообщения журнала добавляется область журнала.

Включите `IncludeScopes` в разделе средства ведения журнала в файле *appsettings.json* :

[!code-json[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

Для создания области журнала добавьте поле, содержащее делегат <xref:System.Func%601> для области. Пример приложения создает поле `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

Используйте <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> для создания делегата. Можно указать до трех типов для использования в качестве аргументов шаблона при вызове делегата. Пример приложения использует шаблон сообщения, включающий в себя число удаленных цитат (тип `int`):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

Предоставьте статический метод расширения для сообщения журнала. Включите любые параметры типа для именованных свойств, отображаемых в шаблоне сообщений. Пример приложения принимает число удаляемых цитат `count` и возвращает `_allQuotesDeletedScope`:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

Область создает оболочку для вызовов расширения ведения журнала в блоке [using](/dotnet/csharp/language-reference/keywords/using-statement).

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Изучите сообщения журнала в выходных данных консоли приложения: Следующий результат указывает три удаленных цитаты с включенным сообщением области журнала:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Функции <xref:Microsoft.Extensions.Logging.LoggerMessage> создают кэшируемые делегаты, которым нужно меньше выделений объектов и вычислительных ресурсов, чем [методам расширения для средства ведения журнала](xref:Microsoft.Extensions.Logging.LoggerExtensions), таким как <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> и <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>. Для сценариев высокопроизводительного ведения журналов используйте шаблон <xref:Microsoft.Extensions.Logging.LoggerMessage>.

<xref:Microsoft.Extensions.Logging.LoggerMessage> предоставляет следующие преимущества производительности по сравнению с методами расширения для средства ведения журнала:

* Методы расширения для средства ведения журнала требуют "упаковку-преобразование" типов значений, таких как `int`, в `object`. Шаблон <xref:Microsoft.Extensions.Logging.LoggerMessage> позволяет избежать упаковки-преобразования за счет статических полей <xref:System.Action> и методов расширения со строго типизированными параметрами.
* Методы расширения для средства ведения журнала должны анализировать шаблон сообщения (именованную строку формата) при каждой записи сообщения журнала. <xref:Microsoft.Extensions.Logging.LoggerMessage> требует анализировать шаблон всего один раз — при определении сообщения.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/logging/loggermessage/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

Пример приложения показывает функции <xref:Microsoft.Extensions.Logging.LoggerMessage> с базовой системой отслеживания цитат. Это приложение добавляет и удаляет цитаты, используя выполняющуюся в памяти базу данных. По мере выполнения этих операций создаются сообщения журнала с помощью шаблона <xref:Microsoft.Extensions.Logging.LoggerMessage>.

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) создает делегат <xref:System.Action> для внесения сообщения в журнал. Перегрузки <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> позволяют передать до шести параметров типа в именованную строку формата (шаблон).

Строка, предоставляемая методу <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, является шаблоном, а не интерполированной строкой. Заполнители заполняются в том порядке, в котором указаны типы. Имена заполнителей в шаблоне должны быть описательными и согласованными между разными шаблонами. Они выступают в качестве имен свойств в структурированных данных журнала. Мы рекомендуем использовать [стиль Pascal ](/dotnet/standard/design-guidelines/capitalization-conventions) для имен заполнителей. Например, `{Count}`, `{FirstName}`.

Каждое сообщение журнала является <xref:System.Action>, хранящимся в статическом поле, созданном [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*). Например, пример приложения создает поле для описания сообщения журнала для запроса GET для страницы индексов (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

Для <xref:System.Action> укажите:

* уровень ведения журнала;
* уникальный идентификатор события (<xref:Microsoft.Extensions.Logging.EventId>) с именем статического метода расширения;
* шаблон сообщения (именованная строка формата). 

Запрос страницы индексов в примере приложения задает:

* уровень ведения журнала — `Information`;
* идентификатор события — `1` с именем метода `IndexPageRequested`;
* шаблон сообщения (именованная строка формата) — строка.

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

Структурированные хранилища для ведения журнала могут использовать имя события, когда оно предоставляется вместе с идентификатором события, для улучшения ведения журнала. Например, [Serilog](https://github.com/serilog/serilog-extensions-logging) использует имя события.

<xref:System.Action> вызывается с помощью строго типизированного метода расширения. Метод `IndexPageRequested` заносит в журнал сообщение для запроса GET страницы индексов в примере приложения:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested` вызывается для средства ведения журнала в методе `OnGetAsync` в *Pages/Index.cshtml.cs*:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Изучите выходные данные консоли приложения:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Чтобы передать параметры в сообщение журнала, определите до шести типов при создании статического поля. Пример приложения регистрирует строку при добавлении цитаты, определяя тип `string` для поля <xref:System.Action>:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

Шаблон сообщения журнала делегата получает его значения заполнителей из предоставленных типов. Пример приложения определяет делегат для добавления цитаты, где параметр квоты имеет значение`string`:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

Статический метод расширения для добавления цитаты `QuoteAdded` получает значение аргумента цитаты и передает его в делегат <xref:System.Action>:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

В страничной модели страницы индексов (*Pages/Index.cshtml.cs*) для регистрации сообщения в журнале вызывается `QuoteAdded`:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Изучите выходные данные консоли приложения:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

Пример приложения реализует шаблон [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) для удаления цитаты. Для успешной операции удаления регистрируется информационное сообщение. Сообщение об ошибке регистрируется для операции удаления, когда возникает исключение. Сообщение журнала для неудачной операции удаления включает трассировку стека исключений (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

Обратите внимание, как исключение передается делегату в `QuoteDeleteFailed`:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

В страничной модели для страницы индексов успешная операция удаления цитаты вызывает метод `QuoteDeleted` для средства ведения журнала. Если удаляемая цитата не найдена, возникает исключение <xref:System.ArgumentNullException>. Это исключение перехватывается инструкцией [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) и регистрируется путем вызова метода `QuoteDeleteFailed` для средства ведения журнала в блоке [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*Pages/Index.cshtml.cs*).

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

При успешном удалении цитаты изучите выходные данные консоли приложения:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

При неудачном удалении цитаты изучите выходные данные консоли приложения. Обратите внимание, что исключение входит в состав сообщения журнала:

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) создает делегат <xref:System.Func%601> для определения [области журнала](xref:fundamentals/logging/index#log-scopes). Перегрузки <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> позволяют передать до трех параметров типа в именованную строку формата (шаблон).

Как и в случае с методом <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, строка, предоставляемая методу <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>, является шаблоном, а не интерполированной строкой. Заполнители заполняются в том порядке, в котором указаны типы. Имена заполнителей в шаблоне должны быть описательными и согласованными между разными шаблонами. Они выступают в качестве имен свойств в структурированных данных журнала. Мы рекомендуем использовать [стиль Pascal ](/dotnet/standard/design-guidelines/capitalization-conventions) для имен заполнителей. Например, `{Count}`, `{FirstName}`.

Определите [область журнала](xref:fundamentals/logging/index#log-scopes), чтобы применить последовательность сообщений журнала с помощью метода <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>.

Пример приложения имеет кнопку **Clear All** (Очистить все), позволяющую удалить все цитаты в базе данных. Удаление цитат производится по одной за раз. При каждом удалении цитаты для средства ведения журнала вызывается метод `QuoteDeleted`. В эти сообщения журнала добавляется область журнала.

Включите `IncludeScopes` в разделе средства ведения журнала в файле *appsettings.json* :

[!code-json[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

Для создания области журнала добавьте поле, содержащее делегат <xref:System.Func%601> для области. Пример приложения создает поле `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

Используйте <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> для создания делегата. Можно указать до трех типов для использования в качестве аргументов шаблона при вызове делегата. Пример приложения использует шаблон сообщения, включающий в себя число удаленных цитат (тип `int`):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

Предоставьте статический метод расширения для сообщения журнала. Включите любые параметры типа для именованных свойств, отображаемых в шаблоне сообщений. Пример приложения принимает число удаляемых цитат `count` и возвращает `_allQuotesDeletedScope`:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

Область создает оболочку для вызовов расширения ведения журнала в блоке [using](/dotnet/csharp/language-reference/keywords/using-statement).

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Изучите сообщения журнала в выходных данных консоли приложения: Следующий результат указывает три удаленных цитаты с включенным сообщением области журнала:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Ведение журнала](xref:fundamentals/logging/index)
