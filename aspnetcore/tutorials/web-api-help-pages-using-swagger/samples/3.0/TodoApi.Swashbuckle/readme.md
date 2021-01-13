---
page_type: sample
description: Дополнительные сведения о добавлении Swashbuckle в проект веб-API ASP.NET Core для интеграции пользовательского интерфейса Swagger.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
- vs-code
- vs-mac
urlFragment: getstarted-swashbuckle-aspnetcore
ms.openlocfilehash: e02247325f430b0ce23dbb3f5bc344a60a1a164a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "78647590"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="41b42-102">Начало работы с Swashbuckle и ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="41b42-102">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="41b42-103">При использовании веб-API разработчику бывает сложно разобраться в различных методах.</span><span class="sxs-lookup"><span data-stu-id="41b42-103">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="41b42-104">[Swagger](https://swagger.io/) (также называется [OpenAPI](https://www.openapis.org/)) позволяет решить проблему создания полезной документации и страниц справки для веб-API.</span><span class="sxs-lookup"><span data-stu-id="41b42-104">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="41b42-105">Он имеет такие преимущества, как интерактивная документация, создание пакета SDK для клиента и возможность обнаружения API.</span><span class="sxs-lookup"><span data-stu-id="41b42-105">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="41b42-106">В этом примере показана реализация [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="41b42-106">In this sample, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) the .NET implementation is shown.</span></span>

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="41b42-107">Добавление и настройка ПО промежуточного слоя Swagger</span><span class="sxs-lookup"><span data-stu-id="41b42-107">Add and configure Swagger middleware</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    });
}
```

<span data-ttu-id="41b42-108">В методе `Startup.Configure` включите ПО промежуточного слоя для обслуживания созданного документа JSON и пользовательского интерфейса Swagger:</span><span class="sxs-lookup"><span data-stu-id="41b42-108">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.),
    // specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1"); 
    });

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="41b42-109">Предыдущий вызов метода `UseSwaggerUI` включает [ПО промежуточного слоя для статических файлов](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="41b42-109">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span></span> <span data-ttu-id="41b42-110">Если код предназначен для .NET Framework или NET Core 1.x, добавьте в проект пакет NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/).</span><span class="sxs-lookup"><span data-stu-id="41b42-110">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="41b42-111">Запустите приложение и перейдите к файлу `http://localhost:<port>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="41b42-111">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="41b42-112">Появится созданный документ, описывающий конечные точки, как показано в [спецификации Swagger (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span><span class="sxs-lookup"><span data-stu-id="41b42-112">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="41b42-113">Пользовательский интерфейс Swagger можно найти по адресу `http://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="41b42-113">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="41b42-114">Изучите API через пользовательский интерфейс Swagger и включите его в другие программы.</span><span class="sxs-lookup"><span data-stu-id="41b42-114">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="41b42-115">Чтобы предоставить пользовательский интерфейс Swagger в корневом каталоге приложения (`http://localhost:<port>/`), установите для свойства `RoutePrefix` пустую строку:</span><span class="sxs-lookup"><span data-stu-id="41b42-115">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

<span data-ttu-id="41b42-116">Если вы используете каталоги в службах IIS или на обратном прокси-сервере, задайте для конечной точки Swagger относительный путь с помощью префикса `./`.</span><span class="sxs-lookup"><span data-stu-id="41b42-116">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="41b42-117">Например, `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="41b42-117">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="41b42-118">`/swagger/v1/swagger.json` указывает, что приложение должно искать JSON-файл в реальном корне URL-адреса (с префиксом маршрута, если он используется).</span><span class="sxs-lookup"><span data-stu-id="41b42-118">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="41b42-119">Например, используйте `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` вместо `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="41b42-119">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="41b42-120">Настройка и расширение</span><span class="sxs-lookup"><span data-stu-id="41b42-120">Customize and extend</span></span>

<span data-ttu-id="41b42-121">Swagger предоставляет параметры для документирования объектной модели и настройки пользовательского интерфейса в соответствии с вашим фирменным стилем.</span><span class="sxs-lookup"><span data-stu-id="41b42-121">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="41b42-122">В классе `Startup` добавьте следующие пространства имен:</span><span class="sxs-lookup"><span data-stu-id="41b42-122">In the `Startup` class, add the following namespaces:</span></span>
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="41b42-123">Данные и описание API</span><span class="sxs-lookup"><span data-stu-id="41b42-123">API info and description</span></span>

