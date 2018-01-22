---
title: "По промежуточного слоя ASP.NET Core"
author: rick-anderson
description: "Дополнительные сведения о ASP.NET Core по промежуточного слоя и конвейер запросов."
ms.author: riande
manager: wpickett
ms.date: 10/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/middleware
ms.openlocfilehash: af16046c97964e8e1c16a4f5989fcfa794741c4d
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="aspnet-core-middleware-fundamentals"></a><span data-ttu-id="15fe3-103">Принципы работы по промежуточного слоя ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="15fe3-103">ASP.NET Core Middleware Fundamentals</span></span>

<a name="fundamentals-middleware"></a>

<span data-ttu-id="15fe3-104">По [Рик Андерсон](https://twitter.com/RickAndMSFT) и [Стив Смит](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="15fe3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="15fe3-105">[Просмотреть или скачать образец кода](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample) ([как скачивать](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="15fe3-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="what-is-middleware"></a><span data-ttu-id="15fe3-106">Новые возможности по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="15fe3-106">What is middleware</span></span>

<span data-ttu-id="15fe3-107">По промежуточного слоя — программное обеспечение, построенный в конвейер приложения для обработки запросов и ответов.</span><span class="sxs-lookup"><span data-stu-id="15fe3-107">Middleware is software that is assembled into an application pipeline to handle requests and responses.</span></span> <span data-ttu-id="15fe3-108">Каждый компонент:</span><span class="sxs-lookup"><span data-stu-id="15fe3-108">Each component:</span></span>

* <span data-ttu-id="15fe3-109">Выбирает, нужно ли передать запрос к следующему компоненту в конвейере.</span><span class="sxs-lookup"><span data-stu-id="15fe3-109">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="15fe3-110">Может выполнять работу, до и после вызова следующему компоненту в конвейере.</span><span class="sxs-lookup"><span data-stu-id="15fe3-110">Can perform work before and after the next component in the pipeline is invoked.</span></span> 

<span data-ttu-id="15fe3-111">Запрос делегаты используются для построения конвейера запросов.</span><span class="sxs-lookup"><span data-stu-id="15fe3-111">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="15fe3-112">Делегаты запрос обработки каждого HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="15fe3-112">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="15fe3-113">Запрос делегаты настраиваются с помощью [запуска](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions), [карты](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions), и [используйте](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions) методы расширения.</span><span class="sxs-lookup"><span data-stu-id="15fe3-113">Request delegates are configured using [Run](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions), [Map](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions), and [Use](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions) extension methods.</span></span> <span data-ttu-id="15fe3-114">Отдельный запрос делегат может быть указанную строку в качестве анонимного метода (называемые по промежуточного слоя в строке), или могут определяться в классе для повторного использования.</span><span class="sxs-lookup"><span data-stu-id="15fe3-114">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="15fe3-115">Эти классы многократного использования и анонимные методы в строке *по промежуточного слоя*, или *компонентов по промежуточного слоя*.</span><span class="sxs-lookup"><span data-stu-id="15fe3-115">These reusable classes and in-line anonymous methods are *middleware*, or *middleware components*.</span></span> <span data-ttu-id="15fe3-116">Каждый компонент по промежуточного слоя в конвейере отвечает за вызов следующему компоненту в конвейере и сокращенное вычисление цепочке при необходимости.</span><span class="sxs-lookup"><span data-stu-id="15fe3-116">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline, or short-circuiting the chain if appropriate.</span></span>

<span data-ttu-id="15fe3-117">[Миграция модули HTTP с по промежуточного слоя](../migration/http-modules.md) объясняются различия между конвейеры запроса в ASP.NET Core и более ранние версии, а также дополнительные примеры по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="15fe3-117">[Migrating HTTP Modules to Middleware](../migration/http-modules.md) explains the difference between request pipelines in ASP.NET Core and the previous versions and provides more middleware samples.</span></span>

## <a name="creating-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="15fe3-118">Создание конвейера по промежуточного слоя с IApplicationBuilder</span><span class="sxs-lookup"><span data-stu-id="15fe3-118">Creating a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="15fe3-119">Конвейер обработки запросов ASP.NET Core состоит из последовательности делегатов запроса, называемый один за другим, как показано на этой диаграмме (поток выполнения следующим черной стрелки):</span><span class="sxs-lookup"><span data-stu-id="15fe3-119">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other, as this diagram shows (the thread of execution follows the black arrows):</span></span>

![При обработке шаблона запроса отображение запросов, поступающих, обработки через три middlewares и ответ, так как приложение.](middleware/_static/request-delegate-pipeline.png)

<span data-ttu-id="15fe3-123">Все делегаты могут выполнять операции, до и после следующего делегата.</span><span class="sxs-lookup"><span data-stu-id="15fe3-123">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="15fe3-124">Делегат можно также принять решение не передавать запрос далее делегата, который называется сокращенного вычисления конвейер обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="15fe3-124">A delegate can also decide to not pass a request to the next delegate, which is called short-circuiting the request pipeline.</span></span> <span data-ttu-id="15fe3-125">Сокращенные вычисления часто является предпочтительным, так как позволяет избежать ненужной работы.</span><span class="sxs-lookup"><span data-stu-id="15fe3-125">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="15fe3-126">Например по промежуточного слоя статических файлов можно вернуть запрос статического файла и сокращенные оставшуюся часть конвейера.</span><span class="sxs-lookup"><span data-stu-id="15fe3-126">For example, the static file middleware can return a request for a static file and short-circuit the rest of the pipeline.</span></span> <span data-ttu-id="15fe3-127">Делегаты обработки исключений должны вызываться в начале конвейера, поэтому они могут перехватывать исключения, возникающие в более поздних стадиях конвейера.</span><span class="sxs-lookup"><span data-stu-id="15fe3-127">Exception-handling delegates need to be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="15fe3-128">Простейший возможных приложения ASP.NET Core устанавливает делегат одного запроса, который обрабатывает все запросы.</span><span class="sxs-lookup"><span data-stu-id="15fe3-128">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="15fe3-129">Этот вариант не содержит конвейер самого запроса.</span><span class="sxs-lookup"><span data-stu-id="15fe3-129">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="15fe3-130">Вместо этого один анонимная функция вызывается в ответ на каждый запрос HTTP.</span><span class="sxs-lookup"><span data-stu-id="15fe3-130">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[Main](middleware/sample/Middleware/Startup.cs)]

