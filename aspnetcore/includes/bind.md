<span data-ttu-id="8aa1f-101">Предпочтительный способ чтения связанных значений конфигурации — использование [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="8aa1f-101">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="8aa1f-102">Например, чтобы считать следующие значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="8aa1f-102">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="8aa1f-103">Создайте следующий класс `PositionOptions`:</span><span class="sxs-lookup"><span data-stu-id="8aa1f-103">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="8aa1f-104">Класс параметров:</span><span class="sxs-lookup"><span data-stu-id="8aa1f-104">An options class:</span></span>

* <span data-ttu-id="8aa1f-105">Должен быть неабстрактным с открытым конструктором без параметров.</span><span class="sxs-lookup"><span data-stu-id="8aa1f-105">Must be non-abstract with a public parameterless constructor.</span></span>
* <span data-ttu-id="8aa1f-106">Все открытые свойства чтения и записи типа привязаны.</span><span class="sxs-lookup"><span data-stu-id="8aa1f-106">All public read-write properties of the type are bound.</span></span>
* <span data-ttu-id="8aa1f-107">Поля **не** привязаны.</span><span class="sxs-lookup"><span data-stu-id="8aa1f-107">Fields are \***not** _ bound.</span></span> <span data-ttu-id="8aa1f-108">В приведенном выше коде свойство `Position` не привязано.</span><span class="sxs-lookup"><span data-stu-id="8aa1f-108">In the preceding code, `Position` is not bound.</span></span> <span data-ttu-id="8aa1f-109">Свойство `Position` используется так, что строку `"Position"` не требуется жестко кодировать в приложении при привязке класса к поставщику конфигурации.</span><span class="sxs-lookup"><span data-stu-id="8aa1f-109">The `Position` property is used so the string `"Position"` doesn't need to be hard coded in the app when binding the class to a configuration provider.</span></span>

<span data-ttu-id="8aa1f-110">В приведенном ниже коде</span><span class="sxs-lookup"><span data-stu-id="8aa1f-110">The following code:</span></span>

<span data-ttu-id="8aa1f-111">_ Вызывает [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) для привязки класса `PositionOptions` к разделу `Position`.</span><span class="sxs-lookup"><span data-stu-id="8aa1f-111">_ Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="8aa1f-112">Отображает данные конфигурации `Position`.</span><span class="sxs-lookup"><span data-stu-id="8aa1f-112">Displays the `Position` configuration data.</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="8aa1f-113">По умолчанию в приведенном выше коде изменения в файле конфигурации JSON, внесенные после запуска приложения, считываются.</span><span class="sxs-lookup"><span data-stu-id="8aa1f-113">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="8aa1f-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) привязывает и возвращает указанный тип.</span><span class="sxs-lookup"><span data-stu-id="8aa1f-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="8aa1f-115">Метод `ConfigurationBinder.Get<T>` может быть более удобным, чем `ConfigurationBinder.Bind`.</span><span class="sxs-lookup"><span data-stu-id="8aa1f-115">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="8aa1f-116">В приведенном ниже примере кода демонстрируются способы использования `ConfigurationBinder.Get<T>` с классом `PositionOptions`:</span><span class="sxs-lookup"><span data-stu-id="8aa1f-116">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="8aa1f-117">По умолчанию в приведенном выше коде изменения в файле конфигурации JSON, внесенные после запуска приложения, считываются.</span><span class="sxs-lookup"><span data-stu-id="8aa1f-117">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="8aa1f-118">Альтернативный подход при использовании \***шаблона параметров** _ — привязка раздела `Position` и добавление его в [контейнер службы внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8aa1f-118">An alternative approach when using the \***options pattern** _ is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8aa1f-119">В следующем коде `PositionOptions` добавляется в контейнер службы с помощью интерфейса <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> и привязывается к конфигурации:</span><span class="sxs-lookup"><span data-stu-id="8aa1f-119">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="8aa1f-120">С помощью приведенного выше кода следующий код считывает параметры расположения:</span><span class="sxs-lookup"><span data-stu-id="8aa1f-120">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="8aa1f-121">В приведенном выше коде изменения в файле конфигурации JSON, внесенные после запуска приложения, ***не*** считываются.</span><span class="sxs-lookup"><span data-stu-id="8aa1f-121">In the preceding code, changes to the JSON configuration file after the app has started are ***not*** read.</span></span> <span data-ttu-id="8aa1f-122">Чтобы считать изменения после запуска приложения, используйте [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span><span class="sxs-lookup"><span data-stu-id="8aa1f-122">To read changes after the app has started, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span></span>
