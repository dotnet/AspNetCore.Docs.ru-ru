---
title: Конфигурация в .NET Core
author: rick-anderson
description: Узнайте, как использовать API конфигурации для настройки приложения ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 1/29/2021
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
uid: fundamentals/configuration/index
ms.openlocfilehash: fa7fbf1ae88b28cd36c5aece626240d849669a96
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586349"
---
# <a name="configuration-in-aspnet-core"></a>Конфигурация в .NET Core

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)

::: moniker range=">= aspnetcore-3.0"

Настройка в ASP.NET Core выполняется с помощью одного или нескольких [поставщиков конфигурации](#cp). Поставщики конфигурации получают данные конфигурации в парах "ключ-значение" из различных источников:

* файлов параметров, таких как *appsettings.json* ;
* Переменные среды
* Хранилище ключей Azure;
* конфигурация приложения Azure;
* аргументов командной строки;
* пользовательские поставщики, установленные или созданные;
* справочных файлов;
* объектов .NET в памяти;

В этом разделе приведены сведения о конфигурации в ASP.NET Core. Сведения об использовании конфигурации в консольных приложениях см. в статье [Конфигурация .NET](/dotnet/core/extensions/configuration).

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))

<a name="default"></a>

## <a name="default-configuration"></a>Конфигурация по умолчанию

Веб-приложения ASP.NET Core, созданные с помощью [dotnet new](/dotnet/core/tools/dotnet-new) или Visual Studio, создают следующий код:

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> предоставляет конфигурацию по умолчанию для приложения в следующем порядке:

