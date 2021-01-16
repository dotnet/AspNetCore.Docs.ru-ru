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
ms.openlocfilehash: c32d20d4616650db004c78fb4d8ea9a4d5a3beab
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252803"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="ceca3-103">Передача файлов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ceca3-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="ceca3-104">По [Стив Смит](https://ardalis.com/) и [рутжер](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="ceca3-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ceca3-105">Действия ASP.NET Core поддерживают передачу одного или нескольких файлов с помощью привязки модели с буферизацией для небольших файлов или потоковой передачи без буферизации для более крупных файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="ceca3-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ceca3-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="ceca3-107">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ceca3-107">Security considerations</span></span>

<span data-ttu-id="ceca3-108">Необходимо соблюдать осторожность при предоставлении пользователям возможности отправки файлов на сервер.</span><span class="sxs-lookup"><span data-stu-id="ceca3-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="ceca3-109">Злоумышленники могут попытаться:</span><span class="sxs-lookup"><span data-stu-id="ceca3-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="ceca3-110">Выполнить атаку типа [отказ в обслуживании](/windows-hardware/drivers/ifs/denial-of-service).</span><span class="sxs-lookup"><span data-stu-id="ceca3-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="ceca3-111">Передать вирусы и вредоносные программы.</span><span class="sxs-lookup"><span data-stu-id="ceca3-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="ceca3-112">Нарушить безопасность сетей и серверов другими способами.</span><span class="sxs-lookup"><span data-stu-id="ceca3-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="ceca3-113">Ниже приведены некоторые действия по обеспечению безопасности, которые снижают вероятность успешных атак.</span><span class="sxs-lookup"><span data-stu-id="ceca3-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="ceca3-114">Передавайте файлы в выделенную область для отправки файлов, желательно не на системный диск.</span><span class="sxs-lookup"><span data-stu-id="ceca3-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="ceca3-115">Использование выделенного расположения упрощает применение мер безопасности к отправленным файлам.</span><span class="sxs-lookup"><span data-stu-id="ceca3-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="ceca3-116">Отключите разрешения на выполнение для расположения отправки файла.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ceca3-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="ceca3-117">**Не** сохраняйте переданные файлы в дереве каталогов, где находится приложение.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ceca3-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="ceca3-118">Используйте безопасное имя файла, определяемое приложением.</span><span class="sxs-lookup"><span data-stu-id="ceca3-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="ceca3-119">Не используйте имя файла, предоставленное пользователем, или имя ненадежного файла отправленного файла. &dagger; HTML кодирует ненадежное имя файла при его отображении.</span><span class="sxs-lookup"><span data-stu-id="ceca3-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="ceca3-120">Например, запись имени файла или отображение в пользовательском интерфейсе ( Razor автоматически кодируется в формате HTML).</span><span class="sxs-lookup"><span data-stu-id="ceca3-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="ceca3-121">Разрешите только утвержденные расширения файлов для спецификации на проектирование приложения.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ceca3-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="ceca3-122">Убедитесь, что на сервере выполняются проверки на стороне клиента. &dagger; Проверки на стороне клиента легко обойти.</span><span class="sxs-lookup"><span data-stu-id="ceca3-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="ceca3-123">Проверьте размер отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="ceca3-124">Установите максимальный предельный размер, чтобы предотвратить передачу больших объемов данных.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ceca3-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="ceca3-125">Если файлы не должны перезаписываться переданным файлом с тем же именем, перед отправкой файла проверьте его имя в базе данных или физическом хранилище.</span><span class="sxs-lookup"><span data-stu-id="ceca3-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="ceca3-126">**Запустите сканер для проверки отправляемого содержимого на наличие вирусов и вредоносных программ, прежде чем сохранять файл.**</span><span class="sxs-lookup"><span data-stu-id="ceca3-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="ceca3-127">&dagger;Пример приложения демонстрирует подход, который соответствует критериям.</span><span class="sxs-lookup"><span data-stu-id="ceca3-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="ceca3-128">Отправка в систему вредоносного кода часто является первым шагом перед выполнением кода, который может:</span><span class="sxs-lookup"><span data-stu-id="ceca3-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="ceca3-129">полностью получить контроль над системой;</span><span class="sxs-lookup"><span data-stu-id="ceca3-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="ceca3-130">перезагрузить систему так, что она окажется в неработоспособном состоянии;</span><span class="sxs-lookup"><span data-stu-id="ceca3-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="ceca3-131">скомпрометировать пользовательские или системные данные;</span><span class="sxs-lookup"><span data-stu-id="ceca3-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="ceca3-132">применить граффити к открытому интерфейсу.</span><span class="sxs-lookup"><span data-stu-id="ceca3-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="ceca3-133">Сведения об уменьшении контактной зоны атаки во время приема файлов от пользователей см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="ceca3-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="ceca3-134">Неограниченная отправка файлов</span><span class="sxs-lookup"><span data-stu-id="ceca3-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="ceca3-135">Безопасность Azure: убедитесь в наличии надлежащих мер контроля при получении файлов от пользователей</span><span class="sxs-lookup"><span data-stu-id="ceca3-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="ceca3-136">Дополнительные сведения о реализации мер безопасности, включая примеры из примера приложения, см. в статье [Передача файлов в ASP.NET Core](#validation).</span><span class="sxs-lookup"><span data-stu-id="ceca3-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="ceca3-137">Сценарии использования хранилища</span><span class="sxs-lookup"><span data-stu-id="ceca3-137">Storage scenarios</span></span>

<span data-ttu-id="ceca3-138">К общим вариантам хранилища файлов относятся следующие:</span><span class="sxs-lookup"><span data-stu-id="ceca3-138">Common storage options for files include:</span></span>

