---
title: Тестирование компонентов в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как тестировать компоненты в приложениях Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
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
uid: blazor/test
ms.openlocfilehash: 67ebfcd322ae08acf2fddae9bd6101f13fa77e7e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280707"
---
# <a name="test-components-in-aspnet-core-blazor"></a><span data-ttu-id="e18f7-103">Тестирование компонентов в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e18f7-103">Test components in ASP.NET Core Blazor</span></span>

<span data-ttu-id="e18f7-104">Автор: [Эгиль Хансен](https://egilhansen.com/) (Egil Hansen)</span><span class="sxs-lookup"><span data-stu-id="e18f7-104">By: [Egil Hansen](https://egilhansen.com/)</span></span>

<span data-ttu-id="e18f7-105">Тестирование — важный аспект создания стабильного и поддерживаемого программного обеспечения.</span><span class="sxs-lookup"><span data-stu-id="e18f7-105">Testing is an important aspect of building stable and maintainable software.</span></span>

<span data-ttu-id="e18f7-106">Для тестирования компонента Blazor с *тестируемым компонентом* (CUT) выполняются следующие действия.</span><span class="sxs-lookup"><span data-stu-id="e18f7-106">To test a Blazor component, the *Component Under Test* (CUT) is:</span></span>

* <span data-ttu-id="e18f7-107">Преобразование для просмотра с соответствующими входными данными для теста.</span><span class="sxs-lookup"><span data-stu-id="e18f7-107">Rendered with relevant input for the test.</span></span>
* <span data-ttu-id="e18f7-108">В зависимости от типа выполняемого теста может осуществляться взаимодействие с этим компонентом или его изменение.</span><span class="sxs-lookup"><span data-stu-id="e18f7-108">Depending on the type of test performed, possibly subject to interaction or modification.</span></span> <span data-ttu-id="e18f7-109">В частности, могут активироваться обработчики событий, например события `onclick` для кнопки.</span><span class="sxs-lookup"><span data-stu-id="e18f7-109">For example, event handlers can be triggered, such as an `onclick` event for a button.</span></span>
* <span data-ttu-id="e18f7-110">Проверка на наличие ожидаемых значений.</span><span class="sxs-lookup"><span data-stu-id="e18f7-110">Inspected for expected values.</span></span>

## <a name="test-approaches"></a><span data-ttu-id="e18f7-111">Методы тестирования</span><span class="sxs-lookup"><span data-stu-id="e18f7-111">Test approaches</span></span>

<span data-ttu-id="e18f7-112">Два распространенных метода тестирования компонентов Blazor — сквозное тестирование (E2E) и модульное тестирование.</span><span class="sxs-lookup"><span data-stu-id="e18f7-112">Two common approaches for testing Blazor components are end-to-end (E2E) testing and unit testing:</span></span>

* <span data-ttu-id="e18f7-113">**Модульное тестирование**. [Модульные тесты](/dotnet/core/testing/) написаны с помощью библиотеки модульного тестирования, которая обеспечивает:</span><span class="sxs-lookup"><span data-stu-id="e18f7-113">**Unit testing**: [Unit tests](/dotnet/core/testing/) are written with a unit testing library that provides:</span></span>
  * <span data-ttu-id="e18f7-114">преобразование компонента для просмотра;</span><span class="sxs-lookup"><span data-stu-id="e18f7-114">Component rendering.</span></span>
  * <span data-ttu-id="e18f7-115">проверку выходных данных и состояния компонента;</span><span class="sxs-lookup"><span data-stu-id="e18f7-115">Inspection of component output and state.</span></span>
  * <span data-ttu-id="e18f7-116">активацию обработчиков событий и методов жизненного цикла;</span><span class="sxs-lookup"><span data-stu-id="e18f7-116">Triggering of event handlers and life cycle methods.</span></span>
  * <span data-ttu-id="e18f7-117">проверочные утверждения, что поведение компонента правильное.</span><span class="sxs-lookup"><span data-stu-id="e18f7-117">Assertions that component behavior is correct.</span></span>

  <span data-ttu-id="e18f7-118">[bUnit](https://github.com/egil/bUnit) — это пример библиотеки, позволяющей выполнять модульное тестирование компонентовRazor.</span><span class="sxs-lookup"><span data-stu-id="e18f7-118">[bUnit](https://github.com/egil/bUnit) is an example of a library that enables Razor component unit testing.</span></span>

* <span data-ttu-id="e18f7-119">**Тестирование E2E**. Средство выполнения тестов запускает приложение Blazor, содержащее CUT, и автоматизирует экземпляр браузера.</span><span class="sxs-lookup"><span data-stu-id="e18f7-119">**E2E testing**: A test runner runs a Blazor app containing the CUT and automates a browser instance.</span></span> <span data-ttu-id="e18f7-120">Средство тестирования проверяет CUT и взаимодействует с ним через браузер.</span><span class="sxs-lookup"><span data-stu-id="e18f7-120">The testing tool inspects and interacts with the CUT through the browser.</span></span> <span data-ttu-id="e18f7-121">[Selenium](https://github.com/SeleniumHQ/selenium) — это пример платформы тестирования E2E, которую можно использовать с приложениями Blazor.</span><span class="sxs-lookup"><span data-stu-id="e18f7-121">[Selenium](https://github.com/SeleniumHQ/selenium) is an example of an E2E testing framework that can be used with Blazor apps.</span></span>

<span data-ttu-id="e18f7-122">В модульном тестировании участвует только компонент Blazor (Razor/C#).</span><span class="sxs-lookup"><span data-stu-id="e18f7-122">In unit testing, only the Blazor component (Razor/C#) is involved.</span></span> <span data-ttu-id="e18f7-123">Внешние зависимости, такие как службы и JS-взаимодействие, должны быть макетированы.</span><span class="sxs-lookup"><span data-stu-id="e18f7-123">External dependencies, such as services and JS interop, must be mocked.</span></span> <span data-ttu-id="e18f7-124">При тестировании E2E компонент Blazor и вся его вспомогательная инфраструктура являются частью теста, включая CSS, JS и API модели DOM и браузера.</span><span class="sxs-lookup"><span data-stu-id="e18f7-124">In E2E testing, the Blazor component and all of it's auxiliary infrastructure are part of the test, including CSS, JS, and the DOM and browser APIs.</span></span>

<span data-ttu-id="e18f7-125">*Область тестирования* описывает, насколько обширны тесты.</span><span class="sxs-lookup"><span data-stu-id="e18f7-125">*Test scope* describes how extensive the tests are.</span></span> <span data-ttu-id="e18f7-126">Область тестирования обычно влияет на скорость тестов.</span><span class="sxs-lookup"><span data-stu-id="e18f7-126">Test scope typically has an influence on the speed of the tests.</span></span> <span data-ttu-id="e18f7-127">Модульные тесты запускаются на подмножестве подсистем приложения и обычно выполняются в течение миллисекунд.</span><span class="sxs-lookup"><span data-stu-id="e18f7-127">Unit tests run on a subset of the app's subsystems and usually execute in milliseconds.</span></span> <span data-ttu-id="e18f7-128">Тесты E2E, которые проверяют обширную группу подсистем приложения, могут выполняться несколько секунд.</span><span class="sxs-lookup"><span data-stu-id="e18f7-128">E2E tests, which test a broad group of the app's subsystems, can take several seconds to complete.</span></span> 

<span data-ttu-id="e18f7-129">Модульное тестирование также предоставляет доступ к экземпляру CUT, позволяя просматривать и проверять внутреннее состояние компонента.</span><span class="sxs-lookup"><span data-stu-id="e18f7-129">Unit testing also provides access to the instance of the CUT, allowing for inspection and verification of the component's internal state.</span></span> <span data-ttu-id="e18f7-130">При тестировании E2E обычно это невозможно.</span><span class="sxs-lookup"><span data-stu-id="e18f7-130">This normally isn't possible in E2E testing.</span></span>

<span data-ttu-id="e18f7-131">В зависимости от среды компонента тесты E2E должны проверять, что ожидаемое состояние среды достигнуто до начала проверки.</span><span class="sxs-lookup"><span data-stu-id="e18f7-131">With regard to the component's environment, E2E tests must make sure that the expected environmental state has been reached before verification starts.</span></span> <span data-ttu-id="e18f7-132">В противном случае результат будет непредсказуемым.</span><span class="sxs-lookup"><span data-stu-id="e18f7-132">Otherwise, the result is unpredictable.</span></span> <span data-ttu-id="e18f7-133">В модульном тестировании преобразование CUT для просмотра и жизненный цикл теста интегрированы в большей степени, что улучшает стабильность тестов.</span><span class="sxs-lookup"><span data-stu-id="e18f7-133">In unit testing, the rendering of the CUT and the life cycle of the test are more integrated, which improves test stability.</span></span>

<span data-ttu-id="e18f7-134">Тестирование E2E включает в себя запуск нескольких процессов, сетевые и дисковые операции ввода-вывода и другие действия подсистем, которые часто приводят к плохой надежности тестов.</span><span class="sxs-lookup"><span data-stu-id="e18f7-134">E2E testing involves launching multiple processes, network and disk I/O, and other subsystem activity that often lead to poor test reliability.</span></span> <span data-ttu-id="e18f7-135">Модульные тесты обычно не зависят от подобных проблем.</span><span class="sxs-lookup"><span data-stu-id="e18f7-135">Unit tests are typically insulated from these sorts of issues.</span></span>

<span data-ttu-id="e18f7-136">В следующей таблице приведены различия между этими двумя методами тестирования.</span><span class="sxs-lookup"><span data-stu-id="e18f7-136">The following table summarizes the difference between the two testing approaches.</span></span>

| <span data-ttu-id="e18f7-137">Функция</span><span class="sxs-lookup"><span data-stu-id="e18f7-137">Capability</span></span>                       | <span data-ttu-id="e18f7-138">Модульное тестирование</span><span class="sxs-lookup"><span data-stu-id="e18f7-138">Unit testing</span></span>                     | <span data-ttu-id="e18f7-139">Тестирование E2E</span><span class="sxs-lookup"><span data-stu-id="e18f7-139">E2E testing</span></span>                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| <span data-ttu-id="e18f7-140">Область тестирования</span><span class="sxs-lookup"><span data-stu-id="e18f7-140">Test scope</span></span>                       | <span data-ttu-id="e18f7-141">Только компонент Blazor (Razor/C#)</span><span class="sxs-lookup"><span data-stu-id="e18f7-141">Blazor component (Razor/C#) only</span></span> | <span data-ttu-id="e18f7-142">Компонент Blazor (Razor/C#) с CSS/JS</span><span class="sxs-lookup"><span data-stu-id="e18f7-142">Blazor component (Razor/C#) with CSS/JS</span></span> |
| <span data-ttu-id="e18f7-143">Время выполнения теста</span><span class="sxs-lookup"><span data-stu-id="e18f7-143">Test execution time</span></span>              | <span data-ttu-id="e18f7-144">Миллисекунды</span><span class="sxs-lookup"><span data-stu-id="e18f7-144">Milliseconds</span></span>                     | <span data-ttu-id="e18f7-145">Секунды</span><span class="sxs-lookup"><span data-stu-id="e18f7-145">Seconds</span></span>                                 |
| <span data-ttu-id="e18f7-146">Доступ к экземпляру компонента</span><span class="sxs-lookup"><span data-stu-id="e18f7-146">Access to the component instance</span></span> | <span data-ttu-id="e18f7-147">Да</span><span class="sxs-lookup"><span data-stu-id="e18f7-147">Yes</span></span>                              | <span data-ttu-id="e18f7-148">Нет</span><span class="sxs-lookup"><span data-stu-id="e18f7-148">No</span></span>                                      |
| <span data-ttu-id="e18f7-149">Чувствительность к среде</span><span class="sxs-lookup"><span data-stu-id="e18f7-149">Sensitive to the environment</span></span>     | <span data-ttu-id="e18f7-150">Нет</span><span class="sxs-lookup"><span data-stu-id="e18f7-150">No</span></span>                               | <span data-ttu-id="e18f7-151">Да</span><span class="sxs-lookup"><span data-stu-id="e18f7-151">Yes</span></span>                                     |
| <span data-ttu-id="e18f7-152">Надежность</span><span class="sxs-lookup"><span data-stu-id="e18f7-152">Reliability</span></span>                      | <span data-ttu-id="e18f7-153">Более надежное</span><span class="sxs-lookup"><span data-stu-id="e18f7-153">More reliable</span></span>                    | <span data-ttu-id="e18f7-154">Менее надежное</span><span class="sxs-lookup"><span data-stu-id="e18f7-154">Less reliable</span></span>                           |

## <a name="choose-the-most-appropriate-test-approach"></a><span data-ttu-id="e18f7-155">Выбор наиболее подходящего метода тестирования</span><span class="sxs-lookup"><span data-stu-id="e18f7-155">Choose the most appropriate test approach</span></span>

<span data-ttu-id="e18f7-156">Рассмотрим сценарий, когда требуется выбрать тип выполняемого тестирования.</span><span class="sxs-lookup"><span data-stu-id="e18f7-156">Consider the scenario when choosing the type of testing to perform.</span></span> <span data-ttu-id="e18f7-157">Некоторые аспекты описаны в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="e18f7-157">Some considerations are described in the following table.</span></span>

| <span data-ttu-id="e18f7-158">Сценарий</span><span class="sxs-lookup"><span data-stu-id="e18f7-158">Scenario</span></span> | <span data-ttu-id="e18f7-159">Рекомендованный метод</span><span class="sxs-lookup"><span data-stu-id="e18f7-159">Suggested approach</span></span> | <span data-ttu-id="e18f7-160">Комментарии</span><span class="sxs-lookup"><span data-stu-id="e18f7-160">Remarks</span></span> |
| -------- | ------------------ | ------- |
| <span data-ttu-id="e18f7-161">Компонент без логики JS-взаимодействия</span><span class="sxs-lookup"><span data-stu-id="e18f7-161">Component without JS interop logic</span></span> | <span data-ttu-id="e18f7-162">Модульное тестирование</span><span class="sxs-lookup"><span data-stu-id="e18f7-162">Unit testing</span></span> | <span data-ttu-id="e18f7-163">Если в компоненте Blazor отсутствует зависимость от взаимодействия с JS, его можно тестировать без доступа к JS или API модели DOM.</span><span class="sxs-lookup"><span data-stu-id="e18f7-163">When there's no dependency on JS interop in a Blazor component, the component can be tested without access to JS or the DOM API.</span></span> <span data-ttu-id="e18f7-164">В этом сценарии выбор модульного тестирования не имеет никаких недостатков.</span><span class="sxs-lookup"><span data-stu-id="e18f7-164">In this scenario, there are no disadvantages to choosing unit testing.</span></span> |
| <span data-ttu-id="e18f7-165">Компонент с простой логикой JS-взаимодействия</span><span class="sxs-lookup"><span data-stu-id="e18f7-165">Component with simple JS interop logic</span></span> | <span data-ttu-id="e18f7-166">Модульное тестирование</span><span class="sxs-lookup"><span data-stu-id="e18f7-166">Unit testing</span></span> | <span data-ttu-id="e18f7-167">Чаще всего компоненты запрашивают модель DOM или активируют анимацию с помощью JS-взаимодействия.</span><span class="sxs-lookup"><span data-stu-id="e18f7-167">It's common for components to query the DOM or trigger animations through JS interop.</span></span> <span data-ttu-id="e18f7-168">Как правило, в этом сценарии предпочтительным является модульное тестирование, так как довольно просто макетировать JS-взаимодействие через интерфейс <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="e18f7-168">Unit testing is usually preferred in this scenario, since it's straightforward to mock the JS interaction through the <xref:Microsoft.JSInterop.IJSRuntime> interface.</span></span> |
| <span data-ttu-id="e18f7-169">Компонент, зависящий от сложного кода JS</span><span class="sxs-lookup"><span data-stu-id="e18f7-169">Component that depends on complex JS code</span></span> | <span data-ttu-id="e18f7-170">Модульное тестирование и отдельное тестирование JS</span><span class="sxs-lookup"><span data-stu-id="e18f7-170">Unit testing and separate JS testing</span></span> | <span data-ttu-id="e18f7-171">Если компонент использует JS-взаимодействие для вызова большой или сложной библиотеки JS, но взаимодействие между компонентом Blazor и библиотекой JS является простым, то, вероятно, лучше всего будет рассматривать компонент и библиотеку JS или код как две отдельные части и тестировать каждую часть индивидуально.</span><span class="sxs-lookup"><span data-stu-id="e18f7-171">If a component uses JS interop to call a large or complex JS library but the interaction between the Blazor component and JS library is simple, then the best approach is likely to treat the component and JS library or code as two separate parts and test each individually.</span></span> <span data-ttu-id="e18f7-172">Протестируйте компонент Blazor с библиотекой модульного тестирования, а библиотеку JS — с библиотекой тестирования JS.</span><span class="sxs-lookup"><span data-stu-id="e18f7-172">Test the Blazor component with a unit testing library, and test the JS with a JS testing library.</span></span> |
| <span data-ttu-id="e18f7-173">Компонент с логикой, зависящей от действий JS с моделью DOM браузера</span><span class="sxs-lookup"><span data-stu-id="e18f7-173">Component with logic that depends on JS manipulation of the browser DOM</span></span> | <span data-ttu-id="e18f7-174">Тестирование E2E</span><span class="sxs-lookup"><span data-stu-id="e18f7-174">E2E testing</span></span> | <span data-ttu-id="e18f7-175">Если функциональность компонента зависит от JS и его действий с моделью DOM, проверяйте код Blazor и JS вместе в тесте E2E.</span><span class="sxs-lookup"><span data-stu-id="e18f7-175">When a component's functionality is dependent on JS and its manipulation of the DOM, verify both the JS and Blazor code together in an E2E test.</span></span> <span data-ttu-id="e18f7-176">Этот подход разработчики платформы Blazor применили к логике отрисовки в браузере Blazor, которая имеет тесно связанные код C# и JS.</span><span class="sxs-lookup"><span data-stu-id="e18f7-176">This is the approach that the Blazor framework developers have taken with Blazor's browser rendering logic, which has tightly-coupled C# and JS code.</span></span> <span data-ttu-id="e18f7-177">Код C# и JS должны работать совместно для правильного преобразования компонентов Blazor для просмотра в браузере.</span><span class="sxs-lookup"><span data-stu-id="e18f7-177">The C# and JS code must work together to correctly render Blazor components in a browser.</span></span>
| <span data-ttu-id="e18f7-178">Компонент, который зависит от сторонней библиотеки компонентов стороннего производителя со сложными для макетирования зависимостями</span><span class="sxs-lookup"><span data-stu-id="e18f7-178">Component that depends on 3rd party component library with hard-to-mock dependencies</span></span> | <span data-ttu-id="e18f7-179">Тестирование E2E</span><span class="sxs-lookup"><span data-stu-id="e18f7-179">E2E testing</span></span> | <span data-ttu-id="e18f7-180">Если функциональные возможности компонента зависят от сторонней библиотеки компонентов со сложными для макетирования зависимостями, такими как JS-взаимодействие, тестирование E2E может быть единственным вариантом для тестирования компонента.</span><span class="sxs-lookup"><span data-stu-id="e18f7-180">When a component's functionality is dependent on a 3rd party component library that has hard-to-mock dependencies, such as JS interop, E2E testing might be the only option to test the component.</span></span> |

## <a name="test-components-with-bunit"></a><span data-ttu-id="e18f7-181">Тестирование компонентов с помощью bUnit</span><span class="sxs-lookup"><span data-stu-id="e18f7-181">Test components with bUnit</span></span>

<span data-ttu-id="e18f7-182">Для Blazor отсутствует официальная платформа тестирования Майкрософт, но поддерживаемый сообществом проект [bUnit](https://github.com/egil/bUnit) предоставляет удобный способ модульного тестирования компонентов Blazor.</span><span class="sxs-lookup"><span data-stu-id="e18f7-182">There's no official Microsoft testing framework for Blazor, but the community-driven project [bUnit](https://github.com/egil/bUnit) provides a convenient way to unit test Blazor components.</span></span>

> [!NOTE]
> <span data-ttu-id="e18f7-183">bUnit — это сторонняя библиотека тестирования, которая не поддерживается или не обслуживается корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="e18f7-183">bUnit is a third-party testing library and isn't supported or maintained by Microsoft.</span></span>

<span data-ttu-id="e18f7-184">bUnit работает с платформами тестирования общего назначения, такими как [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/) и [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="e18f7-184">bUnit works with general-purpose testing frameworks, such as [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/), and [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="e18f7-185">На этих платформах тестирования тесты bUnit выглядят так же, как обычные модульные тесты.</span><span class="sxs-lookup"><span data-stu-id="e18f7-185">These testing frameworks make bUnit tests look and feel like regular unit tests.</span></span> <span data-ttu-id="e18f7-186">Тесты bUnit, интегрированные с платформой тестирования общего назначения, обычно выполняются с помощью:</span><span class="sxs-lookup"><span data-stu-id="e18f7-186">bUnit tests integrated with a general-purpose testing framework are ordinarily executed with:</span></span>

* <span data-ttu-id="e18f7-187">[обозревателя тестов Visual Studio](/visualstudio/test/run-unit-tests-with-test-explorer);</span><span class="sxs-lookup"><span data-stu-id="e18f7-187">[Visual Studio's Test Explorer](/visualstudio/test/run-unit-tests-with-test-explorer).</span></span>
* <span data-ttu-id="e18f7-188">команды [`dotnet test`](/dotnet/core/tools/dotnet-test) CLI в командной оболочке;</span><span class="sxs-lookup"><span data-stu-id="e18f7-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) CLI command in a command shell.</span></span>
* <span data-ttu-id="e18f7-189">автоматизированного конвейера тестирования DevOps.</span><span class="sxs-lookup"><span data-stu-id="e18f7-189">An automated DevOps testing pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="e18f7-190">Концепции тестирования и реализации тестов в разных платформах тестирования похожи, но не идентичны.</span><span class="sxs-lookup"><span data-stu-id="e18f7-190">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span> <span data-ttu-id="e18f7-191">Инструкции см. в документации платформы тестирования.</span><span class="sxs-lookup"><span data-stu-id="e18f7-191">Refer to the test framework's documentation for guidance.</span></span>

<span data-ttu-id="e18f7-192">Ниже показана структура теста bUnit для компонента `Counter` в приложении на основе шаблона проекта Blazor.</span><span class="sxs-lookup"><span data-stu-id="e18f7-192">The following demonstrates the structure of a bUnit test on the `Counter` component in an app based on a Blazor project template.</span></span> <span data-ttu-id="e18f7-193">Компонент `Counter` отображает счетчик и увеличивает его значение при нажатии пользователем кнопки на странице.</span><span class="sxs-lookup"><span data-stu-id="e18f7-193">The `Counter` component displays and increments a counter based on the user selecting a button in the page:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="e18f7-194">Следующий тест bUnit проверяет правильность увеличения счетчика CUT при нажатии кнопки.</span><span class="sxs-lookup"><span data-stu-id="e18f7-194">The following bUnit test verifies that the CUT's counter is incremented correctly when the button is selected:</span></span>

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

<span data-ttu-id="e18f7-195">На каждом шаге теста выполняются следующие действия.</span><span class="sxs-lookup"><span data-stu-id="e18f7-195">The following actions take place at each step of the test:</span></span>

* <span data-ttu-id="e18f7-196">*Arrange*. Компонент `Counter` преобразуется для просмотра с помощью `TestContext` теста bUnit.</span><span class="sxs-lookup"><span data-stu-id="e18f7-196">*Arrange*: The `Counter` component is rendered using bUnit's `TestContext`.</span></span> <span data-ttu-id="e18f7-197">Элемент абзаца (`<p>`) CUT обнаруживается и назначается `paraElm`.</span><span class="sxs-lookup"><span data-stu-id="e18f7-197">The CUT's paragraph element (`<p>`) is found and assigned to `paraElm`.</span></span>

* <span data-ttu-id="e18f7-198">*Act*. Элемент кнопки (`<button>`) обнаруживается и выбирается путем вызова `Click`, который должен увеличить значение счетчика и обновить содержимое тега абзаца (`<p>`).</span><span class="sxs-lookup"><span data-stu-id="e18f7-198">*Act*: The button's element (`<button>`) is located and then selected by calling `Click`, which should increment the counter and update the content of the paragraph tag (`<p>`).</span></span> <span data-ttu-id="e18f7-199">Текстовое содержимое элемента абзаца извлекается путем вызова `TextContent`.</span><span class="sxs-lookup"><span data-stu-id="e18f7-199">The paragraph element text content is obtained by calling `TextContent`.</span></span>

* <span data-ttu-id="e18f7-200">*Assert*. Для текстового содержимого вызывается метод `MarkupMatches`, который проверяет его соответствие ожидаемой строке `Current count: 1`.</span><span class="sxs-lookup"><span data-stu-id="e18f7-200">*Assert*: `MarkupMatches` is called on the text content to verify that it matches the expected string, which is `Current count: 1`.</span></span>

> [!NOTE]
> <span data-ttu-id="e18f7-201">Метод утверждения `MarkupMatches` отличается от обычного утверждения сравнения строк (например, `Assert.Equal("Current count: 1", paraElmText);`). `MarkupMatches` выполняет семантическое сравнение введенного и ожидаемого исправления HTML.</span><span class="sxs-lookup"><span data-stu-id="e18f7-201">The `MarkupMatches` assert method differs from a regular string comparison assertion (for example, `Assert.Equal("Current count: 1", paraElmText);`) `MarkupMatches` performs a semantic comparison of the input and expected HTML markup.</span></span> <span data-ttu-id="e18f7-202">Семантическое сравнение учитывает семантику HTML. Это означает, что такие детали, как незначащие пробелы, игнорируются.</span><span class="sxs-lookup"><span data-stu-id="e18f7-202">A semantic comparison is aware of HTML semantics, meaning things like insignificant whitespace is ignored.</span></span> <span data-ttu-id="e18f7-203">Это приводит к повышению стабильности тестов.</span><span class="sxs-lookup"><span data-stu-id="e18f7-203">This results in more stable tests.</span></span> <span data-ttu-id="e18f7-204">Дополнительные сведения см. в разделе [Настройка семантического сравнения HTML](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span><span class="sxs-lookup"><span data-stu-id="e18f7-204">For more information, see [Customizing the Semantic HTML Comparison](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e18f7-205">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e18f7-205">Additional resources</span></span>

* <span data-ttu-id="e18f7-206">[Начало работы с bUnit](https://bunit.egilhansen.com/docs/getting-started/). Инструкции по bUnit включают руководство по созданию тестового проекта, ссылки на пакеты платформ тестирования, а также инструкции по созданию и выполнению тестов.</span><span class="sxs-lookup"><span data-stu-id="e18f7-206">[Getting Started with bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit instructions include guidance on creating a test project, referencing testing framework packages, and building and running tests.</span></span>
