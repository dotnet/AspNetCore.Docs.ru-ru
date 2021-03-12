---
title: Привязка модели в ASP.NET Core
author: rick-anderson
description: Узнайте, как работает привязка модели в ASP.NET Core и как настроить ее поведение.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: 5eaedf6dbe5df59848b9cf8a5bda67add48db2a6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586947"
---
# <a name="model-binding-in-aspnet-core"></a>Привязка модели в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

В этой статье объясняется, что такое привязка модели, как это работает и как настроить ее поведение.

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/model-binding/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).

## <a name="what-is-model-binding"></a>Что такое привязка модели

Контроллеры и Razor страницы работают с данными, поступающими из HTTP-запросов. Например, данные о маршруте могут предоставлять ключ записи, а опубликованные поля формы могут предоставлять значения для свойств модели. Написание кода для получения этих значений и их преобразования из строк в типы .NET будет утомительной задачей с высоким риском ошибок. Привязка модели позволяет автоматизировать этот процесс. Система привязки модели:

* Извлекает данные из различных источников, таких как данные о маршруте, поля формы и строки запроса.
* Предоставляет данные для контроллеров и Razor страниц в параметрах метода и общих свойствах.
* Преобразует строковые данные в типы .NET.
* Обновляет свойства сложных типов.

## <a name="example"></a>Например, .

Предположим, у вас есть следующий метод действия:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

И приложение получает запрос с этим URL-адресом:

```
http://contoso.com/api/pets/2?DogsOnly=true
```

Привязка модели выполняет следующие действия, после того, как система маршрутизации выберет метод действия:

* Находит первый параметр `GetByID`, целое число с именем `id`.
* Просматривает доступные источники в HTTP-запросе и находит `id` = "2" в данных маршрута.
* Преобразует строку "2" в целое число 2.
* Находит следующий параметр `GetByID`, логическое значение с именем `dogsOnly`.
* Просматривает источники и находит "DogsOnly=true" в строке запроса. Сопоставление имен не учитывает регистр.
* Преобразует строку "true" в логическое значение `true`.

Затем платформа вызывает метод `GetById`, передавая 2 для параметра `id` и `true` для параметра `dogsOnly`.

В приведенном выше примере целевые объекты привязки модели — это параметры методов, которые являются примитивными типами. Целевые объекты также могут быть свойствами сложного типа. После успешной привязки каждого свойства осуществляется [проверка модели](xref:mvc/models/validation) для этого свойства. Записи о данных, привязанных к модели, а также об ошибках привязки или проверки хранятся в [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) или [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState). Чтобы узнать об успешном выполнении этого процесса, приложение проверяет наличие флага [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).

## <a name="targets"></a>Цели

Привязка модели попытается найти значения для следующих типов целевых объектов:

* Параметры метода действия контроллера, к которому направлен запрос.
* Параметры Razor метода обработчика страниц, к которому направляется запрос. 
* Открытые свойства контроллера или класса `PageModel`, если задано атрибутами.

### <a name="bindproperty-attribute"></a>Атрибут [BindProperty]

Может применяться к открытому свойству контроллера или класса `PageModel` для привязки модели для этого свойства:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a>Атрибут [BindProperties]

Доступно в ASP.NET Core 2.1 и более поздней версии.  Может применяться к контроллеру или классу `PageModel`, чтобы привязка модели была направлена на все открытые свойства этого класса:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>Привязка модели для HTTP-запросов GET

По умолчанию свойства не привязываются к HTTP-запросам GET. Как правило, для запроса GET вам нужен только параметр идентификатора записи. Идентификатор записи используется для поиска элемента в базе данных. Поэтому не нужно привязывать свойство, которое содержит экземпляр модели. Если вы хотите привязать свойства к данным от запросов GET, задайте для свойства `SupportsGet` значение `true`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>Источники

По умолчанию привязка модели получает данные в виде пар "ключ-значение" из следующих источников в HTTP-запросе:

