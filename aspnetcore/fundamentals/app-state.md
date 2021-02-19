---
title: Сеанс в ASP.NET Core
author: rick-anderson
description: Методы обнаружения для сохранения сеанса между запросами.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
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
uid: fundamentals/app-state
ms.openlocfilehash: c11b748f9d79235b14c9541019da6e1fb3428af6
ms.sourcegitcommit: c1839f2992b003c92cd958244a2e0771ae928786
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2021
ms.locfileid: "93051412"
---
# <a name="session-and-state-management-in-aspnet-core"></a>Управление сеансами и состояниями в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin) и [Диана Лароуз](https://github.com/DianaLaRose) (Diana LaRose)

HTTP — это протокол без отслеживания состояния. По умолчанию HTTP-запросы являются независимыми сообщениями, которые не сохраняют значения пользователя. В этой статье описывается несколько подходов для сохранения данных пользователя между запросами.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Управление состоянием

Состояние можно сохранить несколькими способами. Эти способы обсуждается ниже в данном разделе.

| Способ хранения данных | Механизм хранения |
| ---------------- | ----------------- |
| [Файлы Cookie](#cookies) | Файлы cookie HTTP. Могут содержать данные, сохраненные с помощью кода приложения на стороне сервера. |
| [Состояние сеанса](#session-state) | Файлы cookie HTTP и код приложения на стороне сервера |
| [TempData](#tempdata) | Файлы cookie HTTP или состояние сеанса |
| [Строки запросов](#query-strings) | Строки запросов HTTP |
| [Скрытые поля](#hidden-fields) | Поля формы HTTP |
| [HttpContext.Items](#httpcontextitems) | Код приложения на стороне сервера |
| [Кэш](#cache) | Код приложения на стороне сервера |

## <a name="cookies"></a>Cookies

Файлы Cookie хранят данные между запросами. Так как файлы cookie отправляются с каждым запросом, их размер нужно свести к минимуму. В идеальном случае файл cookie должен содержать лишь идентификатор, а сами данные хранятся в приложении. Большинство браузеров позволяют использовать файлы cookie размером до 4096 байт. Для каждого домена доступно лишь ограниченное число файлов cookie.

Так как файлы cookie могут быть незаконно изменены, их нужно проверять в приложении. Файлы Cookie могут удаляться пользователем и имеют ограниченный срок хранения на клиентах. Тем не менее файлы cookie обычно являются самым надежным способом хранения данных на стороне клиента.

Файлы Cookie часто используются для персонализации, когда содержимое настраивается под известного пользователя. В большинстве случаев пользователь проходит только идентификацию, а не проверку подлинности. Файл cookie может хранить имя пользователя, имя учетной записи или уникальный идентификатор пользователя, например GUID. Можно использовать файл cookie для доступа к личным настройкам пользователя, таким как цвет фона на веб-сайте.

См. [Общий регламент по защите данных в ЕС (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) при создании файлов cookie и решении вопросов с конфиденциальностью. Дополнительные сведения см. в разделе [Общий регламент по защите данных (GDPR), принятый в ЕС, в ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Состояние сеанса

Состояние сеанса — это сценарий ASP.NET Core для хранения пользовательских данных, когда пользователь находится в веб-приложении. Состояние сеанса использует хранилище, обслуживаемое приложением, для сохранения данных между запросами от клиента. Данные сеанса поддерживаются кэшем и считаются временными. Сайт должен продолжать работать без данных сеанса. Критически важные данные приложения должны храниться в пользовательской базе данных и кэшироваться в сеансе только в качестве оптимизации производительности.

Сеанс не поддерживается в приложениях [SignalR](xref:signalr/index), так как [концентратор SignalR](xref:signalr/hubs) может работать независимо от контекста HTTP. Например, это может произойти, если хаб поддерживает длительный запрос на опрос открытым, когда время существования контекста HTTP для запроса уже истекло.

ASP.NET Core сохраняет состояние сеанса, предоставляя клиенту файл cookie, содержащий идентификатор сеанса. Идентификатор файла cookie сеанса.

* Отправляется в приложение с каждым запросом.
* Используется приложением для получения данных сеанса.

Состояние сеанса реагирует на события следующим образом:

* Файл cookie сеанса относится к браузеру. Сеансы не используются в разных браузерах.
* Файлы cookie сеанса удаляются при завершении сеанса браузера.
* Если получен файл cookie для просроченного сеанса, создается сеанс, использующий этот же файл cookie.
* Пустые сеансы не сохраняются. В сеансе должно быть хотя бы одно значение, чтобы его можно быть сохранить между запросами. Если сеанс не сохраняется, для каждого нового запроса создается новый идентификатор сеанса.
* Приложение хранит сеанс некоторое время после последнего запроса. Приложение устанавливает время ожидания сеанса или использует значение по умолчанию, равное 20 минутам. Состояние сеанса идеально подходит для хранения пользовательских данных:
  * Если они относятся к конкретному сеансу.
  * Если данные не нуждаются в постоянном хранении между сеансами.
* Данные сеанса удаляются при вызове реализации <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> или когда истекает срок действия сеанса.
* Не существует механизма по умолчанию, который бы информировал код приложения о том, что браузер клиента закрыт или файл cookie сеанса удален или просрочен на клиенте.
* Файлы cookie состояния сеанса по умолчанию не отмечены как основные. Состояние сеанса не работает, если отслеживание не разрешено посетителем сайта. Для получения дополнительной информации см. <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Не храните конфиденциальные данные в состоянии сеанса. Пользователь может не закрывать браузер и очистить файл cookie сеанса. Некоторые браузеры сохраняют файлы cookie сеанса в нескольких окнах браузера. Сеанс может быть не ограничен одним пользователем. Следующий пользователь продолжит использовать приложение с тем же файлом cookie сеанса.

Поставщик кэша в памяти хранит данные сеанса в памяти сервера, содержащего приложение. В сценарии с фермой серверов:

* Используйте *прикрепленные сеансы* для привязки сеанса к конкретному экземпляру приложения на отдельном сервере. [Служба приложений Azure](https://azure.microsoft.com/services/app-service/) использует [маршрутизацию запросов приложений (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module), чтобы прикрепленные сеансы создавались по умолчанию. Однако прикрепленные сеансы могут негативно повлиять на масштабируемость и усложнить обновление веб-приложений. Лучше использовать распределенные кэши SQL Server или Redis, для которых прикрепленные сеансы не требуются. Для получения дополнительной информации см. <xref:performance/caching/distributed>.
* Файл cookie сеанса шифруется с помощью <xref:Microsoft.AspNetCore.DataProtection.IDataProtector>. Необходимо правильно настроить защиту данных, чтобы читать файлы cookie сеанса на каждом компьютере. Дополнительные сведения см. статьях <xref:security/data-protection/introduction> и [Key storage providers in ASP.NET Core](xref:security/data-protection/implementation/key-storage-providers) (Поставщики хранилища ключей в ASP.NET Core).

### <a name="configure-session-state"></a>Настройка состояния сеанса

Пакет [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/):

* Неявно включается платформой.
* Предоставляет ПО промежуточного слоя для управления состоянием сеанса.

Чтобы включить сеанс ПО промежуточного слоя для сеансов, `Startup` должен содержать:

* Любой из кэшей памяти <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, при этом реализация `IDistributedCache` используется в качестве резервного хранилища для сеанса. Для получения дополнительной информации см. <xref:performance/caching/distributed>.
* Вызов к <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> в `ConfigureServices`.
* Вызов к <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> в `Configure`.

Следующий код показывает, как настроить поставщик сеансов в памяти с реализацией в памяти `IDistributedCache` по умолчанию:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,45)]

Приведенный выше код задает короткий тайм-аут для упрощения тестирования.

Порядок ПО промежуточного слоя важен.  `UseSession` вызывается после `UseRouting`, но перед `UseEndpoints`. См. [Порядок ПО промежуточного слоя](xref:fundamentals/middleware/index#order).

После настройки состояния сеанса доступно свойство [HttpContext.Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session).

Невозможно получить доступ к `HttpContext.Session` до вызова `UseSession`.

Невозможно создать новый сеанс с новым файлом cookie сеанса после того, как приложение начинает запись в поток ответа. Исключение записывается в журнал веб-сервера и не отображается в браузере.

### <a name="load-session-state-asynchronously"></a>Асинхронная загрузка состояния сеанса

Поставщик сеансов по умолчанию в ASP.NET Core загружает записи сеанса из базового резервного хранилища <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> в асинхронном режиме только при явном вызове метода <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> перед методами <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A>, <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> или <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A>. Если `LoadAsync` не вызывается первым, базовая запись сеанса загружается синхронно, что может негативно повлиять на производительность.

Чтобы принудительно использовать этот режим в приложениях, включите реализации <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> и <xref:Microsoft.AspNetCore.Session.DistributedSession> в версии, которые выдают исключение, когда метод `LoadAsync` не вызывается перед `TryGetValue`, `Set` или `Remove`. Зарегистрируйте версии оболочки в контейнере служб.

### <a name="session-options"></a>Параметры сеанса

Чтобы переопределить значения по умолчанию для сеанса, используйте <xref:Microsoft.AspNetCore.Builder.SessionOptions>.

| Параметр | Описание |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Определяет параметры, используемые для создания файлов cookie. Для <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> задается значение по умолчанию <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> (`.AspNetCore.Session`). Для <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> задается значение по умолчанию <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> (`/`). Для <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> задается значение по умолчанию <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> (`1`). Для <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> задается значение по умолчанию `true`. Для <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> задается значение по умолчанию `false`. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout` указывает, как долго сеанс может быть неактивным, прежде чем его содержимое отбрасывается. Каждый доступ к сеансу сбрасывает время ожидания. Этот параметр применяется только к содержимому сеанса, а не к файлу cookie. Значение по умолчанию — 20 минут. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | Максимальный период загрузки сеанса из хранилища или его сохранения обратно в хранилище. Этот параметр может применяться только к асинхронным операциям. Время ожидания можно отключить с помощью <xref:System.Threading.Timeout.InfiniteTimeSpan>. Значение по умолчанию — 1 минута. |

Сеанс использует файл cookie для отслеживания и идентификации запросов в одном браузере. По умолчанию этот файл cookie называется `.AspNetCore.Session` и использует путь `/`. Так как по умолчанию файл cookie не определяет домен, он остается недоступным для клиентского скрипта на странице (так как для <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> по умолчанию задается значение `true`).

Чтобы переопределить значения по умолчанию для файла cookie сеанса, используйте <xref:Microsoft.AspNetCore.Builder.SessionOptions>.

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

Приложение использует свойство <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout>, чтобы определить, как долго сеанс может оставаться неактивным до сброса его содержимого в кэше сервера. Это свойство не зависит от срока действия файла cookie. Каждый запрос, проходящий через [ПО промежуточного слоя сеанса](xref:Microsoft.AspNetCore.Session.SessionMiddleware), сбрасывает это время ожидания.

Состояние сеанса является *неблокирующим*. Когда два запроса пытаются изменить содержимое сеанса, последний из них переопределяет первый. `Session` реализован в виде *согласованного сеанса*, что означает, что все содержимое хранится вместе. Когда два запроса пытаются изменить разные значения сеанса, последний запрос может переопределить изменения, внесенные первым.

### <a name="set-and-get-session-values"></a>Установка и получение значений сеанса

Доступ к состоянию сеанса осуществляется из класса Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> или класса MVC <xref:Microsoft.AspNetCore.Mvc.Controller> с <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType>. Это свойство является реализацией <xref:Microsoft.AspNetCore.Http.ISession>.

Реализация `ISession` предоставляет несколько методов расширения для задания и извлечения значений типа integer и string. Методы расширения относятся к пространству имен <xref:Microsoft.AspNetCore.Http>.

Методы расширения `ISession`:

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

В следующем примере извлекается значение сеанса для ключа `IndexModel.SessionKeyName` (`_Name` в примере приложения) на странице Razor Pages.

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

В следующем примере показано, как задать, а затем получить значения integer и string:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Все данные сеанса должны быть сериализованы, чтобы использовать сценарий-распределенного кэша даже при использовании кэша в памяти. Сериализаторы строк и целых чисел предоставляются методами расширения <xref:Microsoft.AspNetCore.Http.ISession>. Сложные типы должны быть сериализованы пользователем с помощью другого механизма, например JSON.

Используйте следующий пример кода для сериализации объектов:

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Следующий пример показывает, как задать и получить сериализуемый объект с помощью класса `SessionExtensions`:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core предоставляет [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) Razor Pages или <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> контроллера. Это свойство хранит данные до тех пор, пока они не будут прочитаны в другом запросе. Для проверки данных без удаления можно использовать методы [Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) и [Peek(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) в конце запроса. [Keep](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) помечает все элементы в словаре для хранения. `TempData` является:

* удобным в использовании для перенаправления, когда данные требуются больше, чем для одного запроса.
* реализуемым поставщиками `TempData`, например с помощью файлов cookie или состояния сеанса.

## <a name="tempdata-samples"></a>Примеры TempData

Рассмотрим следующую страницу, которая создает клиент:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

На следующей странице отображается `TempData["Message"]`:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

В предыдущей разметке в конце запроса `TempData["Message"]`**не** удаляется, так как используется `Peek`. На обновляемой странице отображается содержимое `TempData["Message"]`.

Следующая разметка похожа на приведенный выше код, но в ней используется `Keep` для сохранения данных в конце запроса:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

При переходе между страницами *IndexPeek* и *IndexKeep*`TempData["Message"]` не удаляется.

Следующий код отображает `TempData["Message"]`, но в конце запроса `TempData["Message"]` удаляется:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Поставщики TempData

Поставщик TempData, основанный на файлах cookie, используется по умолчанию для хранения TempData в файлах cookie.

Данные файлов cookie шифруются с помощью <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> с кодировкой с использованием <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> и последующей фрагментацией. Максимальный размер файла cookie меньше [4096 байт](http://www.faqs.org/rfcs/rfc2965.html) из-за шифрования и фрагментирования. Данные файла cookie не сжимаются, так как сжатие зашифрованных данных может привести к проблемам с безопасностью, например атакам [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) и [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)). Дополнительные сведения о поставщике TempData, основанном на файлах cookie, см. здесь: <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>.

### <a name="choose-a-tempdata-provider"></a>Выбор поставщика TempData

Выбор поставщика TempData включает в себя несколько аспектов:

* Приложение уже использует состояние сеанса? Если это так, использование поставщика TempData для состояния сеанса не требует от приложения никаких дополнительных издержек, кроме объема данных.
* Приложение использует TempData лишь ограниченно, для сравнительно небольших объемов данных до 500 байт? Если это так, поставщик TempData на основе файлов cookie незначительно увеличивает издержки для каждого запроса, переносящего TempData. В противном случае поставщик TempData для состояния сеанса удобно использовать, чтобы устранить круговой обход большого объема данных в каждом запросе до полного использования TempData.
* Приложение выполняется на ферме серверов на нескольких серверах? Если это так, не требуется дополнительная настройка для использования поставщика TempData для файлов cookie, за исключением защиты данных (см. статьи <xref:security/data-protection/introduction> и [Поставщики услуг хранилищ ключей](xref:security/data-protection/implementation/key-storage-providers)).

Большинство веб-клиентов (например, веб-браузеры) налагают ограничение на максимальный размер каждого файла cookie и общее количество таких файлов cookie. При использовании поставщика TempData на основе файлов cookie убедитесь, что приложение не нарушает [эти ограничения](http://www.faqs.org/rfcs/rfc2965.html). Учитывайте общий объем данных. Учитывайте увеличение размеров файла cookie в результате шифрования и фрагментирования.

### <a name="configure-the-tempdata-provider"></a>Настройка поставщика TempData

Поставщик TempData на основе файлов cookie включен по умолчанию.

Чтобы включить поставщик TempData на основе сеанса, используйте метод расширения <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A>. Требуется только один вызов `AddSessionStateTempDataProvider`:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,8,30)]

## <a name="query-strings"></a>Строки запроса

Вы можете передать ограниченный объем данных из одного запроса в другой, добавив их в строку запроса нового запроса. Это удобно для захвата состояния в сохраняемом виде, что позволяет обмениваться ссылками с внедренным состоянием по электронной почте или через социальные сети. Поскольку строки запроса URL-адреса являются открытыми, никогда не используйте их для конфиденциальных данных.

В дополнение к непреднамеренному совместному использованию, включая данные в строках запроса, приложение может стать уязвимым для атак [с подделкой межсайтовых запросов (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)). Любое сохраненное состояние сеанса необходимо защитить от атак CSRF. Для получения дополнительной информации см. <xref:security/anti-request-forgery>.

## <a name="hidden-fields"></a>Скрытые поля

Данные можно сохранить в скрытых полях формы и опубликовать обратно при следующем запросе. Это типичное поведение для многостраничных форм. Так как клиент может незаконно изменить данные, приложение всегда должно перепроверять данные в скрытых полях.

## <a name="httpcontextitems"></a>HttpContext.Items

Коллекция <xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType> используется для хранения данных при обработке отдельного запроса. Ее содержимое удаляется после обработки каждого запроса. Коллекцию `Items` часто используют для обеспечения взаимодействия компонентов или ПО промежуточного слоя, когда они выполняются в разные моменты времени во время обработки запроса и не могут передавать параметры напрямую.

В следующем примере [ПО промежуточного слоя](xref:fundamentals/middleware/index) добавляет `isVerified` в коллекцию `Items`:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

Для ПО промежуточного слоя, которое используется всего одним приложением, допустимы фиксированные ключи `string`. ПО промежуточного слоя, используемое несколькими приложениями, должно использовать уникальные ключи объекта во избежание конфликтов. В следующем примере показано, как использовать уникальный ключ объекта, определенный в классе ПО промежуточного слоя:

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Другой код может обратиться к значению, хранящемуся в `HttpContext.Items`, с помощью ключа, предоставляемого классом ПО промежуточного слоя:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Данный подход также позволяет не использовать строки ключей в коде.

## <a name="cache"></a>Кэш

Кэширование — это эффективный способ хранения и извлечения данных. Приложение может управлять временем существования элементов кэша. Для получения дополнительной информации см. <xref:performance/caching/response>.

Кэшированные данные не связаны с конкретным запросом, пользователем или сеансом. **Не кэшируйте данные пользователя, которые можно извлечь по запросу другого пользователя.**

Сведения о кэшировании данных для всего приложения см. в разделе <xref:performance/caching/memory>.

## <a name="common-errors"></a>Распространенные ошибки

* "Unable to resolve service for type "Microsoft.Extensions.Caching.Distributed.IDistributedCache" while attempting to activate "Microsoft.AspNetCore.Session.DistributedSessionStore"" (Не удается разрешить службу для типа "Microsoft.Extensions.Caching.Distributed.IDistributedCache" при попытке активировать "Microsoft.AspNetCore.Session.DistributedSessionStore").

  Обычно она вызвана невозможностью настройки по меньшей мере одной реализации `IDistributedCache`. Дополнительные сведения см. в разделах <xref:performance/caching/distributed> и <xref:performance/caching/memory>.

Если ПО промежуточного слоя сеанса не удается сохранить сеанс:

* ПО промежуточного слоя регистрирует исключение, и запрос продолжит работу в обычном режиме.
* Это приводит к непредсказуемому поведению.

ПО промежуточного слоя сеанса может не сохранять сеанс, если резервное хранилище недоступно. Например, пользователь сохраняет корзину для покупок в сеансе. Он добавляет товар в корзину, но фиксация завершается сбоем. Приложению неизвестно о сбое, поэтому оно сообщает пользователю, что товар добавлен в корзину, хотя это не так.

Для проверки на наличие таких ошибок рекомендуется вызывать `await feature.Session.CommitAsync` по окончании записи в сеанс. <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*> создает исключение, если резервное хранилище недоступно. Если `CommitAsync` завершается ошибкой, приложение может обработать исключение. Исключение <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*> возникает в таких же условиях, когда хранилище данных недоступно.
  
## <a name="signalr-and-session-state"></a>SignalR и состояние сеанса

Приложения SignalR не должны использовать состояние сеанса для хранения информации. Приложения SignalR могут хранить состояние каждого подключения в элементе `Context.Items` в концентраторе. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Дополнительные ресурсы

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Стив Смит](https://ardalis.com/) (Steve Smith), [Диана Лароуз](https://github.com/DianaLaRose) (Diana LaRose) и [Люк Лэтэм](https://github.com/guardrex) (Luke Latham)

HTTP — это протокол без отслеживания состояния. Без дополнительных действий HTTP-запросы являются независимыми сообщениями, которые не сохраняют значения пользователя или состояние приложения. В этой статье описывается несколько подходов для сохранения данных пользователя и состояния приложения между запросами.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Управление состоянием

Состояние можно сохранить несколькими способами. Эти способы обсуждается ниже в данном разделе.

| Способ хранения данных | Механизм хранения |
| ---------------- | ----------------- |
| [Файлы Cookie](#cookies) | Файлы cookie HTTPS (могут содержать данные, сохраненные с помощью кода приложения на стороне сервера) |
| [Состояние сеанса](#session-state) | Файлы cookie HTTP и код приложения на стороне сервера |
| [TempData](#tempdata) | Файлы cookie HTTP или состояние сеанса |
| [Строки запросов](#query-strings) | Строки запросов HTTP |
| [Скрытые поля](#hidden-fields) | Поля формы HTTP |
| [HttpContext.Items](#httpcontextitems) | Код приложения на стороне сервера |
| [Кэш](#cache) | Код приложения на стороне сервера |
| [Введение зависимостей](#dependency-injection) | Код приложения на стороне сервера |

## <a name="cookies"></a>Cookies

Файлы Cookie хранят данные между запросами. Так как файлы cookie отправляются с каждым запросом, их размер нужно свести к минимуму. В идеальном случае файл cookie должен содержать лишь идентификатор, а сами данные хранятся в приложении. Большинство браузеров позволяют использовать файлы cookie размером до 4096 байт. Для каждого домена доступно лишь ограниченное число файлов cookie.

Так как файлы cookie могут быть незаконно изменены, их нужно проверять в приложении. Файлы Cookie могут удаляться пользователем и имеют ограниченный срок хранения на клиентах. Тем не менее файлы cookie обычно являются самым надежным способом хранения данных на стороне клиента.

Файлы Cookie часто используются для персонализации, когда содержимое настраивается под известного пользователя. В большинстве случаев пользователь проходит только идентификацию, а не проверку подлинности. Файл cookie может хранить имя пользователя, имя учетной записи или уникальный идентификатор пользователя, например GUID. Затем можно использовать файл cookie для доступа к личным настройкам пользователя, таким как цвет фона на веб-сайте.

Помните об [Общем регламенте по защите данных в ЕС (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) при создании файлов cookie и решении вопросов с конфиденциальностью. Дополнительные сведения см. в разделе [Общий регламент по защите данных (GDPR), принятый в ЕС, в ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Состояние сеанса

Состояние сеанса — это сценарий ASP.NET Core для хранения пользовательских данных, когда пользователь находится в веб-приложении. Состояние сеанса использует хранилище, обслуживаемое приложением, для сохранения данных между запросами от клиента. Данные сеанса поддерживаются кэшем и считаются временными, &mdash; сайт должен работать и без данных сеанса. Критически важные данные приложения должны храниться в пользовательской базе данных и кэшироваться в сеансе только в качестве оптимизации производительности.

> [!NOTE]
> Сеанс не поддерживается в приложениях [SignalR](xref:signalr/index), так как [концентратор SignalR](xref:signalr/hubs) может работать независимо от контекста HTTP. Например, это может произойти, если хаб поддерживает длительный запрос на опрос открытым, когда время существования контекста HTTP для запроса уже истекло.

ASP.NET Core сохраняет состояние сеанса, предоставляя клиенту файл cookie, содержащий идентификатор сеанса, который отправляется в приложение вместе с каждым запросом. Приложение использует этот идентификатор для получения данных сеанса.

Состояние сеанса реагирует на события следующим образом:

* Так как файл cookie сеанса относится к конкретному браузеру, обеспечить общий доступ к сеансам из разных браузеров невозможно.
* Файлы cookie сеанса удаляются при завершении сеанса браузера.
* Если получен файл cookie для просроченного сеанса, создается сеанс, использующий этот же файл cookie.
* Пустые сеансы не сохраняются &mdash; в сеансе должно быть хотя бы одно значение, чтобы его можно быть сохранить между запросами. Если сеанс не сохраняется, для каждого нового запроса создается новый идентификатор сеанса.
* Приложение хранит сеанс некоторое время после последнего запроса. Приложение устанавливает время ожидания сеанса или использует значение по умолчанию, равное 20 минутам. Состояние сеанса оптимально подходит для сохранения пользовательских данных, относящихся к определенному сеансу, которые не требуется хранить бессрочно для нескольких сеансов.
* Данные сеанса удаляются при вызове реализации <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> или когда истекает срок действия сеанса.
* Не существует механизма по умолчанию, который бы информировал код приложения о том, что браузер клиента закрыт или файл cookie сеанса удален или просрочен на клиенте.
* Шаблоны ASP.NET Core MVC и Razor Pages поддерживают Общий регламент по защите данных (GDPR). Файлы cookie для состояния сеанса не помечаются как основные по умолчанию, поэтому состояние сеанса недоступно, если отслеживание разрешено посетителем сайта. Для получения дополнительной информации см. <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Не храните конфиденциальные данные в состоянии сеанса. Пользователь может не закрывать браузер и очистить файл cookie сеанса. Некоторые браузеры сохраняют файлы cookie сеанса в нескольких окнах браузера. Сеанс может быть не ограничен одним пользователем &mdash; следующий пользователь продолжит использовать приложение с тем же файлом cookie сеанса.

Поставщик кэша в памяти хранит данные сеанса в памяти сервера, содержащего приложение. В сценарии с фермой серверов:

* Используйте *прикрепленные сеансы* для привязки сеанса к конкретному экземпляру приложения на отдельном сервере. [Служба приложений Azure](https://azure.microsoft.com/services/app-service/) использует [маршрутизацию запросов приложений (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module), чтобы прикрепленные сеансы создавались по умолчанию. Однако прикрепленные сеансы могут негативно повлиять на масштабируемость и усложнить обновление веб-приложений. Лучше использовать распределенные кэши SQL Server или Redis, для которых прикрепленные сеансы не требуются. Для получения дополнительной информации см. <xref:performance/caching/distributed>.
* Файл cookie сеанса шифруется с помощью <xref:Microsoft.AspNetCore.DataProtection.IDataProtector>. Необходимо правильно настроить защиту данных, чтобы читать файлы cookie сеанса на каждом компьютере. Дополнительные сведения см. статьях <xref:security/data-protection/introduction> и [Key storage providers in ASP.NET Core](xref:security/data-protection/implementation/key-storage-providers) (Поставщики хранилища ключей в ASP.NET Core).

### <a name="configure-session-state"></a>Настройка состояния сеанса

Пакет [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/), который входит в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), предоставляет ПО промежуточного слоя для управления состоянием сеанса. Чтобы включить сеанс ПО промежуточного слоя для сеансов, `Startup` должен содержать:

* Любой из кэшей памяти <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, при этом реализация `IDistributedCache` используется в качестве резервного хранилища для сеанса. Для получения дополнительной информации см. <xref:performance/caching/distributed>.
* Вызов к <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> в `ConfigureServices`.
* Вызов к <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> в `Configure`.

Следующий код показывает, как настроить поставщик сеансов в памяти с реализацией в памяти `IDistributedCache` по умолчанию:

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

Порядок ПО промежуточного слоя важен. В предыдущем примере исключение `InvalidOperationException` возникает при вызове `UseSession` после `UseMvc`. Дополнительные сведения см. в разделе [Порядок расположения ПО промежуточного слоя](xref:fundamentals/middleware/index#order).

После настройки состояния сеанса доступно свойство <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType>.

Невозможно получить доступ к `HttpContext.Session` до вызова `UseSession`.

Невозможно создать новый сеанс с новым файлом cookie сеанса после того, как приложение начинает запись в поток ответа. Исключение записывается в журнал веб-сервера и не отображается в браузере.

### <a name="load-session-state-asynchronously"></a>Асинхронная загрузка состояния сеанса

Поставщик сеансов по умолчанию в ASP.NET Core загружает записи сеанса из базового резервного хранилища <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> в асинхронном режиме только при явном вызове метода <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> перед методами <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A>, <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> или <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A>. Если `LoadAsync` не вызывается первым, базовая запись сеанса загружается синхронно, что может негативно повлиять на производительность.

Чтобы принудительно использовать этот режим в приложениях, включите реализации <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> и <xref:Microsoft.AspNetCore.Session.DistributedSession> в версии, которые выдают исключение, когда метод `LoadAsync` не вызывается перед `TryGetValue`, `Set` или `Remove`. Зарегистрируйте версии оболочки в контейнере служб.

### <a name="session-options"></a>Параметры сеанса

Чтобы переопределить значения по умолчанию для сеанса, используйте <xref:Microsoft.AspNetCore.Builder.SessionOptions>.

| Параметр | Описание |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Определяет параметры, используемые для создания файлов cookie. Для <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> задается значение по умолчанию <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> (`.AspNetCore.Session`). Для <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> задается значение по умолчанию <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> (`/`). Для <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> задается значение по умолчанию <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> (`1`). Для <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> задается значение по умолчанию `true`. Для <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> задается значение по умолчанию `false`. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout` указывает, как долго сеанс может быть неактивным, прежде чем его содержимое отбрасывается. Каждый доступ к сеансу сбрасывает время ожидания. Этот параметр применяется только к содержимому сеанса, а не к файлу cookie. Значение по умолчанию — 20 минут. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | Максимальный период загрузки сеанса из хранилища или его сохранения обратно в хранилище. Этот параметр может применяться только к асинхронным операциям. Время ожидания можно отключить с помощью <xref:System.Threading.Timeout.InfiniteTimeSpan>. Значение по умолчанию — 1 минута. |

Сеанс использует файл cookie для отслеживания и идентификации запросов в одном браузере. По умолчанию этот файл cookie называется `.AspNetCore.Session` и использует путь `/`. Так как по умолчанию файл cookie не определяет домен, он остается недоступным для клиентского скрипта на странице (так как для <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> по умолчанию задается значение `true`).

Чтобы переопределить значения по умолчанию для файла cookie сеанса, используйте `SessionOptions`.

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

Приложение использует свойство <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout>, чтобы определить, как долго сеанс может оставаться неактивным до сброса его содержимого в кэше сервера. Это свойство не зависит от срока действия файла cookie. Каждый запрос, проходящий через [ПО промежуточного слоя сеанса](xref:Microsoft.AspNetCore.Session.SessionMiddleware), сбрасывает это время ожидания.

Состояние сеанса является *неблокирующим*. Когда два запроса пытаются изменить содержимое сеанса, последний из них переопределяет первый. `Session` реализован в виде *согласованного сеанса*, что означает, что все содержимое хранится вместе. Когда два запроса пытаются изменить разные значения сеанса, последний запрос может переопределить изменения, внесенные первым.

### <a name="set-and-get-session-values"></a>Установка и получение значений сеанса

Доступ к состоянию сеанса осуществляется из класса Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> или класса MVC <xref:Microsoft.AspNetCore.Mvc.Controller> с <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType>. Это свойство является реализацией <xref:Microsoft.AspNetCore.Http.ISession>.

Реализация `ISession` предоставляет несколько методов расширения для задания и извлечения значений типа integer и string. Методы расширения находятся в пространстве имен <xref:Microsoft.AspNetCore.Http> (добавьте оператор `using Microsoft.AspNetCore.Http;`, чтобы получить доступ к методам расширения), когда проект ссылается на пакет [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/). Оба пакета входят в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Методы расширения `ISession`:

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

В следующем примере извлекается значение сеанса для ключа `IndexModel.SessionKeyName` (`_Name` в примере приложения) на странице Razor Pages.

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

В следующем примере показано, как задать, а затем получить значения integer и string:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Все данные сеанса должны быть сериализованы, чтобы использовать сценарий-распределенного кэша даже при использовании кэша в памяти. Сериализаторы строк и целых чисел предоставляются методами расширения <xref:Microsoft.AspNetCore.Http.ISession>. Сложные типы должны быть сериализованы пользователем с помощью другого механизма, например JSON.

Добавьте приведенные ниже методы расширения, чтобы задавать и получать сериализуемые объекты:

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Следующий пример показывает, как задать и получить сериализуемый объект с помощью методов расширения:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core предоставляет [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) Razor Pages или <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> контроллера. Это свойство хранит данные до тех пор, пока они не будут прочитаны в другом запросе. Для проверки данных без удаления можно использовать методы [Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) и [Peek(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) в конце запроса. [Keep()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) помечает все элементы в словаре для хранения. `TempData` особенно удобно использовать для перенаправления, когда данные требуются для нескольких запросов. `TempData` реализуется поставщиками `TempData`, например с помощью файлов cookie или состояния сеанса.

## <a name="tempdata-samples"></a>Примеры TempData

Рассмотрим следующую страницу, которая создает клиент:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

На следующей странице отображается `TempData["Message"]`:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

В предыдущей разметке в конце запроса `TempData["Message"]`**не** удаляется, так как используется `Peek`. На обновляемой странице отображается `TempData["Message"]`.

Следующая разметка похожа на приведенный выше код, но в ней используется `Keep` для сохранения данных в конце запроса:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

При переходе между страницами *IndexPeek* и *IndexKeep*`TempData["Message"]` не удаляется.

Следующий код отображает `TempData["Message"]`, но в конце запроса `TempData["Message"]` удаляется:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Поставщики TempData

Поставщик TempData, основанный на файлах cookie, используется по умолчанию для хранения TempData в файлах cookie.

Данные файлов cookie шифруются с помощью <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> с кодировкой с использованием <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> и последующей фрагментацией. Так как файл cookie фрагментируется, ограничение на размер одного такого файла cookie, заданное в ASP.NET Core 1.x, не применяется. Данные файла cookie не сжимаются, так как сжатие зашифрованных данных может привести к проблемам с безопасностью, например атакам [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) и [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)). Дополнительные сведения о поставщике TempData, основанном на файлах cookie, см. здесь: <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>.

### <a name="choose-a-tempdata-provider"></a>Выбор поставщика TempData

Выбор поставщика TempData включает в себя несколько аспектов:

1. Приложение уже использует состояние сеанса? Если это так, использование поставщика TempData для состояния сеанса не требует от приложения никаких дополнительных издержек (кроме объема данных).
2. Приложение использует TempData лишь ограниченно, для сравнительно небольших объемов данных (до 500 байт)? Если это так, поставщик TempData на основе файлов cookie незначительно увеличивает издержки для каждого запроса, переносящего TempData. В противном случае поставщик TempData для состояния сеанса удобно использовать, чтобы устранить круговой обход большого объема данных в каждом запросе до полного использования TempData.
3. Приложение выполняется на ферме серверов на нескольких серверах? Если это так, не требуется дополнительная настройка для использования поставщика TempData для файлов cookie, за исключением защиты данных (см. статьи <xref:security/data-protection/introduction> и [Поставщики услуг хранилищ ключей](xref:security/data-protection/implementation/key-storage-providers)).

> [!NOTE]
> Большинство веб-клиентов (например, веб-браузеры) налагают ограничение на максимальный размер каждого файла cookie и (или) общее количество файлов cookie. При использовании поставщика TempData на основе файлов cookie убедитесь, что приложение не нарушает эти ограничения. Учитывайте общий объем данных. Учитывайте увеличение размеров файла cookie в результате шифрования и фрагментирования.

### <a name="configure-the-tempdata-provider"></a>Настройка поставщика TempData

Поставщик TempData на основе файлов cookie включен по умолчанию.

Чтобы включить поставщик TempData на основе сеанса, используйте метод расширения <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A>.

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

Порядок ПО промежуточного слоя важен. В предыдущем примере исключение `InvalidOperationException` возникает при вызове `UseSession` после `UseMvc`. Дополнительные сведения см. в разделе [Порядок расположения ПО промежуточного слоя](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> Если вы ориентируетесь на .NET Framework и используете поставщик TempData на основе сеансов, добавьте в проект пакет [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/).

## <a name="query-strings"></a>Строки запроса

Вы можете передать ограниченный объем данных из одного запроса в другой, добавив их в строку запроса нового запроса. Это удобно для захвата состояния в сохраняемом виде, что позволяет обмениваться ссылками с внедренным состоянием по электронной почте или через социальные сети. Поскольку строки запроса URL-адреса являются открытыми, никогда не используйте их для конфиденциальных данных.

Вдобавок к случайному раскрытию информации включение данных в строки запроса может открыть возможности для атак с [подделкой межсайтовых запросов (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)), которые могут обманом вынудить пользователей посещать вредоносные веб-сайты во время проверки подлинности. Это позволит злоумышленникам похитить данные пользователей из приложения или предпринимать вредоносные действия от их имени. Любое сохраненное состояние приложения или сеанса необходимо защитить от атак CSRF. Для получения дополнительной информации см. <xref:security/anti-request-forgery>.

## <a name="hidden-fields"></a>Скрытые поля

Данные можно сохранить в скрытых полях формы и опубликовать обратно при следующем запросе. Это типичное поведение для многостраничных форм. Так как клиент может незаконно изменить данные, приложение всегда должно перепроверять данные в скрытых полях.

## <a name="httpcontextitems"></a>HttpContext.Items

Коллекция <xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType> используется для хранения данных при обработке отдельного запроса. Ее содержимое удаляется после обработки каждого запроса. Коллекцию `Items` часто используют для обеспечения взаимодействия компонентов или ПО промежуточного слоя, когда они выполняются в разные моменты времени во время обработки запроса и не могут передавать параметры напрямую.

В следующем примере [ПО промежуточного слоя](xref:fundamentals/middleware/index) добавляет `isVerified` в коллекцию `Items`.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

Далее в конвейере другое ПО промежуточного слоя может получить доступ к значению `isVerified`:

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Для ПО промежуточного слоя, которое используется всего одним приложением, допустимы ключи `string`. ПО промежуточного слоя, используемое несколькими экземплярами приложения, должно использовать уникальные ключи объекта во избежание конфликтов. В следующем примере показано, как использовать уникальный ключ объекта, определенный в классе ПО промежуточного слоя:

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Другой код может обратиться к значению, хранящемуся в `HttpContext.Items`, с помощью ключа, предоставляемого классом ПО промежуточного слоя:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Данный подход также позволяет не использовать строки ключей в коде.

## <a name="cache"></a>Кэш

Кэширование — это эффективный способ хранения и извлечения данных. Приложение может управлять временем существования элементов кэша.

Кэшированные данные не связаны с конкретным запросом, пользователем или сеансом. **Будьте внимательны и не кэшируйте данные пользователя, которые можно извлечь по запросу другого пользователя.**

Для получения дополнительной информации см. <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Внедрение зависимостей

Используйте [внедрение зависимостей](xref:fundamentals/dependency-injection), чтобы сделать данные доступными для всех пользователей:

1. Определите службу, содержащую данные. Например, определяется класс с именем `MyAppData`:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Добавьте класс службы в `Startup.ConfigureServices`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Используйте класс службы данных:

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

## <a name="common-errors"></a>Распространенные ошибки

* "Unable to resolve service for type "Microsoft.Extensions.Caching.Distributed.IDistributedCache" while attempting to activate "Microsoft.AspNetCore.Session.DistributedSessionStore"" (Не удается разрешить службу для типа "Microsoft.Extensions.Caching.Distributed.IDistributedCache" при попытке активировать "Microsoft.AspNetCore.Session.DistributedSessionStore").

  Обычно она вызвана невозможностью настройки по меньшей мере одной реализации `IDistributedCache`. Дополнительные сведения см. в разделах <xref:performance/caching/distributed> и <xref:performance/caching/memory>.

* Если ПО промежуточного слоя для сеанса не удается сохранить сеанс (например, резервное хранилище недоступно), оно регистрирует исключение и запрос выполняется обычным образом. Это приводит к непредсказуемому поведению.

  Например, пользователь сохраняет корзину для покупок в сеансе. Он добавляет товар в корзину, но фиксация завершается сбоем. Приложению неизвестно о сбое, поэтому оно сообщает пользователю, что товар добавлен в корзину, хотя это не так.

  Для проверки на наличие таких ошибок рекомендуется вызывать `await feature.Session.CommitAsync();` из кода приложения по окончании записи в сеанс. `CommitAsync` создает исключение, если резервное хранилище недоступно. Если `CommitAsync` завершается ошибкой, приложение может обработать исключение. Исключение `LoadAsync` возникает в таких же условиях, когда хранилище данных недоступно.
  
## <a name="signalr-and-session-state"></a>SignalR и состояние сеанса

Приложения SignalR не должны использовать состояние сеанса для хранения информации. Приложения SignalR могут хранить состояние каждого подключения в элементе `Context.Items` в концентраторе. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Дополнительные ресурсы

<xref:host-and-deploy/web-farm>
::: moniker-end
