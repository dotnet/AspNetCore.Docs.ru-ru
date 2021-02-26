# <a name="contribute-to-the-aspnet-core-documentation"></a>Внесите свой вклад в документацию по ASP.NET Core

В этом документе рассматривается процесс участия в написании статей и примеров кода, размещенных на [сайте документации по ASP.NET](https://docs.microsoft.com/aspnet/). Вы можете исправлять опечатки и размещать новые статьи.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Как создать простое исправление или предложение

Статьи хранятся в репозитории в виде файлов Markdown. Простые изменения в содержимое файла Markdown вносятся в браузере — нажмите на ссылку **Изменить** в правом верхнем углу окна браузера. (В узком окне браузера разверните панель **Параметры**, чтобы увидеть ссылку **Изменить**.) Следуйте инструкциям по созданию запроса на вытягивание (PR). Мы рассмотрим запрос на вытягивание и примем его или предложим изменения.

## <a name="how-to-make-a-more-complex-submission"></a>Как сделать более сложные правки

Требуется базовое представление о [Git и GitHub.com](https://guides.github.com/activities/hello-world/).

* Откройте [проблему](https://github.com/dotnet/AspNetCore.Docs/issues/new), описывающую, что нужно сделать, например изменить существующую статью или создать новую. Мы часто запрашиваем краткое содержание предлагаемой статьи. Дождитесь от нас одобрения, прежде чем тратить время.
* Создайте вилку репозитория [aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/) и создайте ветвь для изменений.
* Отправьте запрос на вытягивание в главную ветвь с вашими изменениями.
* Если запрос на вытягивание имеет метку "cla-required", [примите лицензионное соглашение с участником (CLA)](https://cla.dotnetfoundation.org/).
* Ответьте на обратную связь по запросу на вытягивание.

Пример публикации новой статьи в соответствии с этим процессом см. в [проблеме 1477](https://github.com/dotnet/docs/issues/1477) и [запросе на вытягивание 18955](https://github.com/dotnet/docs/pull/18955) в репозитории документов по .NET. В новой статье используется [покрытие кода для модульного тестирования](/dotnet/core/testing/unit-testing-code-coverage).

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a>Расширение "Пакет создания документов" в Visual Studio Code

Если вы используете Visual Studio Code для участия в разработке документации ASP.NET, вы можете повысить производительность, установив расширение [Пакет создания документов](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack). Данное расширение предоставляет широкий набор средств, упрощающий проверку формата Markdown, проверку орфографии кода и создание статей по шаблонам.

## <a name="markdown-syntax"></a>Синтаксис Markdown

Статьи написаны в формате [DocFx-flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), который представляет собой расширенную версию [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/). Примеры синтаксиса DFM для функций пользовательского интерфейса, обычно используемых в документации по ASP.NET, см. в разделе [Шаблон метаданных и Markdown](https://github.com/dotnet/docs/blob/master/styleguide/template.md) руководства по стилю репозитория документации по .NET. 

## <a name="folder-structure-conventions"></a>Соглашения о структуре папок

Для каждого файла Markdown могут существовать папка для изображений и папка для примера кода. Если это статья в виде файла [fundamentals/configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/fundamentals/configuration/index.md), изображения находятся в папке [fundamentals/configuration/index/\_static](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static), а пример файлов проекта приложения — в папке [fundamentals/configuration/index/sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample). Изображение в файле *fundamentals/configuration/index.md* обрабатывается по следующим правилам Markdown:

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

Все изображения должны иметь [замещающий текст](https://wikipedia.org/wiki/Alt_attribute). Советы по заданию замещающего текста см. в сетевых ресурсах, например [WebAIM: замещающий текст](https://webaim.org/techniques/alttext/).

Имена файлов Markdown и имена файлов изображений должны состоять из символов нижнего регистра.

## <a name="internal-links"></a>Внутренние ссылки

Внутренние ссылки должны использовать `uid` целевой статьи со ссылкой xref (текст ссылки задается по заголовку связанного содержимого):

```md
<xref:uid_of_the_topic>
```

Если заголовок статьи не подходит для текста ссылки (например, слово или фраза в предложении является текстом ссылки), укажите ссылку xref и текст ссылки следующим образом:

```md
[link text](xref:uid_of_the_topic)
```

Дополнительные сведения см. в разделе [Перекрестные ссылки DocFX](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).

## <a name="images-and-screenshots"></a>Изображения и снимки экрана

Не включайте изображения в статьи, за исключением следующих случаев:

* если создаются базовые руководства по внедрению (для начинающих);
* если изображение необходимо для понимания.

Эти ограничения позволяют уменьшить размер репозитория.

В качестве дополнительного шага убедитесь, что все изображения и снимки экрана в документации сжаты, чтобы не увеличивать размер файла и время загрузки страницы. Среди популярных — средства TinyPNG ([веб-сайт TinyPNG](https://tinypng.com/) или [API TinyPNG](https://tinypng.com/developers)) или расширение Visual Studio [Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer). 

## <a name="code-snippets"></a>Фрагменты кода

Статьи часто содержат фрагменты кода для иллюстрации. DFM позволяет скопировать код в файле Markdown или ссылаться на отдельный файл кода. Рекомендуется использовать отдельные файлы кода, когда это возможно, чтобы свести к минимуму вероятность ошибок в коде. Файлы кода хранятся в репозитории в соответствии со структурой папок, описанной ранее для образцов проектов. 

В следующих примерах показан [синтаксис фрагмента кода DFM](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) для использования в файле *configuration/index.md*.

Для отображения всего файла кода в виде фрагмента:

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

Для подготовки к просмотру части файла как фрагмента с помощью номеров строк:

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

Для фрагментов C# сошлитесь на [область C#](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region). По возможности используйте области, а не номера строк, так как номера строк в файле кода, как правило, меняются и теряют синхронизацию с номерами строк в Markdown. Области C# могут быть вложенными. При ссылке на внешнюю область внутренние директивы `#region` и `#endregion` не отображаются во фрагменте кода. 

Для подготовки области C# с именем "snippet_Example":

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

Чтобы выделить выбранные строки в готовом для просмотра фрагменте кода (обычно выделяются желтым цветом):

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a>Тестирование изменений с помощью DocFX

Протестируйте изменения с помощью [средства командной строки DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), которое создает локально расположенную версию сайта. DocFX не отображает стиль и расширения сайта, созданные для сайта docs.microsoft.com.

DocFX требует:

* .NET Framework на Windows.
* Mono для Linux или macOS. 

### <a name="windows-instructions"></a>Инструкции для Windows

* Скачайте и распакуйте *docfx.zip* из [выпусков DocFX](https://github.com/dotnet/docfx/releases).
* Добавьте DocFX в PATH.
* В командной оболочке перейдите в папку, содержащую файл *docfx.json* (*aspnet* для содержимого ASP.NET или *aspnetcore* для содержимого ASP.NET Core) и выполните следующую команду:

  ```console
  docfx --serve
  ```

* В браузере перейдите на адрес `http://localhost:8080/group1-dest/`.

### <a name="mono-instructions"></a>Инструкции для Mono

* Установите Mono через Homebrew:

  ```console
  brew install mono
  ```

* Загрузите [последнюю версию DocFX](https://github.com/dotnet/docfx/releases).
* Извлеките архив *$HOME/bin/docfx*.
* Создайте пару псевдонимов для **docfx** в оболочке bash. Первый псевдоним используется для создания документации. Второй псевдоним используется для создания и обслуживания документации.

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* В командной оболочке перейдите в папку, содержащую файл *docfx.json* (*aspnet* для содержимого ASP.NET или *aspnetcore* для содержимого ASP.NET Core) и выполните следующую команду, чтобы создавать и обслуживать документы с использованием их псевдонимов.

  ```console
  docfx-serve
  ```

* В браузере перейдите на адрес `http://localhost:8080/group1-dest/`.

## <a name="voice-and-tone"></a>Стиль и тон

Наши документы должны быть понятными для самой широкой аудитории. С этой целью мы предлагаем рекомендации по стилю и просим наших авторов следовать им. Дополнительные сведения см. в разделе [Рекомендации по стилю](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) в репозитории .NET.

## <a name="microsoft-writing-style-guide"></a>Руководство по стилю Майкрософт

[Руководство по стилю Майкрософт](https://docs.microsoft.com/style-guide/welcome/) предоставляет рекомендации по стилю и терминологии для всех технических текстов, включая документацию по ASP.NET Core.

## <a name="redirects"></a>Перенаправления

Если вы удаляете статью, измените ее имя файла или переместите ее в другую папку, создайте перенаправление, чтобы пользователи, добавившие статью в закладки, не получали ошибку *404 Не найдено*. Добавьте перенаправление [в главный файл перенаправления](https://github.com/dotnet/AspNetCore.Docs/blob/master/.openpublishing.redirection.json).
