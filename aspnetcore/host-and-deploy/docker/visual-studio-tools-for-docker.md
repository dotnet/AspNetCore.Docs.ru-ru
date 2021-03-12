---
title: Использование средств Visual Studio для контейнеров с ASP.NET Core
author: spboyer
description: Сведения о том, как упаковать в контейнер приложение ASP.NET Core с помощью средств Visual Studio и Docker для Windows.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
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
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: c11261fef84234a9caaf872ae9871932d02617fa
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585998"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a>Использование средств Visual Studio для контейнеров с ASP.NET Core

Visual Studio 2017 и более поздних версий поддерживает сборку, отладку и запуск контейнерных приложений ASP.NET Core для работы с .NET Core. Поддерживаются контейнеры Windows и Linux.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Предварительные требования

* [Docker для Windows](https://docs.docker.com/docker-for-windows/install/)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **Кроссплатформенная разработка .NET Core**

## <a name="installation-and-setup"></a>Установка и настройка

Перед установкой Docker ознакомьтесь со статьей о [Docker для Windows и том, что следует знать перед установкой](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). Затем [установите Docker для Windows](https://docs.docker.com/docker-for-windows/install/).

Нужно настроить **[Общие диски](https://docs.docker.com/docker-for-windows/#shared-drives)** в Docker для Windows, чтобы обеспечить поддержку сопоставления тома и отладки. Щелкните правой кнопкой мыши значок Docker на панели задач и выберите пункт **Параметры**, а затем — **Общие диски**. Выберите диск, на котором Docker сохраняет файлы. Нажмите кнопку **Применить**.

![Диалоговое окно для выбора общего локального диска C для контейнеров](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> Visual Studio 2017 версии 15.6 и более поздней выдает предупреждение, если **Общие диски** не настроены.

## <a name="add-a-project-to-a-docker-container"></a>Добавление проекта в контейнер Docker

Чтобы сделать проект ASP.NET Core контейнерным, этот проект должен быть предназначен для .NET Core. Поддерживаются как контейнеры Linux, так и контейнеры Windows.

При добавлении в проект поддержки Docker выберите контейнер Windows или Linux. Узел Docker должен работать на контейнерах такого же типа. Чтобы изменить тип контейнера для работающего экземпляра Docker, щелкните правой кнопкой мыши значок Docker в области уведомлений и выберите **Переключение на контейнеры Windows** или **Переключение на контейнеры Linux**.

### <a name="new-app"></a>Новое приложение

При создании приложения с помощью шаблонов проектов **веб-приложения ASP.NET Core** установите флажок **Включение поддержки Docker**:

![Флажок "Включение поддержки Docker"](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

Если целевой платформой является .NET Core, в раскрывающемся списке **ОС** можно выбрать тип контейнера.

### <a name="existing-app"></a>Существующее приложение

Для проектов ASP.NET Core, ориентированных на .NET Core, добавить поддержку Docker через средства можно двумя способами. Откройте проект в Visual Studio и выберите один из следующих параметров:

* Выберите пункт **Поддержка Docker** в меню **Проект**.
* В **Обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункты **Добавить** > **Поддержка Docker**.

Средства Visual Studio для контейнеров не поддерживают добавление Docker в существующий проект ASP.NET Core, предназначенный для .NET Framework.

## <a name="dockerfile-overview"></a>Общие сведения о Dockerfile

*Dockerfile* с инструкциями по созданию окончательного образа Docker добавляется в корень проекта. См. [справочник по Dockerfile](https://docs.docker.com/engine/reference/builder/) для получения сведений о других доступных в нем командах. Этот конкретный *Dockerfile* использует [многоэтапную сборку](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) из четырех раздельных именованных этапов:

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

Предыдущий *Dockerfile* основан на образе [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/). Этот базовый образ содержит среду выполнения ASP.NET Core и пакеты NuGet. Пакеты скомпилированы JIT-компилятором для повышения производительности запуска.

Если в диалоговом окне создания проекта установлен флажок **Configure for HTTPS** (Настроить для трафика HTTPS), *Dockerfile* предоставляет два порта. Один порт используется для трафика HTTP, другой — для HTTPS. Если флажок не установлен, для трафика HTTP предоставляется один порт (80).

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

Предыдущий *Dockerfile* основан на образе [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/). Этот базовый образ включает пакеты NuGet платформы ASP.NET Core, которые скомпилированы JIT-компилятором для повышения производительности запуска.

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a>Добавление поддержки оркестратора контейнеров в приложение

Visual Studio 2017 версии 15.7 или более ранние версии поддерживают [Docker Compose](https://docs.docker.com/compose/overview/) как единственное решение для оркестрации контейнеров. Для добавления артефактов Docker Compose необходимо последовательно выбрать **Добавить** > **Поддержка Docker**.

Visual Studio 2017 версии 15.8 или более поздние версии поддерживают решение для оркестрации только когда это указано отдельно. В **Обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункты **Добавить** > **Поддержка оркестратора контейнеров**. Доступны следующие варианты: 

* [Docker Compose](#docker-compose)
* [Service Fabric](#service-fabric)
* [Kubernetes/Helm ](https://helm.sh/)

### <a name="docker-compose"></a>Docker Compose

Средства Visual Studio для контейнеров добавляют в решение проект *docker-compose* со следующими файлами:

* *docker-compose.dcproj.* Файл, представляющий проект. Включает в себя элемент `<DockerTargetOS>`, указывающий используемую ОС.
* *.dockerignore.* Содержит список шаблонов файлов и каталогов для исключения при создании контекста сборки.
* *docker-compose.yml.* Базовый файл [Docker Compose](https://docs.docker.com/compose/overview/), который служит для определения коллекции образов, сборка и запуск которых выполняется с помощью команд `docker-compose build` и `docker-compose run` соответственно.
* *docker-compose.override.yml.* Дополнительный файл, считываемый Docker Compose и содержащий переопределения конфигурации для служб. Visual Studio выполняет `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` для объединения этих файлов.

Файл *docker-compose.yml* ссылается на имя образа, создаваемого при выполнении проекта:

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

В предыдущем примере `image: hellodockertools` создает образ `hellodockertools:dev`, когда приложение выполняется в режиме **отладки**. Образ `hellodockertools:latest` создается, когда приложение запускается в режиме **выпуска**.

Если образ будет отправляться в реестр, добавьте имя пользователя [центра Docker](https://hub.docker.com/) (например, `dockerhubusername/hellodockertools`) в качестве префикса к имени образа. Кроме того, в имя образа можно включить URL-адрес частного реестра (например, `privateregistry.domain.com/hellodockertools`) в зависимости от конфигурации.

Чтобы реализовать разное поведение в зависимости от конфигурации сборки (например, отладка или выпуск), добавьте зависящие от конфигурации файлы *docker-compose*. Имена файлов должны соответствовать конфигурации сборки (например, *docker-compose.vs.debug.yml* и *docker-compose.vs.release.yml*) и находиться в том же расположении, что и файл *docker-compose-override.yml*. 

Используя зависящие от конфигурации файлы переопределения, можно указать различные параметры (например, переменные среды или точки входа) для конфигураций отладки и выпуска.

Чтобы в Docker Compose отображался параметр для запуска в Visual Studio, проект Docker должен быть запускаемым.

### <a name="service-fabric"></a>Service Fabric.

Помимо базовых [Предварительных требований](#prerequisites) для решения по оркестрации [Service Fabric](/azure/service-fabric/) необходимо выполнить следующие предварительные условия:

* [Пакет SDK для Microsoft Azure Service Fabric](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) версии 2.6 или более поздней версии
* Рабочая нагрузка **Разработки Azure** в Visual Studio

Service Fabric не поддерживает запуск контейнеров Linux в кластере локальной разработки в Windows. Если в проекте уже используется контейнер Linux, Visual Studio предложит переключиться на контейнеры Windows.

Средства Visual Studio для контейнеров позволяют выполнять следующие задачи:

* добавление проекта **Service Fabric Application** *&lt;имя_проекта&gt;приложение* в решение;
* добавление *Dockerfile* и файла *.dockerignore* в проект ASP.NET Core. Если *Dockerfile* уже существует в проекте ASP.NET Core, он переименовывается в *Dockerfile.original*. Создается новый *Dockerfile*, аналогичный следующему:

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* добавляет элемент `<IsServiceFabricServiceProject>` в файл *.csproj* проекта ASP.NET Core;

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* добавляет папку *PackageRoot* в проект ASP.NET Core. Папка содержит манифест службы и параметры для новой службы.

Дополнительные сведения см. в статье [Руководство по развертыванию приложения .NET в контейнере Windows в Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).

## <a name="debug"></a>Отладка

Выберите пункт **Docker** в раскрывающемся списке отладки на панели инструментов, чтобы начать отладку приложения. Представление **Docker** окна **Выходные данные** показывает следующие выполненные действия:

::: moniker range=">= aspnetcore-2.1"

* Получение тега *2.1-aspnetcore-runtime* образа среды выполнения *microsoft/dotnet* (если его еще нет в кэше). Образ устанавливает среды выполнения ASP.NET Core и .NET Core и связанные библиотеки. Он оптимизирован для запуска приложений ASP.NET Core в рабочей среде.
* Задание для переменной среды `ASPNETCORE_ENVIRONMENT` значения `Development` внутри контейнера.
* Предоставление двух динамически назначенных портов: для трафика HTTP и HTTPS. Отправка запроса к порту, назначенному localhost, с помощью команды `docker ps`.
* Копирование приложения в контейнер.
* Запуск браузера по умолчанию с подключенным отладчиком для обращения к контейнеру по динамически назначаемому порту.

Итоговый образ Docker приложения помечается как *dev*. Образ основан на теге *2.1-aspnetcore-runtime* базового образа *microsoft/dotnet*. Выполните команду `docker images` в окне **Консоль диспетчера пакетов** (PMC). На компьютере отобразятся следующие образы:

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* Получение образа среды выполнения *microsoft/aspnetcore* (если его еще нет в кэше).
* Задание для переменной среды `ASPNETCORE_ENVIRONMENT` значения `Development` внутри контейнера.
* Порт 80 открыт и сопоставлен с динамически назначаемым портом для localhost. Порт определяется узлом Docker и может запрашиваться с помощью команды `docker ps`.
* Копирование приложения в контейнер.
* Запуск браузера по умолчанию с подключенным отладчиком для обращения к контейнеру по динамически назначаемому порту.

Итоговый образ Docker приложения помечается как *dev*. Образ основан на базовом образе *microsoft/aspnetcore*. Выполните команду `docker images` в окне **Консоль диспетчера пакетов** (PMC). На компьютере отобразятся следующие образы:

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> В образе *dev* нет содержимого приложения, так как конфигурации **Отладки** используют подключение томов для удобства при итеративных процессах отладки. Чтобы отправить образ, используйте конфигурацию **выпуска**.

Выполните команду `docker ps` в PMC. Обратите внимание, что приложение выполняется с помощью контейнера:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a>Изменение и продолжение

Изменения статических полей и представлений Razor применяются автоматически без необходимости выполнять этап компиляции. Внесите изменение, сохраните его и перезагрузите страницу в браузере, чтобы увидеть обновление.

Для внесения изменений в файлы кода требуются выполнить компиляцию и перезапуск Kestrel в контейнере. После внесения изменения нажмите клавиши `CTRL+F5`, чтобы выполнить процесс и запустить приложение в контейнере. Контейнер Docker не перестраивается и не останавливается. Выполните команду `docker ps` в PMC. Обратите внимание, что исходный контейнер все еще выполняется, как и 10 минут назад:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a>Публикация образов Docker

После завершения цикла разработки и отладки приложения используйте Средства Visual Studio для контейнеров, чтобы создать рабочий образ приложения. Выберите в раскрывающемся списке конфигурации значение **Выпуск** и выполните сборку приложения. Инструментарий получает образ компиляции или публикации из центра Docker (если он отсутствует в кэше). Образ создается с тегом *latest*, который можно отправить в закрытый реестр или в центр Docker.

В PMC выполните команду `docker images`, чтобы просмотреть список образов. Выходные данные должны выглядеть примерно так:

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

Начиная с .NET Core 2.1, образы `microsoft/aspnetcore-build` и `microsoft/aspnetcore`, указанные в предыдущих выходных данных, заменяются образами `microsoft/dotnet`. Дополнительные сведения см. в [объявлении о миграции репозиториев Docker](https://github.com/aspnet/Announcements/issues/298).

::: moniker-end

> [!NOTE]
> Команда `docker images` возвращает промежуточные образы с именами репозитория и тегами, обозначенными как *\<none>* (не указаны выше). Эти неименованные образы создаются *Dockerfile* [многоэтапной сборки](https://docs.docker.com/engine/userguide/eng-image/multistage-build/). Они повышают эффективность сборки окончательного образа &mdash; при изменениях перестраиваются только необходимые слои. Когда промежуточные образы больше не требуются, удалите их с помощью команды [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).

Рабочий образ или образ выпуска может быть меньше по размеру, чем образ *dev*. Из-за сопоставления томов отладчик и приложение запускались с локального компьютера, а не внутри контейнера. Образ с тегом *latest* упаковал код приложения, необходимый для запуска приложения на хост-компьютере. Таким образом, размер изменится на размер кода конкретного приложения.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Разработка с помощью контейнеров в Visual Studio](/visualstudio/containers)
* [Azure Service Fabric: Prepare your development environment](/azure/service-fabric/service-fabric-get-started) (Настройка среды разработки для Windows)
* [Руководство по развертыванию приложения .NET в контейнере Windows в Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [Устранение неполадок при разработке с Docker в Visual Studio](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [Репозиторий GitHub со средствами Visual Studio для контейнеров](https://github.com/Microsoft/DockerTools)
* [GC с использованием DOCKER и небольших контейнеров](xref:performance/memory#sc)
