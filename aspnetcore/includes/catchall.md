---
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
ms.openlocfilehash: ca743cdb39423d833902c330f6f0682b600c9833
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552441"
---
::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> Соответствие параметра **catch-all** маршрутам может быть неправильным из-за [ошибки](https://github.com/dotnet/aspnetcore/issues/18677) в маршрутизации. Приложения, на работу которых влияет эта ошибка, обладают следующими характеристиками:
>
> * Маршрут catch-all, например `{**slug}"`.
> * Маршрут catch-all не соответствует необходимым запросам.
> * После удаления других маршрутов маршрут catch-all начинает функционировать должным образом.
>
> Ознакомьтесь с примерами [18677](https://github.com/dotnet/aspnetcore/issues/18677) и [16579](https://github.com/dotnet/aspnetcore/issues/16579), в которых встречается эта ошибка, на сайте GitHub.
>
> Опциональное исправление для этой ошибки содержится в [пакете SDK для .NET Core начиная с версии 3.1.301](https://dotnet.microsoft.com/download/dotnet-core/3.1). Следующий код задает внутренний переключатель, исправляющий эту ошибку:
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end