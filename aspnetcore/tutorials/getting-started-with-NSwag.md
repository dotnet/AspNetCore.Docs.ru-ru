---
title: Начало работы с NSwag и ASP.NET Core
author: zuckerthoben
description: Узнайте, как использовать NSwag для создания документации и страниц справки для веб-API в ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
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
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 882bbe659990cb8ea66110fccb85b8c6197df3f7
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588579"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="9ed43-103">Начало работы с NSwag и ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ed43-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="9ed43-104">Авторы: [Кристоф Ниенабер (Christoph Nienaber)](https://twitter.com/zuckerthoben), [Рико Сутер (Rico Suter)](https://rsuter.com) и [Дейв Брок (Dave Brock)](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="9ed43-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), and [Dave Brock](https://twitter.com/daveabrock)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="9ed43-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9ed43-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="9ed43-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9ed43-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="9ed43-107">NSwag обеспечивает следующие возможности:</span><span class="sxs-lookup"><span data-stu-id="9ed43-107">NSwag offers the following capabilities:</span></span>

* <span data-ttu-id="9ed43-108">Работу с пользовательским интерфейсом Swagger и генератором Swagger.</span><span class="sxs-lookup"><span data-stu-id="9ed43-108">The ability to utilize the Swagger UI and Swagger generator.</span></span>
* <span data-ttu-id="9ed43-109">Создание гибкого кода.</span><span class="sxs-lookup"><span data-stu-id="9ed43-109">Flexible code generation capabilities.</span></span>

<span data-ttu-id="9ed43-110">С NSwag отпадает необходимость в существующем API &mdash; вы можете использовать сторонние API, которые содержат Swagger и создают реализацию клиента.</span><span class="sxs-lookup"><span data-stu-id="9ed43-110">With NSwag, you don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and generate a client implementation.</span></span> <span data-ttu-id="9ed43-111">NSwag позволяет ускорить цикл разработки и легко адаптироваться к изменениям API.</span><span class="sxs-lookup"><span data-stu-id="9ed43-111">NSwag allows you to expedite the development cycle and easily adapt to API changes.</span></span>

## <a name="register-the-nswag-middleware"></a><span data-ttu-id="9ed43-112">Регистрация ПО промежуточного слоя NSwag</span><span class="sxs-lookup"><span data-stu-id="9ed43-112">Register the NSwag middleware</span></span>

<span data-ttu-id="9ed43-113">Зарегистрируйте ПО промежуточного слоя NSwag, чтобы выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="9ed43-113">Register the NSwag middleware to:</span></span>

* <span data-ttu-id="9ed43-114">создавать спецификации Swagger для реализованного веб-API;</span><span class="sxs-lookup"><span data-stu-id="9ed43-114">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="9ed43-115">применять пользовательский интерфейс Swagger для просмотра и тестирования веб-API.</span><span class="sxs-lookup"><span data-stu-id="9ed43-115">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="9ed43-116">Чтобы использовать ПО промежуточного слоя [NSwag](https://github.com/RicoSuter/NSwag) для ASP.NET Core, установите пакет NuGet [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/).</span><span class="sxs-lookup"><span data-stu-id="9ed43-116">To use the [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core middleware, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="9ed43-117">Этот пакет содержит ПО промежуточного слоя, которое позволяет создавать и использовать спецификацию Swagger, пользовательский интерфейс Swagger (версий 2 и 3) и [пользовательский интерфейс ReDoc](https://github.com/Rebilly/ReDoc).</span><span class="sxs-lookup"><span data-stu-id="9ed43-117">This package contains the middleware to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="9ed43-118">Чтобы установить пакет NuGet NSwag, воспользуйтесь одним из следующих способов.</span><span class="sxs-lookup"><span data-stu-id="9ed43-118">Use one of the following approaches to install the NSwag NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9ed43-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9ed43-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9ed43-120">В окне **Консоль диспетчера пакетов**</span><span class="sxs-lookup"><span data-stu-id="9ed43-120">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="9ed43-121">Перейдите в раздел **Представление** > **Другие окна** > **Консоль диспетчера пакетов**</span><span class="sxs-lookup"><span data-stu-id="9ed43-121">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="9ed43-122">Перейдите в каталог, в котором существует файл *TodoApi.csproj*</span><span class="sxs-lookup"><span data-stu-id="9ed43-122">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="9ed43-123">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="9ed43-123">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="9ed43-124">В диалоговом окне **Управление пакетами NuGet**</span><span class="sxs-lookup"><span data-stu-id="9ed43-124">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="9ed43-125">Щелкните правой кнопкой мыши проект в **обозревателе решений** > **Управление пакетами NuGet**</span><span class="sxs-lookup"><span data-stu-id="9ed43-125">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="9ed43-126">В качестве **источника пакета** выберите "nuget.org".</span><span class="sxs-lookup"><span data-stu-id="9ed43-126">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="9ed43-127">В поле поиска введите "NSwag.AspNetCore"</span><span class="sxs-lookup"><span data-stu-id="9ed43-127">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="9ed43-128">Выберите пакет "NSwag.AspNetCore" на вкладке **Обзор** и нажмите **Установить**</span><span class="sxs-lookup"><span data-stu-id="9ed43-128">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9ed43-129">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9ed43-129">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9ed43-130">Щелкните правой кнопкой мыши папку *Пакеты* на **панели решения** > **Добавление пакетов...** .</span><span class="sxs-lookup"><span data-stu-id="9ed43-130">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="9ed43-131">В раскрывающемся списке **Источник** в окне **Добавление пакетов** выберите вариант "nuget.org".</span><span class="sxs-lookup"><span data-stu-id="9ed43-131">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="9ed43-132">В поле поиска введите "NSwag.AspNetCore"</span><span class="sxs-lookup"><span data-stu-id="9ed43-132">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="9ed43-133">В области результатов выберите пакет "NSwag.AspNetCore" и нажмите **Добавить пакет**</span><span class="sxs-lookup"><span data-stu-id="9ed43-133">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9ed43-134">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="9ed43-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9ed43-135">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="9ed43-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="9ed43-136">Добавление и настройка ПО промежуточного слоя Swagger</span><span class="sxs-lookup"><span data-stu-id="9ed43-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="9ed43-137">Добавьте Swagger в приложение ASP.NET Core и настройте это ПО промежуточного слоя, выполнив следующие действия:</span><span class="sxs-lookup"><span data-stu-id="9ed43-137">Add and configure Swagger in your ASP.NET Core app by performing the following steps:</span></span>

* <span data-ttu-id="9ed43-138">Используя метод `Startup.ConfigureServices`, зарегистрируйте необходимые службы Swagger:</span><span class="sxs-lookup"><span data-stu-id="9ed43-138">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* <span data-ttu-id="9ed43-139">В методе `Startup.Configure` включите ПО промежуточного слоя для обслуживания созданной спецификации Swagger и пользовательского интерфейса Swagger:</span><span class="sxs-lookup"><span data-stu-id="9ed43-139">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* <span data-ttu-id="9ed43-140">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="9ed43-140">Launch the app.</span></span> <span data-ttu-id="9ed43-141">Перейдите к:</span><span class="sxs-lookup"><span data-stu-id="9ed43-141">Navigate to:</span></span>
  * <span data-ttu-id="9ed43-142">`http://localhost:<port>/swagger` для просмотра пользовательского интерфейса Swagger.</span><span class="sxs-lookup"><span data-stu-id="9ed43-142">`http://localhost:<port>/swagger` to view the Swagger UI.</span></span>
  * <span data-ttu-id="9ed43-143">`http://localhost:<port>/swagger/v1/swagger.json` для просмотра спецификации Swagger.</span><span class="sxs-lookup"><span data-stu-id="9ed43-143">`http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="9ed43-144">Создание кода</span><span class="sxs-lookup"><span data-stu-id="9ed43-144">Code generation</span></span>

<span data-ttu-id="9ed43-145">Можно воспользоваться преимуществами создания кода в NSwag, выбрав один из следующих вариантов:</span><span class="sxs-lookup"><span data-stu-id="9ed43-145">You can take advantage of NSwag's code generation capabilities by choosing one of the following options:</span></span>

* <span data-ttu-id="9ed43-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): классическое приложение Windows для создания клиентского кода API на C# или TypeScript.</span><span class="sxs-lookup"><span data-stu-id="9ed43-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): A Windows desktop app for generating API client code in C# or TypeScript.</span></span>
* <span data-ttu-id="9ed43-147">Пакеты NuGet [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) или [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) для создания кода внутри проекта.</span><span class="sxs-lookup"><span data-stu-id="9ed43-147">The [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages for code generation inside your project.</span></span>
* <span data-ttu-id="9ed43-148">NSwag из [командной строки](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span><span class="sxs-lookup"><span data-stu-id="9ed43-148">NSwag from the [command line](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="9ed43-149">Пакет NuGet [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild).</span><span class="sxs-lookup"><span data-stu-id="9ed43-149">The [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet package.</span></span>
* <span data-ttu-id="9ed43-150">[Подключенная служба Unchase OpenAPI (Swagger)](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): подключенная служба Visual Studio для создания клиентского кода API на C# или TypeScript.</span><span class="sxs-lookup"><span data-stu-id="9ed43-150">The [Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): A Visual Studio Connected Service for generating API client code in C# or TypeScript.</span></span> <span data-ttu-id="9ed43-151">Она также создает контроллеры C# для служб OpenAPI с NSwag.</span><span class="sxs-lookup"><span data-stu-id="9ed43-151">Also generates C# controllers for OpenAPI services with NSwag.</span></span>

### <a name="generate-code-with-nswagstudio"></a><span data-ttu-id="9ed43-152">Создание кода с помощью NSwagStudio</span><span class="sxs-lookup"><span data-stu-id="9ed43-152">Generate code with NSwagStudio</span></span>

* <span data-ttu-id="9ed43-153">Установите NSwagStudio, следуя инструкциям из [репозитория NSwagStudio на веб-сайте GitHub](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span><span class="sxs-lookup"><span data-stu-id="9ed43-153">Install NSwagStudio by following the instructions at the [NSwagStudio GitHub repository](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span></span> <span data-ttu-id="9ed43-154">На странице выпуска NSwag можно скачать версию xcopy, которую можно запустить без установки и прав администратора.</span><span class="sxs-lookup"><span data-stu-id="9ed43-154">On the NSwag release page you can download an xcopy version which can be started without installation and admin privileges.</span></span>
* <span data-ttu-id="9ed43-155">Запустите NSwagStudio и введите URL-адрес файла *swagger.json* в текстовое поле **Swagger Specification URL** (URL-адрес спецификации Swagger).</span><span class="sxs-lookup"><span data-stu-id="9ed43-155">Launch NSwagStudio and enter the *swagger.json* file URL in the **Swagger Specification URL** text box.</span></span> <span data-ttu-id="9ed43-156">Например, *http://localhost:44354/swagger/v1/swagger.json* .</span><span class="sxs-lookup"><span data-stu-id="9ed43-156">For example, *http://localhost:44354/swagger/v1/swagger.json*.</span></span>
* <span data-ttu-id="9ed43-157">Нажмите кнопку **Create local Copy** (Создать локальную копию), чтобы создать представление JSON своей спецификации Swagger.</span><span class="sxs-lookup"><span data-stu-id="9ed43-157">Click the **Create local Copy** button to generate a JSON representation of your Swagger specification.</span></span>

  ![Создание локальной копии спецификация Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* <span data-ttu-id="9ed43-159">В области **Outputs** (Выходные данные) установите флажок **CSharp Client** (Клиент CSharp).</span><span class="sxs-lookup"><span data-stu-id="9ed43-159">In the **Outputs** area, click the **CSharp Client** check box.</span></span> <span data-ttu-id="9ed43-160">В зависимости от проекта вы также можете выбрать **TypeScript Client** (Клиент TypeScript) или **CSharp Web API Controller** (Контроллер веб-API CSharp).</span><span class="sxs-lookup"><span data-stu-id="9ed43-160">Depending on your project, you can also choose **TypeScript Client** or **CSharp Web API Controller**.</span></span> <span data-ttu-id="9ed43-161">Если выбрать **CSharp Web API Controller** (Контроллер веб-API CSharp), служба будет перестроена по спецификации посредством обратного создания.</span><span class="sxs-lookup"><span data-stu-id="9ed43-161">If you select **CSharp Web API Controller**, a service specification rebuilds the service, serving as a reverse generation.</span></span>
* <span data-ttu-id="9ed43-162">Щелкните **Generate Outputs** (Создать выходные данные), чтобы создать полную клиентскую реализацию проекта *TodoApi.NSwag* на языке C#.</span><span class="sxs-lookup"><span data-stu-id="9ed43-162">Click **Generate Outputs** to produce a complete C# client implementation of the *TodoApi.NSwag* project.</span></span> <span data-ttu-id="9ed43-163">Откройте вкладку **CSharp Client** (Клиент CSharp), чтобы просмотреть созданный клиентский код.</span><span class="sxs-lookup"><span data-stu-id="9ed43-163">To see the generated client code, click the **CSharp Client** tab:</span></span>

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> <span data-ttu-id="9ed43-164">Клиентский код на C# создается на основе выбранных параметров на вкладке **Settings** (Параметры). Измените параметры для выполнения таких задач, как переименование пространства имен по умолчанию и создание синхронного метода.</span><span class="sxs-lookup"><span data-stu-id="9ed43-164">The C# client code is generated based on selections in the **Settings** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="9ed43-165">Скопируйте созданный код на C# в файл в проекте клиента, который будет использовать API.</span><span class="sxs-lookup"><span data-stu-id="9ed43-165">Copy the generated C# code into a file in the client project that will consume the API.</span></span>
* <span data-ttu-id="9ed43-166">Начните использовать веб-API:</span><span class="sxs-lookup"><span data-stu-id="9ed43-166">Start consuming the web API:</span></span>

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a><span data-ttu-id="9ed43-167">Настройка документирования для API</span><span class="sxs-lookup"><span data-stu-id="9ed43-167">Customize API documentation</span></span>

<span data-ttu-id="9ed43-168">Swagger предоставляет параметры для документирования объектной модели и упрощения использования веб-API.</span><span class="sxs-lookup"><span data-stu-id="9ed43-168">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="9ed43-169">Данные и описание API</span><span class="sxs-lookup"><span data-stu-id="9ed43-169">API info and description</span></span>

<span data-ttu-id="9ed43-170">В методе `Startup.ConfigureServices` действие по настройке, передаваемое в метод `AddSwaggerDocument`, можно использовать для добавления таких сведений, как автор, лицензия и описание:</span><span class="sxs-lookup"><span data-stu-id="9ed43-170">In the `Startup.ConfigureServices` method, a configuration action passed to the `AddSwaggerDocument` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

<span data-ttu-id="9ed43-171">Пользовательский интерфейс Swagger отображает сведения о версии:</span><span class="sxs-lookup"><span data-stu-id="9ed43-171">The Swagger UI displays the version's information:</span></span>

![Пользовательский интерфейс Swagger с информацией о версии](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="9ed43-173">XML-комментарии</span><span class="sxs-lookup"><span data-stu-id="9ed43-173">XML comments</span></span>

<span data-ttu-id="9ed43-174">Чтобы включить комментарии XML, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="9ed43-174">To enable XML comments, perform the following steps:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9ed43-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9ed43-175">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="9ed43-176">В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите команду **Изменить <имя_проекта>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="9ed43-176">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="9ed43-177">Вручную добавьте выделенные строки в файл *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="9ed43-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="9ed43-178">В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункт **Свойства**.</span><span class="sxs-lookup"><span data-stu-id="9ed43-178">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="9ed43-179">Установите флажок **Файл XML-документации** в разделе **Вывод** на вкладке **Сборка**</span><span class="sxs-lookup"><span data-stu-id="9ed43-179">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9ed43-180">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9ed43-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="9ed43-181">В *панели решения* нажмите **Управление** и выберите имя проекта.</span><span class="sxs-lookup"><span data-stu-id="9ed43-181">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="9ed43-182">Перейдите в раздел **Сервис** > **Изменить файл**.</span><span class="sxs-lookup"><span data-stu-id="9ed43-182">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="9ed43-183">Вручную добавьте выделенные строки в файл *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="9ed43-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="9ed43-184">Откройте диалоговое окно **Параметры проекта** > **Сборка** > **Компилятор**.</span><span class="sxs-lookup"><span data-stu-id="9ed43-184">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="9ed43-185">Установите флажок **Сформировать XML-документацию** в разделе **Общие параметры**</span><span class="sxs-lookup"><span data-stu-id="9ed43-185">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="net-core-cli"></a>[<span data-ttu-id="9ed43-186">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="9ed43-186">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9ed43-187">Вручную добавьте выделенные строки в файл *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="9ed43-187">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="9ed43-188">Заметки к данным</span><span class="sxs-lookup"><span data-stu-id="9ed43-188">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="9ed43-189">Так как NSwag использует [отражение](/dotnet/csharp/programming-guide/concepts/reflection), а рекомендуемый тип возвращаемого значения для действий веб-API — это [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), NSwag не может определить что выполняет ваше действие и что оно возвращает.</span><span class="sxs-lookup"><span data-stu-id="9ed43-189">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), it can't infer what your action is doing and what it returns.</span></span>

<span data-ttu-id="9ed43-190">Рассмотрим следующий пример.</span><span class="sxs-lookup"><span data-stu-id="9ed43-190">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="9ed43-191">Предыдущее действие возвращает `IActionResult`, но внутри действия возвращается [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) или [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span><span class="sxs-lookup"><span data-stu-id="9ed43-191">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) or [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span></span> <span data-ttu-id="9ed43-192">Используйте заметки к данным для сообщения клиентам о том, какие коды состояний HTTP возвращает это действие.</span><span class="sxs-lookup"><span data-stu-id="9ed43-192">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="9ed43-193">Пометьте действие следующими атрибутами:</span><span class="sxs-lookup"><span data-stu-id="9ed43-193">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="9ed43-194">Так как NSwag использует [отражение](/dotnet/csharp/programming-guide/concepts/reflection), а рекомендуемый тип возвращаемого значения для действий веб-API — это [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), NSwag может определить только тип возвращаемого значения, задаваемый `T`.</span><span class="sxs-lookup"><span data-stu-id="9ed43-194">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), it can only infer the return type defined by `T`.</span></span> <span data-ttu-id="9ed43-195">Невозможно автоматически определить другие возможные типы возвращаемого значения.</span><span class="sxs-lookup"><span data-stu-id="9ed43-195">You can't automatically infer other possible return types.</span></span>

<span data-ttu-id="9ed43-196">Рассмотрим следующий пример.</span><span class="sxs-lookup"><span data-stu-id="9ed43-196">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="9ed43-197">Предыдущее действие возвращает `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="9ed43-197">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="9ed43-198">Внутри действия оно возвращает [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span><span class="sxs-lookup"><span data-stu-id="9ed43-198">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="9ed43-199">Так как контроллер имеет атрибут [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute), ответ [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) также возможен.</span><span class="sxs-lookup"><span data-stu-id="9ed43-199">Since the controller has the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="9ed43-200">Дополнительные сведения см. в разделе [Автоматические отклики HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="9ed43-200">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="9ed43-201">Используйте заметки к данным для сообщения клиентам о том, какие коды состояний HTTP возвращает это действие.</span><span class="sxs-lookup"><span data-stu-id="9ed43-201">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="9ed43-202">Пометьте действие следующими атрибутами:</span><span class="sxs-lookup"><span data-stu-id="9ed43-202">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="9ed43-203">В ASP.NET Core 2.2 или более поздней версии можно использовать соглашения вместо явного добавления `[ProducesResponseType]` к отдельным действиям.</span><span class="sxs-lookup"><span data-stu-id="9ed43-203">In ASP.NET Core 2.2 or later, you can use conventions instead of explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="9ed43-204">Для получения дополнительной информации см. <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="9ed43-204">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="9ed43-205">Генератор Swagger теперь может точно описать это действие, и созданные клиенты знают, что они получают при вызове конечной точки.</span><span class="sxs-lookup"><span data-stu-id="9ed43-205">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="9ed43-206">Рекомендуется помечать этими атрибутами все действия.</span><span class="sxs-lookup"><span data-stu-id="9ed43-206">As a recommendation, mark all actions with these attributes.</span></span>

<span data-ttu-id="9ed43-207">Рекомендации о том, какие ответы HTTP должны возвращать ваши действия API, см. в [спецификации RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).</span><span class="sxs-lookup"><span data-stu-id="9ed43-207">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
