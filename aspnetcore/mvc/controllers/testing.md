---
title: Тестирование логики контроллера в ASP.NET Core
author: ardalis
description: Узнайте, как протестировать логику контроллера в ASP.NET Core с помощью Moq и xUnit.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: mvc/controllers/testing
ms.openlocfilehash: 0f4fcceefdef946a5f29633c069cbb64ec345a3d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586960"
---
# <a name="unit-test-controller-logic-in-aspnet-core"></a>Модульное тестирование логики контроллера в ASP.NET Core

Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)

::: moniker range=">= aspnetcore-3.0"

[Модульное тестирование](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) предполагает тестирование части приложения изолированно от его инфраструктуры и зависимостей. При модульном тестировании логики контроллера тестируется только содержимое отдельного действия, но не поведение его зависимостей или самой платформы.

## <a name="unit-testing-controllers"></a>Модульное тестирование контроллеров

Настройте модульные тесты для действий контроллера, чтобы сосредоточиться на поведении контроллера. В модульных тестах контроллера не учитываются такие аспекты, как [фильтрация](xref:mvc/controllers/filters), [маршрутизация](xref:fundamentals/routing) и (или) [привязка модели](xref:mvc/models/model-binding). Тесты, учитывающие взаимодействие компонентов, участвующих в ответе на запрос, включены в *интеграционные тесты*. Дополнительные сведения об интеграционных тестах см. в статье <xref:test/integration-tests>.

Если вы создаете пользовательские фильтры и маршруты, проводите для них модульное тестирование изолированно, но не в рамках тестирования определенного действия контроллера.

Для демонстрации модульных тестов контроллера давайте рассмотрим контроллер в приведенном ниже примере приложения. 

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/testing/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

Этот контроллер Home выводит список сеансов мозгового штурма и позволяет создавать новые сеансы с помощью запроса POST.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Предыдущий контроллер:

