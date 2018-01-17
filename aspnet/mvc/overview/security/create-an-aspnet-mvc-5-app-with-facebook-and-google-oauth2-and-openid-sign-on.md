---
uid: mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on
title: "Создать MVC 5 приложения с Facebook, Twitter, LinkedIn и Google OAuth2 единого входа (C#) | Документы Microsoft"
author: Rick-Anderson
description: "Этот учебник показывает, как для создания веб-приложения ASP.NET MVC 5, позволяющий пользователям выполнять вход с использованием OAuth 2.0 с учетными данными из внешних предварительная пр..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/03/2015
ms.topic: article
ms.assetid: 81ee500f-fc37-40d6-8722-f1b64720fbb6
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on
msc.type: authoredcontent
ms.openlocfilehash: aaa061e61b9bab5b33083851624f0487b2cf6473
ms.sourcegitcommit: ccf08615ad59bc6f654560de33b93396113a2eb0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2017
---
<a name="create-an-aspnet-mvc-5-app-with-facebook-twitter-linkedin-and-google-oauth2-sign-on-c"></a><span data-ttu-id="32635-103">Создание приложения ASP.NET MVC 5 с Facebook, Twitter, LinkedIn и Google OAuth2 единого входа (C#)</span><span class="sxs-lookup"><span data-stu-id="32635-103">Create an ASP.NET MVC 5 App with Facebook, Twitter, LinkedIn and Google OAuth2 Sign-on (C#)</span></span>
====================
<span data-ttu-id="32635-104">По [Рик Андерсон](https://github.com/Rick-Anderson)</span><span class="sxs-lookup"><span data-stu-id="32635-104">by [Rick Anderson](https://github.com/Rick-Anderson)</span></span>

> <span data-ttu-id="32635-105">Этого учебника показано, как построить веб-приложение ASP.NET MVC 5, который позволяет пользователям входить в систему с помощью [OAuth 2.0](http://oauth.net/2/) с учетными данными из внешнего поставщика аутентификации, например Facebook, Twitter, LinkedIn, Microsoft или Google.</span><span class="sxs-lookup"><span data-stu-id="32635-105">This tutorial shows you how to build an ASP.NET MVC 5 web application that enables users to log in using [OAuth 2.0](http://oauth.net/2/) with credentials from an external authentication provider, such as Facebook, Twitter, LinkedIn, Microsoft, or Google.</span></span> <span data-ttu-id="32635-106">Для простоты учебнике рассматривается работа с учетными данными с Facebook и Google.</span><span class="sxs-lookup"><span data-stu-id="32635-106">For simplicity, this tutorial focuses on working with credentials from Facebook and Google.</span></span>
> 
> <span data-ttu-id="32635-107">Включение этих учетных данных на веб-узлах обеспечивает значительное преимущество, так как миллионы пользователи уже имеют учетные записи, с помощью этих внешних поставщиков.</span><span class="sxs-lookup"><span data-stu-id="32635-107">Enabling these credentials in your web sites provides a significant advantage because millions of users already have accounts with these external providers.</span></span> <span data-ttu-id="32635-108">Эти пользователи могут иметь более необходимо, чтобы зарегистрироваться для веб-узла, если они не нужно создавать и запоминать новый набор учетных данных.</span><span class="sxs-lookup"><span data-stu-id="32635-108">These users may be more inclined to sign up for your site if they do not have to create and remember a new set of credentials.</span></span>
> 
> <span data-ttu-id="32635-109">См. также [приложения ASP.NET MVC 5 с помощью SMS и электронной почты двухфакторной проверки подлинности](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication.md).</span><span class="sxs-lookup"><span data-stu-id="32635-109">See also [ASP.NET MVC 5 app with SMS and email Two-Factor Authentication](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication.md).</span></span>
> 
> <span data-ttu-id="32635-110">Учебник также показано, как добавить данные профиля для пользователя и как использовать API членства для добавления ролей.</span><span class="sxs-lookup"><span data-stu-id="32635-110">The tutorial also shows how to add profile data for the user, and how to use the Membership API to add roles.</span></span> <span data-ttu-id="32635-111">Это руководство было написано с [Рик Андерсон](https://blogs.msdn.com/rickAndy) (следуйте мне в Twitter: [ @RickAndMSFT ](https://twitter.com/RickAndMSFT) ).</span><span class="sxs-lookup"><span data-stu-id="32635-111">This tutorial was written by [Rick Anderson](https://blogs.msdn.com/rickAndy) ( Please follow me on Twitter: [@RickAndMSFT](https://twitter.com/RickAndMSFT) ).</span></span>


<a id="start"></a>
## <a name="getting-started"></a><span data-ttu-id="32635-112">Начало работы</span><span class="sxs-lookup"><span data-stu-id="32635-112">Getting Started</span></span>

<span data-ttu-id="32635-113">Начните с установки и запуска [Visual Studio Express 2013 для Web](https://go.microsoft.com/fwlink/?LinkId=299058) или [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566).</span><span class="sxs-lookup"><span data-stu-id="32635-113">Start by installing and running [Visual Studio Express 2013 for Web](https://go.microsoft.com/fwlink/?LinkId=299058) or [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566).</span></span> <span data-ttu-id="32635-114">Установка Visual Studio [2013 обновление 3](https://go.microsoft.com/fwlink/?LinkId=390521) или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="32635-114">Install Visual Studio [2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390521) or higher.</span></span> <span data-ttu-id="32635-115">Для получения справки по Dropbox, GitHub, Linkedin, Instagram, буфера, salesforce, поток данных, Exchange стека, Tripit, twitch, Twitter, Yahoo и многое другое см. в этой [универсальное руководство по](http://www.oauthforaspnet.com/).</span><span class="sxs-lookup"><span data-stu-id="32635-115">For help with Dropbox, GitHub, Linkedin, Instagram, buffer, salesforce, STEAM, Stack Exchange, Tripit, twitch, Twitter, Yahoo and more, see this [one stop guide](http://www.oauthforaspnet.com/).</span></span>

> [!NOTE]
> <span data-ttu-id="32635-116">Необходимо установить Visual Studio [2013 обновление 3](https://go.microsoft.com/fwlink/?LinkId=390521) или более поздней версии с помощью Google OAuth 2 и отлаживать локально без предупреждения SSL.</span><span class="sxs-lookup"><span data-stu-id="32635-116">You must install Visual Studio [2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390521) or higher to use Google OAuth 2 and to debug locally without SSL warnings.</span></span>


<span data-ttu-id="32635-117">Нажмите кнопку **новый проект** из **запустить** страницы, или можно использовать меню и выберите **файл**, а затем **новый проект**.</span><span class="sxs-lookup"><span data-stu-id="32635-117">Click **New Project** from the **Start** page, or you can use the menu and select **File**, and then **New Project**.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image1.png)  
 

<a id="1st"></a>
## <a name="creating-your-first-application"></a><span data-ttu-id="32635-118">Создание первого приложения</span><span class="sxs-lookup"><span data-stu-id="32635-118">Creating Your First Application</span></span>

<span data-ttu-id="32635-119">Нажмите кнопку **новый проект**, а затем выберите **Visual C#** слева, затем **Web** , а затем выберите **веб-приложение ASP.NET**.</span><span class="sxs-lookup"><span data-stu-id="32635-119">Click **New Project**, then select **Visual C#** on the left, then **Web** and then select **ASP.NET Web Application**.</span></span> <span data-ttu-id="32635-120">Назовите проект «MvcAuth» и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="32635-120">Name your project "MvcAuth" and then click **OK**.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image2.png)

<span data-ttu-id="32635-121">В **новый проект ASP.NET** диалоговое окно, нажмите кнопку **MVC**.</span><span class="sxs-lookup"><span data-stu-id="32635-121">In the **New ASP.NET Project** dialog, click **MVC**.</span></span> <span data-ttu-id="32635-122">Если проверка подлинности не **отдельных учетных записей пользователей**, нажмите кнопку **изменить аутентификацию** и выберите пункт **индивидуальные учетные записи**.</span><span class="sxs-lookup"><span data-stu-id="32635-122">If the Authentication is not **Individual User Accounts**, click the **Change Authentication** button and select **Individual User Accounts**.</span></span> <span data-ttu-id="32635-123">Проверив **узел в облаке**, приложение будет очень легко разместить в Azure.</span><span class="sxs-lookup"><span data-stu-id="32635-123">By checking **Host in the cloud**, the app will be very easy to host in Azure.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image3.png)

<span data-ttu-id="32635-124">Если вы выбрали **узел в облаке**, завершения Настройка диалогового окна.</span><span class="sxs-lookup"><span data-stu-id="32635-124">If you selected **Host in the cloud**, complete the configure dialog.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image4.png)


### <a name="use-nuget-to-update-to-the-latest-owin-middleware"></a><span data-ttu-id="32635-125">Использовать NuGet для обновления до последней по промежуточного слоя OWIN</span><span class="sxs-lookup"><span data-stu-id="32635-125">Use NuGet to update to the latest OWIN middleware</span></span>

<span data-ttu-id="32635-126">Используйте диспетчер пакетов NuGet для обновления [по промежуточного слоя OWIN](../../../aspnet/overview/owin-and-katana/getting-started-with-owin-and-katana.md).</span><span class="sxs-lookup"><span data-stu-id="32635-126">Use the NuGet package manager to update the [OWIN middleware](../../../aspnet/overview/owin-and-katana/getting-started-with-owin-and-katana.md).</span></span> <span data-ttu-id="32635-127">Выберите **обновления** в левом меню.</span><span class="sxs-lookup"><span data-stu-id="32635-127">Select **Updates** in the left menu.</span></span> <span data-ttu-id="32635-128">Можно щелкнуть **обновить все** кнопки, или можно выполнить поиск только пакеты OWIN (как показано на следующем рисунке):</span><span class="sxs-lookup"><span data-stu-id="32635-128">You can click on the **Update All** button or you can search for only OWIN packages (shown in the next image):</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image5.png)

<span data-ttu-id="32635-129">На приведенном ниже рисунке показаны только пакеты OWIN.</span><span class="sxs-lookup"><span data-stu-id="32635-129">In the image below, only OWIN packages are shown:</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image6.png)

<span data-ttu-id="32635-130">Из пакета Диспетчер консоли (PMC), можно ввести `Update-Package` команду, которая обновит все пакеты.</span><span class="sxs-lookup"><span data-stu-id="32635-130">From the Package Manager Console (PMC), you can enter the `Update-Package` command, which will update all packages.</span></span>

<span data-ttu-id="32635-131">Нажмите клавишу **F5** или **Ctrl + F5** для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="32635-131">Press **F5** or **Ctrl+F5** to run the application.</span></span> <span data-ttu-id="32635-132">На рисунке ниже номер порта — 1234.</span><span class="sxs-lookup"><span data-stu-id="32635-132">In the image below, the port number is 1234.</span></span> <span data-ttu-id="32635-133">При запуске приложения вы увидите другой номер порта.</span><span class="sxs-lookup"><span data-stu-id="32635-133">When you run the application, you'll see a different port number.</span></span>

<span data-ttu-id="32635-134">В зависимости от размера окна браузера, может потребоваться щелкните значок навигации для просмотра **Главная**, **о**, **контакт**, **зарегистрировать**и **входа** ссылки.</span><span class="sxs-lookup"><span data-stu-id="32635-134">Depending on the size of your browser window, you might need to click the navigation icon to see the **Home**, **About**, **Contact**, **Register** and **Log in** links.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image7.png)  
![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image8.png) 

