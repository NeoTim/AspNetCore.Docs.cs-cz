---
title: Ověřování pro Facebook, Google a externí poskytovatele v ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, jak vytvořit aplikaci ASP.NET Core pomocí OAuth 2,0 s externími poskytovateli ověřování.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/index
ms.openlocfilehash: 880aeea4dce5f5ae6533a3293067d89f98587e72
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777147"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="e945f-103">Ověřování pro Facebook, Google a externí poskytovatele v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e945f-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="e945f-104">Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e945f-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e945f-105">Tento kurz ukazuje, jak vytvořit aplikaci ASP.NET Core 3,0, která uživatelům umožňuje přihlásit se pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování.</span><span class="sxs-lookup"><span data-stu-id="e945f-105">This tutorial demonstrates how to build an ASP.NET Core 3.0 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="e945f-106">Poskytovatelé [Facebooku](xref:security/authentication/facebook-logins), [Twitteru](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins)a [Microsoftu](xref:security/authentication/microsoft-logins) jsou popsaná v následujících částech a používají počáteční projekt vytvořený v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="e945f-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="e945f-107">Jiní poskytovatelé jsou k dispozici v balíčcích třetích stran, jako jsou [ASPNET. Security. OAuth. Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) a [ASPNET. Security. OpenId. Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span><span class="sxs-lookup"><span data-stu-id="e945f-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="e945f-108">Povolení přihlášení uživatelů pomocí stávajících přihlašovacích údajů:</span><span class="sxs-lookup"><span data-stu-id="e945f-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="e945f-109">Je vhodné pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="e945f-109">Is convenient for the users.</span></span>
* <span data-ttu-id="e945f-110">Posune mnoho složitých složitosti při správě procesu přihlašování na třetí stranu.</span><span class="sxs-lookup"><span data-stu-id="e945f-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="e945f-111">Příklady, jak mohou sociální přihlášení prosazovat přenosy a převody zákazníků, najdete v tématu případové studie na [Facebooku](https://www.facebook.com/unsupportedbrowser) a [Twitteru](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="e945f-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="e945f-112">Vytvoření nového projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e945f-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e945f-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e945f-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e945f-114">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="e945f-114">Create a new project.</span></span>
* <span data-ttu-id="e945f-115">Vyberte **ASP.NET Core webová aplikace** a **Další**.</span><span class="sxs-lookup"><span data-stu-id="e945f-115">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="e945f-116">Zadejte **název projektu** a potvrďte nebo změňte **umístění**.</span><span class="sxs-lookup"><span data-stu-id="e945f-116">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="e945f-117">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e945f-117">Select **Create**.</span></span>
* <span data-ttu-id="e945f-118">V rozevíracím seznamu vyberte nejnovější verzi ASP.NET Core (**ASP.NET Core {X. Y}**) a pak vyberte **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="e945f-118">Select the latest version of ASP.NET Core in the drop-down (**ASP.NET Core {X.Y}**), and then select **Web Application**.</span></span>
* <span data-ttu-id="e945f-119">V části **ověřování**vyberte **změnit** a nastavte ověřování na **jednotlivé uživatelské účty**.</span><span class="sxs-lookup"><span data-stu-id="e945f-119">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="e945f-120">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="e945f-120">Select **OK**.</span></span>
* <span data-ttu-id="e945f-121">V okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e945f-121">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e945f-122">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e945f-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e945f-123">Otevřete terminál.</span><span class="sxs-lookup"><span data-stu-id="e945f-123">Open the terminal.</span></span>  <span data-ttu-id="e945f-124">Pro Visual Studio Code můžete otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e945f-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="e945f-125">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="e945f-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="e945f-126">V případě Windows spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e945f-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="e945f-127">Pro macOS a Linux spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e945f-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="e945f-128">`dotnet new` Příkaz vytvoří nový projekt Razor Pages ve složce *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="e945f-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="e945f-129">`-au Individual`vytvoří kód pro individuální ověřování.</span><span class="sxs-lookup"><span data-stu-id="e945f-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="e945f-130">`-uld`používá LocalDB, zjednodušenou verzi SQL Server Express pro Windows.</span><span class="sxs-lookup"><span data-stu-id="e945f-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="e945f-131">Vynechejte `-uld` pro použití sqlite.</span><span class="sxs-lookup"><span data-stu-id="e945f-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="e945f-132">`code` Příkaz otevře složku *WebApp1* v nové instanci Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e945f-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="e945f-133">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="e945f-133">Apply migrations</span></span>

