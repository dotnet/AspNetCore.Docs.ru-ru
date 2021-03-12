---
title: Разработка приложений ASP.NET Core с использованием наблюдателя файлов
author: rick-anderson
description: В этом руководстве показано, как установить и использовать наблюдатель за файлами .NET Core CLI (dotnet watch) в приложении ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
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
uid: tutorials/dotnet-watch
ms.openlocfilehash: 84cae3b3babe28c2ebf6dba50023b020112d1bb3
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587584"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Разработка приложений ASP.NET Core с использованием наблюдателя файлов

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Виктор Хурдугачи](https://twitter.com/victorhurdugaci) (Victor Hurdugaci)

`dotnet watch` — это средство, которое запускает команду [.NET Core CLI](/dotnet/core/tools) при изменении исходных файлов. Например, в результате изменения файла может выполняться компиляция, тестирование или развертывание.

В этом руководстве используется уже существующее приложение веб-API с двумя конечными точками, одна из которых возвращает сумму, а другая — произведение. В методе произведения есть ошибка, которая исправлена в этом руководстве.

Скачайте [образец приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/dotnet-watch/sample). Он содержит два проекта: *WebApp* (веб-API ASP.NET Core) и *WebAppTests* (модульные тесты для веб-API).

В командной оболочке перейдите в папку *WebApp*. Выполните следующую команду:

```dotnetcli
dotnet run
```

> [!NOTE]
> Можно использовать `dotnet run --project <PROJECT>`, чтобы указать проект для запуска. Например, при выполнении `dotnet run --project WebApp` из корневой папки примера приложения также будет запущен проект *WebApp*.

В выходных данных в консоли будут показаны аналогичные следующим сообщения, которые свидетельствуют о том, что приложение выполняется и ожидает запросов:

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

В браузере перейдите на адрес `http://localhost:<port number>/api/math/sum?a=4&b=5`. Должен появиться результат `9`.

Перейдите к API продукта (`http://localhost:<port number>/api/math/product?a=4&b=5`). Он возвращает `9`, а не `20`, как ожидалось. Эта проблема устраняется далее в этом руководстве.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>Добавление `dotnet watch` в проект

Средство наблюдения за файлами `dotnet watch` входит в версию 2.1.300 пакета SDK для .NET Core. Выполните следующие действия при использовании более ранней версии пакета SDK для .NET Core.

1. Добавьте ссылку на пакет `Microsoft.DotNet.Watcher.Tools` в *CSPROJ*-файл:

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. Установите пакет `Microsoft.DotNet.Watcher.Tools`, запустив следующую команду:

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Запуск команд .NET Core CLI с помощью `dotnet watch`

Любую [команду .NET Core CLI](/dotnet/core/tools#cli-commands) можно запустить с `dotnet watch`. Пример:

| Команда | Команда с контрольным значением |
| ---- | ----- |
| dotnet run | dotnet watch run |
| dotnet run -f netcoreapp3.1 | dotnet watch run -f netcoreapp3.1 |
| dotnet run -f netcoreapp3.1 -- --arg1 | dotnet watch run -f netcoreapp3.1 -- --arg1 |
| dotnet test | dotnet watch test |

Запустите `dotnet watch run` в папке *WebApp*. В выходных данных консоли будет указано, что запущен `watch`.

::: moniker range=">= aspnetcore-5.0"
При выполнении `dotnet watch run` в веб-приложении запускается браузер с переходом по URL-адресу приложения, когда оно будет готово. Для этого `dotnet watch` считывает выходные данные из консоли приложения и ожидает, когда <xref:Microsoft.AspNetCore.WebHost> выведет сообщение о готовности.

`dotnet watch` обновляет содержимое в браузере при обнаружении изменений в отслеживаемых файлах. Для этого команда watch вставляет в приложение ПО промежуточного слоя, которое изменяет ответы HTML, создаваемые приложением. ПО промежуточного слоя добавляет на страницу блок скрипта JavaScript, позволяющий `dotnet watch` давать команду на обновление содержимого в браузере. В настоящее время изменения в любых отслеживаемых файлах, включая статическое содержимое, такое как файлы *HTML* и *CSS*, приводят к повторной сборке приложения.

`dotnet watch`:

* По умолчанию отслеживаются только файлы, которые влияют на сборку.
* Дополнительные отслеживаемые файлы (указанные в конфигурации) по-прежнему приводят к сборке.

Дополнительные сведения о конфигурации см. в разделе [Конфигурация dotnet-watch](#dotnet-watch-configuration) этой документации.

::: moniker-end

> [!NOTE]
> Можно использовать `dotnet watch --project <PROJECT>`, чтобы указать проект для наблюдения. Например, при выполнении `dotnet watch --project WebApp run` из корневой папки примера приложения также будет запущен проект *WebApp*, в том числе для наблюдения.

## <a name="make-changes-with-dotnet-watch"></a>Изменения с помощью `dotnet watch`

Убедитесь, что `dotnet watch` выполняется.

Исправьте ошибку в методе `Product` для *MathController.cs* так, чтобы он возвращал произведение, а не сумму чисел.

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Сохраните файл. В выходных данных консоли будет показано, что `dotnet watch` обнаружил изменение файла и перезапустил приложение.

Убедитесь, что `http://localhost:<port number>/api/math/product?a=4&b=5` возвращает правильный результат.

## <a name="run-tests-using-dotnet-watch"></a>Тестирование с помощью `dotnet watch`

1. Снова измените метод `Product` для *MathController.cs* так, чтобы он возвращал сумму чисел. Сохраните файл.
1. В командной оболочке перейдите в папку *WebAppTests*.
1. Запустите [dotnet restore](/dotnet/core/tools/dotnet-restore).
1. Запустите `dotnet watch test`. В выходных данных будет указано, что проверка не пройдена и наблюдатель ожидает изменений в файле:

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Исправьте код метода `Product` так, чтобы он возвращал произведение. Сохраните файл.

`dotnet watch` обнаруживает изменения в файле и повторно запускает тесты. В выходных данных консоли будет указано, что проверка пройдена.

## <a name="customize-files-list-to-watch"></a>Настройка списка файлов для наблюдения

По умолчанию `dotnet-watch` отслеживает все файлы, соответствующие следующим стандартным маскам:

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

Можно добавить в список наблюдения еще элементы, отредактировав файл *.csproj*. Элементы можно указывать по отдельности или с помощью стандартной маски.

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>Удаление файлов из списка наблюдения

Можно настроить `dotnet-watch` так, чтобы игнорировать параметры по умолчанию. Чтобы пропускать определенные файлы, добавьте атрибут `Watch="false"` в определение элемента в файле *.csproj*:

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a>Пользовательское наблюдение за проектами

`dotnet-watch` используется не только с проектами C#. Создайте пользовательское наблюдение за проектами для различных ситуаций. Вы можете использовать следующий макет проекта:

* **test/**
  * *UnitTests/UnitTests.csproj*
  * *IntegrationTests/IntegrationTests.csproj*

Если вы хотите наблюдать за обоими проектами, создайте пользовательский файл проекта, настроенный для наблюдения за обоими проектами:

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

Чтобы запустить наблюдение за файлами для обоих проектов, внесите изменения в папку *test*. Выполните следующую команду:

```dotnetcli
dotnet watch msbuild /t:Test
```

VSTest выполняется при изменении любого файла в любом из тестовых проектов.

## <a name="dotnet-watch-configuration"></a>Конфигурация dotnet-watch

Некоторые параметры конфигурации можно передавать в `dotnet watch` с помощью переменных среды. Доступные переменные:

| Параметр  | Описание |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | Если задано значение 1 или true, `dotnet watch` использует опрашивающий наблюдатель за файлами вместо `FileSystemWatcher` в CoreFx. Используется при отслеживании файлов в сетевых папках или подключенных томах Docker.                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | По умолчанию `dotnet watch` оптимизирует сборку, исключая определенные операции, такие как восстановление или повторная оценка набора отслеживаемых файлов при каждом изменении. Если задано значение 1 или true, эти оптимизации отключены. |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | `dotnet watch run` пытается запускать веб-приложения в браузерах с использованием параметра `launchBrowser`, настроенного в файле *launchSettings.json*. Если задано значение 1 или true, это поведение подавляется. |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | `dotnet watch run` пытается обновлять содержимое в браузерах при обнаружении изменений в файлах. Если задано значение 1 или true, это поведение подавляется. Это поведение также подавляется, если задан параметр `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`. |
