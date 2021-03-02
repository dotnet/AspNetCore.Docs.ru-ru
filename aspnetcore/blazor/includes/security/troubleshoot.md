---
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
ms.openlocfilehash: 4e7c0e9b0a164e0181af5d6baaedf0669c1c06aa
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551585"
---
## <a name="troubleshoot"></a><span data-ttu-id="81ada-101">Диагностика</span><span class="sxs-lookup"><span data-stu-id="81ada-101">Troubleshoot</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="common-errors"></a><span data-ttu-id="81ada-102">Распространенные ошибки</span><span class="sxs-lookup"><span data-stu-id="81ada-102">Common errors</span></span>

* <span data-ttu-id="81ada-103">Неавторизованный клиент для AAD</span><span class="sxs-lookup"><span data-stu-id="81ada-103">Unauthorized client for AAD</span></span>

  > <span data-ttu-id="81ada-104">Сведения: Не удалось выполнить авторизацию Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2].</span><span class="sxs-lookup"><span data-stu-id="81ada-104">info: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization failed.</span></span> <span data-ttu-id="81ada-105">Не выполнены такие требования: DenyAnonymousAuthorizationRequirement. Требуется пользователь, прошедший проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="81ada-105">These requirements were not met: DenyAnonymousAuthorizationRequirement: Requires an authenticated user.</span></span>

  <span data-ttu-id="81ada-106">Ошибка обратного вызова входа от AAD:</span><span class="sxs-lookup"><span data-stu-id="81ada-106">Login callback error from AAD:</span></span>

  * <span data-ttu-id="81ada-107">Ошибка `unauthorized_client` (У вызывающей стороны нет прав на запись для ресурса: [subscriptions/])</span><span class="sxs-lookup"><span data-stu-id="81ada-107">Error: `unauthorized_client`</span></span>
  * <span data-ttu-id="81ada-108">Описание: `AADB2C90058: The provided application is not configured to allow public clients.`</span><span class="sxs-lookup"><span data-stu-id="81ada-108">Description: `AADB2C90058: The provided application is not configured to allow public clients.`</span></span>

  <span data-ttu-id="81ada-109">Чтобы устранить эту ошибку, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="81ada-109">To resolve the error:</span></span>

  1. <span data-ttu-id="81ada-110">На портале Azure перейдите к [манифесту приложения](/azure/active-directory/develop/reference-app-manifest).</span><span class="sxs-lookup"><span data-stu-id="81ada-110">In the Azure portal, access the [app's manifest](/azure/active-directory/develop/reference-app-manifest).</span></span>
  1. <span data-ttu-id="81ada-111">Задайте для атрибута [ `allowPublicClient` ](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) значение `null` или `true`.</span><span class="sxs-lookup"><span data-stu-id="81ada-111">Set the [`allowPublicClient` attribute](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) to `null` or `true`.</span></span>

::: moniker-end

### <a name="cookies-and-site-data"></a><span data-ttu-id="81ada-112">Файлы Cookie и данные сайта</span><span class="sxs-lookup"><span data-stu-id="81ada-112">Cookies and site data</span></span>

<span data-ttu-id="81ada-113">Файлы Cookie и данные сайта могут сохраняться при обновлении приложения и влиять на тестирование и устранение неполадок.</span><span class="sxs-lookup"><span data-stu-id="81ada-113">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="81ada-114">При внесении изменений в код приложения, изменений в учетную запись пользователя у поставщика или изменений конфигурации приложения поставщика очистите следующее:</span><span class="sxs-lookup"><span data-stu-id="81ada-114">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="81ada-115">файлы cookie входа пользователя;</span><span class="sxs-lookup"><span data-stu-id="81ada-115">User sign-in cookies</span></span>
* <span data-ttu-id="81ada-116">файлы cookie приложения;</span><span class="sxs-lookup"><span data-stu-id="81ada-116">App cookies</span></span>
* <span data-ttu-id="81ada-117">кэшированные и сохраненные данные сайта.</span><span class="sxs-lookup"><span data-stu-id="81ada-117">Cached and stored site data</span></span>

<span data-ttu-id="81ada-118">Один из подходов, позволяющих предотвратить влияние устаревших файлов cookie и данных сайта на тестирование и устранение неполадок, заключается в следующем.</span><span class="sxs-lookup"><span data-stu-id="81ada-118">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="81ada-119">Настройте браузер</span><span class="sxs-lookup"><span data-stu-id="81ada-119">Configure a browser</span></span>
  * <span data-ttu-id="81ada-120">Для тестирования используйте браузер, в котором можно настроить удаление всех файлов cookie и данных сайта при каждом закрытии браузера.</span><span class="sxs-lookup"><span data-stu-id="81ada-120">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="81ada-121">Убедитесь, что при любых изменениях в приложении, в данных тестового пользователя или в конфигурации поставщика закрытие браузера выполняется вручную или интегрированной средой разработки.</span><span class="sxs-lookup"><span data-stu-id="81ada-121">Make sure that the browser is closed manually or by the IDE for any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="81ada-122">Используйте настраиваемую команду, чтобы открыть браузер в режиме инкогнито или частном режиме в Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="81ada-122">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="81ada-123">Откройте диалоговое окно **Просмотр с помощью**, которое можно выбрать с помощью кнопки **Запустить** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="81ada-123">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="81ada-124">Нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="81ada-124">Select the **Add** button.</span></span>
  * <span data-ttu-id="81ada-125">Укажите путь к браузеру в поле **Программа**.</span><span class="sxs-lookup"><span data-stu-id="81ada-125">Provide the path to your browser in the **Program** field.</span></span> <span data-ttu-id="81ada-126">Следующие пути к исполняемым файлам являются типичными расположениями установки для Windows 10.</span><span class="sxs-lookup"><span data-stu-id="81ada-126">The following executable paths are typical installation locations for Windows 10.</span></span> <span data-ttu-id="81ada-127">Если браузер установлен в другом расположении или вы используете операционную систему, отличную от Windows 10, укажите путь к исполняемому файлу браузера.</span><span class="sxs-lookup"><span data-stu-id="81ada-127">If your browser is installed in a different location or you aren't using Windows 10, provide the path to the browser's executable.</span></span>
    * <span data-ttu-id="81ada-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span><span class="sxs-lookup"><span data-stu-id="81ada-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span></span>
    * <span data-ttu-id="81ada-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span><span class="sxs-lookup"><span data-stu-id="81ada-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span></span>
    * <span data-ttu-id="81ada-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span><span class="sxs-lookup"><span data-stu-id="81ada-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span></span>
  * <span data-ttu-id="81ada-131">В поле **Аргументы** укажите параметр командной строки, используемый браузером для открытия в режиме инкогнито или частном режиме.</span><span class="sxs-lookup"><span data-stu-id="81ada-131">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode.</span></span> <span data-ttu-id="81ada-132">Для некоторых браузеров требуется URL-адрес приложения.</span><span class="sxs-lookup"><span data-stu-id="81ada-132">Some browsers require the URL of the app.</span></span>
    * <span data-ttu-id="81ada-133">Microsoft Edge: Используйте ключевое слово `-inprivate`.</span><span class="sxs-lookup"><span data-stu-id="81ada-133">Microsoft Edge: Use `-inprivate`.</span></span>
    * <span data-ttu-id="81ada-134">Google Chrome: используйте `--incognito --new-window {URL}`, где заполнитель `{URL}` — это открываемый URL-адрес (например, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="81ada-134">Google Chrome: Use `--incognito --new-window {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
    * <span data-ttu-id="81ada-135">Mozilla Firefox: используйте `-private -url {URL}`, где заполнитель `{URL}` — это открываемый URL-адрес (например, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="81ada-135">Mozilla Firefox: Use `-private -url {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
  * <span data-ttu-id="81ada-136">Введите имя в поле **Понятное имя**.</span><span class="sxs-lookup"><span data-stu-id="81ada-136">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="81ada-137">Например, `Firefox Auth Testing`.</span><span class="sxs-lookup"><span data-stu-id="81ada-137">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="81ada-138">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="81ada-138">Select the **OK** button.</span></span>
  * <span data-ttu-id="81ada-139">Чтобы не выбирать профиль браузера для каждой операции тестирования с помощью приложения, задайте профиль по умолчанию с помощью кнопки **По умолчанию**.</span><span class="sxs-lookup"><span data-stu-id="81ada-139">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="81ada-140">Убедитесь, что при любых изменениях в приложении, в данных тестового пользователя или в конфигурации поставщика закрытие браузера выполняется интегрированной средой разработки.</span><span class="sxs-lookup"><span data-stu-id="81ada-140">Make sure that the browser is closed by the IDE for any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="81ada-141">Запуск серверного приложения</span><span class="sxs-lookup"><span data-stu-id="81ada-141">Run the Server app</span></span>

<span data-ttu-id="81ada-142">При тестировании и устранении неполадок в размещенном приложении Blazor убедитесь, что вы используете приложение из проекта **`Server`** .</span><span class="sxs-lookup"><span data-stu-id="81ada-142">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **`Server`** project.</span></span> <span data-ttu-id="81ada-143">Например, в Visual Studio убедитесь, что серверный проект выделен в **обозревателе решений** перед запуском приложения с помощью любого из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="81ada-143">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="81ada-144">Нажмите кнопку **Запустить**.</span><span class="sxs-lookup"><span data-stu-id="81ada-144">Select the **Run** button.</span></span>
* <span data-ttu-id="81ada-145">В меню выберите **Отладка** > **Начать отладку**.</span><span class="sxs-lookup"><span data-stu-id="81ada-145">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="81ada-146">Нажмите клавишу <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="81ada-146">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="81ada-147">Проверка содержимого JSON Web Token (JWT)</span><span class="sxs-lookup"><span data-stu-id="81ada-147">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="81ada-148">Чтобы декодировать JSON Web Token (JWT), используйте средство [jwt.ms](https://jwt.ms/) (Майкрософт).</span><span class="sxs-lookup"><span data-stu-id="81ada-148">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="81ada-149">Значения в пользовательском интерфейсе остаются в браузере.</span><span class="sxs-lookup"><span data-stu-id="81ada-149">Values in the UI never leave your browser.</span></span>
