---
title: "Создание вспомогательных функций тегов в ASP.NET Core"
author: rick-anderson
description: "Сведения о разработке вспомогательных функций тегов в ASP.NET Core."
ms.author: riande
manager: wpickett
ms.date: 01/19/2018
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/tag-helpers/authoring
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9aaf40377e07e53fd0b7ebb177bcbb2df52b7553
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="author-tag-helpers-in-aspnet-core-a-walkthrough-with-samples"></a><span data-ttu-id="2cb8c-103">Автор вспомогательных функций тегов в ASP.NET Core, пошаговое руководство с примерами</span><span class="sxs-lookup"><span data-stu-id="2cb8c-103">Author Tag Helpers in ASP.NET Core, a walkthrough with samples</span></span>

<span data-ttu-id="2cb8c-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="2cb8c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2cb8c-105">[Просмотреть или скачать образец кода](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([как скачивать](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2cb8c-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="get-started-with-tag-helpers"></a><span data-ttu-id="2cb8c-106">Приступая к работе с вспомогательных функций тегов</span><span class="sxs-lookup"><span data-stu-id="2cb8c-106">Get started with Tag Helpers</span></span>

<span data-ttu-id="2cb8c-107">Этот учебник содержит вводные сведения о программирования вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-107">This tutorial provides an introduction to programming Tag Helpers.</span></span> <span data-ttu-id="2cb8c-108">[Общие сведения о вспомогательных функций тегов](intro.md) описываются преимущества, которые предоставляют вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-108">[Introduction to Tag Helpers](intro.md) describes the benefits that Tag Helpers provide.</span></span>

<span data-ttu-id="2cb8c-109">Вспомогательный объект тег является любой класс, реализующий `ITagHelper` интерфейса.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-109">A tag helper is any class that implements the `ITagHelper` interface.</span></span> <span data-ttu-id="2cb8c-110">Тем не менее, при создании вспомогательных тег вы обычно являются производными от `TagHelper`, выполнив Да предоставляет вам доступ к `Process` метод.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-110">However, when you author a tag helper, you generally derive from `TagHelper`, doing so gives you access to the `Process` method.</span></span>

1. <span data-ttu-id="2cb8c-111">Создайте новый проект ASP.NET Core с именем **AuthoringTagHelpers**.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-111">Create a new ASP.NET Core project called **AuthoringTagHelpers**.</span></span> <span data-ttu-id="2cb8c-112">Нет необходимости проверки подлинности для этого проекта.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-112">You won't need authentication for this project.</span></span>

2. <span data-ttu-id="2cb8c-113">Создайте папку для хранения вспомогательных функций тегов, которая называется *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-113">Create a folder to hold the Tag Helpers called *TagHelpers*.</span></span> <span data-ttu-id="2cb8c-114">*TagHelpers* папка *не* обязательно, но это разумного соглашение.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-114">The *TagHelpers* folder is *not* required, but it is a reasonable convention.</span></span> <span data-ttu-id="2cb8c-115">Теперь давайте приступить к написанию Некоторые помощники тега в краткой форме.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-115">Now let's get started writing some simple tag helpers.</span></span>

## <a name="a-minimal-tag-helper"></a><span data-ttu-id="2cb8c-116">Вспомогательный объект минимальной тега</span><span class="sxs-lookup"><span data-stu-id="2cb8c-116">A minimal Tag Helper</span></span>

<span data-ttu-id="2cb8c-117">В этом разделе запись помощник тег, который обновляет тег электронной почты.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-117">In this section, you write a tag helper that updates an email tag.</span></span> <span data-ttu-id="2cb8c-118">Пример:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-118">For example:</span></span>

```html
<email>Support</email>
   ```

<span data-ttu-id="2cb8c-119">Сервер будет использовать наши вспомогательные тег электронной почты для преобразования, разметку в следующее:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-119">The server will use our email tag helper to convert that markup into the following:</span></span>

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

<span data-ttu-id="2cb8c-120">То есть тег, делает это ссылку в электронной почте.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-120">That is, an anchor tag that makes this an email link.</span></span> <span data-ttu-id="2cb8c-121">Может потребоваться в случае, если вы пишете механизмом поддержки блогов, для отправки электронной почты для отдела маркетинга, поддержки и другие контакты все в одном домене.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-121">You might want to do this if you are writing a blog engine and need it to send email for marketing, support, and other contacts, all to the same domain.</span></span>

