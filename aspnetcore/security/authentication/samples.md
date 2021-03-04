---
title: Примеры проверки подлинности для ASP.NET Core
author: rick-anderson
description: Содержит ссылки на примеры проверки подлинности в репозитории ASP.NET Core.
ms.author: riande
ms.date: 02/21/2021
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
uid: security/authentication/samples
ms.openlocfilehash: e7fb2ac32f57cf4ecd3c5db294bd0df8e186b6c6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110122"
---
# <a name="authentication-samples-for-aspnet-core"></a>Примеры проверки подлинности для ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

[Репозиторий ASP.NET Core](https://github.com/dotnet/aspnetcore) содержит следующие примеры проверки подлинности ( `main` ветвь):

* [Преобразование утверждений](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/ClaimsTransformation)
* [Cookie идентификаци](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)
* [Пользовательский ответ на ошибку авторизации](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomAuthorizationFailureResponse)
* [Поставщик настраиваемой политики — Иаусоризатионполиципровидер](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomPolicyProvider)
* [Схемы и параметры динамической проверки подлинности](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/DynamicSchemes)
* [Внешние утверждения](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)
* [Выбор между cookie и другой схемой проверки подлинности на основе запроса](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/PathSchemeSelection)
* [Разрешает доступ к статическим файлам](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/StaticFilesAuth)

## <a name="obtain-and-run-the-samples"></a>Получение и запуск примеров

Примеры ссылок, приведенные в этой статье, содержат образцы для предстоящего выпуска ASP.NET Core. Чтобы получить пример для текущего выпуска или более ранней версии, выполните следующие действия.

* Выберите ветвь выпуска [репозитория ASP.NET Core](https://github.com/dotnet/aspnetcore)] ( https://github.com/dotnet/aspnetcore) . Например, `release/5.0` ветвь содержит примеры для выпуска ASP.NET Core 5,0.
* Клонировать или скачать репозиторий ASP.NET Core.
* Убедитесь, что в локальной системе установлена версия [пакет SDK для .NET Core](https://dotnet.microsoft.com/download/dotnet-core) , соответствующая клону репозитория ASP.NET Core.
* Перейдите к примеру в `aspnetcore/src/Security/samples` папке и запустите пример с помощью [ `dotnet run` команды](/dotnet/core/tools/dotnet-run).
