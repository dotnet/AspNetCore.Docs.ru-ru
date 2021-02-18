---
title: Настройка компоновщика для ASP.NET Core Blazor
author: guardrex
description: Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.
monikerRange: '>= aspnetcore-3.1 < aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: c720747983da4ef6997d95d77c3f5305cfd7d3c0
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279920"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>Настройка компоновщика для ASP.NET Core Blazor

Blazor WebAssembly выполняет компоновку [промежуточного языка (IL)](/dotnet/standard/managed-code#intermediate-language--execution) во время сборки, чтобы затем удалить ненужный IL из выходных сборок приложения. Компоновщик отключен при сборке в конфигурации отладки. Для включения компоновщика приложения должны быть построены в конфигурации выпуска. Мы рекомендуем создавать выпуск при развертывании приложений Blazor WebAssembly. 

Компоновка приложения оптимизируется в зависимости от размера, но это может иметь негативные последствия. Приложения, использующие отражение или связанные динамические функции, могут прерываться при усечении, так как компоновщик не знает об этом динамическом поведении и не может определить, какие типы необходимы для отражения во время выполнения. Чтобы обрезать такие приложения, компоновщик должен быть уведомлен о любых типах, необходимых для отражения в коде и в пакетах или платформах, от которых зависит приложение.

Чтобы обеспечить правильную работу обрезанного приложения после его развертывания, важно часто тестировать сборки выпуска приложения при разработке.

Компоновку приложений Blazor можно настроить с помощью следующих функций MSBuild:

* настройка компоновки глобально с помощью [свойства MSBuild](#control-linking-with-an-msbuild-property);
* управлять компоновкой каждой сборки с помощью [файла конфигурации](#control-linking-with-a-configuration-file).

## <a name="control-linking-with-an-msbuild-property"></a>Управление компоновкой с помощью свойства MSBuild

Компоновка включается, когда приложение собирается в конфигурации `Release`. Чтобы изменить это поведение, настройте свойство MSBuild `BlazorWebAssemblyEnableLinking` в файле проекта:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Управление компоновкой с помощью файла конфигурации

Чтобы управлять компоновкой каждой сборки, нужно предоставить XML-файл конфигурации и указать его как элемент MSBuild в файле проекта.

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

`LinkerConfig.xml`:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

Дополнительные сведения см. в разделе [Форматы данных (репозиторий GitHub для mono/компоновщика)](https://github.com/mono/linker/blob/master/docs/data-formats.md).

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Добавление файла конфигурации компоновщика XML в библиотеку

Чтобы настроить компоновщик для определенной библиотеки, добавьте файл конфигурации компоновщика XML в библиотеку в качестве внедренного ресурса. Имя внедренного ресурса должно совпадать с именем сборки.

В следующем примере файл `LinkerConfig.xml` указан как внедренный ресурс, имя которого совпадает с именем сборки библиотеки.

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Настройка компоновщика для интернационализации

По умолчанию конфигурация компоновщика Blazor для приложений Blazor WebAssembly исключает сведения об интернационализации, кроме явно запрошенных языковых стандартов. Удаление этих сборок уменьшает размер приложения.

Чтобы указать, какие сборки I18N необходимо оставить, задайте свойство MSBuild `<BlazorWebAssemblyI18NAssemblies>` в файле проекта:

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| Значение региона     | Сборка для одного региона    |
| ---------------- | ----------------------- |
| `all`            | Включены все сборки |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| `none` (по умолчанию) | None                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

Для разделения нескольких значений используйте запятую (например, `mideast,west`).

Дополнительные сведения см. на странице [I18N: библиотека платформы интернационализации Pnetlib (репозиторий mono/mono GitHub)](https://github.com/mono/mono/tree/master/mcs/class/I18N).

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>