<span data-ttu-id="41b42-124">Действие по настройке, передаваемое в метод `AddSwaggerGen`, можно использовать для добавления таких сведений, как автор, лицензия и описание:</span><span class="sxs-lookup"><span data-stu-id="41b42-124">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

```csharp
// Register the Swagger generator, defining 1 or more Swagger documents
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Shayne Boyer",
            Email = string.Empty,
            Url = new Uri("https://twitter.com/spboyer"),
        },
        License = new OpenApiLicense
        {
            Name = "Use under LICX",
            Url = new Uri("https://example.com/license"),
        }
    });
});
```

<span data-ttu-id="41b42-125">Пользовательский интерфейс Swagger отображает сведения о версии:</span><span class="sxs-lookup"><span data-stu-id="41b42-125">The Swagger UI displays the version's information:</span></span>

![Пользовательский интерфейс Swagger с данными версии: описание, автор и ссылка на дополнительные сведения](sample_images/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="41b42-127">XML-комментарии</span><span class="sxs-lookup"><span data-stu-id="41b42-127">XML comments</span></span>

<span data-ttu-id="41b42-128">XML-комментарии можно включить следующим образом.</span><span class="sxs-lookup"><span data-stu-id="41b42-128">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="41b42-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41b42-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="41b42-130">В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите команду **Изменить <имя_проекта>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="41b42-130">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="41b42-131">Вручную добавьте выделенные строки в файл *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="41b42-131">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="41b42-132">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="41b42-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="41b42-133">В *панели решения* нажмите **Управление** и выберите имя проекта.</span><span class="sxs-lookup"><span data-stu-id="41b42-133">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="41b42-134">Перейдите в раздел **Сервис** > **Изменить файл**.</span><span class="sxs-lookup"><span data-stu-id="41b42-134">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="41b42-135">Вручную добавьте выделенные строки в файл *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="41b42-135">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[<span data-ttu-id="41b42-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41b42-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="41b42-137">Вручную добавьте выделенные строки в файл *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="41b42-137">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

<span data-ttu-id="41b42-138">Включение комментариев XML предоставляет отладочную информацию для недокументированных открытых типов и членов.</span><span class="sxs-lookup"><span data-stu-id="41b42-138">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="41b42-139">Недокументированные типы и члены указываются в предупреждающем сообщении.</span><span class="sxs-lookup"><span data-stu-id="41b42-139">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="41b42-140">Например, следующее сообщение оповещает о нарушении кода предупреждения 1591:</span><span class="sxs-lookup"><span data-stu-id="41b42-140">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="41b42-141">Чтобы отключить предупреждения на уровне всего проекта, определите разделенный точками с запятой список игнорируемых кодов предупреждений в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="41b42-141">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="41b42-142">Добавление кодов предупреждений в `$(NoWarn);` также применяется к [значениям C# по умолчанию](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).</span><span class="sxs-lookup"><span data-stu-id="41b42-142">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

<span data-ttu-id="41b42-143">Чтобы отключить предупреждения только для определенных элементов, поместите код в директивы препроцессора [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning).</span><span class="sxs-lookup"><span data-stu-id="41b42-143">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="41b42-144">Этот подход полезен, если код не должен быть доступен в документации API. В приведенном ниже примере код предупреждения CS1591 игнорируется для всего класса `Program`.</span><span class="sxs-lookup"><span data-stu-id="41b42-144">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="41b42-145">Применение кода предупреждения можно восстановить в конце определения класса.</span><span class="sxs-lookup"><span data-stu-id="41b42-145">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="41b42-146">Укажите несколько кодов предупреждений в списке, разделив их запятыми.</span><span class="sxs-lookup"><span data-stu-id="41b42-146">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="41b42-147">Настройте Swagger на использование XML-файла, который создается с помощью приведенных выше инструкций.</span><span class="sxs-lookup"><span data-stu-id="41b42-147">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="41b42-148">В Linux и других операционных системах, отличных от Windows, имена и пути файлов могут быть чувствительны к регистру.</span><span class="sxs-lookup"><span data-stu-id="41b42-148">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="41b42-149">Например, файл *ToDoApi.XML* допустим в Windows, но не в CentOS.</span><span class="sxs-lookup"><span data-stu-id="41b42-149">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

```csharp
/// NOTE LAST 3 LINES IN THIS SNIPPET
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo
        {
            Version = "v1",
            Title = "ToDo API",
            Description = "A simple example ASP.NET Core Web API",
            TermsOfService = new Uri("https://example.com/terms"),
            Contact = new OpenApiContact
            {
                Name = "Shayne Boyer",
                Email = string.Empty,
                Url = new Uri("https://twitter.com/spboyer"),
            },
            License = new OpenApiLicense
            {
                Name = "Use under LICX",
                Url = new Uri("https://example.com/license"),
            }
        });

        // Set the comments path for the Swagger JSON and UI.
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });
}
```

<span data-ttu-id="41b42-150">В приведенном выше коде для создания имени XML-файла, соответствующего имени проекта веб-API, используется [отражение](/dotnet/csharp/programming-guide/concepts/reflection).</span><span class="sxs-lookup"><span data-stu-id="41b42-150">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="41b42-151">Свойство [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) используется для построения пути к XML-файлу.</span><span class="sxs-lookup"><span data-stu-id="41b42-151">The [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="41b42-152">Некоторые функции Swagger (например, схемы входных параметров или методы HTTP и коды ответов из соответствующих атрибутов) работают без использования XML-файла документации.</span><span class="sxs-lookup"><span data-stu-id="41b42-152">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="41b42-153">Для большинства функций, включая метод сводки и описания параметров и кодов ответа, использование XML-файла является обязательным.</span><span class="sxs-lookup"><span data-stu-id="41b42-153">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="41b42-154">Включение в действие комментариев с тройной косой чертой улучшает пользовательский интерфейс Swagger, так как позволяет добавить описание к заголовку раздела.</span><span class="sxs-lookup"><span data-stu-id="41b42-154">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="41b42-155">Добавьте элемент [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) над действием `Delete`:</span><span class="sxs-lookup"><span data-stu-id="41b42-155">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

```csharp
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
/// <param name="id"></param>        
[HttpDelete("{id}")]
public IActionResult Delete(long id)
{
    var todo = _context.TodoItems.Find(id);

    if (todo == null)
    {
        return NotFound();
    }

    _context.TodoItems.Remove(todo);
    _context.SaveChanges();

    return NoContent();
}
```
<span data-ttu-id="41b42-156">Пользовательский интерфейс Swagger отображает внутренний текст элемента `<summary>` в приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="41b42-156">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Пользовательский интерфейс Swagger с XML-комментарием "Удаляет указанный элемент TodoItem"](sample_images/triple-slash-comments.png)

<span data-ttu-id="41b42-159">Пользовательский интерфейс определяется созданной схемой JSON:</span><span class="sxs-lookup"><span data-stu-id="41b42-159">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```
<span data-ttu-id="41b42-160">Добавьте элемент [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) в документацию по методу действия `Create`.</span><span class="sxs-lookup"><span data-stu-id="41b42-160">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="41b42-161">Он дополняет сведения, указанные в элементе `<summary>`, и предоставляет более надежный пользовательский интерфейс Swagger.</span><span class="sxs-lookup"><span data-stu-id="41b42-161">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="41b42-162">Содержимое элемента `<remarks>` может включать текст, JSON или XML.</span><span class="sxs-lookup"><span data-stu-id="41b42-162">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

```csharp
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item1",
///        "isComplete": true
///     }
///
/// </remarks>
/// <param name="item"></param>
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```
<span data-ttu-id="41b42-163">Обратите внимание, как эти дополнительные комментарии улучшают пользовательский интерфейс:</span><span class="sxs-lookup"><span data-stu-id="41b42-163">Notice the UI enhancements with these additional comments:</span></span>

![Пользовательский интерфейс Swagger с показанными дополнительными комментариями](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="41b42-165">Заметки к данным</span><span class="sxs-lookup"><span data-stu-id="41b42-165">Data annotations</span></span>

<span data-ttu-id="41b42-166">Пометьте модель атрибутами из пространства имен [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations), чтобы обеспечить компоненты пользовательского интерфейса Swagger.</span><span class="sxs-lookup"><span data-stu-id="41b42-166">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="41b42-167">Добавьте атрибут `[Required]` к свойству `Name` класса `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="41b42-167">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace TodoApi.Models
{
    public class TodoItem
    {
        public long Id { get; set; }

        [Required]
        public string Name { get; set; }

        [DefaultValue(false)]
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="41b42-168">Наличие этого атрибута изменяет поведение пользовательского интерфейса и схему базового JSON.</span><span class="sxs-lookup"><span data-stu-id="41b42-168">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="41b42-169">Добавьте к контроллеру API атрибут `[Produces("application/json")]`.</span><span class="sxs-lookup"><span data-stu-id="41b42-169">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="41b42-170">Он позволяет объявить, что действия контроллера поддерживают тип содержимого ответа *application/json*:</span><span class="sxs-lookup"><span data-stu-id="41b42-170">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
<span data-ttu-id="41b42-171">В раскрывающемся списке **Тип содержимого ответа** этот тип содержимого выбирается по умолчанию для операций контроллера GET.</span><span class="sxs-lookup"><span data-stu-id="41b42-171">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Пользовательский интерфейс Swagger с типом содержимого ответа по умолчанию](sample_images/json-response-content-type.png)

<span data-ttu-id="41b42-173">Чем больше заметок к данным используется в веб-API, тем более содержательными и полезными становятся пользовательский интерфейс и страницы справки по API.</span><span class="sxs-lookup"><span data-stu-id="41b42-173">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="41b42-174">Описание типов ответов</span><span class="sxs-lookup"><span data-stu-id="41b42-174">Describe response types</span></span>

<span data-ttu-id="41b42-175">Разработчиков, использующих веб-API, в первую очередь, волнуют возвращаемые данные &mdash; а именно: типы ответов и коды ошибок (если они не стандартны).</span><span class="sxs-lookup"><span data-stu-id="41b42-175">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="41b42-176">Типы ответов и коды ошибок обозначаются в комментариях к XML и заметках к данным.</span><span class="sxs-lookup"><span data-stu-id="41b42-176">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="41b42-177">Действие `Create` в случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="41b42-177">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="41b42-178">Код состояния HTTP 400 возвращается в том случае, когда текст отправленного запроса имеет значение NULL.</span><span class="sxs-lookup"><span data-stu-id="41b42-178">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="41b42-179">Без надлежащей документации в пользовательском интерфейсе Swagger пользователь не будет знать, чего ожидать.</span><span class="sxs-lookup"><span data-stu-id="41b42-179">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="41b42-180">Эту проблему решает добавление строк, выделенных в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="41b42-180">Fix that problem by adding the highlighted lines in the following example:</span></span>

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

<span data-ttu-id="41b42-181">Теперь пользовательский интерфейс Swagger четко документирует ожидаемые коды HTTP-ответов.</span><span class="sxs-lookup"><span data-stu-id="41b42-181">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Пользовательский интерфейс Swagger, показывающий описание класса ответа POST "Возвращает вновь созданный элемент Todo" и "400 — Если элемент имеет значение NULL" для кода состояния и причины в разделе "Сообщения ответов"](sample_images/data-annotations-response-types.png)

<span data-ttu-id="41b42-183">В ASP.NET Core 2.2 или более поздней версии соглашения можно использовать в качестве альтернативы явному добавлению к отдельным действиям элемента `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="41b42-183">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="41b42-184">См. сведения см. об [использовании соглашений веб-API](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="41b42-184">For more information, see [Use web API conventions](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span></span>

<span data-ttu-id="41b42-185">См. сведения о настройке пользовательского интерфейса: [Настройка пользовательского интерфейса](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span><span class="sxs-lookup"><span data-stu-id="41b42-185">For information on customizing the UI see: [Customize the UI](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span></span>
