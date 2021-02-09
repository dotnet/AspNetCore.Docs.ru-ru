---
title: Использование gRPC на поддерживаемых платформах .NET
author: jamesnk
description: Сведения о поддерживаемых платформах для gRPC в .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: 6e48a19027f79b75edeebde9c584419871fba533
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530168"
---
# <a name="grpc-on-net-supported-platforms"></a>Использование gRPC на поддерживаемых платформах .NET

Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)

В этой статье описаны требования и поддерживаемые платформы для использования gRPC с .NET.

gRPC использует преимущества расширенных функций, предоставляемых HTTP/2. HTTP/2 поддерживается везде, но для gRPC доступен второй сетевой формат на основе HTTP/1.1:

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) — обычное использование gRPC через HTTP/2.
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) — gRPC-Web изменяет протокол gRPC, обеспечивая совместимость с HTTP/1.1. gRPC-Web можно использовать в других сценариях, в частности при вызове браузерными приложениями. Две расширенные функции gRPC больше не поддерживаются: потоковая передача клиента и двунаправленная потоковая передача.

gRPC в .NET поддерживает оба формата для передачи данных по сети. gRPC через HTTP/2 используется по умолчанию. Сведения о настройке gRPC-Web см. здесь: <xref:grpc/browser>.

## <a name="device-requirements"></a>Требования к устройствам

gRPC в .NET поддерживает любое устройство, поддерживаемое .NET Core.

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;
> * Браузеры&Dagger;

&dagger;[macOS не поддерживает размещение приложений ASP.NET Core с использованием HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos). Клиенты gRPC в macOS могут вызывать удаленные службы, использующие HTTPS.

Приложения Blazor WebAssembly&Dagger; могут вызывать службы gRPC с использованием gRPC-Web.

## <a name="aspnet-core-server-requirements"></a>Требования к серверу ASP.NET Core

Службы gRPC могут размещаться на всех встроенных серверах ASP.NET Core.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;Для работы служб IIS требуется .NET 5 и Windows 10 сборки 20241 или более поздней.

&Dagger;Для работы HTTP.sys требуется .NET 5 и Windows 10 сборки 19529 или более поздней.

Сведения о настройке серверов ASP.NET Core для запуска gRPC см. здесь: <xref:grpc/aspnetcore#server-options>.

## <a name="net-version-requirements"></a>Требования к версии .NET

gRPC в .NET поддерживает .NET Core 3 и .NET 5 или более поздней версии.

> [!div class="checklist"]
>
> * .NET 5 или более поздней версии
> * .NET Core 3

gRPC в .NET не поддерживает выполнение в .NET Framework и Xamarin. [gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) — это сторонняя библиотека, которая поддерживает .NET Framework and Xamarin. gRPC C-core не поддерживается Майкрософт.

## <a name="azure-services"></a>Службы Azure

> [!div class="checklist"]
>
> * [Служба Azure Kubernetes (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Служба приложений Azure](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Служба приложений Azure не поддерживает размещение gRPC по протоколу HTTP/2. gRPC-Web является совместимой альтернативой.

Мы работаем над улучшением поддержки gRPC с HTTP/2 в Службе приложений Azure. Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore/issues/9020).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/)