<a id="ssl"></a>
## <a name="setting-up-ssl-in-the-project"></a><span data-ttu-id="32635-135">Настройка SSL в проекте</span><span class="sxs-lookup"><span data-stu-id="32635-135">Setting up SSL in the Project</span></span>

<span data-ttu-id="32635-136">Чтобы подключиться к поставщиков проверки подлинности, например Google и Facebook, необходимо настроить IIS Express для использования протокола SSL.</span><span class="sxs-lookup"><span data-stu-id="32635-136">To connect to authentication providers like Google and Facebook, you will need to set up IIS-Express to use SSL.</span></span> <span data-ttu-id="32635-137">Очень важно сохранить с помощью протокола SSL после входа в систему и не удалять обратно на HTTP, cookie-файл входа как секрет имя пользователя и пароль, а не с помощью протокола SSL при отправке в открытым текстом по каналу связи.</span><span class="sxs-lookup"><span data-stu-id="32635-137">It's important to keep using SSL after login and not drop back to HTTP, your login cookie is just as secret as your username and password, and without using SSL you're sending it in clear-text across the wire.</span></span> <span data-ttu-id="32635-138">Кроме того, вы уже сделали времени для выполнения установки соединения и защитить канал (являющийся массовой делает HTTPS медленнее, чем HTTP) перед запуском конвейера MVC, поэтому перенаправление обратно HTTP после вы выполнили вход в не текущего запроса или сделать будущее запросы гораздо быстрее.</span><span class="sxs-lookup"><span data-stu-id="32635-138">Besides, you've already taken the time to perform the handshake and secure the channel (which is the bulk of what makes HTTPS slower than HTTP) before the MVC pipeline is run, so redirecting back to HTTP after you're logged in won't make the current request or future requests much faster.</span></span>

