---
title: Вспомогательная функция тега среды в ASP.NET Core
author: pkellner
description: Определенная в ASP.NET Core вспомогательная функция тега среды, включая все свойства
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: d63364b0c052ba7f9e745e1ad829b8d1ca9122d2
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253128"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Вспомогательная функция тега среды в ASP.NET Core

Авторы: [Питер Кельнер (Peter Kellner)](https://peterkellner.net) и [Хишам Бин Атея](https://twitter.com/hishambinateya) (Hisham Bin Ateya)

Вспомогательная функция тега среды условно визуализирует свое содержимое в соответствии с текущей [средой размещения](xref:fundamentals/environments). Единственный атрибут вспомогательной функции тега среды, `names`, — это разделенный запятыми список имен сред. Если одно из указанных имен среды соответствует текущей среде, включенное содержимое подготавливается к просмотру.

Общие сведения о вспомогательных функциях тегов см. здесь: <xref:mvc/views/tag-helpers/intro>.

## <a name="environment-tag-helper-attributes"></a>Атрибуты вспомогательной функции тега среды

### <a name="names"></a>имена

`names` принимает одно имя среды размещения или список разделенных запятыми имен сред размещения, которые запускают отрисовку включенного в функцию содержимого.

Значения среды сравниваются с текущим значением, возвращенным методом [ивебхостенвиронмент. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*). При сравнении регистр не учитывается.

В приведенном ниже примере используется вспомогательная функция тега среды. Содержимое подготавливается к просмотру в том случае, если среда размещения является промежуточной или рабочей:

```cshtml
<environment names="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>Атрибуты include и exclude

`include`& `exclude` элемент управления атрибутами подготовит содержимое, основанное на включенных или исключенных именах среды размещения.

### <a name="include"></a>include

Свойство `include` ведет себя так же для атрибута `names`. Среда, указанная в `include` значении атрибута, должна соответствовать среде размещения приложения ([Ивебхостенвиронмент. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) для отрисовки содержимого `<environment>` тега.

```cshtml
<environment include="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>исключение

В отличие от атрибута `include`, содержимое тега `<environment>` отрисовывается, когда среда размещения не соответствует среде, указанной в значении атрибута `exclude`.

```cshtml
<environment exclude="Development">
    <strong>IWebHostEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/environments>
