---
title: Перенос конфигурации в ASP.NET Core
author: ardalis
description: Узнайте, как перенести конфигурацию из проекта ASP.NET MVC в проект ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
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
uid: migration/configuration
ms.openlocfilehash: c3957bf45dddcead24f7bb0f2702bf1a08950bdd
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587948"
---
# <a name="migrate-configuration-to-aspnet-core"></a>Перенос конфигурации в ASP.NET Core

Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith) и [Скотт Эдди](https://scottaddie.com) (Scott Addie)

В предыдущей статье мы начали [Перенос проекта ASP.NET MVC на ASP.NET Core MVC](xref:migration/mvc). В этой статье мы выполним миграцию конфигурации.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/configuration/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>Настройка конфигурации

ASP.NET Core больше не использует файлы *Global. asax* и *web.config* , которые использовались в предыдущих версиях ASP.NET. В более ранних версиях ASP.NET логика запуска приложения была помещена в `Application_StartUp` метод в *Global. asax*. Позже в ASP.NET MVC файл *Startup.CS* был добавлен в корень проекта. и был вызван при запуске приложения. ASP.NET Core полностью приняла этот подход, поместив всю логику запуска в файл *Startup.CS* .

Файл *web.config* также был заменен в ASP.NET Core. Теперь конфигурацию можно настроить в рамках процедуры запуска приложения, описанной в *Startup.CS*. Конфигурация по-прежнему может использовать XML-файлы, но обычно ASP.NET Core проекты помещают значения конфигурации в файл в формате JSON, например *appsettings.json* . Система настройки ASP.NET Core также может легко получить доступ к переменным среды, что может обеспечить [более безопасное и надежное расположение](xref:security/app-secrets) для значений, зависящих от среды. Это особенно справедливо для таких секретов, как строки подключения и ключи API, которые не должны быть возвращены в систему управления версиями. Дополнительные сведения о настройке в ASP.NET Core см. в разделе [Конфигурация](xref:fundamentals/configuration/index) .

В этой статье мы начинаем с частично перенесенного ASP.NET Core проекта из [предыдущей статьи](xref:migration/mvc). Чтобы настроить конфигурацию, добавьте следующий конструктор и свойство в файл *Startup.CS* , расположенный в корне проекта:

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

Обратите внимание, что на этом этапе файл *Startup.CS* не будет компилироваться, так как по-прежнему нужно добавить следующую `using` инструкцию:

```csharp
using Microsoft.Extensions.Configuration;
```

Добавьте *appsettings.json* файл в корень проекта, используя соответствующий шаблон элемента:

![Добавление формата AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Перенос параметров конфигурации из web.config

Наш проект ASP.NET MVC включал необходимую строку подключения к базе данных в *web.config* в `<connectionStrings>` элементе. В нашем ASP.NET Core проекте мы будем хранить эти сведения в *appsettings.json* файле. Откройте *appsettings.json* и обратите внимание, что в нем уже есть следующее:

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

В выделенной выше строке измените имя базы данных с **_CHANGE_ME** на имя базы данных.

## <a name="summary"></a>Итоги

ASP.NET Core помещает всю логику запуска приложения в один файл, в котором можно определить и настроить необходимые службы и зависимости. Он заменяет файл *web.config* на гибкую функцию настройки, которая может использовать различные форматы файлов, такие как JSON, а также переменные среды.