<span data-ttu-id="15fe3-131">Первый [приложения. Запустите](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions) делегата завершает конвейера.</span><span class="sxs-lookup"><span data-stu-id="15fe3-131">The first [app.Run](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions) delegate terminates the pipeline.</span></span>

<span data-ttu-id="15fe3-132">Можно соединить в цепочку несколько делегатов запроса вместе с [приложения. Используйте](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions).</span><span class="sxs-lookup"><span data-stu-id="15fe3-132">You can chain multiple request delegates together with [app.Use](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions).</span></span> <span data-ttu-id="15fe3-133">`next` Представляет параметр Далее делегата в конвейере.</span><span class="sxs-lookup"><span data-stu-id="15fe3-133">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="15fe3-134">(Помните, что краткой записи продаж по *не* вызов *Далее* параметров.) До и после следующего делегата обычно можно выполнять действия, как показано в этом примере.</span><span class="sxs-lookup"><span data-stu-id="15fe3-134">(Remember that you can short-circuit the pipeline by *not* calling the *next* parameter.) You can typically perform actions both before and after the next delegate, as this example demonstrates:</span></span>

[!code-csharp[Main](middleware/sample/Chain/Startup.cs?name=snippet1)]

>[!WARNING]
> <span data-ttu-id="15fe3-135">Не вызывайте `next.Invoke` после отправки ответа клиенту.</span><span class="sxs-lookup"><span data-stu-id="15fe3-135">Do not call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="15fe3-136">Изменения `HttpResponse` после запуска ответ будет создано исключение.</span><span class="sxs-lookup"><span data-stu-id="15fe3-136">Changes to `HttpResponse` after the response has started will throw an exception.</span></span> <span data-ttu-id="15fe3-137">Например изменения, такие как установка заголовки, код состояния, и т.д., возникает исключение.</span><span class="sxs-lookup"><span data-stu-id="15fe3-137">For example, changes such as setting headers, status code, etc,  will throw an exception.</span></span> <span data-ttu-id="15fe3-138">Запись в тексте ответа после вызова метода `next`:</span><span class="sxs-lookup"><span data-stu-id="15fe3-138">Writing to the response body after calling `next`:</span></span>
> - <span data-ttu-id="15fe3-139">Может вызвать нарушение протокола.</span><span class="sxs-lookup"><span data-stu-id="15fe3-139">May cause a protocol violation.</span></span> <span data-ttu-id="15fe3-140">Например, записи более чем указанных выше `content-length`.</span><span class="sxs-lookup"><span data-stu-id="15fe3-140">For example, writing more than the stated `content-length`.</span></span>
> - <span data-ttu-id="15fe3-141">Может привести к повреждению формат текста сообщения.</span><span class="sxs-lookup"><span data-stu-id="15fe3-141">May corrupt the body format.</span></span> <span data-ttu-id="15fe3-142">Например запись нижний колонтитул в HTML, CSS-файл.</span><span class="sxs-lookup"><span data-stu-id="15fe3-142">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="15fe3-143">[HttpResponse.HasStarted](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted) полезно подсказка для указания, если отправлены заголовки и/или записан в тексте.</span><span class="sxs-lookup"><span data-stu-id="15fe3-143">[HttpResponse.HasStarted](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted) is a useful hint to indicate if headers have been sent and/or the body has been written to.</span></span>