1. <span data-ttu-id="32635-139">В **обозревателе решений**, нажмите кнопку **MvcAuth** проекта.</span><span class="sxs-lookup"><span data-stu-id="32635-139">In **Solution Explorer**, click the **MvcAuth** project.</span></span>
2. <span data-ttu-id="32635-140">Нажмите клавишу F4 для отображения свойств проекта.</span><span class="sxs-lookup"><span data-stu-id="32635-140">Hit the F4 key to show the project properties.</span></span> <span data-ttu-id="32635-141">Кроме того, в **представление** меню можно выбрать **окно свойств**.</span><span class="sxs-lookup"><span data-stu-id="32635-141">Alternatively, from the **View** menu you can select **Properties Window**.</span></span>
3. <span data-ttu-id="32635-142">Изменение **включен протокол SSL** значение True.</span><span class="sxs-lookup"><span data-stu-id="32635-142">Change **SSL Enabled** to True.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image9.png)
4. <span data-ttu-id="32635-143">Скопируйте URL-адрес SSL (который может быть `https://localhost:44300/` Если вы создали проекты SSL).</span><span class="sxs-lookup"><span data-stu-id="32635-143">Copy the SSL URL (which will be `https://localhost:44300/` unless you've created other SSL projects).</span></span>
5. <span data-ttu-id="32635-144">В **обозревателе решений**, щелкните правой кнопкой мыши **MvcAuth** проект и выберите **свойства**.</span><span class="sxs-lookup"><span data-stu-id="32635-144">In **Solution Explorer**, right click the **MvcAuth** project and select **Properties**.</span></span>
6. <span data-ttu-id="32635-145">Выберите **Web** вкладку, а затем вставьте URL-адрес SSL в **URL-адрес проекта** поле.</span><span class="sxs-lookup"><span data-stu-id="32635-145">Select the **Web** tab, and then paste the SSL URL into the **Project Url** box.</span></span> <span data-ttu-id="32635-146">Сохраните файл (Ctl + S).</span><span class="sxs-lookup"><span data-stu-id="32635-146">Save the file (Ctl+S).</span></span> <span data-ttu-id="32635-147">Вам потребуется этот URL-адрес для настройки проверки подлинности приложения Facebook и Google.</span><span class="sxs-lookup"><span data-stu-id="32635-147">You will need this URL to configure Facebook and Google authentication apps.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image10.png)
7. <span data-ttu-id="32635-148">Добавить [RequireHttps](https://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx) атрибут `Home` контроллера требовать все запросы должны использовать HTTPS.</span><span class="sxs-lookup"><span data-stu-id="32635-148">Add the [RequireHttps](https://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx) attribute to the `Home` controller to require all requests must use HTTPS.</span></span> <span data-ttu-id="32635-149">Более безопасный подход — добавить [RequireHttps](https://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx) фильтра в приложение.</span><span class="sxs-lookup"><span data-stu-id="32635-149">A more secure approach is to add the [RequireHttps](https://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx) filter to the application.</span></span> <span data-ttu-id="32635-150">См. в разделе &quot;защиты приложения с SSL и авторизовать атрибутом&quot; в моей tutoral [создать приложение ASP.NET MVC с помощью проверки подлинности и баз данных SQL Server и развернуть в службе приложений Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="32635-150">See the section &quot;Protect the Application with SSL and the Authorize Attribute&quot; in my tutoral [Create an ASP.NET MVC app with auth and SQL DB and deploy to Azure App Service](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data).</span></span> <span data-ttu-id="32635-151">Ниже приведен фрагмент контроллера Home.</span><span class="sxs-lookup"><span data-stu-id="32635-151">A portion of the Home controller is shown below.</span></span>

    [!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample1.cs?highlight=1)]
8. <span data-ttu-id="32635-152">Нажмите CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="32635-152">Press CTRL+F5 to run the application.</span></span> <span data-ttu-id="32635-153">Если вы уже установили сертификат в прошлом, можно пропустить оставшейся части этого раздела и перехода к [Создание приложения Google OAuth 2 и подключение приложения к проекту](#goog), в противном случае следуйте инструкциям, чтобы доверять собственной подписью сертификат, созданный IIS Express.</span><span class="sxs-lookup"><span data-stu-id="32635-153">If you've installed the certificate in the past, you can skip the rest of this section and jump to [Creating a Google app for OAuth 2 and connecting the app to the project](#goog), otherwise, follow the instructions to trust the self-signed certificate that IIS Express has generated.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image11.png)
9. <span data-ttu-id="32635-154">Чтение **предупреждение системы безопасности** диалоговое окно и нажмите кнопку **Да** Если вы хотите установить сертификат, представляющий localhost.</span><span class="sxs-lookup"><span data-stu-id="32635-154">Read the **Security Warning** dialog and then click **Yes** if you want to install the certificate representing localhost.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image12.png)
10. <span data-ttu-id="32635-155">Показывает, IE *Главная* страницы и отсутствуют предупреждения SSL.</span><span class="sxs-lookup"><span data-stu-id="32635-155">IE shows the *Home* page and there are no SSL warnings.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image13.png)
11. <span data-ttu-id="32635-156">Google Chrome также принимает сертификат и будет показано содержимое HTTPS без предупреждения.</span><span class="sxs-lookup"><span data-stu-id="32635-156">Google Chrome also accepts the certificate and will show HTTPS content without a warning.</span></span> <span data-ttu-id="32635-157">Firefox использует собственное хранилище сертификатов, поэтому он будет отображаться предупреждение.</span><span class="sxs-lookup"><span data-stu-id="32635-157">Firefox uses its own certificate store, so it will display a warning.</span></span> <span data-ttu-id="32635-158">Для нашего приложения могут безопасно щелкните **я понимаю последствия**.</span><span class="sxs-lookup"><span data-stu-id="32635-158">For our application you can safely click **I Understand the Risks**.</span></span>   
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image14.png)

