---
title: Настройка средства обрезки для ASP.NET Core Blazor
author: guardrex
description: Узнайте, как управлять компоновщиком (средством обрезки) для промежуточного языка (IL) при сборке приложения Blazor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975223"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a>Настройка средства обрезки для ASP.NET Core Blazor

Blazor WebAssembly выполняет обрезку [промежуточного языка (IL)](/dotnet/standard/managed-code#intermediate-language--execution) для уменьшения размера публикуемых выходных данных. По умолчанию усечение выполняется при публикации приложения.

Обрезка может иметь негативные последствия. В приложениях, использующих отражение, обрезка зачастую не позволяет определить необходимые типы для отражения во время выполнения. Чтобы обрезать такие приложения, средство обрезки должно быть уведомлено о любых типах, необходимых для отражения как в коде, так и в пакетах или платформах, от которых зависит приложение. Обрезка также не реагирует на динамическое поведение приложения во время выполнения. Чтобы обеспечить правильную работу обрезанного приложения после его развертывания, следует часто тестировать публикуемые выходные данные во время разработки.

Сведения о настройке средства обрезки см. в статье [Параметры обрезки](/dotnet/core/deploying/trimming-options) в документации по основным понятиям .NET, в которую входят рекомендации по следующим темам:

* Отключение обрезки для всего приложения со свойством `<PublishTrimmed>` в файле проекта.
* Управление тем, как агрессивно неиспользуемый IL отбрасывается с помощью усечения.
* Остановка усечения отдельных сборок.
* "Корневые" сборки для усечения.
* Отображение предупреждений для отраженных типов путем присвоения свойству `<SuppressTrimAnalysisWarnings>` значения `false` в файле проекта.
* Управление обрезкой символов и поддержкой отладчика.
* Настройка функций средства обрезки для усечения функций библиотеки инфраструктуры.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Обрезка автономных развертываний и исполняемых файлов](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
