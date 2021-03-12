---
title: Непрерывное развертывание ASP.NET Core в Azure с помощью Visual Studio и Git
author: rick-anderson
description: Сведения о создании веб-приложения ASP.NET Core с помощью Visual Studio и его развертывании в службе приложений Azure с использованием Git для непрерывного развертывания.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 12/06/2018
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
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 561f41dd9d50df0651c060fac4f75d9d59244029
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605650"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="21522-103">Непрерывное развертывание ASP.NET Core в Azure с помощью Visual Studio и Git</span><span class="sxs-lookup"><span data-stu-id="21522-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="21522-104">Автор: [Эрик Рейтан](https://github.com/Erikre) (Erik Reitan)</span><span class="sxs-lookup"><span data-stu-id="21522-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="21522-105">В этом руководстве показано, как создать веб-приложение ASP.NET Core с помощью Visual Studio и развернуть его из Visual Studio в службе приложений Azure, используя непрерывное развертывание.</span><span class="sxs-lookup"><span data-stu-id="21522-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="21522-106">См. также статью [Create your first pipeline](/azure/devops/pipelines/get-started-yaml) (Создание первого конвейера), в которой объяснятеся, как настроить рабочий процесс непрерывной поставки (CD) для [Службы приложений Azure](/azure/app-service/app-service-web-overview) с помощью служб Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="21522-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="21522-107">Azure Pipelines (служба, входящая в пакет Azure DevOps Services) упрощает настройку надежного конвейера развертывания с целью публикации обновлений для приложений, размещенных в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="21522-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="21522-108">Этот конвейер можно настроить на портале Azure для сборки, выполнения тестов, развертывания в промежуточном слоте и последующего развертывания в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="21522-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="21522-109">Для работы с этим руководством требуется учетная запись Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="21522-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="21522-110">Чтобы получить учетную запись, [активируйте преимущества подписчика MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) или [зарегистрируйтесь для получения бесплатной версии](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).</span><span class="sxs-lookup"><span data-stu-id="21522-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="21522-111">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="21522-111">Prerequisites</span></span>

<span data-ttu-id="21522-112">В этом руководстве предполагается, что установлено следующее программное обеспечение:</span><span class="sxs-lookup"><span data-stu-id="21522-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="21522-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21522-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="21522-114">[Git](https://git-scm.com/downloads) для Windows.</span><span class="sxs-lookup"><span data-stu-id="21522-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="21522-115">Создание веб-приложения ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21522-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="21522-116">Запустите Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="21522-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="21522-117">В меню **Файл** выберите **Создать**  > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="21522-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="21522-118">Выберите шаблон проекта **Веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="21522-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="21522-119">Он находится в разделе **Installed** (Установлено) > **Templates** (Шаблоны) > **Visual C#**  >  **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="21522-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="21522-120">Задайте для проекта имя `SampleWebAppDemo`.</span><span class="sxs-lookup"><span data-stu-id="21522-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="21522-121">Выберите параметр **Создать репозиторий Git** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="21522-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![Диалоговое окно создания нового проекта](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="21522-123">В диалоговом окне **New ASP.NET Core Project** (Новый проект ASP.NET Core) выберите **пустой** шаблон и щелкните **ОК**.</span><span class="sxs-lookup"><span data-stu-id="21522-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![Диалоговое окно "Создание проекта ASP.NET Core"](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="21522-125">Последним выпуском .NET Core является версия 2.0.</span><span class="sxs-lookup"><span data-stu-id="21522-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="21522-126">Запуск веб-приложения в локальной системе</span><span class="sxs-lookup"><span data-stu-id="21522-126">Running the web app locally</span></span>

1. <span data-ttu-id="21522-127">После того как Visual Studio завершит создание приложения, запустите его, выбрав **Отладка** > **Начать отладку**.</span><span class="sxs-lookup"><span data-stu-id="21522-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="21522-128">Вы также можете нажать клавишу **F5**.</span><span class="sxs-lookup"><span data-stu-id="21522-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="21522-129">Инициализация Visual Studio и нового приложения может занять некоторое время.</span><span class="sxs-lookup"><span data-stu-id="21522-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="21522-130">По завершении запущенное приложение появится в браузере.</span><span class="sxs-lookup"><span data-stu-id="21522-130">Once it's complete, the browser shows the running app.</span></span>

   ![Окно браузера с выполняющимся приложением, в котором выводится сообщение "Hello World!"](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="21522-132">Посмотрев, как работает веб-приложение, закройте браузер и щелкните значок "Остановить отладку" на панели инструментов Visual Studio, чтобы остановить работу приложения.</span><span class="sxs-lookup"><span data-stu-id="21522-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="21522-133">Создание веб-приложения на портале Azure</span><span class="sxs-lookup"><span data-stu-id="21522-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="21522-134">Ниже приведены инструкции по созданию веб-приложения на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="21522-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="21522-135">Войдите на [портал Azure](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="21522-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="21522-136">В левом верхнем углу интерфейса портала нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="21522-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="21522-137">Выберите **Интернет и мобильные устройства** > **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="21522-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Портал Microsoft Azure: Новая кнопка: Интернет и мобильные устройства в Marketplace: Кнопка "Веб-приложение" в разделе "Рекомендуемые приложения"](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="21522-139">В колонке **Веб-приложение** в поле **Имя службы приложений** введите уникальное значение.</span><span class="sxs-lookup"><span data-stu-id="21522-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![Колонка "Веб-приложение"](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="21522-141">**Имя службы приложений** должно быть уникальным.</span><span class="sxs-lookup"><span data-stu-id="21522-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="21522-142">Портал применяет это правило, если имя указано.</span><span class="sxs-lookup"><span data-stu-id="21522-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="21522-143">Если вы введете другое значение, подставьте его вместо каждого экземпляра имени **SampleWebAppDemo** в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="21522-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="21522-144">Кроме того, в колонке **Веб-приложение** выберите существующее **Расположение или план службы приложений** либо создайте собственный план или расположение.</span><span class="sxs-lookup"><span data-stu-id="21522-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="21522-145">При создании плана необходимо выбрать ценовую категорию, расположение и другие параметры.</span><span class="sxs-lookup"><span data-stu-id="21522-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="21522-146">Дополнительные сведения о планах службы приложений см. в статье [Обзор планов службы приложений Azure](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span><span class="sxs-lookup"><span data-stu-id="21522-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="21522-147">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="21522-147">Select **Create**.</span></span> <span data-ttu-id="21522-148">Azure подготовит и запустит веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="21522-148">Azure will provision and start the web app.</span></span>

   ![Портал Azure: колонка "Основное" для приложения SampleWebAppDemo01](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="21522-150">Включение публикации в Git для нового веб-приложения</span><span class="sxs-lookup"><span data-stu-id="21522-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="21522-151">Git — это распределенная система управления версиями, с помощью которой можно развернуть веб-приложение службы приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="21522-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="21522-152">Код веб-приложения хранится в локальном репозитории Git и развертывается в Azure путем отправки в удаленный репозиторий.</span><span class="sxs-lookup"><span data-stu-id="21522-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="21522-153">Войдите на [портал Azure](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="21522-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="21522-154">Щелкните **Службы приложений**, чтобы просмотреть список служб приложений, связанных с подпиской Azure.</span><span class="sxs-lookup"><span data-stu-id="21522-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="21522-155">Выберите веб-приложение, созданное в предыдущем разделе этого руководства.</span><span class="sxs-lookup"><span data-stu-id="21522-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="21522-156">В колонке **Deployment** (Развертывание) выберите **Deployment options** (Параметры развертывания) > **Выбор источника** > **Локальный репозиторий Git**.</span><span class="sxs-lookup"><span data-stu-id="21522-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![Колонка "Параметры": Колонка "Источник развертывания": Колонка "Выбрать источник"](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="21522-158">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="21522-158">Select **OK**.</span></span>

1. <span data-ttu-id="21522-159">Если учетные данные развертывания для публикации веб-приложения или другого приложения службы приложений еще не настроены, сделайте это сейчас.</span><span class="sxs-lookup"><span data-stu-id="21522-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="21522-160">Выберите **Параметры** > **Учетные данные развертывания**.</span><span class="sxs-lookup"><span data-stu-id="21522-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="21522-161">Появится колонка **Установка учетных данных развертывания**.</span><span class="sxs-lookup"><span data-stu-id="21522-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="21522-162">Создайте имя пользователя и пароль.</span><span class="sxs-lookup"><span data-stu-id="21522-162">Create a user name and password.</span></span> <span data-ttu-id="21522-163">Сохраните пароль для дальнейшего использования при настройке Git.</span><span class="sxs-lookup"><span data-stu-id="21522-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="21522-164">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="21522-164">Select **Save**.</span></span>

1. <span data-ttu-id="21522-165">В колонке **Веб-приложение** выберите **Параметры** > **Свойства**.</span><span class="sxs-lookup"><span data-stu-id="21522-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="21522-166">URL-адрес удаленного репозитория Git, в котором будет развертываться приложение, отображается в поле **URL-адрес GIT**.</span><span class="sxs-lookup"><span data-stu-id="21522-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="21522-167">Скопируйте значение в поле **URL-адрес Git**, так как оно понадобится далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="21522-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Портал Azure > колонка свойств приложения](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="21522-169">Публикация веб-приложения в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="21522-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="21522-170">В этом разделе с помощью Visual Studio будет создан локальный репозиторий Git, из которого код будет отправлен в Azure, чтобы развернуть веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="21522-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="21522-171">Для этого потребуется выполнить указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="21522-171">The steps involved include the following:</span></span>

* <span data-ttu-id="21522-172">Добавьте параметр удаленного репозитория, используя значение URL-адреса GIT, чтобы развернуть локальный репозиторий в Azure.</span><span class="sxs-lookup"><span data-stu-id="21522-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="21522-173">Зафиксируйте изменения, внесенные в проект.</span><span class="sxs-lookup"><span data-stu-id="21522-173">Commit project changes.</span></span>
* <span data-ttu-id="21522-174">Отправьте изменения, внесенные в проект, из локального репозитория в удаленный репозиторий в Azure.</span><span class="sxs-lookup"><span data-stu-id="21522-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="21522-175">В **обозревателе решений** щелкните правой кнопкой мыши элемент **Решение "SampleWebAppDemo"** и выберите пункт **Зафиксировать**.</span><span class="sxs-lookup"><span data-stu-id="21522-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="21522-176">Откроется **Team Explorer**.</span><span class="sxs-lookup"><span data-stu-id="21522-176">The **Team Explorer** is displayed.</span></span>

   ![Вкладка "Подключение" в Team Explorer](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="21522-178">В **Team Explorer** щелкните значок **Главная** и выберите **Параметры** > **Параметры репозитория**.</span><span class="sxs-lookup"><span data-stu-id="21522-178">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="21522-179">В разделе **Удаленные** окна **Параметры репозитория** нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="21522-179">In the **Remotes** section of the **Repository Settings**, select **Add**.</span></span> <span data-ttu-id="21522-180">Откроется диалоговое окно **Добавить удаленный объект**.</span><span class="sxs-lookup"><span data-stu-id="21522-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="21522-181">В качестве **имени** удаленного объекта укажите **Azure-SampleApp**.</span><span class="sxs-lookup"><span data-stu-id="21522-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="21522-182">В поле **Извлечь** укажите **URL-адрес Git**, скопированный ранее на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="21522-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="21522-183">Обратите внимание на то, что этот URL-адрес должен заканчиваться на **.git**.</span><span class="sxs-lookup"><span data-stu-id="21522-183">Note that this is the URL that ends with **.git**.</span></span>

   ![Диалоговое окно "Изменение удаленного объекта"](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="21522-185">Удаленный репозиторий можно также указать в **командном окне**. Для этого откройте **командное окно**, перейдите в каталог проекта и введите команду.</span><span class="sxs-lookup"><span data-stu-id="21522-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the command.</span></span> <span data-ttu-id="21522-186">Пример.</span><span class="sxs-lookup"><span data-stu-id="21522-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="21522-187">Щелкните значок **Главная** и выберите **Параметры** > **Глобальные параметры**.</span><span class="sxs-lookup"><span data-stu-id="21522-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="21522-188">Убедитесь, что имя и адрес электронной почты заданы.</span><span class="sxs-lookup"><span data-stu-id="21522-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="21522-189">При необходимости щелкните **Обновить**.</span><span class="sxs-lookup"><span data-stu-id="21522-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="21522-190">Выберите **Главная** > **Изменения**, чтобы вернуться к представлению **Изменения**.</span><span class="sxs-lookup"><span data-stu-id="21522-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="21522-191">Введите сообщение о фиксации, например **Начальная отправка № 1**, и щелкните **Зафиксировать**.</span><span class="sxs-lookup"><span data-stu-id="21522-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="21522-192">В результате будет создана локальная *фиксация*.</span><span class="sxs-lookup"><span data-stu-id="21522-192">This action creates a *commit* locally.</span></span>

   ![Вкладка "Подключение" в Team Explorer](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="21522-194">Изменения можно также фиксировать в **командном окне**. Для этого откройте **командное окно**, перейдите в каталог проекта и введите команды Git.</span><span class="sxs-lookup"><span data-stu-id="21522-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="21522-195">Пример.</span><span class="sxs-lookup"><span data-stu-id="21522-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="21522-196">Выберите **Главная** > **Синхронизировать** > **Действия** > **Открыть командную строку**.</span><span class="sxs-lookup"><span data-stu-id="21522-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="21522-197">В командной строке откроется каталог проекта.</span><span class="sxs-lookup"><span data-stu-id="21522-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="21522-198">Введите в командном окне следующую команду:</span><span class="sxs-lookup"><span data-stu-id="21522-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp main`

1. <span data-ttu-id="21522-199">Введите пароль из **учетных данных развертывания** в Azure, созданных ранее на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="21522-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="21522-200">Эта команда запускает процесс отправки локальных файлов проекта в Azure.</span><span class="sxs-lookup"><span data-stu-id="21522-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="21522-201">Выходные данные приведенной выше команды заканчиваются сообщением о том, что развертывание прошло успешно.</span><span class="sxs-lookup"><span data-stu-id="21522-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      main -> main
   Branch main set up to track remote branch main from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="21522-202">Если вам нужно работать над проектом совместно с другими пользователями, возможно, следует отправлять изменения в [GitHub](https://github.com), прежде чем отправлять их в Azure.</span><span class="sxs-lookup"><span data-stu-id="21522-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="21522-203">Проверка активного развертывания</span><span class="sxs-lookup"><span data-stu-id="21522-203">Verify the Active Deployment</span></span>

<span data-ttu-id="21522-204">Убедитесь, что передача веб-приложения из локальной среды в Azure выполнена.</span><span class="sxs-lookup"><span data-stu-id="21522-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="21522-205">На [портале Azure](https://portal.azure.com) выберите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="21522-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="21522-206">Выберите **Развертывание** > **Параметры развертывания**.</span><span class="sxs-lookup"><span data-stu-id="21522-206">Select **Deployment** > **Deployment options**.</span></span>

![Портал Azure: Колонка "Параметры": Колонка "Развертывания", содержащая успешное развертывание](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="21522-208">Запуск приложения в Azure</span><span class="sxs-lookup"><span data-stu-id="21522-208">Run the app in Azure</span></span>

<span data-ttu-id="21522-209">Теперь, когда веб-приложение развертывается в Azure, запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="21522-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="21522-210">Это можно сделать двумя способами:</span><span class="sxs-lookup"><span data-stu-id="21522-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="21522-211">На портале Azure найдите колонку соответствующего веб приложения.</span><span class="sxs-lookup"><span data-stu-id="21522-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="21522-212">Щелкните **Обзор**, чтобы открыть приложение в браузере по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="21522-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="21522-213">Откройте браузер и введите URL-адрес веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="21522-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="21522-214">Пример: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="21522-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="21522-215">Обновление и повторная публикация веб-приложения</span><span class="sxs-lookup"><span data-stu-id="21522-215">Update the web app and republish</span></span>

<span data-ttu-id="21522-216">После внесения изменений в локальный код выполните повторную публикацию.</span><span class="sxs-lookup"><span data-stu-id="21522-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="21522-217">В **обозревателе решений** Visual Studio откройте файл *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="21522-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="21522-218">В методе `Configure` измените метод `Response.WriteAsync`, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="21522-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="21522-219">Сохраните изменения в файле *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="21522-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="21522-220">В **обозревателе решений** щелкните правой кнопкой мыши элемент **Решение "SampleWebAppDemo"** и выберите пункт **Зафиксировать**.</span><span class="sxs-lookup"><span data-stu-id="21522-220">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="21522-221">Откроется **Team Explorer**.</span><span class="sxs-lookup"><span data-stu-id="21522-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="21522-222">Введите сообщение о фиксации, например: `Update #2`.</span><span class="sxs-lookup"><span data-stu-id="21522-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="21522-223">Нажмите кнопку **Зафиксировать**, чтобы зафиксировать изменения проекта.</span><span class="sxs-lookup"><span data-stu-id="21522-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="21522-224">Выберите **Главная** > **Синхронизировать** > **Действия** > **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="21522-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="21522-225">Изменения можно также отправить в **командном окне**. Для этого откройте **командное окно**, перейдите в каталог проекта и введите команду Git.</span><span class="sxs-lookup"><span data-stu-id="21522-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="21522-226">Пример.</span><span class="sxs-lookup"><span data-stu-id="21522-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp main`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="21522-227">Просмотр обновленного веб-приложения в Azure</span><span class="sxs-lookup"><span data-stu-id="21522-227">View the updated web app in Azure</span></span>

<span data-ttu-id="21522-228">Чтобы просмотреть обновленное веб-приложение, на портале Azure в колонке веб-приложения выберите **Обзор** или откройте браузер и введите URL-адрес веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="21522-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="21522-229">Пример: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="21522-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="21522-230">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="21522-230">Additional resources</span></span>

* [<span data-ttu-id="21522-231">Создание первого конвейера с помощью Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="21522-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="21522-232">Проект Kudu</span><span class="sxs-lookup"><span data-stu-id="21522-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
