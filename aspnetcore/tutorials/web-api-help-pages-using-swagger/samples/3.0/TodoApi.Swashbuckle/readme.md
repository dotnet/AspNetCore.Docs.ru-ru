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
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Начало работы с Swashbuckle и ASP.NET Core

При использовании веб-API разработчику бывает сложно разобраться в различных методах. [Swagger](https://swagger.io/) (также называется [OpenAPI](https://www.openapis.org/)) позволяет решить проблему создания полезной документации и страниц справки для веб-API. Он имеет такие преимущества, как интерактивная документация, создание пакета SDK для клиента и возможность обнаружения API.

В этом примере показана реализация [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).

## <a name="add-and-configure-swagger-middleware"></a>Добавление и настройка ПО промежуточного слоя Swagger

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

В методе `Startup.Configure` включите ПО промежуточного слоя для обслуживания созданного документа JSON и пользовательского интерфейса Swagger:

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

Предыдущий вызов метода `UseSwaggerUI` включает [ПО промежуточного слоя для статических файлов](https://docs.microsoft.com/aspnet/core/fundamentals/static-files). Если код предназначен для .NET Framework или NET Core 1.x, добавьте в проект пакет NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/).

Запустите приложение и перейдите к файлу `http://localhost:<port>/swagger/v1/swagger.json`. Появится созданный документ, описывающий конечные точки, как показано в [спецификации Swagger (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).

Пользовательский интерфейс Swagger можно найти по адресу `http://localhost:<port>/swagger`. Изучите API через пользовательский интерфейс Swagger и включите его в другие программы.

> [!TIP]
> Чтобы предоставить пользовательский интерфейс Swagger в корневом каталоге приложения (`http://localhost:<port>/`), установите для свойства `RoutePrefix` пустую строку:
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

Если вы используете каталоги в службах IIS или на обратном прокси-сервере, задайте для конечной точки Swagger относительный путь с помощью префикса `./`. Например, `./swagger/v1/swagger.json`. `/swagger/v1/swagger.json` указывает, что приложение должно искать JSON-файл в реальном корне URL-адреса (с префиксом маршрута, если он используется). Например, используйте `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` вместо `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.

## <a name="customize-and-extend"></a>Настройка и расширение

Swagger предоставляет параметры для документирования объектной модели и настройки пользовательского интерфейса в соответствии с вашим фирменным стилем.

В классе `Startup` добавьте следующие пространства имен:
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>Данные и описание API

Действие по настройке, передаваемое в метод `AddSwaggerGen`, можно использовать для добавления таких сведений, как автор, лицензия и описание:

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

Пользовательский интерфейс Swagger отображает сведения о версии:

![Пользовательский интерфейс Swagger с данными версии: описание, автор и ссылка на дополнительные сведения](sample_images/custom-info.png)

### <a name="xml-comments"></a>XML-комментарии

XML-комментарии можно включить следующим образом.

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите команду **Изменить <имя_проекта>.csproj**.
* Вручную добавьте выделенные строки в файл *.csproj*:

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* В *панели решения* нажмите **Управление** и выберите имя проекта. Перейдите в раздел **Сервис** > **Изменить файл**.
* Вручную добавьте выделенные строки в файл *.csproj*:

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Вручную добавьте выделенные строки в файл *.csproj*:

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

Включение комментариев XML предоставляет отладочную информацию для недокументированных открытых типов и членов. Недокументированные типы и члены указываются в предупреждающем сообщении. Например, следующее сообщение оповещает о нарушении кода предупреждения 1591:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Чтобы отключить предупреждения на уровне всего проекта, определите разделенный точками с запятой список игнорируемых кодов предупреждений в файле проекта. Добавление кодов предупреждений в `$(NoWarn);` также применяется к [значениям C# по умолчанию](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

Чтобы отключить предупреждения только для определенных элементов, поместите код в директивы препроцессора [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning). Этот подход полезен, если код не должен быть доступен в документации API. В приведенном ниже примере код предупреждения CS1591 игнорируется для всего класса `Program`. Применение кода предупреждения можно восстановить в конце определения класса. Укажите несколько кодов предупреждений в списке, разделив их запятыми.

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

Настройте Swagger на использование XML-файла, который создается с помощью приведенных выше инструкций. В Linux и других операционных системах, отличных от Windows, имена и пути файлов могут быть чувствительны к регистру. Например, файл *ToDoApi.XML* допустим в Windows, но не в CentOS.

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

В приведенном выше коде для создания имени XML-файла, соответствующего имени проекта веб-API, используется [отражение](/dotnet/csharp/programming-guide/concepts/reflection). Свойство [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) используется для построения пути к XML-файлу. Некоторые функции Swagger (например, схемы входных параметров или методы HTTP и коды ответов из соответствующих атрибутов) работают без использования XML-файла документации. Для большинства функций, включая метод сводки и описания параметров и кодов ответа, использование XML-файла является обязательным.

Включение в действие комментариев с тройной косой чертой улучшает пользовательский интерфейс Swagger, так как позволяет добавить описание к заголовку раздела. Добавьте элемент [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) над действием `Delete`:

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
Пользовательский интерфейс Swagger отображает внутренний текст элемента `<summary>` в приведенном выше коде:

![Пользовательский интерфейс Swagger с XML-комментарием "Удаляет указанный элемент TodoItem" для метода DELETE](sample_images/triple-slash-comments.png)

Пользовательский интерфейс определяется созданной схемой JSON:

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
Добавьте элемент [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) в документацию по методу действия `Create`. Он дополняет сведения, указанные в элементе `<summary>`, и предоставляет более надежный пользовательский интерфейс Swagger. Содержимое элемента `<remarks>` может включать текст, JSON или XML.

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
Обратите внимание, как эти дополнительные комментарии улучшают пользовательский интерфейс:

![Пользовательский интерфейс Swagger с показанными дополнительными комментариями](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a>Заметки к данным

Пометьте модель атрибутами из пространства имен [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations), чтобы обеспечить компоненты пользовательского интерфейса Swagger.

Добавьте атрибут `[Required]` к свойству `Name` класса `TodoItem`.

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

Наличие этого атрибута изменяет поведение пользовательского интерфейса и схему базового JSON.

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

Добавьте к контроллеру API атрибут `[Produces("application/json")]`. Он позволяет объявить, что действия контроллера поддерживают тип содержимого ответа *application/json*:

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
В раскрывающемся списке **Тип содержимого ответа** этот тип содержимого выбирается по умолчанию для операций контроллера GET.

![Пользовательский интерфейс Swagger с типом содержимого ответа по умолчанию](sample_images/json-response-content-type.png)

Чем больше заметок к данным используется в веб-API, тем более содержательными и полезными становятся пользовательский интерфейс и страницы справки по API.

### <a name="describe-response-types"></a>Описание типов ответов

Разработчиков, использующих веб-API, в первую очередь, волнуют возвращаемые данные &mdash; а именно: типы ответов и коды ошибок (если они не стандартны). Типы ответов и коды ошибок обозначаются в комментариях к XML и заметках к данным.

Действие `Create` в случае успеха возвращает код состояния HTTP 201. Код состояния HTTP 400 возвращается в том случае, когда текст отправленного запроса имеет значение NULL. Без надлежащей документации в пользовательском интерфейсе Swagger пользователь не будет знать, чего ожидать. Эту проблему решает добавление строк, выделенных в следующем примере:

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

Теперь пользовательский интерфейс Swagger четко документирует ожидаемые коды HTTP-ответов.

![Пользовательский интерфейс Swagger, показывающий описание класса ответа POST "Возвращает вновь созданный элемент Todo" и "400 — Если элемент имеет значение NULL" для кода состояния и причины в разделе "Сообщения ответов"](sample_images/data-annotations-response-types.png)

В ASP.NET Core 2.2 или более поздней версии соглашения можно использовать в качестве альтернативы явному добавлению к отдельным действиям элемента `[ProducesResponseType]`. См. сведения см. об [использовании соглашений веб-API](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).

См. сведения о настройке пользовательского интерфейса: [Настройка пользовательского интерфейса](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)