## <a name="ordering"></a><span data-ttu-id="15fe3-144">Упорядочение</span><span class="sxs-lookup"><span data-stu-id="15fe3-144">Ordering</span></span>

<span data-ttu-id="15fe3-145">Порядок, в котором компонентов по промежуточного слоя, добавляются в `Configure` метод определяет порядок, в котором они вызываются при запросах и обратный порядок для ответа.</span><span class="sxs-lookup"><span data-stu-id="15fe3-145">The order that middleware components are added in the `Configure` method defines the order in which they are invoked on requests, and the reverse order for the response.</span></span> <span data-ttu-id="15fe3-146">Этот порядок важен для безопасности, производительности и функциональности.</span><span class="sxs-lookup"><span data-stu-id="15fe3-146">This ordering is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="15fe3-147">Метод конфигурации (см. ниже) добавляет следующие компоненты по промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="15fe3-147">The Configure method (shown below) adds the following middleware components:</span></span>

1. <span data-ttu-id="15fe3-148">Обработка исключений и ошибок</span><span class="sxs-lookup"><span data-stu-id="15fe3-148">Exception/error handling</span></span>
2. <span data-ttu-id="15fe3-149">Статические файлового сервера</span><span class="sxs-lookup"><span data-stu-id="15fe3-149">Static file server</span></span>
3. <span data-ttu-id="15fe3-150">Проверка подлинности</span><span class="sxs-lookup"><span data-stu-id="15fe3-150">Authentication</span></span>
4. <span data-ttu-id="15fe3-151">MVC</span><span class="sxs-lookup"><span data-stu-id="15fe3-151">MVC</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="15fe3-152">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="15fe3-152">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)


```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseExceptionHandler("/Home/Error"); // Call first to catch exceptions
                                            // thrown in the following middleware.

    app.UseStaticFiles();                   // Return static files and end pipeline.

    app.UseAuthentication();               // Authenticate before you access
                                           // secure resources.

    app.UseMvcWithDefaultRoute();          // Add MVC to the request pipeline.
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="15fe3-153">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="15fe3-153">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseExceptionHandler("/Home/Error"); // Call first to catch exceptions
                                            // thrown in the following middleware.

    app.UseStaticFiles();                   // Return static files and end pipeline.

    app.UseIdentity();                     // Authenticate before you access
                                           // secure resources.

    app.UseMvcWithDefaultRoute();          // Add MVC to the request pipeline.
}
```