<a id="goog"></a>
## <a name="creating-a-google-app-for-oauth-2-and-connecting-the-app-to-the-project"></a><span data-ttu-id="32635-159">Создание приложения Google OAuth 2 и подключение приложения в проект</span><span class="sxs-lookup"><span data-stu-id="32635-159">Creating a Google app for OAuth 2 and connecting the app to the project</span></span>

1. <span data-ttu-id="32635-160">Перейдите к [Google Developers Console](https://console.developers.google.com/).</span><span class="sxs-lookup"><span data-stu-id="32635-160">Navigate to the [Google Developers Console](https://console.developers.google.com/).</span></span>
1. <span data-ttu-id="32635-161">Если проект перед еще не создан, выберите **учетные данные** левой вкладке, а затем выберите **создать**.</span><span class="sxs-lookup"><span data-stu-id="32635-161">If you haven't created a project before, select **Credentials** in the left tab, and then select **Create**.</span></span>
1. <span data-ttu-id="32635-162">На левой вкладке нажмите кнопку **учетные данные**.</span><span class="sxs-lookup"><span data-stu-id="32635-162">In the left tab, click **Credentials**.</span></span>
1. <span data-ttu-id="32635-163">Нажмите кнопку **создать учетные данные** затем **идентификатор клиента OAuth**.</span><span class="sxs-lookup"><span data-stu-id="32635-163">Click **Create credentials** then **OAuth client ID**.</span></span> 

    1. <span data-ttu-id="32635-164">В **создать идентификатор клиента** диалоговое окно, оставьте значение по умолчанию **веб-приложение** для типа приложения.</span><span class="sxs-lookup"><span data-stu-id="32635-164">In the **Create Client ID** dialog, keep the default **Web application** for the application type.</span></span>
    2. <span data-ttu-id="32635-165">Задать **право JavaScript** источники, которые можно использовать выше URL-адрес SSL (`https://localhost:44300/` Если вы создали проекты SSL)</span><span class="sxs-lookup"><span data-stu-id="32635-165">Set the **Authorized JavaScript** origins to the SSL URL you used above (`https://localhost:44300/` unless you've created other SSL projects)</span></span>
    3. <span data-ttu-id="32635-166">Задать **авторизованные URI перенаправления** для:</span><span class="sxs-lookup"><span data-stu-id="32635-166">Set the **Authorized redirect URI** to:</span></span>  
         `https://localhost:44300/signin-google`
5. <span data-ttu-id="32635-167">Пункт меню экран согласия OAuth, а затем задайте адрес и продукта имени электронной почты.</span><span class="sxs-lookup"><span data-stu-id="32635-167">Click the OAuth Consent screen menu item, then set your email address and product name.</span></span> <span data-ttu-id="32635-168">После завершения работы щелкните форму **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="32635-168">When you have completed the form click **Save**.</span></span>
6. <span data-ttu-id="32635-169">Пункт меню библиотеки, поиска **API Google +**, щелкните его, а затем нажмите клавишу Enable.</span><span class="sxs-lookup"><span data-stu-id="32635-169">Click the Library menu item, search **Google+ API**, click on it then press Enable.</span></span>
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image15.png)  
  
 <span data-ttu-id="32635-170">На рисунке ниже показана включен API-интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="32635-170">The image below shows the enabled APIs.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image16.png)
7. <span data-ttu-id="32635-171">Посетите из Google API-интерфейсы API диспетчера, **учетные данные** вкладку, чтобы получить **идентификатор клиента**.</span><span class="sxs-lookup"><span data-stu-id="32635-171">From the Google APIs API Manager, visit the **Credentials** tab to obtain the **Client ID**.</span></span> <span data-ttu-id="32635-172">Загрузите, сохранить файл JSON с секретными данными приложения.</span><span class="sxs-lookup"><span data-stu-id="32635-172">Download to save a JSON file with application secrets.</span></span> <span data-ttu-id="32635-173">Скопируйте и вставьте **ClientId** и **ClientSecret** в `UseGoogleAuthentication` найти метод в *Startup.Auth.cs* файла в *App_Start* папки.</span><span class="sxs-lookup"><span data-stu-id="32635-173">Copy and paste the **ClientId** and **ClientSecret** into the `UseGoogleAuthentication` method found in the *Startup.Auth.cs* file in the *App_Start* folder.</span></span> <span data-ttu-id="32635-174">**ClientId** и **ClientSecret** значений, приведенных ниже примеров и не работают.</span><span class="sxs-lookup"><span data-stu-id="32635-174">The **ClientId** and **ClientSecret** values shown below are samples and don't work.</span></span>

    [!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample2.cs?highlight=37-39)]

    > [!WARNING]
    > <span data-ttu-id="32635-175">Безопасность — никогда не конфиденциальных данных в хранилище в исходном коде.</span><span class="sxs-lookup"><span data-stu-id="32635-175">Security - Never store sensitive data in your source code.</span></span> <span data-ttu-id="32635-176">Учетная запись и учетные данные добавляются выше, чтобы не усложнять в образце кода.</span><span class="sxs-lookup"><span data-stu-id="32635-176">The account and credentials are added to the code above to keep the sample simple.</span></span> <span data-ttu-id="32635-177">В разделе [советы и рекомендации по развертыванию пароли и другие конфиденциальные данные, ASP.NET и службы приложений Azure](../../../identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure.md).</span><span class="sxs-lookup"><span data-stu-id="32635-177">See [Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service](../../../identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure.md).</span></span>
