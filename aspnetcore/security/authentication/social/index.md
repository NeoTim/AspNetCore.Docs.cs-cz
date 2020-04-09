---
title: Ověřování na Facebooku, Googlu a externím poskytovateli v ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, jak vytvořit aplikaci ASP.NET Core pomocí OAuth 2.0 s externími poskytovateli ověřování.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/authentication/social/index
ms.openlocfilehash: c698edbd85d665509366287b1dcad08e276e71cc
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78668040"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="f8770-103">Ověřování na Facebooku, Googlu a externím poskytovateli v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8770-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="f8770-104">[Valerij Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f8770-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f8770-105">Tento kurz ukazuje, jak vytvořit ASP.NET aplikaci Core 3.0, která umožňuje uživatelům přihlásit pomocí OAuth 2.0 s pověřeními od externích poskytovatelů ověřování.</span><span class="sxs-lookup"><span data-stu-id="f8770-105">This tutorial demonstrates how to build an ASP.NET Core 3.0 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="f8770-106">[Poskytovatelé Facebooku](xref:security/authentication/facebook-logins), [Twitteru](xref:security/authentication/twitter-logins), [Googlu](xref:security/authentication/google-logins)a [Microsoftu](xref:security/authentication/microsoft-logins) jsou zahrnuti v následujících částech a používají počáteční projekt vytvořený v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="f8770-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="f8770-107">Další poskytovatelé jsou k dispozici v balíčcích třetích stran, jako je [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) a [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span><span class="sxs-lookup"><span data-stu-id="f8770-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="f8770-108">Povolení uživatelům přihlásit se pomocí jejich stávajících přihlašovacích údajů:</span><span class="sxs-lookup"><span data-stu-id="f8770-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="f8770-109">Je vhodný pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="f8770-109">Is convenient for the users.</span></span>
* <span data-ttu-id="f8770-110">Přesune mnoho složitostí správy procesu přihlášení na třetí stranu.</span><span class="sxs-lookup"><span data-stu-id="f8770-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="f8770-111">Příklady toho, jak mohou přihlášení na sociální chod řídit návštěvnost a konverze zákazníků, najdete v tématu případové studie společností [Facebook](https://www.facebook.com/unsupportedbrowser) a [Twitter](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="f8770-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="f8770-112">Vytvoření nového ASP.NET základního projektu</span><span class="sxs-lookup"><span data-stu-id="f8770-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8770-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8770-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f8770-114">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="f8770-114">Create a new project.</span></span>
* <span data-ttu-id="f8770-115">Vyberte **ASP.NET základní webovou aplikaci** a **další**.</span><span class="sxs-lookup"><span data-stu-id="f8770-115">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="f8770-116">Zadejte **název projektu** a potvrďte nebo změňte **umístění**.</span><span class="sxs-lookup"><span data-stu-id="f8770-116">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="f8770-117">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="f8770-117">Select **Create**.</span></span>
* <span data-ttu-id="f8770-118">V rozevíracím souboru **(ASP.NET Jádrem {X.Y}** vyberte nejnovější verzi ASP.NET jádra a pak vyberte **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="f8770-118">Select the latest version of ASP.NET Core in the drop-down (**ASP.NET Core {X.Y}**), and then select **Web Application**.</span></span>
* <span data-ttu-id="f8770-119">V části **Ověřování**vyberte **Změnit** a nastavte ověřování na **jednotlivé uživatelské účty**.</span><span class="sxs-lookup"><span data-stu-id="f8770-119">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="f8770-120">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="f8770-120">Select **OK**.</span></span>
* <span data-ttu-id="f8770-121">V okně **Vytvořit novou ASP.NET základní webovou aplikaci** vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="f8770-121">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f8770-122">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f8770-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f8770-123">Otevřete terminál.</span><span class="sxs-lookup"><span data-stu-id="f8770-123">Open the terminal.</span></span>  <span data-ttu-id="f8770-124">Pro Visual Studio Code můžete otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f8770-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="f8770-125">Změňte adresáře (`cd`) na složku, která bude projekt obsahovat.</span><span class="sxs-lookup"><span data-stu-id="f8770-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="f8770-126">V případě Windows spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f8770-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="f8770-127">Pro macOS a Linux spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f8770-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="f8770-128">Příkaz `dotnet new` vytvoří nový projekt Razor Pages ve složce *WebApp1.*</span><span class="sxs-lookup"><span data-stu-id="f8770-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="f8770-129">`-au Individual`vytvoří kód pro individuální ověřování.</span><span class="sxs-lookup"><span data-stu-id="f8770-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="f8770-130">`-uld`používá LocalDB, odlehčenou verzi SQL Server Express pro Windows.</span><span class="sxs-lookup"><span data-stu-id="f8770-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="f8770-131">Vynechat `-uld` použití SQLite.</span><span class="sxs-lookup"><span data-stu-id="f8770-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="f8770-132">Příkaz `code` otevře složku *WebApp1* v nové instanci kódu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f8770-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="f8770-133">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="f8770-133">Apply migrations</span></span>

* <span data-ttu-id="f8770-134">Spusťte aplikaci a vyberte odkaz **Registrovat.**</span><span class="sxs-lookup"><span data-stu-id="f8770-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="f8770-135">Zadejte e-mail a heslo pro nový účet a pak vyberte **Registrovat**.</span><span class="sxs-lookup"><span data-stu-id="f8770-135">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="f8770-136">Podle pokynů použijte migrace.</span><span class="sxs-lookup"><span data-stu-id="f8770-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="f8770-137">Použití nástroje SecretManager k ukládání tokenů přiřazených poskytovateli přihlášení</span><span class="sxs-lookup"><span data-stu-id="f8770-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="f8770-138">Zprostředkovatelé přihlášení **na sociální chod** přiřazují tokeny Id aplikace a **tajných aplikací** během procesu registrace.</span><span class="sxs-lookup"><span data-stu-id="f8770-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="f8770-139">Přesné názvy tokenů se liší podle zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="f8770-139">The exact token names vary by provider.</span></span> <span data-ttu-id="f8770-140">Tyto tokeny představují přihlašovací údaje, které vaše aplikace používá pro přístup k jejich rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="f8770-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="f8770-141">Tokeny představují "tajné klíče", které lze propojit s konfigurací aplikace pomocí [Správce tajných barev](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="f8770-141">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="f8770-142">Správce tajných barev je bezpečnější alternativou k ukládání tokenů v konfiguračním souboru, například *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f8770-142">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f8770-143">Tajný správce je pouze pro účely vývoje.</span><span class="sxs-lookup"><span data-stu-id="f8770-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="f8770-144">Tajné klíče azure test a produkční verze můžete ukládat a chránit pomocí [zprostředkovatele konfigurace azure key vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="f8770-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="f8770-145">Postupujte podle kroků v [části Bezpečné ukládání tajných kódů aplikací ve vývoji v tématu ASP.NET Core](xref:security/app-secrets) pro ukládání tokenů přiřazených jednotlivými poskytovateli přihlášení níže.</span><span class="sxs-lookup"><span data-stu-id="f8770-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="f8770-146">Nastavení zprostředkovatelů přihlášení vyžadovaných vaší aplikací</span><span class="sxs-lookup"><span data-stu-id="f8770-146">Setup login providers required by your application</span></span>

<span data-ttu-id="f8770-147">Ke konfiguraci aplikace pro použití příslušných zprostředkovatelů použijte následující témata:</span><span class="sxs-lookup"><span data-stu-id="f8770-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="f8770-148">[Pokyny k Facebooku](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="f8770-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="f8770-149">[Twitter](xref:security/authentication/twitter-logins) návod</span><span class="sxs-lookup"><span data-stu-id="f8770-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="f8770-150">[Pokyny k Googlu](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="f8770-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="f8770-151">[Pokyny společnosti Microsoft](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="f8770-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="f8770-152">[Další](xref:security/authentication/otherlogins) pokyny poskytovatele</span><span class="sxs-lookup"><span data-stu-id="f8770-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="f8770-153">Volitelně nastavit heslo</span><span class="sxs-lookup"><span data-stu-id="f8770-153">Optionally set password</span></span>

<span data-ttu-id="f8770-154">Když se zaregistrujete u externího poskytovatele přihlašovacích údajů, nemáte heslo registrované v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8770-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="f8770-155">To zmírňuje vytváření a zapamatování hesla pro web, ale také vás činí závislými na externím poskytovateli přihlášení.</span><span class="sxs-lookup"><span data-stu-id="f8770-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="f8770-156">Pokud externí poskytovatel přihlášení není k dispozici, nebudete se moci přihlásit k webu.</span><span class="sxs-lookup"><span data-stu-id="f8770-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="f8770-157">Vytvoření hesla a přihlášení pomocí e-mailu, který jste nastavili během procesu přihlášení u externích poskytovatelů:</span><span class="sxs-lookup"><span data-stu-id="f8770-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="f8770-158">Vyberte odkaz \*\*Dobrý e-mailový &lt;alias&gt; \*\* v pravém horním rohu a přejděte do zobrazení **Spravovat.**</span><span class="sxs-lookup"><span data-stu-id="f8770-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Webová aplikace Spravovat zobrazení](index/_static/pass1a.png)

* <span data-ttu-id="f8770-160">Vybrat **vytvořit**</span><span class="sxs-lookup"><span data-stu-id="f8770-160">Select **Create**</span></span>

![Nastavení stránky s heslem](index/_static/pass2a.png)

* <span data-ttu-id="f8770-162">Nastavte platné heslo a můžete se pomocí tohoto nastavení přihlásit pomocí e-mailu.</span><span class="sxs-lookup"><span data-stu-id="f8770-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f8770-163">Další kroky</span><span class="sxs-lookup"><span data-stu-id="f8770-163">Next steps</span></span>

* <span data-ttu-id="f8770-164">Informace o tom, jak přizpůsobit přihlašovací tlačítka, najdete v tomto problému s [GitHubem.](https://github.com/dotnet/AspNetCore.Docs/issues/10563)</span><span class="sxs-lookup"><span data-stu-id="f8770-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="f8770-165">Tento článek zavedl externí ověřování a vysvětlil požadavky potřebné k přidání externích přihlášení do aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8770-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="f8770-166">Odkaz na stránky specifické pro zprostředkovatele pro konfiguraci přihlášení pro zprostředkovatele vyžadované vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="f8770-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="f8770-167">Můžete chtít zachovat další data o uživateli a jeho přístup a aktualizovat tokeny.</span><span class="sxs-lookup"><span data-stu-id="f8770-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="f8770-168">Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="f8770-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