-----------

<span data-ttu-id="15fe3-154">В представленном выше коде `UseExceptionHandler` — это первый компонент по промежуточного слоя, добавить в конвейер, таким образом, он перехватывает все исключения, возникающие в последующих вызовах.</span><span class="sxs-lookup"><span data-stu-id="15fe3-154">In the code above, `UseExceptionHandler` is the first middleware component added to the pipeline—therefore, it catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="15fe3-155">По промежуточного слоя статических файлов называется ранних этапах конвейера, чтобы он мог обрабатывать запросы и краткой записи, минуя остальных компонентов.</span><span class="sxs-lookup"><span data-stu-id="15fe3-155">The static file middleware is called early in the pipeline so it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="15fe3-156">Предоставляет по промежуточного слоя статических файлов **не** проверки авторизации.</span><span class="sxs-lookup"><span data-stu-id="15fe3-156">The static file middleware provides **no** authorization checks.</span></span> <span data-ttu-id="15fe3-157">Все файлы, обслуживаемых, включая те, в разделе *wwwroot*, имеющихся в открытом доступе.</span><span class="sxs-lookup"><span data-stu-id="15fe3-157">Any files served by it, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="15fe3-158">В разделе [работа с файлами статического](xref:fundamentals/static-files) подход для защиты статических файлов.</span><span class="sxs-lookup"><span data-stu-id="15fe3-158">See [Working with static files](xref:fundamentals/static-files) for an approach to secure static files.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="15fe3-159">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="15fe3-159">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)


<span data-ttu-id="15fe3-160">Если запрос не обрабатывается по промежуточного слоя статических файлов, оно передается по промежуточного слоя идентификаторов (`app.UseAuthentication`), который выполняет проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="15fe3-160">If the request is not handled by the static file middleware, it's passed on to the Identity middleware (`app.UseAuthentication`), which performs authentication.</span></span> <span data-ttu-id="15fe3-161">Удостоверение не сокращенный запросы без проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="15fe3-161">Identity does not short-circuit unauthenticated requests.</span></span> <span data-ttu-id="15fe3-162">Несмотря на то, что запросы проверки подлинности удостоверения авторизации (и отклонения) возникает только в том случае, после MVC выбирает конкретных страниц Razor или контроллера и действия.</span><span class="sxs-lookup"><span data-stu-id="15fe3-162">Although Identity authenticates requests,  authorization (and rejection) occurs only after MVC selects a specific Razor Page or controller and action.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="15fe3-163">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="15fe3-163">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="15fe3-164">Если запрос не обрабатывается по промежуточного слоя статических файлов, оно передается по промежуточного слоя идентификаторов (`app.UseIdentity`), который выполняет проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="15fe3-164">If the request is not handled by the static file middleware, it's passed on to the Identity middleware (`app.UseIdentity`), which performs authentication.</span></span> <span data-ttu-id="15fe3-165">Удостоверение не сокращенный запросы без проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="15fe3-165">Identity does not short-circuit unauthenticated requests.</span></span> <span data-ttu-id="15fe3-166">Несмотря на то, что запросы проверки подлинности удостоверения авторизации (и отклонения) возникает только в том случае, после MVC выбирает конкретного контроллера и действия.</span><span class="sxs-lookup"><span data-stu-id="15fe3-166">Although Identity authenticates requests,  authorization (and rejection) occurs only after MVC selects a specific controller and action.</span></span>

-----------