8. <span data-ttu-id="32635-178">Нажмите клавишу **CTRL + F5** для построения и запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="32635-178">Press **CTRL+F5** to build and run the application.</span></span> <span data-ttu-id="32635-179">Нажмите кнопку **входа** ссылку.</span><span class="sxs-lookup"><span data-stu-id="32635-179">Click the **Log in** link.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image17.png)
9. <span data-ttu-id="32635-180">В разделе **используется другой службой для входа в**, нажмите кнопку **Google**.</span><span class="sxs-lookup"><span data-stu-id="32635-180">Under **Use another service to log in**, click **Google**.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image18.png)

    > [!NOTE]
    > <span data-ttu-id="32635-181">Если пропустить любой из вышеперечисленных действий вы получите сообщение об ошибке HTTP 401.</span><span class="sxs-lookup"><span data-stu-id="32635-181">If you miss any of the steps above you will get a HTTP 401 error.</span></span> <span data-ttu-id="32635-182">Повторная проверка выше действия.</span><span class="sxs-lookup"><span data-stu-id="32635-182">Recheck your steps above.</span></span> <span data-ttu-id="32635-183">Если вы пропустите обязательный параметр (например **название продукта**), Добавление отсутствующих элементов и сохранить, может занять несколько минут для проверки подлинности для работы.</span><span class="sxs-lookup"><span data-stu-id="32635-183">If you miss a required setting (for example **product name**), add the missing item and save, it can take a few minutes for authentication to work.</span></span>
10. <span data-ttu-id="32635-184">Вы будете перенаправлены на сайт google, можно будет ввести учетные данные.</span><span class="sxs-lookup"><span data-stu-id="32635-184">You will be redirected to the google site where you will enter your credentials.</span></span>   
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image19.png)
11. <span data-ttu-id="32635-185">После ввода учетных данных, будет предложено предоставить разрешения для только что созданной веб-приложения:</span><span class="sxs-lookup"><span data-stu-id="32635-185">After you enter your credentials, you will be prompted to give permissions to the web application you just created:</span></span>
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image20.png)
12. <span data-ttu-id="32635-186">Нажмите кнопку **принимать**.</span><span class="sxs-lookup"><span data-stu-id="32635-186">Click **Accept**.</span></span> <span data-ttu-id="32635-187">Вам будут перенаправляться к **зарегистрировать** страница MvcAuth приложения, где можно зарегистрировать учетную запись Google.</span><span class="sxs-lookup"><span data-stu-id="32635-187">You will now be redirected back to the **Register** page of the MvcAuth application where you can register your Google account.</span></span> <span data-ttu-id="32635-188">Вы можете изменить имя локальной электронной почты регистрации, используемое для свою учетную запись Gmail, но обычно требуется сохранить псевдоним электронной почты по умолчанию (один используется для проверки подлинности).</span><span class="sxs-lookup"><span data-stu-id="32635-188">You have the option of changing the local email registration name used for your Gmail account, but you generally want to keep the default email alias (that is, the one you used for authentication).</span></span> <span data-ttu-id="32635-189">Щелкните ссылку **Регистрация**.</span><span class="sxs-lookup"><span data-stu-id="32635-189">Click **Register**.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image21.png)

<a id="fb"></a>
## <a name="creating-the-app-in-facebook-and-connecting-the-app-to-the-project"></a><span data-ttu-id="32635-190">Создать приложение в Facebook и подключить приложения в проект</span><span class="sxs-lookup"><span data-stu-id="32635-190">Creating the app in Facebook and connecting the app to the project</span></span>

<span data-ttu-id="32635-191">Для проверки подлинности Facebook OAuth2 необходимо скопировать в проект некоторые параметры из приложения, создаваемые в Facebook.</span><span class="sxs-lookup"><span data-stu-id="32635-191">For Facebook OAuth2 authentication, you need to copy to your project some settings from an application that you create in Facebook.</span></span>