* <span data-ttu-id="ceca3-139">База данных</span><span class="sxs-lookup"><span data-stu-id="ceca3-139">Database</span></span>

  * <span data-ttu-id="ceca3-140">В случае отправки небольших файлов база данных часто работает быстрее, чем физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="ceca3-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="ceca3-141">База данных часто более удобна по сравнению с вариантами физического хранилища, так как получение записи из базы пользовательских данных может одновременно предоставить содержимое файла (например, изображение аватара).</span><span class="sxs-lookup"><span data-stu-id="ceca3-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="ceca3-142">Эксплуатация базы данных потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="ceca3-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="ceca3-143">Физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="ceca3-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="ceca3-144">Обработка передачи больших файлов:</span><span class="sxs-lookup"><span data-stu-id="ceca3-144">For large file uploads:</span></span>
    * <span data-ttu-id="ceca3-145">Ограничения базы данных могут ограничивать размер передачи.</span><span class="sxs-lookup"><span data-stu-id="ceca3-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="ceca3-146">Физическое хранилище часто менее экономически выгодно, чем хранилище в базе данных.</span><span class="sxs-lookup"><span data-stu-id="ceca3-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="ceca3-147">Эксплуатация физического хранилища потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="ceca3-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="ceca3-148">Процесс приложения должен иметь разрешения на чтение и запись для места хранения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="ceca3-149">**Никогда не предоставляйте разрешение на выполнение.**</span><span class="sxs-lookup"><span data-stu-id="ceca3-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="ceca3-150">Служба хранилища данных (например, хранилище [BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)).</span><span class="sxs-lookup"><span data-stu-id="ceca3-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="ceca3-151">Обычно службы обеспечивают улучшенную масштабируемость и устойчивость по сравнению с локальными решениями, которые обычно подвержены единым точкам отказа.</span><span class="sxs-lookup"><span data-stu-id="ceca3-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="ceca3-152">Затраты на использование служб обычно ниже в сценариях с крупномасштабной инфраструктурой хранения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="ceca3-153">Дополнительные сведения см. в разделе [Краткое руководство. Создание большого двоичного объекта в хранилище объектов с помощью .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="ceca3-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="ceca3-154">Сценарии передачи файлов</span><span class="sxs-lookup"><span data-stu-id="ceca3-154">File upload scenarios</span></span>

<span data-ttu-id="ceca3-155">Есть два распространенных подхода к передаче файлов — буферизация и потоковая передача.</span><span class="sxs-lookup"><span data-stu-id="ceca3-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="ceca3-156">**ответов**</span><span class="sxs-lookup"><span data-stu-id="ceca3-156">**Buffering**</span></span>

<span data-ttu-id="ceca3-157">Весь файл считывается в <xref:Microsoft.AspNetCore.Http.IFormFile> (представление файла на C#, используемого для обработки или сохранения файла).</span><span class="sxs-lookup"><span data-stu-id="ceca3-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="ceca3-158">Потребление ресурсов (диска, памяти) при передаче файлов зависит от количества и размера одновременно передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="ceca3-159">При попытке приложения поместить в буфер слишком много файлов может произойти аварийное завершение работы сайта из-за нехватки памяти или места на диске.</span><span class="sxs-lookup"><span data-stu-id="ceca3-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="ceca3-160">Если размер или частота отправки файлов исчерпывают ресурсы приложения, используйте потоковую передачу.</span><span class="sxs-lookup"><span data-stu-id="ceca3-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="ceca3-161">Один буферизованный файл размером свыше 64 КБ перемещается из памяти во временный файл на диске.</span><span class="sxs-lookup"><span data-stu-id="ceca3-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="ceca3-162">Буферизация небольших файлов описана в следующих разделах этой статьи:</span><span class="sxs-lookup"><span data-stu-id="ceca3-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="ceca3-163">Физическое хранилище</span><span class="sxs-lookup"><span data-stu-id="ceca3-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="ceca3-164">База данных</span><span class="sxs-lookup"><span data-stu-id="ceca3-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="ceca3-165">**Потоковая передача**</span><span class="sxs-lookup"><span data-stu-id="ceca3-165">**Streaming**</span></span>

<span data-ttu-id="ceca3-166">Файл можно получить с помощью составного запроса. Затем он обрабатывается или сохраняется приложением напрямую.</span><span class="sxs-lookup"><span data-stu-id="ceca3-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="ceca3-167">Потоковая передача повышает производительность не значительно.</span><span class="sxs-lookup"><span data-stu-id="ceca3-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="ceca3-168">При отправке файлов потоковая передача снижает нагрузку на память или на место на диске.</span><span class="sxs-lookup"><span data-stu-id="ceca3-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="ceca3-169">Потоковая передача больших файлов рассматривается в разделе [Передача больших файлов с помощью потоковой передачи](#upload-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="ceca3-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="ceca3-170">Передача небольших файлов с привязкой буферизованной модели к физическому хранилищу</span><span class="sxs-lookup"><span data-stu-id="ceca3-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="ceca3-171">Для передачи небольших файлов можно применить составную форму или сформировать запрос POST на языке JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ceca3-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="ceca3-172">В следующем примере показано использование Razor формы страниц для передачи одного файла (*pages/буффередсинглефилеуплоадфисикал. cshtml* в примере приложения):</span><span class="sxs-lookup"><span data-stu-id="ceca3-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="ceca3-173">Следующий пример аналогичен предыдущему примеру, за исключением следующего:</span><span class="sxs-lookup"><span data-stu-id="ceca3-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="ceca3-174">JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) используется для отправки данных формы.</span><span class="sxs-lookup"><span data-stu-id="ceca3-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="ceca3-175">Проверка не выполняется.</span><span class="sxs-lookup"><span data-stu-id="ceca3-175">There's no validation.</span></span>

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

<span data-ttu-id="ceca3-176">Для выполнения отправки формы в JavaScript для клиентов, которые [не поддерживают Fetch API](https://caniuse.com/#feat=fetch), используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="ceca3-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="ceca3-177">Используйте функцию Fetch Polyfill (например, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="ceca3-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="ceca3-178">Используйте ключевое слово `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="ceca3-179">Пример:</span><span class="sxs-lookup"><span data-stu-id="ceca3-179">For example:</span></span>

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

<span data-ttu-id="ceca3-180">Для поддержки передачи файлов в HTML-формах должен указываться тип кодировки `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="ceca3-181">Для элемента ввода `files`, поддерживающего отправку нескольких файлов, в элементе `<input>` необходимо указать атрибут `multiple`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="ceca3-182">Доступ к отдельным файлам, переданным на сервер, можно получать посредством [привязки модели](xref:mvc/models/model-binding) с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="ceca3-183">В примере приложения показано несколько отправок буферизованных файлов для баз данных и физических хранилищ.</span><span class="sxs-lookup"><span data-stu-id="ceca3-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="ceca3-184">**Не** используйте свойство `FileName` объекта <xref:Microsoft.AspNetCore.Http.IFormFile>, кроме как для отображения и ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="ceca3-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="ceca3-185">При отображении или ведении журнала кодируйте имя файла в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="ceca3-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="ceca3-186">Злоумышленник может предоставить имя вредоносного файла, включая полные или относительные пути.</span><span class="sxs-lookup"><span data-stu-id="ceca3-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="ceca3-187">Приложения должны:</span><span class="sxs-lookup"><span data-stu-id="ceca3-187">Applications should:</span></span>
>
> * <span data-ttu-id="ceca3-188">удалить путь из имени файла, указываемого пользователем;</span><span class="sxs-lookup"><span data-stu-id="ceca3-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="ceca3-189">сохранить имя файла, закодированное в формате HTML, откуда был удален путь, для пользовательского интерфейса или ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="ceca3-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="ceca3-190">создать случайное имя файла для хранения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="ceca3-191">Следующий код удаляет путь из имени файла:</span><span class="sxs-lookup"><span data-stu-id="ceca3-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="ceca3-192">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ceca3-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="ceca3-193">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="ceca3-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ceca3-194">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ceca3-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ceca3-195">Проверка</span><span class="sxs-lookup"><span data-stu-id="ceca3-195">Validation</span></span>](#validation)

<span data-ttu-id="ceca3-196">При отправке файлов с помощью привязки модели и <xref:Microsoft.AspNetCore.Http.IFormFile> метод действия может принимать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="ceca3-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="ceca3-197">Один файл <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="ceca3-198">Любая из следующих коллекций, представляющих несколько файлов:</span><span class="sxs-lookup"><span data-stu-id="ceca3-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="ceca3-199">[Числ](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="ceca3-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="ceca3-200">Привязка сопоставляет файлы форм по имени.</span><span class="sxs-lookup"><span data-stu-id="ceca3-200">Binding matches form files by name.</span></span> <span data-ttu-id="ceca3-201">Например, значение HTML `name` в `<input type="file" name="formFile">` должно соответствовать привязанному к C# параметру или свойству (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="ceca3-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="ceca3-202">Дополнительные сведения см. в разделе [Сопоставление значения атрибута имени и имени параметра метода POST](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="ceca3-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="ceca3-203">Следующий пример:</span><span class="sxs-lookup"><span data-stu-id="ceca3-203">The following example:</span></span>

* <span data-ttu-id="ceca3-204">Циклично отправляет один или несколько передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="ceca3-205">Использует метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы вернуть полный путь к файлу, включая его имя.</span><span class="sxs-lookup"><span data-stu-id="ceca3-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="ceca3-206">Сохраняет файлы в локальную файловую систему, используя имя файла, созданное приложением.</span><span class="sxs-lookup"><span data-stu-id="ceca3-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="ceca3-207">Возвращает общее число и размер отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="ceca3-208">Чтобы создать имя файла без пути, используйте `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="ceca3-209">В следующем примере путь получен из конфигурации:</span><span class="sxs-lookup"><span data-stu-id="ceca3-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="ceca3-210">Передаваемый в  путь <xref:System.IO.FileStream> *должен* содержать имя файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="ceca3-211">Если имя файла не указано, в среде выполнения возникает исключение <xref:System.UnauthorizedAccessException>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="ceca3-212">Файлы, передаваемые с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>, буферизуются в памяти или на диске на сервере перед обработкой.</span><span class="sxs-lookup"><span data-stu-id="ceca3-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="ceca3-213">Внутри метода действия содержимое <xref:Microsoft.AspNetCore.Http.IFormFile> доступно в виде <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="ceca3-214">Помимо локальной файловой системы, файлы можно сохранять в сетевой папке или в службе хранилища файлов, например в хранилище [BLOB-объектов Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="ceca3-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="ceca3-215">Еще один пример, который перебирает несколько файлов для отправки и использует надежные имена файлов, см. в файле *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="ceca3-216">Метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) вызывает исключение <xref:System.IO.IOException> в случае создания более чем 65 535 файлов без удаления предыдущих временных файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="ceca3-217">Ограничение в 65 535 файлов предусмотрено для каждого сервера.</span><span class="sxs-lookup"><span data-stu-id="ceca3-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="ceca3-218">Дополнительные сведения об этом ограничении в ОС Windows см. в примечаниях в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="ceca3-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * <span data-ttu-id="ceca3-219">[Функция GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks);</span><span class="sxs-lookup"><span data-stu-id="ceca3-219">[GetTempFileNameA function](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)</span></span>
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="ceca3-220">Передача небольших файлов с привязкой буферизованной модели к базе данных</span><span class="sxs-lookup"><span data-stu-id="ceca3-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="ceca3-221">Для сохранения данных двоичных файлов в базе данных с помощью [Entity Framework](/ef/core/index) определите для сущности свойство массива <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="ceca3-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="ceca3-222">Укажите свойство модели страницы для класса, который содержит <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="ceca3-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="ceca3-223"><xref:Microsoft.AspNetCore.Http.IFormFile> можно использовать непосредственно как параметр метода действия или свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="ceca3-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="ceca3-224">В предыдущем примере используется свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="ceca3-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="ceca3-225">`FileUpload`Используется в Razor форме страниц:</span><span class="sxs-lookup"><span data-stu-id="ceca3-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="ceca3-226">При публикации формы на сервере скопируйте <xref:Microsoft.AspNetCore.Http.IFormFile> в поток и сохраните его в базе данных в виде массива байтов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="ceca3-227">В следующем примере `_dbContext` сохраняет контекст базы данных приложения:</span><span class="sxs-lookup"><span data-stu-id="ceca3-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="ceca3-228">Предыдущий пример похож на сценарий, продемонстрированный в примере приложения:</span><span class="sxs-lookup"><span data-stu-id="ceca3-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="ceca3-229">*Pages/BufferedSingleFileUploadDb.cshtml*;</span><span class="sxs-lookup"><span data-stu-id="ceca3-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="ceca3-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="ceca3-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="ceca3-231">При сохранении двоичных данных в реляционных базах данных следует соблюдать осторожность, так как это может отрицательно сказаться на производительности.</span><span class="sxs-lookup"><span data-stu-id="ceca3-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="ceca3-232">Свойство `FileName` параметра <xref:Microsoft.AspNetCore.Http.IFormFile> требует обязательной проверки.</span><span class="sxs-lookup"><span data-stu-id="ceca3-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="ceca3-233">Свойство `FileName` следует использовать только в целях вывода и только после HTML-кодирования.</span><span class="sxs-lookup"><span data-stu-id="ceca3-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="ceca3-234">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ceca3-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="ceca3-235">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="ceca3-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ceca3-236">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ceca3-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ceca3-237">Проверка</span><span class="sxs-lookup"><span data-stu-id="ceca3-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="ceca3-238">Передача больших файлов с помощью потоковой передачи</span><span class="sxs-lookup"><span data-stu-id="ceca3-238">Upload large files with streaming</span></span>

<span data-ttu-id="ceca3-239">В приведенном ниже примере демонстрируется использование JavaScript для потоковой передачи файла в действие контроллера.</span><span class="sxs-lookup"><span data-stu-id="ceca3-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="ceca3-240">Токен против подделки файла создается с помощью пользовательского атрибута фильтра и передается в заголовках HTTP клиента, а не в теле запроса.</span><span class="sxs-lookup"><span data-stu-id="ceca3-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="ceca3-241">Так как метод действия обрабатывает передаваемые данные напрямую, привязка модели формы отключается другим пользовательским фильтром.</span><span class="sxs-lookup"><span data-stu-id="ceca3-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="ceca3-242">Внутри действия содержимое формы считывается с помощью объекта `MultipartReader`, который считывает каждый объект `MultipartSection` по отдельности, обрабатывая файл или сохраняя содержимое.</span><span class="sxs-lookup"><span data-stu-id="ceca3-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="ceca3-243">После считывания составных разделов действие выполняет собственную привязку модели.</span><span class="sxs-lookup"><span data-stu-id="ceca3-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="ceca3-244">Начальный ответ страницы загружает форму и сохраняет токен подделки в cookie (с помощью `GenerateAntiforgeryTokenCookieAttribute` атрибута).</span><span class="sxs-lookup"><span data-stu-id="ceca3-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="ceca3-245">Для установки с помощью маркера запроса атрибут использует встроенную поддержку защиты от [подделки](xref:security/anti-request-forgery) ASP.NET Core cookie .</span><span class="sxs-lookup"><span data-stu-id="ceca3-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="ceca3-246">Для отключения привязки модели используется `DisableFormValueModelBindingAttribute`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="ceca3-247">В примере приложения `GenerateAntiforgeryTokenCookieAttribute` и применяются в `DisableFormValueModelBindingAttribute` качестве фильтров к моделям приложений страницы `/StreamedSingleFileUploadDb` и `/StreamedSingleFileUploadPhysical` в `Startup.ConfigureServices` с использованием [ Razor соглашений о страницах](xref:razor-pages/razor-pages-conventions).</span><span class="sxs-lookup"><span data-stu-id="ceca3-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="ceca3-248">Так как привязка модели не считывает форму, параметры, привязанные из формы, не привязываются (запросы, маршруты и заголовки продолжают работать).</span><span class="sxs-lookup"><span data-stu-id="ceca3-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="ceca3-249">Метод действия работает напрямую со свойством `Request`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="ceca3-250">Для считывания каждого раздела служит объект `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="ceca3-251">Данные "ключ — значение" хранятся в `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="ceca3-252">После считывания составных разделов содержимое `KeyValueAccumulator` используется для привязки данных формы к типу модели.</span><span class="sxs-lookup"><span data-stu-id="ceca3-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="ceca3-253">Полный метод `StreamingController.UploadDatabase` для потоковой передачи в базу данных с EF Core:</span><span class="sxs-lookup"><span data-stu-id="ceca3-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="ceca3-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="ceca3-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="ceca3-255">Полный метод `StreamingController.UploadPhysical` для потоковой передачи в физическое расположение:</span><span class="sxs-lookup"><span data-stu-id="ceca3-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="ceca3-256">В примере приложения проверки обрабатываются с помощью `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="ceca3-257">Проверка</span><span class="sxs-lookup"><span data-stu-id="ceca3-257">Validation</span></span>

<span data-ttu-id="ceca3-258">В классе `FileHelpers` примера приложения демонстрируется несколько проверок буферизованного файла <xref:Microsoft.AspNetCore.Http.IFormFile> и потоковой передачи файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="ceca3-259">Сведения об обработке буферизованных файлов <xref:Microsoft.AspNetCore.Http.IFormFile> в примере приложения см. в описании метода `ProcessFormFile` в файле *Utilities/FileHelpers.cs*.</span><span class="sxs-lookup"><span data-stu-id="ceca3-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="ceca3-260">Сведения об обработке потоковых файлов см. в описании метода `ProcessStreamedFile` в том же файле.</span><span class="sxs-lookup"><span data-stu-id="ceca3-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="ceca3-261">Методы обработки проверки, показанные в примере приложения, не проверяют содержимое отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="ceca3-262">В большинстве рабочих сценариев в файле применяется API сканирования на наличие вирусов и вредоносных программ, прежде чем сделать файл доступным для пользователей или других систем.</span><span class="sxs-lookup"><span data-stu-id="ceca3-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="ceca3-263">Хотя пример в разделе содержит рабочий пример методов проверки, не следует реализовывать класс `FileHelpers` в рабочем приложении, кроме таких случаев:</span><span class="sxs-lookup"><span data-stu-id="ceca3-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="ceca3-264">Вы полностью разбираетесь в реализации.</span><span class="sxs-lookup"><span data-stu-id="ceca3-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="ceca3-265">Вы изменяете реализацию соответствующим образом для среды и спецификаций приложения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="ceca3-266">**Никогда не реализуйте код безопасности в приложении, не выполнив эти требования.**</span><span class="sxs-lookup"><span data-stu-id="ceca3-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="ceca3-267">Проверка содержимого</span><span class="sxs-lookup"><span data-stu-id="ceca3-267">Content validation</span></span>

<span data-ttu-id="ceca3-268">**Используйте сторонний API сканирования на наличие вирусов и вредоносных программ для отправленного содержимого.**</span><span class="sxs-lookup"><span data-stu-id="ceca3-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="ceca3-269">Сканирование файлов требует использования ресурсов сервера в сценариях с большими объемами данных.</span><span class="sxs-lookup"><span data-stu-id="ceca3-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="ceca3-270">Если производительность обработки запросов снижается из-за сканирования файлов, рассмотрите возможность разгрузки сканирования путем использования [фоновой службы](xref:fundamentals/host/hosted-services), возможно, службы, которая работает на сервере, отличном от сервера приложения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="ceca3-271">Как правило, передаваемые файлы хранятся в карантинной области до тех пор, пока фоновый сканер не проверит их на наличие вирусов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="ceca3-272">При передаче файл перемещается в нормальное место хранения файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="ceca3-273">Эти действия обычно выполняются вместе с записью базы данных, которая указывает состояние сканирования файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="ceca3-274">Благодаря такому подходу приложение и сервер приложений остаются в режиме реагирования на запросы.</span><span class="sxs-lookup"><span data-stu-id="ceca3-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="ceca3-275">Проверка расширения файла</span><span class="sxs-lookup"><span data-stu-id="ceca3-275">File extension validation</span></span>

<span data-ttu-id="ceca3-276">Расширение переданного файла должно проверяться в соответствии со списком разрешенных расширений.</span><span class="sxs-lookup"><span data-stu-id="ceca3-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="ceca3-277">Пример:</span><span class="sxs-lookup"><span data-stu-id="ceca3-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="ceca3-278">Проверка подписи файла</span><span class="sxs-lookup"><span data-stu-id="ceca3-278">File signature validation</span></span>

<span data-ttu-id="ceca3-279">Подпись файла определяется по первым нескольким байтам в начале файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="ceca3-280">Эти байты можно использовать, чтобы указать, совпадает ли расширение с содержимым файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="ceca3-281">Пример приложения проверяет подписи файлов на соответствие нескольким распространенным типам файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="ceca3-282">В следующем примере проверяется подпись файла для изображения JPEG:</span><span class="sxs-lookup"><span data-stu-id="ceca3-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="ceca3-283">Дополнительные сведения о получении дополнительных подписей файлов см. [по этой ссылке](https://www.filesignatures.net/) и в официальных спецификациях файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="ceca3-284">Безопасность имени файла</span><span class="sxs-lookup"><span data-stu-id="ceca3-284">File name security</span></span>

<span data-ttu-id="ceca3-285">Никогда не используйте для хранения файла в физическом хранилище имя, предоставляемое клиентом.</span><span class="sxs-lookup"><span data-stu-id="ceca3-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="ceca3-286">Создайте надежное имя файла с помощью [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) или [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы получить полный путь (включая имя файла) для временного хранилища.</span><span class="sxs-lookup"><span data-stu-id="ceca3-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="ceca3-287">Razor автоматически кодирует значения свойств для вывода.</span><span class="sxs-lookup"><span data-stu-id="ceca3-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="ceca3-288">Ниже приведен безопасный код, который можно использовать.</span><span class="sxs-lookup"><span data-stu-id="ceca3-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="ceca3-289">За пределами Razor , всегда следует <xref:System.Net.WebUtility.HtmlEncode*> содержимое имени файла из запроса пользователя.</span><span class="sxs-lookup"><span data-stu-id="ceca3-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="ceca3-290">Во многих реализациях следует включать проверку существования файла. В противном случае файл перезаписывается файлом с тем же именем.</span><span class="sxs-lookup"><span data-stu-id="ceca3-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="ceca3-291">Предоставьте дополнительную логику для соответствия спецификациям приложения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="ceca3-292">Проверка размера</span><span class="sxs-lookup"><span data-stu-id="ceca3-292">Size validation</span></span>

<span data-ttu-id="ceca3-293">Ограничьте размер передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="ceca3-294">В примере приложения размер файла ограничен 2 МБ (указывается в байтах).</span><span class="sxs-lookup"><span data-stu-id="ceca3-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="ceca3-295">Ограничение предоставляется через [конфигурацию](xref:fundamentals/configuration/index) из *appsettings.json* файла:</span><span class="sxs-lookup"><span data-stu-id="ceca3-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="ceca3-296">В классы `PageModel` внедряется `FileSizeLimit`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="ceca3-297">Если размер файла превышает ограничение, файл отклоняется:</span><span class="sxs-lookup"><span data-stu-id="ceca3-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="ceca3-298">Сопоставление значения атрибута имени и имени параметра метода POST</span><span class="sxs-lookup"><span data-stu-id="ceca3-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="ceca3-299">В других Razor формах, которые передают данные формы или используют JavaScript `FormData` непосредственно, имя, указанное в элементе формы, или `FormData` должно совпадать с именем параметра в действии контроллера.</span><span class="sxs-lookup"><span data-stu-id="ceca3-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="ceca3-300">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ceca3-300">In the following example:</span></span>

* <span data-ttu-id="ceca3-301">При использовании элемента `<input>` атрибуту `name` присваивается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="ceca3-302">При использовании `FormData` в JavaScript для имени задается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="ceca3-303">Используйте соответствующее имя для параметра метода C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="ceca3-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="ceca3-304">Для Razor метода обработчика страницы страниц с именем `Upload` :</span><span class="sxs-lookup"><span data-stu-id="ceca3-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="ceca3-305">Для метода действия контроллера POST приложения MVC:</span><span class="sxs-lookup"><span data-stu-id="ceca3-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="ceca3-306">Конфигурация сервера и приложения</span><span class="sxs-lookup"><span data-stu-id="ceca3-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="ceca3-307">Ограничение длины составного текста</span><span class="sxs-lookup"><span data-stu-id="ceca3-307">Multipart body length limit</span></span>

<span data-ttu-id="ceca3-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> устанавливает ограничение длины каждого составного текста.</span><span class="sxs-lookup"><span data-stu-id="ceca3-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="ceca3-309">Разделы формы, превышающие это ограничение, вызовут <xref:System.IO.InvalidDataException> при синтаксическом анализе.</span><span class="sxs-lookup"><span data-stu-id="ceca3-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="ceca3-310">Значение по умолчанию — 134 217 728 байт (128 МБ).</span><span class="sxs-lookup"><span data-stu-id="ceca3-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="ceca3-311">Настройте ограничение с помощью параметра <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ceca3-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> используется для настройки <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="ceca3-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="ceca3-313">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ceca3-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ceca3-314">В Razor приложении "страницы" или в приложении MVC примените фильтр к модели страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="ceca3-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="ceca3-315">Максимальный размер текста запроса Kestrel</span><span class="sxs-lookup"><span data-stu-id="ceca3-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="ceca3-316">Для приложений, размещенных в Kestrel, по умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="ceca3-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="ceca3-317">Настройте ограничение с помощью параметра сервера Kestrel [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size):</span><span class="sxs-lookup"><span data-stu-id="ceca3-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="ceca3-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> используется для настройки [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="ceca3-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="ceca3-319">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ceca3-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ceca3-320">В Razor приложении Pages или MVC примените фильтр к классу обработчика страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="ceca3-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="ceca3-321">`RequestSizeLimitAttribute`Можно также применить с помощью [`@attribute`](xref:mvc/views/razor#attribute) Razor директивы:</span><span class="sxs-lookup"><span data-stu-id="ceca3-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="ceca3-322">Другие ограничения Kestrel</span><span class="sxs-lookup"><span data-stu-id="ceca3-322">Other Kestrel limits</span></span>

<span data-ttu-id="ceca3-323">Другие ограничения Kestrel могут применяться для приложений, размещенных в Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ceca3-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="ceca3-324">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="ceca3-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel/options#maximum-client-connections)
* [<span data-ttu-id="ceca3-325">Скорость передачи данных запросов и ответов</span><span class="sxs-lookup"><span data-stu-id="ceca3-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel/options#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="ceca3-326">IIS</span><span class="sxs-lookup"><span data-stu-id="ceca3-326">IIS</span></span>

<span data-ttu-id="ceca3-327">Ограничение запроса по умолчанию ( `maxAllowedContentLength` ) составляет 30 000 000 байт, что составляет примерно 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="ceca3-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="ceca3-328">Настройка ограничения в `web.config` файле.</span><span class="sxs-lookup"><span data-stu-id="ceca3-328">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="ceca3-329">В следующем примере для ограничения установлено значение 50 МБ (52 428 800 байт):</span><span class="sxs-lookup"><span data-stu-id="ceca3-329">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="ceca3-330">`maxAllowedContentLength`Параметр применяется только к службам IIS.</span><span class="sxs-lookup"><span data-stu-id="ceca3-330">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="ceca3-331">Дополнительные сведения см. в разделе [ограничения `<requestLimits>` запросов ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="ceca3-331">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ceca3-332">Диагностика</span><span class="sxs-lookup"><span data-stu-id="ceca3-332">Troubleshoot</span></span>

<span data-ttu-id="ceca3-333">Ниже описываются некоторые распространенные проблемы, которые возникают при передаче файлов, и возможные способы их решения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-333">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="ceca3-334">Ошибка "Не найдено" при развертывании на сервере IIS</span><span class="sxs-lookup"><span data-stu-id="ceca3-334">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="ceca3-335">Следующая ошибка свидетельствует о том, что размер передаваемых файлов превышает настроенное на сервере ограничение длины содержимого:</span><span class="sxs-lookup"><span data-stu-id="ceca3-335">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="ceca3-336">Дополнительные сведения см. в описании [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="ceca3-336">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="ceca3-337">Сбой подключения</span><span class="sxs-lookup"><span data-stu-id="ceca3-337">Connection failure</span></span>

<span data-ttu-id="ceca3-338">Ошибка соединения и сброс соединения с сервером, скорее всего, означают, что размер отправленного файла превышает максимальную длину текста запроса Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ceca3-338">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="ceca3-339">Дополнительные сведения см. в разделе [Максимальный размер текста запроса Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="ceca3-339">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="ceca3-340">Может также потребоваться настроить ограничения подключений клиентов Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ceca3-340">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="ceca3-341">Исключение, связанное с пустой ссылкой в IFormFile</span><span class="sxs-lookup"><span data-stu-id="ceca3-341">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="ceca3-342">Если контроллер принимает передаваемые файлы с помощью <xref:Microsoft.AspNetCore.Http.IFormFile>, но значение равно `null`, проверьте, указан ли в HTML-форме атрибут `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-342">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="ceca3-343">Если этот атрибут не задан для элемента `<form>`, передача файлов происходить не будет и все связанные аргументы <xref:Microsoft.AspNetCore.Http.IFormFile> будут иметь значение `null`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-343">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="ceca3-344">Убедитесь также, что [именование передачи в данных формы совпадает с именованием приложения](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="ceca3-344">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="ceca3-345">Поток слишком длинный</span><span class="sxs-lookup"><span data-stu-id="ceca3-345">Stream was too long</span></span>

<span data-ttu-id="ceca3-346">В примерах в этом разделе <xref:System.IO.MemoryStream> используется для хранения содержимого отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-346">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="ceca3-347">Максимальный размер `MemoryStream` ограничен значением `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-347">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="ceca3-348">Если в сценарии передачи файлов в приложении требуется хранить содержимое, размер которого больше 50 МБ, используйте другой подход, не зависящий от одного только `MemoryStream`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-348">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="ceca3-349">Действия ASP.NET Core поддерживают передачу одного или нескольких файлов с помощью привязки модели с буферизацией для небольших файлов или потоковой передачи без буферизации для более крупных файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-349">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="ceca3-350">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ceca3-350">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="ceca3-351">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ceca3-351">Security considerations</span></span>

<span data-ttu-id="ceca3-352">Необходимо соблюдать осторожность при предоставлении пользователям возможности отправки файлов на сервер.</span><span class="sxs-lookup"><span data-stu-id="ceca3-352">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="ceca3-353">Злоумышленники могут попытаться:</span><span class="sxs-lookup"><span data-stu-id="ceca3-353">Attackers may attempt to:</span></span>

* <span data-ttu-id="ceca3-354">Выполнить атаку типа [отказ в обслуживании](/windows-hardware/drivers/ifs/denial-of-service).</span><span class="sxs-lookup"><span data-stu-id="ceca3-354">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="ceca3-355">Передать вирусы и вредоносные программы.</span><span class="sxs-lookup"><span data-stu-id="ceca3-355">Upload viruses or malware.</span></span>
* <span data-ttu-id="ceca3-356">Нарушить безопасность сетей и серверов другими способами.</span><span class="sxs-lookup"><span data-stu-id="ceca3-356">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="ceca3-357">Ниже приведены некоторые действия по обеспечению безопасности, которые снижают вероятность успешных атак.</span><span class="sxs-lookup"><span data-stu-id="ceca3-357">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="ceca3-358">Передавайте файлы в выделенную область для отправки файлов, желательно не на системный диск.</span><span class="sxs-lookup"><span data-stu-id="ceca3-358">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="ceca3-359">Использование выделенного расположения упрощает применение мер безопасности к отправленным файлам.</span><span class="sxs-lookup"><span data-stu-id="ceca3-359">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="ceca3-360">Отключите разрешения на выполнение для расположения отправки файла.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ceca3-360">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="ceca3-361">**Не** сохраняйте переданные файлы в дереве каталогов, где находится приложение.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ceca3-361">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="ceca3-362">Используйте безопасное имя файла, определяемое приложением.</span><span class="sxs-lookup"><span data-stu-id="ceca3-362">Use a safe file name determined by the app.</span></span> <span data-ttu-id="ceca3-363">Не используйте имя файла, предоставленное пользователем, или имя ненадежного файла отправленного файла. &dagger; HTML кодирует ненадежное имя файла при его отображении.</span><span class="sxs-lookup"><span data-stu-id="ceca3-363">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="ceca3-364">Например, запись имени файла или отображение в пользовательском интерфейсе ( Razor автоматически кодируется в формате HTML).</span><span class="sxs-lookup"><span data-stu-id="ceca3-364">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="ceca3-365">Разрешите только утвержденные расширения файлов для спецификации на проектирование приложения.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ceca3-365">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="ceca3-366">Убедитесь, что на сервере выполняются проверки на стороне клиента. &dagger; Проверки на стороне клиента легко обойти.</span><span class="sxs-lookup"><span data-stu-id="ceca3-366">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="ceca3-367">Проверьте размер отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-367">Check the size of an uploaded file.</span></span> <span data-ttu-id="ceca3-368">Установите максимальный предельный размер, чтобы предотвратить передачу больших объемов данных.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ceca3-368">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="ceca3-369">Если файлы не должны перезаписываться переданным файлом с тем же именем, перед отправкой файла проверьте его имя в базе данных или физическом хранилище.</span><span class="sxs-lookup"><span data-stu-id="ceca3-369">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="ceca3-370">**Запустите сканер для проверки отправляемого содержимого на наличие вирусов и вредоносных программ, прежде чем сохранять файл.**</span><span class="sxs-lookup"><span data-stu-id="ceca3-370">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="ceca3-371">&dagger;Пример приложения демонстрирует подход, который соответствует критериям.</span><span class="sxs-lookup"><span data-stu-id="ceca3-371">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="ceca3-372">Отправка в систему вредоносного кода часто является первым шагом перед выполнением кода, который может:</span><span class="sxs-lookup"><span data-stu-id="ceca3-372">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="ceca3-373">полностью получить контроль над системой;</span><span class="sxs-lookup"><span data-stu-id="ceca3-373">Completely gain control of a system.</span></span>
> * <span data-ttu-id="ceca3-374">перезагрузить систему так, что она окажется в неработоспособном состоянии;</span><span class="sxs-lookup"><span data-stu-id="ceca3-374">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="ceca3-375">скомпрометировать пользовательские или системные данные;</span><span class="sxs-lookup"><span data-stu-id="ceca3-375">Compromise user or system data.</span></span>
> * <span data-ttu-id="ceca3-376">применить граффити к открытому интерфейсу.</span><span class="sxs-lookup"><span data-stu-id="ceca3-376">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="ceca3-377">Сведения об уменьшении контактной зоны атаки во время приема файлов от пользователей см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="ceca3-377">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="ceca3-378">Неограниченная отправка файлов</span><span class="sxs-lookup"><span data-stu-id="ceca3-378">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="ceca3-379">Безопасность Azure: убедитесь в наличии надлежащих мер контроля при получении файлов от пользователей</span><span class="sxs-lookup"><span data-stu-id="ceca3-379">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="ceca3-380">Дополнительные сведения о реализации мер безопасности, включая примеры из примера приложения, см. в статье [Передача файлов в ASP.NET Core](#validation).</span><span class="sxs-lookup"><span data-stu-id="ceca3-380">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="ceca3-381">Сценарии использования хранилища</span><span class="sxs-lookup"><span data-stu-id="ceca3-381">Storage scenarios</span></span>

<span data-ttu-id="ceca3-382">К общим вариантам хранилища файлов относятся следующие:</span><span class="sxs-lookup"><span data-stu-id="ceca3-382">Common storage options for files include:</span></span>

* <span data-ttu-id="ceca3-383">База данных</span><span class="sxs-lookup"><span data-stu-id="ceca3-383">Database</span></span>

  * <span data-ttu-id="ceca3-384">В случае отправки небольших файлов база данных часто работает быстрее, чем физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="ceca3-384">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="ceca3-385">База данных часто более удобна по сравнению с вариантами физического хранилища, так как получение записи из базы пользовательских данных может одновременно предоставить содержимое файла (например, изображение аватара).</span><span class="sxs-lookup"><span data-stu-id="ceca3-385">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="ceca3-386">Эксплуатация базы данных потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="ceca3-386">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="ceca3-387">Физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="ceca3-387">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="ceca3-388">Обработка передачи больших файлов:</span><span class="sxs-lookup"><span data-stu-id="ceca3-388">For large file uploads:</span></span>
    * <span data-ttu-id="ceca3-389">Ограничения базы данных могут ограничивать размер передачи.</span><span class="sxs-lookup"><span data-stu-id="ceca3-389">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="ceca3-390">Физическое хранилище часто менее экономически выгодно, чем хранилище в базе данных.</span><span class="sxs-lookup"><span data-stu-id="ceca3-390">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="ceca3-391">Эксплуатация физического хранилища потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="ceca3-391">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="ceca3-392">Процесс приложения должен иметь разрешения на чтение и запись для места хранения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-392">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="ceca3-393">**Никогда не предоставляйте разрешение на выполнение.**</span><span class="sxs-lookup"><span data-stu-id="ceca3-393">**Never grant execute permission.**</span></span>

* <span data-ttu-id="ceca3-394">Служба хранилища данных (например, хранилище [BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)).</span><span class="sxs-lookup"><span data-stu-id="ceca3-394">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="ceca3-395">Обычно службы обеспечивают улучшенную масштабируемость и устойчивость по сравнению с локальными решениями, которые обычно подвержены единым точкам отказа.</span><span class="sxs-lookup"><span data-stu-id="ceca3-395">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="ceca3-396">Затраты на использование служб обычно ниже в сценариях с крупномасштабной инфраструктурой хранения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-396">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="ceca3-397">Дополнительные сведения см. в разделе [Краткое руководство. Создание большого двоичного объекта в хранилище объектов с помощью .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="ceca3-397">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="ceca3-398">Сценарии передачи файлов</span><span class="sxs-lookup"><span data-stu-id="ceca3-398">File upload scenarios</span></span>

<span data-ttu-id="ceca3-399">Есть два распространенных подхода к передаче файлов — буферизация и потоковая передача.</span><span class="sxs-lookup"><span data-stu-id="ceca3-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="ceca3-400">**ответов**</span><span class="sxs-lookup"><span data-stu-id="ceca3-400">**Buffering**</span></span>

<span data-ttu-id="ceca3-401">Весь файл считывается в <xref:Microsoft.AspNetCore.Http.IFormFile> (представление файла на C#, используемого для обработки или сохранения файла).</span><span class="sxs-lookup"><span data-stu-id="ceca3-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="ceca3-402">Потребление ресурсов (диска, памяти) при передаче файлов зависит от количества и размера одновременно передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="ceca3-403">При попытке приложения поместить в буфер слишком много файлов может произойти аварийное завершение работы сайта из-за нехватки памяти или места на диске.</span><span class="sxs-lookup"><span data-stu-id="ceca3-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="ceca3-404">Если размер или частота отправки файлов исчерпывают ресурсы приложения, используйте потоковую передачу.</span><span class="sxs-lookup"><span data-stu-id="ceca3-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="ceca3-405">Один буферизованный файл размером свыше 64 КБ перемещается из памяти во временный файл на диске.</span><span class="sxs-lookup"><span data-stu-id="ceca3-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="ceca3-406">Буферизация небольших файлов описана в следующих разделах этой статьи:</span><span class="sxs-lookup"><span data-stu-id="ceca3-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="ceca3-407">Физическое хранилище</span><span class="sxs-lookup"><span data-stu-id="ceca3-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="ceca3-408">База данных</span><span class="sxs-lookup"><span data-stu-id="ceca3-408">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="ceca3-409">**Потоковая передача**</span><span class="sxs-lookup"><span data-stu-id="ceca3-409">**Streaming**</span></span>

<span data-ttu-id="ceca3-410">Файл можно получить с помощью составного запроса. Затем он обрабатывается или сохраняется приложением напрямую.</span><span class="sxs-lookup"><span data-stu-id="ceca3-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="ceca3-411">Потоковая передача повышает производительность не значительно.</span><span class="sxs-lookup"><span data-stu-id="ceca3-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="ceca3-412">При отправке файлов потоковая передача снижает нагрузку на память или на место на диске.</span><span class="sxs-lookup"><span data-stu-id="ceca3-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="ceca3-413">Потоковая передача больших файлов рассматривается в разделе [Передача больших файлов с помощью потоковой передачи](#upload-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="ceca3-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="ceca3-414">Передача небольших файлов с привязкой буферизованной модели к физическому хранилищу</span><span class="sxs-lookup"><span data-stu-id="ceca3-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="ceca3-415">Для передачи небольших файлов можно применить составную форму или сформировать запрос POST на языке JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ceca3-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="ceca3-416">В следующем примере показано использование Razor формы страниц для передачи одного файла (*pages/буффередсинглефилеуплоадфисикал. cshtml* в примере приложения):</span><span class="sxs-lookup"><span data-stu-id="ceca3-416">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="ceca3-417">Следующий пример аналогичен предыдущему примеру, за исключением следующего:</span><span class="sxs-lookup"><span data-stu-id="ceca3-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="ceca3-418">JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) используется для отправки данных формы.</span><span class="sxs-lookup"><span data-stu-id="ceca3-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="ceca3-419">Проверка не выполняется.</span><span class="sxs-lookup"><span data-stu-id="ceca3-419">There's no validation.</span></span>

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

<span data-ttu-id="ceca3-420">Для выполнения отправки формы в JavaScript для клиентов, которые [не поддерживают Fetch API](https://caniuse.com/#feat=fetch), используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="ceca3-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="ceca3-421">Используйте функцию Fetch Polyfill (например, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="ceca3-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="ceca3-422">Используйте ключевое слово `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="ceca3-423">Пример:</span><span class="sxs-lookup"><span data-stu-id="ceca3-423">For example:</span></span>

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

<span data-ttu-id="ceca3-424">Для поддержки передачи файлов в HTML-формах должен указываться тип кодировки `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="ceca3-425">Для элемента ввода `files`, поддерживающего отправку нескольких файлов, в элементе `<input>` необходимо указать атрибут `multiple`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="ceca3-426">Доступ к отдельным файлам, переданным на сервер, можно получать посредством [привязки модели](xref:mvc/models/model-binding) с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="ceca3-427">В примере приложения показано несколько отправок буферизованных файлов для баз данных и физических хранилищ.</span><span class="sxs-lookup"><span data-stu-id="ceca3-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="ceca3-428">**Не** используйте свойство `FileName` объекта <xref:Microsoft.AspNetCore.Http.IFormFile>, кроме как для отображения и ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="ceca3-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="ceca3-429">При отображении или ведении журнала кодируйте имя файла в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="ceca3-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="ceca3-430">Злоумышленник может предоставить имя вредоносного файла, включая полные или относительные пути.</span><span class="sxs-lookup"><span data-stu-id="ceca3-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="ceca3-431">Приложения должны:</span><span class="sxs-lookup"><span data-stu-id="ceca3-431">Applications should:</span></span>
>
> * <span data-ttu-id="ceca3-432">удалить путь из имени файла, указываемого пользователем;</span><span class="sxs-lookup"><span data-stu-id="ceca3-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="ceca3-433">сохранить имя файла, закодированное в формате HTML, откуда был удален путь, для пользовательского интерфейса или ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="ceca3-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="ceca3-434">создать случайное имя файла для хранения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="ceca3-435">Следующий код удаляет путь из имени файла:</span><span class="sxs-lookup"><span data-stu-id="ceca3-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="ceca3-436">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ceca3-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="ceca3-437">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="ceca3-437">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ceca3-438">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ceca3-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ceca3-439">Проверка</span><span class="sxs-lookup"><span data-stu-id="ceca3-439">Validation</span></span>](#validation)

<span data-ttu-id="ceca3-440">При отправке файлов с помощью привязки модели и <xref:Microsoft.AspNetCore.Http.IFormFile> метод действия может принимать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="ceca3-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="ceca3-441">Один файл <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="ceca3-442">Любая из следующих коллекций, представляющих несколько файлов:</span><span class="sxs-lookup"><span data-stu-id="ceca3-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="ceca3-443">[Числ](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="ceca3-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="ceca3-444">Привязка сопоставляет файлы форм по имени.</span><span class="sxs-lookup"><span data-stu-id="ceca3-444">Binding matches form files by name.</span></span> <span data-ttu-id="ceca3-445">Например, значение HTML `name` в `<input type="file" name="formFile">` должно соответствовать привязанному к C# параметру или свойству (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="ceca3-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="ceca3-446">Дополнительные сведения см. в разделе [Сопоставление значения атрибута имени и имени параметра метода POST](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="ceca3-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="ceca3-447">Следующий пример:</span><span class="sxs-lookup"><span data-stu-id="ceca3-447">The following example:</span></span>

* <span data-ttu-id="ceca3-448">Циклично отправляет один или несколько передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="ceca3-449">Использует метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы вернуть полный путь к файлу, включая его имя.</span><span class="sxs-lookup"><span data-stu-id="ceca3-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="ceca3-450">Сохраняет файлы в локальную файловую систему, используя имя файла, созданное приложением.</span><span class="sxs-lookup"><span data-stu-id="ceca3-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="ceca3-451">Возвращает общее число и размер отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-451">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="ceca3-452">Чтобы создать имя файла без пути, используйте `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="ceca3-453">В следующем примере путь получен из конфигурации:</span><span class="sxs-lookup"><span data-stu-id="ceca3-453">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="ceca3-454">Передаваемый в  путь <xref:System.IO.FileStream> *должен* содержать имя файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="ceca3-455">Если имя файла не указано, в среде выполнения возникает исключение <xref:System.UnauthorizedAccessException>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="ceca3-456">Файлы, передаваемые с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>, буферизуются в памяти или на диске на сервере перед обработкой.</span><span class="sxs-lookup"><span data-stu-id="ceca3-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="ceca3-457">Внутри метода действия содержимое <xref:Microsoft.AspNetCore.Http.IFormFile> доступно в виде <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="ceca3-458">Помимо локальной файловой системы, файлы можно сохранять в сетевой папке или в службе хранилища файлов, например в хранилище [BLOB-объектов Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="ceca3-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="ceca3-459">Еще один пример, который перебирает несколько файлов для отправки и использует надежные имена файлов, см. в файле *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="ceca3-460">Метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) вызывает исключение <xref:System.IO.IOException> в случае создания более чем 65 535 файлов без удаления предыдущих временных файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="ceca3-461">Ограничение в 65 535 файлов предусмотрено для каждого сервера.</span><span class="sxs-lookup"><span data-stu-id="ceca3-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="ceca3-462">Дополнительные сведения об этом ограничении в ОС Windows см. в примечаниях в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="ceca3-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * <span data-ttu-id="ceca3-463">[Функция GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks);</span><span class="sxs-lookup"><span data-stu-id="ceca3-463">[GetTempFileNameA function](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)</span></span>
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="ceca3-464">Передача небольших файлов с привязкой буферизованной модели к базе данных</span><span class="sxs-lookup"><span data-stu-id="ceca3-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="ceca3-465">Для сохранения данных двоичных файлов в базе данных с помощью [Entity Framework](/ef/core/index) определите для сущности свойство массива <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="ceca3-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="ceca3-466">Укажите свойство модели страницы для класса, который содержит <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="ceca3-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="ceca3-467"><xref:Microsoft.AspNetCore.Http.IFormFile> можно использовать непосредственно как параметр метода действия или свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="ceca3-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="ceca3-468">В предыдущем примере используется свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="ceca3-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="ceca3-469">`FileUpload`Используется в Razor форме страниц:</span><span class="sxs-lookup"><span data-stu-id="ceca3-469">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="ceca3-470">При публикации формы на сервере скопируйте <xref:Microsoft.AspNetCore.Http.IFormFile> в поток и сохраните его в базе данных в виде массива байтов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="ceca3-471">В следующем примере `_dbContext` сохраняет контекст базы данных приложения:</span><span class="sxs-lookup"><span data-stu-id="ceca3-471">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="ceca3-472">Предыдущий пример похож на сценарий, продемонстрированный в примере приложения:</span><span class="sxs-lookup"><span data-stu-id="ceca3-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="ceca3-473">*Pages/BufferedSingleFileUploadDb.cshtml*;</span><span class="sxs-lookup"><span data-stu-id="ceca3-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="ceca3-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="ceca3-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="ceca3-475">При сохранении двоичных данных в реляционных базах данных следует соблюдать осторожность, так как это может отрицательно сказаться на производительности.</span><span class="sxs-lookup"><span data-stu-id="ceca3-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="ceca3-476">Свойство `FileName` параметра <xref:Microsoft.AspNetCore.Http.IFormFile> требует обязательной проверки.</span><span class="sxs-lookup"><span data-stu-id="ceca3-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="ceca3-477">Свойство `FileName` следует использовать только в целях вывода и только после HTML-кодирования.</span><span class="sxs-lookup"><span data-stu-id="ceca3-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="ceca3-478">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ceca3-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="ceca3-479">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="ceca3-479">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ceca3-480">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ceca3-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ceca3-481">Проверка</span><span class="sxs-lookup"><span data-stu-id="ceca3-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="ceca3-482">Передача больших файлов с помощью потоковой передачи</span><span class="sxs-lookup"><span data-stu-id="ceca3-482">Upload large files with streaming</span></span>

<span data-ttu-id="ceca3-483">В приведенном ниже примере демонстрируется использование JavaScript для потоковой передачи файла в действие контроллера.</span><span class="sxs-lookup"><span data-stu-id="ceca3-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="ceca3-484">Токен против подделки файла создается с помощью пользовательского атрибута фильтра и передается в заголовках HTTP клиента, а не в теле запроса.</span><span class="sxs-lookup"><span data-stu-id="ceca3-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="ceca3-485">Так как метод действия обрабатывает передаваемые данные напрямую, привязка модели формы отключается другим пользовательским фильтром.</span><span class="sxs-lookup"><span data-stu-id="ceca3-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="ceca3-486">Внутри действия содержимое формы считывается с помощью объекта `MultipartReader`, который считывает каждый объект `MultipartSection` по отдельности, обрабатывая файл или сохраняя содержимое.</span><span class="sxs-lookup"><span data-stu-id="ceca3-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="ceca3-487">После считывания составных разделов действие выполняет собственную привязку модели.</span><span class="sxs-lookup"><span data-stu-id="ceca3-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="ceca3-488">Начальный ответ страницы загружает форму и сохраняет токен подделки в cookie (с помощью `GenerateAntiforgeryTokenCookieAttribute` атрибута).</span><span class="sxs-lookup"><span data-stu-id="ceca3-488">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="ceca3-489">Для установки с помощью маркера запроса атрибут использует встроенную поддержку защиты от [подделки](xref:security/anti-request-forgery) ASP.NET Core cookie .</span><span class="sxs-lookup"><span data-stu-id="ceca3-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="ceca3-490">Для отключения привязки модели используется `DisableFormValueModelBindingAttribute`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="ceca3-491">В примере приложения `GenerateAntiforgeryTokenCookieAttribute` и применяются в `DisableFormValueModelBindingAttribute` качестве фильтров к моделям приложений страницы `/StreamedSingleFileUploadDb` и `/StreamedSingleFileUploadPhysical` в `Startup.ConfigureServices` с использованием [ Razor соглашений о страницах](xref:razor-pages/razor-pages-conventions).</span><span class="sxs-lookup"><span data-stu-id="ceca3-491">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="ceca3-492">Так как привязка модели не считывает форму, параметры, привязанные из формы, не привязываются (запросы, маршруты и заголовки продолжают работать).</span><span class="sxs-lookup"><span data-stu-id="ceca3-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="ceca3-493">Метод действия работает напрямую со свойством `Request`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="ceca3-494">Для считывания каждого раздела служит объект `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="ceca3-495">Данные "ключ — значение" хранятся в `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="ceca3-496">После считывания составных разделов содержимое `KeyValueAccumulator` используется для привязки данных формы к типу модели.</span><span class="sxs-lookup"><span data-stu-id="ceca3-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="ceca3-497">Полный метод `StreamingController.UploadDatabase` для потоковой передачи в базу данных с EF Core:</span><span class="sxs-lookup"><span data-stu-id="ceca3-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="ceca3-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="ceca3-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="ceca3-499">Полный метод `StreamingController.UploadPhysical` для потоковой передачи в физическое расположение:</span><span class="sxs-lookup"><span data-stu-id="ceca3-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="ceca3-500">В примере приложения проверки обрабатываются с помощью `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="ceca3-501">Проверка</span><span class="sxs-lookup"><span data-stu-id="ceca3-501">Validation</span></span>

<span data-ttu-id="ceca3-502">В классе `FileHelpers` примера приложения демонстрируется несколько проверок буферизованного файла <xref:Microsoft.AspNetCore.Http.IFormFile> и потоковой передачи файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="ceca3-503">Сведения об обработке буферизованных файлов <xref:Microsoft.AspNetCore.Http.IFormFile> в примере приложения см. в описании метода `ProcessFormFile` в файле *Utilities/FileHelpers.cs*.</span><span class="sxs-lookup"><span data-stu-id="ceca3-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="ceca3-504">Сведения об обработке потоковых файлов см. в описании метода `ProcessStreamedFile` в том же файле.</span><span class="sxs-lookup"><span data-stu-id="ceca3-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="ceca3-505">Методы обработки проверки, показанные в примере приложения, не проверяют содержимое отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="ceca3-506">В большинстве рабочих сценариев в файле применяется API сканирования на наличие вирусов и вредоносных программ, прежде чем сделать файл доступным для пользователей или других систем.</span><span class="sxs-lookup"><span data-stu-id="ceca3-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="ceca3-507">Хотя пример в разделе содержит рабочий пример методов проверки, не следует реализовывать класс `FileHelpers` в рабочем приложении, кроме таких случаев:</span><span class="sxs-lookup"><span data-stu-id="ceca3-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="ceca3-508">Вы полностью разбираетесь в реализации.</span><span class="sxs-lookup"><span data-stu-id="ceca3-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="ceca3-509">Вы изменяете реализацию соответствующим образом для среды и спецификаций приложения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="ceca3-510">**Никогда не реализуйте код безопасности в приложении, не выполнив эти требования.**</span><span class="sxs-lookup"><span data-stu-id="ceca3-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="ceca3-511">Проверка содержимого</span><span class="sxs-lookup"><span data-stu-id="ceca3-511">Content validation</span></span>

<span data-ttu-id="ceca3-512">**Используйте сторонний API сканирования на наличие вирусов и вредоносных программ для отправленного содержимого.**</span><span class="sxs-lookup"><span data-stu-id="ceca3-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="ceca3-513">Сканирование файлов требует использования ресурсов сервера в сценариях с большими объемами данных.</span><span class="sxs-lookup"><span data-stu-id="ceca3-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="ceca3-514">Если производительность обработки запросов снижается из-за сканирования файлов, рассмотрите возможность разгрузки сканирования путем использования [фоновой службы](xref:fundamentals/host/hosted-services), возможно, службы, которая работает на сервере, отличном от сервера приложения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="ceca3-515">Как правило, передаваемые файлы хранятся в карантинной области до тех пор, пока фоновый сканер не проверит их на наличие вирусов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="ceca3-516">При передаче файл перемещается в нормальное место хранения файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="ceca3-517">Эти действия обычно выполняются вместе с записью базы данных, которая указывает состояние сканирования файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="ceca3-518">Благодаря такому подходу приложение и сервер приложений остаются в режиме реагирования на запросы.</span><span class="sxs-lookup"><span data-stu-id="ceca3-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="ceca3-519">Проверка расширения файла</span><span class="sxs-lookup"><span data-stu-id="ceca3-519">File extension validation</span></span>

<span data-ttu-id="ceca3-520">Расширение переданного файла должно проверяться в соответствии со списком разрешенных расширений.</span><span class="sxs-lookup"><span data-stu-id="ceca3-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="ceca3-521">Пример:</span><span class="sxs-lookup"><span data-stu-id="ceca3-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="ceca3-522">Проверка подписи файла</span><span class="sxs-lookup"><span data-stu-id="ceca3-522">File signature validation</span></span>

<span data-ttu-id="ceca3-523">Подпись файла определяется по первым нескольким байтам в начале файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="ceca3-524">Эти байты можно использовать, чтобы указать, совпадает ли расширение с содержимым файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="ceca3-525">Пример приложения проверяет подписи файлов на соответствие нескольким распространенным типам файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="ceca3-526">В следующем примере проверяется подпись файла для изображения JPEG:</span><span class="sxs-lookup"><span data-stu-id="ceca3-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="ceca3-527">Дополнительные сведения о получении дополнительных подписей файлов см. [по этой ссылке](https://www.filesignatures.net/) и в официальных спецификациях файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="ceca3-528">Безопасность имени файла</span><span class="sxs-lookup"><span data-stu-id="ceca3-528">File name security</span></span>

<span data-ttu-id="ceca3-529">Никогда не используйте для хранения файла в физическом хранилище имя, предоставляемое клиентом.</span><span class="sxs-lookup"><span data-stu-id="ceca3-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="ceca3-530">Создайте надежное имя файла с помощью [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) или [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы получить полный путь (включая имя файла) для временного хранилища.</span><span class="sxs-lookup"><span data-stu-id="ceca3-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="ceca3-531">Razor автоматически кодирует значения свойств для вывода.</span><span class="sxs-lookup"><span data-stu-id="ceca3-531">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="ceca3-532">Ниже приведен безопасный код, который можно использовать.</span><span class="sxs-lookup"><span data-stu-id="ceca3-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="ceca3-533">За пределами Razor , всегда следует <xref:System.Net.WebUtility.HtmlEncode*> содержимое имени файла из запроса пользователя.</span><span class="sxs-lookup"><span data-stu-id="ceca3-533">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="ceca3-534">Во многих реализациях следует включать проверку существования файла. В противном случае файл перезаписывается файлом с тем же именем.</span><span class="sxs-lookup"><span data-stu-id="ceca3-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="ceca3-535">Предоставьте дополнительную логику для соответствия спецификациям приложения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="ceca3-536">Проверка размера</span><span class="sxs-lookup"><span data-stu-id="ceca3-536">Size validation</span></span>

<span data-ttu-id="ceca3-537">Ограничьте размер передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="ceca3-538">В примере приложения размер файла ограничен 2 МБ (указывается в байтах).</span><span class="sxs-lookup"><span data-stu-id="ceca3-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="ceca3-539">Ограничение предоставляется через [конфигурацию](xref:fundamentals/configuration/index) из *appsettings.json* файла:</span><span class="sxs-lookup"><span data-stu-id="ceca3-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="ceca3-540">В классы `PageModel` внедряется `FileSizeLimit`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="ceca3-541">Если размер файла превышает ограничение, файл отклоняется:</span><span class="sxs-lookup"><span data-stu-id="ceca3-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="ceca3-542">Сопоставление значения атрибута имени и имени параметра метода POST</span><span class="sxs-lookup"><span data-stu-id="ceca3-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="ceca3-543">В других Razor формах, которые передают данные формы или используют JavaScript `FormData` непосредственно, имя, указанное в элементе формы, или `FormData` должно совпадать с именем параметра в действии контроллера.</span><span class="sxs-lookup"><span data-stu-id="ceca3-543">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="ceca3-544">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ceca3-544">In the following example:</span></span>

* <span data-ttu-id="ceca3-545">При использовании элемента `<input>` атрибуту `name` присваивается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="ceca3-546">При использовании `FormData` в JavaScript для имени задается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="ceca3-547">Используйте соответствующее имя для параметра метода C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="ceca3-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="ceca3-548">Для Razor метода обработчика страницы страниц с именем `Upload` :</span><span class="sxs-lookup"><span data-stu-id="ceca3-548">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="ceca3-549">Для метода действия контроллера POST приложения MVC:</span><span class="sxs-lookup"><span data-stu-id="ceca3-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="ceca3-550">Конфигурация сервера и приложения</span><span class="sxs-lookup"><span data-stu-id="ceca3-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="ceca3-551">Ограничение длины составного текста</span><span class="sxs-lookup"><span data-stu-id="ceca3-551">Multipart body length limit</span></span>

<span data-ttu-id="ceca3-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> устанавливает ограничение длины каждого составного текста.</span><span class="sxs-lookup"><span data-stu-id="ceca3-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="ceca3-553">Разделы формы, превышающие это ограничение, вызовут <xref:System.IO.InvalidDataException> при синтаксическом анализе.</span><span class="sxs-lookup"><span data-stu-id="ceca3-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="ceca3-554">Значение по умолчанию — 134 217 728 байт (128 МБ).</span><span class="sxs-lookup"><span data-stu-id="ceca3-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="ceca3-555">Настройте ограничение с помощью параметра <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ceca3-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> используется для настройки <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="ceca3-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="ceca3-557">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ceca3-557">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ceca3-558">В Razor приложении "страницы" или в приложении MVC примените фильтр к модели страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="ceca3-558">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="ceca3-559">Максимальный размер текста запроса Kestrel</span><span class="sxs-lookup"><span data-stu-id="ceca3-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="ceca3-560">Для приложений, размещенных в Kestrel, по умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="ceca3-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="ceca3-561">Настройте ограничение с помощью параметра сервера Kestrel [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size):</span><span class="sxs-lookup"><span data-stu-id="ceca3-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="ceca3-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> используется для настройки [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="ceca3-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="ceca3-563">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ceca3-563">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ceca3-564">В Razor приложении Pages или MVC примените фильтр к классу обработчика страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="ceca3-564">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="ceca3-565">`RequestSizeLimitAttribute`Можно также применить с помощью [`@attribute`](xref:mvc/views/razor#attribute) Razor директивы:</span><span class="sxs-lookup"><span data-stu-id="ceca3-565">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="ceca3-566">Другие ограничения Kestrel</span><span class="sxs-lookup"><span data-stu-id="ceca3-566">Other Kestrel limits</span></span>

<span data-ttu-id="ceca3-567">Другие ограничения Kestrel могут применяться для приложений, размещенных в Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ceca3-567">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="ceca3-568">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="ceca3-568">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="ceca3-569">Скорость передачи данных запросов и ответов</span><span class="sxs-lookup"><span data-stu-id="ceca3-569">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="ceca3-570">IIS</span><span class="sxs-lookup"><span data-stu-id="ceca3-570">IIS</span></span>

<span data-ttu-id="ceca3-571">Ограничение запроса по умолчанию ( `maxAllowedContentLength` ) составляет 30 000 000 байт, что составляет примерно 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="ceca3-571">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="ceca3-572">Настройка ограничения в `web.config` файле.</span><span class="sxs-lookup"><span data-stu-id="ceca3-572">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="ceca3-573">В следующем примере для ограничения установлено значение 50 МБ (52 428 800 байт):</span><span class="sxs-lookup"><span data-stu-id="ceca3-573">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="ceca3-574">`maxAllowedContentLength`Параметр применяется только к службам IIS.</span><span class="sxs-lookup"><span data-stu-id="ceca3-574">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="ceca3-575">Дополнительные сведения см. в разделе [ограничения `<requestLimits>` запросов ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="ceca3-575">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="ceca3-576">Увеличьте максимальный размер текста запроса для HTTP-запроса, задав в параметре <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ceca3-576">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ceca3-577">В следующем примере для ограничения установлено значение 50 МБ (52 428 800 байт):</span><span class="sxs-lookup"><span data-stu-id="ceca3-577">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="ceca3-578">Для получения дополнительной информации см. <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-578">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ceca3-579">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="ceca3-579">Troubleshoot</span></span>

<span data-ttu-id="ceca3-580">Ниже описываются некоторые распространенные проблемы, которые возникают при передаче файлов, и возможные способы их решения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-580">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="ceca3-581">Ошибка "Не найдено" при развертывании на сервере IIS</span><span class="sxs-lookup"><span data-stu-id="ceca3-581">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="ceca3-582">Следующая ошибка свидетельствует о том, что размер передаваемых файлов превышает настроенное на сервере ограничение длины содержимого:</span><span class="sxs-lookup"><span data-stu-id="ceca3-582">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="ceca3-583">Дополнительные сведения см. в описании [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="ceca3-583">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="ceca3-584">Сбой подключения</span><span class="sxs-lookup"><span data-stu-id="ceca3-584">Connection failure</span></span>

<span data-ttu-id="ceca3-585">Ошибка соединения и сброс соединения с сервером, скорее всего, означают, что размер отправленного файла превышает максимальную длину текста запроса Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ceca3-585">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="ceca3-586">Дополнительные сведения см. в разделе [Максимальный размер текста запроса Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="ceca3-586">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="ceca3-587">Может также потребоваться настроить ограничения подключений клиентов Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ceca3-587">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="ceca3-588">Исключение, связанное с пустой ссылкой в IFormFile</span><span class="sxs-lookup"><span data-stu-id="ceca3-588">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="ceca3-589">Если контроллер принимает передаваемые файлы с помощью <xref:Microsoft.AspNetCore.Http.IFormFile>, но значение равно `null`, проверьте, указан ли в HTML-форме атрибут `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-589">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="ceca3-590">Если этот атрибут не задан для элемента `<form>`, передача файлов происходить не будет и все связанные аргументы <xref:Microsoft.AspNetCore.Http.IFormFile> будут иметь значение `null`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-590">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="ceca3-591">Убедитесь также, что [именование передачи в данных формы совпадает с именованием приложения](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="ceca3-591">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="ceca3-592">Поток слишком длинный</span><span class="sxs-lookup"><span data-stu-id="ceca3-592">Stream was too long</span></span>

<span data-ttu-id="ceca3-593">В примерах в этом разделе <xref:System.IO.MemoryStream> используется для хранения содержимого отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-593">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="ceca3-594">Максимальный размер `MemoryStream` ограничен значением `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-594">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="ceca3-595">Если в сценарии передачи файлов в приложении требуется хранить содержимое, размер которого больше 50 МБ, используйте другой подход, не зависящий от одного только `MemoryStream`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-595">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ceca3-596">Действия ASP.NET Core поддерживают передачу одного или нескольких файлов с помощью привязки модели с буферизацией для небольших файлов или потоковой передачи без буферизации для более крупных файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-596">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="ceca3-597">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ceca3-597">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="ceca3-598">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ceca3-598">Security considerations</span></span>

<span data-ttu-id="ceca3-599">Необходимо соблюдать осторожность при предоставлении пользователям возможности отправки файлов на сервер.</span><span class="sxs-lookup"><span data-stu-id="ceca3-599">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="ceca3-600">Злоумышленники могут попытаться:</span><span class="sxs-lookup"><span data-stu-id="ceca3-600">Attackers may attempt to:</span></span>

* <span data-ttu-id="ceca3-601">Выполнить атаку типа [отказ в обслуживании](/windows-hardware/drivers/ifs/denial-of-service).</span><span class="sxs-lookup"><span data-stu-id="ceca3-601">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="ceca3-602">Передать вирусы и вредоносные программы.</span><span class="sxs-lookup"><span data-stu-id="ceca3-602">Upload viruses or malware.</span></span>
* <span data-ttu-id="ceca3-603">Нарушить безопасность сетей и серверов другими способами.</span><span class="sxs-lookup"><span data-stu-id="ceca3-603">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="ceca3-604">Ниже приведены некоторые действия по обеспечению безопасности, которые снижают вероятность успешных атак.</span><span class="sxs-lookup"><span data-stu-id="ceca3-604">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="ceca3-605">Передавайте файлы в выделенную область для отправки файлов, желательно не на системный диск.</span><span class="sxs-lookup"><span data-stu-id="ceca3-605">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="ceca3-606">Использование выделенного расположения упрощает применение мер безопасности к отправленным файлам.</span><span class="sxs-lookup"><span data-stu-id="ceca3-606">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="ceca3-607">Отключите разрешения на выполнение для расположения отправки файла.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ceca3-607">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="ceca3-608">**Не** сохраняйте переданные файлы в дереве каталогов, где находится приложение.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ceca3-608">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="ceca3-609">Используйте безопасное имя файла, определяемое приложением.</span><span class="sxs-lookup"><span data-stu-id="ceca3-609">Use a safe file name determined by the app.</span></span> <span data-ttu-id="ceca3-610">Не используйте имя файла, предоставленное пользователем, или имя ненадежного файла отправленного файла. &dagger; HTML кодирует ненадежное имя файла при его отображении.</span><span class="sxs-lookup"><span data-stu-id="ceca3-610">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="ceca3-611">Например, запись имени файла или отображение в пользовательском интерфейсе ( Razor автоматически кодируется в формате HTML).</span><span class="sxs-lookup"><span data-stu-id="ceca3-611">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="ceca3-612">Разрешите только утвержденные расширения файлов для спецификации на проектирование приложения.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ceca3-612">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="ceca3-613">Убедитесь, что на сервере выполняются проверки на стороне клиента. &dagger; Проверки на стороне клиента легко обойти.</span><span class="sxs-lookup"><span data-stu-id="ceca3-613">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="ceca3-614">Проверьте размер отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-614">Check the size of an uploaded file.</span></span> <span data-ttu-id="ceca3-615">Установите максимальный предельный размер, чтобы предотвратить передачу больших объемов данных.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ceca3-615">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="ceca3-616">Если файлы не должны перезаписываться переданным файлом с тем же именем, перед отправкой файла проверьте его имя в базе данных или физическом хранилище.</span><span class="sxs-lookup"><span data-stu-id="ceca3-616">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="ceca3-617">**Запустите сканер для проверки отправляемого содержимого на наличие вирусов и вредоносных программ, прежде чем сохранять файл.**</span><span class="sxs-lookup"><span data-stu-id="ceca3-617">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="ceca3-618">&dagger;Пример приложения демонстрирует подход, который соответствует критериям.</span><span class="sxs-lookup"><span data-stu-id="ceca3-618">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="ceca3-619">Отправка в систему вредоносного кода часто является первым шагом перед выполнением кода, который может:</span><span class="sxs-lookup"><span data-stu-id="ceca3-619">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="ceca3-620">полностью получить контроль над системой;</span><span class="sxs-lookup"><span data-stu-id="ceca3-620">Completely gain control of a system.</span></span>
> * <span data-ttu-id="ceca3-621">перезагрузить систему так, что она окажется в неработоспособном состоянии;</span><span class="sxs-lookup"><span data-stu-id="ceca3-621">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="ceca3-622">скомпрометировать пользовательские или системные данные;</span><span class="sxs-lookup"><span data-stu-id="ceca3-622">Compromise user or system data.</span></span>
> * <span data-ttu-id="ceca3-623">применить граффити к открытому интерфейсу.</span><span class="sxs-lookup"><span data-stu-id="ceca3-623">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="ceca3-624">Сведения об уменьшении контактной зоны атаки во время приема файлов от пользователей см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="ceca3-624">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="ceca3-625">Неограниченная отправка файлов</span><span class="sxs-lookup"><span data-stu-id="ceca3-625">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="ceca3-626">Безопасность Azure: убедитесь в наличии надлежащих мер контроля при получении файлов от пользователей</span><span class="sxs-lookup"><span data-stu-id="ceca3-626">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="ceca3-627">Дополнительные сведения о реализации мер безопасности, включая примеры из примера приложения, см. в статье [Передача файлов в ASP.NET Core](#validation).</span><span class="sxs-lookup"><span data-stu-id="ceca3-627">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="ceca3-628">Сценарии использования хранилища</span><span class="sxs-lookup"><span data-stu-id="ceca3-628">Storage scenarios</span></span>

<span data-ttu-id="ceca3-629">К общим вариантам хранилища файлов относятся следующие:</span><span class="sxs-lookup"><span data-stu-id="ceca3-629">Common storage options for files include:</span></span>

* <span data-ttu-id="ceca3-630">База данных</span><span class="sxs-lookup"><span data-stu-id="ceca3-630">Database</span></span>

  * <span data-ttu-id="ceca3-631">В случае отправки небольших файлов база данных часто работает быстрее, чем физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="ceca3-631">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="ceca3-632">База данных часто более удобна по сравнению с вариантами физического хранилища, так как получение записи из базы пользовательских данных может одновременно предоставить содержимое файла (например, изображение аватара).</span><span class="sxs-lookup"><span data-stu-id="ceca3-632">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="ceca3-633">Эксплуатация базы данных потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="ceca3-633">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="ceca3-634">Физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="ceca3-634">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="ceca3-635">Обработка передачи больших файлов:</span><span class="sxs-lookup"><span data-stu-id="ceca3-635">For large file uploads:</span></span>
    * <span data-ttu-id="ceca3-636">Ограничения базы данных могут ограничивать размер передачи.</span><span class="sxs-lookup"><span data-stu-id="ceca3-636">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="ceca3-637">Физическое хранилище часто менее экономически выгодно, чем хранилище в базе данных.</span><span class="sxs-lookup"><span data-stu-id="ceca3-637">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="ceca3-638">Эксплуатация физического хранилища потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="ceca3-638">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="ceca3-639">Процесс приложения должен иметь разрешения на чтение и запись для места хранения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-639">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="ceca3-640">**Никогда не предоставляйте разрешение на выполнение.**</span><span class="sxs-lookup"><span data-stu-id="ceca3-640">**Never grant execute permission.**</span></span>

* <span data-ttu-id="ceca3-641">Служба хранилища данных (например, хранилище [BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)).</span><span class="sxs-lookup"><span data-stu-id="ceca3-641">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="ceca3-642">Обычно службы обеспечивают улучшенную масштабируемость и устойчивость по сравнению с локальными решениями, которые обычно подвержены единым точкам отказа.</span><span class="sxs-lookup"><span data-stu-id="ceca3-642">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="ceca3-643">Затраты на использование служб обычно ниже в сценариях с крупномасштабной инфраструктурой хранения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-643">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="ceca3-644">Дополнительные сведения см. в разделе [Краткое руководство. Создание большого двоичного объекта в хранилище объектов с помощью .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="ceca3-644">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="ceca3-645">В этом разделе показан метод <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, но метод <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> можно использовать для сохранения <xref:System.IO.FileStream> в хранилище BLOB-объектов при работе с <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-645">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="ceca3-646">Сценарии передачи файлов</span><span class="sxs-lookup"><span data-stu-id="ceca3-646">File upload scenarios</span></span>

<span data-ttu-id="ceca3-647">Есть два распространенных подхода к передаче файлов — буферизация и потоковая передача.</span><span class="sxs-lookup"><span data-stu-id="ceca3-647">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="ceca3-648">**ответов**</span><span class="sxs-lookup"><span data-stu-id="ceca3-648">**Buffering**</span></span>

<span data-ttu-id="ceca3-649">Весь файл считывается в <xref:Microsoft.AspNetCore.Http.IFormFile> (представление файла на C#, используемого для обработки или сохранения файла).</span><span class="sxs-lookup"><span data-stu-id="ceca3-649">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="ceca3-650">Потребление ресурсов (диска, памяти) при передаче файлов зависит от количества и размера одновременно передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-650">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="ceca3-651">При попытке приложения поместить в буфер слишком много файлов может произойти аварийное завершение работы сайта из-за нехватки памяти или места на диске.</span><span class="sxs-lookup"><span data-stu-id="ceca3-651">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="ceca3-652">Если размер или частота отправки файлов исчерпывают ресурсы приложения, используйте потоковую передачу.</span><span class="sxs-lookup"><span data-stu-id="ceca3-652">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="ceca3-653">Один буферизованный файл размером свыше 64 КБ перемещается из памяти во временный файл на диске.</span><span class="sxs-lookup"><span data-stu-id="ceca3-653">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="ceca3-654">Буферизация небольших файлов описана в следующих разделах этой статьи:</span><span class="sxs-lookup"><span data-stu-id="ceca3-654">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="ceca3-655">Физическое хранилище</span><span class="sxs-lookup"><span data-stu-id="ceca3-655">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="ceca3-656">База данных</span><span class="sxs-lookup"><span data-stu-id="ceca3-656">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="ceca3-657">**Потоковая передача**</span><span class="sxs-lookup"><span data-stu-id="ceca3-657">**Streaming**</span></span>

<span data-ttu-id="ceca3-658">Файл можно получить с помощью составного запроса. Затем он обрабатывается или сохраняется приложением напрямую.</span><span class="sxs-lookup"><span data-stu-id="ceca3-658">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="ceca3-659">Потоковая передача повышает производительность не значительно.</span><span class="sxs-lookup"><span data-stu-id="ceca3-659">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="ceca3-660">При отправке файлов потоковая передача снижает нагрузку на память или на место на диске.</span><span class="sxs-lookup"><span data-stu-id="ceca3-660">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="ceca3-661">Потоковая передача больших файлов рассматривается в разделе [Передача больших файлов с помощью потоковой передачи](#upload-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="ceca3-661">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="ceca3-662">Передача небольших файлов с привязкой буферизованной модели к физическому хранилищу</span><span class="sxs-lookup"><span data-stu-id="ceca3-662">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="ceca3-663">Для передачи небольших файлов можно применить составную форму или сформировать запрос POST на языке JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ceca3-663">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="ceca3-664">В следующем примере показано использование Razor формы страниц для передачи одного файла (*pages/буффередсинглефилеуплоадфисикал. cshtml* в примере приложения):</span><span class="sxs-lookup"><span data-stu-id="ceca3-664">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="ceca3-665">Следующий пример аналогичен предыдущему примеру, за исключением следующего:</span><span class="sxs-lookup"><span data-stu-id="ceca3-665">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="ceca3-666">JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) используется для отправки данных формы.</span><span class="sxs-lookup"><span data-stu-id="ceca3-666">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="ceca3-667">Проверка не выполняется.</span><span class="sxs-lookup"><span data-stu-id="ceca3-667">There's no validation.</span></span>

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

<span data-ttu-id="ceca3-668">Для выполнения отправки формы в JavaScript для клиентов, которые [не поддерживают Fetch API](https://caniuse.com/#feat=fetch), используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="ceca3-668">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="ceca3-669">Используйте функцию Fetch Polyfill (например, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="ceca3-669">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="ceca3-670">Используйте ключевое слово `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-670">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="ceca3-671">Пример:</span><span class="sxs-lookup"><span data-stu-id="ceca3-671">For example:</span></span>

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

<span data-ttu-id="ceca3-672">Для поддержки передачи файлов в HTML-формах должен указываться тип кодировки `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-672">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="ceca3-673">Для элемента ввода `files`, поддерживающего отправку нескольких файлов, в элементе `<input>` необходимо указать атрибут `multiple`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-673">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="ceca3-674">Доступ к отдельным файлам, переданным на сервер, можно получать посредством [привязки модели](xref:mvc/models/model-binding) с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-674">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="ceca3-675">В примере приложения показано несколько отправок буферизованных файлов для баз данных и физических хранилищ.</span><span class="sxs-lookup"><span data-stu-id="ceca3-675">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="ceca3-676">**Не** используйте свойство `FileName` объекта <xref:Microsoft.AspNetCore.Http.IFormFile>, кроме как для отображения и ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="ceca3-676">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="ceca3-677">При отображении или ведении журнала кодируйте имя файла в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="ceca3-677">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="ceca3-678">Злоумышленник может предоставить имя вредоносного файла, включая полные или относительные пути.</span><span class="sxs-lookup"><span data-stu-id="ceca3-678">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="ceca3-679">Приложения должны:</span><span class="sxs-lookup"><span data-stu-id="ceca3-679">Applications should:</span></span>
>
> * <span data-ttu-id="ceca3-680">удалить путь из имени файла, указываемого пользователем;</span><span class="sxs-lookup"><span data-stu-id="ceca3-680">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="ceca3-681">сохранить имя файла, закодированное в формате HTML, откуда был удален путь, для пользовательского интерфейса или ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="ceca3-681">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="ceca3-682">создать случайное имя файла для хранения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-682">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="ceca3-683">Следующий код удаляет путь из имени файла:</span><span class="sxs-lookup"><span data-stu-id="ceca3-683">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="ceca3-684">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ceca3-684">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="ceca3-685">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="ceca3-685">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ceca3-686">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ceca3-686">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ceca3-687">Проверка</span><span class="sxs-lookup"><span data-stu-id="ceca3-687">Validation</span></span>](#validation)

<span data-ttu-id="ceca3-688">При отправке файлов с помощью привязки модели и <xref:Microsoft.AspNetCore.Http.IFormFile> метод действия может принимать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="ceca3-688">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="ceca3-689">Один файл <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-689">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="ceca3-690">Любая из следующих коллекций, представляющих несколько файлов:</span><span class="sxs-lookup"><span data-stu-id="ceca3-690">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="ceca3-691">[Числ](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="ceca3-691">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="ceca3-692">Привязка сопоставляет файлы форм по имени.</span><span class="sxs-lookup"><span data-stu-id="ceca3-692">Binding matches form files by name.</span></span> <span data-ttu-id="ceca3-693">Например, значение HTML `name` в `<input type="file" name="formFile">` должно соответствовать привязанному к C# параметру или свойству (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="ceca3-693">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="ceca3-694">Дополнительные сведения см. в разделе [Сопоставление значения атрибута имени и имени параметра метода POST](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="ceca3-694">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="ceca3-695">Следующий пример:</span><span class="sxs-lookup"><span data-stu-id="ceca3-695">The following example:</span></span>

* <span data-ttu-id="ceca3-696">Циклично отправляет один или несколько передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-696">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="ceca3-697">Использует метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы вернуть полный путь к файлу, включая его имя.</span><span class="sxs-lookup"><span data-stu-id="ceca3-697">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="ceca3-698">Сохраняет файлы в локальную файловую систему, используя имя файла, созданное приложением.</span><span class="sxs-lookup"><span data-stu-id="ceca3-698">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="ceca3-699">Возвращает общее число и размер отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-699">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="ceca3-700">Чтобы создать имя файла без пути, используйте `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-700">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="ceca3-701">В следующем примере путь получен из конфигурации:</span><span class="sxs-lookup"><span data-stu-id="ceca3-701">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="ceca3-702">Передаваемый в  путь <xref:System.IO.FileStream> *должен* содержать имя файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-702">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="ceca3-703">Если имя файла не указано, в среде выполнения возникает исключение <xref:System.UnauthorizedAccessException>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-703">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="ceca3-704">Файлы, передаваемые с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>, буферизуются в памяти или на диске на сервере перед обработкой.</span><span class="sxs-lookup"><span data-stu-id="ceca3-704">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="ceca3-705">Внутри метода действия содержимое <xref:Microsoft.AspNetCore.Http.IFormFile> доступно в виде <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-705">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="ceca3-706">Помимо локальной файловой системы, файлы можно сохранять в сетевой папке или в службе хранилища файлов, например в хранилище [BLOB-объектов Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="ceca3-706">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="ceca3-707">Еще один пример, который перебирает несколько файлов для отправки и использует надежные имена файлов, см. в файле *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-707">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="ceca3-708">Метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) вызывает исключение <xref:System.IO.IOException> в случае создания более чем 65 535 файлов без удаления предыдущих временных файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="ceca3-709">Ограничение в 65 535 файлов предусмотрено для каждого сервера.</span><span class="sxs-lookup"><span data-stu-id="ceca3-709">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="ceca3-710">Дополнительные сведения об этом ограничении в ОС Windows см. в примечаниях в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="ceca3-710">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * <span data-ttu-id="ceca3-711">[Функция GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks);</span><span class="sxs-lookup"><span data-stu-id="ceca3-711">[GetTempFileNameA function](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)</span></span>
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="ceca3-712">Передача небольших файлов с привязкой буферизованной модели к базе данных</span><span class="sxs-lookup"><span data-stu-id="ceca3-712">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="ceca3-713">Для сохранения данных двоичных файлов в базе данных с помощью [Entity Framework](/ef/core/index) определите для сущности свойство массива <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="ceca3-713">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="ceca3-714">Укажите свойство модели страницы для класса, который содержит <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="ceca3-714">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="ceca3-715"><xref:Microsoft.AspNetCore.Http.IFormFile> можно использовать непосредственно как параметр метода действия или свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="ceca3-715"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="ceca3-716">В предыдущем примере используется свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="ceca3-716">The prior example uses a bound model property.</span></span>

<span data-ttu-id="ceca3-717">`FileUpload`Используется в Razor форме страниц:</span><span class="sxs-lookup"><span data-stu-id="ceca3-717">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="ceca3-718">При публикации формы на сервере скопируйте <xref:Microsoft.AspNetCore.Http.IFormFile> в поток и сохраните его в базе данных в виде массива байтов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-718">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="ceca3-719">В следующем примере `_dbContext` сохраняет контекст базы данных приложения:</span><span class="sxs-lookup"><span data-stu-id="ceca3-719">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="ceca3-720">Предыдущий пример похож на сценарий, продемонстрированный в примере приложения:</span><span class="sxs-lookup"><span data-stu-id="ceca3-720">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="ceca3-721">*Pages/BufferedSingleFileUploadDb.cshtml*;</span><span class="sxs-lookup"><span data-stu-id="ceca3-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="ceca3-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="ceca3-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="ceca3-723">При сохранении двоичных данных в реляционных базах данных следует соблюдать осторожность, так как это может отрицательно сказаться на производительности.</span><span class="sxs-lookup"><span data-stu-id="ceca3-723">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="ceca3-724">Свойство `FileName` параметра <xref:Microsoft.AspNetCore.Http.IFormFile> требует обязательной проверки.</span><span class="sxs-lookup"><span data-stu-id="ceca3-724">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="ceca3-725">Свойство `FileName` следует использовать только в целях вывода и только после HTML-кодирования.</span><span class="sxs-lookup"><span data-stu-id="ceca3-725">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="ceca3-726">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ceca3-726">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="ceca3-727">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="ceca3-727">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ceca3-728">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ceca3-728">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ceca3-729">Проверка</span><span class="sxs-lookup"><span data-stu-id="ceca3-729">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="ceca3-730">Передача больших файлов с помощью потоковой передачи</span><span class="sxs-lookup"><span data-stu-id="ceca3-730">Upload large files with streaming</span></span>

<span data-ttu-id="ceca3-731">В приведенном ниже примере демонстрируется использование JavaScript для потоковой передачи файла в действие контроллера.</span><span class="sxs-lookup"><span data-stu-id="ceca3-731">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="ceca3-732">Токен против подделки файла создается с помощью пользовательского атрибута фильтра и передается в заголовках HTTP клиента, а не в теле запроса.</span><span class="sxs-lookup"><span data-stu-id="ceca3-732">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="ceca3-733">Так как метод действия обрабатывает передаваемые данные напрямую, привязка модели формы отключается другим пользовательским фильтром.</span><span class="sxs-lookup"><span data-stu-id="ceca3-733">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="ceca3-734">Внутри действия содержимое формы считывается с помощью объекта `MultipartReader`, который считывает каждый объект `MultipartSection` по отдельности, обрабатывая файл или сохраняя содержимое.</span><span class="sxs-lookup"><span data-stu-id="ceca3-734">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="ceca3-735">После считывания составных разделов действие выполняет собственную привязку модели.</span><span class="sxs-lookup"><span data-stu-id="ceca3-735">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="ceca3-736">Начальный ответ страницы загружает форму и сохраняет токен подделки в cookie (с помощью `GenerateAntiforgeryTokenCookieAttribute` атрибута).</span><span class="sxs-lookup"><span data-stu-id="ceca3-736">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="ceca3-737">Для установки с помощью маркера запроса атрибут использует встроенную поддержку защиты от [подделки](xref:security/anti-request-forgery) ASP.NET Core cookie .</span><span class="sxs-lookup"><span data-stu-id="ceca3-737">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="ceca3-738">Для отключения привязки модели используется `DisableFormValueModelBindingAttribute`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-738">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="ceca3-739">В примере приложения `GenerateAntiforgeryTokenCookieAttribute` и применяются в `DisableFormValueModelBindingAttribute` качестве фильтров к моделям приложений страницы `/StreamedSingleFileUploadDb` и `/StreamedSingleFileUploadPhysical` в `Startup.ConfigureServices` с использованием [ Razor соглашений о страницах](xref:razor-pages/razor-pages-conventions).</span><span class="sxs-lookup"><span data-stu-id="ceca3-739">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="ceca3-740">Так как привязка модели не считывает форму, параметры, привязанные из формы, не привязываются (запросы, маршруты и заголовки продолжают работать).</span><span class="sxs-lookup"><span data-stu-id="ceca3-740">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="ceca3-741">Метод действия работает напрямую со свойством `Request`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-741">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="ceca3-742">Для считывания каждого раздела служит объект `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-742">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="ceca3-743">Данные "ключ — значение" хранятся в `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-743">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="ceca3-744">После считывания составных разделов содержимое `KeyValueAccumulator` используется для привязки данных формы к типу модели.</span><span class="sxs-lookup"><span data-stu-id="ceca3-744">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="ceca3-745">Полный метод `StreamingController.UploadDatabase` для потоковой передачи в базу данных с EF Core:</span><span class="sxs-lookup"><span data-stu-id="ceca3-745">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="ceca3-746">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="ceca3-746">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="ceca3-747">Полный метод `StreamingController.UploadPhysical` для потоковой передачи в физическое расположение:</span><span class="sxs-lookup"><span data-stu-id="ceca3-747">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="ceca3-748">В примере приложения проверки обрабатываются с помощью `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-748">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="ceca3-749">Проверка</span><span class="sxs-lookup"><span data-stu-id="ceca3-749">Validation</span></span>

<span data-ttu-id="ceca3-750">В классе `FileHelpers` примера приложения демонстрируется несколько проверок буферизованного файла <xref:Microsoft.AspNetCore.Http.IFormFile> и потоковой передачи файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-750">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="ceca3-751">Сведения об обработке буферизованных файлов <xref:Microsoft.AspNetCore.Http.IFormFile> в примере приложения см. в описании метода `ProcessFormFile` в файле *Utilities/FileHelpers.cs*.</span><span class="sxs-lookup"><span data-stu-id="ceca3-751">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="ceca3-752">Сведения об обработке потоковых файлов см. в описании метода `ProcessStreamedFile` в том же файле.</span><span class="sxs-lookup"><span data-stu-id="ceca3-752">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="ceca3-753">Методы обработки проверки, показанные в примере приложения, не проверяют содержимое отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-753">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="ceca3-754">В большинстве рабочих сценариев в файле применяется API сканирования на наличие вирусов и вредоносных программ, прежде чем сделать файл доступным для пользователей или других систем.</span><span class="sxs-lookup"><span data-stu-id="ceca3-754">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="ceca3-755">Хотя пример в разделе содержит рабочий пример методов проверки, не следует реализовывать класс `FileHelpers` в рабочем приложении, кроме таких случаев:</span><span class="sxs-lookup"><span data-stu-id="ceca3-755">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="ceca3-756">Вы полностью разбираетесь в реализации.</span><span class="sxs-lookup"><span data-stu-id="ceca3-756">Fully understand the implementation.</span></span>
> * <span data-ttu-id="ceca3-757">Вы изменяете реализацию соответствующим образом для среды и спецификаций приложения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-757">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="ceca3-758">**Никогда не реализуйте код безопасности в приложении, не выполнив эти требования.**</span><span class="sxs-lookup"><span data-stu-id="ceca3-758">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="ceca3-759">Проверка содержимого</span><span class="sxs-lookup"><span data-stu-id="ceca3-759">Content validation</span></span>

<span data-ttu-id="ceca3-760">**Используйте сторонний API сканирования на наличие вирусов и вредоносных программ для отправленного содержимого.**</span><span class="sxs-lookup"><span data-stu-id="ceca3-760">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="ceca3-761">Сканирование файлов требует использования ресурсов сервера в сценариях с большими объемами данных.</span><span class="sxs-lookup"><span data-stu-id="ceca3-761">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="ceca3-762">Если производительность обработки запросов снижается из-за сканирования файлов, рассмотрите возможность разгрузки сканирования путем использования [фоновой службы](xref:fundamentals/host/hosted-services), возможно, службы, которая работает на сервере, отличном от сервера приложения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-762">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="ceca3-763">Как правило, передаваемые файлы хранятся в карантинной области до тех пор, пока фоновый сканер не проверит их на наличие вирусов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-763">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="ceca3-764">При передаче файл перемещается в нормальное место хранения файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-764">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="ceca3-765">Эти действия обычно выполняются вместе с записью базы данных, которая указывает состояние сканирования файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-765">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="ceca3-766">Благодаря такому подходу приложение и сервер приложений остаются в режиме реагирования на запросы.</span><span class="sxs-lookup"><span data-stu-id="ceca3-766">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="ceca3-767">Проверка расширения файла</span><span class="sxs-lookup"><span data-stu-id="ceca3-767">File extension validation</span></span>

<span data-ttu-id="ceca3-768">Расширение переданного файла должно проверяться в соответствии со списком разрешенных расширений.</span><span class="sxs-lookup"><span data-stu-id="ceca3-768">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="ceca3-769">Пример:</span><span class="sxs-lookup"><span data-stu-id="ceca3-769">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="ceca3-770">Проверка подписи файла</span><span class="sxs-lookup"><span data-stu-id="ceca3-770">File signature validation</span></span>

<span data-ttu-id="ceca3-771">Подпись файла определяется по первым нескольким байтам в начале файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-771">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="ceca3-772">Эти байты можно использовать, чтобы указать, совпадает ли расширение с содержимым файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-772">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="ceca3-773">Пример приложения проверяет подписи файлов на соответствие нескольким распространенным типам файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-773">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="ceca3-774">В следующем примере проверяется подпись файла для изображения JPEG:</span><span class="sxs-lookup"><span data-stu-id="ceca3-774">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="ceca3-775">Дополнительные сведения о получении дополнительных подписей файлов см. [по этой ссылке](https://www.filesignatures.net/) и в официальных спецификациях файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-775">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="ceca3-776">Безопасность имени файла</span><span class="sxs-lookup"><span data-stu-id="ceca3-776">File name security</span></span>

<span data-ttu-id="ceca3-777">Никогда не используйте для хранения файла в физическом хранилище имя, предоставляемое клиентом.</span><span class="sxs-lookup"><span data-stu-id="ceca3-777">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="ceca3-778">Создайте надежное имя файла с помощью [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) или [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы получить полный путь (включая имя файла) для временного хранилища.</span><span class="sxs-lookup"><span data-stu-id="ceca3-778">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="ceca3-779">Razor автоматически кодирует значения свойств для вывода.</span><span class="sxs-lookup"><span data-stu-id="ceca3-779">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="ceca3-780">Ниже приведен безопасный код, который можно использовать.</span><span class="sxs-lookup"><span data-stu-id="ceca3-780">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="ceca3-781">За пределами Razor , всегда следует <xref:System.Net.WebUtility.HtmlEncode*> содержимое имени файла из запроса пользователя.</span><span class="sxs-lookup"><span data-stu-id="ceca3-781">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="ceca3-782">Во многих реализациях следует включать проверку существования файла. В противном случае файл перезаписывается файлом с тем же именем.</span><span class="sxs-lookup"><span data-stu-id="ceca3-782">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="ceca3-783">Предоставьте дополнительную логику для соответствия спецификациям приложения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-783">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="ceca3-784">Проверка размера</span><span class="sxs-lookup"><span data-stu-id="ceca3-784">Size validation</span></span>

<span data-ttu-id="ceca3-785">Ограничьте размер передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ceca3-785">Limit the size of uploaded files.</span></span>

<span data-ttu-id="ceca3-786">В примере приложения размер файла ограничен 2 МБ (указывается в байтах).</span><span class="sxs-lookup"><span data-stu-id="ceca3-786">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="ceca3-787">Ограничение предоставляется через [конфигурацию](xref:fundamentals/configuration/index) из *appsettings.json* файла:</span><span class="sxs-lookup"><span data-stu-id="ceca3-787">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="ceca3-788">В классы `PageModel` внедряется `FileSizeLimit`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-788">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="ceca3-789">Если размер файла превышает ограничение, файл отклоняется:</span><span class="sxs-lookup"><span data-stu-id="ceca3-789">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="ceca3-790">Сопоставление значения атрибута имени и имени параметра метода POST</span><span class="sxs-lookup"><span data-stu-id="ceca3-790">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="ceca3-791">В других Razor формах, которые передают данные формы или используют JavaScript `FormData` непосредственно, имя, указанное в элементе формы, или `FormData` должно совпадать с именем параметра в действии контроллера.</span><span class="sxs-lookup"><span data-stu-id="ceca3-791">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="ceca3-792">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ceca3-792">In the following example:</span></span>

* <span data-ttu-id="ceca3-793">При использовании элемента `<input>` атрибуту `name` присваивается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-793">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="ceca3-794">При использовании `FormData` в JavaScript для имени задается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-794">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="ceca3-795">Используйте соответствующее имя для параметра метода C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="ceca3-795">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="ceca3-796">Для Razor метода обработчика страницы страниц с именем `Upload` :</span><span class="sxs-lookup"><span data-stu-id="ceca3-796">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="ceca3-797">Для метода действия контроллера POST приложения MVC:</span><span class="sxs-lookup"><span data-stu-id="ceca3-797">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="ceca3-798">Конфигурация сервера и приложения</span><span class="sxs-lookup"><span data-stu-id="ceca3-798">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="ceca3-799">Ограничение длины составного текста</span><span class="sxs-lookup"><span data-stu-id="ceca3-799">Multipart body length limit</span></span>

<span data-ttu-id="ceca3-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> устанавливает ограничение длины каждого составного текста.</span><span class="sxs-lookup"><span data-stu-id="ceca3-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="ceca3-801">Разделы формы, превышающие это ограничение, вызовут <xref:System.IO.InvalidDataException> при синтаксическом анализе.</span><span class="sxs-lookup"><span data-stu-id="ceca3-801">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="ceca3-802">Значение по умолчанию — 134 217 728 байт (128 МБ).</span><span class="sxs-lookup"><span data-stu-id="ceca3-802">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="ceca3-803">Настройте ограничение с помощью параметра <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ceca3-803">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ceca3-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> используется для настройки <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="ceca3-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="ceca3-805">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ceca3-805">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ceca3-806">В Razor приложении "страницы" или в приложении MVC примените фильтр к модели страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="ceca3-806">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="ceca3-807">Максимальный размер текста запроса Kestrel</span><span class="sxs-lookup"><span data-stu-id="ceca3-807">Kestrel maximum request body size</span></span>

<span data-ttu-id="ceca3-808">Для приложений, размещенных в Kestrel, по умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="ceca3-808">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="ceca3-809">Настройте ограничение с помощью параметра сервера Kestrel [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size):</span><span class="sxs-lookup"><span data-stu-id="ceca3-809">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="ceca3-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> используется для настройки [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="ceca3-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="ceca3-811">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ceca3-811">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ceca3-812">В Razor приложении Pages или MVC примените фильтр к классу обработчика страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="ceca3-812">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="ceca3-813">Другие ограничения Kestrel</span><span class="sxs-lookup"><span data-stu-id="ceca3-813">Other Kestrel limits</span></span>

<span data-ttu-id="ceca3-814">Другие ограничения Kestrel могут применяться для приложений, размещенных в Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ceca3-814">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="ceca3-815">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="ceca3-815">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="ceca3-816">Скорость передачи данных запросов и ответов</span><span class="sxs-lookup"><span data-stu-id="ceca3-816">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="ceca3-817">IIS</span><span class="sxs-lookup"><span data-stu-id="ceca3-817">IIS</span></span>

<span data-ttu-id="ceca3-818">Ограничение запроса по умолчанию ( `maxAllowedContentLength` ) составляет 30 000 000 байт, что составляет примерно 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="ceca3-818">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="ceca3-819">Настройка ограничения в `web.config` файле.</span><span class="sxs-lookup"><span data-stu-id="ceca3-819">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="ceca3-820">В следующем примере для ограничения установлено значение 50 МБ (52 428 800 байт):</span><span class="sxs-lookup"><span data-stu-id="ceca3-820">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="ceca3-821">`maxAllowedContentLength`Параметр применяется только к службам IIS.</span><span class="sxs-lookup"><span data-stu-id="ceca3-821">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="ceca3-822">Дополнительные сведения см. в разделе [ограничения `<requestLimits>` запросов ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="ceca3-822">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="ceca3-823">Увеличьте максимальный размер текста запроса для HTTP-запроса, задав в параметре <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ceca3-823">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ceca3-824">В следующем примере для ограничения установлено значение 50 МБ (52 428 800 байт):</span><span class="sxs-lookup"><span data-stu-id="ceca3-824">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="ceca3-825">Для получения дополнительной информации см. <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="ceca3-825">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ceca3-826">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="ceca3-826">Troubleshoot</span></span>

<span data-ttu-id="ceca3-827">Ниже описываются некоторые распространенные проблемы, которые возникают при передаче файлов, и возможные способы их решения.</span><span class="sxs-lookup"><span data-stu-id="ceca3-827">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="ceca3-828">Ошибка "Не найдено" при развертывании на сервере IIS</span><span class="sxs-lookup"><span data-stu-id="ceca3-828">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="ceca3-829">Следующая ошибка свидетельствует о том, что размер передаваемых файлов превышает настроенное на сервере ограничение длины содержимого:</span><span class="sxs-lookup"><span data-stu-id="ceca3-829">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="ceca3-830">Дополнительные сведения см. в описании [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="ceca3-830">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="ceca3-831">Сбой подключения</span><span class="sxs-lookup"><span data-stu-id="ceca3-831">Connection failure</span></span>

<span data-ttu-id="ceca3-832">Ошибка соединения и сброс соединения с сервером, скорее всего, означают, что размер отправленного файла превышает максимальную длину текста запроса Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ceca3-832">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="ceca3-833">Дополнительные сведения см. в разделе [Максимальный размер текста запроса Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="ceca3-833">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="ceca3-834">Может также потребоваться настроить ограничения подключений клиентов Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ceca3-834">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="ceca3-835">Исключение, связанное с пустой ссылкой в IFormFile</span><span class="sxs-lookup"><span data-stu-id="ceca3-835">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="ceca3-836">Если контроллер принимает передаваемые файлы с помощью <xref:Microsoft.AspNetCore.Http.IFormFile>, но значение равно `null`, проверьте, указан ли в HTML-форме атрибут `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-836">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="ceca3-837">Если этот атрибут не задан для элемента `<form>`, передача файлов происходить не будет и все связанные аргументы <xref:Microsoft.AspNetCore.Http.IFormFile> будут иметь значение `null`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-837">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="ceca3-838">Убедитесь также, что [именование передачи в данных формы совпадает с именованием приложения](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="ceca3-838">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="ceca3-839">Поток слишком длинный</span><span class="sxs-lookup"><span data-stu-id="ceca3-839">Stream was too long</span></span>

<span data-ttu-id="ceca3-840">В примерах в этом разделе <xref:System.IO.MemoryStream> используется для хранения содержимого отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="ceca3-840">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="ceca3-841">Максимальный размер `MemoryStream` ограничен значением `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-841">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="ceca3-842">Если в сценарии передачи файлов в приложении требуется хранить содержимое, размер которого больше 50 МБ, используйте другой подход, не зависящий от одного только `MemoryStream`.</span><span class="sxs-lookup"><span data-stu-id="ceca3-842">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="ceca3-843">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ceca3-843">Additional resources</span></span>

::: moniker range="< aspnetcore-5.0"
* [<span data-ttu-id="ceca3-844">Сток запросов HTTP-подключения</span><span class="sxs-lookup"><span data-stu-id="ceca3-844">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
::: moniker-end
::: moniker range=">= aspnetcore-5.0"
* [<span data-ttu-id="ceca3-845">Сток запросов HTTP-подключения</span><span class="sxs-lookup"><span data-stu-id="ceca3-845">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel/request-draining)
::: moniker-end

* [<span data-ttu-id="ceca3-846">Неограниченная отправка файлов</span><span class="sxs-lookup"><span data-stu-id="ceca3-846">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="ceca3-847">Безопасность Azure: кадр безопасности: Проверка входных данных | Способы их устранения</span><span class="sxs-lookup"><span data-stu-id="ceca3-847">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="ceca3-848">Шаблоны разработки в облаке Azure: шаблон камердинера Key</span><span class="sxs-lookup"><span data-stu-id="ceca3-848">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