<span data-ttu-id="15fe3-167">В следующем примере показано по промежуточного слоя, упорядочение, где обрабатываются запросы статических файлов по промежуточного слоя статических файлов до сжатия ответа по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="15fe3-167">The following example demonstrates a middleware ordering where requests for static files are handled by the static file middleware before the response compression middleware.</span></span> <span data-ttu-id="15fe3-168">Статические файлы, не сжимаются с порядком, по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="15fe3-168">Static files are not compressed with this ordering of the middleware.</span></span> <span data-ttu-id="15fe3-169">Отклики MVC [UseMvcWithDefaultRoute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) могут быть сжаты.</span><span class="sxs-lookup"><span data-stu-id="15fe3-169">The MVC responses from [UseMvcWithDefaultRoute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseStaticFiles();         // Static files not compressed
                                  // by middleware.
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

<a name="middleware-run-map-use"></a>

### <a name="use-run-and-map"></a><span data-ttu-id="15fe3-170">Использование, выполнения и карты</span><span class="sxs-lookup"><span data-stu-id="15fe3-170">Use, Run, and Map</span></span>

<span data-ttu-id="15fe3-171">Настройка конвейера HTTP с помощью `Use`, `Run`, и `Map`.</span><span class="sxs-lookup"><span data-stu-id="15fe3-171">You configure the HTTP pipeline using `Use`, `Run`, and `Map`.</span></span> <span data-ttu-id="15fe3-172">`Use` Метод может краткой записи конвейера (то есть, в том случае, если он не вызывает `next` делегат запроса).</span><span class="sxs-lookup"><span data-stu-id="15fe3-172">The `Use` method can short-circuit the pipeline (that is, if it does not call a `next` request delegate).</span></span> <span data-ttu-id="15fe3-173">`Run`является правилом, и некоторые компоненты по промежуточного слоя может привести `Run[Middleware]` методы, которые выполняются в конце конвейера.</span><span class="sxs-lookup"><span data-stu-id="15fe3-173">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="15fe3-174">`Map*`расширения используются как соглашение для ветвления в конвейер.</span><span class="sxs-lookup"><span data-stu-id="15fe3-174">`Map*` extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="15fe3-175">[Карта](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions) ветвей конвейера запросов на основе совпадений для данного пути запроса.</span><span class="sxs-lookup"><span data-stu-id="15fe3-175">[Map](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions) branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="15fe3-176">Если путь запроса начинается с заданного пути, выполняется ветвь.</span><span class="sxs-lookup"><span data-stu-id="15fe3-176">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[Main](middleware/sample/Chain/StartupMap.cs?name=snippet1)]

<span data-ttu-id="15fe3-177">Ниже приведены запросы и ответы от `http://localhost:1234` с помощью предыдущего кода:</span><span class="sxs-lookup"><span data-stu-id="15fe3-177">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="15fe3-178">Запрос</span><span class="sxs-lookup"><span data-stu-id="15fe3-178">Request</span></span> | <span data-ttu-id="15fe3-179">Ответ</span><span class="sxs-lookup"><span data-stu-id="15fe3-179">Response</span></span> |
| --- | --- |
| <span data-ttu-id="15fe3-180">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="15fe3-180">localhost:1234</span></span> | <span data-ttu-id="15fe3-181">Здравствуйте, из карты-делегата.</span><span class="sxs-lookup"><span data-stu-id="15fe3-181">Hello from non-Map delegate.</span></span>  |
| <span data-ttu-id="15fe3-182">localhost:1234 / map1</span><span class="sxs-lookup"><span data-stu-id="15fe3-182">localhost:1234/map1</span></span> | <span data-ttu-id="15fe3-183">Тест карты 1</span><span class="sxs-lookup"><span data-stu-id="15fe3-183">Map Test 1</span></span> |
| <span data-ttu-id="15fe3-184">localhost:1234 / map2</span><span class="sxs-lookup"><span data-stu-id="15fe3-184">localhost:1234/map2</span></span> | <span data-ttu-id="15fe3-185">Тест карты 2</span><span class="sxs-lookup"><span data-stu-id="15fe3-185">Map Test 2</span></span> |
| <span data-ttu-id="15fe3-186">localhost:1234 / map3</span><span class="sxs-lookup"><span data-stu-id="15fe3-186">localhost:1234/map3</span></span> | <span data-ttu-id="15fe3-187">Здравствуйте, из карты-делегата.</span><span class="sxs-lookup"><span data-stu-id="15fe3-187">Hello from non-Map delegate.</span></span>  |

