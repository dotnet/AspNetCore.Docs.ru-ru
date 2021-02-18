---
title: Поддерживаемые платформы ASP.NET Core Blazor
author: guardrex
description: Сведения о поддерживаемых платформах для ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 948c3e3f66da4727731b37491ae5c5470cfb36fe
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280724"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>Поддерживаемые платформы ASP.NET Core Blazor

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly и Blazor Server поддерживаются в браузерах, приведенных в таблице ниже.

| Браузер                          | Версия         |
| -------------------------------- | --------------- |
| Apple Safari, включая iOS      | Текущий&dagger; |
| Google Chrome, включая Android | Текущий&dagger; |
| Microsoft Edge                   | Текущий&dagger; |
| Mozilla Firefox;                  | Текущий&dagger; |  

&dagger;*Текущий* означает последнюю версию браузера.  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| Браузер                          | Версия               |
| -------------------------------- | --------------------- |
| Apple Safari, включая iOS      | Текущий&dagger;       |
| Google Chrome, включая Android | Текущий&dagger;       |
| Microsoft Edge                   | Текущий&dagger;       |
| Microsoft Internet Explorer      | Не поддерживается&Dagger; |
| Mozilla Firefox;                  | Текущий&dagger;       |  

&dagger;*Текущий* означает последнюю версию браузера.  
&Dagger;Microsoft Internet Explorer не поддерживает [WebAssembly](https://webassembly.org).

## Blazor Server

| Браузер                          | Версия         |
| -------------------------------- | --------------- |
| Apple Safari, включая iOS      | Текущий&dagger; |
| Google Chrome, включая Android | Текущий&dagger; |
| Microsoft Edge                   | Текущий&dagger; |
| Microsoft Internet Explorer      | 11&Dagger;      |
| Mozilla Firefox;                  | Текущий&dagger; |

&dagger;*Текущий* означает последнюю версию браузера.  
&Dagger;Требуются дополнительные заполнения. Например, обещания можно добавить с помощью пакета [`Polyfill.io`](https://polyfill.io/v3/).

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