1. Поля формы
1. Текст запроса (для [контроллеров, имеющих атрибут [ApiController]](xref:web-api/index#binding-source-parameter-inference).)
1. Данные маршрута
1. Параметры строки запроса
1. Отправленные файлы

Для каждого целевого параметра или свойства источники проверяются в порядке, указанном в предыдущем списке. Существует несколько исключений:

* Данные маршрутизации и значения строк запросов используются только для примитивных типов.
* Отправленные файлы привязаны только к типам целевых объектов, которые реализуют `IFormFile` или `IEnumerable<IFormFile>`.

Если источник по умолчанию неверен, используйте один из следующих атрибутов, чтобы указать источник:

* [`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) — Получает значения из строки запроса. 
* [`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) — Получает значения из данных маршрута.
* [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) — Получает значения из опубликованных полей формы.
* [`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) — Получает значения из текста запроса.
* [`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) — Получает значения из заголовков HTTP.

Эти атрибуты:

* Добавляются к свойствам модели по отдельности (не к классу модели), как показано в следующем примере:

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* При необходимости принимают значение имени модели в конструкторе. Этот параметр предоставляется в том случае, если имя свойства не соответствует значению в запросе. Например, значение в запросе может быть заголовком с дефисом в имени, как показано в следующем примере:

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>Атрибут [FromBody]

Примените атрибут `[FromBody]` к параметру, чтобы заполнить его свойства из тела HTTP-запроса. Среда выполнения ASP.NET Core делегирует ответственность за считывание тела форматировщику входных данных. Форматировщики входных данных описываются [далее в этой статье](#input-formatters).

При применении `[FromBody]` к параметру сложного типа все атрибуты источника привязки, применяемые к его свойствам, игнорируются. Например, следующее действие `Create` указывает, что параметр `pet` заполняется из тела:

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

Класс `Pet` указывает, что свойство `Breed` заполняется из параметра строки запроса:

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

В предшествующем примере:

* Атрибут `[FromQuery]` не учитывается.
* Свойство `Breed` не заполняется из параметра строки запроса. 

Форматировщики входных данных считывают только тело и не распознают атрибуты источника привязки. Если подходящее значение найдено в теле, оно используется для заполнения свойства `Breed`.

Не применяют `[FromBody]` к нескольким параметрам в методе действия. После считывания потока запроса форматировщиком входных данных он больше не доступен для повторного чтения для привязки других параметров `[FromBody]`.

### <a name="additional-sources"></a>Дополнительные источники

Исходные данные предоставляются системой привязки модели *поставщиками значений*. Вы можете записать и зарегистрировать пользовательские поставщики значений, которые получают данные для привязки модели из других источников. Например, могут потребоваться данные из cookie s или из состояния сеанса. Для получения данных из нового источника:

* Создайте класс, реализующий `IValueProvider`.
* Создайте класс, реализующий `IValueProviderFactory`.
* Зарегистрируйте класс фабрики в `Startup.ConfigureServices`.

Пример приложения включает в себя [поставщик значений](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) и пример [фабрики](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) , который получает значения из cookie s. Ниже приведен код регистрации в `Startup.ConfigureServices`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

Этот код помещает поставщик пользовательских значений после всех встроенных поставщиков значений.  Чтобы сделать его первым в списке, вызовите `Insert(0, new CookieValueProviderFactory())` вместо `Add`.

## <a name="no-source-for-a-model-property"></a>Отсутствие источника для свойства модели

По умолчанию ошибка состояния модели не создается, если не найдено значение для свойства модели. Свойство получает значение NULL или значение по умолчанию:

* Примитивным типам, допускающим значение NULL, задается значение `null`.
* Типам значений, не допускающим значение NULL, задается значение `default(T)`. Например, параметр `int id` получает значение 0.
* Для сложных типов привязка модели создает экземпляр с помощью конструктора по умолчанию без задания свойств.
* Массивы имеют значение `Array.Empty<T>()`, за исключением массивов `byte[]`, которые имеют значение `null`.

Если состояние модели должно быть недействительным, если в полях формы для свойства модели не найдено ничего, используйте [`[BindRequired]`](#bindrequired-attribute) атрибут.

Обратите внимание, это поведение `[BindRequired]` применяется к привязке модели из опубликованных данных формы, а не к данным JSON или XML в тексте запроса. Данные основного текста запроса обрабатываются [форматировщиками входных данных](#input-formatters).

## <a name="type-conversion-errors"></a>Ошибки преобразования типа

Если источник найден, но его нельзя преобразовать в тип целевого объекта, состояние модели помечается как недопустимое. Параметр или свойство целевого объекта получает значение NULL или значение по умолчанию, как отмечалось в предыдущем разделе.

В контроллере API с атрибутом `[ApiController]` недопустимое состояние модели приводит к автоматическому ответу HTTP 400.

На Razor странице повторно отобразите страницу с сообщением об ошибке:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

Проверка на стороне клиента приводит к перехвату большинства недопустимых данных, которые в противном случае были бы переданы в Razor форму страниц. Эта проверка затрудняет срабатывание выделенного выше кода. Пример приложения включает кнопку **Отправить с неверной датой**, которая помещает недопустимые данные в поле **Дата приема на работу** и отправляет форму. Эта кнопка показывает, как работает код для повторного отображения страницы, если возникла ошибка преобразования данных.

Когда страница отображается повторно приведенным выше кодом, недопустимые входные данные не отображаются в поле формы. Это связано с тем, что свойству модели задано значение NULL или значение по умолчанию. Недопустимые входные данные отображаются в сообщении об ошибке. Но если требуется повторно отобразить неправильные данные в поле формы, возможно, следует сделать свойство модели строкой и выполнить преобразование данных вручную.

Та же стратегия рекомендуется в том случае, если вы не хотите, чтобы ошибки преобразования типов приводили к ошибкам состояния модели. В этом случае следует сделать свойство модели строкой.

## <a name="simple-types"></a>Простые типы

Связыватель модели может преобразовать исходные строки в следующие примитивные типы:

* [Логический](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [DateTime](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [Десятичное число](xref:System.ComponentModel.DecimalConverter)
* [Double](xref:System.ComponentModel.DoubleConverter)
* [Enum](xref:System.ComponentModel.EnumConverter)
* [Guid](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)
* [Single](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)
* [URI](xref:System.UriTypeConverter)
* [Версия](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>Сложные типы

Сложный тип должен иметь открытый конструктор по умолчанию, а также открытые и доступные для записи свойства, подлежащие привязке. Когда происходит привязка модели, класс создается с помощью открытого конструктора по умолчанию. 

Для каждого свойства сложного типа привязка модели ищет в источниках шаблон имени *prefix.property_name*. Если ничего не найдено, он ищет только *property_name* без префикса.

Для привязки к параметру префикс является именем параметра. Для привязки к открытому свойству `PageModel` префикс является именем открытого свойства. Некоторые атрибуты имеют свойство `Prefix`, которое позволяет переопределить использование по умолчанию для имени параметра или свойства.

Предположим, например, что сложный тип принадлежит к следующему классу `Instructor`:

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>Префикс — это имя параметра

Если модель, которую нужно привязать, является параметром с именем `instructorToUpdate`:

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

Привязка модели начинается с поиска источников ключа `instructorToUpdate.ID`. Если он не найден, ищется `ID` без префикса.

### <a name="prefix--property-name"></a>Префикс — это имя свойства

Если модель для привязки является свойством с именем `Instructor` контроллера или класса `PageModel`:

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

Привязка модели начинается с поиска источников ключа `Instructor.ID`. Если он не найден, ищется `ID` без префикса.

### <a name="custom-prefix"></a>Пользовательский префикс

Если модель для привязки — это параметр с именем `instructorToUpdate`, а атрибут `Bind` задает `Instructor` как префикс:

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

Привязка модели начинается с поиска источников ключа `Instructor.ID`. Если он не найден, ищется `ID` без префикса.

### <a name="attributes-for-complex-type-targets"></a>Атрибуты для целевых объектов сложного типа

Несколько встроенных атрибутов доступны для управления привязкой моделей сложных типов:

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> Эти атрибуты влияют на привязку модели, если опубликованные данные формы являются источником значений. Они ***не*** влияют на модули форматирования ввода, которые обрабатывают опубликованные тексты JSON и XML-запросов. Форматировщики входных данных описываются [далее в этой статье](#input-formatters).

### <a name="bind-attribute"></a>Атрибут [Bind]

Может быть применен к классу или параметру метода. Указывает, какие свойства модели должны быть включены в привязку модели. `[Bind]` не ***влияет на*** модули форматирования ввода.

В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается любой метод действия или обработчик:

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается метод `OnPost`:

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

Атрибут `[Bind]` может использоваться для защиты от чрезмерной передачи данных при *создании*. Он не работает в сценариях редактирования, поскольку исключенным свойствам задается значение NULL или значение по умолчанию, но не оставляется значение без изменений. Для защиты от чрезмерной передачи данных рекомендуется использовать модели представлений вместо атрибута `[Bind]`. Дополнительные сведения см. в разделе [Примечание по безопасности о чрезмерной передаче данных](xref:data/ef-mvc/crud#security-note-about-overposting).

### <a name="modelbinder-attribute"></a>Атрибут [Моделбиндер]

<xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> может применяться к типам, свойствам или параметрам. Он позволяет указать тип связывателя модели, используемый для привязки конкретного экземпляра или типа. Пример:

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

`[ModelBinder]`Атрибут также можно использовать для изменения имени свойства или параметра, если оно привязано к модели:

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a>Атрибут [BindRequired]

Может применяться только к свойствам модели, а не к параметрам метода. Приводит к тому, что привязка модели добавляет ошибку состояния модели, если привязка для свойства модели невозможна. Ниже приведен пример:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

Также см. обсуждение атрибута `[Required]` в разделе [Проверка модели](xref:mvc/models/validation#required-attribute).

### <a name="bindnever-attribute"></a>Атрибут [BindNever]

Может применяться только к свойствам модели, а не к параметрам метода. Запрещает привязке модели задавать свойство модели. Ниже приведен пример:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a>Коллекции

Для целевых объектов, которые являются коллекциями примитивных типов, привязка модели ищет совпадения с *parameter_name* или *property_name*. Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса. Пример:

* Предположим, что параметром для привязки является массив с именем `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* Строковые данные формы или запроса могут иметь один из следующих форматов:
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* Следующий формат поддерживается только в данных формы:

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* Для всех перечисленных ранее форматов привязка модели передает массив из двух элементов в параметр `selectedCourses`:

  * selectedCourses[0]=1050
  * selectedCourses[1]=2000

  Форматы данных, которые используют номера нижних индексов (... [0] ... [1] ...), должны нумероваться последовательно, начиная с нуля. Если в нумерации есть пробелы, все элементы после пробела не учитываются. Например, если указаны индексы 0 и 2, а не 0 и 1, второй элемент игнорируется.

## <a name="dictionaries"></a>Словари

Для целевых объектов `Dictionary` привязка модели ищет совпадения с *parameter_name* или *property_name*. Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса. Пример:

* Предположим, что целевой параметр является `Dictionary<int, string>` с именем `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* Опубликованные строковые данные формы или запроса могут выглядеть как один из следующих примеров:

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* Для всех перечисленных ранее форматов привязка модели передает словарь из двух элементов в параметр `selectedCourses`:

  * selectedCourses["1050"]="Chemistry"
  * selectedCourses["2000"]="Economics"
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a>Привязка конструктора и типы записей

Привязка модели требует, чтобы сложные типы имели конструктор без параметров. `System.Text.Json` `Newtonsoft.Json` Модули форматирования ввода и поддерживают десериализацию классов, у которых нет конструктора без параметров. 

В C# 9 появились типы записей, которые представляют собой отличный способ краткого представления данных по сети. ASP.NET Core добавляет поддержку привязки модели и проверку типов записей с помощью одного конструктора:

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

`Person/Index.cshtml`:

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

При проверке типов записей среда выполнения ищет метаданные проверки специально для параметров, а не для свойств.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a>Поведение глобализации для данных маршрутов привязки модели и строк запросов

Поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core:

* обрабатывают значения как имеющие инвариантные язык и региональные параметры.
* Следует ожидать, что URL-адреса имеют инвариантные язык и региональные параметры.

Напротив, значения, поступающие из данных форм, подвергаются преобразованию с учетом языка и региональных параметров. Это сделано намеренно, чтобы URL-адреса были общими в разных языковых стандартах.

Чтобы поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core производили преобразование с учетом языка и региональных параметров, выполните указанные ниже действия.

* наследуют от <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.
* Скопируйте код из [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) или [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)
* Замените [значение языка и региональных параметров](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30), передаваемое в конструктор поставщика значений, на [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)
* Замените метод производства поставщика значений по умолчанию в параметрах MVC на новый:

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a>Специальные типы данных

Существуют некоторые особые типы данных, которые может обрабатывать привязка модели.

### <a name="iformfile-and-iformfilecollection"></a>IFormFile и IFormFileCollection

Переданный файл, включенный в HTTP-запрос.  Также поддерживается `IEnumerable<IFormFile>` для нескольких файлов.

### <a name="cancellationtoken"></a>CancellationToken

При необходимости действия могут быть привязаны `CancellationToken` как параметр. Это привязывает этот <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> сигнал при прерывании соединения, лежащего в основе HTTP-запроса. Действия могут использовать этот параметр для отмены долго выполняющихся асинхронных операций, выполняемых в рамках действий контроллера.

### <a name="formcollection"></a>FormCollection

Используется для извлечения всех значений из опубликованных данных формы.

## <a name="input-formatters"></a>Форматировщики входных данных

Данные в тексте запроса могут быть в формате JSON, XML или другом формате. Для анализа этих данных модель привязки использует *форматировщик входных данных*, настроенный для обработки определенного типа содержимого. По умолчанию ASP.NET Core включает форматировщики входных данных на основе JSON для обработки данных JSON. Вы можете добавить другие форматировщики для других типов содержимого.

ASP.NET Core выбирает форматировщики входных данных на основе атрибута [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute). Если атрибут отсутствует, используется [Заголовок Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).

Чтобы использовать встроенные форматировщики входных данных XML:

* Установите пакет NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.

* В `Startup.ConfigureServices` вызовите <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> или <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* Примените атрибут `Consumes` к классам контроллера или методам действий, которые должны ожидать XML в тексте запроса.

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  Дополнительные сведения см. в разделе [Введение в сериализацию XML](/dotnet/standard/serialization/introducing-xml-serialization).

### <a name="customize-model-binding-with-input-formatters"></a>Настройка привязки модели с помощью форматировщиков входных данных

Форматировщик входных данных полностью отвечает за чтение данных из текста запроса. Чтобы настроить этот процесс, настройте API-интерфейсы, используемые форматировщиками входных данных. В этом разделе описывается, как настроить форматировщик входных данных на основе `System.Text.Json` так, чтобы он понимал настраиваемый тип с именем `ObjectId`. 

Рассмотрим следующую модель, которая содержит настраиваемое свойство `ObjectId` с именем `Id`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

Чтобы настроить процесс привязки модели при использовании `System.Text.Json`, создайте производный класс на основе класса <xref:System.Text.Json.Serialization.JsonConverter%601>:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

Чтобы использовать пользовательский преобразователь, примените к типу атрибут <xref:System.Text.Json.Serialization.JsonConverterAttribute>. В следующем примере тип `ObjectId` настраивается с `ObjectIdConverter` в качестве пользовательского преобразователя:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

Дополнительные сведения см. в статье [How to write custom converters for JSON serialization (marshalling) in .NET](/dotnet/standard/serialization/system-text-json-converters-how-to) (Создание пользовательских преобразователей для сериализации JSON (маршалинг) в .NET).

## <a name="exclude-specified-types-from-model-binding"></a>Исключение указанных типов из привязки модели

Поведение привязки модели и системы проверки определяется классом [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata). Вы можете настроить `ModelMetadata`, добавив поставщик сведений в [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders). Встроенные поставщики сведений доступны для отключения привязки модели или проверки для указанных типов.

Чтобы отключить привязку модели для всех моделей указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> в `Startup.ConfigureServices`. Например, для отключения привязки модели для всех моделей типа `System.Version`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

Чтобы отключить проверку свойств указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> в `Startup.ConfigureServices`. Например, чтобы отключить проверку по свойствам типа `System.Guid`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a>Настраиваемые связыватели модели

Привязку модели можно расширить путем написания пользовательского связывателя модели и с помощью атрибута `[ModelBinder]`, чтобы выбрать его для заданного целевого объекта. Узнайте больше о [пользовательской привязке модели](xref:mvc/advanced/custom-model-binding).

## <a name="manual-model-binding"></a>Привязка модели вручную 

Привязка модели может вызываться вручную с помощью метода <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>. Этот метод определен в классах `ControllerBase` и `PageModel`. Перегрузки метода позволяют задать поставщик префиксов и значений. Этот метод возвращает `false` при сбое привязки модели. Ниже приведен пример:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> использует поставщиков значений для получения данных из текста формы, строки запроса и данных маршрута. `TryUpdateModelAsync` как правило: 

* Используется с Razor страницами и приложениями MVC с использованием контроллеров и представлений для предотвращения перебора.
* не используется с веб-API, если только не используется из данных формы, строк запроса и данных маршрута. Конечные точки веб-API, которые используют JSON, применяют [форматировщики входных данных](#input-formatters) для десериализации текста запроса в объект.

Дополнительные сведения см. в разделе [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).

## <a name="fromservices-attribute"></a>Атрибут [FromServices]

Имя этого атрибута соответствует шаблону атрибутов привязки модели, которые указывают источник данных. Но это не связано с привязкой данных от поставщика значений. Он получает экземпляр типа из контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection). Он предназначен для предоставления альтернативы внедрению через конструктор, когда вам нужна служба, только если вызывается конкретный метод.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В этой статье объясняется, что такое привязка модели, как это работает и как настроить ее поведение.

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/model-binding/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).

## <a name="what-is-model-binding"></a>Что такое привязка модели

Контроллеры и Razor страницы работают с данными, поступающими из HTTP-запросов. Например, данные о маршруте могут предоставлять ключ записи, а опубликованные поля формы могут предоставлять значения для свойств модели. Написание кода для получения этих значений и их преобразования из строк в типы .NET будет утомительной задачей с высоким риском ошибок. Привязка модели позволяет автоматизировать этот процесс. Система привязки модели:

* Извлекает данные из различных источников, таких как данные о маршруте, поля формы и строки запроса.
* Предоставляет данные для контроллеров и Razor страниц в параметрах метода и общих свойствах.
* Преобразует строковые данные в типы .NET.
* Обновляет свойства сложных типов.

## <a name="example"></a>Например, .

Предположим, у вас есть следующий метод действия:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

И приложение получает запрос с этим URL-адресом:

```
http://contoso.com/api/pets/2?DogsOnly=true
```

Привязка модели выполняет следующие действия, после того, как система маршрутизации выберет метод действия:

* Находит первый параметр `GetByID`, целое число с именем `id`.
* Просматривает доступные источники в HTTP-запросе и находит `id` = "2" в данных маршрута.
* Преобразует строку "2" в целое число 2.
* Находит следующий параметр `GetByID`, логическое значение с именем `dogsOnly`.
* Просматривает источники и находит "DogsOnly=true" в строке запроса. Сопоставление имен не учитывает регистр.
* Преобразует строку "true" в логическое значение `true`.

Затем платформа вызывает метод `GetById`, передавая 2 для параметра `id` и `true` для параметра `dogsOnly`.

В приведенном выше примере целевые объекты привязки модели — это параметры методов, которые являются примитивными типами. Целевые объекты также могут быть свойствами сложного типа. После успешной привязки каждого свойства осуществляется [проверка модели](xref:mvc/models/validation) для этого свойства. Записи о данных, привязанных к модели, а также об ошибках привязки или проверки хранятся в [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) или [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState). Чтобы узнать об успешном выполнении этого процесса, приложение проверяет наличие флага [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).

## <a name="targets"></a>Цели

Привязка модели попытается найти значения для следующих типов целевых объектов:

* Параметры метода действия контроллера, к которому направлен запрос.
* Параметры Razor метода обработчика страниц, к которому направляется запрос. 
* Открытые свойства контроллера или класса `PageModel`, если задано атрибутами.

### <a name="bindproperty-attribute"></a>Атрибут [BindProperty]

Может применяться к открытому свойству контроллера или класса `PageModel` для привязки модели для этого свойства:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a>Атрибут [BindProperties]

Доступно в ASP.NET Core 2.1 и более поздней версии.  Может применяться к контроллеру или классу `PageModel`, чтобы привязка модели была направлена на все открытые свойства этого класса:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>Привязка модели для HTTP-запросов GET

По умолчанию свойства не привязываются к HTTP-запросам GET. Как правило, для запроса GET вам нужен только параметр идентификатора записи. Идентификатор записи используется для поиска элемента в базе данных. Поэтому не нужно привязывать свойство, которое содержит экземпляр модели. Если вы хотите привязать свойства к данным от запросов GET, задайте для свойства `SupportsGet` значение `true`:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>Источники

По умолчанию привязка модели получает данные в виде пар "ключ-значение" из следующих источников в HTTP-запросе:

1. Поля формы
1. Текст запроса (для [контроллеров, имеющих атрибут [ApiController]](xref:web-api/index#binding-source-parameter-inference).)
1. Данные маршрута
1. Параметры строки запроса
1. Отправленные файлы

Для каждого целевого параметра или свойства источники проверяются в порядке, указанном в предыдущем списке. Существует несколько исключений:

* Данные маршрутизации и значения строк запросов используются только для примитивных типов.
* Отправленные файлы привязаны только к типам целевых объектов, которые реализуют `IFormFile` или `IEnumerable<IFormFile>`.

Если источник по умолчанию неверен, используйте один из следующих атрибутов, чтобы указать источник:

* [`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) — Получает значения из строки запроса. 
* [`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) — Получает значения из данных маршрута.
* [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) — Получает значения из опубликованных полей формы.
* [`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) — Получает значения из текста запроса.
* [`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) — Получает значения из заголовков HTTP.

Эти атрибуты:

* Добавляются к свойствам модели по отдельности (не к классу модели), как показано в следующем примере:

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* При необходимости принимают значение имени модели в конструкторе. Этот параметр предоставляется в том случае, если имя свойства не соответствует значению в запросе. Например, значение в запросе может быть заголовком с дефисом в имени, как показано в следующем примере:

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>Атрибут [FromBody]

Примените атрибут `[FromBody]` к параметру, чтобы заполнить его свойства из тела HTTP-запроса. Среда выполнения ASP.NET Core делегирует ответственность за считывание тела форматировщику входных данных. Форматировщики входных данных описываются [далее в этой статье](#input-formatters).

При применении `[FromBody]` к параметру сложного типа все атрибуты источника привязки, применяемые к его свойствам, игнорируются. Например, следующее действие `Create` указывает, что параметр `pet` заполняется из тела:

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

Класс `Pet` указывает, что свойство `Breed` заполняется из параметра строки запроса:

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

В предшествующем примере:

* Атрибут `[FromQuery]` не учитывается.
* Свойство `Breed` не заполняется из параметра строки запроса. 

Форматировщики входных данных считывают только тело и не распознают атрибуты источника привязки. Если подходящее значение найдено в теле, оно используется для заполнения свойства `Breed`.

Не применяют `[FromBody]` к нескольким параметрам в методе действия. После считывания потока запроса форматировщиком входных данных он больше не доступен для повторного чтения для привязки других параметров `[FromBody]`.

### <a name="additional-sources"></a>Дополнительные источники

Исходные данные предоставляются системой привязки модели *поставщиками значений*. Вы можете записать и зарегистрировать пользовательские поставщики значений, которые получают данные для привязки модели из других источников. Например, могут потребоваться данные из cookie s или из состояния сеанса. Для получения данных из нового источника:

* Создайте класс, реализующий `IValueProvider`.
* Создайте класс, реализующий `IValueProviderFactory`.
* Зарегистрируйте класс фабрики в `Startup.ConfigureServices`.

Пример приложения включает в себя [поставщик значений](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) и пример [фабрики](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) , который получает значения из cookie s. Ниже приведен код регистрации в `Startup.ConfigureServices`:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

Этот код помещает поставщик пользовательских значений после всех встроенных поставщиков значений.  Чтобы сделать его первым в списке, вызовите `Insert(0, new CookieValueProviderFactory())` вместо `Add`.

## <a name="no-source-for-a-model-property"></a>Отсутствие источника для свойства модели

По умолчанию ошибка состояния модели не создается, если не найдено значение для свойства модели. Свойство получает значение NULL или значение по умолчанию:

* Примитивным типам, допускающим значение NULL, задается значение `null`.
* Типам значений, не допускающим значение NULL, задается значение `default(T)`. Например, параметр `int id` получает значение 0.
* Для сложных типов привязка модели создает экземпляр с помощью конструктора по умолчанию без задания свойств.
* Массивы имеют значение `Array.Empty<T>()`, за исключением массивов `byte[]`, которые имеют значение `null`.

Если состояние модели должно быть недействительным, если в полях формы для свойства модели не найдено ничего, используйте [`[BindRequired]`](#bindrequired-attribute) атрибут.

Обратите внимание, это поведение `[BindRequired]` применяется к привязке модели из опубликованных данных формы, а не к данным JSON или XML в тексте запроса. Данные основного текста запроса обрабатываются [форматировщиками входных данных](#input-formatters).

## <a name="type-conversion-errors"></a>Ошибки преобразования типа

Если источник найден, но его нельзя преобразовать в тип целевого объекта, состояние модели помечается как недопустимое. Параметр или свойство целевого объекта получает значение NULL или значение по умолчанию, как отмечалось в предыдущем разделе.

В контроллере API с атрибутом `[ApiController]` недопустимое состояние модели приводит к автоматическому ответу HTTP 400.

На Razor странице повторно отобразите страницу с сообщением об ошибке:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

Проверка на стороне клиента приводит к перехвату большинства недопустимых данных, которые в противном случае были бы переданы в Razor форму страниц. Эта проверка затрудняет срабатывание выделенного выше кода. Пример приложения включает кнопку **Отправить с неверной датой**, которая помещает недопустимые данные в поле **Дата приема на работу** и отправляет форму. Эта кнопка показывает, как работает код для повторного отображения страницы, если возникла ошибка преобразования данных.

Когда страница отображается повторно приведенным выше кодом, недопустимые входные данные не отображаются в поле формы. Это связано с тем, что свойству модели задано значение NULL или значение по умолчанию. Недопустимые входные данные отображаются в сообщении об ошибке. Но если требуется повторно отобразить неправильные данные в поле формы, возможно, следует сделать свойство модели строкой и выполнить преобразование данных вручную.

Та же стратегия рекомендуется в том случае, если вы не хотите, чтобы ошибки преобразования типов приводили к ошибкам состояния модели. В этом случае следует сделать свойство модели строкой.

## <a name="simple-types"></a>Простые типы

Связыватель модели может преобразовать исходные строки в следующие примитивные типы:

* [Логический](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [DateTime](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [Десятичное число](xref:System.ComponentModel.DecimalConverter)
* [Double](xref:System.ComponentModel.DoubleConverter)
* [Enum](xref:System.ComponentModel.EnumConverter)
* [Guid](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)
* [Single](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)
* [URI](xref:System.UriTypeConverter)
* [Версия](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>Сложные типы

Сложный тип должен иметь открытый конструктор по умолчанию, а также открытые и доступные для записи свойства, подлежащие привязке. Когда происходит привязка модели, класс создается с помощью открытого конструктора по умолчанию. 

Для каждого свойства сложного типа привязка модели ищет в источниках шаблон имени *prefix.property_name*. Если ничего не найдено, он ищет только *property_name* без префикса.

Для привязки к параметру префикс является именем параметра. Для привязки к открытому свойству `PageModel` префикс является именем открытого свойства. Некоторые атрибуты имеют свойство `Prefix`, которое позволяет переопределить использование по умолчанию для имени параметра или свойства.

Предположим, например, что сложный тип принадлежит к следующему классу `Instructor`:

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>Префикс — это имя параметра

Если модель, которую нужно привязать, является параметром с именем `instructorToUpdate`:

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

Привязка модели начинается с поиска источников ключа `instructorToUpdate.ID`. Если он не найден, ищется `ID` без префикса.

### <a name="prefix--property-name"></a>Префикс — это имя свойства

Если модель для привязки является свойством с именем `Instructor` контроллера или класса `PageModel`:

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

Привязка модели начинается с поиска источников ключа `Instructor.ID`. Если он не найден, ищется `ID` без префикса.

### <a name="custom-prefix"></a>Пользовательский префикс

Если модель для привязки — это параметр с именем `instructorToUpdate`, а атрибут `Bind` задает `Instructor` как префикс:

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

Привязка модели начинается с поиска источников ключа `Instructor.ID`. Если он не найден, ищется `ID` без префикса.

### <a name="attributes-for-complex-type-targets"></a>Атрибуты для целевых объектов сложного типа

Несколько встроенных атрибутов доступны для управления привязкой моделей сложных типов:

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> Эти атрибуты влияют на привязку модели, если опубликованные данные формы являются источником значений. Они не влияют на форматировщики входных данных, которые обрабатывают опубликованные тексты запросов JSON и XML. Форматировщики входных данных описываются [далее в этой статье](#input-formatters).
>
> Также см. обсуждение атрибута `[Required]` в разделе [Проверка модели](xref:mvc/models/validation#required-attribute).

### <a name="bindrequired-attribute"></a>Атрибут [BindRequired]

Может применяться только к свойствам модели, а не к параметрам метода. Приводит к тому, что привязка модели добавляет ошибку состояния модели, если привязка для свойства модели невозможна. Ниже приведен пример:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a>Атрибут [BindNever]

Может применяться только к свойствам модели, а не к параметрам метода. Запрещает привязке модели задавать свойство модели. Ниже приведен пример:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a>Атрибут [Bind]

Может быть применен к классу или параметру метода. Указывает, какие свойства модели должны быть включены в привязку модели.

В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается любой метод действия или обработчик:

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается метод `OnPost`:

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

Атрибут `[Bind]` может использоваться для защиты от чрезмерной передачи данных при *создании*. Он не работает в сценариях редактирования, поскольку исключенным свойствам задается значение NULL или значение по умолчанию, но не оставляется значение без изменений. Для защиты от чрезмерной передачи данных рекомендуется использовать модели представлений вместо атрибута `[Bind]`. Дополнительные сведения см. в разделе [Примечание по безопасности о чрезмерной передаче данных](xref:data/ef-mvc/crud#security-note-about-overposting).

## <a name="collections"></a>Коллекции

Для целевых объектов, которые являются коллекциями примитивных типов, привязка модели ищет совпадения с *parameter_name* или *property_name*. Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса. Пример:

* Предположим, что параметром для привязки является массив с именем `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* Строковые данные формы или запроса могут иметь один из следующих форматов:
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* Следующий формат поддерживается только в данных формы:

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* Для всех перечисленных ранее форматов привязка модели передает массив из двух элементов в параметр `selectedCourses`:

  * selectedCourses[0]=1050
  * selectedCourses[1]=2000

  Форматы данных, которые используют номера нижних индексов (... [0] ... [1] ...), должны нумероваться последовательно, начиная с нуля. Если в нумерации есть пробелы, все элементы после пробела не учитываются. Например, если указаны индексы 0 и 2, а не 0 и 1, второй элемент игнорируется.

## <a name="dictionaries"></a>Словари

Для целевых объектов `Dictionary` привязка модели ищет совпадения с *parameter_name* или *property_name*. Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса. Пример:

* Предположим, что целевой параметр является `Dictionary<int, string>` с именем `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* Опубликованные строковые данные формы или запроса могут выглядеть как один из следующих примеров:

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* Для всех перечисленных ранее форматов привязка модели передает словарь из двух элементов в параметр `selectedCourses`:

  * selectedCourses["1050"]="Chemistry"
  * selectedCourses["2000"]="Economics"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a>Поведение глобализации для данных маршрутов привязки модели и строк запросов

Поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core:

* обрабатывают значения как имеющие инвариантные язык и региональные параметры.
* Следует ожидать, что URL-адреса имеют инвариантные язык и региональные параметры.

Напротив, значения, поступающие из данных форм, подвергаются преобразованию с учетом языка и региональных параметров. Это сделано намеренно, чтобы URL-адреса были общими в разных языковых стандартах.

Чтобы поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core производили преобразование с учетом языка и региональных параметров, выполните указанные ниже действия.

* наследуют от <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.
* Скопируйте код из [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) или [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)
* Замените [значение языка и региональных параметров](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30), передаваемое в конструктор поставщика значений, на [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)
* Замените метод производства поставщика значений по умолчанию в параметрах MVC на новый:

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a>Специальные типы данных

Существуют некоторые особые типы данных, которые может обрабатывать привязка модели.

### <a name="iformfile-and-iformfilecollection"></a>IFormFile и IFormFileCollection

Переданный файл, включенный в HTTP-запрос.  Также поддерживается `IEnumerable<IFormFile>` для нескольких файлов.

### <a name="cancellationtoken"></a>CancellationToken

используется для отмены действия в асинхронных контроллерах.

### <a name="formcollection"></a>FormCollection

Используется для извлечения всех значений из опубликованных данных формы.

## <a name="input-formatters"></a>Форматировщики входных данных

Данные в тексте запроса могут быть в формате JSON, XML или другом формате. Для анализа этих данных модель привязки использует *форматировщик входных данных*, настроенный для обработки определенного типа содержимого. По умолчанию ASP.NET Core включает форматировщики входных данных на основе JSON для обработки данных JSON. Вы можете добавить другие форматировщики для других типов содержимого.

ASP.NET Core выбирает форматировщики входных данных на основе атрибута [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute). Если атрибут отсутствует, используется [Заголовок Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).

Чтобы использовать встроенные форматировщики входных данных XML:

* Установите пакет NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.

* В `Startup.ConfigureServices` вызовите <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> или <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* Примените атрибут `Consumes` к классам контроллера или методам действий, которые должны ожидать XML в тексте запроса.

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  Дополнительные сведения см. в разделе [Введение в сериализацию XML](/dotnet/standard/serialization/introducing-xml-serialization).

## <a name="exclude-specified-types-from-model-binding"></a>Исключение указанных типов из привязки модели

Поведение привязки модели и системы проверки определяется классом [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata). Вы можете настроить `ModelMetadata`, добавив поставщик сведений в [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders). Встроенные поставщики сведений доступны для отключения привязки модели или проверки для указанных типов.

Чтобы отключить привязку модели для всех моделей указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> в `Startup.ConfigureServices`. Например, для отключения привязки модели для всех моделей типа `System.Version`:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

Чтобы отключить проверку свойств указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> в `Startup.ConfigureServices`. Например, чтобы отключить проверку по свойствам типа `System.Guid`:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a>Настраиваемые связыватели модели

Привязку модели можно расширить путем написания пользовательского связывателя модели и с помощью атрибута `[ModelBinder]`, чтобы выбрать его для заданного целевого объекта. Узнайте больше о [пользовательской привязке модели](xref:mvc/advanced/custom-model-binding).

## <a name="manual-model-binding"></a>Привязка модели вручную

Привязка модели может вызываться вручную с помощью метода <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>. Этот метод определен в классах `ControllerBase` и `PageModel`. Перегрузки метода позволяют задать поставщик префиксов и значений. Этот метод возвращает `false` при сбое привязки модели. Ниже приведен пример:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a>Атрибут [FromServices]

Имя этого атрибута соответствует шаблону атрибутов привязки модели, которые указывают источник данных. Но это не связано с привязкой данных от поставщика значений. Он получает экземпляр типа из контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection). Он предназначен для предоставления альтернативы внедрению через конструктор, когда вам нужна служба, только если вызывается конкретный метод.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
