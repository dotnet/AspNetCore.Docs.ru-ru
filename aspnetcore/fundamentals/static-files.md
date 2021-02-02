---
title: Статические файлы в ASP.NET Core
author: rick-anderson
description: Узнайте, как в веб-приложениях ASP.NET Core обслуживать и защищать статические файлы, а также как настраивать ПО промежуточного слоя по размещению статических файлов.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: d97caeffc6e8beebddb01a5bd126d61ba988de65
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689296"
---
# <a name="static-files-in-aspnet-core"></a>Статические файлы в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)

Статические файлы, такие как HTML, CSS, изображения и JavaScript, являются ресурсами, которые приложения ASP.NET Core предоставляют клиентам напрямую по умолчанию.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Обслуживание статических файлов

Статические файлы хранятся в [корневом каталоге документов](xref:fundamentals/index#web-root) проекта. Каталог по умолчанию — `{content root}/wwwroot`, но его можно изменить с помощью метода <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A>. См. разделы [Корневой каталог содержимого](xref:fundamentals/index#content-root) и [Корневой веб-каталог](xref:fundamentals/index#web-root).

Метод <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> устанавливает текущий каталог в качестве корневого каталога содержимого:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

Предыдущий код создан с помощью шаблона веб-приложения.

Статические файлы доступны по относительному пути от [корневого каталога документов](xref:fundamentals/index#web-root). Например, шаблоны проекта **Web Application** содержат несколько папок в папке `wwwroot`:

* `wwwroot`
  * `css`
  * `js`
  * `lib`

Мы рекомендуем создать папку *wwwroot/images* и добавить в нее файл *wwwroot/images/MyImage.jpg*. Формат URI для доступа к файлу в папке `images`: `https://<hostname>/images/<image_file_name>`. Например: `https://localhost:5001/images/MyImage.jpg`

### <a name="serve-files-in-web-root"></a>Обслуживание файлов в корневом каталоге веб-сайта

Шаблоны веб-приложений по умолчанию вызывают метод <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> в `Startup.Configure`, который позволяет обслуживать статические файлы:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

Эта перегрузка метода `UseStaticFiles` не принимает параметров, она помечает файлы в [корневом каталоге документов](xref:fundamentals/index#web-root) как обслуживаемые. Следующая разметка ссылается на *wwwroot/images/MyImage.jpg*:

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

В приведенном выше коде знак тильды `~/` указывает на [корневой каталог документов](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Обслуживание файлов вне корневого веб-каталога

Пусть имеется иерархия каталогов, в которой статические файлы обслуживаются вне [корневого каталога документов](xref:fundamentals/index#web-root):

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

В запросе можно получить доступ к файлу `red-rose.jpg`, настроив ПО промежуточного слоя для статических файлов следующим образом:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

В приведенном выше коде доступ к иерархии каталога *MyStaticFiles* представляется через сегмент URI *StaticFiles*. Запрос к `https://<hostname>/StaticFiles/images/red-rose.jpg` обслуживает файл *red-rose.jpg*.

Следующая разметка ссылается на *MyStaticFiles/images/red-rose.jpg*:

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a>Установка заголовков HTTP-ответов

Для установки заголовков HTTP-ответов можно использовать объект <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>. Кроме настройки обслуживания статических файлов в [корневом каталоге документов](xref:fundamentals/index#web-root), следующий код также устанавливает заголовок `Cache-Control`:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

Статические файлы являются общедоступными для кэширования в течение 600 с:

![Добавлены заголовки ответов, включая заголовок Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Авторизация статических файлов

Шаблоны ASP.NET Core вызывают <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> перед вызовом <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>. Большинство приложений используют этот шаблон. При вызове ПО промежуточного слоя статического файла перед ПО промежуточного слоя авторизации:

  * для статических файлов не выполняются проверки авторизации;
  * статические файлы, обслуживаемые ПО промежуточного слоя статического файла, например те, которые находятся в `wwwroot`, являются общедоступными.
  
Для обслуживания статических файлов на основе авторизации:

  * сохраните их за пределами `wwwroot`;
  * вызовите `UseStaticFiles`, указав путь, после вызова `UseAuthorization`;
  * задайте [резервную политику авторизации](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2&highlight=24-29)]
  
  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-25)]

  В приведенном выше коде резервная политика авторизации требует проверки подлинности ***всех** _ пользователей. Конечные точки, такие как контроллеры, страницы Razor и т. д., которые определяют собственные требования к авторизации, не используют резервную политику авторизации. Например, Razor Pages, контроллеры или методы действий с `[AllowAnonymous]` или `[Authorize(PolicyName="MyPolicy")]` используют примененный атрибут авторизации вместо резервной политики авторизации.

  <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> добавляет <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> к текущему экземпляру, что обеспечивает проверку подлинности текущего пользователя.

  Статические ресурсы в `wwwroot` являются общедоступными, так как ПО промежуточного слоя для статического файла по умолчанию (`app.UseStaticFiles();`) вызывается перед `UseAuthentication`. Для статических ресурсов в папке _MyStaticFiles* требуется проверка подлинности. Это показано в следующем [примере кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples).

Альтернативный подход к обработке файлов на основе авторизации:

  * Сохраните файлы за пределами каталога `wwwroot` в любом каталоге, к которому имеет доступ ПО промежуточного слоя для статических файлов.
  * Обслуживайте их через метод действия, к которому применима авторизация, и получите объект <xref:Microsoft.AspNetCore.Mvc.FileResult>:

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a>Просмотр каталогов

Просмотр каталогов позволяет просматривать список каталогов в указанных каталогах.

По соображениям безопасности просмотр каталогов отключен по умолчанию. См. раздел [Безопасность статических файлов](#considerations).

Включите просмотр каталогов с помощью:

* <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> в `Startup.ConfigureServices`;
* <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> в `Startup.Configure`;

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

Приведенный выше код разрешает просмотр папки *wwwroot/images* с помощью URL-адреса `https://<hostname>/MyImages`, со ссылками на все файлы и папки:

![просмотр каталогов](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a>Обслуживание документов по умолчанию

Страница по умолчанию является для пользователей отправной точкой на веб-сайте. Чтобы обслуживать страницу по умолчанию из `wwwroot` без полного URI, вызовите метод <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A>:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

Для обслуживания файла по умолчанию метод `UseDefaultFiles`должен быть вызван до метода `UseStaticFiles`. `UseDefaultFiles` — это средство переопределения URL-адресов, которое не обслуживает файл.

При использовании `UseDefaultFiles` запросы к папке в `wwwroot` будут искать следующие файлы:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Первый найденный файл из списка будет обслужен, как будто был введен полный URI. URL-адрес в браузере будет соответствовать запрошенному URI.

Следующий код позволяет изменить имя файла по умолчанию на *mydefault.html*:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a>Использование UseFileServer для документов по умолчанию

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> объединяет функции `UseStaticFiles`, `UseDefaultFiles` и при необходимости `UseDirectoryBrowser`.

Вызовите `app.UseFileServer`, чтобы включить обслуживание статических файлов и файла по умолчанию. Просмотр каталогов отключен. В следующем примере кода демонстрируется `Startup.Configure` с `UseFileServer`:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

Следующий пример кода позволяет обслуживать статические файлы, файл по умолчанию и просмотр каталогов:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

Пусть имеется следующая иерархия каталогов:

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

Следующий пример кода позволяет обслуживать статические файлы, файл по умолчанию и просмотр каталогов `MyStaticFiles`:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> должно вызываться, когда свойство `EnableDirectoryBrowsing` имеет значение `true`.

При указанных выше коде и иерархии файлов URL-адреса будут разрешаться следующим образом:

| URI            |      Ответ  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | *MyStaticFiles/images/MyImage.jpg* |
| `https://<hostname>/StaticFiles` | *MyStaticFiles/default.html* |

Если в каталоге *MyStaticFiles* отсутствует файл с именем по умолчанию, `https://<hostname>/StaticFiles` возвращает список содержимого каталога с доступными для перехода ссылками:

![Список статических файлов](static-files/_static/db2.png)

<xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> и <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> выполняют перенаправление на стороне клиента из целевого URI без `/` в конце в целевой URI с `/` в конце. Например, из `https://<hostname>/StaticFiles` в `https://<hostname>/StaticFiles/`. Относительные URL-адреса в каталоге *StaticFiles* считаются недопустимыми без косой черты в конце (`/`).

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

Класс <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> содержит свойство `Mappings`, которое используется для сопоставления расширений файлов и типов содержимого MIME. В следующем примере несколько расширений файлов сопоставляются с известными типами MIME. Расширение *.rtf* заменяется, а *.mp4* удаляется:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

См. раздел [Типы содержимого MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Нестандартные типы содержимого

ПО промежуточного слоя для статических файлов распознает почти 400 известных типов содержимого файлов. Если пользователь запрашивает файл неизвестного типа, ПО промежуточного слоя статических файлов передает запрос следующему компоненту ПО промежуточного слоя в конвейере. Если ПО промежуточного слоя не удается обработать запрос, возвращается ответ *404 Не найдено*. Если просмотр каталогов разрешен, то в списке каталогов отображается ссылка на файл.

Следующий код включает обслуживание неизвестных типов и обслуживает неизвестные файлы как изображения:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

При выполнении вышеописанного кода ответ на запрос файла с неизвестным типом содержимого вернется в виде изображения.

> [!WARNING]
> При включении <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> возникает угроза безопасности. По умолчанию он отключен, и его использование не рекомендуется. Использование класса [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) является более безопасной альтернативой для обслуживания файлов с нестандартными расширениями.

## <a name="serve-files-from-multiple-locations"></a>Предоставление файлов из нескольких расположений

В качестве значений `UseStaticFiles` и `UseFileServer` по умолчанию используется поставщик файлов, указывающий на `wwwroot`. Вы можете предоставить дополнительные экземпляры `UseStaticFiles` и `UseFileServer` с другими поставщиками файлов для обслуживания файлов из других расположений. Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a>Безопасность статических файлов

> [!WARNING]
> Использование `UseDirectoryBrowser` и `UseStaticFiles` может привести к утечке конфиденциальной информации. Настоятельно рекомендуется отключать просмотр каталогов в рабочей среде. Тщательно проверьте, просмотр каких каталогов разрешен посредством `UseStaticFiles` или `UseDirectoryBrowser`. Весь каталог и его подкаталоги становятся общедоступными. Храните файлы, предназначенные для общего доступа, в выделенных каталогах, таких как `<content_root>/wwwroot`. Отделите эти файлы от представлений MVC, Razor Pages, файлов конфигурации и т. д.

* К URL-адресам содержимого, к которому предоставлен доступ методами `UseDirectoryBrowser` и `UseStaticFiles`, применяются те же требования по регистрозависимости и запрещенным символам, что и к базовой файловой системе. Например, в Windows регистр не учитывается, а в macOS и Linux учитывается.

* Приложения ASP.NET Core, размещенные в IIS, используют [Модуль Core ASP.NET](xref:host-and-deploy/aspnet-core-module) для перенаправления всех запросов к приложению, включая запросы статических файлов. Обработчик статических файлов IIS не используется и не выполняет обработку запросов.

* Выполните следующие шаги в диспетчере служб IIS для удаления обработчика статических файлов IIS на уровне сервера или веб-сайта:
    1. Перейдите к компоненту **Модули**.
    1. Выберите в списке модуль **StaticFileModule**.
    1. Нажмите кнопку **Удалить** в боковой панели **Действия**.

> [!WARNING]
> Если обработчик статических файлов IIS включен **и** модуль ASP.NET Core настроен неправильно, то статические файлы будут обслуживаться. Это может случиться, если, например, не был развернут файл *web.config*.

* Размещайте файлы с кодом, включая файлы *.cs* и *.cshtml*, за пределами [корневого веб-каталога](xref:fundamentals/index#web-root) проекта приложения. Таким образом, в приложении создается логическое разделение между клиентским содержимым и серверным кодом. Это предотвращает утечку серверного кода.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [ПО промежуточного слоя](xref:fundamentals/middleware/index)
* [Введение в ASP.NET Core](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)

Статические файлы, такие как HTML, CSS, изображения и JavaScript, являются ресурсами, которые приложения ASP.NET Core предоставляют клиентам напрямую. Для обслуживания таких файлов требуется настроить некоторые параметры.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Обслуживание статических файлов

Статические файлы хранятся в [корневом каталоге документов](xref:fundamentals/index#web-root) проекта. Каталог по умолчанию — *{корневой_каталог_содержимого}/wwwroot*, но его можно изменить с помощью метода <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A>. Дополнительные сведения см. в разделах [Корневой каталог содержимого](xref:fundamentals/index#content-root) и [Корневой веб-каталог](xref:fundamentals/index#web-root).

Веб-узел приложения должен знать о расположении корневого каталога содержимого.

Метод `WebHost.CreateDefaultBuilder` устанавливает текущий каталог в качестве корневого каталога содержимого:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

Статические файлы доступны по относительному пути от [корневого каталога документов](xref:fundamentals/index#web-root). Например, шаблон проекта **Web Application** содержит несколько папок в папке `wwwroot`:

* `wwwroot`
  * `css`
  * `images`
  * `js`

Формат URI для доступа к файлу во вложенной папке *images*: *http://\<server_address>/images/\<image_file_name>* . Например, *http://localhost:9189/images/banner3.svg* .

Если код предназначен для .NET Framework, добавьте в проект пакет [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/). Если код предназначен для .NET Core, то этот пакет уже включен в метапакет [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Настройте [ПО промежуточного слоя](xref:fundamentals/middleware/index), позволяющее обслуживать статические файлы.

### <a name="serve-files-inside-of-web-root"></a>Обслуживание файлов в корневом веб-каталоге

Вызовите метод <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> в `Startup.Configure`:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

Эта перегрузка метода `UseStaticFiles` не принимает параметров, она помечает файлы в [корневом каталоге документов](xref:fundamentals/index#web-root) как обслуживаемые. Следующая разметка ссылается на *wwwroot/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

В приведенном выше коде знак тильды `~/` указывает на [корневой каталог документов](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Обслуживание файлов вне корневого веб-каталога

Пусть имеется иерархия каталогов, в которой статические файлы обслуживаются вне [корневого каталога документов](xref:fundamentals/index#web-root):

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

В запросе можно получить доступ к файлу *banner1.svg*, настроив ПО промежуточного слоя для статических файлов следующим образом:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

В приведенном выше коде доступ к иерархии каталога *MyStaticFiles* представляется через сегмент URI *StaticFiles*. Запрос *http://\<server_address>/StaticFiles/images/banner1.svg* обслуживает файл *banner1.svg*.

Следующая разметка ссылается на *MyStaticFiles/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Установка заголовков HTTP-ответов

Для установки заголовков HTTP-ответов можно использовать объект <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>. Кроме настройки обслуживания статических файлов в [корневом каталоге документов](xref:fundamentals/index#web-root), следующий код также устанавливает заголовок `Cache-Control`:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Метод <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> существует в пакете [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).

Файлы, к которым был предоставлен доступ, кэшируются в течение 10 минут (600 секунд) в среде разработки:

![Добавлены заголовки ответов, включая заголовок Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Авторизация статических файлов

ПО промежуточного слоя для статических файлов не предоставляет возможности авторизации. Все обслуживаемые им файлы, включая расположенные в *wwwroot*, находятся в открытом доступе. Для обслуживания файлов с авторизацией:

* Сохраните файлы в любом каталоге за пределами каталога *wwwroot*, к которому имеет доступ ПО промежуточного слоя для статических файлов.
* Обслуживайте их через метод действия, к которому применима авторизация. Получите объект <xref:Microsoft.AspNetCore.Mvc.FileResult>:

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Просмотр каталогов

Просмотр каталогов позволяет пользователям веб-приложениям просматривать файлы и каталоги внутри определенного каталога. По соображениям безопасности просмотр каталогов отключен по умолчанию (см. раздел [Особенности](#considerations)). Включите просмотр каталогов, вызвав метод <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> в `Startup.Configure`:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Добавьте необходимые службы путем вызова метода <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> из `Startup.ConfigureServices`:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Приведенный выше код разрешает просмотр папки *wwwroot/images* с помощью URL-адреса *http://\<server_address>/MyImages*, со ссылками на все файлы и папки:

![просмотр каталогов](static-files/_static/dir-browse.png)

Информацию об угрозе безопасности при включении просмотра каталогов см. в разделе [Особенности](#considerations).

Обратите внимание на два вызова метода `UseStaticFiles` в следующем примере. Первый вызов включает обслуживание статических файлов в папке *wwwroot*. Второй вызов включает просмотр папки *wwwroot/images* с помощью URL-адреса *http://\<server_address>/MyImages*:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Обслуживание документа по умолчанию

Домашняя страница по умолчанию является для пользователей логической отправной точкой при посещении веб-сайта. Для обслуживания страницы по умолчанию без указания полного имени URI вызовите метод <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> из `Startup.Configure`:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> Для обслуживания файла по умолчанию метод `UseDefaultFiles`должен быть вызван до метода `UseStaticFiles`. Метод `UseDefaultFiles` фактически не обслуживает файл, а только перезаписывает URL-адрес. Для обслуживания файла включите ПО промежуточного слоя для статических файлов, вызвав метод `UseStaticFiles`.

При вызове метода `UseDefaultFiles` запросы к папке будут искать следующие файлы:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Первый найденный файл из списка будет обслужен, как будто был введен полный URI. URL-адрес в браузере будет соответствовать запрошенному URI.

Следующий код позволяет изменить имя файла по умолчанию на *mydefault.html*:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> объединяет функции `UseStaticFiles`, `UseDefaultFiles` и при необходимости `UseDirectoryBrowser`.

Следующий пример кода позволяет обслуживать статические файлы и файл по умолчанию. Просмотр каталогов отключен.

```csharp
app.UseFileServer();
```

Следующий код отличается от предыдущей перегрузки метода без параметров включением просмотра каталогов:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Пусть имеется следующая иерархия каталогов:

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*
  * *default.html*

Следующий пример кода включает обслуживание статических файлов, файлы по умолчанию и просмотр каталога `MyStaticFiles`:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

Метод `AddDirectoryBrowser` должен вызываться при значении `true` свойства `EnableDirectoryBrowsing`:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

При указанных выше коде и иерархии файлов URL-адреса будут разрешаться следующим образом:

| URI            |                             Ответ  |
| ------- | ------|
| *http://\<server_address>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<server_address>/StaticFiles*             |     MyStaticFiles/default.html |

Если в каталоге *MyStaticFiles* отсутствует файл с именем по умолчанию, *http://\<server_address>/StaticFiles* возвращает список содержимого каталога с доступными для перехода ссылками:

![Список статических файлов](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> и <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> выполняют перенаправление на стороне клиента из `http://{SERVER ADDRESS}/StaticFiles` (без косой черты в конце) в `http://{SERVER ADDRESS}/StaticFiles/` (с косой чертой в конце). Относительные URL-адреса в каталоге *StaticFiles* считаются недопустимыми без косой черты в конце.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

Класс <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> содержит свойство `Mappings`, которое используется для сопоставления расширений файлов и типов содержимого MIME. В следующем примере несколько расширений файлов регистрируются в известные типы MIME. Расширение *.rtf* заменяется, а *.mp4* удаляется.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

См. раздел [Типы содержимого MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

Сведения об использовании настраиваемых <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> и настройке других <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> в приложениях Blazor Server см. в разделе <xref:blazor/fundamentals/additional-scenarios#static-files>.

## <a name="non-standard-content-types"></a>Нестандартные типы содержимого

ПО промежуточного слоя для статических файлов воспринимает почти 400 известных типов содержимого файлов. Если пользователь запрашивает файл неизвестного типа, ПО промежуточного слоя статических файлов передает запрос следующему компоненту ПО промежуточного слоя в конвейере. Если ПО промежуточного слоя не удается обработать запрос, возвращается ответ *404 Не найдено*. Если просмотр каталогов разрешен, то в списке каталогов отображается ссылка на файл.

Следующий код включает обслуживание неизвестных типов и обслуживает неизвестные файлы как изображения:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

При выполнении вышеописанного кода ответ на запрос файла с неизвестным типом содержимого вернется в виде изображения.

> [!WARNING]
> При включении <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> возникает угроза безопасности. По умолчанию он отключен, и его использование не рекомендуется. Использование класса [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) является более безопасной альтернативой для обслуживания файлов с нестандартными расширениями.

## <a name="serve-files-from-multiple-locations"></a>Предоставление файлов из нескольких расположений

В качестве значений `UseStaticFiles` и `UseFileServer` по умолчанию используется поставщик файлов, указывающий на *wwwroot*. Вы можете предоставить дополнительные экземпляры `UseStaticFiles` и `UseFileServer` с другими поставщиками файлов для предоставления файлов из других расположений. Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

### <a name="considerations"></a>Особенности

> [!WARNING]
> Использование `UseDirectoryBrowser` и `UseStaticFiles` может привести к утечке конфиденциальной информации. Настоятельно рекомендуется отключать просмотр каталогов в рабочей среде. Тщательно проверьте, просмотр каких каталогов разрешен посредством `UseStaticFiles` или `UseDirectoryBrowser`. Весь каталог и его подкаталоги становятся общедоступными. Храните файлы, предназначенные для общего доступа, в выделенных каталогах, таких как *\<content_root>/wwwroot*. Отделите эти файлы от представлений MVC, страниц Razor Pages (только для версии 2.x), файлов конфигурации и т. д.

* К URL-адресам содержимого, к которому предоставлен доступ методами `UseDirectoryBrowser` и `UseStaticFiles`, применяются те же требования по регистрозависимости и запрещенным символам, что и к базовой файловой системе. Например, в Windows не учитывается регистр, а в macOS и Linux &mdash; учитывается.

* Приложения ASP.NET Core, размещенные в IIS, используют [Модуль Core ASP.NET](xref:host-and-deploy/aspnet-core-module) для перенаправления всех запросов к приложению, включая запросы статических файлов. Обработчик статических файлов IIS не используется. Не существует возможности обработать запросы до того, как их обработает модуль.

* Выполните следующие шаги в диспетчере служб IIS для удаления обработчика статических файлов IIS на уровне сервера или веб-сайта:
    1. Перейдите к компоненту **Модули**.
    1. Выберите в списке модуль **StaticFileModule**.
    1. Нажмите кнопку **Удалить** в боковой панели **Действия**.

> [!WARNING]
> Если обработчик статических файлов IIS включен **и** модуль ASP.NET Core настроен неправильно, то статические файлы будут обслуживаться. Это может случиться, если, например, не был развернут файл *web.config*.

* Размещайте файлы с кодом (включая *CS* и *CSHTML*) за пределами [корневого каталога документов](xref:fundamentals/index#web-root) проекта приложения. Таким образом, в приложении создается логическое разделение между клиентским содержимым и серверным кодом. Это предотвращает утечку серверного кода.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [ПО промежуточного слоя](xref:fundamentals/middleware/index)
* [Введение в ASP.NET Core](xref:index)

::: moniker-end
