---
title: Размещение и развертывание ASP.NET Core Blazor
author: guardrex
description: Узнайте, как размещать и развертывать приложения Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: blazor/host-and-deploy/index
ms.openlocfilehash: e7bc44b396b46e2ac3e0279520c7cc8ea6679f5a
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279766"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Размещение и развертывание ASP.NET Core Blazor

## <a name="publish-the-app"></a>Публикация приложения

Приложения публикуются для развертывания в конфигурации выпуска.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. На панели навигации выберите **Сборка** > **Опубликовать {приложение}** .
1. Выберите *целевой объект публикации*. Чтобы опубликовать объект в локальной среде, выберите **папку**.
1. Оставьте расположение по умолчанию в поле **выбора папки** или укажите другое расположение. Нажмите кнопку **`Publish`** .

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. Щелкните **Сборка** > **Опубликовать в папке**.
1. Подтвердите, что папка должна получить опубликованные ресурсы, и щелкните **`Publish`** .

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Используйте команду [`dotnet publish`](/dotnet/core/tools/dotnet-publish), чтобы опубликовать приложение с конфигурацией выпуска.

```dotnetcli
dotnet publish -c Release
```

---

Публикация приложения активирует [восстановление](/dotnet/core/tools/dotnet-restore) зависимостей проекта и выполняет [сборку](/dotnet/core/tools/dotnet-build) проекта, прежде чем создавать ресурсы для развертывания. В ходе процесса построения удаляются неиспользуемые методы и сборки, чтобы уменьшить размер скачиваемого приложения и время загрузки.

Расположения публикации.

* Blazor WebAssembly
  * Изолированное. Приложение будет опубликовано в папке `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot`. Чтобы развернуть приложение как статический сайт, скопируйте содержимое папки `wwwroot` на узел статического сайта.
  * Размещенное. Клиентское приложение Blazor WebAssembly будет опубликовано в папку `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` серверного приложения вместе со статическими веб-ресурсами серверного приложения. Разверните содержимое папки `publish` на узле.
* Blazor Server. Приложение будет опубликовано в папке `/bin/Release/{TARGET FRAMEWORK}/publish`. Разверните содержимое папки `publish` на узле.

Ресурсы из папки развертываются на веб-сервере. Развертывание может проводиться вручную или автоматизированно в зависимости от используемых средств разработки.

## <a name="app-base-path"></a>Базовый путь приложения

*Базовый путь приложения*  — это корневой URL-путь приложения. Рассмотрим следующее приложение ASP.NET Core и подприложение Blazor:

* Приложение ASP.NET Core называется `MyApp`:
  * Физическое расположение приложения: `d:/MyApp`.
  * запросы принимаются по адресу: `https://www.contoso.com/{MYAPP RESOURCE}`.
* Приложение Blazor с именем `CoolApp` является подприложением `MyApp`:
  * Физическое расположение дочернего приложения: `d:/MyApp/CoolApp`.
  * запросы принимаются по адресу: `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.

Если для `CoolApp` не указана дополнительная конфигурация, дочернее приложение в этом сценарии не имеет сведений о своем местоположении на сервере. Например, приложение не может создавать правильные относительные URL-адреса к своим ресурсам, если ему неизвестно, что оно находится по относительному пути URL `/CoolApp/`.

Чтобы указать конфигурацию для базового пути к приложению Blazor `https://www.contoso.com/CoolApp/`, в качестве значения атрибута `href` тега `<base>` задается относительный корневой путь в файле `wwwroot/index.html` (Blazor WebAssembly) или в файле `Pages/_Host.cshtml` (Blazor Server).

Blazor WebAssembly (`wwwroot/index.html`):

```html
<base href="/CoolApp/">
```

Blazor Server (`Pages/_Host.cshtml`):

```html
<base href="~/CoolApp/">
```

Приложения Blazor Server дополнительно устанавливают базовый путь на стороне сервера путем вызова <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> в конвейере запросов приложения `Startup.Configure`:

```csharp
app.UsePathBase("/CoolApp");
```

При указании относительного пути URL компонент, который не находится в корневом каталоге, может создавать URL-адреса относительно корневого пути приложения. Компоненты, которые существуют на разных уровнях структуры каталогов, могут создавать ссылки на другие ресурсы во всех местах приложения. Базовый путь к приложению также используется для перехвата выбранных гиперссылок, когда целевой объект ссылки `href` находится в пределах URI-пространства базового пути к приложению. Маршрутизатор Blazor обрабатывает внутреннюю навигацию.

Во многих сценариях размещения относительный путь URL к приложению является корнем приложения. В таких случаях относительный базовый путь URL к приложению представляет собой косую черту (`<base href="/" />`), что является конфигурацией по умолчанию для приложения Blazor. В других сценариях размещения, таких как страницы GitHub и подприложения IIS, базовому пути приложения должно быть присвоено значение относительного пути URL к серверу для приложения.

Чтобы задать базовый путь к приложению, измените тег `<base>` в элементах `<head>` тега файла `Pages/_Host.cshtml` (Blazor Server) или файла `wwwroot/index.html` (Blazor WebAssembly). Задайте для атрибута `href` значение `/{RELATIVE URL PATH}/` (Blazor WebAssembly) или `~/{RELATIVE URL PATH}/` (Blazor Server). **Требуется завершающая косая черта.** Заполнитель `{RELATIVE URL PATH}` — это полный относительный URL-путь к приложению.

Приложение Blazor WebAssembly с некорневым относительным путем URL (например, `<base href="/CoolApp/">`) не сможет найти свои ресурсы при *локальном запуске*. Для решения этой проблемы во время локальной разработки и тестирования можно предоставить аргумент *базового пути*, который соответствует значению `href` тега `<base>` во время выполнения. **Не добавляйте в конце косую черту.** Для передачи аргумента базового пути при локальном запуске приложения выполните из каталога приложения команду `dotnet run` с параметром `--pathbase`:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Для приложения Blazor WebAssembly с относительным путем URL `/CoolApp/` (`<base href="/CoolApp/">`) команда имеет следующий вид:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Приложение Blazor WebAssembly отвечает локально по `http://localhost:port/CoolApp`.

**Конфигурация Blazor Server `MapFallbackToPage`**

Передайте следующий путь к <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> в метод `Startup.Configure`:

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

Заполнитель `{RELATIVE PATH}` используется для пути к некорневому каталогу на сервере. Например, `CoolApp` будет сегментом заполнителя, если URL-адрес некорневого каталога приложения — `https://{HOST}:{PORT}/CoolApp/`):

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

**Размещение нескольких приложений Blazor WebAssembly**

Дополнительные сведения о размещении нескольких приложений Blazor WebAssembly в размещенном решении Blazor см. в разделе <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.

## <a name="deployment"></a>Развертывание

Инструкции по развертыванию см. в следующих разделах:

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
