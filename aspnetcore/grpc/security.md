---
title: Вопросы безопасности применительно к gRPC для ASP.NET Core
author: jamesnk
description: Сведения о вопросах безопасности применительно к gRPC для ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
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
uid: grpc/security
ms.openlocfilehash: 45ac0916a368cf68f4d40e14298a7628446989ee
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252816"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>Вопросы безопасности применительно к gRPC для ASP.NET Core

Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)

В этой статье представлены сведения о защите gRPC в .NET Core.

## <a name="transport-security"></a>Защита транспорта

Сообщения gRPC отправляются и принимаются по протоколу HTTP/2. Примите во внимание следующие рекомендации.

* Для защиты сообщений в рабочих приложениях gRPC следует использовать [протокол TLS](https://tools.ietf.org/html/rfc5246).
* Службы gRPC должны ожидать передачи данных и отвечать только через защищенные порты.

::: moniker range=">= aspnetcore-5.0"
В Kestrel следует настроить протокол TLS. Дополнительные сведения о настройке конечных точек Kestrel см. в статье [Конфигурация конечной точки Kestrel](xref:fundamentals/servers/kestrel/endpoints).
::: moniker-end

::: moniker range="< aspnetcore-5.0"
В Kestrel следует настроить протокол TLS. Дополнительные сведения о настройке конечных точек Kestrel см. в статье [Конфигурация конечной точки Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).
::: moniker-end

## <a name="exceptions"></a>Исключения

Сообщения об исключениях, как правило, считаются конфиденциальными данными, которые не следует раскрывать клиенту. По умолчанию gRPC не отправляет сведения об исключении, возникшем в службе gRPC, клиенту. Вместо этого клиент получает общее сообщение с указанием произошедшей ошибки. Доставку сообщений об исключениях клиенту можно переопределить (например, при разработке или тестировании) с помощью [EnableDetailedErrors](xref:grpc/configuration#configure-services-options). В рабочих приложениях сообщения об исключениях не должны предоставляться клиенту.

## <a name="message-size-limits"></a>Ограничения на размер сообщений

Входящие сообщения для клиентов и служб gRPC загружаются в память. Ограничения на размер сообщений позволяют предотвратить чрезмерное потребление ресурсов системой gRPC.

В gRPC используются ограничения на размер отдельных входящих и исходящих сообщений. По умолчанию максимальный размер входящего сообщения в gRPC составляет 4 МБ. На размер исходящих сообщений ограничений нет.

На сервере можно настроить ограничения на размер сообщений для всех служб приложения gRPC с помощью `AddGrpc`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

С помощью `AddServiceOptions<TService>` можно также настроить ограничения для отдельной службы. Дополнительные сведения о настройке ограничений на размер сообщений см. в статье [Настройка gRPC](xref:grpc/configuration).

## <a name="client-certificate-validation"></a>Проверка сертификата клиента

[Сертификаты клиента](https://tools.ietf.org/html/rfc5246#section-7.4.4) изначально проверяются при установке соединения. По умолчанию Kestrel не выполняет дополнительную проверку сертификата клиента для подключения.

Рекомендуется, чтобы службы gRPC, защищенные с помощью сертификатов клиента, использовали пакет [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth). При проверке подлинности на основе сертификата в ASP.NET Core выполняются дополнительные проверки сертификата клиента, включая следующие:

* сертификат имеет допустимое значение расширенного использования ключа (EKU);
* период действия сертификата не истек;
* проверка отзыва сертификата.
