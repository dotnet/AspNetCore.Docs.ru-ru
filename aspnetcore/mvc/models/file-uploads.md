---
title: Передача файлов в ASP.NET Core
author: rick-anderson
description: Сведения об использовании привязки модели и потоковой передачи для передачи файлов в ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
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
uid: mvc/models/file-uploads
ms.openlocfilehash: 90bde63ac94ba3fd29a067962989cf773ec613db
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587247"
---
# <a name="upload-files-in-aspnet-core"></a>Передача файлов в ASP.NET Core

По [Стив Смит](https://ardalis.com/) и [рутжер](https://github.com/rutix)

::: moniker range=">= aspnetcore-5.0"

Действия ASP.NET Core поддерживают передачу одного или нескольких файлов с помощью привязки модели с буферизацией для небольших файлов или потоковой передачи без буферизации для более крупных файлов.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Вопросы безопасности

Необходимо соблюдать осторожность при предоставлении пользователям возможности отправки файлов на сервер. Злоумышленники могут попытаться:

* Выполнить атаку типа [отказ в обслуживании](/windows-hardware/drivers/ifs/denial-of-service).
* Передать вирусы и вредоносные программы.
* Нарушить безопасность сетей и серверов другими способами.

Ниже приведены некоторые действия по обеспечению безопасности, которые снижают вероятность успешных атак.

* Передавайте файлы в выделенную область для отправки файлов, желательно не на системный диск. Использование выделенного расположения упрощает применение мер безопасности к отправленным файлам. Отключите разрешения на выполнение для расположения отправки файла.&dagger;
* **Не** сохраняйте переданные файлы в дереве каталогов, где находится приложение.&dagger;
* Используйте безопасное имя файла, определяемое приложением. Не используйте имя файла, предоставленное пользователем, или имя ненадежного файла отправленного файла. &dagger; HTML кодирует ненадежное имя файла при его отображении. Например, запись имени файла или отображение в пользовательском интерфейсе ( Razor автоматически кодируется в формате HTML).
* Разрешите только утвержденные расширения файлов для спецификации на проектирование приложения.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Убедитесь, что на сервере выполняются проверки на стороне клиента. &dagger; Проверки на стороне клиента легко обойти.
* Проверьте размер отправленного файла. Установите максимальный предельный размер, чтобы предотвратить передачу больших объемов данных.&dagger;
* Если файлы не должны перезаписываться переданным файлом с тем же именем, перед отправкой файла проверьте его имя в базе данных или физическом хранилище.
* **Запустите сканер для проверки отправляемого содержимого на наличие вирусов и вредоносных программ, прежде чем сохранять файл.**

&dagger;Пример приложения демонстрирует подход, который соответствует критериям.

> [!WARNING]
> Отправка в систему вредоносного кода часто является первым шагом перед выполнением кода, который может:
>
> * полностью получить контроль над системой;
> * перезагрузить систему так, что она окажется в неработоспособном состоянии;
> * скомпрометировать пользовательские или системные данные;
> * применить граффити к открытому интерфейсу.
>
> Сведения об уменьшении контактной зоны атаки во время приема файлов от пользователей см. в следующих ресурсах:
>
> * [Неограниченная отправка файлов](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Безопасность Azure: убедитесь в наличии надлежащих мер контроля при получении файлов от пользователей](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Дополнительные сведения о реализации мер безопасности, включая примеры из примера приложения, см. в статье [Передача файлов в ASP.NET Core](#validation).

## <a name="storage-scenarios"></a>Сценарии использования хранилища

К общим вариантам хранилища файлов относятся следующие:

* База данных

  * В случае отправки небольших файлов база данных часто работает быстрее, чем физическое хранилище (файловая система или сетевая папка).
  * База данных часто более удобна по сравнению с вариантами физического хранилища, так как получение записи из базы пользовательских данных может одновременно предоставить содержимое файла (например, изображение аватара).
  * Эксплуатация базы данных потенциально дешевле, чем использование службы хранилища данных.

* Физическое хранилище (файловая система или сетевая папка).

  * Обработка передачи больших файлов:
    * Ограничения базы данных могут ограничивать размер передачи.
    * Физическое хранилище часто менее экономически выгодно, чем хранилище в базе данных.
  * Эксплуатация физического хранилища потенциально дешевле, чем использование службы хранилища данных.
  * Процесс приложения должен иметь разрешения на чтение и запись для места хранения. **Никогда не предоставляйте разрешение на выполнение.**

* Служба хранилища данных (например, хранилище [BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)).

  * Обычно службы обеспечивают улучшенную масштабируемость и устойчивость по сравнению с локальными решениями, которые обычно подвержены единым точкам отказа.
  * Затраты на использование служб обычно ниже в сценариях с крупномасштабной инфраструктурой хранения.

  Дополнительные сведения см. в разделе [Краткое руководство. Создание большого двоичного объекта в хранилище объектов с помощью .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Сценарии передачи файлов

Есть два распространенных подхода к передаче файлов — буферизация и потоковая передача.

**ответов**

Весь файл считывается в <xref:Microsoft.AspNetCore.Http.IFormFile> (представление файла на C#, используемого для обработки или сохранения файла).

Потребление ресурсов (диска, памяти) при передаче файлов зависит от количества и размера одновременно передаваемых файлов. При попытке приложения поместить в буфер слишком много файлов может произойти аварийное завершение работы сайта из-за нехватки памяти или места на диске. Если размер или частота отправки файлов исчерпывают ресурсы приложения, используйте потоковую передачу.

> [!NOTE]
> Один буферизованный файл размером свыше 64 КБ перемещается из памяти во временный файл на диске.

Буферизация небольших файлов описана в следующих разделах этой статьи:

* [Физическое хранилище](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [База данных](#upload-small-files-with-buffered-model-binding-to-a-database)

**Потоковая передача**

Файл можно получить с помощью составного запроса. Затем он обрабатывается или сохраняется приложением напрямую. Потоковая передача повышает производительность не значительно. При отправке файлов потоковая передача снижает нагрузку на память или на место на диске.

Потоковая передача больших файлов рассматривается в разделе [Передача больших файлов с помощью потоковой передачи](#upload-large-files-with-streaming).

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Передача небольших файлов с привязкой буферизованной модели к физическому хранилищу

Для передачи небольших файлов можно применить составную форму или сформировать запрос POST на языке JavaScript.

В следующем примере показано использование Razor формы страниц для передачи одного файла (*pages/буффередсинглефилеуплоадфисикал. cshtml* в примере приложения):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

Следующий пример аналогичен предыдущему примеру, за исключением следующего:

* JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) используется для отправки данных формы.
* Проверка не выполняется.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Для выполнения отправки формы в JavaScript для клиентов, которые [не поддерживают Fetch API](https://caniuse.com/#feat=fetch), используйте один из следующих подходов:

* Используйте функцию Fetch Polyfill (например, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Используйте ключевое слово `XMLHttpRequest`. Пример:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Для поддержки передачи файлов в HTML-формах должен указываться тип кодировки `enctype` со значением `multipart/form-data`.

Для элемента ввода `files`, поддерживающего отправку нескольких файлов, в элементе `<input>` необходимо указать атрибут `multiple`:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Доступ к отдельным файлам, переданным на сервер, можно получать посредством [привязки модели](xref:mvc/models/model-binding) с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>. В примере приложения показано несколько отправок буферизованных файлов для баз данных и физических хранилищ.

<a name="filename"></a>

> [!WARNING]
> **Не** используйте свойство `FileName` объекта <xref:Microsoft.AspNetCore.Http.IFormFile>, кроме как для отображения и ведения журнала. При отображении или ведении журнала кодируйте имя файла в формате HTML. Злоумышленник может предоставить имя вредоносного файла, включая полные или относительные пути. Приложения должны:
>
> * удалить путь из имени файла, указываемого пользователем;
> * сохранить имя файла, закодированное в формате HTML, откуда был удален путь, для пользовательского интерфейса или ведения журнала.
> * создать случайное имя файла для хранения.
>
> Следующий код удаляет путь из имени файла:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> В приведенных выше примерах не учитываются вопросы безопасности. Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/).
>
> * [Вопросы безопасности](#security-considerations)
> * [Проверка](#validation)

При отправке файлов с помощью привязки модели и <xref:Microsoft.AspNetCore.Http.IFormFile> метод действия может принимать следующие файлы:

* Один файл <xref:Microsoft.AspNetCore.Http.IFormFile>.
* Любая из следующих коллекций, представляющих несколько файлов:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Числ](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Привязка сопоставляет файлы форм по имени. Например, значение HTML `name` в `<input type="file" name="formFile">` должно соответствовать привязанному к C# параметру или свойству (`FormFile`). Дополнительные сведения см. в разделе [Сопоставление значения атрибута имени и имени параметра метода POST](#match-name-attribute-value-to-parameter-name-of-post-method).

В следующем примере происходит следующее:

* Циклично отправляет один или несколько передаваемых файлов.
* Использует метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы вернуть полный путь к файлу, включая его имя. 
* Сохраняет файлы в локальную файловую систему, используя имя файла, созданное приложением.
* Возвращает общее число и размер отправленных файлов.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Чтобы создать имя файла без пути, используйте `Path.GetRandomFileName`. В следующем примере путь получен из конфигурации:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Передаваемый в  путь <xref:System.IO.FileStream> *должен* содержать имя файла. Если имя файла не указано, в среде выполнения возникает исключение <xref:System.UnauthorizedAccessException>.

Файлы, передаваемые с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>, буферизуются в памяти или на диске на сервере перед обработкой. Внутри метода действия содержимое <xref:Microsoft.AspNetCore.Http.IFormFile> доступно в виде <xref:System.IO.Stream>. Помимо локальной файловой системы, файлы можно сохранять в сетевой папке или в службе хранилища файлов, например в хранилище [BLOB-объектов Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Еще один пример, который перебирает несколько файлов для отправки и использует надежные имена файлов, см. в файле *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* в примере приложения.

> [!WARNING]
> Метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) вызывает исключение <xref:System.IO.IOException> в случае создания более чем 65 535 файлов без удаления предыдущих временных файлов. Ограничение в 65 535 файлов предусмотрено для каждого сервера. Дополнительные сведения об этом ограничении в ОС Windows см. в примечаниях в следующих разделах:
>
> * [Функция GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks);
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Передача небольших файлов с привязкой буферизованной модели к базе данных

Для сохранения данных двоичных файлов в базе данных с помощью [Entity Framework](/ef/core/index) определите для сущности свойство массива <xref:System.Byte>:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Укажите свойство модели страницы для класса, который содержит <xref:Microsoft.AspNetCore.Http.IFormFile>:

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> можно использовать непосредственно как параметр метода действия или свойство модели привязки. В предыдущем примере используется свойство модели привязки.

`FileUpload`Используется в Razor форме страниц:

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

При публикации формы на сервере скопируйте <xref:Microsoft.AspNetCore.Http.IFormFile> в поток и сохраните его в базе данных в виде массива байтов. В следующем примере `_dbContext` сохраняет контекст базы данных приложения:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

Предыдущий пример похож на сценарий, продемонстрированный в примере приложения:

* *Pages/BufferedSingleFileUploadDb.cshtml*;
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*.

> [!WARNING]
> При сохранении двоичных данных в реляционных базах данных следует соблюдать осторожность, так как это может отрицательно сказаться на производительности.
>
> Свойство `FileName` параметра <xref:Microsoft.AspNetCore.Http.IFormFile> требует обязательной проверки. Свойство `FileName` следует использовать только в целях вывода и только после HTML-кодирования.
>
> В приведенных выше примерах не учитываются вопросы безопасности. Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/).
>
> * [Вопросы безопасности](#security-considerations)
> * [Проверка](#validation)

### <a name="upload-large-files-with-streaming"></a>Передача больших файлов с помощью потоковой передачи

В приведенном ниже примере демонстрируется использование JavaScript для потоковой передачи файла в действие контроллера. Токен против подделки файла создается с помощью пользовательского атрибута фильтра и передается в заголовках HTTP клиента, а не в теле запроса. Так как метод действия обрабатывает передаваемые данные напрямую, привязка модели формы отключается другим пользовательским фильтром. Внутри действия содержимое формы считывается с помощью объекта `MultipartReader`, который считывает каждый объект `MultipartSection` по отдельности, обрабатывая файл или сохраняя содержимое. После считывания составных разделов действие выполняет собственную привязку модели.

Начальный ответ страницы загружает форму и сохраняет токен подделки в cookie (с помощью `GenerateAntiforgeryTokenCookieAttribute` атрибута). Для установки с помощью маркера запроса атрибут использует встроенную поддержку защиты от [подделки](xref:security/anti-request-forgery) ASP.NET Core cookie .

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Для отключения привязки модели используется `DisableFormValueModelBindingAttribute`:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

В примере приложения `GenerateAntiforgeryTokenCookieAttribute` и применяются в `DisableFormValueModelBindingAttribute` качестве фильтров к моделям приложений страницы `/StreamedSingleFileUploadDb` и `/StreamedSingleFileUploadPhysical` в `Startup.ConfigureServices` с использованием [ Razor соглашений о страницах](xref:razor-pages/razor-pages-conventions).

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

Так как привязка модели не считывает форму, параметры, привязанные из формы, не привязываются (запросы, маршруты и заголовки продолжают работать). Метод действия работает напрямую со свойством `Request`. Для считывания каждого раздела служит объект `MultipartReader`. Данные "ключ — значение" хранятся в `KeyValueAccumulator`. После считывания составных разделов содержимое `KeyValueAccumulator` используется для привязки данных формы к типу модели.

Полный метод `StreamingController.UploadDatabase` для потоковой передачи в базу данных с EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Полный метод `StreamingController.UploadPhysical` для потоковой передачи в физическое расположение:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

В примере приложения проверки обрабатываются с помощью `FileHelpers.ProcessStreamedFile`.

## <a name="validation"></a>Проверка

В классе `FileHelpers` примера приложения демонстрируется несколько проверок буферизованного файла <xref:Microsoft.AspNetCore.Http.IFormFile> и потоковой передачи файлов. Сведения об обработке буферизованных файлов <xref:Microsoft.AspNetCore.Http.IFormFile> в примере приложения см. в описании метода `ProcessFormFile` в файле *Utilities/FileHelpers.cs*. Сведения об обработке потоковых файлов см. в описании метода `ProcessStreamedFile` в том же файле.

> [!WARNING]
> Методы обработки проверки, показанные в примере приложения, не проверяют содержимое отправленных файлов. В большинстве рабочих сценариев в файле применяется API сканирования на наличие вирусов и вредоносных программ, прежде чем сделать файл доступным для пользователей или других систем.
>
> Хотя пример в разделе содержит рабочий пример методов проверки, не следует реализовывать класс `FileHelpers` в рабочем приложении, кроме таких случаев:
>
> * Вы полностью разбираетесь в реализации.
> * Вы изменяете реализацию соответствующим образом для среды и спецификаций приложения.
>
> **Никогда не реализуйте код безопасности в приложении, не выполнив эти требования.**

### <a name="content-validation"></a>Проверка содержимого

**Используйте сторонний API сканирования на наличие вирусов и вредоносных программ для отправленного содержимого.**

Сканирование файлов требует использования ресурсов сервера в сценариях с большими объемами данных. Если производительность обработки запросов снижается из-за сканирования файлов, рассмотрите возможность разгрузки сканирования путем использования [фоновой службы](xref:fundamentals/host/hosted-services), возможно, службы, которая работает на сервере, отличном от сервера приложения. Как правило, передаваемые файлы хранятся в карантинной области до тех пор, пока фоновый сканер не проверит их на наличие вирусов. При передаче файл перемещается в нормальное место хранения файлов. Эти действия обычно выполняются вместе с записью базы данных, которая указывает состояние сканирования файла. Благодаря такому подходу приложение и сервер приложений остаются в режиме реагирования на запросы.

### <a name="file-extension-validation"></a>Проверка расширения файла

Расширение переданного файла должно проверяться в соответствии со списком разрешенных расширений. Пример:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Проверка подписи файла

Подпись файла определяется по первым нескольким байтам в начале файла. Эти байты можно использовать, чтобы указать, совпадает ли расширение с содержимым файла. Пример приложения проверяет подписи файлов на соответствие нескольким распространенным типам файлов. В следующем примере проверяется подпись файла для изображения JPEG:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Дополнительные сведения о получении дополнительных подписей файлов см. [по этой ссылке](https://www.filesignatures.net/) и в официальных спецификациях файла.

### <a name="file-name-security"></a>Безопасность имени файла

Никогда не используйте для хранения файла в физическом хранилище имя, предоставляемое клиентом. Создайте надежное имя файла с помощью [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) или [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы получить полный путь (включая имя файла) для временного хранилища.

Razor автоматически кодирует значения свойств для вывода. Ниже приведен безопасный код, который можно использовать.

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

За пределами Razor , всегда следует <xref:System.Net.WebUtility.HtmlEncode*> содержимое имени файла из запроса пользователя.

Во многих реализациях следует включать проверку существования файла. В противном случае файл перезаписывается файлом с тем же именем. Предоставьте дополнительную логику для соответствия спецификациям приложения.

### <a name="size-validation"></a>Проверка размера

Ограничьте размер передаваемых файлов.

В примере приложения размер файла ограничен 2 МБ (указывается в байтах). Ограничение предоставляется через [конфигурацию](xref:fundamentals/configuration/index) из *appsettings.json* файла:

```json
{
  "FileSizeLimit": 2097152
}
```

В классы `PageModel` внедряется `FileSizeLimit`.

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Если размер файла превышает ограничение, файл отклоняется:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Сопоставление значения атрибута имени и имени параметра метода POST

В других Razor формах, которые передают данные формы или используют JavaScript `FormData` непосредственно, имя, указанное в элементе формы, или `FormData` должно совпадать с именем параметра в действии контроллера.

В следующем примере:

* При использовании элемента `<input>` атрибуту `name` присваивается значение `battlePlans`:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* При использовании `FormData` в JavaScript для имени задается значение `battlePlans`:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Используйте соответствующее имя для параметра метода C# (`battlePlans`):

* Для Razor метода обработчика страницы страниц с именем `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Для метода действия контроллера POST приложения MVC:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Конфигурация сервера и приложения

### <a name="multipart-body-length-limit"></a>Ограничение длины составного текста

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> устанавливает ограничение длины каждого составного текста. Разделы формы, превышающие это ограничение, вызовут <xref:System.IO.InvalidDataException> при синтаксическом анализе. Значение по умолчанию — 134 217 728 байт (128 МБ). Настройте ограничение с помощью параметра <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> в `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> используется для настройки <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> для одной страницы или действия.

В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

В Razor приложении "страницы" или в приложении MVC примените фильтр к модели страницы или методу действия:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Максимальный размер текста запроса Kestrel

Для приложений, размещенных в Kestrel, по умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ. Настройте ограничение с помощью параметра сервера Kestrel [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size):

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> используется для настройки [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) для одной страницы или действия.

В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

В Razor приложении Pages или MVC примените фильтр к классу обработчика страницы или методу действия:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`Можно также применить с помощью [`@attribute`](xref:mvc/views/razor#attribute) Razor директивы:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Другие ограничения Kestrel

Другие ограничения Kestrel могут применяться для приложений, размещенных в Kestrel:

* [максимальное число клиентских подключений;](xref:fundamentals/servers/kestrel/options#maximum-client-connections)
* [Скорость передачи данных запросов и ответов](xref:fundamentals/servers/kestrel/options#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

Ограничение запроса по умолчанию ( `maxAllowedContentLength` ) составляет 30 000 000 байт, что составляет примерно 28,6 МБ. Настройка ограничения в `web.config` файле. В следующем примере для ограничения установлено значение 50 МБ (52 428 800 байт):

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

`maxAllowedContentLength`Параметр применяется только к службам IIS. Дополнительные сведения см. в разделе [ограничения `<requestLimits>` запросов ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

## <a name="troubleshoot"></a>Диагностика

Ниже описываются некоторые распространенные проблемы, которые возникают при передаче файлов, и возможные способы их решения.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Ошибка "Не найдено" при развертывании на сервере IIS

Следующая ошибка свидетельствует о том, что размер передаваемых файлов превышает настроенное на сервере ограничение длины содержимого:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Дополнительные сведения см. в описании [IIS](#iis).

### <a name="connection-failure"></a>Сбой подключения

Ошибка соединения и сброс соединения с сервером, скорее всего, означают, что размер отправленного файла превышает максимальную длину текста запроса Kestrel. Дополнительные сведения см. в разделе [Максимальный размер текста запроса Kestrel](#kestrel-maximum-request-body-size). Может также потребоваться настроить ограничения подключений клиентов Kestrel.

### <a name="null-reference-exception-with-iformfile"></a>Исключение, связанное с пустой ссылкой в IFormFile

Если контроллер принимает передаваемые файлы с помощью <xref:Microsoft.AspNetCore.Http.IFormFile>, но значение равно `null`, проверьте, указан ли в HTML-форме атрибут `enctype` со значением `multipart/form-data`. Если этот атрибут не задан для элемента `<form>`, передача файлов происходить не будет и все связанные аргументы <xref:Microsoft.AspNetCore.Http.IFormFile> будут иметь значение `null`. Убедитесь также, что [именование передачи в данных формы совпадает с именованием приложения](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Поток слишком длинный

В примерах в этом разделе <xref:System.IO.MemoryStream> используется для хранения содержимого отправленного файла. Максимальный размер `MemoryStream` ограничен значением `int.MaxValue`. Если в сценарии передачи файлов в приложении требуется хранить содержимое, размер которого больше 50 МБ, используйте другой подход, не зависящий от одного только `MemoryStream`.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Действия ASP.NET Core поддерживают передачу одного или нескольких файлов с помощью привязки модели с буферизацией для небольших файлов или потоковой передачи без буферизации для более крупных файлов.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Вопросы безопасности

Необходимо соблюдать осторожность при предоставлении пользователям возможности отправки файлов на сервер. Злоумышленники могут попытаться:

* Выполнить атаку типа [отказ в обслуживании](/windows-hardware/drivers/ifs/denial-of-service).
* Передать вирусы и вредоносные программы.
* Нарушить безопасность сетей и серверов другими способами.

Ниже приведены некоторые действия по обеспечению безопасности, которые снижают вероятность успешных атак.

* Передавайте файлы в выделенную область для отправки файлов, желательно не на системный диск. Использование выделенного расположения упрощает применение мер безопасности к отправленным файлам. Отключите разрешения на выполнение для расположения отправки файла.&dagger;
* **Не** сохраняйте переданные файлы в дереве каталогов, где находится приложение.&dagger;
* Используйте безопасное имя файла, определяемое приложением. Не используйте имя файла, предоставленное пользователем, или имя ненадежного файла отправленного файла. &dagger; HTML кодирует ненадежное имя файла при его отображении. Например, запись имени файла или отображение в пользовательском интерфейсе ( Razor автоматически кодируется в формате HTML).
* Разрешите только утвержденные расширения файлов для спецификации на проектирование приложения.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Убедитесь, что на сервере выполняются проверки на стороне клиента. &dagger; Проверки на стороне клиента легко обойти.
* Проверьте размер отправленного файла. Установите максимальный предельный размер, чтобы предотвратить передачу больших объемов данных.&dagger;
* Если файлы не должны перезаписываться переданным файлом с тем же именем, перед отправкой файла проверьте его имя в базе данных или физическом хранилище.
* **Запустите сканер для проверки отправляемого содержимого на наличие вирусов и вредоносных программ, прежде чем сохранять файл.**

&dagger;Пример приложения демонстрирует подход, который соответствует критериям.

> [!WARNING]
> Отправка в систему вредоносного кода часто является первым шагом перед выполнением кода, который может:
>
> * полностью получить контроль над системой;
> * перезагрузить систему так, что она окажется в неработоспособном состоянии;
> * скомпрометировать пользовательские или системные данные;
> * применить граффити к открытому интерфейсу.
>
> Сведения об уменьшении контактной зоны атаки во время приема файлов от пользователей см. в следующих ресурсах:
>
> * [Неограниченная отправка файлов](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Безопасность Azure: убедитесь в наличии надлежащих мер контроля при получении файлов от пользователей](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Дополнительные сведения о реализации мер безопасности, включая примеры из примера приложения, см. в статье [Передача файлов в ASP.NET Core](#validation).

## <a name="storage-scenarios"></a>Сценарии использования хранилища

К общим вариантам хранилища файлов относятся следующие:

* База данных

  * В случае отправки небольших файлов база данных часто работает быстрее, чем физическое хранилище (файловая система или сетевая папка).
  * База данных часто более удобна по сравнению с вариантами физического хранилища, так как получение записи из базы пользовательских данных может одновременно предоставить содержимое файла (например, изображение аватара).
  * Эксплуатация базы данных потенциально дешевле, чем использование службы хранилища данных.

* Физическое хранилище (файловая система или сетевая папка).

  * Обработка передачи больших файлов:
    * Ограничения базы данных могут ограничивать размер передачи.
    * Физическое хранилище часто менее экономически выгодно, чем хранилище в базе данных.
  * Эксплуатация физического хранилища потенциально дешевле, чем использование службы хранилища данных.
  * Процесс приложения должен иметь разрешения на чтение и запись для места хранения. **Никогда не предоставляйте разрешение на выполнение.**

* Служба хранилища данных (например, хранилище [BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)).

  * Обычно службы обеспечивают улучшенную масштабируемость и устойчивость по сравнению с локальными решениями, которые обычно подвержены единым точкам отказа.
  * Затраты на использование служб обычно ниже в сценариях с крупномасштабной инфраструктурой хранения.

  Дополнительные сведения см. в разделе [Краткое руководство. Создание большого двоичного объекта в хранилище объектов с помощью .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Сценарии передачи файлов

Есть два распространенных подхода к передаче файлов — буферизация и потоковая передача.

**ответов**

Весь файл считывается в <xref:Microsoft.AspNetCore.Http.IFormFile> (представление файла на C#, используемого для обработки или сохранения файла).

Потребление ресурсов (диска, памяти) при передаче файлов зависит от количества и размера одновременно передаваемых файлов. При попытке приложения поместить в буфер слишком много файлов может произойти аварийное завершение работы сайта из-за нехватки памяти или места на диске. Если размер или частота отправки файлов исчерпывают ресурсы приложения, используйте потоковую передачу.

> [!NOTE]
> Один буферизованный файл размером свыше 64 КБ перемещается из памяти во временный файл на диске.

Буферизация небольших файлов описана в следующих разделах этой статьи:

* [Физическое хранилище](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [База данных](#upload-small-files-with-buffered-model-binding-to-a-database)

**Потоковая передача**

Файл можно получить с помощью составного запроса. Затем он обрабатывается или сохраняется приложением напрямую. Потоковая передача повышает производительность не значительно. При отправке файлов потоковая передача снижает нагрузку на память или на место на диске.

Потоковая передача больших файлов рассматривается в разделе [Передача больших файлов с помощью потоковой передачи](#upload-large-files-with-streaming).

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Передача небольших файлов с привязкой буферизованной модели к физическому хранилищу

Для передачи небольших файлов можно применить составную форму или сформировать запрос POST на языке JavaScript.

В следующем примере показано использование Razor формы страниц для передачи одного файла (*pages/буффередсинглефилеуплоадфисикал. cshtml* в примере приложения):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

Следующий пример аналогичен предыдущему примеру, за исключением следующего:

* JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) используется для отправки данных формы.
* Проверка не выполняется.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Для выполнения отправки формы в JavaScript для клиентов, которые [не поддерживают Fetch API](https://caniuse.com/#feat=fetch), используйте один из следующих подходов:

* Используйте функцию Fetch Polyfill (например, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Используйте ключевое слово `XMLHttpRequest`. Пример:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Для поддержки передачи файлов в HTML-формах должен указываться тип кодировки `enctype` со значением `multipart/form-data`.

Для элемента ввода `files`, поддерживающего отправку нескольких файлов, в элементе `<input>` необходимо указать атрибут `multiple`:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Доступ к отдельным файлам, переданным на сервер, можно получать посредством [привязки модели](xref:mvc/models/model-binding) с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>. В примере приложения показано несколько отправок буферизованных файлов для баз данных и физических хранилищ.

<a name="filename"></a>

> [!WARNING]
> **Не** используйте свойство `FileName` объекта <xref:Microsoft.AspNetCore.Http.IFormFile>, кроме как для отображения и ведения журнала. При отображении или ведении журнала кодируйте имя файла в формате HTML. Злоумышленник может предоставить имя вредоносного файла, включая полные или относительные пути. Приложения должны:
>
> * удалить путь из имени файла, указываемого пользователем;
> * сохранить имя файла, закодированное в формате HTML, откуда был удален путь, для пользовательского интерфейса или ведения журнала.
> * создать случайное имя файла для хранения.
>
> Следующий код удаляет путь из имени файла:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> В приведенных выше примерах не учитываются вопросы безопасности. Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/).
>
> * [Вопросы безопасности](#security-considerations)
> * [Проверка](#validation)

При отправке файлов с помощью привязки модели и <xref:Microsoft.AspNetCore.Http.IFormFile> метод действия может принимать следующие файлы:

* Один файл <xref:Microsoft.AspNetCore.Http.IFormFile>.
* Любая из следующих коллекций, представляющих несколько файлов:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Числ](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Привязка сопоставляет файлы форм по имени. Например, значение HTML `name` в `<input type="file" name="formFile">` должно соответствовать привязанному к C# параметру или свойству (`FormFile`). Дополнительные сведения см. в разделе [Сопоставление значения атрибута имени и имени параметра метода POST](#match-name-attribute-value-to-parameter-name-of-post-method).

В следующем примере происходит следующее:

* Циклично отправляет один или несколько передаваемых файлов.
* Использует метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы вернуть полный путь к файлу, включая его имя. 
* Сохраняет файлы в локальную файловую систему, используя имя файла, созданное приложением.
* Возвращает общее число и размер отправленных файлов.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Чтобы создать имя файла без пути, используйте `Path.GetRandomFileName`. В следующем примере путь получен из конфигурации:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Передаваемый в  путь <xref:System.IO.FileStream> *должен* содержать имя файла. Если имя файла не указано, в среде выполнения возникает исключение <xref:System.UnauthorizedAccessException>.

Файлы, передаваемые с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>, буферизуются в памяти или на диске на сервере перед обработкой. Внутри метода действия содержимое <xref:Microsoft.AspNetCore.Http.IFormFile> доступно в виде <xref:System.IO.Stream>. Помимо локальной файловой системы, файлы можно сохранять в сетевой папке или в службе хранилища файлов, например в хранилище [BLOB-объектов Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Еще один пример, который перебирает несколько файлов для отправки и использует надежные имена файлов, см. в файле *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* в примере приложения.

> [!WARNING]
> Метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) вызывает исключение <xref:System.IO.IOException> в случае создания более чем 65 535 файлов без удаления предыдущих временных файлов. Ограничение в 65 535 файлов предусмотрено для каждого сервера. Дополнительные сведения об этом ограничении в ОС Windows см. в примечаниях в следующих разделах:
>
> * [Функция GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks);
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Передача небольших файлов с привязкой буферизованной модели к базе данных

Для сохранения данных двоичных файлов в базе данных с помощью [Entity Framework](/ef/core/index) определите для сущности свойство массива <xref:System.Byte>:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Укажите свойство модели страницы для класса, который содержит <xref:Microsoft.AspNetCore.Http.IFormFile>:

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> можно использовать непосредственно как параметр метода действия или свойство модели привязки. В предыдущем примере используется свойство модели привязки.

`FileUpload`Используется в Razor форме страниц:

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

При публикации формы на сервере скопируйте <xref:Microsoft.AspNetCore.Http.IFormFile> в поток и сохраните его в базе данных в виде массива байтов. В следующем примере `_dbContext` сохраняет контекст базы данных приложения:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

Предыдущий пример похож на сценарий, продемонстрированный в примере приложения:

* *Pages/BufferedSingleFileUploadDb.cshtml*;
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*.

> [!WARNING]
> При сохранении двоичных данных в реляционных базах данных следует соблюдать осторожность, так как это может отрицательно сказаться на производительности.
>
> Свойство `FileName` параметра <xref:Microsoft.AspNetCore.Http.IFormFile> требует обязательной проверки. Свойство `FileName` следует использовать только в целях вывода и только после HTML-кодирования.
>
> В приведенных выше примерах не учитываются вопросы безопасности. Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/).
>
> * [Вопросы безопасности](#security-considerations)
> * [Проверка](#validation)

### <a name="upload-large-files-with-streaming"></a>Передача больших файлов с помощью потоковой передачи

В приведенном ниже примере демонстрируется использование JavaScript для потоковой передачи файла в действие контроллера. Токен против подделки файла создается с помощью пользовательского атрибута фильтра и передается в заголовках HTTP клиента, а не в теле запроса. Так как метод действия обрабатывает передаваемые данные напрямую, привязка модели формы отключается другим пользовательским фильтром. Внутри действия содержимое формы считывается с помощью объекта `MultipartReader`, который считывает каждый объект `MultipartSection` по отдельности, обрабатывая файл или сохраняя содержимое. После считывания составных разделов действие выполняет собственную привязку модели.

Начальный ответ страницы загружает форму и сохраняет токен подделки в cookie (с помощью `GenerateAntiforgeryTokenCookieAttribute` атрибута). Для установки с помощью маркера запроса атрибут использует встроенную поддержку защиты от [подделки](xref:security/anti-request-forgery) ASP.NET Core cookie .

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Для отключения привязки модели используется `DisableFormValueModelBindingAttribute`:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

В примере приложения `GenerateAntiforgeryTokenCookieAttribute` и применяются в `DisableFormValueModelBindingAttribute` качестве фильтров к моделям приложений страницы `/StreamedSingleFileUploadDb` и `/StreamedSingleFileUploadPhysical` в `Startup.ConfigureServices` с использованием [ Razor соглашений о страницах](xref:razor-pages/razor-pages-conventions).

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

Так как привязка модели не считывает форму, параметры, привязанные из формы, не привязываются (запросы, маршруты и заголовки продолжают работать). Метод действия работает напрямую со свойством `Request`. Для считывания каждого раздела служит объект `MultipartReader`. Данные "ключ — значение" хранятся в `KeyValueAccumulator`. После считывания составных разделов содержимое `KeyValueAccumulator` используется для привязки данных формы к типу модели.

Полный метод `StreamingController.UploadDatabase` для потоковой передачи в базу данных с EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Полный метод `StreamingController.UploadPhysical` для потоковой передачи в физическое расположение:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

В примере приложения проверки обрабатываются с помощью `FileHelpers.ProcessStreamedFile`.

## <a name="validation"></a>Проверка

В классе `FileHelpers` примера приложения демонстрируется несколько проверок буферизованного файла <xref:Microsoft.AspNetCore.Http.IFormFile> и потоковой передачи файлов. Сведения об обработке буферизованных файлов <xref:Microsoft.AspNetCore.Http.IFormFile> в примере приложения см. в описании метода `ProcessFormFile` в файле *Utilities/FileHelpers.cs*. Сведения об обработке потоковых файлов см. в описании метода `ProcessStreamedFile` в том же файле.

> [!WARNING]
> Методы обработки проверки, показанные в примере приложения, не проверяют содержимое отправленных файлов. В большинстве рабочих сценариев в файле применяется API сканирования на наличие вирусов и вредоносных программ, прежде чем сделать файл доступным для пользователей или других систем.
>
> Хотя пример в разделе содержит рабочий пример методов проверки, не следует реализовывать класс `FileHelpers` в рабочем приложении, кроме таких случаев:
>
> * Вы полностью разбираетесь в реализации.
> * Вы изменяете реализацию соответствующим образом для среды и спецификаций приложения.
>
> **Никогда не реализуйте код безопасности в приложении, не выполнив эти требования.**

### <a name="content-validation"></a>Проверка содержимого

**Используйте сторонний API сканирования на наличие вирусов и вредоносных программ для отправленного содержимого.**

Сканирование файлов требует использования ресурсов сервера в сценариях с большими объемами данных. Если производительность обработки запросов снижается из-за сканирования файлов, рассмотрите возможность разгрузки сканирования путем использования [фоновой службы](xref:fundamentals/host/hosted-services), возможно, службы, которая работает на сервере, отличном от сервера приложения. Как правило, передаваемые файлы хранятся в карантинной области до тех пор, пока фоновый сканер не проверит их на наличие вирусов. При передаче файл перемещается в нормальное место хранения файлов. Эти действия обычно выполняются вместе с записью базы данных, которая указывает состояние сканирования файла. Благодаря такому подходу приложение и сервер приложений остаются в режиме реагирования на запросы.

### <a name="file-extension-validation"></a>Проверка расширения файла

Расширение переданного файла должно проверяться в соответствии со списком разрешенных расширений. Пример:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Проверка подписи файла

Подпись файла определяется по первым нескольким байтам в начале файла. Эти байты можно использовать, чтобы указать, совпадает ли расширение с содержимым файла. Пример приложения проверяет подписи файлов на соответствие нескольким распространенным типам файлов. В следующем примере проверяется подпись файла для изображения JPEG:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Дополнительные сведения о получении дополнительных подписей файлов см. [по этой ссылке](https://www.filesignatures.net/) и в официальных спецификациях файла.

### <a name="file-name-security"></a>Безопасность имени файла

Никогда не используйте для хранения файла в физическом хранилище имя, предоставляемое клиентом. Создайте надежное имя файла с помощью [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) или [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы получить полный путь (включая имя файла) для временного хранилища.

Razor автоматически кодирует значения свойств для вывода. Ниже приведен безопасный код, который можно использовать.

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

За пределами Razor , всегда следует <xref:System.Net.WebUtility.HtmlEncode*> содержимое имени файла из запроса пользователя.

Во многих реализациях следует включать проверку существования файла. В противном случае файл перезаписывается файлом с тем же именем. Предоставьте дополнительную логику для соответствия спецификациям приложения.

### <a name="size-validation"></a>Проверка размера

Ограничьте размер передаваемых файлов.

В примере приложения размер файла ограничен 2 МБ (указывается в байтах). Ограничение предоставляется через [конфигурацию](xref:fundamentals/configuration/index) из *appsettings.json* файла:

```json
{
  "FileSizeLimit": 2097152
}
```

В классы `PageModel` внедряется `FileSizeLimit`.

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Если размер файла превышает ограничение, файл отклоняется:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Сопоставление значения атрибута имени и имени параметра метода POST

В других Razor формах, которые передают данные формы или используют JavaScript `FormData` непосредственно, имя, указанное в элементе формы, или `FormData` должно совпадать с именем параметра в действии контроллера.

В следующем примере:

* При использовании элемента `<input>` атрибуту `name` присваивается значение `battlePlans`:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* При использовании `FormData` в JavaScript для имени задается значение `battlePlans`:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Используйте соответствующее имя для параметра метода C# (`battlePlans`):

* Для Razor метода обработчика страницы страниц с именем `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Для метода действия контроллера POST приложения MVC:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Конфигурация сервера и приложения

### <a name="multipart-body-length-limit"></a>Ограничение длины составного текста

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> устанавливает ограничение длины каждого составного текста. Разделы формы, превышающие это ограничение, вызовут <xref:System.IO.InvalidDataException> при синтаксическом анализе. Значение по умолчанию — 134 217 728 байт (128 МБ). Настройте ограничение с помощью параметра <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> в `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> используется для настройки <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> для одной страницы или действия.

В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

В Razor приложении "страницы" или в приложении MVC примените фильтр к модели страницы или методу действия:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Максимальный размер текста запроса Kestrel

Для приложений, размещенных в Kestrel, по умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ. Настройте ограничение с помощью параметра сервера Kestrel [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size):

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> используется для настройки [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) для одной страницы или действия.

В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

В Razor приложении Pages или MVC примените фильтр к классу обработчика страницы или методу действия:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`Можно также применить с помощью [`@attribute`](xref:mvc/views/razor#attribute) Razor директивы:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Другие ограничения Kestrel

Другие ограничения Kestrel могут применяться для приложений, размещенных в Kestrel:

* [максимальное число клиентских подключений;](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Скорость передачи данных запросов и ответов](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

Ограничение запроса по умолчанию ( `maxAllowedContentLength` ) составляет 30 000 000 байт, что составляет примерно 28,6 МБ. Настройка ограничения в `web.config` файле. В следующем примере для ограничения установлено значение 50 МБ (52 428 800 байт):

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

`maxAllowedContentLength`Параметр применяется только к службам IIS. Дополнительные сведения см. в разделе [ограничения `<requestLimits>` запросов ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Увеличьте максимальный размер текста запроса для HTTP-запроса, задав в параметре <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> `Startup.ConfigureServices` . В следующем примере для ограничения установлено значение 50 МБ (52 428 800 байт):

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

Для получения дополнительной информации см. <xref:host-and-deploy/iis/index#iis-options>.

## <a name="troubleshoot"></a>Устранение неполадок

Ниже описываются некоторые распространенные проблемы, которые возникают при передаче файлов, и возможные способы их решения.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Ошибка "Не найдено" при развертывании на сервере IIS

Следующая ошибка свидетельствует о том, что размер передаваемых файлов превышает настроенное на сервере ограничение длины содержимого:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Дополнительные сведения см. в описании [IIS](#iis).

### <a name="connection-failure"></a>Сбой подключения

Ошибка соединения и сброс соединения с сервером, скорее всего, означают, что размер отправленного файла превышает максимальную длину текста запроса Kestrel. Дополнительные сведения см. в разделе [Максимальный размер текста запроса Kestrel](#kestrel-maximum-request-body-size). Может также потребоваться настроить ограничения подключений клиентов Kestrel.

### <a name="null-reference-exception-with-iformfile"></a>Исключение, связанное с пустой ссылкой в IFormFile

Если контроллер принимает передаваемые файлы с помощью <xref:Microsoft.AspNetCore.Http.IFormFile>, но значение равно `null`, проверьте, указан ли в HTML-форме атрибут `enctype` со значением `multipart/form-data`. Если этот атрибут не задан для элемента `<form>`, передача файлов происходить не будет и все связанные аргументы <xref:Microsoft.AspNetCore.Http.IFormFile> будут иметь значение `null`. Убедитесь также, что [именование передачи в данных формы совпадает с именованием приложения](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Поток слишком длинный

В примерах в этом разделе <xref:System.IO.MemoryStream> используется для хранения содержимого отправленного файла. Максимальный размер `MemoryStream` ограничен значением `int.MaxValue`. Если в сценарии передачи файлов в приложении требуется хранить содержимое, размер которого больше 50 МБ, используйте другой подход, не зависящий от одного только `MemoryStream`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Действия ASP.NET Core поддерживают передачу одного или нескольких файлов с помощью привязки модели с буферизацией для небольших файлов или потоковой передачи без буферизации для более крупных файлов.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Вопросы безопасности

Необходимо соблюдать осторожность при предоставлении пользователям возможности отправки файлов на сервер. Злоумышленники могут попытаться:

* Выполнить атаку типа [отказ в обслуживании](/windows-hardware/drivers/ifs/denial-of-service).
* Передать вирусы и вредоносные программы.
* Нарушить безопасность сетей и серверов другими способами.

Ниже приведены некоторые действия по обеспечению безопасности, которые снижают вероятность успешных атак.

* Передавайте файлы в выделенную область для отправки файлов, желательно не на системный диск. Использование выделенного расположения упрощает применение мер безопасности к отправленным файлам. Отключите разрешения на выполнение для расположения отправки файла.&dagger;
* **Не** сохраняйте переданные файлы в дереве каталогов, где находится приложение.&dagger;
* Используйте безопасное имя файла, определяемое приложением. Не используйте имя файла, предоставленное пользователем, или имя ненадежного файла отправленного файла. &dagger; HTML кодирует ненадежное имя файла при его отображении. Например, запись имени файла или отображение в пользовательском интерфейсе ( Razor автоматически кодируется в формате HTML).
* Разрешите только утвержденные расширения файлов для спецификации на проектирование приложения.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Убедитесь, что на сервере выполняются проверки на стороне клиента. &dagger; Проверки на стороне клиента легко обойти.
* Проверьте размер отправленного файла. Установите максимальный предельный размер, чтобы предотвратить передачу больших объемов данных.&dagger;
* Если файлы не должны перезаписываться переданным файлом с тем же именем, перед отправкой файла проверьте его имя в базе данных или физическом хранилище.
* **Запустите сканер для проверки отправляемого содержимого на наличие вирусов и вредоносных программ, прежде чем сохранять файл.**

&dagger;Пример приложения демонстрирует подход, который соответствует критериям.

> [!WARNING]
> Отправка в систему вредоносного кода часто является первым шагом перед выполнением кода, который может:
>
> * полностью получить контроль над системой;
> * перезагрузить систему так, что она окажется в неработоспособном состоянии;
> * скомпрометировать пользовательские или системные данные;
> * применить граффити к открытому интерфейсу.
>
> Сведения об уменьшении контактной зоны атаки во время приема файлов от пользователей см. в следующих ресурсах:
>
> * [Неограниченная отправка файлов](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Безопасность Azure: убедитесь в наличии надлежащих мер контроля при получении файлов от пользователей](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Дополнительные сведения о реализации мер безопасности, включая примеры из примера приложения, см. в статье [Передача файлов в ASP.NET Core](#validation).

## <a name="storage-scenarios"></a>Сценарии использования хранилища

К общим вариантам хранилища файлов относятся следующие:

* База данных

  * В случае отправки небольших файлов база данных часто работает быстрее, чем физическое хранилище (файловая система или сетевая папка).
  * База данных часто более удобна по сравнению с вариантами физического хранилища, так как получение записи из базы пользовательских данных может одновременно предоставить содержимое файла (например, изображение аватара).
  * Эксплуатация базы данных потенциально дешевле, чем использование службы хранилища данных.

* Физическое хранилище (файловая система или сетевая папка).

  * Обработка передачи больших файлов:
    * Ограничения базы данных могут ограничивать размер передачи.
    * Физическое хранилище часто менее экономически выгодно, чем хранилище в базе данных.
  * Эксплуатация физического хранилища потенциально дешевле, чем использование службы хранилища данных.
  * Процесс приложения должен иметь разрешения на чтение и запись для места хранения. **Никогда не предоставляйте разрешение на выполнение.**

* Служба хранилища данных (например, хранилище [BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)).

  * Обычно службы обеспечивают улучшенную масштабируемость и устойчивость по сравнению с локальными решениями, которые обычно подвержены единым точкам отказа.
  * Затраты на использование служб обычно ниже в сценариях с крупномасштабной инфраструктурой хранения.

  Дополнительные сведения см. в разделе [Краткое руководство. Создание большого двоичного объекта в хранилище объектов с помощью .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet). В этом разделе показан метод <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, но метод <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> можно использовать для сохранения <xref:System.IO.FileStream> в хранилище BLOB-объектов при работе с <xref:System.IO.Stream>.

## <a name="file-upload-scenarios"></a>Сценарии передачи файлов

Есть два распространенных подхода к передаче файлов — буферизация и потоковая передача.

**ответов**

Весь файл считывается в <xref:Microsoft.AspNetCore.Http.IFormFile> (представление файла на C#, используемого для обработки или сохранения файла).

Потребление ресурсов (диска, памяти) при передаче файлов зависит от количества и размера одновременно передаваемых файлов. При попытке приложения поместить в буфер слишком много файлов может произойти аварийное завершение работы сайта из-за нехватки памяти или места на диске. Если размер или частота отправки файлов исчерпывают ресурсы приложения, используйте потоковую передачу.

> [!NOTE]
> Один буферизованный файл размером свыше 64 КБ перемещается из памяти во временный файл на диске.

Буферизация небольших файлов описана в следующих разделах этой статьи:

* [Физическое хранилище](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [База данных](#upload-small-files-with-buffered-model-binding-to-a-database)

**Потоковая передача**

Файл можно получить с помощью составного запроса. Затем он обрабатывается или сохраняется приложением напрямую. Потоковая передача повышает производительность не значительно. При отправке файлов потоковая передача снижает нагрузку на память или на место на диске.

Потоковая передача больших файлов рассматривается в разделе [Передача больших файлов с помощью потоковой передачи](#upload-large-files-with-streaming).

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Передача небольших файлов с привязкой буферизованной модели к физическому хранилищу

Для передачи небольших файлов можно применить составную форму или сформировать запрос POST на языке JavaScript.

В следующем примере показано использование Razor формы страниц для передачи одного файла (*pages/буффередсинглефилеуплоадфисикал. cshtml* в примере приложения):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

Следующий пример аналогичен предыдущему примеру, за исключением следующего:

* JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) используется для отправки данных формы.
* Проверка не выполняется.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Для выполнения отправки формы в JavaScript для клиентов, которые [не поддерживают Fetch API](https://caniuse.com/#feat=fetch), используйте один из следующих подходов:

* Используйте функцию Fetch Polyfill (например, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Используйте ключевое слово `XMLHttpRequest`. Пример:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Для поддержки передачи файлов в HTML-формах должен указываться тип кодировки `enctype` со значением `multipart/form-data`.

Для элемента ввода `files`, поддерживающего отправку нескольких файлов, в элементе `<input>` необходимо указать атрибут `multiple`:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Доступ к отдельным файлам, переданным на сервер, можно получать посредством [привязки модели](xref:mvc/models/model-binding) с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>. В примере приложения показано несколько отправок буферизованных файлов для баз данных и физических хранилищ.

<a name="filename2"></a>

> [!WARNING]
> **Не** используйте свойство `FileName` объекта <xref:Microsoft.AspNetCore.Http.IFormFile>, кроме как для отображения и ведения журнала. При отображении или ведении журнала кодируйте имя файла в формате HTML. Злоумышленник может предоставить имя вредоносного файла, включая полные или относительные пути. Приложения должны:
>
> * удалить путь из имени файла, указываемого пользователем;
> * сохранить имя файла, закодированное в формате HTML, откуда был удален путь, для пользовательского интерфейса или ведения журнала.
> * создать случайное имя файла для хранения.
>
> Следующий код удаляет путь из имени файла:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> В приведенных выше примерах не учитываются вопросы безопасности. Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/).
>
> * [Вопросы безопасности](#security-considerations)
> * [Проверка](#validation)

При отправке файлов с помощью привязки модели и <xref:Microsoft.AspNetCore.Http.IFormFile> метод действия может принимать следующие файлы:

* Один файл <xref:Microsoft.AspNetCore.Http.IFormFile>.
* Любая из следующих коллекций, представляющих несколько файлов:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Числ](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Привязка сопоставляет файлы форм по имени. Например, значение HTML `name` в `<input type="file" name="formFile">` должно соответствовать привязанному к C# параметру или свойству (`FormFile`). Дополнительные сведения см. в разделе [Сопоставление значения атрибута имени и имени параметра метода POST](#match-name-attribute-value-to-parameter-name-of-post-method).

В следующем примере происходит следующее:

* Циклично отправляет один или несколько передаваемых файлов.
* Использует метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы вернуть полный путь к файлу, включая его имя. 
* Сохраняет файлы в локальную файловую систему, используя имя файла, созданное приложением.
* Возвращает общее число и размер отправленных файлов.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Чтобы создать имя файла без пути, используйте `Path.GetRandomFileName`. В следующем примере путь получен из конфигурации:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Передаваемый в  путь <xref:System.IO.FileStream> *должен* содержать имя файла. Если имя файла не указано, в среде выполнения возникает исключение <xref:System.UnauthorizedAccessException>.

Файлы, передаваемые с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>, буферизуются в памяти или на диске на сервере перед обработкой. Внутри метода действия содержимое <xref:Microsoft.AspNetCore.Http.IFormFile> доступно в виде <xref:System.IO.Stream>. Помимо локальной файловой системы, файлы можно сохранять в сетевой папке или в службе хранилища файлов, например в хранилище [BLOB-объектов Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Еще один пример, который перебирает несколько файлов для отправки и использует надежные имена файлов, см. в файле *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* в примере приложения.

> [!WARNING]
> Метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) вызывает исключение <xref:System.IO.IOException> в случае создания более чем 65 535 файлов без удаления предыдущих временных файлов. Ограничение в 65 535 файлов предусмотрено для каждого сервера. Дополнительные сведения об этом ограничении в ОС Windows см. в примечаниях в следующих разделах:
>
> * [Функция GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks);
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Передача небольших файлов с привязкой буферизованной модели к базе данных

Для сохранения данных двоичных файлов в базе данных с помощью [Entity Framework](/ef/core/index) определите для сущности свойство массива <xref:System.Byte>:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Укажите свойство модели страницы для класса, который содержит <xref:Microsoft.AspNetCore.Http.IFormFile>:

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> можно использовать непосредственно как параметр метода действия или свойство модели привязки. В предыдущем примере используется свойство модели привязки.

`FileUpload`Используется в Razor форме страниц:

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

При публикации формы на сервере скопируйте <xref:Microsoft.AspNetCore.Http.IFormFile> в поток и сохраните его в базе данных в виде массива байтов. В следующем примере `_dbContext` сохраняет контекст базы данных приложения:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

Предыдущий пример похож на сценарий, продемонстрированный в примере приложения:

* *Pages/BufferedSingleFileUploadDb.cshtml*;
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*.

> [!WARNING]
> При сохранении двоичных данных в реляционных базах данных следует соблюдать осторожность, так как это может отрицательно сказаться на производительности.
>
> Свойство `FileName` параметра <xref:Microsoft.AspNetCore.Http.IFormFile> требует обязательной проверки. Свойство `FileName` следует использовать только в целях вывода и только после HTML-кодирования.
>
> В приведенных выше примерах не учитываются вопросы безопасности. Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/).
>
> * [Вопросы безопасности](#security-considerations)
> * [Проверка](#validation)

### <a name="upload-large-files-with-streaming"></a>Передача больших файлов с помощью потоковой передачи

В приведенном ниже примере демонстрируется использование JavaScript для потоковой передачи файла в действие контроллера. Токен против подделки файла создается с помощью пользовательского атрибута фильтра и передается в заголовках HTTP клиента, а не в теле запроса. Так как метод действия обрабатывает передаваемые данные напрямую, привязка модели формы отключается другим пользовательским фильтром. Внутри действия содержимое формы считывается с помощью объекта `MultipartReader`, который считывает каждый объект `MultipartSection` по отдельности, обрабатывая файл или сохраняя содержимое. После считывания составных разделов действие выполняет собственную привязку модели.

Начальный ответ страницы загружает форму и сохраняет токен подделки в cookie (с помощью `GenerateAntiforgeryTokenCookieAttribute` атрибута). Для установки с помощью маркера запроса атрибут использует встроенную поддержку защиты от [подделки](xref:security/anti-request-forgery) ASP.NET Core cookie .

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Для отключения привязки модели используется `DisableFormValueModelBindingAttribute`:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

В примере приложения `GenerateAntiforgeryTokenCookieAttribute` и применяются в `DisableFormValueModelBindingAttribute` качестве фильтров к моделям приложений страницы `/StreamedSingleFileUploadDb` и `/StreamedSingleFileUploadPhysical` в `Startup.ConfigureServices` с использованием [ Razor соглашений о страницах](xref:razor-pages/razor-pages-conventions).

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Так как привязка модели не считывает форму, параметры, привязанные из формы, не привязываются (запросы, маршруты и заголовки продолжают работать). Метод действия работает напрямую со свойством `Request`. Для считывания каждого раздела служит объект `MultipartReader`. Данные "ключ — значение" хранятся в `KeyValueAccumulator`. После считывания составных разделов содержимое `KeyValueAccumulator` используется для привязки данных формы к типу модели.

Полный метод `StreamingController.UploadDatabase` для потоковой передачи в базу данных с EF Core:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Полный метод `StreamingController.UploadPhysical` для потоковой передачи в физическое расположение:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

В примере приложения проверки обрабатываются с помощью `FileHelpers.ProcessStreamedFile`.

## <a name="validation"></a>Проверка

В классе `FileHelpers` примера приложения демонстрируется несколько проверок буферизованного файла <xref:Microsoft.AspNetCore.Http.IFormFile> и потоковой передачи файлов. Сведения об обработке буферизованных файлов <xref:Microsoft.AspNetCore.Http.IFormFile> в примере приложения см. в описании метода `ProcessFormFile` в файле *Utilities/FileHelpers.cs*. Сведения об обработке потоковых файлов см. в описании метода `ProcessStreamedFile` в том же файле.

> [!WARNING]
> Методы обработки проверки, показанные в примере приложения, не проверяют содержимое отправленных файлов. В большинстве рабочих сценариев в файле применяется API сканирования на наличие вирусов и вредоносных программ, прежде чем сделать файл доступным для пользователей или других систем.
>
> Хотя пример в разделе содержит рабочий пример методов проверки, не следует реализовывать класс `FileHelpers` в рабочем приложении, кроме таких случаев:
>
> * Вы полностью разбираетесь в реализации.
> * Вы изменяете реализацию соответствующим образом для среды и спецификаций приложения.
>
> **Никогда не реализуйте код безопасности в приложении, не выполнив эти требования.**

### <a name="content-validation"></a>Проверка содержимого

**Используйте сторонний API сканирования на наличие вирусов и вредоносных программ для отправленного содержимого.**

Сканирование файлов требует использования ресурсов сервера в сценариях с большими объемами данных. Если производительность обработки запросов снижается из-за сканирования файлов, рассмотрите возможность разгрузки сканирования путем использования [фоновой службы](xref:fundamentals/host/hosted-services), возможно, службы, которая работает на сервере, отличном от сервера приложения. Как правило, передаваемые файлы хранятся в карантинной области до тех пор, пока фоновый сканер не проверит их на наличие вирусов. При передаче файл перемещается в нормальное место хранения файлов. Эти действия обычно выполняются вместе с записью базы данных, которая указывает состояние сканирования файла. Благодаря такому подходу приложение и сервер приложений остаются в режиме реагирования на запросы.

### <a name="file-extension-validation"></a>Проверка расширения файла

Расширение переданного файла должно проверяться в соответствии со списком разрешенных расширений. Пример:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Проверка подписи файла

Подпись файла определяется по первым нескольким байтам в начале файла. Эти байты можно использовать, чтобы указать, совпадает ли расширение с содержимым файла. Пример приложения проверяет подписи файлов на соответствие нескольким распространенным типам файлов. В следующем примере проверяется подпись файла для изображения JPEG:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Дополнительные сведения о получении дополнительных подписей файлов см. [по этой ссылке](https://www.filesignatures.net/) и в официальных спецификациях файла.

### <a name="file-name-security"></a>Безопасность имени файла

Никогда не используйте для хранения файла в физическом хранилище имя, предоставляемое клиентом. Создайте надежное имя файла с помощью [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) или [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы получить полный путь (включая имя файла) для временного хранилища.

Razor автоматически кодирует значения свойств для вывода. Ниже приведен безопасный код, который можно использовать.

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

За пределами Razor , всегда следует <xref:System.Net.WebUtility.HtmlEncode*> содержимое имени файла из запроса пользователя.

Во многих реализациях следует включать проверку существования файла. В противном случае файл перезаписывается файлом с тем же именем. Предоставьте дополнительную логику для соответствия спецификациям приложения.

### <a name="size-validation"></a>Проверка размера

Ограничьте размер передаваемых файлов.

В примере приложения размер файла ограничен 2 МБ (указывается в байтах). Ограничение предоставляется через [конфигурацию](xref:fundamentals/configuration/index) из *appsettings.json* файла:

```json
{
  "FileSizeLimit": 2097152
}
```

В классы `PageModel` внедряется `FileSizeLimit`.

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Если размер файла превышает ограничение, файл отклоняется:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Сопоставление значения атрибута имени и имени параметра метода POST

В других Razor формах, которые передают данные формы или используют JavaScript `FormData` непосредственно, имя, указанное в элементе формы, или `FormData` должно совпадать с именем параметра в действии контроллера.

В следующем примере:

* При использовании элемента `<input>` атрибуту `name` присваивается значение `battlePlans`:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* При использовании `FormData` в JavaScript для имени задается значение `battlePlans`:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Используйте соответствующее имя для параметра метода C# (`battlePlans`):

* Для Razor метода обработчика страницы страниц с именем `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Для метода действия контроллера POST приложения MVC:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Конфигурация сервера и приложения

### <a name="multipart-body-length-limit"></a>Ограничение длины составного текста

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> устанавливает ограничение длины каждого составного текста. Разделы формы, превышающие это ограничение, вызовут <xref:System.IO.InvalidDataException> при синтаксическом анализе. Значение по умолчанию — 134 217 728 байт (128 МБ). Настройте ограничение с помощью параметра <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> в `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> используется для настройки <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> для одной страницы или действия.

В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

В Razor приложении "страницы" или в приложении MVC примените фильтр к модели страницы или методу действия:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Максимальный размер текста запроса Kestrel

Для приложений, размещенных в Kestrel, по умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ. Настройте ограничение с помощью параметра сервера Kestrel [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size):

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> используется для настройки [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) для одной страницы или действия.

В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

В Razor приложении Pages или MVC примените фильтр к классу обработчика страницы или методу действия:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>Другие ограничения Kestrel

Другие ограничения Kestrel могут применяться для приложений, размещенных в Kestrel:

* [максимальное число клиентских подключений;](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Скорость передачи данных запросов и ответов](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

Ограничение запроса по умолчанию ( `maxAllowedContentLength` ) составляет 30 000 000 байт, что составляет примерно 28,6 МБ. Настройка ограничения в `web.config` файле. В следующем примере для ограничения установлено значение 50 МБ (52 428 800 байт):

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

`maxAllowedContentLength`Параметр применяется только к службам IIS. Дополнительные сведения см. в разделе [ограничения `<requestLimits>` запросов ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Увеличьте максимальный размер текста запроса для HTTP-запроса, задав в параметре <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> `Startup.ConfigureServices` . В следующем примере для ограничения установлено значение 50 МБ (52 428 800 байт):

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

Для получения дополнительной информации см. <xref:host-and-deploy/iis/index#iis-options>.

## <a name="troubleshoot"></a>Устранение неполадок

Ниже описываются некоторые распространенные проблемы, которые возникают при передаче файлов, и возможные способы их решения.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Ошибка "Не найдено" при развертывании на сервере IIS

Следующая ошибка свидетельствует о том, что размер передаваемых файлов превышает настроенное на сервере ограничение длины содержимого:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Дополнительные сведения см. в описании [IIS](#iis).

### <a name="connection-failure"></a>Сбой подключения

Ошибка соединения и сброс соединения с сервером, скорее всего, означают, что размер отправленного файла превышает максимальную длину текста запроса Kestrel. Дополнительные сведения см. в разделе [Максимальный размер текста запроса Kestrel](#kestrel-maximum-request-body-size). Может также потребоваться настроить ограничения подключений клиентов Kestrel.

### <a name="null-reference-exception-with-iformfile"></a>Исключение, связанное с пустой ссылкой в IFormFile

Если контроллер принимает передаваемые файлы с помощью <xref:Microsoft.AspNetCore.Http.IFormFile>, но значение равно `null`, проверьте, указан ли в HTML-форме атрибут `enctype` со значением `multipart/form-data`. Если этот атрибут не задан для элемента `<form>`, передача файлов происходить не будет и все связанные аргументы <xref:Microsoft.AspNetCore.Http.IFormFile> будут иметь значение `null`. Убедитесь также, что [именование передачи в данных формы совпадает с именованием приложения](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Поток слишком длинный

В примерах в этом разделе <xref:System.IO.MemoryStream> используется для хранения содержимого отправленного файла. Максимальный размер `MemoryStream` ограничен значением `int.MaxValue`. Если в сценарии передачи файлов в приложении требуется хранить содержимое, размер которого больше 50 МБ, используйте другой подход, не зависящий от одного только `MemoryStream`.

::: moniker-end


## <a name="additional-resources"></a>Дополнительные ресурсы

::: moniker range="< aspnetcore-5.0"
* [Сток запросов HTTP-подключения](xref:fundamentals/servers/kestrel#http11-request-draining)
::: moniker-end
::: moniker range=">= aspnetcore-5.0"
* [Сток запросов HTTP-подключения](xref:fundamentals/servers/kestrel/request-draining)
::: moniker-end

* [Неограниченная отправка файлов](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [Безопасность Azure: кадр безопасности: Проверка входных данных | Способы их устранения](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Шаблоны разработки в облаке Azure: шаблон камердинера Key](/azure/architecture/patterns/valet-key)
