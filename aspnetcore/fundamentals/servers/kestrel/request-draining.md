---
title: Очистка запросов с помощью веб-сервера Kestrel для ASP.NET Core
author: rick-anderson
description: Сведения об очистке запросов с помощью Kestrel, кроссплатформенного веб-сервера для ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253867"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="34329-103">Очистка запросов с помощью веб-сервера Kestrel для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34329-103">Request draining with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="34329-104">Открытие HTTP-соединений занимает много времени.</span><span class="sxs-lookup"><span data-stu-id="34329-104">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="34329-105">Для протокола HTTPS это также требует больших ресурсов.</span><span class="sxs-lookup"><span data-stu-id="34329-105">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="34329-106">Поэтому Kestrel пытается повторно использовать подключения по протоколу HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="34329-106">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="34329-107">Чтобы разрешить повторное использование соединения, текст запроса должен быть полностью использован.</span><span class="sxs-lookup"><span data-stu-id="34329-107">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="34329-108">Приложение не всегда использует текст запроса, например запросы HTTP POST, в которых сервер возвращает ответ перенаправления или 404.</span><span class="sxs-lookup"><span data-stu-id="34329-108">The app doesn't always consume the request body, such as HTTP POST requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="34329-109">В случае перенаправления HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="34329-109">In the HTTP POST redirect case:</span></span>

* <span data-ttu-id="34329-110">Возможно, клиент уже отправил часть данных POST.</span><span class="sxs-lookup"><span data-stu-id="34329-110">The client may already have sent part of the POST data.</span></span>
* <span data-ttu-id="34329-111">Сервер записывает ответ 301.</span><span class="sxs-lookup"><span data-stu-id="34329-111">The server writes the 301 response.</span></span>
* <span data-ttu-id="34329-112">Соединение нельзя использовать для нового запроса, пока не будут полностью прочитаны данные POST из предыдущего текста запроса.</span><span class="sxs-lookup"><span data-stu-id="34329-112">The connection can't be used for a new request until the POST data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="34329-113">Kestrel пытается очистить текст запроса.</span><span class="sxs-lookup"><span data-stu-id="34329-113">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="34329-114">Очистка текста запроса означает чтение и отмену данных без их обработки.</span><span class="sxs-lookup"><span data-stu-id="34329-114">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="34329-115">Процесс очистки позволяет найти баланс между возможностью повторного использования соединения и временем, которое требуется для очистки оставшихся данных:</span><span class="sxs-lookup"><span data-stu-id="34329-115">The draining process makes a tradeoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="34329-116">Время ожидания очистки составляет 5 секунд. Этот параметр нельзя изменить.</span><span class="sxs-lookup"><span data-stu-id="34329-116">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="34329-117">Если все данные, указанные в заголовке `Content-Length` или `Transfer-Encoding`, не были считаны до истечения времени ожидания, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="34329-117">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="34329-118">Иногда может потребоваться немедленно завершить запрос до или после записи ответа.</span><span class="sxs-lookup"><span data-stu-id="34329-118">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="34329-119">Например, клиенты могут иметь строгие ограничения на данные.</span><span class="sxs-lookup"><span data-stu-id="34329-119">For example, clients may have restrictive data caps.</span></span> <span data-ttu-id="34329-120">При этом приоритетным требованием может быть ограничение передаваемых данных.</span><span class="sxs-lookup"><span data-stu-id="34329-120">Limiting uploaded data might be a priority.</span></span> <span data-ttu-id="34329-121">В таких случаях для завершения запроса вызовите [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) из контроллера, страницы Razor или ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="34329-121">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="34329-122">Вызов `Abort` имеет определенные недостатки.</span><span class="sxs-lookup"><span data-stu-id="34329-122">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="34329-123">Создание новых подключений может выполняться очень медленно и требовать много ресурсов.</span><span class="sxs-lookup"><span data-stu-id="34329-123">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="34329-124">Нет никакой гарантии, что клиент прочитал ответ перед закрытием соединения.</span><span class="sxs-lookup"><span data-stu-id="34329-124">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="34329-125">Вызов `Abort` следует использовать редко и только для серьезных, а не распространенных ошибок.</span><span class="sxs-lookup"><span data-stu-id="34329-125">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="34329-126">Вызывайте `Abort`, только когда нужно решить конкретную проблему.</span><span class="sxs-lookup"><span data-stu-id="34329-126">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="34329-127">Например, вызовите `Abort`, если вредоносные клиенты пытаются выполнить операцию POST с данными или если в клиентском коде есть ошибка, вызывающая большие или многочисленные запросы.</span><span class="sxs-lookup"><span data-stu-id="34329-127">For example, call `Abort` if malicious clients are trying to POST data or when there's a bug in client code that causes large or several requests.</span></span>
  * <span data-ttu-id="34329-128">Не вызывайте `Abort` для распространенных ошибок, таких как HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="34329-128">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="34329-129">Вызов [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) перед вызовом `Abort` гарантирует, что сервер завершит запись ответа.</span><span class="sxs-lookup"><span data-stu-id="34329-129">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="34329-130">Однако поведение клиента не предсказуемо. Он может не считать ответ, прежде чем подключение будет прервано.</span><span class="sxs-lookup"><span data-stu-id="34329-130">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="34329-131">Этот процесс отличается для HTTP/2, так как протокол поддерживает прерывание отдельных потоков запросов без закрытия соединения.</span><span class="sxs-lookup"><span data-stu-id="34329-131">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="34329-132">5-секундное время ожидания очистки не применяется.</span><span class="sxs-lookup"><span data-stu-id="34329-132">The five-second drain timeout doesn't apply.</span></span> <span data-ttu-id="34329-133">Если после завершения ответа сервер содержит непрочтенные данные текста запроса, он отправляет кадр HTTP/2 RST.</span><span class="sxs-lookup"><span data-stu-id="34329-133">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="34329-134">Дополнительные кадры данных текста запроса игнорируются.</span><span class="sxs-lookup"><span data-stu-id="34329-134">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="34329-135">По возможности клиентам лучше использовать заголовок запроса [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) и дожидаться ответа сервера перед началом отправки текста запроса.</span><span class="sxs-lookup"><span data-stu-id="34329-135">If possible, it's better for clients to use the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="34329-136">Это дает клиенту возможность проверить ответ и прервать операцию перед отправкой ненужных данных.</span><span class="sxs-lookup"><span data-stu-id="34329-136">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>