1.  <span data-ttu-id="2cb8c-122">Добавьте следующие `EmailTagHelper` класса *TagHelpers* папки.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-122">Add the following `EmailTagHelper` class to the *TagHelpers* folder.</span></span>

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]
    
    <span data-ttu-id="2cb8c-123">**Примечания.**</span><span class="sxs-lookup"><span data-stu-id="2cb8c-123">**Notes:**</span></span>
    
    * <span data-ttu-id="2cb8c-124">Тег вспомогательные методы используют соглашение об именовании, предназначенного элементы корневого имени класса (минус *вспомогательной функции тегов* часть имени класса).</span><span class="sxs-lookup"><span data-stu-id="2cb8c-124">Tag helpers use a naming convention that targets elements of the root class name (minus the *TagHelper* portion of the class name).</span></span> <span data-ttu-id="2cb8c-125">В этом примере имя корневой папки **электронной почты**является вспомогательной функции тегов *электронной почты*, поэтому `<email>` целевые тег.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-125">In this example, the root name of **Email**TagHelper is *email*, so the `<email>` tag will be targeted.</span></span> <span data-ttu-id="2cb8c-126">Это соглашение об именовании подойдут для большинства вспомогательных функций тегов, впоследствии будет показано как переопределить его.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-126">This naming convention should work for most tag helpers, later on I'll show how to override it.</span></span>
    
    * <span data-ttu-id="2cb8c-127">Класс `EmailTagHelper` является производным от класса `TagHelper`.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-127">The `EmailTagHelper` class derives from `TagHelper`.</span></span> <span data-ttu-id="2cb8c-128">`TagHelper` Класс предоставляет методы и свойства для записи вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-128">The `TagHelper` class provides methods and properties for writing Tag Helpers.</span></span>
    
    * <span data-ttu-id="2cb8c-129">Переопределенный `Process` метод управляет назначение вспомогательный тег при выполнении.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-129">The overridden `Process` method controls what the tag helper does when executed.</span></span> <span data-ttu-id="2cb8c-130">`TagHelper` Класс также предоставляет асинхронную версию (`ProcessAsync`) с теми же параметрами.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-130">The `TagHelper` class also provides an asynchronous version (`ProcessAsync`) with the same parameters.</span></span>
    
    * <span data-ttu-id="2cb8c-131">Параметр контекста для `Process` (и `ProcessAsync`) содержит сведения, связанные с выполнением текущего HTML-тега.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-131">The context parameter to `Process` (and `ProcessAsync`) contains information associated with the execution of the current HTML tag.</span></span>
    
    * <span data-ttu-id="2cb8c-132">Выходной параметр, чтобы `Process` (и `ProcessAsync`) содержит элемент с отслеживанием состояния HTML отражает исходного источника, используемый для создания HTML-тег и содержимое.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-132">The output parameter to `Process` (and `ProcessAsync`) contains a stateful HTML element representative of the original source used to generate an HTML tag and content.</span></span>
    
    * <span data-ttu-id="2cb8c-133">Наш имя класса содержит суффикс **вспомогательной функции тегов**, который является *не* требуется, но он считается наилучшим соглашением рекомендаций.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-133">Our class name has a suffix of **TagHelper**, which is *not* required, but it's considered a best practice convention.</span></span> <span data-ttu-id="2cb8c-134">Можно объявить класс как:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-134">You could declare the class as:</span></span>
    
    ```csharp
    public class Email : TagHelper
    ```

2.  <span data-ttu-id="2cb8c-135">Чтобы сделать `EmailTagHelper` класса для наших представлений Razor, добавьте `addTagHelper` директиву *Views/_ViewImports.cshtml* файла:[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]</span><span class="sxs-lookup"><span data-stu-id="2cb8c-135">To make the `EmailTagHelper` class available to all our Razor views, add the `addTagHelper` directive to the *Views/_ViewImports.cshtml* file: [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]</span></span>
    
    <span data-ttu-id="2cb8c-136">Приведенный выше код использует синтаксисе знаков подстановки для указания всех вспомогательных функций тегов в нашем сборки будет доступно.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-136">The code above uses the wildcard syntax to specify all the tag helpers in our assembly will be available.</span></span> <span data-ttu-id="2cb8c-137">Первая строка после `@addTagHelper` указывает тег вспомогательное приложение для загрузки (используйте «\*» для всех вспомогательных функций тегов), и вторая строка «AuthoringTagHelpers» указывает тег модуль сборки.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-137">The first string after `@addTagHelper` specifies the tag helper to load (Use "\*" for all tag helpers), and the second string "AuthoringTagHelpers" specifies the assembly the tag helper is in.</span></span> <span data-ttu-id="2cb8c-138">Кроме того, обратите внимание, что вторая строка переносит вспомогательных функций тегов Core ASP.NET MVC с помощью синтаксиса подстановочный знак (Эти вспомогательные методы обсуждаются в [Общие сведения о вспомогательных функций тегов](intro.md).) Это `@addTagHelper` директиву, которая делает доступными для представления Razor вспомогательный тег.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-138">Also, note that the second line brings in the ASP.NET Core MVC tag helpers using the wildcard syntax (those helpers are discussed in [Introduction to Tag Helpers](intro.md).) It's the `@addTagHelper` directive that makes the tag helper available to the Razor view.</span></span> <span data-ttu-id="2cb8c-139">Кроме того можно указать полное доменное имя (FQN) тега вспомогательного метода, как показано ниже:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-139">Alternatively, you can provide the fully qualified name (FQN) of a tag helper as shown below:</span></span>
    
```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```
    
<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->
    
<span data-ttu-id="2cb8c-140">Чтобы добавить тег вспомогательный класс для представления с помощью FQN, сначала добавьте FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), а затем имя сборки (*AuthoringTagHelpers*).</span><span class="sxs-lookup"><span data-stu-id="2cb8c-140">To add a tag helper to a view using a FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name (*AuthoringTagHelpers*).</span></span> <span data-ttu-id="2cb8c-141">Большинство разработчиков будет предпочитают использовать синтаксисе знаков подстановки.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-141">Most developers will prefer to use the wildcard syntax.</span></span> <span data-ttu-id="2cb8c-142">[Общие сведения о вспомогательных функций тегов](intro.md) попадают в подробности синтаксиса тегов вспомогательный добавления, удаления, иерархии и подстановочный знак.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-142">[Introduction to Tag Helpers](intro.md) goes into detail on tag helper adding, removing, hierarchy, and wildcard syntax.</span></span>
    
