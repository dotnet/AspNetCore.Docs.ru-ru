# <a name="contribute-to-the-aspnet-core-documentation"></a><span data-ttu-id="2cde3-101">Внесите свой вклад в документацию по ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2cde3-101">Contribute to the ASP.NET Core documentation</span></span>

<span data-ttu-id="2cde3-102">В этом документе рассматривается процесс участия в написании статей и примеров кода, размещенных на [сайте документации по ASP.NET](https://docs.microsoft.com/aspnet/).</span><span class="sxs-lookup"><span data-stu-id="2cde3-102">This document covers the process for contributing to the articles and code samples that are hosted on the [ASP.NET documentation site](https://docs.microsoft.com/aspnet/).</span></span> <span data-ttu-id="2cde3-103">Вы можете исправлять опечатки и размещать новые статьи.</span><span class="sxs-lookup"><span data-stu-id="2cde3-103">Typo corrections and new articles are welcome contributions.</span></span>

## <a name="how-to-make-a-simple-correction-or-suggestion"></a><span data-ttu-id="2cde3-104">Как создать простое исправление или предложение</span><span class="sxs-lookup"><span data-stu-id="2cde3-104">How to make a simple correction or suggestion</span></span>

<span data-ttu-id="2cde3-105">Статьи хранятся в репозитории в виде файлов Markdown.</span><span class="sxs-lookup"><span data-stu-id="2cde3-105">Articles are stored in the repository as Markdown files.</span></span> <span data-ttu-id="2cde3-106">Простые изменения в содержимое файла Markdown вносятся в браузере — нажмите на ссылку **Изменить** в правом верхнем углу окна браузера.</span><span class="sxs-lookup"><span data-stu-id="2cde3-106">Simple changes to the content of a Markdown file are made in the browser by selecting the **Edit** link in the upper-right corner of the browser window.</span></span> <span data-ttu-id="2cde3-107">(В узком окне браузера разверните панель **Параметры**, чтобы увидеть ссылку **Изменить**.) Следуйте инструкциям по созданию запроса на вытягивание (PR).</span><span class="sxs-lookup"><span data-stu-id="2cde3-107">(In a narrow browser window, expand the **options** bar to see the **Edit** link.) Follow the directions to create a pull request (PR).</span></span> <span data-ttu-id="2cde3-108">Мы рассмотрим запрос на вытягивание и примем его или предложим изменения.</span><span class="sxs-lookup"><span data-stu-id="2cde3-108">We will review the PR and accept it or suggest changes.</span></span>

## <a name="how-to-make-a-more-complex-submission"></a><span data-ttu-id="2cde3-109">Как сделать более сложные правки</span><span class="sxs-lookup"><span data-stu-id="2cde3-109">How to make a more complex submission</span></span>

<span data-ttu-id="2cde3-110">Требуется базовое представление о [Git и GitHub.com](https://guides.github.com/activities/hello-world/).</span><span class="sxs-lookup"><span data-stu-id="2cde3-110">You need a basic understanding of [Git and GitHub.com](https://guides.github.com/activities/hello-world/).</span></span>

* <span data-ttu-id="2cde3-111">Откройте [проблему](https://github.com/dotnet/AspNetCore.Docs/issues/new), описывающую, что нужно сделать, например изменить существующую статью или создать новую.</span><span class="sxs-lookup"><span data-stu-id="2cde3-111">Open an [issue](https://github.com/dotnet/AspNetCore.Docs/issues/new) describing what you want to do, such as changing an existing article or creating a new one.</span></span> <span data-ttu-id="2cde3-112">Мы часто запрашиваем краткое содержание предлагаемой статьи.</span><span class="sxs-lookup"><span data-stu-id="2cde3-112">We often request an outline for a new topic suggestion.</span></span> <span data-ttu-id="2cde3-113">Дождитесь от нас одобрения, прежде чем тратить время.</span><span class="sxs-lookup"><span data-stu-id="2cde3-113">Wait for approval from the team before you invest much time.</span></span>
* <span data-ttu-id="2cde3-114">Создайте вилку репозитория [aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/) и создайте ветвь для изменений.</span><span class="sxs-lookup"><span data-stu-id="2cde3-114">Fork the [aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/) repo and create a branch for your changes.</span></span>
* <span data-ttu-id="2cde3-115">Отправьте запрос на вытягивание в главную ветвь с вашими изменениями.</span><span class="sxs-lookup"><span data-stu-id="2cde3-115">Submit a PR to master with your changes.</span></span>
* <span data-ttu-id="2cde3-116">Если запрос на вытягивание имеет метку "cla-required", [примите лицензионное соглашение с участником (CLA)](https://cla.dotnetfoundation.org/).</span><span class="sxs-lookup"><span data-stu-id="2cde3-116">If your PR has the label 'cla-required' assigned, [complete the Contribution License Agreement (CLA)](https://cla.dotnetfoundation.org/).</span></span>
* <span data-ttu-id="2cde3-117">Ответьте на обратную связь по запросу на вытягивание.</span><span class="sxs-lookup"><span data-stu-id="2cde3-117">Respond to PR feedback.</span></span>

<span data-ttu-id="2cde3-118">Пример публикации новой статьи в соответствии с этим процессом см. в [проблеме 1477](https://github.com/dotnet/docs/issues/1477) и [запросе на вытягивание 18955](https://github.com/dotnet/docs/pull/18955) в репозитории документов по .NET.</span><span class="sxs-lookup"><span data-stu-id="2cde3-118">For an example where this process led to publication of a new article, see [Issue 1477](https://github.com/dotnet/docs/issues/1477) and [Pull Request 18955](https://github.com/dotnet/docs/pull/18955) in the .NET Docs repository.</span></span> <span data-ttu-id="2cde3-119">В новой статье используется [покрытие кода для модульного тестирования](/dotnet/core/testing/unit-testing-code-coverage).</span><span class="sxs-lookup"><span data-stu-id="2cde3-119">The new article is [Use code coverage for unit testing](/dotnet/core/testing/unit-testing-code-coverage).</span></span>

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a><span data-ttu-id="2cde3-120">Расширение "Пакет создания документов" в Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2cde3-120">Docs Authoring Pack extension in Visual Studio Code</span></span>

<span data-ttu-id="2cde3-121">Если вы используете Visual Studio Code для участия в разработке документации ASP.NET, вы можете повысить производительность, установив расширение [Пакет создания документов](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack).</span><span class="sxs-lookup"><span data-stu-id="2cde3-121">If you use Visual Studio Code to contribute to the ASP.NET documentation, you can boost your productivity by installing the [Docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) extension.</span></span> <span data-ttu-id="2cde3-122">Данное расширение предоставляет широкий набор средств, упрощающий проверку формата Markdown, проверку орфографии кода и создание статей по шаблонам.</span><span class="sxs-lookup"><span data-stu-id="2cde3-122">The extension provides a variety of tools that helps with Markdown linting, code spell checking, and article templates.</span></span>

## <a name="markdown-syntax"></a><span data-ttu-id="2cde3-123">Синтаксис Markdown</span><span class="sxs-lookup"><span data-stu-id="2cde3-123">Markdown syntax</span></span>

<span data-ttu-id="2cde3-124">Статьи написаны в формате [DocFx-flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), который представляет собой расширенную версию [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span><span class="sxs-lookup"><span data-stu-id="2cde3-124">Articles are written in [DocFx-flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), which is a superset of [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span></span> <span data-ttu-id="2cde3-125">Примеры синтаксиса DFM для функций пользовательского интерфейса, обычно используемых в документации по ASP.NET, см. в разделе [Шаблон метаданных и Markdown](https://github.com/dotnet/docs/blob/master/styleguide/template.md) руководства по стилю репозитория документации по .NET.</span><span class="sxs-lookup"><span data-stu-id="2cde3-125">For examples of DFM syntax for UI features commonly used in the ASP.NET documentation, see [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) in the .NET Docs repo style guide.</span></span> 

## <a name="folder-structure-conventions"></a><span data-ttu-id="2cde3-126">Соглашения о структуре папок</span><span class="sxs-lookup"><span data-stu-id="2cde3-126">Folder structure conventions</span></span>

<span data-ttu-id="2cde3-127">Для каждого файла Markdown могут существовать папка для изображений и папка для примера кода.</span><span class="sxs-lookup"><span data-stu-id="2cde3-127">For each Markdown file, a folder for images and a folder for sample code may exist.</span></span> <span data-ttu-id="2cde3-128">Если это статья в виде файла [fundamentals/configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/fundamentals/configuration/index.md), изображения находятся в папке [fundamentals/configuration/index/\_static](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static), а пример файлов проекта приложения — в папке [fundamentals/configuration/index/sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample).</span><span class="sxs-lookup"><span data-stu-id="2cde3-128">If the article is [fundamentals/configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/fundamentals/configuration/index.md), the images are in [fundamentals/configuration/index/\_static](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static) and the sample app project files are in [fundamentals/configuration/index/sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample).</span></span> <span data-ttu-id="2cde3-129">Изображение в файле *fundamentals/configuration/index.md* обрабатывается по следующим правилам Markdown:</span><span class="sxs-lookup"><span data-stu-id="2cde3-129">An image in the *fundamentals/configuration/index.md* file is rendered by the following Markdown:</span></span>

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

<span data-ttu-id="2cde3-130">Все изображения должны иметь [замещающий текст](https://wikipedia.org/wiki/Alt_attribute).</span><span class="sxs-lookup"><span data-stu-id="2cde3-130">All images should have [alternative (alt) text](https://wikipedia.org/wiki/Alt_attribute).</span></span> <span data-ttu-id="2cde3-131">Советы по заданию замещающего текста см. в сетевых ресурсах, например [WebAIM: замещающий текст](https://webaim.org/techniques/alttext/).</span><span class="sxs-lookup"><span data-stu-id="2cde3-131">For advice on specifying alt text, see online resources, such as [WebAIM: Alternative Text](https://webaim.org/techniques/alttext/).</span></span>

<span data-ttu-id="2cde3-132">Имена файлов Markdown и имена файлов изображений должны состоять из символов нижнего регистра.</span><span class="sxs-lookup"><span data-stu-id="2cde3-132">Use lowercase for Markdown file names and image file names.</span></span>

## <a name="internal-links"></a><span data-ttu-id="2cde3-133">Внутренние ссылки</span><span class="sxs-lookup"><span data-stu-id="2cde3-133">Internal links</span></span>

<span data-ttu-id="2cde3-134">Внутренние ссылки должны использовать `uid` целевой статьи со ссылкой xref (текст ссылки задается по заголовку связанного содержимого):</span><span class="sxs-lookup"><span data-stu-id="2cde3-134">Internal links should use the `uid` of the target article with an xref link (link text is set to the linked content's title):</span></span>

```md
<xref:uid_of_the_topic>
```

<span data-ttu-id="2cde3-135">Если заголовок статьи не подходит для текста ссылки (например, слово или фраза в предложении является текстом ссылки), укажите ссылку xref и текст ссылки следующим образом:</span><span class="sxs-lookup"><span data-stu-id="2cde3-135">If the title of the article is unsuitable for link text (for example, a word or phrase in a sentence is the link text), specify the xref link and link text with the following:</span></span>

```md
[link text](xref:uid_of_the_topic)
```

<span data-ttu-id="2cde3-136">Дополнительные сведения см. в разделе [Перекрестные ссылки DocFX](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).</span><span class="sxs-lookup"><span data-stu-id="2cde3-136">For more information, see the [DocFX Cross Reference](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).</span></span>

## <a name="images-and-screenshots"></a><span data-ttu-id="2cde3-137">Изображения и снимки экрана</span><span class="sxs-lookup"><span data-stu-id="2cde3-137">Images and screenshots</span></span>

<span data-ttu-id="2cde3-138">Не включайте изображения в статьи, за исключением следующих случаев:</span><span class="sxs-lookup"><span data-stu-id="2cde3-138">Don't include images with articles, except:</span></span>

* <span data-ttu-id="2cde3-139">если создаются базовые руководства по внедрению (для начинающих);</span><span class="sxs-lookup"><span data-stu-id="2cde3-139">In basic onboarding (beginner) tutorials.</span></span>
* <span data-ttu-id="2cde3-140">если изображение необходимо для понимания.</span><span class="sxs-lookup"><span data-stu-id="2cde3-140">When an image is needed for clarity.</span></span>

<span data-ttu-id="2cde3-141">Эти ограничения позволяют уменьшить размер репозитория.</span><span class="sxs-lookup"><span data-stu-id="2cde3-141">These restrictions reduce the size of the repository.</span></span>

<span data-ttu-id="2cde3-142">В качестве дополнительного шага убедитесь, что все изображения и снимки экрана в документации сжаты, чтобы не увеличивать размер файла и время загрузки страницы.</span><span class="sxs-lookup"><span data-stu-id="2cde3-142">As an optional step, ensure that any images and screenshots used in the documentation are compressed, which helps with file size and page load performance.</span></span> <span data-ttu-id="2cde3-143">Среди популярных — средства TinyPNG ([веб-сайт TinyPNG](https://tinypng.com/) или [API TinyPNG](https://tinypng.com/developers)) или расширение Visual Studio [Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer).</span><span class="sxs-lookup"><span data-stu-id="2cde3-143">A few popular tools include TinyPNG (using the [TinyPNG website](https://tinypng.com/) or the [TinyPNG API](https://tinypng.com/developers)) or the [Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) Visual Studio extension.</span></span> 

## <a name="code-snippets"></a><span data-ttu-id="2cde3-144">Фрагменты кода</span><span class="sxs-lookup"><span data-stu-id="2cde3-144">Code snippets</span></span>

<span data-ttu-id="2cde3-145">Статьи часто содержат фрагменты кода для иллюстрации.</span><span class="sxs-lookup"><span data-stu-id="2cde3-145">Articles frequently contain code snippets to illustrate points.</span></span> <span data-ttu-id="2cde3-146">DFM позволяет скопировать код в файле Markdown или ссылаться на отдельный файл кода.</span><span class="sxs-lookup"><span data-stu-id="2cde3-146">DFM allows you to copy code into the Markdown file or refer to a separate code file.</span></span> <span data-ttu-id="2cde3-147">Рекомендуется использовать отдельные файлы кода, когда это возможно, чтобы свести к минимуму вероятность ошибок в коде.</span><span class="sxs-lookup"><span data-stu-id="2cde3-147">We prefer to use separate code files whenever possible to minimize the chance of errors in the code.</span></span> <span data-ttu-id="2cde3-148">Файлы кода хранятся в репозитории в соответствии со структурой папок, описанной ранее для образцов проектов.</span><span class="sxs-lookup"><span data-stu-id="2cde3-148">The code files are stored in the repo using the folder structure described earlier for sample projects.</span></span> 

<span data-ttu-id="2cde3-149">В следующих примерах показан [синтаксис фрагмента кода DFM](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) для использования в файле *configuration/index.md*.</span><span class="sxs-lookup"><span data-stu-id="2cde3-149">The following examples illustrate [DFM code snippet syntax](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) for use in a *configuration/index.md* file.</span></span>

<span data-ttu-id="2cde3-150">Для отображения всего файла кода в виде фрагмента:</span><span class="sxs-lookup"><span data-stu-id="2cde3-150">To render an entire code file as a snippet:</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

<span data-ttu-id="2cde3-151">Для подготовки к просмотру части файла как фрагмента с помощью номеров строк:</span><span class="sxs-lookup"><span data-stu-id="2cde3-151">To render a portion of a file as a snippet by using line numbers:</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

<span data-ttu-id="2cde3-152">Для фрагментов C# сошлитесь на [область C#](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region).</span><span class="sxs-lookup"><span data-stu-id="2cde3-152">For C# snippets, reference a [C# region](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region).</span></span> <span data-ttu-id="2cde3-153">По возможности используйте области, а не номера строк, так как номера строк в файле кода, как правило, меняются и теряют синхронизацию с номерами строк в Markdown.</span><span class="sxs-lookup"><span data-stu-id="2cde3-153">Whenever possible, use regions rather than line numbers because line numbers in a code file tend to change and become out of sync with line number references in Markdown.</span></span> <span data-ttu-id="2cde3-154">Области C# могут быть вложенными.</span><span class="sxs-lookup"><span data-stu-id="2cde3-154">C# regions can be nested.</span></span> <span data-ttu-id="2cde3-155">При ссылке на внешнюю область внутренние директивы `#region` и `#endregion` не отображаются во фрагменте кода.</span><span class="sxs-lookup"><span data-stu-id="2cde3-155">If referencing the outer region, the inner `#region` and `#endregion` directives aren't rendered in a snippet.</span></span> 

<span data-ttu-id="2cde3-156">Для подготовки области C# с именем "snippet_Example":</span><span class="sxs-lookup"><span data-stu-id="2cde3-156">To render a C# region named "snippet_Example":</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

<span data-ttu-id="2cde3-157">Чтобы выделить выбранные строки в готовом для просмотра фрагменте кода (обычно выделяются желтым цветом):</span><span class="sxs-lookup"><span data-stu-id="2cde3-157">To highlight selected lines in a rendered snippet (usually renders as yellow background color):</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a><span data-ttu-id="2cde3-158">Тестирование изменений с помощью DocFX</span><span class="sxs-lookup"><span data-stu-id="2cde3-158">Test changes with DocFX</span></span>

<span data-ttu-id="2cde3-159">Протестируйте изменения с помощью [средства командной строки DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), которое создает локально расположенную версию сайта.</span><span class="sxs-lookup"><span data-stu-id="2cde3-159">Test your changes with the [DocFX command-line tool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), which creates a locally hosted version of the site.</span></span> <span data-ttu-id="2cde3-160">DocFX не отображает стиль и расширения сайта, созданные для сайта docs.microsoft.com.</span><span class="sxs-lookup"><span data-stu-id="2cde3-160">DocFX doesn't render style and site extensions created for docs.microsoft.com.</span></span>

<span data-ttu-id="2cde3-161">DocFX требует:</span><span class="sxs-lookup"><span data-stu-id="2cde3-161">DocFX requires:</span></span>

* <span data-ttu-id="2cde3-162">.NET Framework на Windows.</span><span class="sxs-lookup"><span data-stu-id="2cde3-162">.NET Framework on Windows.</span></span>
* <span data-ttu-id="2cde3-163">Mono для Linux или macOS.</span><span class="sxs-lookup"><span data-stu-id="2cde3-163">Mono for Linux or macOS.</span></span> 

### <a name="windows-instructions"></a><span data-ttu-id="2cde3-164">Инструкции для Windows</span><span class="sxs-lookup"><span data-stu-id="2cde3-164">Windows instructions</span></span>

* <span data-ttu-id="2cde3-165">Скачайте и распакуйте *docfx.zip* из [выпусков DocFX](https://github.com/dotnet/docfx/releases).</span><span class="sxs-lookup"><span data-stu-id="2cde3-165">Download and unzip *docfx.zip* from [DocFX releases](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="2cde3-166">Добавьте DocFX в PATH.</span><span class="sxs-lookup"><span data-stu-id="2cde3-166">Add DocFX to your PATH.</span></span>
* <span data-ttu-id="2cde3-167">В командной оболочке перейдите в папку, содержащую файл *docfx.json* (*aspnet* для содержимого ASP.NET или *aspnetcore* для содержимого ASP.NET Core) и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="2cde3-167">In a command shell, navigate to the folder that contains the *docfx.json* file (*aspnet* for ASP.NET content or *aspnetcore* for ASP.NET Core content) and run the following command:</span></span>

  ```console
  docfx --serve
  ```

* <span data-ttu-id="2cde3-168">В браузере перейдите на адрес `http://localhost:8080/group1-dest/`.</span><span class="sxs-lookup"><span data-stu-id="2cde3-168">In a browser, navigate to `http://localhost:8080/group1-dest/`.</span></span>

### <a name="mono-instructions"></a><span data-ttu-id="2cde3-169">Инструкции для Mono</span><span class="sxs-lookup"><span data-stu-id="2cde3-169">Mono instructions</span></span>

* <span data-ttu-id="2cde3-170">Установите Mono через Homebrew:</span><span class="sxs-lookup"><span data-stu-id="2cde3-170">Install Mono via Homebrew:</span></span>

  ```console
  brew install mono
  ```

* <span data-ttu-id="2cde3-171">Загрузите [последнюю версию DocFX](https://github.com/dotnet/docfx/releases).</span><span class="sxs-lookup"><span data-stu-id="2cde3-171">Download the [latest version of DocFX](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="2cde3-172">Извлеките архив *$HOME/bin/docfx*.</span><span class="sxs-lookup"><span data-stu-id="2cde3-172">Extract the archive to *$HOME/bin/docfx*.</span></span>
* <span data-ttu-id="2cde3-173">Создайте пару псевдонимов для **docfx** в оболочке bash.</span><span class="sxs-lookup"><span data-stu-id="2cde3-173">Create a pair of aliases for **docfx** in a bash shell.</span></span> <span data-ttu-id="2cde3-174">Первый псевдоним используется для создания документации.</span><span class="sxs-lookup"><span data-stu-id="2cde3-174">The first alias is used to build the documentation.</span></span> <span data-ttu-id="2cde3-175">Второй псевдоним используется для создания и обслуживания документации.</span><span class="sxs-lookup"><span data-stu-id="2cde3-175">The second alias is used to build and serve the documentation.</span></span>

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* <span data-ttu-id="2cde3-176">В командной оболочке перейдите в папку, содержащую файл *docfx.json* (*aspnet* для содержимого ASP.NET или *aspnetcore* для содержимого ASP.NET Core) и выполните следующую команду, чтобы создавать и обслуживать документы с использованием их псевдонимов.</span><span class="sxs-lookup"><span data-stu-id="2cde3-176">In a command shell, navigate to the folder that contains the *docfx.json* file (*aspnet* for ASP.NET content or *aspnetcore* for ASP.NET Core content) and run the following command to build and serve the docs via its alias:</span></span>

  ```console
  docfx-serve
  ```

* <span data-ttu-id="2cde3-177">В браузере перейдите на адрес `http://localhost:8080/group1-dest/`.</span><span class="sxs-lookup"><span data-stu-id="2cde3-177">In a browser, navigate to `http://localhost:8080/group1-dest/`.</span></span>

## <a name="voice-and-tone"></a><span data-ttu-id="2cde3-178">Стиль и тон</span><span class="sxs-lookup"><span data-stu-id="2cde3-178">Voice and tone</span></span>

<span data-ttu-id="2cde3-179">Наши документы должны быть понятными для самой широкой аудитории.</span><span class="sxs-lookup"><span data-stu-id="2cde3-179">Our goal is to write documentation that is easily understandable by the widest possible audience.</span></span> <span data-ttu-id="2cde3-180">С этой целью мы предлагаем рекомендации по стилю и просим наших авторов следовать им.</span><span class="sxs-lookup"><span data-stu-id="2cde3-180">To that end, we established guidelines for writing style that we ask our contributors to follow.</span></span> <span data-ttu-id="2cde3-181">Дополнительные сведения см. в разделе [Рекомендации по стилю](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) в репозитории .NET.</span><span class="sxs-lookup"><span data-stu-id="2cde3-181">For more information, see [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) in the .NET repo.</span></span>

## <a name="microsoft-writing-style-guide"></a><span data-ttu-id="2cde3-182">Руководство по стилю Майкрософт</span><span class="sxs-lookup"><span data-stu-id="2cde3-182">Microsoft Writing Style Guide</span></span>

<span data-ttu-id="2cde3-183">[Руководство по стилю Майкрософт](https://docs.microsoft.com/style-guide/welcome/) предоставляет рекомендации по стилю и терминологии для всех технических текстов, включая документацию по ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2cde3-183">The [Microsoft Writing Style Guide](https://docs.microsoft.com/style-guide/welcome/) provides writing style and terminology guidance for all forms of technology communication, including the ASP.NET Core documentation.</span></span>

## <a name="redirects"></a><span data-ttu-id="2cde3-184">Перенаправления</span><span class="sxs-lookup"><span data-stu-id="2cde3-184">Redirects</span></span>

<span data-ttu-id="2cde3-185">Если вы удаляете статью, измените ее имя файла или переместите ее в другую папку, создайте перенаправление, чтобы пользователи, добавившие статью в закладки, не получали ошибку *404 Не найдено*.</span><span class="sxs-lookup"><span data-stu-id="2cde3-185">If you delete an article, change its file name, or move it to a different folder, create a redirect so that people who bookmarked the article don't receive a *404 Not Found* error.</span></span> <span data-ttu-id="2cde3-186">Добавьте перенаправление [в главный файл перенаправления](https://github.com/dotnet/AspNetCore.Docs/blob/master/.openpublishing.redirection.json).</span><span class="sxs-lookup"><span data-stu-id="2cde3-186">Add redirects to the [master redirect file](https://github.com/dotnet/AspNetCore.Docs/blob/master/.openpublishing.redirection.json).</span></span>