1. <span data-ttu-id="32635-192">В браузере перейдите к [https://developers.facebook.com/apps](https://developers.facebook.com/apps) и войдите в систему, указав учетные данные Facebook.</span><span class="sxs-lookup"><span data-stu-id="32635-192">In your browser, navigate to [https://developers.facebook.com/apps](https://developers.facebook.com/apps) and log in by entering your Facebook credentials.</span></span>
2. <span data-ttu-id="32635-193">Если вы уже не зарегистрирован как разработчика Facebook, нажмите кнопку **зарегистрировать как разработчик** и следуйте инструкциям для регистрации.</span><span class="sxs-lookup"><span data-stu-id="32635-193">If you aren't already registered as a Facebook developer, click **Register as a Developer** and follow the directions to register.</span></span>
3. <span data-ttu-id="32635-194">На **приложения** щелкните **создать новое приложение**.</span><span class="sxs-lookup"><span data-stu-id="32635-194">On the **Apps** tab, click **Create New App**.</span></span>

    ![Создайте новое приложение](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image22.png)
4. <span data-ttu-id="32635-196">Введите **имя приложения** и **категории**, нажмите кнопку **Создание приложения**.</span><span class="sxs-lookup"><span data-stu-id="32635-196">Enter an **App Name** and **Category**, then click **Create App**.</span></span>

    <span data-ttu-id="32635-197">Это должно быть уникальным для Facebook.</span><span class="sxs-lookup"><span data-stu-id="32635-197">This must be unique across Facebook.</span></span> <span data-ttu-id="32635-198">**Пространство имен приложения** является частью URL-адрес, приложение будет использовать для доступа к приложению Facebook для проверки подлинности (например, https://apps.facebook.com/ {пространство имен приложения}).</span><span class="sxs-lookup"><span data-stu-id="32635-198">The **App Namespace** is the part of the URL that your App will use to access the Facebook application for authentication (for example, https://apps.facebook.com/{App Namespace}).</span></span> <span data-ttu-id="32635-199">Если не указать **пространство имен приложения**, **идентификатор приложения** будет использоваться URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="32635-199">If you don't specify an **App Namespace**, the **App ID** will be used for the URL.</span></span> <span data-ttu-id="32635-200">**Идентификатор приложения** долго системой номер, который вы увидите на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="32635-200">The **App ID** is a long system-generated number that you will see in the next step.</span></span>

    ![Создание диалогового окна нового приложения](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image23.png)
5. <span data-ttu-id="32635-202">Отправьте проверки безопасности standard.</span><span class="sxs-lookup"><span data-stu-id="32635-202">Submit the standard security check.</span></span>

    ![Проверка безопасности](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image24.png)
6. <span data-ttu-id="32635-204">Выберите **параметры** для левого меню.![ Строка меню разработчика Facebook](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image25.png)</span><span class="sxs-lookup"><span data-stu-id="32635-204">Select **Settings** for the left menu bar.![Facebook Developer's menu bar](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image25.png)</span></span>
7. <span data-ttu-id="32635-205">На **основные** страницы «Параметры» выберите **добавить платформу** для указания, что добавляется приложение веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="32635-205">On the **Basic** settings section of the page select **Add Platform** to specify that you are adding a website application.</span></span> <span data-ttu-id="32635-206">![Основные параметры](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image26.png)</span><span class="sxs-lookup"><span data-stu-id="32635-206">![Basic Settings](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image26.png)</span></span>
8. <span data-ttu-id="32635-207">Выберите **веб-сайт** из вариантов платформы.</span><span class="sxs-lookup"><span data-stu-id="32635-207">Select **Website** from the platform choices.</span></span>  
  
    ![Выбор платформы](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image27.png)
9. <span data-ttu-id="32635-209">Обратите внимание на **идентификатор приложения** и **секрет приложения** , чтобы оба сертификата в MVC-приложения можно добавить позднее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="32635-209">Make a note of your **App ID** and your **App Secret** so that you can add both into your MVC application later in this tutorial.</span></span> <span data-ttu-id="32635-210">Кроме того, добавьте URL-адрес сайта (`https://localhost:44300/`) для тестирования приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="32635-210">Also, Add your Site URL (`https://localhost:44300/`) to test your MVC application.</span></span> <span data-ttu-id="32635-211">Кроме того, добавьте **адрес электронной почты**.</span><span class="sxs-lookup"><span data-stu-id="32635-211">Also, add a **Contact Email**.</span></span> <span data-ttu-id="32635-212">Выберите **сохранить изменения**.</span><span class="sxs-lookup"><span data-stu-id="32635-212">Then, select **Save Changes**.</span></span>   

    ![Страница сведений о основного приложения](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image28.png)

    > [!NOTE]
    > <span data-ttu-id="32635-214">Обратите внимание, что можно будет только возможность проверки подлинности с помощью зарегистрированного псевдоним электронной почты.</span><span class="sxs-lookup"><span data-stu-id="32635-214">Note that you will only be able to authenticate using the email alias you have registered.</span></span> <span data-ttu-id="32635-215">Другие пользователи и тестовые учетные записи не будет возможности регистрации.</span><span class="sxs-lookup"><span data-stu-id="32635-215">Other users and test accounts will not be able to register.</span></span> <span data-ttu-id="32635-216">Вы можете предоставить доступ учетным записям других Facebook для приложения на Facebook **роль разработчика** вкладки.</span><span class="sxs-lookup"><span data-stu-id="32635-216">You can grant other Facebook accounts access to the application on the Facebook **Developer Roles** tab.</span></span>
10. <span data-ttu-id="32635-217">В Visual Studio откройте *приложения\_Start\Startup.Auth.cs*.</span><span class="sxs-lookup"><span data-stu-id="32635-217">In Visual Studio, open *App\_Start\Startup.Auth.cs*.</span></span>
11. <span data-ttu-id="32635-218">Скопируйте и вставьте **AppId** и **секрет приложения** в `UseFacebookAuthentication` метод.</span><span class="sxs-lookup"><span data-stu-id="32635-218">Copy and paste the **AppId** and **App Secret** into the `UseFacebookAuthentication` method.</span></span> <span data-ttu-id="32635-219">**AppId** и **секрет приложения** значений, приведенных ниже примеров и не будет работать.</span><span class="sxs-lookup"><span data-stu-id="32635-219">The **AppId** and **App Secret** values shown below are samples and will not work.</span></span>

    [!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample3.cs?highlight=33-35,38-39)]
12. <span data-ttu-id="32635-220">Нажмите кнопку **сохранить изменения**.</span><span class="sxs-lookup"><span data-stu-id="32635-220">Click **Save Changes**.</span></span>
13. <span data-ttu-id="32635-221">Нажмите клавишу **CTRL + F5** для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="32635-221">Press **CTRL+F5** to run the application.</span></span>


<span data-ttu-id="32635-222">Выберите **входа** для отображения страницы входа.</span><span class="sxs-lookup"><span data-stu-id="32635-222">Select **Log in** to display the Login page.</span></span> <span data-ttu-id="32635-223">Нажмите кнопку **Facebook** под **используйте для входа другую службу.**</span><span class="sxs-lookup"><span data-stu-id="32635-223">Click **Facebook** under **Use another service to log in.**</span></span>

<span data-ttu-id="32635-224">Введите учетные данные Facebook.</span><span class="sxs-lookup"><span data-stu-id="32635-224">Enter your Facebook credentials.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image29.png)

<span data-ttu-id="32635-225">Будет предложено предоставить разрешение на доступ к открытый профиль и список friend приложения.</span><span class="sxs-lookup"><span data-stu-id="32635-225">You will be prompted to grant permission for the application to access your public profile and friend list.</span></span>

![Сведения о приложении Facebook](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image30.png)

<span data-ttu-id="32635-227">Вы вошли.</span><span class="sxs-lookup"><span data-stu-id="32635-227">You are now logged in.</span></span> <span data-ttu-id="32635-228">Теперь можно зарегистрировать эту учетную запись с приложением.</span><span class="sxs-lookup"><span data-stu-id="32635-228">You can now register this account with the application.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image31.png)

<span data-ttu-id="32635-229">При регистрации, добавляется запись *пользователей* таблицы базы данных членства.</span><span class="sxs-lookup"><span data-stu-id="32635-229">When you register, an entry is added to the *Users* table of the membership database.</span></span>

<a id="mdb"></a>
## <a name="examine-the-membership-data"></a><span data-ttu-id="32635-230">Проверьте данные членства</span><span class="sxs-lookup"><span data-stu-id="32635-230">Examine the Membership Data</span></span>