3.  <span data-ttu-id="2cb8c-143">Обновить разметки в *Views/Home/Contact.cshtml* файла с учетом внесенных изменений:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-143">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

4.  <span data-ttu-id="2cb8c-144">Запустите приложение и использовать любимом браузере для просмотра исходного кода HTML, чтобы можно было удостовериться, что теги электронной почты заменяются разметки привязки (например, `<a>Support</a>`).</span><span class="sxs-lookup"><span data-stu-id="2cb8c-144">Run the app and use your favorite browser to view the HTML source so you can verify that the email tags are replaced with anchor markup (For example, `<a>Support</a>`).</span></span> <span data-ttu-id="2cb8c-145">*Поддержка* и *маркетинга* отображаются как ссылки, но они не имеют `href` атрибут, чтобы сделать их работы.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-145">*Support* and *Marketing* are rendered as a links, but they don't have an `href` attribute to make them functional.</span></span> <span data-ttu-id="2cb8c-146">Мы исправим это в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-146">We'll fix that in the next section.</span></span>

<span data-ttu-id="2cb8c-147">Примечание: Как HTML-теги и атрибуты, теги, имена классов и атрибутов в Razor и C# не учитывают регистр.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-147">Note: Like HTML tags and attributes, tags, class names and attributes in Razor, and C# are not case-sensitive.</span></span>

## <a name="setattribute-and-setcontent"></a><span data-ttu-id="2cb8c-148">SetAttribute и SetContent</span><span class="sxs-lookup"><span data-stu-id="2cb8c-148">SetAttribute and SetContent</span></span>

<span data-ttu-id="2cb8c-149">В этом разделе мы обновим `EmailTagHelper` , чтобы он создает допустимый тег для электронной почты.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-149">In this section, we'll update the `EmailTagHelper` so that it will create a valid anchor tag for email.</span></span> <span data-ttu-id="2cb8c-150">Мы обновим принимают данные из представления Razor (в виде `mail-to` атрибут) и использовать его при создании привязки.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-150">We'll update it to take information from a Razor view (in the form of a `mail-to` attribute) and use that in generating the anchor.</span></span>

<span data-ttu-id="2cb8c-151">Обновление `EmailTagHelper` класса следующими строками:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-151">Update the `EmailTagHelper` class with the following:</span></span>

