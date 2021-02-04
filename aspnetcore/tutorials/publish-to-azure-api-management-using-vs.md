---
title: Публикация веб-API ASP.NET Core в службе управления API Azure с помощью Visual Studio
author: codemillmatt
description: Узнайте, как опубликовать веб-API ASP.NET Core в службе управления API Azure с помощью Visual Studio.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: ddff54bbd146c98cf83a865910401df26e7ac4ec
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217587"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a>Публикация веб-API ASP.NET Core в службе управления API Azure с помощью Visual Studio

Автор: [Мэтт Сукуп](https://twitter.com/codemillmatt) (Matt Soucoup)

## <a name="set-up"></a>Настройка

- Создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/dotnet/), если у вас ее нет.
- [Создайте новый экземпляр службы управления API Azure](/azure/api-management/get-started-create-service-instance), если вы еще этого не сделали.
- [Создайте проект приложения веб-API](xref:tutorials/first-web-api#create-a-web-project).

## <a name="configure-the-app"></a>Настройка приложения

Добавление определений Swagger в веб-API ASP.NET Core позволяет службе управления API Azure считывать определения API приложения. Выполните следующие шаги.

### <a name="add-swagger"></a>Добавление Swagger

1. Добавьте пакет NuGet [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) в проект веб-API ASP.NET Core.

    ![Снимок экрана для настройки диалогового окна NuGet](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. Добавьте следующую строку в метод `Startup.ConfigureServices`:
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. Добавьте следующую строку в метод `Startup.Configure`:

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a>Изменение маршрутизации API

Далее необходимо изменить структуру URL-адреса, требуемую для доступа к действию `Get` в `WeatherForecastController`. Выполните следующие шаги:

1. Откройте файл *WeatherForecastController.cs*.
1. Удалите атрибут `[Route("[controller]")]` уровня класса. Определение класса будет выглядеть следующим образом.

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. Добавьте атрибут `[Route("/")]` к действию `Get()`. Определение функции будет выглядеть следующим образом.

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a>Публикация веб-API в Службе приложений Azure

Выполните следующие действия, чтобы опубликовать веб-API ASP.NET Core в службе управления API Azure.

1. Публикация приложения API в Службе приложений Azure
1. Добавьте пустой API в экземпляр службы управления API Azure.
1. Опубликуйте приложение веб-API ASP.NET Core в экземпляре службы управления API Azure.

### <a name="publish-the-api-app-to-azure-app-service"></a>Публикация приложения API в Службе приложений Azure

Выполните следующие действия, чтобы опубликовать веб-API ASP.NET Core в службе управления API Azure.

1. В **Обозревателе решений** щелкните проект правой кнопкой мыши и выберите действие **Публикация**.

    ![Открытое контекстное меню с выделенной ссылкой "Опубликовать"](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. В диалоговом окне **Публикация** выберите **Azure** и нажмите кнопку **Далее**.

    ![Диалоговое окно публикации](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. Выберите **Служба приложений Azure (Windows)** и нажмите кнопку **Далее**.

    ![Диалоговое окно публикации: выбор службы приложений](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. Выберите **Создание новой службы приложений Azure**.

    ![Диалоговое окно публикации: выбор экземпляра службы Azure](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    Откроется диалоговое окно **Создать службу приложений**. Заполняются поля ввода **Имя приложения**, **Группа ресурсов** и **План службы приложений**. Вы можете сохранить эти имена или изменить их.
1. Нажмите кнопку **Создать**.

    ![Диалоговое окно "Создание службы приложений"](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

Как только создание завершится, диалоговое окно автоматически закроется и снова активируется окно **Публикация**. Созданный экземпляр выбирается автоматически.

![Диалоговое окно публикации: выбор экземпляра Службы приложений](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

На этом этапе необходимо добавить API в службу управления API Azure. Не открывайте Visual Studio, пока выполняются следующие задачи.

### <a name="add-an-api-to-azure-api-management"></a>Добавление API в службу управления API Azure

1. Откройте экземпляр службы управления API, созданный ранее на портале Azure. Выберите колонку **API-интерфейсов**.

  ![Колонка API-интерфейсов, выбранная из экземпляра службы управления API](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. Выберите три точки рядом с **API Echo** и выберите **Удалить** во всплывающем меню, чтобы удалить его.

  ![Удаление API Echo из экземпляра службы Управления API](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. На панели **Добавить новый API** выберите плитку **Пустой API**.

  ![Экран, на котором выделена плитка пустого API](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. Введите следующие значения в диалоговом окне **Создание пустого API**, которое появляется.    

    - **Отображаемое имя**: *WeatherForecasts*
    - **Имя**: *weatherforecasts*
    - **Суффикс URL-адреса API**: *v1*
    - Оставьте поле **URL-адрес веб-службы** пустым.

1. Нажмите кнопку **Создать**.

    ![Снимок экрана: диалоговое окно завершения создания пустого API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

Создается пустой API.

![Снимок экрана пустого API на портале управления API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a>Публикация веб-API ASP.NET Core в службе управления API Azure

1. Вернитесь в Visual Studio. Диалоговое окно **Публикация** должно оставаться открытым там, где вы остановились ранее.
1. Выберите только что опубликованную службу приложений Azure, чтобы выделить ее.
1. Нажмите кнопку **Далее**.

    ![Снимок экрана: диалоговое окно публикации с выбранной службой приложений](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. Теперь в диалоговом окне отображается служба управления API Azure, созданная ранее. Разверните его и папку *интерфейсов API*, чтобы увидеть созданный пустой API.
1. Выберите имя пустого API и нажмите кнопку **Готово**.

    ![Снимок экрана с недавно созданным пустым API службы управления API Azure, выбранным в диалоговом окне публикации](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. Диалоговое окно закроется, а затем появится экран сводки со сведениями о публикации. Нажмите кнопку **Опубликовать**.

    ![Снимок экрана Visual Studio с отображаемым профилем публикации](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    Веб-API будет опубликован как в службе приложений Azure, так и в службе управления API Azure. Появится новое окно браузера, в котором будет показан API, работающий в службе приложений Azure. Это окно можно закрыть.

1. Перейдите к экземпляру управления API Azure на портале Azure.
1. Обновите приложение в браузере.
1. Выберите пустой API, созданный на предыдущих шагах. Теперь он заполнен, и вы можете изучить его.

    ![Снимок экрана с заполненным API в службе управления API Azure](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a>Настройка имени опубликованного API

Обратите внимание, что имя API отличается от имени, которое вы присвоили. Опубликованный API называется *WeatherAPI*; однако вы присваивали ему имя *WeatherForecasts* при его создании. Чтобы переименовать его, выполните следующие действия.

![Снимок экрана API с разными именами, которые выделены](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. Удалите следующую строку из метода `Startup.ConfigureServices`.
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. Добавьте следующий код в метод `Startup.ConfigureServices`:
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("v1", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "WeatherForecasts",
            Version = "v1"
        });
    });
    ```

1. Откройте созданный профиль публикации. Его можно найти в **обозревателе решений** в папке *Properties/PublishProfiles*.

    ![Снимок экрана с выделенным расположением файла профиля публикации](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. Измените значение элемента `<OpenAPIDocumentName>` с `v1` на `WeatherForecasts`.

    ![Снимок экрана изменений, необходимых для профиля публикации](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. Повторно опубликуйте веб-API ASP.NET Core и откройте экземпляр службы управления API Azure на портале Azure.
1. Обновите страницу в браузере. Теперь отобразится правильное имя API.

    ![Снимок экрана готового API на портале](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a>Проверка работоспособности веб-API

Вы можете протестировать развернутый веб-API ASP.NET Core в службе управления API Azure на портале Azure, выполнив следующие действия.

1. Откройте вкладку **Тест**.
1. Выберите **/** или операцию **Get**.
1. Нажмите кнопку **Отправить**.

    ![Снимок экрана портала перед тестом](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

Успешный ответ будет выглядеть следующим образом.

![Снимок экрана успешного ответа от службы управления API](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a>Очистка

Завершив тестирование приложения, перейдите на [портал Azure](https://portal.azure.com/) и удалите приложение.

1. Выберите пункт **Группы ресурсов**, а затем созданную группу ресурсов.

    ![Портал Azure: "Группы ресурсов" в меню боковой панели](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. На странице **Группы ресурсов** выберите **Удалить**.

    ![Портал Azure: страница "Группы ресурсов"](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. Введите имя группы ресурсов и выберите **Удалить**. Ваше приложение и все ресурсы, созданные при работе с этим руководством, удалены из Azure.

## <a name="next-steps"></a>Следующие шаги

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Управление API Azure](/azure/api-management/api-management-key-concepts)
- [Служба приложений Azure](/azure/app-service/app-service-web-overview)