<span data-ttu-id="32635-231">В **представление** меню, нажмите кнопку **обозревателя серверов**.</span><span class="sxs-lookup"><span data-stu-id="32635-231">In the **View** menu, click **Server Explorer**.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image32.png)

<span data-ttu-id="32635-232">Разверните **DefaultConnection (MvcAuth)**, разверните **таблиц**, щелкните правой кнопкой мыши **AspNetUsers** и нажмите кнопку **Показать таблицу данных**.</span><span class="sxs-lookup"><span data-stu-id="32635-232">Expand **DefaultConnection (MvcAuth)**, expand **Tables**, right click **AspNetUsers** and click **Show Table Data**.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image33.png)

![aspnetusers таблицы данных](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image34.png)

<a id="ap"></a>
## <a name="adding-profile-data-to-the-user-class"></a><span data-ttu-id="32635-234">Добавление данных профиля в класс пользователя</span><span class="sxs-lookup"><span data-stu-id="32635-234">Adding Profile Data to the User Class</span></span>

<span data-ttu-id="32635-235">В этом разделе вы добавите дату рождения и родного города пользовательских данных во время регистрации, как показано на следующем рисунке.</span><span class="sxs-lookup"><span data-stu-id="32635-235">In this section you'll add birth date and home town to the user data during registration, as shown in the following image.</span></span>

![раздел реестра с родного города и д.рожд.](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image35.png)

<span data-ttu-id="32635-237">Откройте *Models\IdentityModels.cs* и добавьте свойства Город Домашняя страница и Дата рождения файла:</span><span class="sxs-lookup"><span data-stu-id="32635-237">Open the *Models\IdentityModels.cs* file and add birth date and home town properties:</span></span>

[!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample4.cs?highlight=3-4)]

<span data-ttu-id="32635-238">Откройте *Models\AccountViewModels.cs* файла и набор свойств Город даты и дома в их создания `ExternalLoginConfirmationViewModel`.</span><span class="sxs-lookup"><span data-stu-id="32635-238">Open the *Models\AccountViewModels.cs* file and the set birth date and home town properties in `ExternalLoginConfirmationViewModel`.</span></span>

[!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample5.cs?highlight=8-9)]

<span data-ttu-id="32635-239">Откройте *Controllers\AccountController.cs* и добавьте код для города Домашняя страница и даты рождения в `ExternalLoginConfirmation` метода действия, как показано:</span><span class="sxs-lookup"><span data-stu-id="32635-239">Open the *Controllers\AccountController.cs* file and add code for birth date and home town in the `ExternalLoginConfirmation` action method as shown:</span></span>

[!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample6.cs?highlight=21-23)]

<span data-ttu-id="32635-240">Добавить дату рождения и родного города для *Views\Account\ExternalLoginConfirmation.cshtml* файла:</span><span class="sxs-lookup"><span data-stu-id="32635-240">Add birth date and home town to the *Views\Account\ExternalLoginConfirmation.cshtml* file:</span></span>

[!code-cshtml[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample7.cshtml?highlight=27-40)]

<span data-ttu-id="32635-241">Удалите базу данных членства, чтобы можно было снова зарегистрировать вашу учетную запись Facebook с приложением и убедитесь, что можно добавить новую дату рождения и сведения о профиле родного города.</span><span class="sxs-lookup"><span data-stu-id="32635-241">Delete the membership database so you can again register your Facebook account with your application and verify you can add the new birth date and home town profile information.</span></span>

<span data-ttu-id="32635-242">Из **обозревателе решений**, нажмите кнопку **Показать все файлы** значок, а затем щелкните правой кнопкой мыши *добавить\_Data\aspnet-MvcAuth -&lt;метки даты&gt;.mdf* и нажмите кнопку **удалить**.</span><span class="sxs-lookup"><span data-stu-id="32635-242">From **Solution Explorer**, click the **Show All Files** icon, then right click *Add\_Data\aspnet-MvcAuth-&lt;dateStamp&gt;.mdf* and click **Delete**.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image36.png)

<span data-ttu-id="32635-243">Из **средства** меню, нажмите кнопку **диспетчера пакетов NuGet**, нажмите кнопку **консоль диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="32635-243">From the **Tools** menu, click **NuGet Package Manger**, then click **Package Manager Console** (PMC).</span></span> <span data-ttu-id="32635-244">Введите следующие команды в PMC.</span><span class="sxs-lookup"><span data-stu-id="32635-244">Enter the following commands in the PMC.</span></span>

1. <span data-ttu-id="32635-245">Enable-Migrations</span><span class="sxs-lookup"><span data-stu-id="32635-245">Enable-Migrations</span></span>
2. <span data-ttu-id="32635-246">Init-Migration</span><span class="sxs-lookup"><span data-stu-id="32635-246">Add-Migration Init</span></span>
3. <span data-ttu-id="32635-247">Update-Database</span><span class="sxs-lookup"><span data-stu-id="32635-247">Update-Database</span></span>

<span data-ttu-id="32635-248">Запустите приложение и использовать для входа и регистрации некоторых пользователей FaceBook и Google.</span><span class="sxs-lookup"><span data-stu-id="32635-248">Run the application and use FaceBook and Google to log in and register some users.</span></span>

## <a name="examine-the-membership-data"></a><span data-ttu-id="32635-249">Проверьте данные членства</span><span class="sxs-lookup"><span data-stu-id="32635-249">Examine the Membership Data</span></span>

<span data-ttu-id="32635-250">В **представление** меню, нажмите кнопку **обозревателя серверов**.</span><span class="sxs-lookup"><span data-stu-id="32635-250">In the **View** menu, click **Server Explorer**.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image37.png)

<span data-ttu-id="32635-251">Щелкните правой кнопкой мыши **AspNetUsers** и нажмите кнопку **Показать таблицу данных**.</span><span class="sxs-lookup"><span data-stu-id="32635-251">Right click **AspNetUsers** and click **Show Table Data**.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image38.png)

<span data-ttu-id="32635-252">`HomeTown` И `BirthDate` поля будут показаны ниже.</span><span class="sxs-lookup"><span data-stu-id="32635-252">The `HomeTown` and `BirthDate` fields are shown below.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image39.png)