<span data-ttu-id="15fe3-188">Когда `Map` — используется, соответствующие пути сегментами, удаляются из `HttpRequest.Path` и присоединяется к `HttpRequest.PathBase` для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="15fe3-188">When `Map` is used, the matched path segment(s) are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="15fe3-189">[MapWhen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapwhenextensions) ветвей конвейера запросов на основе результата заданного предиката.</span><span class="sxs-lookup"><span data-stu-id="15fe3-189">[MapWhen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapwhenextensions) branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="15fe3-190">Любой предикат типа `Func<HttpContext, bool>` может использоваться для сопоставления в новой ветви конвейера запросов.</span><span class="sxs-lookup"><span data-stu-id="15fe3-190">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="15fe3-191">В следующем примере предикат используется для определения наличия переменной строки запроса `branch`:</span><span class="sxs-lookup"><span data-stu-id="15fe3-191">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[Main](middleware/sample/Chain/StartupMapWhen.cs?name=snippet1)]

<span data-ttu-id="15fe3-192">Ниже приведены запросы и ответы от `http://localhost:1234` с помощью предыдущего кода:</span><span class="sxs-lookup"><span data-stu-id="15fe3-192">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="15fe3-193">Запрос</span><span class="sxs-lookup"><span data-stu-id="15fe3-193">Request</span></span> | <span data-ttu-id="15fe3-194">Ответ</span><span class="sxs-lookup"><span data-stu-id="15fe3-194">Response</span></span> |
| --- | --- |
| <span data-ttu-id="15fe3-195">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="15fe3-195">localhost:1234</span></span> | <span data-ttu-id="15fe3-196">Здравствуйте, из карты-делегата.</span><span class="sxs-lookup"><span data-stu-id="15fe3-196">Hello from non-Map delegate.</span></span>  |
| <span data-ttu-id="15fe3-197">localhost:1234 /? ветвь = master</span><span class="sxs-lookup"><span data-stu-id="15fe3-197">localhost:1234/?branch=master</span></span> | <span data-ttu-id="15fe3-198">Использовать ветви = master</span><span class="sxs-lookup"><span data-stu-id="15fe3-198">Branch used = master</span></span>|

<span data-ttu-id="15fe3-199">`Map`поддерживает вложение, например:</span><span class="sxs-lookup"><span data-stu-id="15fe3-199">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
       level1App.Map("/level2a", level2AApp => {
           // "/level1/level2a"
           //...
       });
       level1App.Map("/level2b", level2BApp => {
           // "/level1/level2b"
           //...
       });
   });
   ```

<span data-ttu-id="15fe3-200">`Map`можно сопоставить несколько сегментов одновременно, например:</span><span class="sxs-lookup"><span data-stu-id="15fe3-200">`Map` can also match multiple segments at once, for example:</span></span>

 ```csharp
