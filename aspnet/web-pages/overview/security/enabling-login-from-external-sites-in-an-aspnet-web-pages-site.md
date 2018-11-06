---
uid: web-pages/overview/security/enabling-login-from-external-sites-in-an-aspnet-web-pages-site
title: Přihlášení pomocí externí weby v ASP.NET Web stránky webu (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento článek vysvětluje, jak se přihlaste k webu webových stránek ASP.NET (Razor) pomocí služby Facebook, Google, Twitter, Yahoo a jiných lokalitách – to znamená, jak podporovat...
ms.author: riande
ms.date: 02/21/2014
ms.assetid: ef852096-a5bf-47b3-9945-125cde065093
msc.legacyurl: /web-pages/overview/security/enabling-login-from-external-sites-in-an-aspnet-web-pages-site
msc.type: authoredcontent
ms.openlocfilehash: 188a9203ba7b04f5a88d0f802f1a05bf35d58d8c
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021128"
---
<a name="logging-in-using-external-sites-in-an-aspnet-web-pages-razor-site"></a><span data-ttu-id="e7d93-103">Přihlášení pomocí externí weby na webu rozhraní ASP.NET Web Pages (Razor)</span><span class="sxs-lookup"><span data-stu-id="e7d93-103">Logging In Using External Sites in an ASP.NET Web Pages (Razor) Site</span></span>
====================
<span data-ttu-id="e7d93-104">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="e7d93-104">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="e7d93-105">Tento článek vysvětluje, jak se přihlaste k webu webových stránek ASP.NET (Razor) pomocí služby Facebook, Google, Twitter, Yahoo a jiných lokalitách – to znamená, jak podporovat OAuth a OpenID ve vaší lokalitě.</span><span class="sxs-lookup"><span data-stu-id="e7d93-105">This article explains how to log in to your ASP.NET Web Pages (Razor) site using Facebook, Google, Twitter, Yahoo, and other sites — that is, how to support OAuth and OpenID in your site.</span></span>
> 
> <span data-ttu-id="e7d93-106">Co se dozvíte:</span><span class="sxs-lookup"><span data-stu-id="e7d93-106">What you'll learn:</span></span>
> 
> - <span data-ttu-id="e7d93-107">Postup povolení přihlášení z jiných webů, když použijete šablonu Starter web služby WebMatrix.</span><span class="sxs-lookup"><span data-stu-id="e7d93-107">How to enable login from other sites when you use the WebMatrix Starter Site template.</span></span>
> 
> <span data-ttu-id="e7d93-108">Toto je funkce technologie ASP.NET zavedené v následujícím článku:</span><span class="sxs-lookup"><span data-stu-id="e7d93-108">This is the ASP.NET feature introduced in the article:</span></span>
> 
> - <span data-ttu-id="e7d93-109">`OAuthWebSecurity` Pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="e7d93-109">The `OAuthWebSecurity` helper.</span></span>
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="e7d93-110">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="e7d93-110">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="e7d93-111">Rozhraní ASP.NET Web Pages (Razor) 2</span><span class="sxs-lookup"><span data-stu-id="e7d93-111">ASP.NET Web Pages (Razor) 2</span></span>
> - <span data-ttu-id="e7d93-112">Služba WebMatrix 3</span><span class="sxs-lookup"><span data-stu-id="e7d93-112">WebMatrix 3</span></span>

<span data-ttu-id="e7d93-113">Zahrnuje podporu pro rozhraní ASP.NET Web Pages [OAuth](http://oauth.net/) a [OpenID](http://openid.net/) poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="e7d93-113">ASP.NET Web Pages includes support for [OAuth](http://oauth.net/) and [OpenID](http://openid.net/) providers.</span></span> <span data-ttu-id="e7d93-114">Pomocí těchto zprostředkovatelů, můžete nechat přihlášení uživatelů do vašeho webu pomocí existujících přihlašovacích údajů z Facebooku, Twitteru, Microsoft a Googlu.</span><span class="sxs-lookup"><span data-stu-id="e7d93-114">Using these providers, you can let users log into your site using their existing credentials from Facebook, Twitter, Microsoft, and Google.</span></span> <span data-ttu-id="e7d93-115">K přihlášení pomocí účtu sítě Facebook, například uživatele můžete prostě vybrat ikonu Facebooku, který přesměruje na přihlašovací stránku Facebooku, kde jsou informace o uživateli zadat.</span><span class="sxs-lookup"><span data-stu-id="e7d93-115">For example, to log in using a Facebook account, users can just choose a Facebook icon, which redirects them to the Facebook login page where they enter their user information.</span></span> <span data-ttu-id="e7d93-116">Přihlášení k Facebooku, pak můžete přiřadit ke svému účtu na webu.</span><span class="sxs-lookup"><span data-stu-id="e7d93-116">They can then associate the Facebook login with their account on your site.</span></span> <span data-ttu-id="e7d93-117">Související vylepšení funkcí členství webové stránky je, že uživatelé mohou přidružit více přihlašovací jména (včetně přihlašování ze sociálních sítí) pomocí jednoho účtu na vašem webu.</span><span class="sxs-lookup"><span data-stu-id="e7d93-117">A related enhancement to the Web Pages membership features is that users can associate multiple logins (including logins from social networking sites) with a single account on your website.</span></span>

<span data-ttu-id="e7d93-118">Tento obrázek ukazuje na přihlašovací stránku z **Starter Site** šablony, ve kterém může uživatel vybrat Facebook, Twitter, Google nebo Microsoft ikonu povolíte protokolování s využitím externí účet:</span><span class="sxs-lookup"><span data-stu-id="e7d93-118">This image shows the Login page from the **Starter Site** template, where a user can choose a Facebook, Twitter, Google or Microsoft icon to enable logging in with an external account:</span></span>

![externí zprostředkovatele](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image1.png)

<span data-ttu-id="e7d93-120">Můžete povolit OAuth a OpenID členství uncommenting pár řádků kódu **Starter Site** šablony.</span><span class="sxs-lookup"><span data-stu-id="e7d93-120">You can enable OAuth and OpenID membership by uncommenting a few lines of code in the **Starter Site** template.</span></span> <span data-ttu-id="e7d93-121">Metody a vlastnosti můžete použít pro práci s OAuth a OpenID zprostředkovatelé jsou v `WebMatrix.Security.OAuthWebSecurity` třídy.</span><span class="sxs-lookup"><span data-stu-id="e7d93-121">The methods and properties you use to work with the OAuth and OpenID providers are in the `WebMatrix.Security.OAuthWebSecurity` class.</span></span> <span data-ttu-id="e7d93-122">**Starter Site** Šablona zahrnuje úplné členství infrastruktury, s přihlašovací stránky, databáze členství a veškerý kód, je potřeba nechat přihlášení uživatelů do vašeho webu pomocí pověření pro místní nebo z jiné lokality .</span><span class="sxs-lookup"><span data-stu-id="e7d93-122">The **Starter Site** template includes a full membership infrastructure, complete with a login page, a membership database, and all the code you need to let users log into your site using either local credentials or those from another site.</span></span>

<span data-ttu-id="e7d93-123">Tato část poskytuje příklad toho, jak umožnit uživatelům přihlášení z externích webů k lokalitě, která je založena na **Starter Site** šablony.</span><span class="sxs-lookup"><span data-stu-id="e7d93-123">This section provides an example of how to let users log in from external sites to a site that's based on the **Starter Site** template.</span></span> <span data-ttu-id="e7d93-124">Po vytvoření první web, můžete postupujte (podrobnosti):</span><span class="sxs-lookup"><span data-stu-id="e7d93-124">After creating a starter site, you do this (details follow):</span></span>

- <span data-ttu-id="e7d93-125">Weby, které používají zprostředkovatele OAuth (Facebook, Twitter a Microsoft) vytvořit aplikaci na externí web.</span><span class="sxs-lookup"><span data-stu-id="e7d93-125">For the sites that use an OAuth provider (Facebook, Twitter, and Microsoft), you create an application on the external site.</span></span> <span data-ttu-id="e7d93-126">To vám dává klíče aplikace, které budete potřebovat k vyvolání funkce přihlášení pro tyto lokality.</span><span class="sxs-lookup"><span data-stu-id="e7d93-126">This gives you application keys that you'll need in order to invoke the login feature for those sites.</span></span>
- <span data-ttu-id="e7d93-127">Pro servery, které využívají poskytovatele OpenID (Google) není nutné k vytvoření aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7d93-127">For sites that use an OpenID provider (Google), you do not have to create an application.</span></span> <span data-ttu-id="e7d93-128">Pro všechny tyto servery musí mít účet k přihlášení a k vytváření aplikací pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="e7d93-128">For all of these sites, you must have an account in order to log in and to create developer applications.</span></span>

    > [!NOTE]
    > <span data-ttu-id="e7d93-129">Aplikace Microsoftu přijímat pouze za provozu adresu URL pro webovou stránku, funkční tak adresy URL místního webu nelze použít pro testování přihlášení.</span><span class="sxs-lookup"><span data-stu-id="e7d93-129">Microsoft applications only accept a live URL for a working website, so you cannot use a local website URL for testing logins.</span></span>
- <span data-ttu-id="e7d93-130">Pokud chcete zadat poskytovatele příslušný ověřovací upravit několik souborů na vašem webu a odeslat přihlášení k webu, kterou chcete použít.</span><span class="sxs-lookup"><span data-stu-id="e7d93-130">Edit a few files in your website in order to specify the appropriate authentication provider and to submit a login to the site you want to use.</span></span>

<span data-ttu-id="e7d93-131">Tento článek poskytuje zvláštní pokyny pro následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="e7d93-131">This article provides separate instructions for the following tasks:</span></span>

- [<span data-ttu-id="e7d93-132">Povolení přihlášení Google</span><span class="sxs-lookup"><span data-stu-id="e7d93-132">Enabling Google logins</span></span>](#To_enable_Google_logins)
- [<span data-ttu-id="e7d93-133">Povolení přihlášení k Facebooku</span><span class="sxs-lookup"><span data-stu-id="e7d93-133">Enabling Facebook logins</span></span>](#To_enable_Facebook_logins)
- [<span data-ttu-id="e7d93-134">Povolení přihlášení k Twitteru</span><span class="sxs-lookup"><span data-stu-id="e7d93-134">Enabling Twitter logins</span></span>](#To_enable_Twitter_logins)

<a id="To_enable_Google_logins"></a>
## <a name="enabling-google-logins"></a><span data-ttu-id="e7d93-135">Povolení přihlášení Google</span><span class="sxs-lookup"><span data-stu-id="e7d93-135">Enabling Google Logins</span></span>

1. <span data-ttu-id="e7d93-136">Vytvořit nebo otevřít web rozhraní ASP.NET Web Pages, který je založen na šabloně Starter web služby WebMatrix.</span><span class="sxs-lookup"><span data-stu-id="e7d93-136">Create or open an ASP.NET Web Pages site that's based on the WebMatrix Starter Site template.</span></span>
2. <span data-ttu-id="e7d93-137">Otevřít  *\_AppStart.cshtml* stránce a zrušte komentář u následující řádek kódu.</span><span class="sxs-lookup"><span data-stu-id="e7d93-137">Open the *\_AppStart.cshtml* page and uncomment the following line of code.</span></span> 

    [!code-css[Main](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/samples/sample1.css)]

### <a name="testing-google-login"></a><span data-ttu-id="e7d93-138">Testování Google přihlášení</span><span class="sxs-lookup"><span data-stu-id="e7d93-138">Testing Google login</span></span>

1. <span data-ttu-id="e7d93-139">Spustit *stránku default.cshtml* stránku vašeho webu a zvolte **přihlášení** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e7d93-139">Run the *default.cshtml* page of your site and choose the **Log in** button.</span></span>
2. <span data-ttu-id="e7d93-140">Na *přihlášení* stránku, **přihlášení pomocí jiné služby** zvolte buď **Google** nebo **Yahoo** tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="e7d93-140">On the *Login* page, in the **Use another service to log in** section, choose either the **Google** or **Yahoo** submit button.</span></span> <span data-ttu-id="e7d93-141">Tento příklad používá přihlášení Google.</span><span class="sxs-lookup"><span data-stu-id="e7d93-141">This example uses the Google login.</span></span> 

    <span data-ttu-id="e7d93-142">Webové stránky přesměruje požadavek na přihlašovací stránku služby Google.</span><span class="sxs-lookup"><span data-stu-id="e7d93-142">The web page redirects the request to the Google login page.</span></span>

    ![Google přihlášení](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image2.png)
3. <span data-ttu-id="e7d93-144">Zadejte přihlašovací údaje pro účet Google.</span><span class="sxs-lookup"><span data-stu-id="e7d93-144">Enter credentials for an existing Google account.</span></span>
4. <span data-ttu-id="e7d93-145">Pokud Google se zeptá, jestli chcete povolit *Localhost* Pokud chcete informace z účtu, klikněte na tlačítko **povolit**.</span><span class="sxs-lookup"><span data-stu-id="e7d93-145">If Google asks you whether you want to allow *Localhost* to use information from the account, click **Allow**.</span></span>

    <span data-ttu-id="e7d93-146">Kód používá Google token k ověření uživatele a vrátí na tuto stránku na vašem webu.</span><span class="sxs-lookup"><span data-stu-id="e7d93-146">The code uses the Google token to authenticate the user, and then returns to this page on your website.</span></span> <span data-ttu-id="e7d93-147">Tato stránka umožňuje uživatelům přihlášení jejich Google přidružit účet na webu, nebo se můžete zaregistrovat nový účet na webu pro přidružení externí přihlášení s.</span><span class="sxs-lookup"><span data-stu-id="e7d93-147">This page lets users associate their Google login with an existing account on your website, or they can register a new account on your site to associate the external login with.</span></span>

    ![OAuth 5](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image3.png)
5. <span data-ttu-id="e7d93-149">Zvolte **přidružit** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e7d93-149">Choose the **Associate** button.</span></span> <span data-ttu-id="e7d93-150">Prohlížeč se vrátí na domovskou stránku vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7d93-150">The browser returns to your application's home page.</span></span>

<a id="To_enable_Facebook_logins"></a>
## <a name="enabling-facebook-logins"></a><span data-ttu-id="e7d93-151">Povolení přihlášení k Facebooku</span><span class="sxs-lookup"><span data-stu-id="e7d93-151">Enabling Facebook Logins</span></span>

1. <span data-ttu-id="e7d93-152">Přejděte [webu vývojáře služby Facebook](https://developers.facebook.com/apps) (v případě se přihlaste se ještě nepřihlásili).</span><span class="sxs-lookup"><span data-stu-id="e7d93-152">Go to the [Facebook developers site](https://developers.facebook.com/apps) (log in if you're not already logged in).</span></span>
2. <span data-ttu-id="e7d93-153">Zvolte **vytvořit novou aplikaci** tlačítko a pak postupujte podle výzev a pojmenujte a vytvořit novou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e7d93-153">Choose the **Create New App** button, and then follow the prompts to name and create the new application.</span></span>
3. <span data-ttu-id="e7d93-154">V části **vyberte, jak se vaše aplikace bude integrovat s Facebook**, zvolte **webu** oddílu.</span><span class="sxs-lookup"><span data-stu-id="e7d93-154">In the section **Select how your app will integrate with Facebook**, choose the **Website** section.</span></span>
4. <span data-ttu-id="e7d93-155">Vyplňte **adresa URL webu** pole s adresou URL vašeho webu (například `http://www.example.com`).</span><span class="sxs-lookup"><span data-stu-id="e7d93-155">Fill in the **Site URL** field with the URL of your site (for example, `http://www.example.com`).</span></span> <span data-ttu-id="e7d93-156">**Domény** pole je volitelné; může být využit k zajištění ověřování pro celou doménu (například *example.com*).</span><span class="sxs-lookup"><span data-stu-id="e7d93-156">The **Domain** field is optional; you can use this to provide authentication for an entire domain (such as *example.com*).</span></span> 

    > [!NOTE]
    > <span data-ttu-id="e7d93-157">Pokud používáte v místním počítači pomocí adresy URL webu, jako jsou `http://localhost:12345` (kde číslo je číslo místního portu), můžete přidat tuto hodnotu **adresa URL webu** pole pro testování webu.</span><span class="sxs-lookup"><span data-stu-id="e7d93-157">If you are running a site on your local computer with a URL like `http://localhost:12345` (where the number is a local port number), you can add this value to the **Site URL** field for testing your site.</span></span> <span data-ttu-id="e7d93-158">Však kdykoli číslo portu změny vaší místní sítě, budete muset aktualizovat **adresa URL webu** pole vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7d93-158">However, any time the port number of your local site changes, you will need to update the **Site URL** field of your application.</span></span>
5. <span data-ttu-id="e7d93-159">Zvolte **uložit změny** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e7d93-159">Choose the **Save Changes** button.</span></span>
6. <span data-ttu-id="e7d93-160">Zvolte **aplikace** kartu znovu a potom zobrazit úvodní stránku pro vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e7d93-160">Choose the **Apps** tab again, and then view the start page for your application.</span></span>
7. <span data-ttu-id="e7d93-161">Kopírovat **ID aplikace** a **tajný kód aplikace** hodnoty pro vaši aplikaci a vložte je do dočasné textového souboru.</span><span class="sxs-lookup"><span data-stu-id="e7d93-161">Copy the **App ID** and **App Secret** values for your application and paste them into a temporary text file.</span></span> <span data-ttu-id="e7d93-162">Tyto hodnoty předá k poskytovateli služby Facebook v kódu vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="e7d93-162">You will pass these values to the Facebook provider in your website code.</span></span>
8. <span data-ttu-id="e7d93-163">Ukončete webu pro vývojáře služby Facebook.</span><span class="sxs-lookup"><span data-stu-id="e7d93-163">Exit the Facebook developer site.</span></span>

<span data-ttu-id="e7d93-164">Teď provedete změny dvě stránky na webu tak, aby uživatelé mohli přihlásit k webu pomocí svých účtů služby Facebook.</span><span class="sxs-lookup"><span data-stu-id="e7d93-164">Now you make changes to two pages in your website so that users will able to log into the site using their Facebook accounts.</span></span>

1. <span data-ttu-id="e7d93-165">Vytvořit nebo otevřít web rozhraní ASP.NET Web Pages, který je založen na šabloně Starter web služby WebMatrix.</span><span class="sxs-lookup"><span data-stu-id="e7d93-165">Create or open an ASP.NET Web Pages site that's based on the WebMatrix Starter Site template.</span></span>
2. <span data-ttu-id="e7d93-166">Otevřít  *\_AppStart.cshtml* stránce a zrušte komentář kódu pro zprostředkovatele OAuth pro Facebook.</span><span class="sxs-lookup"><span data-stu-id="e7d93-166">Open the *\_AppStart.cshtml* page and uncomment the code for the Facebook OAuth provider.</span></span> <span data-ttu-id="e7d93-167">Blok neokomentovaném textu kódu vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="e7d93-167">The uncommented code block looks like the following:</span></span> 

    [!code-csharp[Main](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/samples/sample2.cs)]
3. <span data-ttu-id="e7d93-168">Kopírovat **ID aplikace** hodnotu z aplikace Facebook jako hodnotu `appId` parametrů (v uvozovkách).</span><span class="sxs-lookup"><span data-stu-id="e7d93-168">Copy the **App ID** value from the Facebook application as the value of the `appId` parameter (inside the quotation marks).</span></span>
4. <span data-ttu-id="e7d93-169">Kopírování **tajný kód aplikace** hodnotu z aplikace Facebook, jako `appSecret` hodnotu parametru.</span><span class="sxs-lookup"><span data-stu-id="e7d93-169">Copy **App Secret** value from the Facebook application as the `appSecret` parameter value.</span></span>
5. <span data-ttu-id="e7d93-170">Soubor uložte a zavřete.</span><span class="sxs-lookup"><span data-stu-id="e7d93-170">Save and close the file.</span></span>

### <a name="testing-facebook-login"></a><span data-ttu-id="e7d93-171">Testování přihlášení k Facebooku</span><span class="sxs-lookup"><span data-stu-id="e7d93-171">Testing Facebook login</span></span>

1. <span data-ttu-id="e7d93-172">Spuštění tohoto webu *stránku default.cshtml* stránky a zvolte **přihlášení** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e7d93-172">Run the site's *default.cshtml* page and choose the **Login** button.</span></span>
2. <span data-ttu-id="e7d93-173">Na *přihlášení* stránku, **přihlášení pomocí jiné služby** zvolte **Facebook** ikonu.</span><span class="sxs-lookup"><span data-stu-id="e7d93-173">On the *Login* page, in the **Use another service to log in** section, choose the **Facebook** icon.</span></span> 

    <span data-ttu-id="e7d93-174">Webové stránky přesměruje požadavek na přihlašovací stránku Facebooku.</span><span class="sxs-lookup"><span data-stu-id="e7d93-174">The web page redirects the request to the Facebook login page.</span></span>

    ![OAuth 2](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image4.png)
3. <span data-ttu-id="e7d93-176">Přihlaste se ke Facebookový účet.</span><span class="sxs-lookup"><span data-stu-id="e7d93-176">Log into a Facebook account.</span></span> 

    <span data-ttu-id="e7d93-177">Kód používá token služby Facebook pro ověření a vrátí na stránku, kde můžete přidružit k přihlášení k Facebooku přihlašovací jméno vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="e7d93-177">The code uses the Facebook token to authenticate you and then returns to a page where you can associate your Facebook login with your site's login.</span></span> <span data-ttu-id="e7d93-178">Uživatelského jména nebo e-mailové adresy se naplní do **e-mailu** pole ve formuláři.</span><span class="sxs-lookup"><span data-stu-id="e7d93-178">Your user name or email address is filled into the **Email** field on the form.</span></span>

    ![OAuth 5](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image5.png)
4. <span data-ttu-id="e7d93-180">Zvolte **přidružit** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e7d93-180">Choose the **Associate** button.</span></span> 

    <span data-ttu-id="e7d93-181">Vrátí prohlížeči na domovskou stránku a jste přihlášení.</span><span class="sxs-lookup"><span data-stu-id="e7d93-181">The browser returns to the home page and you are logged in.</span></span>

<a id="To_enable_Twitter_logins"></a>
## <a name="enabling-twitter-logins"></a><span data-ttu-id="e7d93-182">Povolení přihlášení k Twitteru</span><span class="sxs-lookup"><span data-stu-id="e7d93-182">Enabling Twitter Logins</span></span>

1. <span data-ttu-id="e7d93-183">Přejděte [webu vývojáře Twitteru](https://dev.twitter.com/).</span><span class="sxs-lookup"><span data-stu-id="e7d93-183">Browse to the [Twitter developers site](https://dev.twitter.com/).</span></span>
2. <span data-ttu-id="e7d93-184">Zvolte **vytvořit aplikaci** odkaz a přihlaste se do lokality.</span><span class="sxs-lookup"><span data-stu-id="e7d93-184">Choose the **Create an App** link and then log into the site.</span></span>
3. <span data-ttu-id="e7d93-185">Na **vytvořit aplikaci** formuláře, vyplňte **název** a **popis** pole.</span><span class="sxs-lookup"><span data-stu-id="e7d93-185">On the **Create an Application** form, fill in the **Name** and **Description** fields.</span></span>
4. <span data-ttu-id="e7d93-186">V **webu** pole, zadejte adresu URL vašeho webu (například `http://www.example.com`).</span><span class="sxs-lookup"><span data-stu-id="e7d93-186">In the **WebSite** field, enter the URL of your site (for example, `http://www.example.com`).</span></span> 

    > [!NOTE]
    > <span data-ttu-id="e7d93-187">Pokud testujete místně webu (pomocí adresy URL `http://localhost:12345`), Twitter, nemusí přijmout adresu URL.</span><span class="sxs-lookup"><span data-stu-id="e7d93-187">If you're testing your site locally (using a URL like `http://localhost:12345`), Twitter might not accept the URL.</span></span> <span data-ttu-id="e7d93-188">Nicméně je možné používat zpětnou smyčku místní IP adresu (například `http://127.0.0.1:12345`).</span><span class="sxs-lookup"><span data-stu-id="e7d93-188">However, you might be able to use the local loopback IP address (for example `http://127.0.0.1:12345`).</span></span> <span data-ttu-id="e7d93-189">To zjednodušuje proces testování vaší aplikace v místním prostředí.</span><span class="sxs-lookup"><span data-stu-id="e7d93-189">This simplifies the process of testing your application locally.</span></span> <span data-ttu-id="e7d93-190">Ale pokaždé, když se mění číslo portu z místní lokality, bude nutné aktualizovat **webu** pole vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7d93-190">However, every time the port number of your local site changes, you'll need to update the **WebSite** field of your application.</span></span>
5. <span data-ttu-id="e7d93-191">V **adresu URL zpětného volání** pole, zadejte adresu URL pro stránku na vašem webu, kterou mají uživatelé k vrácení po přihlášení na Twitteru.</span><span class="sxs-lookup"><span data-stu-id="e7d93-191">In the **Callback URL** field, enter a URL for the page in your website that you want users to return to after logging into Twitter.</span></span> <span data-ttu-id="e7d93-192">Například pokud chcete odeslat uživatelů na domovskou stránku Starter Site (který rozpozná stav jejich přihlášení), zadejte stejnou adresu URL, kterou jste zadali v **webu** pole.</span><span class="sxs-lookup"><span data-stu-id="e7d93-192">For example, to send users to the home page of the Starter Site (which will recognize their logged-in status), enter the same URL that you entered in the **WebSite** field.</span></span>
6. <span data-ttu-id="e7d93-193">Přijměte podmínky, zvolte **vytvoření aplikace Twitter** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e7d93-193">Accept the terms and choose the **Create your Twitter application** button.</span></span>
7. <span data-ttu-id="e7d93-194">Na **Moje aplikace** úvodní stránka, zvolte aplikaci, kterou jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="e7d93-194">On the **My Applications** landing page, choose the application you created.</span></span>
8. <span data-ttu-id="e7d93-195">Na **podrobnosti** , posuňte dolů a klikněte na příkaz **vytvořit My Access Token** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e7d93-195">On the **Details** tab, scroll to the bottom and choose the **Create My Access Token** button.</span></span>
9. <span data-ttu-id="e7d93-196">Na **podrobnosti** kartu, zkopírujte **uživatelský klíč** a **uživatelský tajný klíč** hodnoty pro vaši aplikaci a vložte je do dočasné textového souboru.</span><span class="sxs-lookup"><span data-stu-id="e7d93-196">On the **Details** tab, copy the **Consumer Key** and **Consumer Secret** values for your application and paste them into a temporary text file.</span></span> <span data-ttu-id="e7d93-197">Tyto hodnoty budete předat poskytovatele Twitteru ve vašem kódu webu.</span><span class="sxs-lookup"><span data-stu-id="e7d93-197">You'll pass these values to the Twitter provider in your website code.</span></span>
10. <span data-ttu-id="e7d93-198">Ukončení serveru Twitter.</span><span class="sxs-lookup"><span data-stu-id="e7d93-198">Exit the Twitter site.</span></span>

<span data-ttu-id="e7d93-199">Teď provedete změny dvě stránky na webu tak, aby uživatelé se nebudou moct přihlásit k webu pomocí svých účtů na Twitteru.</span><span class="sxs-lookup"><span data-stu-id="e7d93-199">Now you make changes to two pages in your website so that users will be able to log into the site using their Twitter accounts.</span></span>

1. <span data-ttu-id="e7d93-200">Vytvořit nebo otevřít web rozhraní ASP.NET Web Pages, který je založen na šabloně Starter web služby WebMatrix.</span><span class="sxs-lookup"><span data-stu-id="e7d93-200">Create or open an ASP.NET Web Pages site that's based on the WebMatrix Starter Site template.</span></span>
2. <span data-ttu-id="e7d93-201">Otevřít  *\_AppStart.cshtml* stránce a zrušte komentář kódu pro zprostředkovatele OAuth pro Twitter.</span><span class="sxs-lookup"><span data-stu-id="e7d93-201">Open the *\_AppStart.cshtml* page and uncomment the code for the Twitter OAuth provider.</span></span> <span data-ttu-id="e7d93-202">Blok neokomentovaném textu kódu vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="e7d93-202">The uncommented code block looks like this:</span></span> 

    [!code-csharp[Main](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/samples/sample3.cs)]
3. <span data-ttu-id="e7d93-203">Kopírovat **uživatelský klíč** hodnota Twitter application jako hodnotu `consumerKey` parametrů (v uvozovkách).</span><span class="sxs-lookup"><span data-stu-id="e7d93-203">Copy the **Consumer Key** value from the Twitter application as the value of the `consumerKey` parameter (inside the quotation marks).</span></span>
4. <span data-ttu-id="e7d93-204">Kopírovat **uživatelský tajný klíč** hodnota Twitter application jako hodnotu `consumerSecret` parametru.</span><span class="sxs-lookup"><span data-stu-id="e7d93-204">Copy the **Consumer Secret** value from the Twitter application as the value of the `consumerSecret` parameter.</span></span>
5. <span data-ttu-id="e7d93-205">Soubor uložte a zavřete.</span><span class="sxs-lookup"><span data-stu-id="e7d93-205">Save and close the file.</span></span>

### <a name="testing-twitter-login"></a><span data-ttu-id="e7d93-206">Testování Twitteru přihlášení</span><span class="sxs-lookup"><span data-stu-id="e7d93-206">Testing Twitter login</span></span>

1. <span data-ttu-id="e7d93-207">Spustit *stránku default.cshtml* stránku vašeho webu a zvolte **přihlášení** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e7d93-207">Run the *default.cshtml* page of your site and choose the **Login** button.</span></span>
2. <span data-ttu-id="e7d93-208">Na *přihlášení* stránku, **přihlášení pomocí jiné služby** zvolte **Twitteru** ikonu.</span><span class="sxs-lookup"><span data-stu-id="e7d93-208">On the *Login* page, in the **Use another service to log in** section, choose the **Twitter** icon.</span></span> 

    <span data-ttu-id="e7d93-209">Webové stránky přesměruje požadavek na stránku Twitteru přihlášení pro aplikaci, kterou jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="e7d93-209">The web page redirects the request to a Twitter login page for the application you created.</span></span>

    ![OAuth 4](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image6.png)
3. <span data-ttu-id="e7d93-211">Přihlaste se ke účtu sítě Twitter.</span><span class="sxs-lookup"><span data-stu-id="e7d93-211">Log into a Twitter account.</span></span>
4. <span data-ttu-id="e7d93-212">Tento kód používá token služby Twitter. k ověření uživatele a pak se vrátíte na stránku ve kterém můžete přidružit vaše přihlášení pomocí svého účtu Web.</span><span class="sxs-lookup"><span data-stu-id="e7d93-212">The code uses the Twitter token to authenticate the user and then returns you to a page where you can associate your login with your website account.</span></span> <span data-ttu-id="e7d93-213">Jména nebo e-mailové adresy se naplní do **e-mailu** pole ve formuláři.</span><span class="sxs-lookup"><span data-stu-id="e7d93-213">Your name or email address is filled into the **Email** field on the form.</span></span>

    ![OAuth 5](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image7.png)
5. <span data-ttu-id="e7d93-215">Zvolte **přidružit** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e7d93-215">Choose the **Associate** button.</span></span> 

    <span data-ttu-id="e7d93-216">Vrátí prohlížeči na domovskou stránku a jste přihlášení.</span><span class="sxs-lookup"><span data-stu-id="e7d93-216">The browser returns to the home page and you are logged in.</span></span>

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a><span data-ttu-id="e7d93-217">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="e7d93-217">Additional Resources</span></span>


- [<span data-ttu-id="e7d93-218">Přizpůsobení chování v celém webu</span><span class="sxs-lookup"><span data-stu-id="e7d93-218">Customizing Site-Wide Behavior</span></span>](https://go.microsoft.com/fwlink/?LinkId=202906)
- [<span data-ttu-id="e7d93-219">Přidání zabezpečení a s členstvím na ASP.NET Web Pages</span><span class="sxs-lookup"><span data-stu-id="e7d93-219">Adding Security and Membership to an ASP.NET Web Pages Site</span></span>](https://go.microsoft.com/fwlink/?LinkID=202904)
