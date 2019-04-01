---
title: Устранение неполадок локализации в ASP.NET Core
author: hishamco
description: Сведения о диагностике проблем локализации в приложениях ASP.NET Core.
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 73405f539c89d79096e7b536407cd9730679d478
ms.sourcegitcommit: 687ffb15ebe65379f75c84739ea851d5a0d788b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58489001"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="9fb5c-103">Устранение неполадок локализации в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9fb5c-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="9fb5c-104">Автор: [Хишам Бин Атея](https://github.com/hishamco) (Hisham Bin Ateya)</span><span class="sxs-lookup"><span data-stu-id="9fb5c-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="9fb5c-105">В этой статье приводятся инструкции по диагностике проблем локализации в приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="9fb5c-106">Проблемы с конфигурацией локализации</span><span class="sxs-lookup"><span data-stu-id="9fb5c-106">Localization configuration issues</span></span>

<span data-ttu-id="9fb5c-107">**Порядок ПО промежуточного слоя локализации**</span><span class="sxs-lookup"><span data-stu-id="9fb5c-107">**Localization middleware order**</span></span>  
<span data-ttu-id="9fb5c-108">Локализация приложения может быть невозможна из-за неправильно упорядоченного ПО промежуточного слоя локализации.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="9fb5c-109">Чтобы устранить эту проблему, убедитесь, что ПО промежуточного слоя локализации зарегистрировано до ПО промежуточного слоя MVC.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="9fb5c-110">В противном случае ПО промежуточного слоя локализации не будет применено.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="9fb5c-111">**Путь к ресурсам локализации не найден**</span><span class="sxs-lookup"><span data-stu-id="9fb5c-111">**Localization resources path not found**</span></span>

<span data-ttu-id="9fb5c-112">**Поддерживаемые языки и региональные параметры в RequestCultureProvider не совпадают с зарегистрированными**</span><span class="sxs-lookup"><span data-stu-id="9fb5c-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="9fb5c-113">Проблемы именования файлов ресурсов</span><span class="sxs-lookup"><span data-stu-id="9fb5c-113">Resource file naming issues</span></span>

<span data-ttu-id="9fb5c-114">В ASP.NET Core существуют предопределенные правила и рекомендации для именования файлов ресурсов локализации, которые подробно описаны [здесь](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span><span class="sxs-lookup"><span data-stu-id="9fb5c-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="9fb5c-115">Отсутствующие ресурсы</span><span class="sxs-lookup"><span data-stu-id="9fb5c-115">Missing resources</span></span>

<span data-ttu-id="9fb5c-116">В число распространенных причин, по которым не удается найти ресурсы, входят следующие:</span><span class="sxs-lookup"><span data-stu-id="9fb5c-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="9fb5c-117">имена ресурсов в файле `resx` или запросе средства локализации написаны с ошибками;</span><span class="sxs-lookup"><span data-stu-id="9fb5c-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="9fb5c-118">ресурс отсутствует в файле `resx` для одних языков, но существует для других;</span><span class="sxs-lookup"><span data-stu-id="9fb5c-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="9fb5c-119">если проблема сохраняется, просмотрите подробные сведения об отсутствующих ресурсах в сообщениях журнала локализации (расположенных на уровне ведения журнала `Debug`).</span><span class="sxs-lookup"><span data-stu-id="9fb5c-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="9fb5c-120">_**Подсказка.** Если вы используете `CookieRequestCultureProvider`, убедитесь, что языки и региональные параметры в значениях файлов cookie локализации указаны без одинарных кавычек. Например, `c='en-UK'|uic='en-US'` — это недопустимое значение файла cookie, а `c=en-UK|uic=en-US` — допустимое._</span><span class="sxs-lookup"><span data-stu-id="9fb5c-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="9fb5c-121">Проблемы с файлами ресурсов и библиотеками классов</span><span class="sxs-lookup"><span data-stu-id="9fb5c-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="9fb5c-122">По умолчанию ASP.NET Core позволяет библиотекам классов находить соответствующие файлы ресурсов с помощью класса [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span><span class="sxs-lookup"><span data-stu-id="9fb5c-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="9fb5c-123">В число распространенных проблем с библиотеками классов входят следующие:</span><span class="sxs-lookup"><span data-stu-id="9fb5c-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="9fb5c-124">из-за отсутствия класса `ResourceLocationAttribute` в библиотеке классов `ResourceManagerStringLocalizerFactory` не сможет обнаруживать ресурсы;</span><span class="sxs-lookup"><span data-stu-id="9fb5c-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="9fb5c-125">именование файлов ресурсов.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-125">Resource file naming.</span></span> <span data-ttu-id="9fb5c-126">Дополнительные сведения см. в разделе [Проблемы именования файлов ресурсов](#resource-file-naming-issues).</span><span class="sxs-lookup"><span data-stu-id="9fb5c-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="9fb5c-127">Изменение корневого пространства имен библиотеки классов.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="9fb5c-128">Дополнительные сведения см. в разделе [Проблемы с корневым пространством имен](#root-namespace-issues).</span><span class="sxs-lookup"><span data-stu-id="9fb5c-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="9fb5c-129">CustomRequestCultureProvider не работает должным образом</span><span class="sxs-lookup"><span data-stu-id="9fb5c-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="9fb5c-130">У класса `RequestLocalizationOptions` есть три поставщика по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="9fb5c-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="9fb5c-131">[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) позволяет настроить способ предоставления сведений о языке и региональных параметрах локализации в приложении.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="9fb5c-132">`CustomRequestCultureProvider` используется, если поставщики по умолчанию не соответствуют вашим требованиям.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="9fb5c-133">Чаще всего неправильная работа настраиваемого поставщика связана с тем, что он не является первым в списке `RequestCultureProviders`.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="9fb5c-134">Действия для устранения этой проблемы.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-134">To resolve this issue:</span></span>

- <span data-ttu-id="9fb5c-135">Вставьте настраиваемый поставщик в позицию 0 в списке `RequestCultureProviders`, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```

- <span data-ttu-id="9fb5c-136">С помощью метода расширения `AddInitialRequestCultureProvider` задайте настраиваемый поставщик в качестве исходного.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="9fb5c-137">Проблемы с корневым пространством имен</span><span class="sxs-lookup"><span data-stu-id="9fb5c-137">Root Namespace issues</span></span>

<span data-ttu-id="9fb5c-138">Если корневое пространство имен сборки отличается от имени сборки, локализация не работает по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="9fb5c-139">Чтобы избежать этой проблемы, используйте свойство [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), которое подробно описано [здесь](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span><span class="sxs-lookup"><span data-stu-id="9fb5c-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

## <a name="resources--build-action"></a><span data-ttu-id="9fb5c-140">Ресурсы и действие при сборке</span><span class="sxs-lookup"><span data-stu-id="9fb5c-140">Resources & Build Action</span></span>

<span data-ttu-id="9fb5c-141">Если вы используете файлы ресурсов для локализации, важно, чтобы у них было соответствующее действие при сборке.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-141">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="9fb5c-142">Они должны быть **внедренными ресурсами**, в противном случае `ResourceStringLocalizer` не сможет их найти.</span><span class="sxs-lookup"><span data-stu-id="9fb5c-142">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>