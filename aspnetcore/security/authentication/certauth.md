---
title: Настройка проверки подлинности сертификата в ASP.NET Core
author: blowdart
description: Узнайте, как настроить проверку подлинности сертификата в ASP.NET Core для IIS и HTTP.sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
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
uid: security/authentication/certauth
ms.openlocfilehash: c862bc8bff6c4cc80696d92067e814889d6e7782
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217535"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="fd404-103">Настройка проверки подлинности сертификата в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fd404-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="fd404-104">`Microsoft.AspNetCore.Authentication.Certificate` содержит реализацию, похожую на [проверку подлинности с помощью сертификата](https://tools.ietf.org/html/rfc5246#section-7.4.4) для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fd404-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="fd404-105">Проверка подлинности по сертификату происходит на уровне TLS, задолго до его попадания в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fd404-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="fd404-106">Точнее, это обработчик проверки подлинности, который проверяет сертификат, а затем предоставляет событие, в котором можно разрешить этот сертификат в `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="fd404-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="fd404-107">[Настройте сервер](#configure-your-server-to-require-certificates) для проверки подлинности на основе сертификата, как IIS, Kestrel, веб-приложения Azure или любое другое приложение, которое вы используете.</span><span class="sxs-lookup"><span data-stu-id="fd404-107">[Configure your server](#configure-your-server-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="fd404-108">Сценарии прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="fd404-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="fd404-109">Проверка подлинности с помощью сертификата — это сценарий с отслеживанием состояния, который в основном используется, когда прокси или балансировщик нагрузки не обрабатывает трафик между клиентами и серверами.</span><span class="sxs-lookup"><span data-stu-id="fd404-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="fd404-110">Если используется прокси-сервер или балансировщик нагрузки, проверка подлинности сертификата работает только в том случае, если прокси-сервер или балансировщик нагрузки:</span><span class="sxs-lookup"><span data-stu-id="fd404-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="fd404-111">Обрабатывает проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="fd404-111">Handles the authentication.</span></span>
* <span data-ttu-id="fd404-112">Передает сведения о проверке подлинности пользователя в приложение (например, в заголовке запроса), которое действует на данные проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="fd404-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="fd404-113">Альтернативой для проверки подлинности на сертификат в средах, где используются прокси-серверы и подсистемы балансировки нагрузки, являются Active Directory Федеративные службы (ADFS) с OpenID Connect Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="fd404-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="fd404-114">Приступая к работе</span><span class="sxs-lookup"><span data-stu-id="fd404-114">Get started</span></span>

<span data-ttu-id="fd404-115">Получите HTTPS сертификат, примените его и [Настройте сервер](#configure-your-server-to-require-certificates) для использования сертификатов.</span><span class="sxs-lookup"><span data-stu-id="fd404-115">Acquire an HTTPS certificate, apply it, and [configure your server](#configure-your-server-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="fd404-116">В веб-приложении добавьте ссылку на пакет [Microsoft. AspNetCore. Authentication. Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) .</span><span class="sxs-lookup"><span data-stu-id="fd404-116">In your web app, add a reference to the [Microsoft.AspNetCore.Authentication.Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) package.</span></span> <span data-ttu-id="fd404-117">Затем в `Startup.ConfigureServices` методе вызовите `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` с вашими параметрами, предоставив делегат для, `OnCertificateValidated` чтобы выполнить дополнительную проверку сертификата клиента, отправляемого с запросами.</span><span class="sxs-lookup"><span data-stu-id="fd404-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="fd404-118">Включите эту информацию в `ClaimsPrincipal` и установите ее в `context.Principal` свойстве.</span><span class="sxs-lookup"><span data-stu-id="fd404-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="fd404-119">Если проверка подлинности завершается неудачно, этот обработчик возвращает `403 (Forbidden)` ответ, а не `401 (Unauthorized)` , как вы можете ожидать.</span><span class="sxs-lookup"><span data-stu-id="fd404-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="fd404-120">Причина заключается в том, что проверка подлинности должна выполняться во время первоначального TLS-подключения.</span><span class="sxs-lookup"><span data-stu-id="fd404-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="fd404-121">К моменту, когда он достигает обработчика, он слишком поздно.</span><span class="sxs-lookup"><span data-stu-id="fd404-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="fd404-122">Невозможно обновить подключение между анонимным подключением и сертификатом.</span><span class="sxs-lookup"><span data-stu-id="fd404-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="fd404-123">Кроме того `app.UseAuthentication();` , добавьте в `Startup.Configure` метод.</span><span class="sxs-lookup"><span data-stu-id="fd404-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="fd404-124">В противном случае `HttpContext.User` не будет задано значение, `ClaimsPrincipal` созданное на основе сертификата.</span><span class="sxs-lookup"><span data-stu-id="fd404-124">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="fd404-125">Например:</span><span class="sxs-lookup"><span data-stu-id="fd404-125">For example:</span></span>

::: moniker range=">= aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate()
        // Adding an ICertificateValidationCache results in certificate auth caching the results.
        // The default implementation uses a memory cache.
        .AddCertificateCache();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

<span data-ttu-id="fd404-126">В предыдущем примере показан способ добавления проверки подлинности с помощью сертификата по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="fd404-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="fd404-127">Обработчик конструирует субъект-пользователя, используя общие свойства сертификата.</span><span class="sxs-lookup"><span data-stu-id="fd404-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="fd404-128">Настройка проверки сертификата</span><span class="sxs-lookup"><span data-stu-id="fd404-128">Configure certificate validation</span></span>

<span data-ttu-id="fd404-129">`CertificateAuthenticationOptions`Обработчик содержит некоторые встроенные проверки, которые являются минимальными проверками, которые следует выполнить с сертификатом.</span><span class="sxs-lookup"><span data-stu-id="fd404-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="fd404-130">Каждый из этих параметров включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="fd404-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="fd404-131">Алловедцертификатетипес = цепочка, Селфсигнед или все (цепочка | Селфсигнед)</span><span class="sxs-lookup"><span data-stu-id="fd404-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="fd404-132">Значение по умолчанию — `CertificateTypes.Chained`.</span><span class="sxs-lookup"><span data-stu-id="fd404-132">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="fd404-133">Эта проверка подтверждает, что разрешен только соответствующий тип сертификата.</span><span class="sxs-lookup"><span data-stu-id="fd404-133">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="fd404-134">Если приложение использует самозаверяющие сертификаты, для этого параметра необходимо задать значение `CertificateTypes.All` или `CertificateTypes.SelfSigned` .</span><span class="sxs-lookup"><span data-stu-id="fd404-134">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="fd404-135">валидатецертификатеусе</span><span class="sxs-lookup"><span data-stu-id="fd404-135">ValidateCertificateUse</span></span>

<span data-ttu-id="fd404-136">Значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="fd404-136">Default value: `true`</span></span>

<span data-ttu-id="fd404-137">Эта проверка проверяет, что сертификат, предоставленный клиентом, имеет расширенное использование ключа проверки подлинности клиента (EKU) или вообще не содержит EKU.</span><span class="sxs-lookup"><span data-stu-id="fd404-137">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="fd404-138">Как говорится в спецификации, если EKU не указано, все EKU считаются допустимыми.</span><span class="sxs-lookup"><span data-stu-id="fd404-138">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="fd404-139">валидатевалидитипериод</span><span class="sxs-lookup"><span data-stu-id="fd404-139">ValidateValidityPeriod</span></span>

<span data-ttu-id="fd404-140">Значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="fd404-140">Default value: `true`</span></span>

<span data-ttu-id="fd404-141">Эта проверка проверяет, что сертификат находится в пределах срока действия.</span><span class="sxs-lookup"><span data-stu-id="fd404-141">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="fd404-142">В каждом запросе обработчик гарантирует, что сертификат, который был действителен, когда он был предоставлен, не истечет в течение текущего сеанса.</span><span class="sxs-lookup"><span data-stu-id="fd404-142">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="fd404-143">ревокатионфлаг</span><span class="sxs-lookup"><span data-stu-id="fd404-143">RevocationFlag</span></span>

<span data-ttu-id="fd404-144">Значение по умолчанию — `X509RevocationFlag.ExcludeRoot`.</span><span class="sxs-lookup"><span data-stu-id="fd404-144">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="fd404-145">Флаг, указывающий, какие сертификаты в цепочке проверяются на отзыв.</span><span class="sxs-lookup"><span data-stu-id="fd404-145">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="fd404-146">Проверки отзыва выполняются только в том случае, если сертификат связан с корневым сертификатом.</span><span class="sxs-lookup"><span data-stu-id="fd404-146">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="fd404-147">ревокатионмоде</span><span class="sxs-lookup"><span data-stu-id="fd404-147">RevocationMode</span></span>

<span data-ttu-id="fd404-148">Значение по умолчанию — `X509RevocationMode.Online`.</span><span class="sxs-lookup"><span data-stu-id="fd404-148">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="fd404-149">Флаг, указывающий, как выполняются проверки отзыва.</span><span class="sxs-lookup"><span data-stu-id="fd404-149">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="fd404-150">Указание проверки в сети может привести к длительной задержке при обращении к центру сертификации.</span><span class="sxs-lookup"><span data-stu-id="fd404-150">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="fd404-151">Проверки отзыва выполняются только в том случае, если сертификат связан с корневым сертификатом.</span><span class="sxs-lookup"><span data-stu-id="fd404-151">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="fd404-152">Можно ли настроить в приложении требование сертификата только для определенных путей?</span><span class="sxs-lookup"><span data-stu-id="fd404-152">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="fd404-153">Это невозможно.</span><span class="sxs-lookup"><span data-stu-id="fd404-153">This isn't possible.</span></span> <span data-ttu-id="fd404-154">Помните, что обмен сертификатами выполняется в начале диалога HTTPS, он выполняется сервером до получения первого запроса на это подключение, поэтому невозможно ограничить область на основе каких-либо полей запроса.</span><span class="sxs-lookup"><span data-stu-id="fd404-154">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="fd404-155">События обработчика</span><span class="sxs-lookup"><span data-stu-id="fd404-155">Handler events</span></span>

<span data-ttu-id="fd404-156">Обработчик имеет два события:</span><span class="sxs-lookup"><span data-stu-id="fd404-156">The handler has two events:</span></span>

* <span data-ttu-id="fd404-157">`OnAuthenticationFailed`: Вызывается, если исключение происходит во время проверки подлинности и позволяет реагировать.</span><span class="sxs-lookup"><span data-stu-id="fd404-157">`OnAuthenticationFailed`: Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="fd404-158">`OnCertificateValidated`: Вызывается после проверки сертификата, прошел проверку и создается участник по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="fd404-158">`OnCertificateValidated`: Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="fd404-159">Это событие позволяет выполнять собственную проверку и дополнять или заменять субъект.</span><span class="sxs-lookup"><span data-stu-id="fd404-159">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="fd404-160">Ниже приведены примеры.</span><span class="sxs-lookup"><span data-stu-id="fd404-160">For examples include:</span></span>
  * <span data-ttu-id="fd404-161">Определение, известен ли сертификат службам.</span><span class="sxs-lookup"><span data-stu-id="fd404-161">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="fd404-162">Создание собственного участника.</span><span class="sxs-lookup"><span data-stu-id="fd404-162">Constructing your own principal.</span></span> <span data-ttu-id="fd404-163">Рассмотрим следующий пример в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fd404-163">Consider the following example in `Startup.ConfigureServices`:</span></span>

    ```csharp
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate(options =>
        {
            options.Events = new CertificateAuthenticationEvents
            {
                OnCertificateValidated = context =>
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject,
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(ClaimTypes.Name,
                            context.ClientCertificate.Subject,
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };
    
                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
    
                    return Task.CompletedTask;
                }
            };
        });
    ```

<span data-ttu-id="fd404-164">Если входящий сертификат не соответствует дополнительной проверке, позвоните `context.Fail("failure reason")` по причине сбоя.</span><span class="sxs-lookup"><span data-stu-id="fd404-164">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="fd404-165">Для реальной функциональности вам, вероятно, потребуется вызвать службу, зарегистрированную в внедрении зависимостей, которая подключается к базе данных или другому хранилищу пользователей.</span><span class="sxs-lookup"><span data-stu-id="fd404-165">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="fd404-166">Получите доступ к службе с помощью контекста, переданного в делегат.</span><span class="sxs-lookup"><span data-stu-id="fd404-166">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="fd404-167">Рассмотрим следующий пример в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fd404-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetRequiredService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="fd404-168">По сути, проверка сертификата является проблемой авторизации.</span><span class="sxs-lookup"><span data-stu-id="fd404-168">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="fd404-169">Добавление проверки, например, издателя или отпечатка в политике авторизации, а не внутри `OnCertificateValidated` , вполне приемлемо.</span><span class="sxs-lookup"><span data-stu-id="fd404-169">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-server-to-require-certificates"></a><span data-ttu-id="fd404-170">Настройка сервера для использования сертификатов</span><span class="sxs-lookup"><span data-stu-id="fd404-170">Configure your server to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="fd404-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="fd404-171">Kestrel</span></span>

<span data-ttu-id="fd404-172">В *Program.CS* настройте Kestrel следующим образом:</span><span class="sxs-lookup"><span data-stu-id="fd404-172">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <span data-ttu-id="fd404-173">К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>, не будут применяться значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="fd404-173">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="fd404-174">IIS</span><span class="sxs-lookup"><span data-stu-id="fd404-174">IIS</span></span>

<span data-ttu-id="fd404-175">В диспетчере служб IIS выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="fd404-175">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="fd404-176">Выберите сайт на вкладке " **подключения** ".</span><span class="sxs-lookup"><span data-stu-id="fd404-176">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="fd404-177">Дважды щелкните параметр **Параметры SSL** в окне **просмотра функций** .</span><span class="sxs-lookup"><span data-stu-id="fd404-177">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="fd404-178">Установите флажок **требовать SSL** и установите переключатель **требуется** в разделе **Сертификаты клиента** .</span><span class="sxs-lookup"><span data-stu-id="fd404-178">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Параметры сертификата клиента в службах IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="fd404-180">Azure и настраиваемые веб-прокси</span><span class="sxs-lookup"><span data-stu-id="fd404-180">Azure and custom web proxies</span></span>

<span data-ttu-id="fd404-181">Сведения о настройке по промежуточного слоя для переадресации сертификатов см. в [документации по узлу и развертыванию](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) .</span><span class="sxs-lookup"><span data-stu-id="fd404-181">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="fd404-182">Использование проверки подлинности сертификата в веб-приложениях Azure</span><span class="sxs-lookup"><span data-stu-id="fd404-182">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="fd404-183">Для Azure Настройка пересылки не требуется.</span><span class="sxs-lookup"><span data-stu-id="fd404-183">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="fd404-184">Это уже настроено в по промежуточного слоя на пересылку сертификатов.</span><span class="sxs-lookup"><span data-stu-id="fd404-184">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="fd404-185">Для этого необходимо наличие Цертификатефорвардингмиддлеваре.</span><span class="sxs-lookup"><span data-stu-id="fd404-185">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="fd404-186">Использование проверки подлинности сертификатов в пользовательских веб-прокси</span><span class="sxs-lookup"><span data-stu-id="fd404-186">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="fd404-187">`AddCertificateForwarding`Метод используется для указания:</span><span class="sxs-lookup"><span data-stu-id="fd404-187">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="fd404-188">Имя заголовка клиента.</span><span class="sxs-lookup"><span data-stu-id="fd404-188">The client header name.</span></span>
* <span data-ttu-id="fd404-189">Способ загрузки сертификата (с помощью `HeaderConverter` Свойства).</span><span class="sxs-lookup"><span data-stu-id="fd404-189">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="fd404-190">В пользовательских веб-прокси сертификат передается в виде пользовательского заголовка запроса, например `X-SSL-CERT` .</span><span class="sxs-lookup"><span data-stu-id="fd404-190">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="fd404-191">Чтобы использовать его, Настройте переадресацию сертификатов в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fd404-191">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;

            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

<span data-ttu-id="fd404-192">`Startup.Configure`Затем метод добавляет по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="fd404-192">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="fd404-193">`UseCertificateForwarding` вызывается перед вызовами функций `UseAuthentication` и `UseAuthorization` :</span><span class="sxs-lookup"><span data-stu-id="fd404-193">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="fd404-194">Для реализации логики проверки можно использовать отдельный класс.</span><span class="sxs-lookup"><span data-stu-id="fd404-194">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="fd404-195">Так как в этом примере используется тот же самозаверяющий сертификат, убедитесь, что только ваш сертификат можно использовать.</span><span class="sxs-lookup"><span data-stu-id="fd404-195">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="fd404-196">Убедитесь, что отпечатки сертификата клиента и сертификата сервера совпадают, в противном случае любой сертификат может быть использован и будет достаточно для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="fd404-196">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="fd404-197">Он будет использоваться внутри `AddCertificate` метода.</span><span class="sxs-lookup"><span data-stu-id="fd404-197">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="fd404-198">Вы также можете проверить субъект или его издателя, если вы используете промежуточные или дочерние сертификаты.</span><span class="sxs-lookup"><span data-stu-id="fd404-198">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="fd404-199">Реализация HttpClient с помощью сертификата и HttpClientHandler</span><span class="sxs-lookup"><span data-stu-id="fd404-199">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="fd404-200">`HttpClientHandler`Можно добавить непосредственно в конструктор `HttpClient` класса.</span><span class="sxs-lookup"><span data-stu-id="fd404-200">The `HttpClientHandler` could be added directly in the constructor of the `HttpClient` class.</span></span> <span data-ttu-id="fd404-201">Будьте осторожны при создании экземпляров `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="fd404-201">Care should be taken when creating instances of the `HttpClient`.</span></span> <span data-ttu-id="fd404-202">`HttpClient`Затем будет отправлен сертификат с каждым запросом.</span><span class="sxs-lookup"><span data-stu-id="fd404-202">The `HttpClient` will then send the certificate with each request.</span></span>

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="fd404-203">Реализация HttpClient с помощью сертификата и именованного HttpClient из Ихттпклиентфактори</span><span class="sxs-lookup"><span data-stu-id="fd404-203">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="fd404-204">В следующем примере сертификат клиента добавляется в, `HttpClientHandler` используя `ClientCertificates` свойство из обработчика.</span><span class="sxs-lookup"><span data-stu-id="fd404-204">In the following example, a client certificate is added to a `HttpClientHandler` using the `ClientCertificates` property from the handler.</span></span> <span data-ttu-id="fd404-205">Затем этот обработчик можно использовать в именованном экземпляре `HttpClient` с помощью `ConfigurePrimaryHttpMessageHandler` метода.</span><span class="sxs-lookup"><span data-stu-id="fd404-205">This handler can then be used in a named instance of an `HttpClient` using the `ConfigurePrimaryHttpMessageHandler` method.</span></span> <span data-ttu-id="fd404-206">Это программа установки в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fd404-206">This is setup in `Startup.ConfigureServices`:</span></span>

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

<span data-ttu-id="fd404-207">`IHttpClientFactory`Затем можно использовать для получения именованного экземпляра с обработчиком и сертификатом.</span><span class="sxs-lookup"><span data-stu-id="fd404-207">The `IHttpClientFactory` can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="fd404-208">`CreateClient`Для получения экземпляра используется метод с именем клиента, определенного в `Startup` классе.</span><span class="sxs-lookup"><span data-stu-id="fd404-208">The `CreateClient` method with the name of the client defined in the `Startup` class is used to get the instance.</span></span> <span data-ttu-id="fd404-209">HTTP-запрос может быть отправлен с помощью клиента по мере необходимости.</span><span class="sxs-lookup"><span data-stu-id="fd404-209">The HTTP request can be sent using the client as required.</span></span>

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

<span data-ttu-id="fd404-210">Если на сервер отправляется правильный сертификат, возвращаются данные.</span><span class="sxs-lookup"><span data-stu-id="fd404-210">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="fd404-211">Если сертификат или неправильный сертификат не отправляется, возвращается код состояния HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="fd404-211">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="fd404-212">Создание сертификатов в PowerShell</span><span class="sxs-lookup"><span data-stu-id="fd404-212">Create certificates in PowerShell</span></span>

<span data-ttu-id="fd404-213">Создание сертификатов — самая сложная часть настройки этого потока.</span><span class="sxs-lookup"><span data-stu-id="fd404-213">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="fd404-214">Корневой сертификат можно создать с помощью `New-SelfSignedCertificate` командлета PowerShell.</span><span class="sxs-lookup"><span data-stu-id="fd404-214">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="fd404-215">При создании сертификата используйте надежный пароль.</span><span class="sxs-lookup"><span data-stu-id="fd404-215">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="fd404-216">Важно добавить `KeyUsageProperty` параметр и `KeyUsage` параметр, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="fd404-216">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="fd404-217">Создать корневой ЦС</span><span class="sxs-lookup"><span data-stu-id="fd404-217">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="fd404-218">`-DnsName`Значение параметра должно соответствовать целевому объекту развертывания приложения.</span><span class="sxs-lookup"><span data-stu-id="fd404-218">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="fd404-219">Например, "localhost" для разработки.</span><span class="sxs-lookup"><span data-stu-id="fd404-219">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="fd404-220">Установить в доверенном корневом каталоге</span><span class="sxs-lookup"><span data-stu-id="fd404-220">Install in the trusted root</span></span>

<span data-ttu-id="fd404-221">Корневой сертификат должен быть доверенным в системе узла.</span><span class="sxs-lookup"><span data-stu-id="fd404-221">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="fd404-222">Корневой сертификат, который не был создан центром сертификации, не будет доверенным по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="fd404-222">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="fd404-223">Следующая ссылка объясняет, как это можно сделать в Windows:</span><span class="sxs-lookup"><span data-stu-id="fd404-223">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="fd404-224">Промежуточный сертификат</span><span class="sxs-lookup"><span data-stu-id="fd404-224">Intermediate certificate</span></span>

<span data-ttu-id="fd404-225">Теперь промежуточный сертификат можно создать на основе корневого сертификата.</span><span class="sxs-lookup"><span data-stu-id="fd404-225">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="fd404-226">Это не является обязательным для всех вариантов использования, но может потребоваться создать несколько сертификатов или необходимо активировать или отключить группы сертификатов.</span><span class="sxs-lookup"><span data-stu-id="fd404-226">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="fd404-227">`TextExtension`Параметр необходим для задания длины пути в базовых ограничениях сертификата.</span><span class="sxs-lookup"><span data-stu-id="fd404-227">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="fd404-228">Затем промежуточный сертификат можно добавить в доверенный промежуточный сертификат в системе узла Windows.</span><span class="sxs-lookup"><span data-stu-id="fd404-228">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="fd404-229">Создать дочерний сертификат из промежуточного сертификата</span><span class="sxs-lookup"><span data-stu-id="fd404-229">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="fd404-230">Дочерний сертификат можно создать из промежуточного сертификата.</span><span class="sxs-lookup"><span data-stu-id="fd404-230">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="fd404-231">Это конечная сущность, и ей не нужно создавать больше дочерних сертификатов.</span><span class="sxs-lookup"><span data-stu-id="fd404-231">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="fd404-232">Создать дочерний сертификат из корневого сертификата</span><span class="sxs-lookup"><span data-stu-id="fd404-232">Create child certificate from root certificate</span></span>

<span data-ttu-id="fd404-233">Дочерний сертификат можно также создать непосредственно из корневого сертификата.</span><span class="sxs-lookup"><span data-stu-id="fd404-233">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="fd404-234">Пример корневого промежуточного сертификата — сертификат</span><span class="sxs-lookup"><span data-stu-id="fd404-234">Example root - intermediate certificate - certificate</span></span>

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

<span data-ttu-id="fd404-235">При использовании корневого, промежуточного или дочернего сертификата сертификаты можно проверять с помощью отпечатка или PublicKey по мере необходимости.</span><span class="sxs-lookup"><span data-stu-id="fd404-235">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```

<a name="occ"></a>

::: moniker range=">= aspnetcore-5.0"

## <a name="certificate-validation-caching"></a><span data-ttu-id="fd404-236">Кэширование проверки сертификатов</span><span class="sxs-lookup"><span data-stu-id="fd404-236">Certificate validation caching</span></span>

<span data-ttu-id="fd404-237">ASP.NET Core 5,0 и более поздних версий поддерживают возможность кэширования результатов проверки.</span><span class="sxs-lookup"><span data-stu-id="fd404-237">ASP.NET Core 5.0 and later versions support the ability to enable caching of validation results.</span></span> <span data-ttu-id="fd404-238">Кэширование значительно повышает производительность проверки подлинности сертификата, так как проверка является дорогостоящей операцией.</span><span class="sxs-lookup"><span data-stu-id="fd404-238">The caching dramatically improves performance of certificate authentication, as validation is an expensive operation.</span></span>

<span data-ttu-id="fd404-239">По умолчанию проверка подлинности на сертификате отключает кэширование.</span><span class="sxs-lookup"><span data-stu-id="fd404-239">By default, certificate authentication disables caching.</span></span> <span data-ttu-id="fd404-240">Чтобы включить кэширование, вызовите `AddCertificateCache` в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fd404-240">To enable caching, call `AddCertificateCache` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate()
            .AddCertificateCache(options =>
            {
                options.CacheSize = 1024;
                options.CacheEntryExpiration = TimeSpan.FromMinutes(2);
            });
}
```

<span data-ttu-id="fd404-241">Реализация кэширования по умолчанию сохраняет результаты в памяти.</span><span class="sxs-lookup"><span data-stu-id="fd404-241">The default caching implementation stores results in memory.</span></span> <span data-ttu-id="fd404-242">Вы можете предоставить собственный кэш, реализовав `ICertificateValidationCache` и зарегистрировав его с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fd404-242">You can provide your own cache by implementing `ICertificateValidationCache` and registering it with dependency injection.</span></span> <span data-ttu-id="fd404-243">Например, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.</span><span class="sxs-lookup"><span data-stu-id="fd404-243">For example, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.</span></span>

::: moniker-end

## <a name="optional-client-certificates"></a><span data-ttu-id="fd404-244">Необязательные сертификаты клиента</span><span class="sxs-lookup"><span data-stu-id="fd404-244">Optional client certificates</span></span>

<span data-ttu-id="fd404-245">В этом разделе содержатся сведения о приложениях, которые должны защищать подмножество приложения с помощью сертификата.</span><span class="sxs-lookup"><span data-stu-id="fd404-245">This section provides information for apps that must protect a subset of the app with a certificate.</span></span> <span data-ttu-id="fd404-246">Например, для Razor страницы или контроллера в приложении могут потребоваться сертификаты клиента.</span><span class="sxs-lookup"><span data-stu-id="fd404-246">For example, a Razor Page or controller in the app might require client certificates.</span></span> <span data-ttu-id="fd404-247">Это представляет проблемы в качестве сертификатов клиента:</span><span class="sxs-lookup"><span data-stu-id="fd404-247">This presents challenges as client certificates:</span></span>
  
* <span data-ttu-id="fd404-248">Являются компонентом TLS, а не компонентом HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd404-248">Are a TLS feature, not an HTTP feature.</span></span>
* <span data-ttu-id="fd404-249">Согласовываются по подключению и должны быть согласованы в начале соединения, прежде чем будут доступны все данные HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd404-249">Are negotiated per-connection and must be be negotiated at the start of the connection before any HTTP data is available.</span></span> <span data-ttu-id="fd404-250">В начале соединения известен только указание имени сервера (SNI) &dagger; .</span><span class="sxs-lookup"><span data-stu-id="fd404-250">At the start of the connection, only the Server Name Indication (SNI)&dagger; is known.</span></span> <span data-ttu-id="fd404-251">Сертификаты клиента и сервера согласовываются до первого запроса в соединении, и запросы, как правило, не могут повторно согласовываться.</span><span class="sxs-lookup"><span data-stu-id="fd404-251">The client and server certificates are negotiated prior to the first request on a connection and requests generally aren't able to renegotiate.</span></span>

<span data-ttu-id="fd404-252">Повторное согласование TLS было старым способом реализации необязательных сертификатов клиента.</span><span class="sxs-lookup"><span data-stu-id="fd404-252">TLS renegotiation was an old way to implement optional client certificates.</span></span> <span data-ttu-id="fd404-253">Это больше не рекомендуется, так как:</span><span class="sxs-lookup"><span data-stu-id="fd404-253">This is no longer recommended because:</span></span>
- <span data-ttu-id="fd404-254">В HTTP/1.1 повторное согласование во время запроса POST может привести к взаимоблокировке, когда текст запроса заполнил окно TCP и пакеты повторного согласования не могут быть получены.</span><span class="sxs-lookup"><span data-stu-id="fd404-254">In HTTP/1.1, renegotiating during a POST request could cause a deadlock where the request body filled up the TCP window and the renegotiation packets can't be received.</span></span>
- <span data-ttu-id="fd404-255">HTTP/2 [явно запрещает](https://tools.ietf.org/html/rfc7540#section-9.2.1) повторное согласование.</span><span class="sxs-lookup"><span data-stu-id="fd404-255">HTTP/2 [explicitly prohibits](https://tools.ietf.org/html/rfc7540#section-9.2.1) renegotiation.</span></span>
- <span data-ttu-id="fd404-256">Протокол TLS 1,3 [удалил](https://tools.ietf.org/html/rfc8740#section-1) поддержку повторного согласования.</span><span class="sxs-lookup"><span data-stu-id="fd404-256">TLS 1.3 has [removed](https://tools.ietf.org/html/rfc8740#section-1) support for renegotiation.</span></span>

<span data-ttu-id="fd404-257">ASP.NET Core 5 Preview 7 и более поздней версии добавляет более удобную поддержку для необязательных клиентских сертификатов.</span><span class="sxs-lookup"><span data-stu-id="fd404-257">ASP.NET Core 5 preview 7 and later adds more convenient support for optional client certificates.</span></span> <span data-ttu-id="fd404-258">Дополнительные сведения см. в [примере необязательных сертификатов](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).</span><span class="sxs-lookup"><span data-stu-id="fd404-258">For more information, see the [Optional certificates sample](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).</span></span>

<span data-ttu-id="fd404-259">Следующий подход поддерживает необязательные сертификаты клиента:</span><span class="sxs-lookup"><span data-stu-id="fd404-259">The following approach supports optional client certificates:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="fd404-260">Настройка привязки для домена и поддомена:</span><span class="sxs-lookup"><span data-stu-id="fd404-260">Set up binding for the domain and subdomain:</span></span>
  * <span data-ttu-id="fd404-261">Например, настройте привязки для `contoso.com` и `myClient.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="fd404-261">For example, set up bindings on `contoso.com` and `myClient.contoso.com`.</span></span> <span data-ttu-id="fd404-262">`contoso.com`Узлу не требуется сертификат клиента, но он `myClient.contoso.com` выполняется.</span><span class="sxs-lookup"><span data-stu-id="fd404-262">The `contoso.com` host doesn't require a client certificate but `myClient.contoso.com` does.</span></span>
  * <span data-ttu-id="fd404-263">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="fd404-263">For more information, see:</span></span>
    * <span data-ttu-id="fd404-264">[Kestrel](/fundamentals/servers/kestrel):</span><span class="sxs-lookup"><span data-stu-id="fd404-264">[Kestrel](/fundamentals/servers/kestrel):</span></span>
      * [<span data-ttu-id="fd404-265">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="fd404-265">ListenOptions.UseHttps</span></span>](xref:fundamentals/servers/kestrel/endpoints#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * <span data-ttu-id="fd404-266">Примечание. Kestrel в настоящее время не поддерживает несколько конфигураций TLS для одной привязки, вам потребуются две привязки с уникальными IP-адресами или портами.</span><span class="sxs-lookup"><span data-stu-id="fd404-266">Note Kestrel does not currently support multiple TLS configurations on one binding, you'll need two bindings with unique IPs or ports.</span></span> <span data-ttu-id="fd404-267">См. раздел https://github.com/dotnet/runtime/issues/31097.</span><span class="sxs-lookup"><span data-stu-id="fd404-267">See https://github.com/dotnet/runtime/issues/31097</span></span>
    * <span data-ttu-id="fd404-268">IIS</span><span class="sxs-lookup"><span data-stu-id="fd404-268">IIS</span></span>
      * [<span data-ttu-id="fd404-269">Размещение IIS</span><span class="sxs-lookup"><span data-stu-id="fd404-269">Hosting IIS</span></span>](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [<span data-ttu-id="fd404-270">Настройка безопасности служб IIS</span><span class="sxs-lookup"><span data-stu-id="fd404-270">Configure security on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * <span data-ttu-id="fd404-271">HTTP.sys: [Настройка Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span><span class="sxs-lookup"><span data-stu-id="fd404-271">HTTP.sys: [Configure Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="fd404-272">Настройка привязки для домена и поддомена:</span><span class="sxs-lookup"><span data-stu-id="fd404-272">Set up binding for the domain and subdomain:</span></span>
  * <span data-ttu-id="fd404-273">Например, настройте привязки для `contoso.com` и `myClient.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="fd404-273">For example, set up bindings on `contoso.com` and `myClient.contoso.com`.</span></span> <span data-ttu-id="fd404-274">`contoso.com`Узлу не требуется сертификат клиента, но он `myClient.contoso.com` выполняется.</span><span class="sxs-lookup"><span data-stu-id="fd404-274">The `contoso.com` host doesn't require a client certificate but `myClient.contoso.com` does.</span></span>
  * <span data-ttu-id="fd404-275">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="fd404-275">For more information, see:</span></span>
    * <span data-ttu-id="fd404-276">[Kestrel](/fundamentals/servers/kestrel):</span><span class="sxs-lookup"><span data-stu-id="fd404-276">[Kestrel](/fundamentals/servers/kestrel):</span></span>
      * [<span data-ttu-id="fd404-277">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="fd404-277">ListenOptions.UseHttps</span></span>](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * <span data-ttu-id="fd404-278">Примечание. Kestrel в настоящее время не поддерживает несколько конфигураций TLS для одной привязки, вам потребуются две привязки с уникальными IP-адресами или портами.</span><span class="sxs-lookup"><span data-stu-id="fd404-278">Note Kestrel does not currently support multiple TLS configurations on one binding, you'll need two bindings with unique IPs or ports.</span></span> <span data-ttu-id="fd404-279">См. раздел https://github.com/dotnet/runtime/issues/31097.</span><span class="sxs-lookup"><span data-stu-id="fd404-279">See https://github.com/dotnet/runtime/issues/31097</span></span>
    * <span data-ttu-id="fd404-280">IIS</span><span class="sxs-lookup"><span data-stu-id="fd404-280">IIS</span></span>
      * [<span data-ttu-id="fd404-281">Размещение IIS</span><span class="sxs-lookup"><span data-stu-id="fd404-281">Hosting IIS</span></span>](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [<span data-ttu-id="fd404-282">Настройка безопасности служб IIS</span><span class="sxs-lookup"><span data-stu-id="fd404-282">Configure security on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * <span data-ttu-id="fd404-283">HTTP.sys: [Настройка Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span><span class="sxs-lookup"><span data-stu-id="fd404-283">HTTP.sys: [Configure Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span></span>

::: moniker-end

* <span data-ttu-id="fd404-284">Для запросов к веб-приложению, которому требуется сертификат клиента, и у которых нет такого сертификата:</span><span class="sxs-lookup"><span data-stu-id="fd404-284">For requests to the web app that require a client certificate and don't have one:</span></span>
  * <span data-ttu-id="fd404-285">Перенаправление на ту же страницу с помощью защищенного поддомена сертификата клиента.</span><span class="sxs-lookup"><span data-stu-id="fd404-285">Redirect to the same page using the client certificate protected subdomain.</span></span>
  * <span data-ttu-id="fd404-286">Например, перенаправление в `myClient.contoso.com/requestedPage` .</span><span class="sxs-lookup"><span data-stu-id="fd404-286">For example, redirect to `myClient.contoso.com/requestedPage`.</span></span> <span data-ttu-id="fd404-287">Так как запрос на `myClient.contoso.com/requestedPage` имя узла отличается от `contoso.com/requestedPage` , клиент устанавливает другое соединение и предоставляется сертификат клиента.</span><span class="sxs-lookup"><span data-stu-id="fd404-287">Because the request to `myClient.contoso.com/requestedPage` is a different hostname than `contoso.com/requestedPage`, the client establishes a different connection and the client certificate is provided.</span></span>
  * <span data-ttu-id="fd404-288">Для получения дополнительной информации см. <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="fd404-288">For more information, see <xref:security/authorization/introduction>.</span></span>

<span data-ttu-id="fd404-289">Оставьте вопросы, комментарии и другие отзывы о необязательных сертификатах клиентов в этой статье об [этом обсуждении GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/18720) .</span><span class="sxs-lookup"><span data-stu-id="fd404-289">Leave questions, comments, and other feedback on optional client certificates in [this GitHub discussion](https://github.com/dotnet/AspNetCore.Docs/issues/18720) issue.</span></span>

<span data-ttu-id="fd404-290">&dagger; Указание имени сервера (SNI) — это расширение TLS для включения виртуального домена в состав согласования SSL.</span><span class="sxs-lookup"><span data-stu-id="fd404-290">&dagger; Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="fd404-291">Это фактически означает, что имя виртуального домена или имени узла можно использовать для указания конечной точки сети.</span><span class="sxs-lookup"><span data-stu-id="fd404-291">This effectively means the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>
