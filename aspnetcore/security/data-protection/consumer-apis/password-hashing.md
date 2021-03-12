---
title: Хэширование паролей в ASP.NET Core
author: rick-anderson
description: Узнайте, как хэшировать пароли с помощью ASP.NET Core интерфейсов API защиты данных.
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
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: b88abd076febae95f8990bcd730ce0d4517e92d2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587766"
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="f1b2e-103">Хэширование паролей в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f1b2e-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="f1b2e-104">База кода защиты данных включает пакет *Microsoft. AspNetCore. Cryptography. KeyDerivation* , который содержит функции формирования ключа шифрования.</span><span class="sxs-lookup"><span data-stu-id="f1b2e-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="f1b2e-105">Этот пакет является автономным компонентом и не зависит от остальной части системы защиты данных.</span><span class="sxs-lookup"><span data-stu-id="f1b2e-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="f1b2e-106">Его можно использовать полностью независимо друг от друга.</span><span class="sxs-lookup"><span data-stu-id="f1b2e-106">It can be used completely independently.</span></span> <span data-ttu-id="f1b2e-107">Для удобства источник существует рядом с базой кода защиты данных.</span><span class="sxs-lookup"><span data-stu-id="f1b2e-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="f1b2e-108">В настоящее время пакет содержит метод `KeyDerivation.Pbkdf2` , позволяющий хэшировать пароль с помощью [алгоритма PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2).</span><span class="sxs-lookup"><span data-stu-id="f1b2e-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="f1b2e-109">Этот API очень похож на существующий [тип Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes)платформа .NET Framework, но есть три важных различия:</span><span class="sxs-lookup"><span data-stu-id="f1b2e-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="f1b2e-110">`KeyDerivation.Pbkdf2`Метод поддерживает использование нескольких прфс (в настоящее время `HMACSHA1` , `HMACSHA256` и `HMACSHA512` ), в то время как `Rfc2898DeriveBytes` тип поддерживает только `HMACSHA1` .</span><span class="sxs-lookup"><span data-stu-id="f1b2e-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="f1b2e-111">`KeyDerivation.Pbkdf2`Метод обнаруживает текущую операционную систему и пытается выбрать наиболее оптимизированную реализацию подпрограммы, предоставляя гораздо более высокую производительность в некоторых случаях.</span><span class="sxs-lookup"><span data-stu-id="f1b2e-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="f1b2e-112">(В Windows 8 она предлагает примерно 10 раз пропускную способность `Rfc2898DeriveBytes` .)</span><span class="sxs-lookup"><span data-stu-id="f1b2e-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="f1b2e-113">`KeyDerivation.Pbkdf2`Метод требует, чтобы вызывающий объект указал все параметры (соль, PRF и число итераций).</span><span class="sxs-lookup"><span data-stu-id="f1b2e-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="f1b2e-114">`Rfc2898DeriveBytes`Тип предоставляет значения по умолчанию для этих параметров.</span><span class="sxs-lookup"><span data-stu-id="f1b2e-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="f1b2e-115">Ознакомьтесь с типом [исходного кода](https://github.com/dotnet/AspNetCore/blob/main/src/Identity/Extensions.Core/src/PasswordHasher.cs) для ASP.NET Core Identity `PasswordHasher` реального варианта использования.</span><span class="sxs-lookup"><span data-stu-id="f1b2e-115">See the [source code](https://github.com/dotnet/AspNetCore/blob/main/src/Identity/Extensions.Core/src/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
