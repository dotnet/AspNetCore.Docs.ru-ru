---
title: Инструменты для Blazor ASP.NET Core
author: guardrex
description: Сведения об инструментах, доступных для создания приложений Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 6b61d9a4645d273b0c78fae0388d569771c43a2d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536250"
---
# <a name="tooling-for-aspnet-core-blazor"></a>Инструменты для Blazor ASP.NET Core

::: zone pivot="windows"

1. Установите последнюю версию [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**.

1. Создайте новый проект.

1. Выберите **Приложение Blazor** . Выберите **Далее**.

1. В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию. Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое. Выберите **Создать**.

1. Для работы с Blazor WebAssembly выберите шаблон **приложения Blazor WebAssembly** . Для работы с Blazor Server выберите шаблон **приложения Blazor Server** . Выберите **Создать**.

   Для работы с размещенной платформой Blazor WebAssembly установите флажок **Размещенный проект ASP.NET Core**.

   Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* (автономной и размещенной) и *Blazor Server* , см. в статье <xref:blazor/hosting-models>.

1. Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.

Дополнительные сведения об установке доверия к сертификату разработки HTTPS ASP.NET Core см. здесь: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.

При выполнении размещенного приложения Blazor WebAssembly запустите приложение из проекта **`Server`** решения.

::: zone-end

::: zone pivot="linux"

1. Установите последнюю версию [пакета SDK для .NET Core](https://dotnet.microsoft.com/download). Если вы установили пакет SDK ранее, можно определить установленную версию, выполнив следующую команду в командной оболочке:

   ```dotnetcli
   dotnet --version
   ```

1. Установите последнюю версию [Visual Studio Code](https://code.visualstudio.com).

1. Установите актуальную версию [расширения C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

1. Для работы с Blazor WebAssembly выполните следующую команду в командной оболочке:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Для работы с размещенной платформой Blazor WebAssembly добавьте параметр hosted (`-ho` или `--hosted`) в команду.

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   Для работы с Blazor Server выполните следующую команду в командной оболочке:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* (автономной и размещенной) и *Blazor Server* , см. в статье <xref:blazor/hosting-models>.

1. Откройте папку `WebApplication1` в Visual Studio Code.

1. Среда IDE запрашивает добавление ресурсов для сборки и отладки проекта. Выберите ответ **Да**.

   **Запуск размещенного Blazor WebAssembly и конфигурация задачи**

   Для размещенных решений Blazor WebAssembly добавьте (или переместите) папку`.vscode` с файлами `launch.json` и `tasks.json` в родительскую папку решения — папку, содержащую типичные имена папок проекта `Client`, `Server` и `Shared`. Обновите или подтвердите, что конфигурация в файлах `launch.json` и `tasks.json` выполняет размещенное приложение Blazor WebAssembly из проекта **`Server`** .

   **`.vscode/launch.json`** (конфигурация `launch`):

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   В предыдущей конфигурации для текущего рабочего каталога (`cwd`) заполнителем `{SERVER APP FOLDER}` является папка проекта **`Server`** , обычно "`Server`".

   Если используется Microsoft Edge и в системе не установлен браузер Google Chrome, добавьте в конфигурацию дополнительное свойство `"browser": "edge"`.

   Пример для папки проекта `Server`, которая порождает Microsoft Edge в качестве браузера для выполнения отладки, вместо браузера по умолчанию Google Chrome:

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   **`.vscode/tasks.json`** (аргументы [команды `dotnet`](/dotnet/core/tools/dotnet)):

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   В предыдущем аргументе:

   * Заполнитель `{SERVER APP FOLDER}` — это папка проекта **`Server`** , обычно "`Server`".
   * Заполнитель `{PROJECT NAME}` — это имя приложения, обычно основанное на имени решения, за которым следует "`.Server`" в приложении, созданном на основе шаблона проекта Blazor.

   В следующем примере из [руководства по использованию SignalR с приложением Blazor WebAssembly](xref:tutorials/signalr-blazor) используется имя папки проекта `Server` и имя проекта `BlazorWebAssemblySignalRApp.Server`:

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

1. Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.

## <a name="trust-a-development-certificate"></a>Установка доверия к сертификату разработки

В Linux отсутствует централизованный способ установки доверия к сертификату. Обычно используется один из следующих подходов:

* исключение URL-адреса приложения в списке исключений браузера;
* установка доверия ко всем самозаверяющим сертификатам для `localhost`;
* добавление сертификата в список доверенных сертификатов в браузере.

Дополнительные сведения см. в рекомендациях, предоставляемых для вашего браузера и дистрибутива Linux.

::: zone-end

::: zone pivot="macos"

1. Установите [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.

1. На боковой панели выберите **Интернет и консоль** > **Приложение**.

   Для работы с Blazor WebAssembly выберите шаблон **приложения Blazor WebAssembly** . Для работы с Blazor Server выберите шаблон **приложения Blazor Server** . Выберите **Далее**.

   Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* (автономной и размещенной) и *Blazor Server* , см. в статье <xref:blazor/hosting-models>.

1. Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**. Выберите **Далее**.

1. Для работы с размещенной платформой Blazor WebAssembly установите флажок **Размещенный проект ASP.NET Core**.

1. В поле **Имя проекта** присвойте имя приложению `WebApplication1`. Нажмите кнопку **создания**.

1. Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение *без отладчика*. Запустите приложение с помощью **Выполнить** > **Начать отладку** или с помощью кнопки "Выполнить" (&#9654;), чтобы запустить приложение *с отладчиком*.

При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить. Для доверия к сертификату необходимы пароли пользователя и цепочки ключей. Дополнительные сведения об установке доверия к сертификату разработки HTTPS ASP.NET Core см. здесь: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.

При выполнении размещенного приложения Blazor WebAssembly запустите приложение из проекта **`Server`** решения.

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a>Использование Visual Studio для кроссплатформенной разработки на Blazor

[Visual Studio Code](https://code.visualstudio.com/) — это кроссплатформенная интегрированная среда разработки (IDE) с открытым кодом, которую можно использовать для разработки приложений Blazor. Используйте .NET CLI для создания приложения Blazor, которое будет разрабатываться в Visual Studio Code. Дополнительные сведения см. в [варианте этой статьи для Linux](?pivots=linux).

## <a name="blazor-template-options"></a>Параметры шаблона Blazor

Платформа Blazor предоставляет шаблоны для создания приложений с использованием каждой из двух моделей размещения Blazor. Шаблоны служат для создания проектов и решений Blazor независимо от того, какие средства выбраны для разработки на Blazor (Visual Studio, Visual Studio для Mac, Visual Studio Code или .NET CLI).

* Шаблон проекта Blazor WebAssembly: `blazorwasm`
* Шаблон проекта Blazor Server: `blazorserver`

Дополнительные сведения о моделях размещения Blazor см. в статье <xref:blazor/hosting-models>.

Параметры шаблона можно просмотреть, передав параметр help (`-h` или `--help`) в команду CLI [`dotnet new`](/dotnet/core/tools/dotnet-new) в командной оболочке.

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
