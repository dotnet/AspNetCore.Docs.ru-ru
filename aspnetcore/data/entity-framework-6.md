---
title: ASP.NET Core и Entity Framework 6
author: rick-anderson
description: Entity Framework версии 6.3 и последующих поддерживает работу с ASP.NET Core версии 3.1 и последующих.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 44211ac7fa2acc7a7a9471ef362cff02f94fa2b6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588273"
---
# <a name="aspnet-core-and-entity-framework-6"></a>ASP.NET Core и Entity Framework 6
::: moniker range=">= aspnetcore-3.0"

Автор: [Патрик Гуд](https://github.com/attrib75) (Patrick Goode)

## <a name="using-entity-framework-6-with-aspnet-core"></a>Использование Entity Framework 6 с ASP.NET Core

Для новых разработок следует использовать [Entity Framework Core](/ef/). В [загружаемом примере](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/3.xsample) используется [Entity Framework 6 (EF6)](/ef/ef6), который можно использовать для переноса существующих приложений в ASP.NET Core.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Entity Framework — конфигурация на основе кода](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Авторы: [Павел Грудзень](https://github.com/pgrudzien12) (Paweł Grudzień), [Дэмиен Понтифекс](https://github.com/DamienPontifex) (Damien Pontifex) и [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra)

В этой статье показано, как использовать платформу Entity Framework 6 в приложении ASP.NET Core.    

## <a name="overview"></a>Обзор 

Чтобы использовать платформу Entity Framework 6, проект необходимо компилировать на основе .NET Framework, поскольку Entity Framework 6 не поддерживает .NET Core. Если вам требуются какие-либо кроссплатформенные функции, необходимо выполнить обновление до [Entity Framework Core](/ef/).  

Рекомендуемый способ использования платформы Entity Framework 6 в приложении ASP.NET Core заключается в помещении контекста и классов модели EF6 в проект библиотеки классов, который предназначен для .NET Framework. Добавьте ссылку на библиотеку классов из проекта ASP.NET Core. См. пример [решения Visual Studio с проектами EF6 и ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/).    

Контекст EF6 нельзя поместить в проект ASP.NET Core, поскольку проекты .NET Core поддерживают не все функции, используемые командами EF6, например *Enable-Migrations*.    

Независимо от типа проекта, в котором размещается контекст EF6, в этом контексте работают только средства командной строки EF6. Например, команда `Scaffold-DbContext` доступна только в Entity Framework Core. Дополнительные сведения о реконструировании базы данных в модель EF6 см. в разделе <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>.    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>Использование ссылок на полную платформу и EF6 в проекте ASP.NET Core 

Проект ASP.NET Core должен быть предназначен для платформы .NET Framework и ссылаться на EF6. Например, файл *CSPROJ* вашего проекта ASP.NET Core будет иметь вид, схожий с показанным в следующем примере (показаны только соответствующие части файла).    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

При создании нового проекта используйте шаблон **Веб-приложение ASP.NET Core (.NET Framework)** .    

## <a name="handle-connection-strings"></a>Обработка строк подключения    

Средства командной строки EF6, которые используются в проекте библиотеки классов EF6, требуют наличия конструктора по умолчанию, с помощью которого они могут создавать экземпляр контекста. Тем не менее, возможно, вам потребуется задать строку подключения для использования в проекте ASP.NET Core. В этом случае конструктор контекста должен иметь параметр, в котором передается строка подключения. Ниже приведен пример.   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

Поскольку в контексте EF6 отсутствует конструктор без параметров, в проекте EF6 необходимо предоставить реализацию <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>. Средства командной строки EF6 будут находить и использовать эту реализацию для создания экземпляра контекста. Рассмотрим пример.   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

В этом примере кода реализация `IDbContextFactory` передает жестко запрограммированную строку подключения. Эта строка подключения будет использоваться средствами командной строки. В некоторых случаях требуется реализовать стратегию, в которой библиотека классов использует ту же строку подключения, что и вызывающее приложение. Например, вы можете получать значение из переменной среды в обоих проектах.   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>Настройка внедрения зависимостей в проекте ASP.NET Core  

В файле *Startup.cs* проекта Core настройте контекст EF6 для внедрения зависимостей в `ConfigureServices`. Объекты контекста EF должны иметь время существования, соответствующее запросу.   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

После этого можно получить экземпляр контекста в контроллерах посредством внедрения зависимостей. Этот код похож на тот, который вы написали для контекста EF Core:    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a>Пример приложения   

Рабочий пример приложения см. в разделе [пример решения Visual Studio](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/), который прилагается к этой статье.    

Этот пример можно создать с нуля, выполнив следующие действия в Visual Studio:    

* Создайте решение.    

* **Добавить** > **Новый проект** > **Интернет** > **Веб-приложение ASP.NET Core**    
  * В диалоговом окне для выбора шаблона проекта укажите версию API и .NET Framework в раскрывающемся списке. 

* **Добавить** > **Новый проект** > **Рабочий стол Windows** > **Библиотека классов (.NET Framework)**  

* В **консоли диспетчера пакетов** для обоих проектов выполните команду `Install-Package Entityframework`.    

* В проекте библиотеки классов создайте классы модели данных и класс контекста, а также реализацию `IDbContextFactory`.    

* В консоли диспетчера пакетов для проекта библиотеки классов выполните команды `Enable-Migrations` и `Add-Migration Initial`. Если проект ASP.NET Core настроен как запускаемый, добавьте `-StartupProjectName EF6` в эти команды. 

* В проекте Core добавьте ссылку на проект библиотеки классов.    

* В проекте Core в файле *Startup.cs* зарегистрируйте контекст для внедрения зависимостей.    

* В проекте Core в файле *appsettings.json* добавьте строку подключения.  

* В проекте Core добавьте контроллер и представление (или представления), чтобы проверить возможность чтения и записи данных. (Обратите внимание, что функция формирования шаблонов ASP.NET Core MVC не будет работать с контекстом EF6, на который указывает ссылка из библиотеки классов.)

::: moniker-end
