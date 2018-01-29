---
uid: web-forms/overview/deployment/web-deployment-in-the-enterprise/understanding-the-project-file
title: "Основные сведения о файле проекта | Документы Microsoft"
author: jrjlee
description: "Файлы проекта Microsoft Build Engine (MSBuild) находятся в основе процесса построения и развертывания. В начале данной статьи приведены общие сведения о MSBuild..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 07978d9d-341c-4524-bcba-62976f390f77
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/web-deployment-in-the-enterprise/understanding-the-project-file
msc.type: authoredcontent
ms.openlocfilehash: 09c3793e9cdddb7c42cf966f2d079245f441540c
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
<a name="understanding-the-project-file"></a>Основные сведения о файле проекта
====================
по [Джейсон Lee](https://github.com/jrjlee)

[Скачать PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Файлы проекта Microsoft Build Engine (MSBuild) находятся в основе процесса построения и развертывания. В начале данной статьи приведены общие сведения о MSBuild и файл проекта. Он описывает ключевые компоненты, которые вы будете попадаться при работе с файлами проекта, и они работают, минуя пример того, как можно использовать файлы проекта для развертывания реальных приложений.
> 
> Что вы узнаете следующее.
> 
> - Как MSBuild использует файлы проекта MSBuild для построения проектов.
> - Как MSBuild интегрируется с технологиями развертывания, таких как средство Internet Information Services (IIS) веб-развертывания (Web Deploy).
> - Как понять основные компоненты файла проекта.
> - Использование файлов проекта для построения и развертывания сложных приложений.


## <a name="msbuild-and-the-project-file"></a>MSBuild и файл проекта

При создании и создавать решения в Visual Studio, Visual Studio использует MSBuild для построения каждого проекта в решении. Каждый проект Visual Studio включает файл проекта MSBuild с расширением, отражающее тип проекта & #x 2014; например, проект C# (.csproj), Visual Basic.NET проекта (.vbproj) или проект базы данных (DBPROJ). Чтобы построить проект, MSBuild необходимо обработать файл проекта, связанные с проектом. Файл проекта является XML-документ, который содержит все сведения и инструкции, что MSBuild требуется, чтобы построить проект, как содержимое для включения требования к платформе, сведения об управлении версиями, веб-сервере или параметры сервера баз данных и задачи, которые должны быть выполнены.

Файлы проекта MSBuild основаны на [схемы MSBuild XML](https://msdn.microsoft.com/library/5dy88c2e.aspx), и в результате процессу сборки не полностью открыть и прозрачным. Кроме того не требуется установить Visual Studio, чтобы использовать модуль MSBuild & #x 2014; исполняемому файлу MSBuild.exe является частью .NET Framework, и его можно запустить из командной строки. Разработчик может создать собственные файлы проекта MSBuild, с помощью схемы MSBuild XML, чтобы применить для сложных и детально контролировать как построение и развертывание проектов. Эти файлы в настраиваемом проекте работать точно так же, как файлы проекта, Visual Studio автоматически создает.

> [!NOTE]
> Можно также использовать файлы проекта MSBuild со службой Team Build в Team Foundation Server (TFS). Например файлы проекта в сценариях непрерывной интеграции (CI) можно использовать для автоматизации развертывания в тестовой среде, при возврате нового кода. Дополнительные сведения см. в разделе [настройки Team Foundation Server для автоматического развертывания веб-](../configuring-team-foundation-server-for-web-deployment/configuring-team-foundation-server-for-web-deployment.md).


### <a name="project-file-naming-conventions"></a>Файл проекта, соглашения об именовании

При создании файлов проекта, можно использовать любое расширение файла, как вам удобно. Однако для упрощения решения другим пользователям понять, следует использовать эти общие соглашения:

- Используйте расширение ".proj" при создании файла проекта, который выполняет построение проектов.
- Используйте расширение .targets, при создании файла проекта для повторного использования для импорта в другие файлы проекта. Файлы с расширением TARGETS обычно не сборки ничего сами, они просто содержат инструкции, которые можно импортировать в файлы ".proj".

### <a name="integration-with-deployment-technologies"></a>Интеграция с технологиями развертывания

Если вы работали с проектами веб-приложений в Visual Studio 2010, такие как веб-приложений ASP.NET и веб-приложений ASP.NET MVC, это означает, что эти проекты содержат встроенную поддержку для упаковки и развертывания веб-приложения к целевой среде. **Свойства** страниц для этих проектов содержат **Пакет/Публикация веб-сайта** и **упаковка и публикация SQL** вкладок, которые можно использовать для настройки как компоненты вашего приложения упаковываются и развертываются. В следующем примере показано **Пакет/Публикация веб-сайта** вкладки:

![](understanding-the-project-file/_static/image1.png)

Базовая технология за эти возможности известен как Web публикации конвейера (WPP). WPP фактически переводит MSBuild и [веб-развертывания](https://go.microsoft.com/?linkid=9805122) вместе, чтобы предоставить полный процесс построения, пакета и развертывания для веб-приложений.

Хорошо то, что можно воспользоваться преимуществами точки интеграции, предоставляемые WPP при создании файлов пользовательский проект для веб-проектов. В файле проекта, который служит для построения проектов, создать пакеты веб-развертывания и установки этих пакетов на удаленных серверах через один файл проекта и за один вызов MSBuild может включать инструкции по развертыванию. Также можно вызывать другие исполняемые объекты как часть процесса построения. Например можно запустить средство командной строки VSDBCMD.exe, чтобы развернуть базу данных из файла схемы. В ходе этого раздела вы увидите, как можно воспользоваться преимуществами этих возможностей в соответствии с требованиями сценариев корпоративного развертывания.

> [!NOTE]
> Дополнительные сведения о том, как работает процесс развертывания веб-приложений см. в разделе [Обзор развертывания веб-приложения ASP.NET проектов](https://msdn.microsoft.com/library/dd394698.aspx).


## <a name="the-anatomy-of-a-project-file"></a>Структура файла проекта

Перед просмотром процесс построения более подробно стоит уделить несколько минут для ознакомления с базовая структура файла проекта MSBuild. В этом разделе Обзор наиболее распространенных элементов, которые вы можете столкнуться при просмотреть, изменить или создать файл проекта. В частности вы узнаете следующее.

- Как использовать *свойства* управления переменными для процесса сборки.
- Как использовать *элементы* для идентификации входные данные для процесса сборки, такие как файлы кода.
- Как использовать *цели* и *задачи* для обеспечения выполнения инструкций на MSBuild, с помощью *свойства* и *элементы* определена в других местах файл проекта.

Это показано отношение между ключевые элементы в файл проекта MSBuild:

![](understanding-the-project-file/_static/image2.png)

### <a name="the-project-element"></a>Элемент проекта

[Проекта](https://msdn.microsoft.com/library/bcxfsh87.aspx) элемент является корневым элементом каждого файла проекта. В дополнение к определению схемы XML для файла проекта, **проекта** элемент может содержать атрибуты для указания точки входа в процесс построения. Например, в [диспетчера контактов образец решения](the-contact-manager-solution.md), *Publish.proj* файл задание запуска построения путем вызова целевой объект с именем **FullPublish**.


[!code-xml[Main](understanding-the-project-file/samples/sample1.xml)]


### <a name="properties-and-conditions"></a>Свойства и условия

Обычно файл проекта должен содержать большое количество разные части сведений для успешного построения и развертывания проектов. Эти компоненты могут включать имена серверов, строки подключения, учетные данные, конфигурации построения, исходный и конечный пути к файлам и прочую информацию, которую требуется включить для поддержки настройки. В файле проекта, свойства должны быть определены в рамках [PropertyGroup](https://msdn.microsoft.com/library/t4w159bs.aspx) элемента. Свойства MSBuild состоят из пары "ключ значение". В пределах **PropertyGroup** элемент, имя элемента определяет ключ свойства и содержимое элемента определяет значение свойства. Например, можно определить свойства с именем **ServerName** и **ConnectionString** для хранения статических серверных имя и строку соединения.


[!code-xml[Main](understanding-the-project-file/samples/sample2.xml)]


Чтобы получить значение свойства, используйте формат **$(***PropertyName***) ***.* Например, чтобы извлечь значение **ServerName** свойство, следует ввести:


[!code-powershell[Main](understanding-the-project-file/samples/sample3.ps1)]


> [!NOTE]
> Вы увидите примеры того, как и когда следует использовать значения свойств этого раздела.


Внедрение сведений о виде статических свойств в файле проекта не всегда идеально подход к управлению процесса построения. Множество сценариев полезно получить данные из других источников или расширить возможности пользователей для предоставления сведений из командной строки. MSBuild можно указать любое значение свойства в качестве параметра командной строки. Например, пользователь может предоставить значение для **ServerName** при выполнении MSBuild.exe из командной строки.


[!code-console[Main](understanding-the-project-file/samples/sample4.cmd)]


> [!NOTE]
> Дополнительные сведения о аргументы и параметры, которые можно использовать с помощью MSBuild.exe см. в разделе [Справочник по командной строке MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).


Для получения значений переменных среды и свойства встроенных проекта можно использовать один и тот же синтаксис свойств. Большое количество часто используемые свойства определяются автоматически, и можно использовать в файлах проекта, включая имя соответствующего параметра. Например, для получения текущей платформы проекта & #x 2014; например, **x86** или **AnyCpu**& #x 2014; может включать **$(Platform)** ссылку на свойство в файл проекта. Дополнительные сведения см. в разделе [макросы для команд и свойств построения](https://msdn.microsoft.com/library/c02as0cs.aspx), [общие свойства проектов MSBuild](https://msdn.microsoft.com/library/bb629394.aspx), и [зарезервированные свойства](https://msdn.microsoft.com/library/ms164309.aspx).

Свойства часто используются в сочетании с *условия*. Поддерживает большинство элементов MSBuild **условие** атрибут, который позволяет указать критерии, по которым MSBuild следует оценить элемента. Например рассмотрим определение свойств:


[!code-xml[Main](understanding-the-project-file/samples/sample5.xml)]


Когда MSBuild обрабатывает определение свойств, он сначала проверяет, является ли **$(OutputRoot)** доступно значение свойства. Если значением свойства является пустое & #x 2014; другими словами, пользователь не предоставляйте значение для этого свойства & #x 2014; условие **true** и свойство имеет значение **... \Publish\Out**. Если пользователь предоставил значение для этого свойства, условие **false** и не используется значение статического свойства.

Дополнительные сведения о различных способах, в котором можно указать условия, см. в разделе [условия MSBuild](https://msdn.microsoft.com/library/7szfhaft.aspx).

### <a name="items-and-item-groups"></a>Элементы и группы элементов

Одним из важных ролей файла проекта является определение входные данные для процесса построения. Обычно эти входные файлы & #x 2014; файлы кода, файлы конфигурации, командные файлы и другие файлы, которые необходимо обработать или Копировать как часть процесса построения. В схеме проекта MSBuild эти входные данные представлены в виде [элемент](https://msdn.microsoft.com/library/ms164283.aspx) элементов. В файле проекта элементы должны быть определены в [ItemGroup](https://msdn.microsoft.com/library/646dk05y.aspx) элемента. Так же, как **свойство** элементов, можно назвать **элемента** элемент Однако вам нравится. Тем не менее, необходимо указать **Include** атрибут, чтобы определить файл или подстановочный знак, который представляет элемент.


[!code-xml[Main](understanding-the-project-file/samples/sample6.xml)]


Путем указания нескольких **элемент** элементов с тем же именем, фактически создании именованного списка ресурсов. Для просмотра внутри одного из файлов проекта, которые Visual Studio создает является удобным способом, чтобы увидеть это в действии. Например *ContactManager.Mvc.csproj* файл в решение примера содержит большой объем группы элементов, каждый из которых несколько одинаковые имена **элемент** элементов.


[!code-xml[Main](understanding-the-project-file/samples/sample7.xml)]


В этом случае файл проекта рекомендует MSBuild для создания списков файлов, которые должны быть обработаны в одном способом & #x 2014; **ссылки** список включает сборки, которые должны быть выполнены для успешной сборки **Компиляции** список включает файлы кода, которые должны быть скомпилированы, и **содержимого** список содержит ресурсы, которые должны быть скопированы без изменений. Мы рассмотрим как ссылается на процесс построения и использует эти элементы ниже в этом разделе.

Элементы можно также включить [ItemMetadata](https://msdn.microsoft.com/library/ms164284.aspx) дочерних элементов. Это определяемые пользователем пары "ключ значение", по существу представляют свойства, относящиеся к этому элементу. Например, большой объем **компиляции** элементов в файле проекта включают **DependentUpon** дочерних элементов.


[!code-xml[Main](understanding-the-project-file/samples/sample8.xml)]


> [!NOTE]
> Помимо метаданных элемента, созданные пользователем все элементы, назначаются различные общие метаданные в момент создания. Дополнительные сведения см. в разделе [Стандартные метаданные элементов](https://msdn.microsoft.com/library/ms164313.aspx).


Можно создать **ItemGroup** элементы внутри корневого уровня **проекта** элемента или в пределах определенного **целевой** элементов. **ItemGroup** элементы также поддерживают **условие** атрибутов, которые можно настроить входные данные для процесса построения в соответствии с условия, такие как конфигурация проекта или платформы.

### <a name="targets-and-tasks"></a>Целевые объекты и задачи

В схеме MSBuild [задачи](https://msdn.microsoft.com/library/77f2hx1s.aspx) элемент представляет инструкции отдельные сборки (или задач). MSBuild включает множество заранее определенных заданий. Пример:

- **Копирования** задача копирует файлы в новое расположение.
- **Csc** задача вызывает компилятор Visual C#.
- **Vbc** задача вызывает компилятор Visual Basic.
- **Exec** задача выполняется указанной программы.
- **Сообщение** задача записывает сообщение в средство ведения журнала.

> [!NOTE]
> Полные сведения о задачи, доступные без дополнительной настройки в разделе [Справочник по задачам MSBuild](https://msdn.microsoft.com/library/7z253716.aspx). Дополнительные сведения о задачах, включая создание собственных пользовательских задач см. [задачи MSBuild](https://msdn.microsoft.com/library/ms171466.aspx).


Задачи должны всегда находиться в пределах [целевой](https://msdn.microsoft.com/library/t50z2hka.aspx) элементов. Объект **целевой** элемент представляет собой набор одну или несколько задач, которые выполняются последовательно и файл проекта может содержать несколько целевых объектов. Если вы хотите запустить задачу или набор задач, вызовите целевой объект, содержащий их. Например предположим, что имеется файл проекта простых, заносит в журнал сообщение.


[!code-xml[Main](understanding-the-project-file/samples/sample9.xml)]


Целевой объект из командной строки, можно вызвать с помощью **/t** для указания целевой объект.


[!code-console[Main](understanding-the-project-file/samples/sample10.cmd)]


Кроме того, можно добавить **DefaultTargets** атрибут **проекта** элемента, чтобы указать целевые объекты, которые вы хотите вызвать.


[!code-xml[Main](understanding-the-project-file/samples/sample11.xml)]


В этом случае не нужно для указания цели из командной строки. Можно просто указать файл проекта и будет вызывать MSBuild **FullPublish** целевой для вас.


[!code-console[Main](understanding-the-project-file/samples/sample12.cmd)]


Целевые объекты и задачи могут включать **условие** атрибуты. Таким образом вы можете исключить все целевые объекты или отдельные задачи выполнены определенные условия.

Вообще говоря при создании полезных задач и целевых объектов, необходимо будет ссылаться на свойства и элементы, которые определены в другом месте в файле проекта:

- Чтобы использовать значение свойства, введите **$(***PropertyName***)**, где *PropertyName* имя **свойства** элемента или имя параметр.
- Чтобы использовать элемент, введите **@(***ItemName***)**, где *ItemName* имя **элемента** элемент.

> [!NOTE]
> Помните, что при создании нескольких элементов с тем же именем, вы создаете список. Напротив Если создается несколько свойств с тем же именем, последнее значение свойства, предоставленные вами перезапишет все предыдущие свойства с таким же именем & #x 2014; свойство может содержать только одно значение.


Например, в *Publish.proj* файла в образец решения, взгляните на **BuildProjects** цели.


[!code-xml[Main](understanding-the-project-file/samples/sample13.xml)]


В этом образце можно отметить следующие факты:

- Если **BuildingInTeamBuild** параметр задан и имеет значение **true**, будет выполняться ни одна из задач в этом целевом объекте.
- Целевой объект содержит один экземпляр [MSBuild](https://msdn.microsoft.com/library/z7f65y0d.aspx) задачи. Эта задача позволяет создавать другие проекты MSBuild.
- **ProjectsToBuild** элемент, переданный задаче. Этот элемент может представлять собой список файлы проекта или решения, все определяется **ProjectsToBuild** элемента элементы внутри группы элементов. В этом случае **ProjectsToBuild** элемент связан с одиночного файла решения.

    [!code-xml[Main](understanding-the-project-file/samples/sample14.xml)]
- Свойство значения, переданные **MSBuild** задачи включают параметры с именем **OutputRoot** и **конфигурации**. Они задаются значения параметров, если они предоставляются или статическое свойство значения, если они не являются.

    [!code-xml[Main](understanding-the-project-file/samples/sample15.xml)]

Можно также видеть, что **MSBuild** задача вызывает целевой объект с именем **сборки**. Это один из нескольких встроенных целевых объектов, которые широко используются в файлы проекта Visual Studio и доступны для вас в файлах пользовательских проектов нравится **построения**, **Очистить**, **Перестроить**, и **публикации**. Дополнительные сведения об использовании целевых платформ и задач для управления процесса сборки, а также о **MSBuild** задач в частности, далее в этом разделе.

> [!NOTE]
> Дополнительные сведения о целевых объектах см. в разделе [целевые объекты MSBuild](https://msdn.microsoft.com/library/ms171462.aspx).


## <a name="splitting-project-files-to-support-multiple-environments"></a>Разбиение файлов проекта для поддержки нескольких сред

Предположим, что вы хотите иметь возможность развернуть решения для нескольких сред, таких как тестовые серверы, платформы промежуточной и рабочей среды. Конфигурация может значительно меняться от этих сред & #x 2014; не только с точки зрения имена серверов, строки подключения и т. д., а также потенциально с точки зрения учетных данных, параметры безопасности и множество других факторов. Если вам нужно регулярно это сделать, не действительно удобнее изменить несколько свойств в файле проекта, каждый раз при переключении в целевой среде. И идеальным решением для требуют бесконечные список значений свойств должны быть предоставлены для процесса построения.

К счастью является альтернативным. MSBuild можно разделить конфигурации сборки в нескольких файлах проектов. Чтобы увидеть, как это работает, в образце решения Обратите внимание, что два файла пользовательский проект:

- *Publish.proj*, который содержит свойства, элементы, и целевые объекты, которые являются общими для всех сред.
- *Env Dev.proj*, который содержит свойства, характерные для среды разработки.

Теперь обратите внимание, что *Publish.proj* файл, содержащий [импорта](https://msdn.microsoft.com/library/92x05xfs.aspx) элемент непосредственно под открывающей **проекта** тег.


[!code-xml[Main](understanding-the-project-file/samples/sample16.xml)]


**Импорта** элемент используется для импорта содержимого файла другого проекта MSBuild в текущий файл проекта MSBuild. В этом случае **TargetEnvPropsFile** параметр предоставляет имя файла для импорта файла проекта. При запуске MSBuild может предоставить значение для этого параметра.


[!code-console[Main](understanding-the-project-file/samples/sample17.cmd)]


Это эффективно объединение двух файлов в один файл проекта. При таком подходе можно создать один проект файл, содержащий конфигурацию построения универсальных и несколько файлов дополнительных проект, содержащий свойства, относящиеся к среде. В результате другое значение параметра, просто выполнив команду позволяет развертывать решения в другую среду.

![](understanding-the-project-file/_static/image3.png)

Разделение файлы проекта в этом случае рекомендуется следовать. Она позволяет разработчикам развертывания в различных средах, выполнив одну команду, при этом избежать дублирования свойства универсальной сборки в нескольких файлах проектов.

> [!NOTE]
> Рекомендации по настройке файлы проекта, относящихся к среде для серверных сред см. в разделе [настройки свойств развертывания для целевой среды](../configuring-server-environments-for-web-deployment/configuring-deployment-properties-for-a-target-environment.md).


## <a name="conclusion"></a>Заключение

В этом разделе Общие сведения о файлах проектов MSBuild и объясняется, как можно создать собственные пользовательские файлы проекта для управления процессом построения. Он также представляет концепцию разделения файлов проекта на инструкции универсальной сборки и сборки среды свойства, для упрощения построения и развертывания проектов в несколько назначений.

Следующий раздел [основные сведения о процессе сборки](understanding-the-build-process.md), предоставляет больше возможностей регулировать использование файлов проекта для построения элементов управления и развертывания посредством развертывания решения с реалистичных уровень сложности.

## <a name="further-reading"></a>Дополнительные сведения

Более подробные сведения о файлы проекта и WPP см. в разделе [внутри Microsoft Build Engine: с помощью MSBuild и Team Foundation Build](http://amzn.com/0735645248) Sayed Ibrahim Hashimi и Bartholomew Уильям, ISBN: 978-0-7356-4524-0.

>[!div class="step-by-step"]
[Назад](setting-up-the-contact-manager-solution.md)
[Вперед](understanding-the-build-process.md)