---
title: "Среда размещения ASP.NET Core в операционной системе Linux с Nginx"
description: "В статье описывается процедура настройки Nginx как обратного прокси-сервера на Ubuntu 16.04 для перенаправления трафика HTTP в веб-приложение ASP.NET Core, выполняемое в Kestrel."
keywords: "ASP.NET Core, Linux, nginx, Ubuntu, обратный прокси-сервер"
author: rick-anderson
ms.author: riande
manager: wpickett
ms.date: 08/21/2017
ms.topic: article
ms.assetid: 1c33e576-33de-481a-8ad3-896b94fde0e3
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/linuxproduction
ms.openlocfilehash: 7c7b949fc922c605aa4554c158200a4123c4eb1c
ms.sourcegitcommit: fc98e93464ccf37d9904e89a71cdddbd4bbdb86a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="set-up-a-hosting-environment-for-aspnet-core-on-linux-with-nginx-and-deploy-to-it"></a><span data-ttu-id="1d439-104">Настройка среды размещения для ASP.NET Core в операционной системе Linux с Nginx и развертывание в эту среду</span><span class="sxs-lookup"><span data-stu-id="1d439-104">Set up a hosting environment for ASP.NET Core on Linux with Nginx, and deploy to it</span></span>

<span data-ttu-id="1d439-105">Автор [Сурабх Ширхатти](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="1d439-105">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="1d439-106">В этом руководстве описывается настройка готовой к работе среды ASP.NET Core на сервере 16.04 Ubuntu.</span><span class="sxs-lookup"><span data-stu-id="1d439-106">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 Server.</span></span>

<span data-ttu-id="1d439-107">**Примечание**. Для Ubuntu 14.04 в качестве решения для мониторинга процесса Kestrel рекомендуется supervisord.</span><span class="sxs-lookup"><span data-stu-id="1d439-107">**Note:** For Ubuntu 14.04, supervisord is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="1d439-108">Решение systemd в Ubuntu 14.04 недоступно.</span><span class="sxs-lookup"><span data-stu-id="1d439-108">systemd is not available on Ubuntu 14.04.</span></span> [<span data-ttu-id="1d439-109">См. предыдущую версию этого документа</span><span class="sxs-lookup"><span data-stu-id="1d439-109">See previous version of this document</span></span>](https://github.com/aspnet/Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)

<span data-ttu-id="1d439-110">В этом руководстве рассматривается</span><span class="sxs-lookup"><span data-stu-id="1d439-110">This guide:</span></span>

* <span data-ttu-id="1d439-111">Размещение существующего приложения ASP.NET Core за обратным прокси-сервером</span><span class="sxs-lookup"><span data-stu-id="1d439-111">Places an existing ASP.NET Core application behind a reverse proxy server</span></span>
* <span data-ttu-id="1d439-112">Настройка обратного прокси-сервера для перенаправления запросов на веб-сервер Kestrel</span><span class="sxs-lookup"><span data-stu-id="1d439-112">Sets up the reverse proxy server to forward requests to the Kestrel web server</span></span>
* <span data-ttu-id="1d439-113">Проверка выполнения веб-приложения как управляющей программы при запуске системы</span><span class="sxs-lookup"><span data-stu-id="1d439-113">Ensures the web application runs on startup as a daemon</span></span>
* <span data-ttu-id="1d439-114">Настройка средства управления процессами в помощь с перезапуском веб-приложения</span><span class="sxs-lookup"><span data-stu-id="1d439-114">Configures a process management tool to help restart the web application</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1d439-115">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="1d439-115">Prerequisites</span></span>

1. <span data-ttu-id="1d439-116">Доступ к серверу Ubuntu 16.04 под учетной записи обычного пользователя с правами sudo</span><span class="sxs-lookup"><span data-stu-id="1d439-116">Access to an Ubuntu 16.04 Server with a standard user account with sudo privilege</span></span>
2. <span data-ttu-id="1d439-117">Существующее приложение ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d439-117">An existing ASP.NET Core application</span></span>

## <a name="copy-over-your-app"></a><span data-ttu-id="1d439-118">Копирование приложения</span><span class="sxs-lookup"><span data-stu-id="1d439-118">Copy over your app</span></span>

<span data-ttu-id="1d439-119">Выполните из среды разработки команду `dotnet publish`, чтобы упаковать приложение в отдельный каталог, который можно запустить на сервере.</span><span class="sxs-lookup"><span data-stu-id="1d439-119">Run `dotnet publish` from the dev environment to package an app into a self-contained directory that can run on the server.</span></span>

<span data-ttu-id="1d439-120">Скопируйте приложение ASP.NET Core на сервер с помощью инструмента (SCP, FTP и т. д.), интегрированного в ваш рабочий процесс.</span><span class="sxs-lookup"><span data-stu-id="1d439-120">Copy the ASP.NET Core app to the server using whatever tool (SCP, FTP, etc.) integrates into your workflow.</span></span> <span data-ttu-id="1d439-121">Протестируйте приложение, например</span><span class="sxs-lookup"><span data-stu-id="1d439-121">Test the app, for example:</span></span>
 - <span data-ttu-id="1d439-122">Выполните из командной строки команду `dotnet yourapp.dll`.</span><span class="sxs-lookup"><span data-stu-id="1d439-122">From the command line, run `dotnet yourapp.dll`</span></span>
 - <span data-ttu-id="1d439-123">В браузере откройте страницу `http://<serveraddress>:<port>`, чтобы убедиться, что приложение работает на платформе Linux.</span><span class="sxs-lookup"><span data-stu-id="1d439-123">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux.</span></span> 
 
## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="1d439-124">Настройка обратного прокси-сервера</span><span class="sxs-lookup"><span data-stu-id="1d439-124">Configure a reverse proxy server</span></span>

<span data-ttu-id="1d439-125">Обратный прокси-сервер — это общая настройка для обслуживания динамических веб-приложений.</span><span class="sxs-lookup"><span data-stu-id="1d439-125">A reverse proxy is a common setup for serving dynamic web applications.</span></span> <span data-ttu-id="1d439-126">Обратный прокси-сервер завершает HTTP-запрос и перенаправляет его в приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1d439-126">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core application.</span></span>

### <a name="why-use-a-reverse-proxy-server"></a><span data-ttu-id="1d439-127">Для чего нужен обратный прокси-сервер?</span><span class="sxs-lookup"><span data-stu-id="1d439-127">Why use a reverse proxy server?</span></span>

<span data-ttu-id="1d439-128">Kestrel превосходно справляется с обслуживанием динамического содержимого из ASP.NET Core; при этом компоненты для работы с веб-службами не настолько функциональны, как серверы типа IIS, Apache или Nginx.</span><span class="sxs-lookup"><span data-stu-id="1d439-128">Kestrel is great for serving dynamic content from ASP.NET Core; however, the web serving parts aren’t as feature rich as servers like IIS, Apache, or Nginx.</span></span> <span data-ttu-id="1d439-129">Обратный прокси-сервер может облегчить такую работу, как обслуживание статического содержимого, кэширование запросов, сжатие запросов и завершение SSL с HTTP-сервера.</span><span class="sxs-lookup"><span data-stu-id="1d439-129">A reverse proxy server can offload work like serving static content, caching requests, compressing requests, and SSL termination from the HTTP server.</span></span> <span data-ttu-id="1d439-130">Обратный прокси-сервер можно разместить на отдельном компьютере или развернуть параллельно с HTTP-сервером.</span><span class="sxs-lookup"><span data-stu-id="1d439-130">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="1d439-131">В контексте данного руководства используется отдельный экземпляр Nginx.</span><span class="sxs-lookup"><span data-stu-id="1d439-131">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="1d439-132">Он выполняется на том же сервере, что и HTTP-сервер.</span><span class="sxs-lookup"><span data-stu-id="1d439-132">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="1d439-133">Вы можете выбрать другую конфигурацию в зависимости от своих условий.</span><span class="sxs-lookup"><span data-stu-id="1d439-133">Based on your requirements, you may choose a different setup.</span></span>

<span data-ttu-id="1d439-134">Так как запросы перенаправляются обратным прокси-сервером, используйте ПО промежуточного слоя `ForwardedHeaders` из пакета `Microsoft.AspNetCore.HttpOverrides`.</span><span class="sxs-lookup"><span data-stu-id="1d439-134">Because requests are forwarded by reverse proxy, use the `ForwardedHeaders` middleware from the `Microsoft.AspNetCore.HttpOverrides` package.</span></span> <span data-ttu-id="1d439-135">Это ПО обновляет `Request.Scheme`, используя заголовок `X-Forwarded-Proto`, что обеспечивает правильную работу URI перенаправления и других политик безопасности.</span><span class="sxs-lookup"><span data-stu-id="1d439-135">This middleware updates `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="1d439-136">При настройке обратного прокси-сервера для промежуточного ПО проверки подлинности необходимо в первую очередь выполнить команду `UseForwardedHeaders`.</span><span class="sxs-lookup"><span data-stu-id="1d439-136">When setting up a reverse proxy server, the authentication middleware needs `UseForwardedHeaders` to run first.</span></span> <span data-ttu-id="1d439-137">Такой порядок гарантирует, что промежуточное ПО проверки подлинности получит соответствующие значения и сформирует верные URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="1d439-137">This ordering ensures that the authentication middleware can consume the affected values and generate correct redirect URIs.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1d439-138">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1d439-138">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1d439-139">Вызовите метод `UseForwardedHeaders` (в методе `Configure` файла *Startup.cs*) перед вызовом `UseAuthentication` или другого ПО промежуточного слоя, предназначенного для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="1d439-139">Invoke the `UseForwardedHeaders` method (in the `Configure` method of *Startup.cs*) before calling `UseAuthentication` or similar authentication scheme middleware:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1d439-140">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1d439-140">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1d439-141">Вызовите метод `UseForwardedHeaders` (в методе `Configure` файла *Startup.cs*) перед вызовом `UseIdentity` и `UseFacebookAuthentication` или другого ПО промежуточного слоя, предназначенного для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="1d439-141">Invoke the `UseForwardedHeaders` method (in the `Configure` method of *Startup.cs*) before calling `UseIdentity` and `UseFacebookAuthentication` or similar authentication scheme middleware:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseIdentity();
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

### <a name="install-nginx"></a><span data-ttu-id="1d439-142">Установка Nginx</span><span class="sxs-lookup"><span data-stu-id="1d439-142">Install Nginx</span></span>

```bash
sudo apt-get install nginx
```

> [!NOTE]
> <span data-ttu-id="1d439-143">Для установки дополнительных модулей Nginx может потребоваться сборка Nginx из источника.</span><span class="sxs-lookup"><span data-stu-id="1d439-143">If you plan to install optional Nginx modules, you may be required to build Nginx from source.</span></span>

<span data-ttu-id="1d439-144">Установите Nginx с помощью команды `apt-get`.</span><span class="sxs-lookup"><span data-stu-id="1d439-144">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="1d439-145">Программа установки создает сценарий инициализации System V, который запускает Nginx как управляющую программу Nginx при запуске системы.</span><span class="sxs-lookup"><span data-stu-id="1d439-145">The installer creates a System V init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="1d439-146">Так как Nginx устанавливается впервые, запустите его напрямую, выполнив следующую команду.</span><span class="sxs-lookup"><span data-stu-id="1d439-146">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="1d439-147">В браузере должна открыться стартовая страница Nginx по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="1d439-147">Verify a browser displays the default landing page for Nginx.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="1d439-148">Настройка Nginx</span><span class="sxs-lookup"><span data-stu-id="1d439-148">Configure Nginx</span></span>

<span data-ttu-id="1d439-149">Чтобы настроить Nginx как обратный прокси-сервер для перенаправления запросов в наше приложение ASP.NET Core, измените файл `/etc/nginx/sites-available/default`.</span><span class="sxs-lookup"><span data-stu-id="1d439-149">To configure Nginx as a reverse proxy to forward requests to our ASP.NET Core application, modify `/etc/nginx/sites-available/default`.</span></span> <span data-ttu-id="1d439-150">Откройте этот файл в текстовом редакторе и замените его содержимое на следующий код.</span><span class="sxs-lookup"><span data-stu-id="1d439-150">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen 80;
    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host $http_host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

<span data-ttu-id="1d439-151">Этот файл конфигурации Nginx перенаправляет входящий публичный трафик с порта `80` на порт `5000`.</span><span class="sxs-lookup"><span data-stu-id="1d439-151">This Nginx configuration file forwards incoming public traffic from port `80` to port `5000`.</span></span>

<span data-ttu-id="1d439-152">Закончив внесение изменений в конфигурацию Nginx, выполните команду `sudo nginx -t`, чтобы проверить синтаксис файлов конфигурации.</span><span class="sxs-lookup"><span data-stu-id="1d439-152">Once you have completed making changes to your Nginx configuration, you can run `sudo nginx -t` to verify the syntax of your configuration files.</span></span> <span data-ttu-id="1d439-153">Если проверка файла конфигурации прошла успешно, укажите Nginx, что нужно принять изменения, выполнив команду `sudo nginx -s reload`.</span><span class="sxs-lookup"><span data-stu-id="1d439-153">If the configuration file test is successful, you can ask Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

## <a name="monitoring-our-application"></a><span data-ttu-id="1d439-154">Мониторинг приложения</span><span class="sxs-lookup"><span data-stu-id="1d439-154">Monitoring our application</span></span>

<span data-ttu-id="1d439-155">Теперь Nginx настроен на перенаправление запросов, отправленных в `http://yourhost:80`, в приложение ASP.NET Core, выполняемое в Kestrel по адресу `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="1d439-155">Nginx is now setup to forward requests made to `http://yourhost:80` on to the ASP.NET Core application running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="1d439-156">При этом Nginx не настроен для управления процессом Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1d439-156">However, Nginx is not set up to manage the Kestrel process.</span></span> <span data-ttu-id="1d439-157">Для запуска и мониторинга соответствующего веб-приложения можно воспользоваться *systemd* и создать файл службы.</span><span class="sxs-lookup"><span data-stu-id="1d439-157">You can use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="1d439-158">*systemd* — это система инициализации, предоставляющая различные функции для запуска и остановки процессов, а также управления ими.</span><span class="sxs-lookup"><span data-stu-id="1d439-158">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="1d439-159">Создание файла службы</span><span class="sxs-lookup"><span data-stu-id="1d439-159">Create the service file</span></span>

<span data-ttu-id="1d439-160">Создайте файл определения службы.</span><span class="sxs-lookup"><span data-stu-id="1d439-160">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

<span data-ttu-id="1d439-161">Пример файла службы для нашего приложения.</span><span class="sxs-lookup"><span data-stu-id="1d439-161">The following is an example service file for our application:</span></span>

```ini
[Unit]
Description=Example .NET Web API Application running on Ubuntu

[Service]
WorkingDirectory=/var/aspnetcore/hellomvc
ExecStart=/usr/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
Restart=always
RestartSec=10  # Restart service after 10 seconds if dotnet service crashes
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="1d439-162">**Примечание**. Если пользователь *www-data* в вашей конфигурации не используется, необходимо сначала создать определенного выше пользователя, а затем предоставить ему необходимые права владения для файлов.</span><span class="sxs-lookup"><span data-stu-id="1d439-162">**Note:** If the user *www-data* is not used by your configuration, the user defined here must be created first and given proper ownership for files.</span></span>

<span data-ttu-id="1d439-163">Сохраните файл и включите службу.</span><span class="sxs-lookup"><span data-stu-id="1d439-163">Save the file, and enable the service.</span></span>

```bash
systemctl enable kestrel-hellomvc.service
```

<span data-ttu-id="1d439-164">Запустите службу и убедитесь, что она работает.</span><span class="sxs-lookup"><span data-stu-id="1d439-164">Start the service and verify that it is running.</span></span>

```
systemctl start kestrel-hellomvc.service
systemctl status kestrel-hellomvc.service

● kestrel-hellomvc.service - Example .NET Web API Application running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-hellomvc.service
            └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

<span data-ttu-id="1d439-165">Теперь, когда обратный прокси-сервер настроен, а управление Kestrel осуществляется системой systemd, веб-приложение полностью настроено и готово для доступа из браузера на локальном компьютере по адресу `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="1d439-165">With the reverse proxy configured and Kestrel managed through systemd, the web application is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="1d439-166">Оно также доступно для удаленных компьютеров, несмотря на наличие блокирующих трафик межсетевых экранов.</span><span class="sxs-lookup"><span data-stu-id="1d439-166">It is also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="1d439-167">Если в ответах есть заголовок `Server`, это означает, что приложение ASP.NET Core обслуживает Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1d439-167">Inspecting the response headers, the `Server` header shows the ASP.NET Core application being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a><span data-ttu-id="1d439-168">Просмотр журналов</span><span class="sxs-lookup"><span data-stu-id="1d439-168">Viewing logs</span></span>

<span data-ttu-id="1d439-169">Так как веб-приложение, использующее Kestrel, управляется с помощью `systemd`, все события и процессы регистрируются в централизованном журнале.</span><span class="sxs-lookup"><span data-stu-id="1d439-169">Since the web application using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="1d439-170">При этом журнал содержит все записи обо всех службах и процессах, управляемых `systemd`.</span><span class="sxs-lookup"><span data-stu-id="1d439-170">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="1d439-171">Чтобы просмотреть элементы, связанные с `kestrel-hellomvc.service`, используйте следующую команду.</span><span class="sxs-lookup"><span data-stu-id="1d439-171">To view the `kestrel-hellomvc.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-hellomvc.service
```

<span data-ttu-id="1d439-172">Кроме того, количество возвращаемых записей можно уменьшить, указав параметры времени, например `--since today`, `--until 1 hour ago` или их комбинацию.</span><span class="sxs-lookup"><span data-stu-id="1d439-172">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-our-application"></a><span data-ttu-id="1d439-173">Защита приложения</span><span class="sxs-lookup"><span data-stu-id="1d439-173">Securing our application</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="1d439-174">Включение AppArmor</span><span class="sxs-lookup"><span data-stu-id="1d439-174">Enable AppArmor</span></span>

<span data-ttu-id="1d439-175">Начиная с версии 2.6, ядро Linux включает платформу модулей безопасности Linux (LSM).</span><span class="sxs-lookup"><span data-stu-id="1d439-175">Linux Security Modules (LSM) is a framework that is part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="1d439-176">LSM поддерживают различные реализации модулей безопасности.</span><span class="sxs-lookup"><span data-stu-id="1d439-176">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="1d439-177">[AppArmor](https://wiki.ubuntu.com/AppArmor) — это LSM, который реализует систему обязательного контроля доступа, позволяющую ограничивать программу определенным набором ресурсов.</span><span class="sxs-lookup"><span data-stu-id="1d439-177">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="1d439-178">Убедитесь, что AppArmor включен и правильно настроен.</span><span class="sxs-lookup"><span data-stu-id="1d439-178">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configuring-our-firewall"></a><span data-ttu-id="1d439-179">Настройка межсетевого экрана</span><span class="sxs-lookup"><span data-stu-id="1d439-179">Configuring our firewall</span></span>

<span data-ttu-id="1d439-180">Закройте все внешние порты, которые не используются.</span><span class="sxs-lookup"><span data-stu-id="1d439-180">Close off all external ports that are not in use.</span></span> <span data-ttu-id="1d439-181">Незамысловатый межсетевой экран (ufw) позволяет взаимодействовать с `iptables`, предоставляя интерфейс командной строки для настройки межсетевого экрана.</span><span class="sxs-lookup"><span data-stu-id="1d439-181">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a command line interface for configuring the firewall.</span></span> <span data-ttu-id="1d439-182">Убедитесь, что настройки `ufw` пропускают трафик на все необходимые порты.</span><span class="sxs-lookup"><span data-stu-id="1d439-182">Verify that `ufw` is configured to allow traffic on any ports you need.</span></span>

```bash
sudo apt-get install ufw
sudo ufw enable

sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

### <a name="securing-nginx"></a><span data-ttu-id="1d439-183">Защита Nginx</span><span class="sxs-lookup"><span data-stu-id="1d439-183">Securing Nginx</span></span>

<span data-ttu-id="1d439-184">В дистрибутиве Nginx по умолчанию SSL не включен.</span><span class="sxs-lookup"><span data-stu-id="1d439-184">The default distribution of Nginx doesn't enable SSL.</span></span> <span data-ttu-id="1d439-185">Чтобы включить дополнительные функции безопасности, выполните сборку из исходного файла.</span><span class="sxs-lookup"><span data-stu-id="1d439-185">To enable additional security features, build from source.</span></span>

#### <a name="download-the-source-and-install-the-build-dependencies"></a><span data-ttu-id="1d439-186">Загрузка исходного файла и установка зависимостей сборки</span><span class="sxs-lookup"><span data-stu-id="1d439-186">Download the source and install the build dependencies</span></span>

```bash
# Install the build dependencies
sudo apt-get update
sudo apt-get install build-essential zlib1g-dev libpcre3-dev libssl-dev libxslt1-dev libxml2-dev libgd2-xpm-dev libgeoip-dev libgoogle-perftools-dev libperl-dev

# Download nginx 1.10.0 or latest
wget http://www.nginx.org/download/nginx-1.10.0.tar.gz
tar zxf nginx-1.10.0.tar.gz
```

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="1d439-187">Изменение имени ответа Nginx</span><span class="sxs-lookup"><span data-stu-id="1d439-187">Change the Nginx response name</span></span>

<span data-ttu-id="1d439-188">Внесите изменения в файл *src/http/ngx_http_header_filter_module.c*:</span><span class="sxs-lookup"><span data-stu-id="1d439-188">Edit *src/http/ngx_http_header_filter_module.c*:</span></span>

```c
static char ngx_http_server_string[] = "Server: Your Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Your Web Server" CRLF;
```

#### <a name="configure-the-options-and-build"></a><span data-ttu-id="1d439-189">Настройка параметров и сборки</span><span class="sxs-lookup"><span data-stu-id="1d439-189">Configure the options and build</span></span>

<span data-ttu-id="1d439-190">Для регулярных выражений требуется библиотека PCRE.</span><span class="sxs-lookup"><span data-stu-id="1d439-190">The PCRE library is required for regular expressions.</span></span> <span data-ttu-id="1d439-191">Регулярные выражения используются в директиве местонахождения для модуля ngx_http_rewrite_module.</span><span class="sxs-lookup"><span data-stu-id="1d439-191">Regular expressions are used in the location directive for the ngx_http_rewrite_module.</span></span> <span data-ttu-id="1d439-192">Модуль http_ssl_module добавляет поддержку протокола HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1d439-192">The http_ssl_module adds HTTPS protocol support.</span></span>

<span data-ttu-id="1d439-193">Для дополнительной защиты приложения можно использовать межсетевой экран для веб-приложений, такой как *ModSecurity*.</span><span class="sxs-lookup"><span data-stu-id="1d439-193">Consider using a web application firewall like *ModSecurity* to harden your application.</span></span>

```bash
./configure
--with-pcre=../pcre-8.38
--with-zlib=../zlib-1.2.8
--with-http_ssl_module
--with-stream
--with-mail=dynamic
```

#### <a name="configure-ssl"></a><span data-ttu-id="1d439-194">Настройка SSL</span><span class="sxs-lookup"><span data-stu-id="1d439-194">Configure SSL</span></span>

* <span data-ttu-id="1d439-195">Настройте сервер для прослушивания трафика HTTPS через порт `443`, указав действительный сертификат, выпущенный доверенным центром сертификации (ЦС).</span><span class="sxs-lookup"><span data-stu-id="1d439-195">Configure your server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="1d439-196">Укрепите защиту, применив некоторые методы, показанные в представленном ниже файле */etc/nginx/nginx.conf*.</span><span class="sxs-lookup"><span data-stu-id="1d439-196">Harden your security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="1d439-197">Это может быть выбор более строгого шифра и перенаправление всего HTTP-трафика в HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1d439-197">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="1d439-198">При добавлении заголовка `HTTP Strict-Transport-Security` (HSTS) все последующие запросы клиента будут проходить только по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1d439-198">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS only.</span></span>

* <span data-ttu-id="1d439-199">Если в дальнейшем вы планируете отключить SSL, не добавляйте заголовок Strict-Transport-Security или выберите соответствующий `max-age`.</span><span class="sxs-lookup"><span data-stu-id="1d439-199">Do not add the Strict-Transport-Security header or chose an appropriate `max-age` if you plan to disable SSL in the future.</span></span>

<span data-ttu-id="1d439-200">Добавьте файл конфигурации */etc/nginx/proxy.conf*:</span><span class="sxs-lookup"><span data-stu-id="1d439-200">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[Main](linuxproduction/proxy.conf)]

<span data-ttu-id="1d439-201">Измените файл конфигурации */etc/nginx/proxy.conf*.</span><span class="sxs-lookup"><span data-stu-id="1d439-201">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="1d439-202">В этом примере показаны разделы `http` и `server` одного и того же файла конфигурации.</span><span class="sxs-lookup"><span data-stu-id="1d439-202">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[Main](../publishing/linuxproduction/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="1d439-203">Защита Nginx от кликджекинга</span><span class="sxs-lookup"><span data-stu-id="1d439-203">Secure Nginx from clickjacking</span></span>
<span data-ttu-id="1d439-204">Кликджекинг — это способ обмана, предназначенный для перехвата кликов пострадавшего пользователя.</span><span class="sxs-lookup"><span data-stu-id="1d439-204">Clickjacking is a malicious technique to collect an infected user's clicks.</span></span> <span data-ttu-id="1d439-205">Кликджекинг обманом заставляет пользователей (посетителей) щелкать зараженные сайты.</span><span class="sxs-lookup"><span data-stu-id="1d439-205">Clickjacking tricks the victim (visitor) into clicking on an infected site.</span></span> <span data-ttu-id="1d439-206">Для защиты своего сайта используйте X-FRAME-OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="1d439-206">Use X-FRAME-OPTIONS to secure your site.</span></span>

<span data-ttu-id="1d439-207">Измените файл *nginx.conf*.</span><span class="sxs-lookup"><span data-stu-id="1d439-207">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="1d439-208">Добавьте строку `add_header X-Frame-Options "SAMEORIGIN";` и сохраните файл, а затем перезапустите Nginx.</span><span class="sxs-lookup"><span data-stu-id="1d439-208">Add the line `add_header X-Frame-Options "SAMEORIGIN";` and save the file, then restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="1d439-209">Сканирование типа MIME</span><span class="sxs-lookup"><span data-stu-id="1d439-209">MIME-type sniffing</span></span>

<span data-ttu-id="1d439-210">Этот заголовок предотвращает MIME-сканирование ответов с указанным типом содержимого в большинстве браузеров, запрещая браузеру переопределять тип содержимого ответа.</span><span class="sxs-lookup"><span data-stu-id="1d439-210">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="1d439-211">Параметр `nosniff` означает, что, если сервер определяет содержимое как text/html, браузер будет обрабатывать его как text/html.</span><span class="sxs-lookup"><span data-stu-id="1d439-211">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="1d439-212">Измените файл *nginx.conf*.</span><span class="sxs-lookup"><span data-stu-id="1d439-212">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="1d439-213">Добавьте строку `add_header X-Content-Type-Options "nosniff";` и сохраните файл, а затем перезапустите Nginx.</span><span class="sxs-lookup"><span data-stu-id="1d439-213">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>