[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

<span data-ttu-id="2cb8c-152">**Примечания.**</span><span class="sxs-lookup"><span data-stu-id="2cb8c-152">**Notes:**</span></span>

* <span data-ttu-id="2cb8c-153">Преобразуется в стиле Pascal имена классов и свойств для вспомогательных функций тегов их [нижний регистр kebab](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="2cb8c-153">Pascal-cased class and property names for tag helpers are translated into their [lower kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="2cb8c-154">Таким образом Чтобы использовать `MailTo` атрибут, вы воспользуетесь `<email mail-to="value"/>` эквивалент.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-154">Therefore, to use the `MailTo` attribute, you'll use `<email mail-to="value"/>` equivalent.</span></span>

* <span data-ttu-id="2cb8c-155">Последняя строка задает завершенного содержимое для наших минимально функциональной тег вспомогательного метода.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-155">The last line sets the completed content for our minimally functional tag helper.</span></span>

* <span data-ttu-id="2cb8c-156">Выделенная строка показан синтаксис для добавления атрибутов:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-156">The highlighted line shows the syntax for adding attributes:</span></span>

[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

<span data-ttu-id="2cb8c-157">Этот способ подходит для атрибута «href», пока он не существует в настоящее время в коллекции атрибутов.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-157">That approach works for the attribute "href" as long as it doesn't currently exist in the attributes collection.</span></span> <span data-ttu-id="2cb8c-158">Можно также использовать `output.Attributes.Add` метод, чтобы добавить атрибут вспомогательной функции тегов в конец коллекции атрибутов тега.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-158">You can also use the `output.Attributes.Add` method to add a tag helper attribute to the end of the collection of tag attributes.</span></span>

1.  <span data-ttu-id="2cb8c-159">Обновить разметки в *Views/Home/Contact.cshtml* файла с учетом внесенных изменений:[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]</span><span class="sxs-lookup"><span data-stu-id="2cb8c-159">Update the markup in the *Views/Home/Contact.cshtml* file with these changes: [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]</span></span>

2.  <span data-ttu-id="2cb8c-160">Запустите приложение и убедитесь, что он создает правильные ссылки.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-160">Run the app and verify that it generates the correct links.</span></span>
    
    > [!NOTE]
    ><span data-ttu-id="2cb8c-161">В случае записи тег электронной почты самостоятельно закрытие (`<email mail-to="Rick" />`), также будут самозакрывающегося конечного результата.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-161">If you were to write the email tag self-closing (`<email mail-to="Rick" />`), the final output would also be self-closing.</span></span> <span data-ttu-id="2cb8c-162">Чтобы включить возможность записать тег с помощью открывающего тега (`<email mail-to="Rick">`) необходимо дополнить класса на следующий:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-162">To enable the ability to write the tag with only a start tag (`<email mail-to="Rick">`) you must decorate the class with the following:</span></span>
    >
    > [!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]
    
    <span data-ttu-id="2cb8c-163">С самозакрывающегося тега вспомогательный электронной почты, результатом будет `<a href="mailto:Rick@contoso.com" />`.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-163">With a self-closing email tag helper, the output would be `<a href="mailto:Rick@contoso.com" />`.</span></span> <span data-ttu-id="2cb8c-164">Требуется закрывать теги привязки не допустимый HTML-код, поэтому не нужно создавать его, но может потребоваться создать помощник тег, который не требуется закрывать.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-164">Self-closing anchor tags are not valid HTML, so you wouldn't want to create one, but you might want to create a tag helper that is self-closing.</span></span> <span data-ttu-id="2cb8c-165">Вспомогательных функций тегов задать тип `TagMode` свойство после прочтения тег.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-165">Tag helpers set the type of the `TagMode` property after reading a tag.</span></span>
    
### <a name="processasync"></a><span data-ttu-id="2cb8c-166">ProcessAsync</span><span class="sxs-lookup"><span data-stu-id="2cb8c-166">ProcessAsync</span></span>

<span data-ttu-id="2cb8c-167">В этом разделе мы записываем вспомогательный асинхронной электронной почты.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-167">In this section, we'll write an asynchronous email helper.</span></span>

1.  <span data-ttu-id="2cb8c-168">Замените `EmailTagHelper` класса следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-168">Replace the `EmailTagHelper` class with the following code:</span></span>

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

    <span data-ttu-id="2cb8c-169">**Примечания.**</span><span class="sxs-lookup"><span data-stu-id="2cb8c-169">**Notes:**</span></span>

    * <span data-ttu-id="2cb8c-170">Эта версия использует асинхронную `ProcessAsync` метод.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-170">This version uses the asynchronous `ProcessAsync` method.</span></span> <span data-ttu-id="2cb8c-171">Асинхронная `GetChildContentAsync` возвращает `Task` содержащий `TagHelperContent`.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-171">The asynchronous `GetChildContentAsync` returns a `Task` containing the `TagHelperContent`.</span></span>

    * <span data-ttu-id="2cb8c-172">Используйте `output` для получения содержимого элемента HTML.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-172">Use the `output` parameter to get contents of the HTML element.</span></span>

2.  <span data-ttu-id="2cb8c-173">Внесите следующее изменение для *Views/Home/Contact.cshtml* файл, поэтому вспомогательные тег можно получить по электронной почте целевой.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-173">Make the following change to the *Views/Home/Contact.cshtml* file so the tag helper can get the target email.</span></span>

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

3.  <span data-ttu-id="2cb8c-174">Запустите приложение и убедитесь, он создает ссылки на допустимый адрес электронной почты.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-174">Run the app and verify that it generates valid email links.</span></span>

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a><span data-ttu-id="2cb8c-175">RemoveAll, PreContent.SetHtmlContent и PostContent.SetHtmlContent</span><span class="sxs-lookup"><span data-stu-id="2cb8c-175">RemoveAll, PreContent.SetHtmlContent and PostContent.SetHtmlContent</span></span>

1.  <span data-ttu-id="2cb8c-176">Добавьте следующие `BoldTagHelper` класса *TagHelpers* папки.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-176">Add the following `BoldTagHelper` class to the *TagHelpers* folder.</span></span>

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

    <span data-ttu-id="2cb8c-177">**Примечания.**</span><span class="sxs-lookup"><span data-stu-id="2cb8c-177">**Notes:**</span></span>
    
    * <span data-ttu-id="2cb8c-178">`[HtmlTargetElement]` Атрибут передает параметр атрибута, указывающее, что любой HTML-элемент, который содержит HTML-атрибут с именем «bold» будет соответствовать, а `Process` переопределяющий метод в классе будет выполняться.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-178">The `[HtmlTargetElement]` attribute passes an attribute parameter that specifies that any HTML element that contains an HTML attribute named "bold" will match, and the `Process` override method in the class will run.</span></span> <span data-ttu-id="2cb8c-179">В нашем примере `Process` метод удаляет атрибут «полужирный» и окружает содержащего разметку с `<strong></strong>`.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-179">In our sample, the `Process` method removes the "bold" attribute and surrounds the containing markup with `<strong></strong>`.</span></span>
    
    * <span data-ttu-id="2cb8c-180">Поскольку вы не хотите заменить существующие теги содержимого, необходимо написать открывающий `<strong>` тег с `PreContent.SetHtmlContent` метод и закрытие `</strong>` тег с `PostContent.SetHtmlContent` метод.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-180">Because you don't want to replace the existing tag content, you must write the opening `<strong>` tag with the `PreContent.SetHtmlContent` method and the closing `</strong>` tag with the `PostContent.SetHtmlContent` method.</span></span>
    
2.  <span data-ttu-id="2cb8c-181">Изменить *About.cshtml* представление для размещения `bold` значение атрибута.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-181">Modify the *About.cshtml* view to contain a `bold` attribute value.</span></span> <span data-ttu-id="2cb8c-182">Ниже приведен полный код.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-182">The completed code is shown below.</span></span>

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

3.  <span data-ttu-id="2cb8c-183">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-183">Run the app.</span></span> <span data-ttu-id="2cb8c-184">Любимом браузере можно использовать для проверки источника и проверьте разметку.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-184">You can use your favorite browser to inspect the source and verify the markup.</span></span>

    <span data-ttu-id="2cb8c-185">`[HtmlTargetElement]` Атрибут выше предназначен только для разметки HTML, который предоставляет имя атрибута «полужирный».</span><span class="sxs-lookup"><span data-stu-id="2cb8c-185">The `[HtmlTargetElement]` attribute above only targets HTML markup that provides an attribute name of "bold".</span></span> <span data-ttu-id="2cb8c-186">`<bold>` Элемент не был изменен тег вспомогательный метод.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-186">The `<bold>` element was not modified by the tag helper.</span></span>

4. <span data-ttu-id="2cb8c-187">Закомментируйте `[HtmlTargetElement]` строку атрибута и он будет по умолчанию для различных версий `<bold>` теги, то есть HTML-разметку формы `<bold>`.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-187">Comment out the `[HtmlTargetElement]` attribute line and it will default to targeting `<bold>` tags, that is, HTML markup of the form `<bold>`.</span></span> <span data-ttu-id="2cb8c-188">Помните, что именования по умолчанию будет совпадать с именем класса **Полужирный**вспомогательной функции тегов для `<bold>` тегов.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-188">Remember, the default naming convention will match the class name **Bold**TagHelper to `<bold>` tags.</span></span>

5. <span data-ttu-id="2cb8c-189">Запустите приложение и убедитесь, что `<bold>` тег обрабатывается тег вспомогательный метод.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-189">Run the app and verify that the `<bold>` tag is processed by the tag helper.</span></span>

<span data-ttu-id="2cb8c-190">Декорирования классов с несколькими `[HtmlTargetElement]` атрибуты результаты логической операцией или целевых объектов.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-190">Decorating a class with multiple `[HtmlTargetElement]` attributes results in a logical-OR of the targets.</span></span> <span data-ttu-id="2cb8c-191">Например используя приведенный ниже код, полужирным тега или атрибута bold будет соответствовать.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-191">For example, using the code below, a bold tag or a bold attribute will match.</span></span>

[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

<span data-ttu-id="2cb8c-192">При добавлении нескольких атрибутов той же инструкции, среда выполнения рассматривает их как логическое и.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-192">When multiple attributes are added to the same statement, the runtime treats them as a logical-AND.</span></span> <span data-ttu-id="2cb8c-193">Например, в следующем коде HTML-элемент должен иметь имя «bold» с атрибутом с именем «bold» (`<bold bold />`) для сопоставления.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-193">For example, in the code below, an HTML element must be named "bold" with an attribute named "bold" (`<bold bold />`) to match.</span></span>

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

<span data-ttu-id="2cb8c-194">Можно также использовать `[HtmlTargetElement]` для изменения имени целевого элемента.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-194">You can also use the `[HtmlTargetElement]` to change the name of the targeted element.</span></span> <span data-ttu-id="2cb8c-195">Например, если вы хотите, чтобы `BoldTagHelper` целевой `<MyBold>` тегов, используется следующий атрибут:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-195">For example if you wanted the `BoldTagHelper` to target `<MyBold>` tags, you would use the following attribute:</span></span>

```csharp
[HtmlTargetElement("MyBold")]
   ```

## <a name="pass-a-model-to-a-tag-helper"></a><span data-ttu-id="2cb8c-196">Передать модель помощникам тега</span><span class="sxs-lookup"><span data-stu-id="2cb8c-196">Pass a model to a Tag Helper</span></span>

1.  <span data-ttu-id="2cb8c-197">Добавить *моделей* папки.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-197">Add a *Models* folder.</span></span>

2.  <span data-ttu-id="2cb8c-198">Добавьте следующий класс `WebsiteContext` в папку *Models*:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-198">Add the following `WebsiteContext` class to the *Models* folder:</span></span>

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

3.  <span data-ttu-id="2cb8c-199">Добавьте следующие `WebsiteInformationTagHelper` класса *TagHelpers* папки.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-199">Add the following `WebsiteInformationTagHelper` class to the *TagHelpers* folder.</span></span>

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]
    
    <span data-ttu-id="2cb8c-200">**Примечания.**</span><span class="sxs-lookup"><span data-stu-id="2cb8c-200">**Notes:**</span></span>
    
    * <span data-ttu-id="2cb8c-201">Как упоминалось ранее, вспомогательных функций тегов преобразует имена классов в стиле Pascal C# и свойства для вспомогательных функций тегов в [нижний регистр kebab](http://wiki.c2.com/?KebabCase).</span><span class="sxs-lookup"><span data-stu-id="2cb8c-201">As mentioned previously, tag helpers translates Pascal-cased C# class names and properties for tag helpers into [lower kebab case](http://wiki.c2.com/?KebabCase).</span></span> <span data-ttu-id="2cb8c-202">Таким образом Чтобы использовать `WebsiteInformationTagHelper` в Razor, вы напишете `<website-information />`.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-202">Therefore, to use the `WebsiteInformationTagHelper` in Razor, you'll write `<website-information />`.</span></span>
    
    * <span data-ttu-id="2cb8c-203">Вы явным образом не идентифицируете целевой элемент с `[HtmlTargetElement]` атрибута, поэтому по умолчанию `website-information` будут применяться.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-203">You are not explicitly identifying the target element with the `[HtmlTargetElement]` attribute, so the default of `website-information` will be targeted.</span></span> <span data-ttu-id="2cb8c-204">Если вы применили (Примечание не kebab так, но совпадает с именем класса) следующий атрибут:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-204">If you applied the following attribute (note it's not kebab case but matches the class name):</span></span>
    
    ```csharp
    [HtmlTargetElement("WebsiteInformation")]
    ```
    
    <span data-ttu-id="2cb8c-205">Ниже тег варианта kebab `<website-information />` не будет совпадать.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-205">The lower kebab case tag `<website-information />` would not match.</span></span> <span data-ttu-id="2cb8c-206">Если вы хотите `[HtmlTargetElement]` атрибут, используйте kebab случая, как показано ниже:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-206">If you want use the `[HtmlTargetElement]` attribute, you would use kebab case as shown below:</span></span>
    
    ```csharp
    [HtmlTargetElement("Website-Information")]
    ```
    
    * <span data-ttu-id="2cb8c-207">Элементы, которые являются самозакрывающегося не имеет содержимого.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-207">Elements that are self-closing have no content.</span></span> <span data-ttu-id="2cb8c-208">В этом примере разметки Razor будет использоваться самостоятельно закрывающегося тега, но будут создавать вспомогательные тег [раздел](http://www.w3.org/TR/html5/sections.html#the-section-element) элемент (который не требуется закрывать и вы пишете содержимое внутри `section` элемент).</span><span class="sxs-lookup"><span data-stu-id="2cb8c-208">For this example, the Razor markup will use a self-closing tag, but the tag helper will be creating a [section](http://www.w3.org/TR/html5/sections.html#the-section-element) element (which is not self-closing and you are writing content inside the `section` element).</span></span> <span data-ttu-id="2cb8c-209">Таким образом, необходимо задать `TagMode` для `StartTagAndEndTag` для записи выходных данных.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-209">Therefore, you need to set `TagMode` to `StartTagAndEndTag` to write output.</span></span> <span data-ttu-id="2cb8c-210">Кроме того, вы можете закомментировать строку, устанавливающую `TagMode` и записи разметки закрывающим тегом.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-210">Alternatively, you can comment out the line setting `TagMode` and write markup with a closing tag.</span></span> <span data-ttu-id="2cb8c-211">(Пример разметки указан далее в этом руководстве.)</span><span class="sxs-lookup"><span data-stu-id="2cb8c-211">(Example markup is provided later in this tutorial.)</span></span>
    
    * <span data-ttu-id="2cb8c-212">`$` (Знак доллара) в следующей строке использует [интерполируются строка](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/interpolated-strings):</span><span class="sxs-lookup"><span data-stu-id="2cb8c-212">The `$` (dollar sign) in the following line uses an [interpolated string](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/interpolated-strings):</span></span>
    
    ```cshtml
    $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
    ```

4.  <span data-ttu-id="2cb8c-213">Добавьте следующую разметку, чтобы *About.cshtml* представления.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-213">Add the following markup to the *About.cshtml* view.</span></span> <span data-ttu-id="2cb8c-214">Выделенную разметку сведения о веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-214">The highlighted markup displays the web site information.</span></span>
    
    [!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,12-)]
    
    >[!NOTE]
    > <span data-ttu-id="2cb8c-215">В разметке Razor, показано ниже:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-215">In the Razor markup shown below:</span></span>
    >
    >[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=13-17)]
    > 
    ><span data-ttu-id="2cb8c-216">Знает Razor `info` атрибут — это класс, не является строкой, и необходимо написать код C#.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-216">Razor knows the `info` attribute is a class, not a string, and you want to write C# code.</span></span> <span data-ttu-id="2cb8c-217">Любой атрибут вспомогательной функции нестроковые тег должен быть записан без `@` символов.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-217">Any non-string tag helper attribute should be written without the `@` character.</span></span>
    
5.  <span data-ttu-id="2cb8c-218">Запустите приложение и перейдите в представление о программе, чтобы просмотреть сведения веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-218">Run the app, and navigate to the About view to see the web site information.</span></span>

    >[!NOTE]
    ><span data-ttu-id="2cb8c-219">Можно использовать следующую разметку с закрывающий тег и удалите строку с `TagMode.StartTagAndEndTag` во вспомогательном методе тег:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-219">You can use the following markup with a closing tag and remove the line with `TagMode.StartTagAndEndTag` in the tag helper:</span></span>
    >
    >[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=13-18)]

## <a name="condition-tag-helper"></a><span data-ttu-id="2cb8c-220">Вспомогательный тег условие</span><span class="sxs-lookup"><span data-stu-id="2cb8c-220">Condition Tag Helper</span></span>

<span data-ttu-id="2cb8c-221">Вспомогательный тег условие отображает выходные данные при передаче значения true.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-221">The condition tag helper renders output when passed a true value.</span></span>

1.  <span data-ttu-id="2cb8c-222">Добавьте следующие `ConditionTagHelper` класса *TagHelpers* папки.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-222">Add the following `ConditionTagHelper` class to the *TagHelpers* folder.</span></span>

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

2.  <span data-ttu-id="2cb8c-223">Замените содержимое *Views/Home/Index.cshtml* файла следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-223">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

    ```cshtml
    @using AuthoringTagHelpers.Models
    @model WebsiteContext
    
    @{
        ViewData["Title"] = "Home Page";
    }
    
    <div>
        <h3>Information about our website (outdated):</h3>
        <website-information info=@Model />
        <div condition="@Model.Approved">
            <p>
                This website has <strong surround="em"> @Model.Approved </strong> been approved yet.
                Visit www.contoso.com for more information.
            </p>
        </div>
    </div>
    ```
    
3.  <span data-ttu-id="2cb8c-224">Замените `Index` метод в `Home` контроллера с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-224">Replace the `Index` method in the `Home` controller with the following code:</span></span>

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

4.  <span data-ttu-id="2cb8c-225">Запустите приложение и перейдите на домашнюю страницу.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-225">Run the app and browse to the home page.</span></span> <span data-ttu-id="2cb8c-226">Разметка в условной `div` не отображался.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-226">The markup in the conditional `div` will not be rendered.</span></span> <span data-ttu-id="2cb8c-227">Добавить строку запроса `?approved=true` URL-адрес (например, `http://localhost:1235/Home/Index?approved=true`).</span><span class="sxs-lookup"><span data-stu-id="2cb8c-227">Append the query string `?approved=true` to the URL (for example, `http://localhost:1235/Home/Index?approved=true`).</span></span> <span data-ttu-id="2cb8c-228">`approved`имеет значение true, а также условия отображения разметки.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-228">`approved` is set to true and the conditional markup will be displayed.</span></span>

>[!NOTE]
><span data-ttu-id="2cb8c-229">Используйте [nameof](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/nameof) оператор, чтобы указать атрибут целевой вместо указания строки, как в случае со вспомогательным методом тег полужирным шрифтом:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-229">Use the [nameof](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/nameof) operator to specify the attribute to target rather than specifying a string as you did with the bold tag helper:</span></span>
>
>[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
><span data-ttu-id="2cb8c-230">[Nameof](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/nameof) оператор будет защищать код должен его постоянно оптимизируемого (мы может возникнуть необходимость изменить имя, `RedCondition`).</span><span class="sxs-lookup"><span data-stu-id="2cb8c-230">The [nameof](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/nameof) operator will protect the code should it ever be refactored (we might want to change the name to `RedCondition`).</span></span>

### <a name="avoid-tag-helper-conflicts"></a><span data-ttu-id="2cb8c-231">Избегание конфликтов в тег вспомогательный</span><span class="sxs-lookup"><span data-stu-id="2cb8c-231">Avoid Tag Helper conflicts</span></span>

<span data-ttu-id="2cb8c-232">В этом разделе запись пару автоматическое связывание вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-232">In this section, you write a pair of auto-linking tag helpers.</span></span> <span data-ttu-id="2cb8c-233">Первый заменяет разметку, содержащую URL-адрес, начинающийся с HTTP HTML привязки тег содержащую же URL-адрес (и тем самым давая ссылку на URL-адрес).</span><span class="sxs-lookup"><span data-stu-id="2cb8c-233">The first will replace markup containing a URL starting with HTTP to an HTML anchor tag containing the same URL (and thus yielding a link to the URL).</span></span> <span data-ttu-id="2cb8c-234">Второй будет то же сделайте для URL-адреса начиная с веб-публикации.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-234">The second will do the same for a URL starting with WWW.</span></span>

<span data-ttu-id="2cb8c-235">Поскольку тесно связаны эти две вспомогательные функции и их может рефакторинг в будущем, мы будем держать их в том же файле.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-235">Because these two helpers are closely related and you may refactor them in the future, we'll keep them in the same file.</span></span>

1.  <span data-ttu-id="2cb8c-236">Добавьте следующие `AutoLinkerHttpTagHelper` класса *TagHelpers* папки.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-236">Add the following `AutoLinkerHttpTagHelper` class to the *TagHelpers* folder.</span></span>

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

    >[!NOTE]
    ><span data-ttu-id="2cb8c-237">`AutoLinkerHttpTagHelper` Класса цели `p` элементы и использует [Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) для создания привязки.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-237">The `AutoLinkerHttpTagHelper` class targets `p` elements and uses [Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) to create the anchor.</span></span>

2.  <span data-ttu-id="2cb8c-238">Добавьте следующую разметку в конец *Views/Home/Contact.cshtml* файла:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-238">Add the following markup to the end of the *Views/Home/Contact.cshtml* file:</span></span>

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

3.  <span data-ttu-id="2cb8c-239">Запустите приложение и проверьте, правильно отображает вспомогательный тег привязки.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-239">Run the app and verify that the tag helper renders the anchor correctly.</span></span>

4.  <span data-ttu-id="2cb8c-240">Обновление `AutoLinker` класса для включения `AutoLinkerWwwTagHelper` которого преобразует www текст в тег, который содержит исходный текст веб-публикации.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-240">Update the `AutoLinker` class to include the `AutoLinkerWwwTagHelper` which will convert www text to an anchor tag that also contains the original www text.</span></span> <span data-ttu-id="2cb8c-241">Обновленный код выделяется ниже:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-241">The updated code is highlighted below:</span></span>

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

5.  <span data-ttu-id="2cb8c-242">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-242">Run the app.</span></span> <span data-ttu-id="2cb8c-243">Обратите внимание, www текст отображается в виде ссылки, но не является текстом HTTP.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-243">Notice the www text is rendered as a link but the HTTP text is not.</span></span> <span data-ttu-id="2cb8c-244">Если установить точку останова в обоих классах, вы увидите, вспомогательный класс тег HTTP выполняется первой.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-244">If you put a break point in both classes, you can see that the HTTP tag helper class runs first.</span></span> <span data-ttu-id="2cb8c-245">Проблема заключается в кэширования вывода вспомогательный тегов, что при запуске вспомогательного тег WWW она перезаписывает кэшированные выходные данные модуля поддержки тег HTTP.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-245">The problem is that the tag helper output is cached, and when the WWW tag helper is run, it overwrites the cached output from the HTTP tag helper.</span></span> <span data-ttu-id="2cb8c-246">Далее в этом учебнике будет показано, как можно управлять порядком выполнения вспомогательных функций тегов в.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-246">Later in the tutorial we'll see how to control the order that tag helpers run in.</span></span> <span data-ttu-id="2cb8c-247">Нам нужно исправить код с помощью следующего:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-247">We'll fix the code with the following:</span></span>

    [!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

    >[!NOTE]
    ><span data-ttu-id="2cb8c-248">В первом выпуске автоматическое связывание вспомогательных функций тегов полученный содержимое целевого объекта с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-248">In the first edition of the auto-linking tag helpers, you got the content of the target with the following code:</span></span>
    >
    >[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
    >
    ><span data-ttu-id="2cb8c-249">То есть, можно вызвать `GetChildContentAsync` с помощью `TagHelperOutput` переданные `ProcessAsync` метод.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-249">That is, you call `GetChildContentAsync` using the `TagHelperOutput` passed into the `ProcessAsync` method.</span></span> <span data-ttu-id="2cb8c-250">Как упоминалось ранее, так как выходные данные кэшируются, последний тег вспомогательный метод для запуска wins.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-250">As mentioned previously, because the output is cached, the last tag helper to run wins.</span></span> <span data-ttu-id="2cb8c-251">Вы фиксированной этой проблеме с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-251">You fixed that problem with the following code:</span></span>
    >
    >[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
    >
    ><span data-ttu-id="2cb8c-252">Приведенный выше код проверяет, если содержимое было изменено, и если да, он получает содержимое из выходного буфера.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-252">The code above checks to see if the content has been modified, and if it has, it gets the content from the output buffer.</span></span>

6.  <span data-ttu-id="2cb8c-253">Запустите приложение и убедиться, что две ссылки работают должным образом.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-253">Run the app and verify that the two links work as expected.</span></span> <span data-ttu-id="2cb8c-254">Хотя может показаться, что наши вспомогательные тег компоновщика автоматически будет полным и правильным, у него есть незначительные проблемы.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-254">While it might appear our auto linker tag helper is correct and complete, it has a subtle problem.</span></span> <span data-ttu-id="2cb8c-255">Если вспомогательные тег WWW выполняется первой, веб-ссылки не будут правильными.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-255">If the WWW tag helper runs first, the www links will not be correct.</span></span> <span data-ttu-id="2cb8c-256">Обновление кода, добавив `Order` перегрузку, чтобы управлять порядком, запускаемую в теге.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-256">Update the code by adding the `Order` overload to control the order that the tag runs in.</span></span> <span data-ttu-id="2cb8c-257">`Order` Свойство определяет порядок выполнения относительно других вспомогательных функций тегов, предназначенные для одного элемента.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-257">The `Order` property determines the execution order relative to other tag helpers targeting the same element.</span></span> <span data-ttu-id="2cb8c-258">Значение порядка по умолчанию равно нулю, и экземпляры с более низкими значениями выполняются первыми.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-258">The default order value is zero and instances with lower values are executed first.</span></span>

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]
    
    <span data-ttu-id="2cb8c-259">Приведенный выше код гарантирует, вспомогательные тег HTTP выполняется перед вспомогательный тег WWW.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-259">The above code will guarantee that the HTTP tag helper runs before the WWW tag helper.</span></span> <span data-ttu-id="2cb8c-260">Изменение `Order` для `MaxValue` и убедитесь, что разметки, созданной для тега WWW неверен.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-260">Change `Order` to `MaxValue` and verify that the markup generated for the WWW tag is incorrect.</span></span>

## <a name="inspect-and-retrieve-child-content"></a><span data-ttu-id="2cb8c-261">Проверки и извлечения содержимого дочернего элемента</span><span class="sxs-lookup"><span data-stu-id="2cb8c-261">Inspect and retrieve child content</span></span>

<span data-ttu-id="2cb8c-262">Вспомогательных функций тегов предоставляют несколько свойств, чтобы извлечь содержимое.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-262">The tag helpers provide several properties to retrieve content.</span></span>

-  <span data-ttu-id="2cb8c-263">Результат `GetChildContentAsync` можно добавить `output.Content`.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-263">The result of `GetChildContentAsync` can be appended to `output.Content`.</span></span>
-  <span data-ttu-id="2cb8c-264">Вы можете проверить результат `GetChildContentAsync` с `GetContent`.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-264">You can inspect the result of `GetChildContentAsync` with `GetContent`.</span></span>
-  <span data-ttu-id="2cb8c-265">При изменении `output.Content`, тексте вспомогательной функции тегов не будет выполнена или не визуализуется, если вызывается `GetChildContentAsync` как в нашем примере компоновщик автоматическое:</span><span class="sxs-lookup"><span data-stu-id="2cb8c-265">If you modify `output.Content`, the TagHelper body will not be executed or rendered unless you call `GetChildContentAsync` as in our auto-linker sample:</span></span>

[!code-csharp[Main](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

-  <span data-ttu-id="2cb8c-266">Несколько вызовов `GetChildContentAsync` будет возвращать то же значение и не повторно выполните `TagHelper` body только при проведении в значение false, указывающее, не используйте параметр кэшированный результат.</span><span class="sxs-lookup"><span data-stu-id="2cb8c-266">Multiple calls to `GetChildContentAsync` will return the same value and will not re-execute the `TagHelper` body unless you pass in a false parameter indicating not use the cached result.</span></span>