* <span data-ttu-id="e945f-134">Spusťte aplikaci a vyberte odkaz **Registrovat** .</span><span class="sxs-lookup"><span data-stu-id="e945f-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="e945f-135">Zadejte e-mail a heslo nového účtu a pak vyberte **zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="e945f-135">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="e945f-136">Při instalaci migrace postupujte podle pokynů.</span><span class="sxs-lookup"><span data-stu-id="e945f-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="e945f-137">Ukládat tokeny přiřazené poskytovateli přihlášení pomocí SecretManager</span><span class="sxs-lookup"><span data-stu-id="e945f-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="e945f-138">Poskytovatelé sociálního přihlášení přidělují **ID aplikace** a tajné tokeny **aplikace** během procesu registrace.</span><span class="sxs-lookup"><span data-stu-id="e945f-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="e945f-139">Přesné názvy tokenů se liší podle poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="e945f-139">The exact token names vary by provider.</span></span> <span data-ttu-id="e945f-140">Tyto tokeny reprezentují přihlašovací údaje, které aplikace používá pro přístup ke svým rozhraním API.</span><span class="sxs-lookup"><span data-stu-id="e945f-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="e945f-141">Tokeny představují "tajné klíče", které lze propojit s konfigurací aplikace pomocí [správce tajných klíčů](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="e945f-141">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="e945f-142">Správce tajných klíčů je bezpečnější alternativou pro ukládání tokenů do konfiguračního souboru, například *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="e945f-142">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e945f-143">Správce tajných klíčů je jenom pro účely vývoje.</span><span class="sxs-lookup"><span data-stu-id="e945f-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="e945f-144">Pomocí [poskytovatele konfigurace Azure Key Vault](xref:security/key-vault-configuration)můžete ukládat a chránit tajné kódy Azure test a produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="e945f-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="e945f-145">Postupujte podle kroků v [bezpečném úložišti tajných kódů aplikací ve vývoji v tématu ASP.NET Core](xref:security/app-secrets) tématu uložení tokenů přiřazených každým poskytovatelem přihlašovacích údajů níže.</span><span class="sxs-lookup"><span data-stu-id="e945f-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="e945f-146">Nastavení poskytovatelů přihlášení požadovaných vaší aplikací</span><span class="sxs-lookup"><span data-stu-id="e945f-146">Setup login providers required by your application</span></span>

<span data-ttu-id="e945f-147">Pomocí následujících témat můžete nakonfigurovat aplikaci tak, aby používala příslušné poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="e945f-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="e945f-148">Pokyny pro [Facebook](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="e945f-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="e945f-149">Pokyny pro [Twitter](xref:security/authentication/twitter-logins)</span><span class="sxs-lookup"><span data-stu-id="e945f-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="e945f-150">Pokyny pro [Google](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="e945f-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="e945f-151">Pokyny [Microsoftu](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="e945f-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="e945f-152">[Další](xref:security/authentication/otherlogins) pokyny pro poskytovatele</span><span class="sxs-lookup"><span data-stu-id="e945f-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="e945f-153">Volitelně nastavit heslo</span><span class="sxs-lookup"><span data-stu-id="e945f-153">Optionally set password</span></span>

<span data-ttu-id="e945f-154">Když se zaregistrujete u externího poskytovatele přihlašovacích údajů, nemáte v aplikaci zaregistrovaným heslem.</span><span class="sxs-lookup"><span data-stu-id="e945f-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="e945f-155">To vám pomůžeme při vytváření a zapamatování hesla pro web, ale také vám to závisí na externím poskytovateli přihlášení.</span><span class="sxs-lookup"><span data-stu-id="e945f-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="e945f-156">Pokud externí poskytovatel přihlášení není dostupný, nebudete se moct přihlásit k webu.</span><span class="sxs-lookup"><span data-stu-id="e945f-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="e945f-157">Pokud chcete vytvořit heslo a přihlásit se pomocí e-mailu, který jste nastavili během procesu přihlašování pomocí externích zprostředkovatelů:</span><span class="sxs-lookup"><span data-stu-id="e945f-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="e945f-158">Vyberte odkaz **e &lt;-mailový alias&gt; Hello** v pravém horním rohu a přejděte do zobrazení **Správa** .</span><span class="sxs-lookup"><span data-stu-id="e945f-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Správa zobrazení webové aplikace](index/_static/pass1a.png)

* <span data-ttu-id="e945f-160">Vyberte **vytvořit** .</span><span class="sxs-lookup"><span data-stu-id="e945f-160">Select **Create**</span></span>

![Nastavení stránky pro heslo](index/_static/pass2a.png)

* <span data-ttu-id="e945f-162">Nastavte platné heslo a můžete ho použít k přihlášení pomocí e-mailu.</span><span class="sxs-lookup"><span data-stu-id="e945f-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e945f-163">Další kroky</span><span class="sxs-lookup"><span data-stu-id="e945f-163">Next steps</span></span>

* <span data-ttu-id="e945f-164">Informace o přizpůsobení přihlašovacích tlačítek najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/10563) .</span><span class="sxs-lookup"><span data-stu-id="e945f-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="e945f-165">Tento článek představil externí ověřování a vysvětluje požadavky potřebné k přidání externích přihlášení do vaší aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e945f-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="e945f-166">Stránky specifické pro poskytovatele odkazů pro konfiguraci přihlášení pro poskytovatele vyžadované vaší aplikací</span><span class="sxs-lookup"><span data-stu-id="e945f-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="e945f-167">Můžete chtít zachovat další data o uživateli a jejich přístup a aktualizovat tokeny.</span><span class="sxs-lookup"><span data-stu-id="e945f-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="e945f-168">Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="e945f-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