app.Map("/level1/level2", HandleMultiSeg);
```

## <a name="built-in-middleware"></a><span data-ttu-id="15fe3-201">Встроенные по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="15fe3-201">Built-in middleware</span></span>

<span data-ttu-id="15fe3-202">ASP.NET Core поставляется со следующими компонентами по промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="15fe3-202">ASP.NET Core ships with the following middleware components:</span></span>

| <span data-ttu-id="15fe3-203">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="15fe3-203">Middleware</span></span> | <span data-ttu-id="15fe3-204">Описание:</span><span class="sxs-lookup"><span data-stu-id="15fe3-204">Description</span></span> |
| ----- | ------- |
| [<span data-ttu-id="15fe3-205">Проверка подлинности</span><span class="sxs-lookup"><span data-stu-id="15fe3-205">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="15fe3-206">Обеспечивает поддержку проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="15fe3-206">Provides authentication support.</span></span> |
| [<span data-ttu-id="15fe3-207">CORS</span><span class="sxs-lookup"><span data-stu-id="15fe3-207">CORS</span></span>](xref:security/cors) | <span data-ttu-id="15fe3-208">Настраивает общий доступ к ресурсам независимо от источника.</span><span class="sxs-lookup"><span data-stu-id="15fe3-208">Configures Cross-Origin Resource Sharing.</span></span> |
| [<span data-ttu-id="15fe3-209">Кэширование ответов</span><span class="sxs-lookup"><span data-stu-id="15fe3-209">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="15fe3-210">Предоставляет поддержку для кэширования ответов.</span><span class="sxs-lookup"><span data-stu-id="15fe3-210">Provides support for caching responses.</span></span> |
| [<span data-ttu-id="15fe3-211">Сжатие ответа</span><span class="sxs-lookup"><span data-stu-id="15fe3-211">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="15fe3-212">Поддерживает сжатие ответов.</span><span class="sxs-lookup"><span data-stu-id="15fe3-212">Provides support for compressing responses.</span></span> |
| [<span data-ttu-id="15fe3-213">Маршрутизация</span><span class="sxs-lookup"><span data-stu-id="15fe3-213">Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="15fe3-214">Определяет и ограничивает запрос маршрутов.</span><span class="sxs-lookup"><span data-stu-id="15fe3-214">Defines and constrains request routes.</span></span> |
| [<span data-ttu-id="15fe3-215">Сеанс</span><span class="sxs-lookup"><span data-stu-id="15fe3-215">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="15fe3-216">Предоставляет поддержку для управления пользовательских сеансов.</span><span class="sxs-lookup"><span data-stu-id="15fe3-216">Provides support for managing user sessions.</span></span> |
| [<span data-ttu-id="15fe3-217">Статические файлы</span><span class="sxs-lookup"><span data-stu-id="15fe3-217">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="15fe3-218">Обеспечивает поддержку для обслуживания статических файлов и просмотр каталогов.</span><span class="sxs-lookup"><span data-stu-id="15fe3-218">Provides support for serving static files and directory browsing.</span></span> |
| [<span data-ttu-id="15fe3-219">ПО промежуточного слоя для переопределения URL-адресов</span><span class="sxs-lookup"><span data-stu-id="15fe3-219">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="15fe3-220">Предоставляет поддержку для перезаписи URL-адресов и перенаправления запросов.</span><span class="sxs-lookup"><span data-stu-id="15fe3-220">Provides support for rewriting URLs and redirecting requests.</span></span> |

<a name="middleware-writing-middleware"></a>

## <a name="writing-middleware"></a><span data-ttu-id="15fe3-221">Записи по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="15fe3-221">Writing middleware</span></span>

<span data-ttu-id="15fe3-222">По промежуточного слоя обычно, содержащийся в классе, которое предоставляется с помощью метода расширения.</span><span class="sxs-lookup"><span data-stu-id="15fe3-222">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="15fe3-223">Рассмотрим следующий по промежуточного слоя, который задает язык и региональные параметры для текущего запроса из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="15fe3-223">Consider the following middleware, which sets the culture for the current request from the query string:</span></span>

[!code-csharp[Main](middleware/sample/Culture/StartupCulture.cs?name=snippet1)]

<span data-ttu-id="15fe3-224">Примечание: Приведенном выше примере используется для демонстрации создания компонентов по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="15fe3-224">Note: The sample code above is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="15fe3-225">В разделе [ глобализации и локализации](xref:fundamentals/localization) для поддержки встроенных локализации ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="15fe3-225">See [ Globalization and localization](xref:fundamentals/localization) for ASP.NET Core's built-in localization support.</span></span>

<span data-ttu-id="15fe3-226">Можно проверить по промежуточного слоя, передав в язык и региональные параметры, например `http://localhost:7997/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="15fe3-226">You can test the middleware by passing in the culture, for example `http://localhost:7997/?culture=no`.</span></span>