1. [ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  добавляет существующий `IConfiguration` в качестве источника. В случае конфигурации по умолчанию добавляет конфигурацию [узла](#hvac) и задает ее в качестве первого источника для конфигурации _приложения_.
1. Файл [appsettings.json](#appsettingsjson), использующий [поставщик конфигурации JSON](#file-configuration-provider).
1. Файл *appsettings.* `Environment` *.json*, использующий [поставщик конфигурации JSON](#file-configuration-provider). Например, *appsettings*.***Production**_._json* и *appsettings*.***Development** _._json*.
1. [Секреты приложения](xref:security/app-secrets), когда приложение выполняется в среде `Development`.
1. Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).
1. Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line).

Поставщики конфигурации, добавленные позже, переопределяют предыдущие параметры ключа. Например, если `MyKey` задан в *appsettings.json* и в среде, используется значение среды. При использовании поставщиков конфигурации по умолчанию [поставщик конфигурации командной строки](#clcp) переопределяет остальных поставщиков.

Дополнительные сведения о `CreateDefaultBuilder` см. в разделе [Параметры сборщика по умолчанию](xref:fundamentals/host/generic-host#default-builder-settings).

В следующем коде отображаются включенные поставщики конфигурации в том порядке, в котором они были добавлены:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

Рассмотрим следующий файл *appsettings.json* :

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> по умолчанию загружает конфигурацию в следующем порядке:

1. *appsettings.json*
1. *appsettings.* `Environment` *.json* : Например, файлы *appsettings*.***Production**_._json* и *appsettings*.***Development** _._json*. Версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*). Для получения дополнительной информации см. <xref:fundamentals/environments>.

Значения в *appsettings*.`Environment`.*json* переопределяют ключи в *appsettings.json* . Например, по умолчанию:

* В среде разработки конфигурация *appsettings*.***Development** _._json* перезаписывает значения, найденные в *appsettings.json* .
* В рабочей среде конфигурация *appsettings*.***Production** _._json* перезаписывает значения, найденные в *appsettings.json* . Например, при развертывании приложения в Azure.

Если необходимо гарантировать значение конфигурации, см. раздел о [GetValue](#getvalue). Предыдущий пример поддерживает только считывание строк и не поддерживает значение по умолчанию.

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a>Привязка иерархических данных конфигурации с помощью шаблона параметров

[!INCLUDE[](~/includes/bind.md)]

С помощью конфигурации [по умолчанию](#default) файлы *appsettings.json* и *appsettings.* `Environment` *.json* включаются с помощью [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75). Изменения, внесенные в файлы *appsettings.json* и *appsettings.* `Environment` *.json* ***после*** запуска приложения, считываются [поставщиком конфигурации JSON](#jcp).

Сведения о добавлении дополнительных файлов конфигурации JSON см. в разделе [Поставщик конфигурации JSON](#jcp) в этом документе.

## <a name="combining-service-collection"></a>Объединение коллекций служб

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a>Безопасность и секреты пользователей

Рекомендации по данным конфигурации:

* Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста. Хранить секреты во время разработки можно с помощью [диспетчера секретов](xref:security/app-secrets).
* Не используйте секреты рабочей среды в средах разработки и тестирования.
* Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.

[По умолчанию](#default) источник конфигурации секрета пользователя регистрируется после источников конфигурации JSON. Таким образом, ключи секретов пользователя имеют приоритет над ключами в *appsettings.json* и *appsettings.* `Environment` *.json*.

Дополнительные сведения о хранении паролей или других конфиденциальных данных:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>. Содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных. Диспетчер секретов использует [поставщик конфигурации файла](#fcp) для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.

В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core. Для получения дополнительной информации см. <xref:security/key-vault-configuration>.

<a name="evcp"></a>

## <a name="environment-variables"></a>Переменные среды

При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пар "ключ-значение" в переменных среды после чтения *appsettings.json* , *appsettings.* `Environment` *.json* и [секретов пользователя](xref:security/app-secrets). Поэтому значения ключей, считанные из среды, переопределяют значения, считанные из *appsettings.json* , *appsettings.* `Environment` *.json* и секретов пользователя.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Следующие команды `set`:

* Задают ключи и значения среды в [предыдущем примере](#appsettingsjson) в Windows.
* Проверяют параметры при использовании [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample). Команда `dotnet run` должна выполняться в каталоге проекта.

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

Предыдущие параметры среды:

* Задаются только в процессах, запускаемых из командного окна, в котором они были установлены.
* Не будут считываться браузерами, запущенными в Visual Studio.

Следующие команды [setx](/windows-server/administration/windows-commands/setx) можно использовать для задания ключей и значений среды в Windows. В отличие от `set`, параметры `setx` сохраняются. `/M` задает переменную в системной среде. Если параметр `/M` не используется, задается переменная среды пользователя.

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

Чтобы проверить, что предыдущие команды переопределяют *appsettings.json* и *appsettings.* `Environment` *.json*, выполните следующие действия:

* В Visual Studio: Выйдите из среды Visual Studio и перезапустите ее.
* В CLI: Откройте новое командное окно и введите `dotnet run`.

Вызовите <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> со строкой, чтобы указать префикс для переменных среды:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

В приведенном выше коде:

* `config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` добавляется после [поставщиков конфигурации по умолчанию](#default). Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).
* Переменные среды, установленные с префиксом `MyCustomPrefix_`, переопределяют [поставщиков конфигурации по умолчанию](#default). Сюда входят переменные среды без префикса.

Префикс отделяется при создании пары конфигурации "ключ-значение".

Следующие команды проверяют пользовательский префикс:

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

[Конфигурация по умолчанию](#default) загружает переменные среды и аргументы командной строки с префиксом `DOTNET_` и `ASPNETCORE_`. Префиксы `DOTNET_` и `ASPNETCORE_` используются ASP.NET Core для [конфигурации узла и приложения](xref:fundamentals/host/generic-host#host-configuration), но не для конфигурации пользователя. Дополнительные сведения о конфигурации узла и приложения см. в разделе [Универсальный узел .NET](xref:fundamentals/host/generic-host).

В [Службе приложений Azure](https://azure.microsoft.com/services/app-service/) выберите **Новый параметр приложения** на странице **Параметры > Конфигурация**. Параметры приложения Службы приложений Azure:

* Шифруются, когда они неактивны, и передаются по зашифрованному каналу.
* Предоставляются как переменные среды.

Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

Сведения о строках подключения к базе данных Azure см. в разделе [Префиксы строк подключения](#constr).

### <a name="naming-of-environment-variables"></a>Именование переменных среды

Имена переменных среды отражают структуру файла *appsettings.json* . Каждый элемент в иерархии отделяется двойным символом подчеркивания (предпочтительно) или двоеточием. Если структура элемента включает массив, индекс массива должен рассматриваться как дополнительное имя элемента в этом пути. Рассмотрим следующий файл *appsettings.json* и его эквивалентные значения, представленные в виде переменных среды:

**appsettings.json**

```json
{
    "SmtpServer": "smtp.example.com",
    "Logging": [
        {
            "Name": "ToEmail",
            "Level": "Critical",
            "Args": {
                "FromAddress": "MySystem@example.com",
                "ToAddress": "SRE@example.com"
            }
        },
        {
            "Name": "ToConsole",
            "Level": "Information"
        }
    ]
}
```

**Переменные среды**

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-generated-launchsettingsjson"></a>Переменные среды, заданные в созданном файле launchSettings.json

Переменные среды, заданные в *launchSettings.json*, переопределяют переменные, заданные в системной среде. Например, веб-шаблоны ASP.NET Core позволяют создать файл *launchSettings.json*, который задает следующую конфигурацию конечной точки:

```json
"applicationUrl": "https://localhost:5001;http://localhost:5000"
```

При настройке `applicationUrl` устанавливается переменная среды `ASPNETCORE_URLS` и переопределяются значения, заданные в среде.

### <a name="escape-environment-variables-on-linux"></a>Экранирование переменных среды в Linux

В Linux значения переменных среды URL-адресов нужно экранировать, чтобы их можно было проанализировать с помощью `systemd`. Используйте средство Linux `systemd-escape`, которое приостанавливает `http:--localhost:5001`.
 
 ```cmd
 groot@terminus:~$ systemd-escape http://localhost:5001
 http:--localhost:5001
 ```

### <a name="display-environment-variables"></a>Отображение переменных среды

Следующий код позволяет отобразить переменные среды и значения при запуске приложения, что может быть полезно при отладке параметров среды:

[!code-csharp[](~/fundamentals/configuration/index/samples_snippets/5.x/Program.cs?name=snippet)]

<a name="clcp"></a>

## <a name="command-line"></a>Командная строка

При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пар "ключ-значение" аргументов командной строки после следующих источников конфигурации:

* Файлы *appsettings.json* и *appsettings*.`Environment`.*json*.
* [Секреты приложения](xref:security/app-secrets) в среде разработки.
* Переменные среды.

По [умолчанию](#default) значения конфигурации, заданные для значений конфигурации переопределения в командной строке, задаются для всех остальных поставщиков конфигурации.

### <a name="command-line-arguments"></a>аргументов командной строки;

Следующая команда задает ключи и значения с помощью `=`:

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

Следующая команда задает ключи и значения с помощью `/`:

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

Следующая команда задает ключи и значения с помощью `--`:

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

Значение ключа:

* Должно соответствовать `=`, или ключ должен иметь префикс `--` или `/`, когда значение следует за пробелом.
* Является обязательным, если используется параметр `=`. Например, `MySetting=`.

В рамках одной и той же команды не смешивайте пары "ключ-значение" аргумента командной строки, которые используют `=` с парами "ключ-значение" с пробелом.

### <a name="switch-mappings"></a>Сопоставления переключений

Сопоставление параметров позволяет указать логику замены имен **ключей**. Предоставьте словарь замены параметров для метода <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки. Если ключ в командной строке находится в словаре, значение словаря передается обратно, чтобы установить пару "ключ-значение" в конфигурацию приложения. Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).

Правила ключей из словаря сопоставления переключений:

* Параметры должны начинаться с `-` или `--`.
* Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.

Чтобы использовать словарь сопоставлений параметров, передайте его в вызов `AddCommandLine`:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

В следующем коде показаны ключевые значения для замененных ключей:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

Следующая команда проверяет замену ключа:

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны. Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные параметры, и нет возможности передать словарь сопоставления параметров в `CreateDefaultBuilder`. Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.

## <a name="hierarchical-configuration-data"></a>Иерархическая модель конфигурации

API конфигурации считывает иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.

[Пример скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *appsettings.json* :

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые параметры конфигурации:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Предпочтительный способ чтения иерархических данных конфигурации — использование шаблона параметров. Дополнительные сведения см. в разделе [Привязка иерархических данных конфигурации](#optpat) в этом документе.

Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации. Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection).

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a>Ключи и значения конфигурации

Ключи конфигурации:

* Не учитывают регистр. Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.
* Если ключ и значение заданы более чем в одном поставщике конфигурации, используется значение из последнего добавленного поставщика. Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default).
* Иерархические ключи
  * При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.
  * В переменных среды разделитель-двоеточие может не работать на всех платформах. Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие — `:`.
  * В Azure Key Vault иерархические ключи используют `--` в качестве разделителя. [Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) автоматически заменяет `--` на `:` при загрузке секретов в конфигурацию приложения.
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации. Привязка массива описана в разделе [Привязка массива к классу](#boa).

Значения конфигурации:

* Представляют собой строки.
* Значение NULL не может храниться в конфигурации или быть привязанным к объектам.

<a name="cp"></a>

## <a name="configuration-providers"></a>Поставщики конфигурации

В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.

| Поставщик | Предоставляет конфигурацию из |
| -------- | ----------------------------------- |
| [Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) | Хранилище ключей Azure; |
| [Поставщик конфигурации приложения Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) | конфигурация приложения Azure; |
| [Поставщик конфигурации командной строки](#clcp) | Параметры командной строки |
| [Поставщик пользовательской конфигурации](#custom-configuration-provider) | Источник пользователя |
| [Поставщик конфигурации переменных среды](#evcp) | Переменные среды |
| [Поставщик конфигурации файла](#file-configuration-provider) | Файлы INI, JSON и XML |
| [Поставщик конфигурации ключа для каждого файла](#key-per-file-configuration-provider) | справочных файлов; |
| [Поставщик конфигурации памяти](#memory-configuration-provider) | Коллекции оперативной памяти |
| [Секреты пользователя](xref:security/app-secrets) | Файл в каталоге профиля пользователя |

Источники конфигурации считываются в том порядке, в котором указываются их поставщики конфигурации. Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.

Типичная последовательность поставщиков конфигурации.

1. *appsettings.json*
1. *appsettings*.`Environment`.*json*
1. [Секреты пользователя](xref:security/app-secrets)
1. Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).
1. Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).

Общепринятой практикой является добавление поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.

Предыдущая последовательность поставщиков используется в [конфигурации по умолчанию](#default).

<a name="constr"></a>

### <a name="connection-string-prefixes"></a>Префиксы строк подключения

API конфигурации имеет особые правила обработки для четырех переменных среды строки подключения. Эти строки подключения участвуют в настройке строк подключения Azure для среды приложения. Переменные среды с префиксами, указанными в таблице, загружаются в приложение с [конфигурацией по умолчанию](#default) или если префикс не предоставлен для `AddEnvironmentVariables`.

| Префикс строки подключения | Поставщик |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Поставщик пользователя |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [База данных SQL Azure](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.

* Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).
* Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).

| Ключ переменной среды | Преобразованный ключ конфигурации | Запись конфигурации поставщика                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Запись конфигурации не создана.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Ключ: `ConnectionStrings:{KEY}_ProviderName`:<br>Значение: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Ключ: `ConnectionStrings:{KEY}_ProviderName`:<br>Значение: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Ключ: `ConnectionStrings:{KEY}_ProviderName`:<br>Значение: `System.Data.SqlClient`  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a>Поставщик конфигурации файла

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы. Следующие поставщики конфигурации являются производными от `FileConfigurationProvider`:

* [Поставщик конфигурации INI](#ini-configuration-provider)
* [Поставщик конфигурации JSON](#jcp)
* [Поставщик конфигурации XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Поставщик конфигурации INI

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.

Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

В приведенном выше коде параметры в *MyIniConfig.ini* и *MyIniConfig*.`Environment`.*ini* переопределяются параметрами в следующих поставщиках:

* [Поставщик конфигурации переменных среды](#evcp).
* [Поставщик конфигурации командной строки](#clcp).

[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyIniConfig.ini*:

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a>Поставщик конфигурации JSON

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ-значение" JSON-файла.

Перегрузки могут указывать:

* Файл является обязательным или нет.
* Будет ли перезагружена конфигурация, если файл изменится.

Рассмотрим следующий код.

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

Предыдущий код:

* Настраивает поставщик конфигурации JSON для загрузки файла *MyConfig.json* со следующими параметрами:
  * `optional: true`. Файл является необязательным.
  * `reloadOnChange: true`: При сохранении изменений файл перезагружается.
* Считывает [поставщиков конфигурации по умолчанию](#default) до файла *MyConfig.json*. Параметры в файле *MyConfig.json* переопределяют параметр в поставщиках конфигурации по умолчанию, включая [поставщик конфигурация переменных среды](#evcp) и [поставщик конфигурации командной строки](#clcp).

Обычно ***не*** требуется, чтобы пользовательские файлы JSON переопределяли значения, заданные в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).

Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

В приведенном выше коде параметры в файлах *MyConfig.json* и *MyConfig*.`Environment`.*json*:

* Переопределяют параметры в файлах *appsettings.json* и *appsettings*.`Environment`.*json*.
* Переопределяются параметрами в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).

[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyConfig.json*:

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a>Поставщик конфигурации XML

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.

Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

В приведенном выше коде параметры в *MyXMLFile.xml* и *MyXMLFile*.`Environment`.*xml* переопределяются параметрами в следующих поставщиках:

* [Поставщик конфигурации переменных среды](#evcp).
* [Поставщик конфигурации командной строки](#clcp).

[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyXMLFile.xml*:

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

Следующий код считывает предыдущий файл конфигурации и отображает ключи и значения:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

Атрибуты можно использовать для предоставления значений.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.

* key:attribute
* section:key:attribute

## <a name="key-per-file-configuration-provider"></a>Поставщик конфигурации ключа для каждого файла

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации. Ключ является именем файла. Значение содержит содержимое файла. Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.

Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>. Значение параметра `directoryPath` должно быть абсолютным путем к файлам.

Перегрузки позволяют указать следующее.

* `Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.
* Обязательно ли указывать каталог и путь к каталогу.

Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов. Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.

Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a>Поставщик конфигурации памяти

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.

Следующий код добавляет коллекцию памяти в систему конфигурации:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются перечисленные выше параметры конфигурации:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

В предыдущем коде `config.AddInMemoryCollection(Dict)` добавляется после [поставщиков конфигурации по умолчанию](#default). Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).

В разделе [Привязка массива](#boa) вы найдете еще один пример использования `MemoryConfigurationProvider`.

::: moniker-end
::: moniker range=">= aspnetcore-5.0"

<a name="kestrel"></a>

## <a name="kestrel-endpoint-configuration"></a>Конфигурация конечных точек Kestrel

Конкретная конфигурация конечной точки Kestrel переопределяет все конфигурации [межсерверных](xref:fundamentals/servers/index) конечных точек. Конфигурации межсерверных конечных точек включают:

  * [UseUrls](xref:fundamentals/host/web-host#server-urls).
  * `--urls` в [командной строке](xref:fundamentals/configuration/index#command-line);
  * [переменную среды](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`.

Обратите внимание на следующий файл *appsettings.json* , используемый в веб-приложении ASP.NET Core:

[!code-json[](~/fundamentals/configuration/index/samples_snippets/5.x/appsettings.json?highlight=2-8)]

Если предыдущая выделенная разметка используется в веб-приложении ASP.NET Core ***и*** приложение запускается в командной строке со следующей конфигурацией межсерверной конечной точки:

`dotnet run --urls="https://localhost:7777"`

Kestrel привязывается к конечной точке, настроенной специально для Kestrel в файле *appsettings.json* (`https://localhost:9999`, а не `https://localhost:7777`).

Обратите внимание на конечную точку Kestrel, настроенную в качестве переменной среды:

`set Kestrel__Endpoints__Https__Url=https://localhost:8888`

В предыдущей переменной среды `Https` — имя конкретной конечной точки Kestrel. В предыдущем файле *appsettings.json* также определяется конечная точка Kestrel с именем `Https`. По [умолчанию](#default-configuration) переменные среды, использующие [поставщик конфигурации переменных среды](#evcp) считываются после *appsettings.* `Environment` *.json*. Поэтому для конечной точки `Https` используется предыдущая переменная среды.

::: moniker-end
::: moniker range=">= aspnetcore-3.0"

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

В приведенном выше коде, если `NumberKey` отсутствует в конфигурации, используется значение по умолчанию `99`.

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren и Exists

В следующих примерах мы рассмотрим следующий файл *MySubsection.json*:

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

Следующий код добавляет *MySubsection.json* к поставщикам конфигурации:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) возвращает подраздел конфигурации с указанным ключом подраздела.

Следующий код возвращает значения для `section1`:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

Следующий код возвращает значения для `section2:subsection0`:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

Значение `GetSection` никогда не возвращает значение `null`. Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.

Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется. <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.

### <a name="getchildren-and-exists"></a>GetChildren и Exists

Следующий код вызывает [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) и возвращает значения для `section2:subsection0`:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

Приведенный выше код вызывает [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) для проверки существования раздела:

 <a name="boa"></a>

## <a name="bind-an-array"></a>Привязка массива

[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) поддерживает привязку массивов к объектам с помощью массива индексов в ключах конфигурации. Любой формат массива, который предоставляет сегмент числового ключа способен привязать массив к массиву класса [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).

Рассмотрим *MyArray.json* из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

Следующий код добавляет *MyArray.json* к поставщикам конфигурации:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

Следующий код считывает конфигурацию и отображает значения:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Предыдущий код возвращает следующие выходные данные:

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

В предыдущих выходных данных индекс 3 имеет значение `value40`, соответствующее `"4": "value40",` в *MyArray.json*. Индексы привязанного массива непрерывны и не привязаны к индексу ключа конфигурации. Модуль привязки конфигурации не поддерживает привязку значений NULL или создание записей NULL в связанных объектах

Следующий код загружает конфигурацию `array:entries` с помощью метода расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

Следующий код считывает конфигурацию в `arrayDict` `Dictionary` и отображает значения:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Предыдущий код возвращает следующие выходные данные:

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`. При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы используются для выполнения итерации данных конфигурации при создании объекта. Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.

Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который считывает пару "ключ-значение" индекса &num;3. Рассмотрим следующий файл *Value3.json* из примера загрузки:

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

Следующий код включает конфигурацию для *Value3.json* и `arrayDict` `Dictionary`:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

Следующий код считывает предыдущую конфигурацию и отображает значения:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Предыдущий код возвращает следующие выходные данные:

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.

## <a name="custom-configuration-provider"></a>Поставщик пользовательской конфигурации

Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).

Поставщик имеет следующие характеристики.

* База данных в памяти EF используется для демонстрационных целей. Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.
* Поставщик считывает таблицу базы данных в конфигурации при запуске. Поставщик не запрашивает базу данных для каждого ключа.
* Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.

Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.

*Models/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. Поставщик конфигурации инициализирует пустую базу данных. Поскольку [конфигурационные ключи не учитывают регистр](#keys), словарь, используемый для инициализации базы данных, создается с помощью функции сравнения без учета регистра ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.

*Extensions/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a>Доступ к конфигурации во время запуска

В следующем коде отображаются данные конфигурации в методах `Startup`:

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-razor-pages"></a>Конфигурация доступа в RazorPages

В следующем коде отображаются данные конфигурации в RazorPage:

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

В следующем коде `MyOptions` добавляется в контейнер службы с помощью интерфейса <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> и привязывается к конфигурации:

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

В следующей разметке для разрешения и вывода значений параметров используется директива Razor [`@inject`](xref:mvc/views/razor#inject).

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a>Доступ к конфигурации в файле представления MVC

В следующем коде отображаются данные конфигурации в представлении MVC:

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a>Настройка параметров с помощью делегата

Параметры, настроенные в делегате, переопределяют значения, заданные в поставщиках конфигурации.

Настройка параметров с помощью делегата демонстрируется в примере 2 в примере приложения.

В приведенном ниже коде в контейнер службы добавляется служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. Она использует делегат для настройки значений для `MyOptions`:

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

Следующий код отображает значения параметров:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

В предыдущем примере значения `Option1` и `Option2` задаются в файле *appsettings.json* , а затем переопределяются настроенным делегатом.

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a>Конфигурация узла и приложения

Перед настройкой и запуском приложения настройте и запустите *узел*. Узел отвечает за запуск приложения и управление временем существования. Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе. Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения. Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.

<a name="dhc"></a>

## <a name="default-host-configuration"></a>Конфигурация узла по умолчанию

Подробные сведения о конфигурации по умолчанию при использовании [веб-узла](xref:fundamentals/host/web-host) см. в [разделе о версии ASP.NET Core 2.2 в этой статье](?view=aspnetcore-2.2&preserve-view=true).

* Существуют следующие способы предоставления конфигурации узла.
  * Переменные среды с префиксом `DOTNET_` (например, `DOTNET_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables). Префикс (`DOTNET_`) исключается при загрузке пар "ключ — значение" конфигурации.
  * Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).
* Устанавливается конфигурация веб-узла по умолчанию (`ConfigureWebHostDefaults`):
  * Kestrel используется в качестве веб-сервера и настраивается с помощью поставщиков конфигурации приложения.
  * Добавьте ПО промежуточного слоя фильтрации узлов.
  * Если переменной среды `ASPNETCORE_FORWARDEDHEADERS_ENABLED` присвоено значение `true`, добавьте ПО промежуточного слоя перенаправления заголовков.
  * Включите интеграцию служб IIS.

## <a name="other-configuration"></a>Другая конфигурация

Этот раздел относится только к *конфигурации приложений*. Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.

* *launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:
  * В <xref:fundamentals/environments#development>.
  * В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.
* *web.config* — это файл конфигурации сервера, описанный в следующих источниках:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Переменные среды, заданные в *launchSettings.json*, переопределяют переменные, заданные в системной среде.

См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.

## <a name="add-configuration-from-an-external-assembly"></a>Добавление конфигурации из внешней сборки

Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`. Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Исходный код конфигурации](https://github.com/dotnet/runtime/tree/main/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Конфигурация приложения в ASP.NET Core основана на парах "ключ — значение", установленных *поставщиками конфигурации*. Поставщики конфигурации получают данные конфигурации в парах "ключ — значение" из различных источников:

* Хранилище ключей Azure;
* конфигурация приложения Azure;
* аргументов командной строки;
* а также пользовательские поставщики (устанавливаемые или создаваемые).
* справочных файлов;
* Переменные среды
* объектов .NET в памяти;
* файлов параметров;

Пакеты конфигурации для распространенных вариантов провайдеров конфигурации ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) включаются в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

В приведенных ниже и представленных в образце приложения примерах кода используется пространство имен <xref:Microsoft.Extensions.Configuration>:

```csharp
using Microsoft.Extensions.Configuration;
```

*Шаблон параметров* является расширением конфигурации основных понятий, описанных в этом разделе. Параметры используют классы для представления групп связанных настроек. Для получения дополнительной информации см. <xref:fundamentals/configuration/options>.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="host-versus-app-configuration"></a>Конфигурация узла и приложения

Перед настройкой и запуском приложения настройте и запустите *узел*. Узел отвечает за запуск приложения и управление временем существования. Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе. Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения. Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.

## <a name="other-configuration"></a>Другая конфигурация

Этот раздел относится только к *конфигурации приложений*. Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.

* *launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:
  * В <xref:fundamentals/environments#development>.
  * В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.
* *web.config* — это файл конфигурации сервера, описанный в следующих источниках:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.

## <a name="default-configuration"></a>Конфигурация по умолчанию

Веб-приложения на основе шаблонов [dotnet new](/dotnet/core/tools/dotnet-new) ASP.NET Core вызывают <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> при создании узла. `CreateDefaultBuilder` предоставляет конфигурацию по умолчанию для приложения в следующем порядке:

Приведенные ниже сведения относятся к приложениям, использующим [веб-узел](xref:fundamentals/host/web-host). Подробные сведения о конфигурации по умолчанию при использовании [универсального узла](xref:fundamentals/host/generic-host) см. в [последней версии этой статьи](xref:fundamentals/configuration/index).

* Существуют следующие способы предоставления конфигурации узла.
  * Переменные среды с префиксом `ASPNETCORE_` (например, `ASPNETCORE_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider). Префикс (`ASPNETCORE_`) исключается при загрузке пар "ключ — значение" конфигурации.
  * Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).
* Конфигурация приложения предоставляется из следующих ресурсов:
  * Файл *appsettings.json* , использующий [поставщик конфигурации файлов](#file-configuration-provider).
  * Файл *appsettings.{Environment}.json*, использующий [поставщик конфигурации файлов](#file-configuration-provider).
  * [секреты пользователя](xref:security/app-secrets), когда приложение выполняется в среде `Development` с использованием начальных сборок;
  * Переменные среды, использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).
  * Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).

## <a name="security"></a>Безопасность

Применяйте описанные ниже методики для защиты конфиденциальных данных конфигурации.

* Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.
* Не используйте секреты рабочей среды в средах разработки и тестирования.
* Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.

Дополнительные сведения см. в следующих разделах:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>. Содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных. Диспетчер секретов использует поставщик конфигурации файла для хранения конфиденциальных данных пользователя в файле JSON в локальной системе. Поставщик конфигурации файлов описан ниже в этом разделе.

В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core. Для получения дополнительной информации см. <xref:security/key-vault-configuration>.

## <a name="hierarchical-configuration-data"></a>Иерархическая модель конфигурации

API конфигурации способен поддерживать иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.

В следующем файле JSON существуют четыре ключа в структурированной иерархии двух разделов.

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

При считывании файла в конфигурацию для сохранения исходной иерархической структуры данных источника конфигурации создаются уникальные ключи. Разделы и ключи преобразовываются в плоскую структуру с использованием двоеточия (`:`) для сохранения исходной структуры.

* section0:key0
* section0:key1
* section1:key0
* section1:key1

Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации. Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection-getchildren-and-exists).

## <a name="conventions"></a>Соглашения

### <a name="sources-and-providers"></a>Источники и поставщики

При запуске приложения источники конфигурации считываются в порядке, в котором были указаны их поставщики конфигурации.

Поставщики конфигурации, которые реализуют обнаружение изменений, имеют возможность перезагрузить конфигурацию при изменении базовых параметров. Так, поставщик файла конфигурации (подробнее о нем далее в этой статье) и [поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) реализуют обнаружение изменений.

Объект <xref:Microsoft.Extensions.Configuration.IConfiguration> доступен в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) приложения. <xref:Microsoft.Extensions.Configuration.IConfiguration> можно внедрить в <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Razor Pages или <xref:Microsoft.AspNetCore.Mvc.Controller> MVC, чтобы получить конфигурацию для класса.

В следующих примерах поле `_config` используется для доступа к значениям конфигурации:

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

Поставщики конфигурации не могут использовать контейнер DI, так как он недоступен при настройке узла.

### <a name="keys"></a>Клавиши

В ключах конфигурации приняты следующие соглашения.

* В ключах не учитывается регистр символов. Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.
* Если значение для одного и того же ключа установлено одним и тем же или разными поставщиками конфигурации, последним значением, установленным на ключе, является используемое значение. Дополнительные сведения о повторяющихся ключах JSON см. в [этой проблеме на GitHub](https://github.com/dotnet/extensions/issues/2381).
* Иерархические ключи
  * При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.
  * В переменных среды разделитель-двоеточие может не работать на всех платформах. Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие.
  * В хранилище ключей Azure иерархические ключи используют `--` (два дефиса) в качестве разделителя. Чтобы заменить дефисы двоеточием, при загрузке секретов в конфигурацию приложения напишите код.
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации. Привязка массива описана в разделе [Привязка массива к классу](#bind-an-array-to-a-class).

### <a name="values"></a>Значения

В значениях конфигурации учитываются следующие соглашения.

* Значения являются строками.
* Значение NULL не может храниться в конфигурации или быть привязанным к объектам.

## <a name="providers"></a>Поставщики

В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.

| Поставщик | Предоставляет конфигурацию из &hellip; |
| -------- | ----------------------------------- |
| [Поставщик конфигурации хранилища ключей Azure](xref:security/key-vault-configuration) (раздел *Безопасность*) | Хранилище ключей Azure; |
| [Поставщик конфигурации приложений Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (документация Azure) | конфигурация приложения Azure; |
| [Поставщик конфигурации командной строки](#command-line-configuration-provider) | Параметры командной строки |
| [Поставщик пользовательской конфигурации](#custom-configuration-provider) | Источник пользователя |
| [Поставщик конфигурации переменных среды](#environment-variables-configuration-provider) | Переменные среды |
| [Поставщик пользовательской конфигурации](#file-configuration-provider) | Файлы (INI, JSON, XML) |
| [Поставщик конфигурации ключа для каждого файла](#key-per-file-configuration-provider) | Справочные файлы |
| [Поставщик конфигурации памяти](#memory-configuration-provider) | Коллекции оперативной памяти |
| [Секреты пользователей](xref:security/app-secrets) (раздел *Безопасность*) | Файл в каталоге профиля пользователя |

Источники конфигурации считываются в том порядке, в котором при запуске указываются их поставщики конфигурации. Поставщики конфигурации в этом разделе описаны в алфавитном порядке, а не в необходимом вам порядке в коде. Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.

Типичная последовательность поставщиков конфигурации.

1. Файлы ( *appsettings.json* , *appsettings.{Environment}.json*, где `{Environment}` — это текущая среда размещения приложения)
1. [Azure Key Vault](xref:security/key-vault-configuration);
1. [Секреты пользователя](xref:security/app-secrets) (только в среде разработки)
1. Переменные среды
1. аргументов командной строки;

Общепринятой практикой является размещение поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.

Предыдущая последовательность поставщиков используется при инициализации нового построителя узла с помощью `CreateDefaultBuilder`. Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).

## <a name="configure-the-host-builder-with-useconfiguration"></a>Настройка построителя узла с помощью UseConfiguration

Чтобы настроить построитель узла, вызовите <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> в построителе узла с конфигурацией.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать поставщики конфигурации приложения в дополнение к тем, которые автоматически добавлены `CreateDefaultBuilder`.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>Переопределение предыдущей конфигурации с помощью аргументов командной строки

Чтобы предоставить конфигурацию приложения, которую можно переопределить с помощью аргументов командной строки, вызовите метод `AddCommandLine` последним:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>Удаление поставщиков, добавленных CreateDefaultBuilder

Чтобы удалить поставщиков, добавленных `CreateDefaultBuilder`, сначала вызовите [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) в [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources):

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>Использование конфигурации во время запуска приложения

Конфигурация, предоставленная приложению в `ConfigureAppConfiguration`, доступна во время запуска приложения, включая `Startup.ConfigureServices`. Дополнительные сведения см. в разделе [Доступ к конфигурации во время запуска](#access-configuration-during-startup).

## <a name="command-line-configuration-provider"></a>Поставщик конфигурации командной строки

<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пары "ключ — значение" аргумента командной строки в среде выполнения.

Чтобы активировать конфигурацию командной строки, вызывается метод расширения <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> для экземпляра <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

`AddCommandLine` вызывается автоматически при вызове `CreateDefaultBuilder(string [])`. Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).

`CreateDefaultBuilder` также загружает следующее:

* дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;
* [Секреты пользователя](xref:security/app-secrets) в среде разработки.
* Переменные среды.

`CreateDefaultBuilder` добавляет последним поставщика конфигурации командной строки. Аргументы командной строки передаются в набор конфигурации переопределения среды выполнения, установленной другими поставщиками.

`CreateDefaultBuilder` действует, когда создается узел. Поэтому конфигурация командной строки, активированная с помощью `CreateDefaultBuilder`, может повлиять на настройку узла.

Для приложений на основе шаблонов ASP.NET Core метод `AddCommandLine` уже был вызван методом `CreateDefaultBuilder`. Чтобы добавить дополнительные поставщики конфигурации и обеспечить возможность переопределения конфигурации от этих поставщиков с помощью аргументов командной строки, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration` и вызовите `AddCommandLine` в последнюю очередь.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**Пример**

Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

1. Откройте командную строку в каталоге проекта.
1. Поставьте аргумент командной строки в команду `dotnet run`: `dotnet run CommandLineKey=CommandLineValue`.
1. После запуска приложения откройте браузер в приложении по адресу `http://localhost:5000`.
1. Обратите внимание, что вывод содержит пару "ключ — значение" для аргумента командной строки конфигурации, предоставленного для `dotnet run`.

### <a name="arguments"></a>Аргументы

Значение должно соответствовать знаку равенства (`=`), или ключ должен иметь префикс (`--` или `/`), когда значение следует за пробелом. Значение не требуется, если используется знак равенства (например, `CommandLineKey=`).

| Префикс ключа               | Пример                                                |
| ------------------------ | ------------------------------------------------------ |
| Без префикса                | `CommandLineKey1=value1`                               |
| Два дефиса (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Прямая косая черта (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

В рамках одной и той же команды не смешивайте пары "ключ — значение" аргумента командной строки, которые используют знак равенства, с парами "ключ — значение", которые используют пробел.

Примеры команд.

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>Сопоставления переключений

Сопоставление параметров позволяет указать логику замены имен ключей. Когда вручную создается конфигурация с помощью <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, методу <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> можно предоставить словарь сопоставления переключений.

В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки. Если ключ в командной строке находится в словаре, значение словаря (замена ключа) передается обратно, чтобы установить пару "ключ — значение" в конфигурацию приложения. Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).

Правила ключей из словаря сопоставления переключений:

* Переключения должны начинаться с дефиса (`-`) или двойного дефиса (`--`).
* Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.

Создайте словарь сопоставления переключений. В следующем примере создаются два сопоставления переключений:

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

При сборке узла вызовите `AddCommandLine` со словарем сопоставлений переключений:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны. Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные переключения, и нет возможности передать словарь сопоставления переключений в `CreateDefaultBuilder`. Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.

Созданный словарь сопоставлений переключений содержит данные, показанные в следующей таблице.

| Ключ       | Значение             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

Если ключи сопоставления переключений используются при запуске приложения, конфигурация принимает значение конфигурации в ключе, предоставленном в словаре.

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

После выполнения предыдущей команды конфигурация содержит значения, показанные в следующей таблице.

| Ключ               | Значение    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>Поставщик конфигурации переменных среды

<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пары "ключ — значение" переменной среды выполнения.

Чтобы активировать конфигурацию переменных среды, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Служба приложений Azure](https://azure.microsoft.com/services/app-service/) позволяет задать переменные среды на портале Azure, который может переопределить конфигурацию приложения, используя поставщик конфигурации переменных среды. Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

`AddEnvironmentVariables` используется для загрузки переменных среды, имеющих префикс `ASPNETCORE_`, для [конфигурации узла](#host-versus-app-configuration) при инициализации нового построителя узла с [веб-узлом](xref:fundamentals/host/web-host) и вызове `CreateDefaultBuilder`. Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).

`CreateDefaultBuilder` также загружает следующее:

* конфигурация приложения на основе переменных среды без префикса путем вызова `AddEnvironmentVariables` без префикса;
* дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;
* [Секреты пользователя](xref:security/app-secrets) в среде разработки.
* аргументы командной строки.

Поставщик конфигурации переменных среды вызывается после выполнения настройки с помощью секретов пользователя и файлов *appsettings*. Вызов поставщика в этой позиции разрешает чтение переменных среды выполнения, чтобы переопределить конфигурацию, заданную секретом пользователя и файлом *appsettings*.

Чтобы добавить конфигурацию приложения из дополнительных переменных среды, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration`, а затем вызовите `AddEnvironmentVariables` с префиксом:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

Вызовите `AddEnvironmentVariables` последним, чтобы разрешить переменным среды с заданным префиксом переопределять значения от других поставщиков.

**Пример**

Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов `AddEnvironmentVariables`.

1. Выполните пример приложения. Откройте в приложении браузер с адресом `http://localhost:5000`.
1. Обратите внимание, что выходные данные содержат пару "ключ — значение" для переменной среды `ENVIRONMENT`. Значение отражает среду, в которой выполняется приложение, обычно при локальном запуске это `Development`.

Чтобы список переменных среды, отображаемый приложением, был коротким, приложение фильтрует переменные среды. См. пример файла *Pages/Index.cshtml.cs* приложения.

Чтобы просмотреть все переменные среды, доступные для приложения, измените значение `FilteredConfiguration` в *Pages/Index.cshtml.cs* на следующее:

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Префиксы

Переменные среды, которые загружаются в конфигурацию приложения, фильтруются при добавлении префикса к методу `AddEnvironmentVariables`. Например, чтобы отфильтровать переменные среды по префиксу `CUSTOM_`, введите префикс поставщику конфигурации:

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

Префикс отделяется при создании пары конфигурации "ключ — значение".

При создании построителя узла конфигурация узла предоставляется переменными среды. Дополнительные сведения о префиксе, используемом для этих переменных среды, см. в разделе [Конфигурация по умолчанию](#default-configuration).

**Префиксы строк подключения**

API конфигурации имеет специальные правила обработки для четырех строк подключения переменных среды, связанных с настройкой строк подключения Azure для среды приложения. Если префикс не указан в `AddEnvironmentVariables`, переменные среды с префиксами, указанными в таблице, загружаются в приложение.

| Префикс строки подключения | Поставщик |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Поставщик пользователя |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [База данных SQL Azure](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.

* Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).
* Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).

| Ключ переменной среды | Преобразованный ключ конфигурации | Запись конфигурации поставщика                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Запись конфигурации не создана.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Ключ: `ConnectionStrings:{KEY}_ProviderName`:<br>Значение: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Ключ: `ConnectionStrings:{KEY}_ProviderName`:<br>Значение: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Ключ: `ConnectionStrings:{KEY}_ProviderName`:<br>Значение: `System.Data.SqlClient`  |

**Пример**

На сервере создается пользовательская переменная среды строки подключения:

* Имя: `CUSTOMCONNSTR_ReleaseDB`
* Значение: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

Если `IConfiguration` вставляется и назначается полю с именем `_config`, считайте значение:

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>Поставщик пользовательской конфигурации

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы. Следующие поставщики конфигурации предназначены для определенных типов файлов:

* [Поставщик конфигурации INI](#ini-configuration-provider)
* [Поставщик конфигурации JSON](#json-configuration-provider)
* [Поставщик конфигурации XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Поставщик конфигурации INI

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.

Чтобы активировать конфигурацию INI-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Двоеточие можно использовать как разделитель раздела в конфигурации файла INI.

Перегрузки позволяют указать следующее.

* Файл является обязательным или нет.
* Будет ли перезагружена конфигурация, если файл изменится.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.

Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

Общий пример конфигурации INI-файла.

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.

* section0:key0
* section0:key1
* section1:subsection:key
* section2:subsection0:key
* section2:subsection1:key

### <a name="json-configuration-provider"></a>Поставщик конфигурации JSON

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ — значение" JSON-файла в среде выполнения.

Чтобы активировать конфигурацию JSON-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Перегрузки позволяют указать следующее.

* Файл является обязательным или нет.
* Будет ли перезагружена конфигурация, если файл изменится.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.

`AddJsonFile` автоматически вызывается дважды при инициализации нового построителя узла с `CreateDefaultBuilder`. Метод вызывается для загрузки конфигурации из:

* *appsettings.json* : Этот файл считывается первым. Версия файла среды может переопределить значения, предоставленные файлом *appsettings.json* .
* *appsettings.{Environment}.json*: Версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).

Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).

`CreateDefaultBuilder` также загружает следующее:

* Переменные среды.
* [Секреты пользователя](xref:security/app-secrets) в среде разработки.
* аргументы командной строки.

Сначала устанавливается поставщик конфигурации JSON-файлов. Таким образом, секреты пользователя, переменные среды и аргументы командной строки переопределят конфигурацию, заданную файлами *appsettings*.

Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать конфигурацию приложения для файлов, отличных от *appsettings.json* и *appsettings.{Environment}.json*.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**Пример**

Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает два вызова `AddJsonFile`:

* Первый вызов `AddJsonFile` загружает конфигурацию из файла *appsettings.json* :

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* Второй вызов `AddJsonFile` загружает конфигурацию из *appsettings.{Environment}.json*. Для *appsettings.Development.json* в примере приложения загружается следующий файл:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. Выполните пример приложения. Откройте в приложении браузер с адресом `http://localhost:5000`.
1. Выходные данные содержат пары "ключ-значение" для конфигурации в зависимости от среды приложения. Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Debug` при запуске приложения в среде разработки.
1. Запустите пример приложения еще раз в рабочей среде:
   1. Откройте файл *Properties/launchSettings.json*.
   1. В профиле `ConfigurationSample` измените значение переменной среды `ASPNETCORE_ENVIRONMENT` на `Production`.
   1. Сохраните файл и запустите приложение с помощью `dotnet run` в командной оболочке.
1. Параметры в *appsettings.Development.json* больше не переопределяют параметры в *appsettings.json* . Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Warning`.

### <a name="xml-configuration-provider"></a>Поставщик конфигурации XML

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.

Чтобы активировать конфигурацию XML-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Перегрузки позволяют указать следующее.

* Файл является обязательным или нет.
* Будет ли перезагружена конфигурация, если файл изменится.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.

Корневой узел файла конфигурации не учитывается при создании пар "ключ — значение" конфигурации. Не указывайте в файле Document Type Definition (определение типа документа, DTD) или пространство имен.

Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

XML-файлы конфигурации могут использовать имена отдельных элементов для повторяющихся разделов.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.

* section0:key0
* section0:key1
* section1:key0
* section1:key1

Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.

* section:section0:key:key0
* section:section0:key:key1
* section:section1:key:key0
* section:section1:key:key1

Атрибуты можно использовать для предоставления значений.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.

* key:attribute
* section:key:attribute

## <a name="key-per-file-configuration-provider"></a>Поставщик конфигурации ключа для каждого файла

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации. Ключ является именем файла. Значение содержит содержимое файла. Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.

Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>. Значение параметра `directoryPath` должно быть абсолютным путем к файлам.

Перегрузки позволяют указать следующее.

* `Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.
* Обязательно ли указывать каталог и путь к каталогу.

Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов. Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.

Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>Поставщик конфигурации памяти

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.

Чтобы активировать конфигурацию коллекции в памяти, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Поставщик конфигурации может инициализироваться значением `IEnumerable<KeyValuePair<String,String>>`.

Чтобы указать конфигурацию приложения, при сборке узла вызовите `ConfigureAppConfiguration`.

В следующем примере создается словарь конфигурации:

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

Словарь используется с вызовом `AddInMemoryCollection` для предоставления конфигурации:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип, не являющийся коллекцией. Перегрузка принимает значение по умолчанию.

В следующем примере происходит следующее:

* Из конфигурации извлекается строковое значение с ключом `NumberKey`. Если `NumberKey` отсутствует в ключах конфигурации, используется значение по умолчанию `99`.
* Значение получает тип `int`.
* Значение сохраняется в свойстве `NumberConfig` для использования на странице.

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren и Exists

В следующих примерах рассмотрим файл JSON. Четыре ключа находятся в двух разделах, один из которых содержит пару из подразделов.

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

Когда файл считывается в конфигурацию, для сохранения значений конфигурации создаются следующие уникальные иерархические ключи.

* section0:key0
* section0:key1
* section1:key0
* section1:key1
* section2:subsection0:key0
* section2:subsection0:key1
* section2:subsection1:key0
* section2:subsection1:key1

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) извлекает подраздел конфигурации с указанным ключом подраздела.

Чтобы вернуть <xref:Microsoft.Extensions.Configuration.IConfigurationSection>, содержащий только пары "ключ — значение" в `section1`, вызовите `GetSection` и укажите имя раздела.

```csharp
var configSection = _config.GetSection("section1");
```

`configSection` не содержит значение, только ключ и путь.

Аналогично, чтобы получить значения для ключей в `section2:subsection0`, вызовите `GetSection` и укажите путь к разделу.

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

Значение `GetSection` никогда не возвращает значение `null`. Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.

Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется. <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.

### <a name="getchildren"></a>GetChildren

Вызов [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) в `section2` получает значение `IEnumerable<IConfigurationSection>`, которое включает:

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a>Exists

Используйте [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*), чтобы определить, существует ли раздел конфигурации.

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

Учитывая данные примера, `sectionExists` является `false`, потому что в данных конфигурации нет `section2:subsection2`.

## <a name="bind-to-an-object-graph"></a>Привязка к графу объектов

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> способна связывать весь граф объекта POCO. Как и при привязке простого объекта, привязываются только открытые свойства чтения и записи.

Образец содержит модель `TvShow`, в графе объектов которого находятся классы `Metadata` и `Actors` (*Модели/TvShow.cs*).

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

В примере приложения есть файл *tvshow.xml*, содержащий данные конфигурации.

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

Конфигурация привязана ко всему ​​графу объектов `TvShow` с помощью метода `Bind`. Привязанный экземпляр присваивается свойству для подготовки к просмотру.

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) привязывает и возвращает указанный тип. Метод `Get<T>` может быть более удобным, чем использование `Bind`. Следующий код показывает, как использовать `Get<T>` с предыдущим примером:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>Привязка массива к классу

*Пример приложения демонстрирует концепции, описанные в этом разделе.*

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации. Любой формат массива, который предоставляет сегмент числового ключа (`:0:`, `:1:`, &hellip; `:{n}:`), способен привязать массив к массиву класса POCO.

> [!NOTE]
> Привязка предоставляется соглашением. Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.

**Обработка массива в оперативной памяти**

Рассмотрите ключи и значения конфигурации, приведенные в следующей таблице.

| Ключ             | Значение  |
| :-------------: | :----: |
| array:entries:0 | value0 |
| array:entries:1 | value1 |
| array:entries:2 | value2 |
| array:entries:4 | value4 |
| array:entries:5 | value5 |

Эти ключи и значения загружаются в пример приложения с помощью поставщика конфигурации памяти.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

Массив пропускает значения для индекса &num;3. Связующее свойство конфигурации не способно связывать нулевые значения или создавать нулевые записи в связанных объектах, что становится ясным в тот момент, когда демонстрируется результат привязки этого массива к объекту.

В примере приложения класс POCO доступен для хранения привязанных данных конфигурации.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

Данные конфигурации, связанные с объектом.

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

Синтаксис [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) также может использоваться для получения более компактного кода:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

Связанный объект, экземпляр `ArrayExample`, получает данные массива из конфигурации.

| Индекс `ArrayExample.Entries` | Значение `ArrayExample.Entries` |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value4                       |
| 4                            | value5                       |

Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`. При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы просто используются для выполнения итерации данных конфигурации при создании объекта. Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.

Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который создает правильную пару "ключ — значение" в конфигурации. Если в образец включен дополнительный поставщик конфигурации JSON с отсутствующей парой "ключ — значение", то `ArrayExample.Entries` подойдет полному массиву конфигурации.

*missing_value.json*:

```json
{
  "array:entries:3": "value3"
}
```

В `ConfigureAppConfiguration`:

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

Пары "ключ — значение", показанные в таблице, загружаются в конфигурации.

| Ключ             | Значение  |
| :-------------: | :----: |
| array:entries:3 | value3 |

Если экземпляр класса `ArrayExample` связан после того, как поставщик конфигурации JSON включает запись для индекса &num;3, то массив `ArrayExample.Entries` включит значение.

| Индекс `ArrayExample.Entries` | Значение `ArrayExample.Entries` |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**Обработка массива JSON**

Если JSON-файл содержит массив, ключи конфигурации будут созданы для элементов массива с индексом раздела, начиная с нуля. В следующем файле конфигурации `subsection` — это массив.

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

Поставщик конфигурации JSON считывает данные конфигурации в следующие пары "ключ — значение".

| Ключ                     | Значение  |
| ----------------------- | :----: |
| json_array:key          | valueA |
| json_array:subsection:0 | valueB |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | valueD |

В примере приложения для привязки пар "ключ — значение" конфигурации доступен следующий класс POCO.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

После выполнения привязки `JsonArrayExample.Key` содержит значение `valueA`. Подраздел значения хранится в свойстве массива POCO `Subsection`.

| Индекс `JsonArrayExample.Subsection` | Значение `JsonArrayExample.Subsection` |
| :---------------------------------: | :---------------------------------: |
| 0                                   | valueB                              |
| 1                                   | valueC                              |
| 2                                   | valueD                              |

## <a name="custom-configuration-provider"></a>Поставщик пользовательской конфигурации

Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).

Поставщик имеет следующие характеристики.

* База данных в памяти EF используется для демонстрационных целей. Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.
* Поставщик считывает таблицу базы данных в конфигурации при запуске. Поставщик не запрашивает базу данных для каждого ключа.
* Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.

Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.

*Models/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. Поставщик конфигурации инициализирует пустую базу данных.

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.

*Extensions/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a>Доступ к конфигурации во время запуска

Внесите значение `IConfiguration` в конструктор `Startup` для доступа к значениям конфигурации в `Startup.ConfigureServices`. Чтобы получить доступ к конфигурации в `Startup.Configure`, либо добавьте значение `IConfiguration` непосредственно в метод, либо используйте экземпляр из конструктора.

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>Настройте доступ на странице Razor Pages или в представлении MVC

Для доступа к параметрам конфигурации на странице Razor Pages или в представлении MVC добавьте [использование директивы](xref:mvc/views/razor#using) ([Справочник по C#: использование директивы](/dotnet/csharp/language-reference/keywords/using-directive)) для [пространства имен Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) и вставьте <xref:Microsoft.Extensions.Configuration.IConfiguration> на страницу или в представление.

На странице Razor Pages:

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

В представлении MVC

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a>Добавление конфигурации из внешней сборки

Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`. Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/configuration/options>

::: moniker-end