* Соблюдает [Принцип явных зависимостей](/dotnet/architecture/modern-web-apps-azure/architectural-principles#explicit-dependencies).
* Ожидает, что [внедрение зависимостей (DI)](xref:fundamentals/dependency-injection) предоставит экземпляр `IBrainstormSessionRepository`.
* Допускает проверку с помощью макета службы `IBrainstormSessionRepository` на платформе макетов объектов, например [Moq](https://www.nuget.org/packages/Moq/). *Макет объекта* представляет собой специально созданный объект с определенным набором свойств и методов поведения, который предназначен для тестирования. Дополнительные сведения см. в разделе [Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests) (Введение в интеграционные тесты).

В методе `HTTP GET Index` нет циклов или ветвления, и он вызывает лишь один метод. Модульный тест для этого действия позволяет выполнить следующие задачи:

* Создание макета службы `IBrainstormSessionRepository` с помощью метода `GetTestSessions`. `GetTestSessions` создает два макета сессий мозгового штурма с датами и именами сеансов;
* Выполнение метода `Index`
* Создание утверждений по возвращаемым методом результатам:
  * возвращается <xref:Microsoft.AspNetCore.Mvc.ViewResult>;
  * [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) имеет тип `StormSessionViewModel`;
  * в `ViewDataDictionary.Model` сохраняются два сеанса мозгового штурма.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Метод `HTTP POST Index` в контроллере Home проверяет следующее:

* Если [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) имеет значение `false` , метод действия возвращает *400 неверного запроса* <xref:Microsoft.AspNetCore.Mvc.ViewResult> с соответствующими данными.
* Если `ModelState.IsValid` имеет значение `true`:
  * вызывается метод `Add` для репозитория;
  * возвращается <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> с правильными аргументами.

Недопустимое состояние модели можно проверить, добавив ошибки с помощью метода <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, как показано в первом тесте ниже.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Если [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) не является допустимым, возвращается тот же результат `ViewResult`, что и для запроса GET. В этом тесте не выполняются попытки передать недопустимую модель. Передача недопустимой модели будет неправильным подходом, так как привязка такой модели не работает (хотя [интеграционный тест](xref:test/integration-tests) и использует привязку модели). В этом случае привязка модели не тестируется. Эти модульные тесты проверяют только код в методе действия.

Второй тест проверяет, выполняются ли при допустимом значении `ModelState` следующие условия:

* добавляется новый `BrainstormSession` (через репозиторий);
* метод возвращает `RedirectToActionResult` с ожидаемыми свойствами.

Макеты вызовов, которые не выполняются, обычно игнорируются, но вызов `Verifiable` в конце вызова Setup позволяет проверить макет в тесте. Для этого выполняется вызов метода `mockRepo.Verify`, который устанавливает состояние непройденного теста, если требуемый метод не был вызван.

> [!NOTE]
> Библиотека Moq, используемая в этом примере, позволяет сочетать проверяемые (строгие) и непроверяемые макеты (которые также называют нестрогими макетами или заглушками). Узнайте больше о [настройке поведения макетов с помощью Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

[SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) в примере приложения выводит сведения, связанные с определенным сеансом мозгового штурма. Этот контроллер содержит логику для работы с недопустимыми значениями `id` (два сценария `return` в следующем примере посвящены этим сценариям). Конечная инструкция `return` возвращает новый `StormSessionViewModel` в представление (*Controllers/SessionController.cs*):

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Модульные тесты содержат по одному тесту для каждого сценария `return` в контроллере Session действия `Index`:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Теперь перейдем к контроллеру Ideas, в котором приложение предоставляет функциональные возможности веб-API для маршрута `api/ideas`:

* Список идей (`IdeaDTO`), полученных в сеансе мозгового штурма, возвращается методом `ForSession`.
* Метод `Create` позволяет добавить в сеанс новые идеи.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Старайтесь не возвращать сущности рабочей предметной области напрямую через вызовы API. Сущности предметной области:

* часто содержат больше данных, чем нужно клиенту;
* неоправданно связывают модели предметной области приложения с общедоступным API-интерфейсом.

Сопоставление между сущностями предметной области и типами, которые возвращаются клиенту, можно выполнять следующими способами:

* Вручную с помощью LINQ `Select`, как в этом примере приложения. Дополнительные сведения см. в статье [Встроенный язык запросов LINQ](/dotnet/standard/using-linq).
* Автоматически через специальную библиотеку, например [AutoMapper](https://github.com/AutoMapper/AutoMapper).

Далее этот пример демонстрирует модульные тесты для методов API `Create` и `ForSession` в контроллере Ideas.

Этот пример приложения содержит два теста `ForSession`. Первый тест определяет, возвращает ли `ForSession` значение <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (ответ HTTP "Не найдено") при недопустимом значении сеанса:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

Второй тест `ForSession` проверяет, возвращает ли `ForSession` список идей сеанса (`<List<IdeaDTO>>`) для допустимого сеанса. Также выполняется анализ первой идеи для проверки правильности свойства `Name`:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Чтобы протестировать поведение метода `Create`, если состояние `ModelState` недопустимо, пример приложения в рамках теста добавляет ошибку модели в контроллер. Не пытайтесь тестировать проверку или привязку модели с помощью модульных тестов &mdash; проверяйте только поведение метода действия при некорректных значениях `ModelState`:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

Для второго теста `Create` нужно, чтобы репозиторий возвращал значение `null`, поэтому здесь настроен макет репозитория, возвращающий значение `null`. Создавать тестовую базу данных (в памяти или где-либо еще) и запрос, возвращающий этот результат, не нужно. Тест можно выполнить в одной инструкции, как показано в примере кода:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Третий тест `Create` (`Create_ReturnsNewlyCreatedIdeaForSession`) позволяет проверить, вызывается ли метод `UpdateAsync` репозитория. С помощью метода `Verifiable` создается обращение к макету репозитория, а затем вызывается метод `Verify` этого макета для подтверждения выполнения проверяемого метода. Проверка того, сохранил ли метод `UpdateAsync` данные, не относится к задачам модульного теста &mdash; это можно сделать с помощью интеграционного теста.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Тестовый ActionResult\<T>

В ASP.NET Core 2,1 или более поздней версии [ \<T> ActionResult](xref:web-api/action-return-types#actionresultt-type) ( <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> ) позволяет возвращать тип, производный от `ActionResult` или возвращающий конкретный тип.

Пример приложения содержит метод, который возвращает `List<IdeaDTO>` для указанного сеанса `id`. Если сеанс `id` не существует, контроллер возвращает <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

В `ApiIdeasControllerTests` включены два теста контроллера `ForSessionActionResult`.

Первый из этих тестов проверяет, что контроллер возвращает `ActionResult`, но не возвращает несуществующий список идей для несуществующего сеанса `id`:

* `ActionResult` имеет тип `ActionResult<List<IdeaDTO>>`;
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> представляет собой <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Второй тест проверяет, что для допустимого сеанса `id` этот метод возвращает следующее:

* `ActionResult` с типом `List<IdeaDTO>`;
* [ActionResult \<T> . Значение](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) является `List<IdeaDTO>` типом.
* первый элемент в списке является допустимой идеей, которая совпадает с первой идеей в макете сеанса (полученной с помощью вызова `GetTestSession`).

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Пример приложения содержит также метод создания нового значения `Idea` для указанного сеанса. Контроллер возвращает следующие результаты:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> для недопустимой модели;
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>, если сеанс не существует;
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>, если в сеанс добавлена новая идея.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

В `ApiIdeasControllerTests` включены три теста `CreateActionResult`.

Первый из этих тестов позволяет проверить, возвращается ли <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> для недопустимой модели.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

Второй тест позволяет проверить, возвращается ли <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>, если сеанс не существует.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Последний тест позволяет проверить, выполняются ли для действительного сеанса `id` следующие условия:

* Метод возвращает `ActionResult` с типом `BrainstormSession`.
* [ActionResult \<T> . Результат](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) — <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> . `CreatedAtActionResult` аналогично ответу *201 — создан ресурс* с заголовком `Location`.
* [ActionResult \<T> . Значение](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) является `BrainstormSession` типом.
* Выполняется вызов макета `UpdateAsync(testSession)` для обновления сеанса. Вызов метода `Verifiable` проверяется выполнением `mockRepo.Verify()` в утверждениях.
* Возвращаются два объекта `Idea` для сеанса.
* Последний элемент (идея `Idea`, добавленная в макет с помощью вызова `UpdateAsync`) совпадает со значением `newIdea`, добавленным в сеанс в этом тесте.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Контроллеры](xref:mvc/controllers/actions) играют важнейшую роль в любом приложении MVC на ASP.NET Core. Это означает, что вы должны быть полностью уверены в правильности их работы. Автоматические тесты позволяют обнаружить ошибки до развертывания приложения в рабочей среде.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/testing/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="unit-tests-of-controller-logic"></a>Модульное тестирование логики контроллера

[Модульное тестирование](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) предполагает тестирование части приложения изолированно от его инфраструктуры и зависимостей. При модульном тестировании логики контроллера тестируется только содержимое отдельного действия, но не поведение его зависимостей или самой платформы.

Настройте модульные тесты для действий контроллера, чтобы сосредоточиться на поведении контроллера. В модульных тестах контроллера не учитываются такие аспекты, как [фильтрация](xref:mvc/controllers/filters), [маршрутизация](xref:fundamentals/routing) и (или) [привязка модели](xref:mvc/models/model-binding). Тесты, учитывающие взаимодействие компонентов, участвующих в ответе на запрос, включены в *интеграционные тесты*. Дополнительные сведения об интеграционных тестах см. в статье <xref:test/integration-tests>.

Если вы создаете пользовательские фильтры и маршруты, проводите для них модульное тестирование изолированно, но не в рамках тестирования определенного действия контроллера.

Для демонстрации модульных тестов контроллера давайте рассмотрим контроллер в приведенном ниже примере приложения. Этот контроллер Home выводит список сеансов мозгового штурма и позволяет создавать новые сеансы с помощью запроса POST.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Предыдущий контроллер:

* Соблюдает [Принцип явных зависимостей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).
* Ожидает, что [внедрение зависимостей (DI)](xref:fundamentals/dependency-injection) предоставит экземпляр `IBrainstormSessionRepository`.
* Допускает проверку с помощью макета службы `IBrainstormSessionRepository` на платформе макетов объектов, например [Moq](https://www.nuget.org/packages/Moq/). *Макет объекта* представляет собой специально созданный объект с определенным набором свойств и методов поведения, который предназначен для тестирования. Дополнительные сведения см. в разделе [Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests) (Введение в интеграционные тесты).

В методе `HTTP GET Index` нет циклов или ветвления, и он вызывает лишь один метод. Модульный тест для этого действия позволяет выполнить следующие задачи:

* Создание макета службы `IBrainstormSessionRepository` с помощью метода `GetTestSessions`. `GetTestSessions` создает два макета сессий мозгового штурма с датами и именами сеансов;
* Выполнение метода `Index`
* Создание утверждений по возвращаемым методом результатам:
  * возвращается <xref:Microsoft.AspNetCore.Mvc.ViewResult>;
  * [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) имеет тип `StormSessionViewModel`;
  * в `ViewDataDictionary.Model` сохраняются два сеанса мозгового штурма.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Метод `HTTP POST Index` в контроллере Home проверяет следующее:

* Если [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) имеет значение `false` , метод действия возвращает *400 неверного запроса* <xref:Microsoft.AspNetCore.Mvc.ViewResult> с соответствующими данными.
* Если `ModelState.IsValid` имеет значение `true`:
  * вызывается метод `Add` для репозитория;
  * возвращается <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> с правильными аргументами.

Недопустимое состояние модели можно проверить, добавив ошибки с помощью метода <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, как показано в первом тесте ниже.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Если [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) не является допустимым, возвращается тот же результат `ViewResult`, что и для запроса GET. В этом тесте не выполняются попытки передать недопустимую модель. Передача недопустимой модели будет неправильным подходом, так как привязка такой модели не работает (хотя [интеграционный тест](xref:test/integration-tests) и использует привязку модели). В этом случае привязка модели не тестируется. Эти модульные тесты проверяют только код в методе действия.

Второй тест проверяет, выполняются ли при допустимом значении `ModelState` следующие условия:

* добавляется новый `BrainstormSession` (через репозиторий);
* метод возвращает `RedirectToActionResult` с ожидаемыми свойствами.

Макеты вызовов, которые не выполняются, обычно игнорируются, но вызов `Verifiable` в конце вызова Setup позволяет проверить макет в тесте. Для этого выполняется вызов метода `mockRepo.Verify`, который устанавливает состояние непройденного теста, если требуемый метод не был вызван.

> [!NOTE]
> Библиотека Moq, используемая в этом примере, позволяет сочетать проверяемые (строгие) и непроверяемые макеты (которые также называют нестрогими макетами или заглушками). Узнайте больше о [настройке поведения макетов с помощью Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

[SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) в примере приложения выводит сведения, связанные с определенным сеансом мозгового штурма. Этот контроллер содержит логику для работы с недопустимыми значениями `id` (два сценария `return` в следующем примере посвящены этим сценариям). Конечная инструкция `return` возвращает новый `StormSessionViewModel` в представление (*Controllers/SessionController.cs*):

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Модульные тесты содержат по одному тесту для каждого сценария `return` в контроллере Session действия `Index`:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Теперь перейдем к контроллеру Ideas, в котором приложение предоставляет функциональные возможности веб-API для маршрута `api/ideas`:

* Список идей (`IdeaDTO`), полученных в сеансе мозгового штурма, возвращается методом `ForSession`.
* Метод `Create` позволяет добавить в сеанс новые идеи.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Старайтесь не возвращать сущности рабочей предметной области напрямую через вызовы API. Сущности предметной области:

* часто содержат больше данных, чем нужно клиенту;
* неоправданно связывают модели предметной области приложения с общедоступным API-интерфейсом.

Сопоставление между сущностями предметной области и типами, которые возвращаются клиенту, можно выполнять следующими способами:

* Вручную с помощью LINQ `Select`, как в этом примере приложения. Дополнительные сведения см. в статье [Встроенный язык запросов LINQ](/dotnet/standard/using-linq).
* Автоматически через специальную библиотеку, например [AutoMapper](https://github.com/AutoMapper/AutoMapper).

Далее этот пример демонстрирует модульные тесты для методов API `Create` и `ForSession` в контроллере Ideas.

Этот пример приложения содержит два теста `ForSession`. Первый тест определяет, возвращает ли `ForSession` значение <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (ответ HTTP "Не найдено") при недопустимом значении сеанса:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

Второй тест `ForSession` проверяет, возвращает ли `ForSession` список идей сеанса (`<List<IdeaDTO>>`) для допустимого сеанса. Также выполняется анализ первой идеи для проверки правильности свойства `Name`:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Чтобы протестировать поведение метода `Create`, если состояние `ModelState` недопустимо, пример приложения в рамках теста добавляет ошибку модели в контроллер. Не пытайтесь тестировать проверку или привязку модели с помощью модульных тестов &mdash; проверяйте только поведение метода действия при некорректных значениях `ModelState`:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

Для второго теста `Create` нужно, чтобы репозиторий возвращал значение `null`, поэтому здесь настроен макет репозитория, возвращающий значение `null`. Создавать тестовую базу данных (в памяти или где-либо еще) и запрос, возвращающий этот результат, не нужно. Тест можно выполнить в одной инструкции, как показано в примере кода:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Третий тест `Create` (`Create_ReturnsNewlyCreatedIdeaForSession`) позволяет проверить, вызывается ли метод `UpdateAsync` репозитория. С помощью метода `Verifiable` создается обращение к макету репозитория, а затем вызывается метод `Verify` этого макета для подтверждения выполнения проверяемого метода. Проверка того, сохранил ли метод `UpdateAsync` данные, не относится к задачам модульного теста &mdash; это можно сделать с помощью интеграционного теста.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Тестовый ActionResult\<T>

В ASP.NET Core 2,1 или более поздней версии [ \<T> ActionResult](xref:web-api/action-return-types#actionresultt-type) ( <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> ) позволяет возвращать тип, производный от `ActionResult` или возвращающий конкретный тип.

Пример приложения содержит метод, который возвращает `List<IdeaDTO>` для указанного сеанса `id`. Если сеанс `id` не существует, контроллер возвращает <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

В `ApiIdeasControllerTests` включены два теста контроллера `ForSessionActionResult`.

Первый из этих тестов проверяет, что контроллер возвращает `ActionResult`, но не возвращает несуществующий список идей для несуществующего сеанса `id`:

* `ActionResult` имеет тип `ActionResult<List<IdeaDTO>>`;
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> представляет собой <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Второй тест проверяет, что для допустимого сеанса `id` этот метод возвращает следующее:

* `ActionResult` с типом `List<IdeaDTO>`;
* [ActionResult \<T> . Значение](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) является `List<IdeaDTO>` типом.
* первый элемент в списке является допустимой идеей, которая совпадает с первой идеей в макете сеанса (полученной с помощью вызова `GetTestSession`).

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Пример приложения содержит также метод создания нового значения `Idea` для указанного сеанса. Контроллер возвращает следующие результаты:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> для недопустимой модели;
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>, если сеанс не существует;
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>, если в сеанс добавлена новая идея.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

В `ApiIdeasControllerTests` включены три теста `CreateActionResult`.

Первый из этих тестов позволяет проверить, возвращается ли <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> для недопустимой модели.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

Второй тест позволяет проверить, возвращается ли <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>, если сеанс не существует.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Последний тест позволяет проверить, выполняются ли для действительного сеанса `id` следующие условия:

* Метод возвращает `ActionResult` с типом `BrainstormSession`.
* [ActionResult \<T> . Результат](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) — <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> . `CreatedAtActionResult` аналогично ответу *201 — создан ресурс* с заголовком `Location`.
* [ActionResult \<T> . Значение](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) является `BrainstormSession` типом.
* Выполняется вызов макета `UpdateAsync(testSession)` для обновления сеанса. Вызов метода `Verifiable` проверяется выполнением `mockRepo.Verify()` в утверждениях.
* Возвращаются два объекта `Idea` для сеанса.
* Последний элемент (идея `Idea`, добавленная в макет с помощью вызова `UpdateAsync`) совпадает со значением `newIdea`, добавленным в сеанс в этом тесте.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:test/integration-tests>
* [Создание и запуск модульных тестов с помощью Visual Studio](/visualstudio/test/unit-test-your-code)
* [Библиотека тестирования митестед. AspNetCore. MVC-Fluent для ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): строго типизированная Библиотека модульного тестирования, предоставляющая интерфейс Fluent для тестирования приложений MVC и веб-API. (*Не поддерживается и не обслуживается Майкрософт.* )
* [JustMockLite](https://github.com/telerik/JustMockLite): платформа макетирования для разработчиков на .NET. (*Не поддерживается и не обслуживается Майкрософт.* )