<a id="off"></a>
## <a name="logging-off-your-app-and-logging-in-with-another-account"></a><span data-ttu-id="32635-253">Входа в систему с другой учетной записи и выход из приложения</span><span class="sxs-lookup"><span data-stu-id="32635-253">Logging off your App and Logging in With Another Account</span></span>

<span data-ttu-id="32635-254">Если вход в приложение с Facebook и затем выйдите из системы и попробуйте войти снова под другой учетной записью Facebook (используя тот же браузер), вам будет немедленно вход в систему предыдущей учетной записи Facebook, который использовался.</span><span class="sxs-lookup"><span data-stu-id="32635-254">If you log on to your app with Facebook,, and then log out and try to log in again with a different Facebook account (using the same browser), you will be immediately logged in to the previous Facebook account you used.</span></span> <span data-ttu-id="32635-255">Чтобы использовать другую учетную запись, необходимо перейти к Facebook и выйдите из системы на Facebook.</span><span class="sxs-lookup"><span data-stu-id="32635-255">In order to use another account, you need to navigate to Facebook and log out at Facebook.</span></span> <span data-ttu-id="32635-256">Это же правило применяется к любой другой стороннего проверки подлинности поставщика.</span><span class="sxs-lookup"><span data-stu-id="32635-256">The same rule applies to any other 3rd party authentication provider.</span></span> <span data-ttu-id="32635-257">Кроме того можно войти под другой учетной записью, используя другой браузер.</span><span class="sxs-lookup"><span data-stu-id="32635-257">Alternatively, you can log in with another account by using a different browser.</span></span>

## <a name="next-steps"></a><span data-ttu-id="32635-258">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="32635-258">Next Steps</span></span>

<span data-ttu-id="32635-259">В разделе [введение Yahoo и LinkedIn OAuth поставщиков безопасности для OWIN](http://www.jerriepelser.com/blog/introducing-the-yahoo-linkedin-oauth-security-providers-for-owin/) по Jerrie Pelser Yahoo и LinkedIn инструкции.</span><span class="sxs-lookup"><span data-stu-id="32635-259">See [Introducing the Yahoo and LinkedIn OAuth security providers for OWIN](http://www.jerriepelser.com/blog/introducing-the-yahoo-linkedin-oauth-security-providers-for-owin/) by Jerrie Pelser for Yahoo and LinkedIn instructions.</span></span> <span data-ttu-id="32635-260">См. в Jerrie довольно кнопки входа социальных сетей для ASP.NET MVC 5 для получения enable кнопки входа социальных сетей.</span><span class="sxs-lookup"><span data-stu-id="32635-260">See Jerrie's Pretty social login buttons for ASP.NET MVC 5 to get enable social login buttons.</span></span>

<span data-ttu-id="32635-261">Выполните my учебника [создать приложение ASP.NET MVC с помощью проверки подлинности и баз данных SQL Server и развернуть в службе приложений Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data), который продолжает этого учебника и отображает следующую:</span><span class="sxs-lookup"><span data-stu-id="32635-261">Follow my tutorial [Create an ASP.NET MVC app with auth and SQL DB and deploy to Azure App Service](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data), which continues this tutorial and shows the following:</span></span>

1. <span data-ttu-id="32635-262">Как развернуть приложение в Azure.</span><span class="sxs-lookup"><span data-stu-id="32635-262">How to deploy your app to Azure.</span></span>
2. <span data-ttu-id="32635-263">Как защитить приложение с ролями.</span><span class="sxs-lookup"><span data-stu-id="32635-263">How to secure you app with roles.</span></span>
3. <span data-ttu-id="32635-264">Как защитить приложение с [RequireHttps](https://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) и [авторизовать](https://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) фильтры.</span><span class="sxs-lookup"><span data-stu-id="32635-264">How to secure your app with the [RequireHttps](https://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) and [Authorize](https://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) filters.</span></span>
4. <span data-ttu-id="32635-265">Инструкции по использованию API членства для добавления пользователей и ролей.</span><span class="sxs-lookup"><span data-stu-id="32635-265">How to use the membership API to add users and roles.</span></span>

<span data-ttu-id="32635-266">Оставьте отзыв на том, как вам понравилось этого учебника и что можно улучшить.</span><span class="sxs-lookup"><span data-stu-id="32635-266">Please leave feedback on how you liked this tutorial and what we could improve.</span></span> <span data-ttu-id="32635-267">Можно также запросить новые разделы на [показать мне как с код](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).</span><span class="sxs-lookup"><span data-stu-id="32635-267">You can also request new topics at [Show Me How With Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).</span></span> <span data-ttu-id="32635-268">Можно даже запрашивает и голосовать о новых функциях, добавляемых к ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="32635-268">You can even ask for and vote on new features to be added to ASP.NET.</span></span> <span data-ttu-id="32635-269">Например, смогут проголосовать за это средство, [Создание и управление пользователями и ролями.](http://aspnet.uservoice.com/forums/41199-general-asp-net/suggestions/5646857-asp-net-identity-membership-db-tool-to-mangage-use)</span><span class="sxs-lookup"><span data-stu-id="32635-269">For example, you can vote for a tool to [create and manage users and roles.](http://aspnet.uservoice.com/forums/41199-general-asp-net/suggestions/5646857-asp-net-identity-membership-db-tool-to-mangage-use)</span></span>

<span data-ttu-id="32635-270">Хорошее описание принципов работы внешние службы проверки подлинности ASP.NET см. в разделе Robert McMurray [внешние службы проверки подлинности](https://asp.net/web-api/overview/security/external-authentication-services).</span><span class="sxs-lookup"><span data-stu-id="32635-270">For an good explanation of how ASP.NET External Authentication Services work, see Robert McMurray's [External Authentication Services](https://asp.net/web-api/overview/security/external-authentication-services).</span></span> <span data-ttu-id="32635-271">Статья Ивана также переходит в подробно Включение проверки подлинности Майкрософт и Twitter.</span><span class="sxs-lookup"><span data-stu-id="32635-271">Robert's article also goes into detail in enabling Microsoft and Twitter authentication.</span></span> <span data-ttu-id="32635-272">Отлично Tom Dykstra [EF и MVC учебника](../getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) показано, как работать с платформой Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="32635-272">Tom Dykstra's excellent [EF/MVC tutorial](../getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) shows how to work with the Entity Framework.</span></span>