<span data-ttu-id="15fe3-227">Следующий код перемещает делегат по промежуточного слоя для класса:</span><span class="sxs-lookup"><span data-stu-id="15fe3-227">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddleware.cs)]

<span data-ttu-id="15fe3-228">Следующий метод расширения предоставляет по промежуточного слоя через [IApplicationBuilder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder):</span><span class="sxs-lookup"><span data-stu-id="15fe3-228">The following extension method exposes the middleware through [IApplicationBuilder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder):</span></span>

[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="15fe3-229">В следующем коде вызывается по промежуточного слоя из `Configure`:</span><span class="sxs-lookup"><span data-stu-id="15fe3-229">The following code calls the middleware from `Configure`:</span></span>

[!code-csharp[Main](middleware/sample/Culture/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="15fe3-230">По промежуточного слоя, должны соответствовать [явные зависимости принцип](http://deviq.com/explicit-dependencies-principle/) путем предоставления доступа к его зависимости в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="15fe3-230">Middleware should follow the [Explicit Dependencies Principle](http://deviq.com/explicit-dependencies-principle/) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="15fe3-231">По промежуточного слоя, создается один раз за *существования приложения*.</span><span class="sxs-lookup"><span data-stu-id="15fe3-231">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="15fe3-232">В разделе *зависимости на запрос* ниже, если вам нужно службы совместно с по промежуточного слоя внутри запроса.</span><span class="sxs-lookup"><span data-stu-id="15fe3-232">See *Per-request dependencies* below if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="15fe3-233">Устранить их зависимости от внедрения зависимостей через параметры конструктора компонентов по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="15fe3-233">Middleware components can resolve their dependencies from dependency injection through constructor parameters.</span></span> <span data-ttu-id="15fe3-234">[`UseMiddleware<T>`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary)может также принимать дополнительные параметры напрямую.</span><span class="sxs-lookup"><span data-stu-id="15fe3-234">[`UseMiddleware<T>`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary) can also accept additional parameters directly.</span></span>

### <a name="per-request-dependencies"></a><span data-ttu-id="15fe3-235">Зависимости на запрос</span><span class="sxs-lookup"><span data-stu-id="15fe3-235">Per-request dependencies</span></span>

<span data-ttu-id="15fe3-236">Так как по промежуточного слоя создается при запуске приложения, а не для каждого запроса, *область* время существования службы, используемые по промежуточного слоя конструкторы не являются общими с другими типами зависимостей введенные во время каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="15fe3-236">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors are not  shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="15fe3-237">Если необходимо предоставить *область* службы между по промежуточного слоя и другими типами, добавьте эти службы, чтобы `Invoke` сигнатуры метода.</span><span class="sxs-lookup"><span data-stu-id="15fe3-237">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="15fe3-238">`Invoke` Метод может принимать дополнительные параметры, которые заполняются внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="15fe3-238">The `Invoke` method can accept additional parameters that are populated by dependency injection.</span></span> <span data-ttu-id="15fe3-239">Пример:</span><span class="sxs-lookup"><span data-stu-id="15fe3-239">For example:</span></span>

```c#
public class MyMiddleware
{
    private readonly RequestDelegate _next;

    public MyMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="resources"></a><span data-ttu-id="15fe3-240">Ресурсы</span><span class="sxs-lookup"><span data-stu-id="15fe3-240">Resources</span></span>

* [<span data-ttu-id="15fe3-241">Пример кода, используемый в этом документе</span><span class="sxs-lookup"><span data-stu-id="15fe3-241">Sample code used in this doc</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample)
* [<span data-ttu-id="15fe3-242">Миграция с модулей HTTP на ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="15fe3-242">Migrating HTTP Modules to Middleware</span></span>](../migration/http-modules.md)
* [<span data-ttu-id="15fe3-243">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="15fe3-243">Application Startup</span></span>](startup.md)
* [<span data-ttu-id="15fe3-244">Параметры запроса</span><span class="sxs-lookup"><span data-stu-id="15fe3-244">Request Features</span></span>](request-features.md)