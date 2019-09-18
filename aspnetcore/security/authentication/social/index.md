---
title: Ověřování pro Facebook, Google a externí poskytovatele v ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, jak vytvořit aplikaci ASP.NET Core 2. x pomocí OAuth 2,0 s externími poskytovateli ověřování.
ms.author: riande
ms.custom: mvc
ms.date: 05/10/2019
uid: security/authentication/social/index
ms.openlocfilehash: edaf9eeaf02879b2f7816bab0eb373a7de640c05
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082508"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="d8046-103">Ověřování pro Facebook, Google a externí poskytovatele v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8046-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="d8046-104">Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d8046-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d8046-105">Tento kurz ukazuje, jak vytvořit aplikaci ASP.NET Core 2,2, která uživatelům umožňuje přihlásit se pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování.</span><span class="sxs-lookup"><span data-stu-id="d8046-105">This tutorial demonstrates how to build an ASP.NET Core 2.2 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="d8046-106">Poskytovatelé [Facebooku](xref:security/authentication/facebook-logins), [Twitteru](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins)a [Microsoftu](xref:security/authentication/microsoft-logins) jsou popsaná v následujících částech.</span><span class="sxs-lookup"><span data-stu-id="d8046-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections.</span></span> <span data-ttu-id="d8046-107">Jiní poskytovatelé jsou k dispozici v balíčcích třetích stran, jako jsou [ASPNET. Security. OAuth. Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) a [ASPNET. Security. OpenId. Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span><span class="sxs-lookup"><span data-stu-id="d8046-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

![Ikony sociálních médií pro Facebook, Twitter, Google Plus a Windows](index/_static/social.png)

<span data-ttu-id="d8046-109">Povolení přihlášení uživatelů pomocí stávajících přihlašovacích údajů:</span><span class="sxs-lookup"><span data-stu-id="d8046-109">Enabling users to sign in with their existing credentials:</span></span>
* <span data-ttu-id="d8046-110">Je vhodné pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="d8046-110">Is convenient for the users.</span></span>
* <span data-ttu-id="d8046-111">Posune mnoho složitých složitosti při správě procesu přihlašování na třetí stranu.</span><span class="sxs-lookup"><span data-stu-id="d8046-111">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span> 

<span data-ttu-id="d8046-112">Příklady, jak mohou sociální přihlášení prosazovat přenosy a převody zákazníků, najdete v tématu případové studie na [Facebooku](https://www.facebook.com/unsupportedbrowser) a [Twitteru](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="d8046-112">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="d8046-113">Vytvoření nového projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8046-113">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d8046-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8046-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8046-115">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="d8046-115">Create a new project.</span></span>
* <span data-ttu-id="d8046-116">Vyberte **ASP.NET Core webová aplikace** a **Další**.</span><span class="sxs-lookup"><span data-stu-id="d8046-116">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="d8046-117">Zadejte **název projektu** a potvrďte nebo změňte **umístění**.</span><span class="sxs-lookup"><span data-stu-id="d8046-117">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="d8046-118">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d8046-118">Select **Create**.</span></span>
* <span data-ttu-id="d8046-119">V rozevíracím seznamu vyberte **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="d8046-119">Select **ASP.NET Core 2.2** in the drop down.</span></span> <span data-ttu-id="d8046-120">V seznamu šablon vyberte **Webová aplikace** .</span><span class="sxs-lookup"><span data-stu-id="d8046-120">Select **Web Application** in the template list.</span></span>
* <span data-ttu-id="d8046-121">V části **ověřování**vyberte **změnit** a nastavte ověřování na **jednotlivé uživatelské účty**.</span><span class="sxs-lookup"><span data-stu-id="d8046-121">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="d8046-122">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8046-122">Select **OK**.</span></span>
* <span data-ttu-id="d8046-123">V okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d8046-123">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d8046-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8046-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d8046-125">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d8046-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="d8046-126">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="d8046-126">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="d8046-127">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d8046-127">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  code -r WebApp1
  ```

  * <span data-ttu-id="d8046-128">Příkaz vytvoří nový projekt Razor Pages ve složce WebApp1. `dotnet new`</span><span class="sxs-lookup"><span data-stu-id="d8046-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="d8046-129">`-uld`místo SQLite používá LocalDB.</span><span class="sxs-lookup"><span data-stu-id="d8046-129">`-uld` uses LocalDB instead of SQLite.</span></span> <span data-ttu-id="d8046-130">Vynechejte `-uld` pro použití sqlite.</span><span class="sxs-lookup"><span data-stu-id="d8046-130">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="d8046-131">`-au Individual`vytvoří kód pro individuální ověřování.</span><span class="sxs-lookup"><span data-stu-id="d8046-131">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="d8046-132">Příkaz otevře složku WebApp1 v nové instanci Visual Studio Code. `code`</span><span class="sxs-lookup"><span data-stu-id="d8046-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

* <span data-ttu-id="d8046-133">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' WebApp1 '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="d8046-133">A dialog box appears with **Required assets to build and debug are missing from 'WebApp1'. Add them?**</span></span> <span data-ttu-id="d8046-134">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="d8046-134">Select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d8046-135">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d8046-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d8046-136">Vyberte **souboru** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="d8046-136">Select **File** > **New Solution**.</span></span>
* <span data-ttu-id="d8046-137">Na bočním panelu vyberte**aplikaci** **.NET Core** > .</span><span class="sxs-lookup"><span data-stu-id="d8046-137">Select **.NET Core** > **App** in the sidebar.</span></span> <span data-ttu-id="d8046-138">Vyberte šablonu **webové aplikace** .</span><span class="sxs-lookup"><span data-stu-id="d8046-138">Select the **Web Application** template.</span></span> <span data-ttu-id="d8046-139">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="d8046-139">Select **Next**.</span></span>
* <span data-ttu-id="d8046-140">V rozevíracím seznamu **Cílová architektura** nastavte **.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="d8046-140">Set the **Target Framework** drop down to **.NET Core 2.2**.</span></span> <span data-ttu-id="d8046-141">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="d8046-141">Select **Next**.</span></span>
* <span data-ttu-id="d8046-142">Zadejte **název projektu**.</span><span class="sxs-lookup"><span data-stu-id="d8046-142">Provide a **Project Name**.</span></span> <span data-ttu-id="d8046-143">Potvrďte nebo změňte **umístění**.</span><span class="sxs-lookup"><span data-stu-id="d8046-143">Confirm or change the **Location**.</span></span> <span data-ttu-id="d8046-144">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d8046-144">Select **Create**.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="d8046-145">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="d8046-145">Apply migrations</span></span>

* <span data-ttu-id="d8046-146">Spusťte aplikaci a vyberte odkaz **Registrovat** .</span><span class="sxs-lookup"><span data-stu-id="d8046-146">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="d8046-147">Zadejte e-mail a heslo nového účtu a pak vyberte **zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="d8046-147">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="d8046-148">Při instalaci migrace postupujte podle pokynů.</span><span class="sxs-lookup"><span data-stu-id="d8046-148">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="d8046-149">Ukládat tokeny přiřazené poskytovateli přihlášení pomocí SecretManager</span><span class="sxs-lookup"><span data-stu-id="d8046-149">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="d8046-150">Poskytovatelé sociálního přihlášení přidělují **ID aplikace** a tajné tokeny **aplikace** během procesu registrace.</span><span class="sxs-lookup"><span data-stu-id="d8046-150">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="d8046-151">Přesné názvy tokenů se liší podle poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="d8046-151">The exact token names vary by provider.</span></span> <span data-ttu-id="d8046-152">Tyto tokeny reprezentují přihlašovací údaje, které aplikace používá pro přístup ke svým rozhraním API.</span><span class="sxs-lookup"><span data-stu-id="d8046-152">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="d8046-153">Tokeny představují "tajné klíče", které lze propojit s konfigurací aplikace pomocí [správce tajných klíčů](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="d8046-153">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="d8046-154">Správce tajných klíčů je bezpečnější alternativou pro ukládání tokenů do konfiguračního souboru, například *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d8046-154">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d8046-155">Správce tajných klíčů je jenom pro účely vývoje.</span><span class="sxs-lookup"><span data-stu-id="d8046-155">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="d8046-156">Pomocí [poskytovatele konfigurace Azure Key Vault](xref:security/key-vault-configuration)můžete ukládat a chránit tajné kódy Azure test a produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="d8046-156">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="d8046-157">Postupujte podle kroků v [bezpečném úložišti tajných kódů aplikací ve vývoji v tématu ASP.NET Core](xref:security/app-secrets) tématu uložení tokenů přiřazených každým poskytovatelem přihlašovacích údajů níže.</span><span class="sxs-lookup"><span data-stu-id="d8046-157">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="d8046-158">Nastavení poskytovatelů přihlášení požadovaných vaší aplikací</span><span class="sxs-lookup"><span data-stu-id="d8046-158">Setup login providers required by your application</span></span>

<span data-ttu-id="d8046-159">Pomocí následujících témat můžete nakonfigurovat aplikaci tak, aby používala příslušné poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="d8046-159">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="d8046-160">Pokyny pro [Facebook](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="d8046-160">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="d8046-161">Pokyny pro [Twitter](xref:security/authentication/twitter-logins)</span><span class="sxs-lookup"><span data-stu-id="d8046-161">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="d8046-162">Pokyny pro [Google](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="d8046-162">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="d8046-163">Pokyny [Microsoftu](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="d8046-163">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="d8046-164">[Další](xref:security/authentication/otherlogins) pokyny pro poskytovatele</span><span class="sxs-lookup"><span data-stu-id="d8046-164">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="d8046-165">Volitelně nastavit heslo</span><span class="sxs-lookup"><span data-stu-id="d8046-165">Optionally set password</span></span>

<span data-ttu-id="d8046-166">Když se zaregistrujete u externího poskytovatele přihlašovacích údajů, nemáte v aplikaci zaregistrovaným heslem.</span><span class="sxs-lookup"><span data-stu-id="d8046-166">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="d8046-167">To vám pomůžeme při vytváření a zapamatování hesla pro web, ale také vám to závisí na externím poskytovateli přihlášení.</span><span class="sxs-lookup"><span data-stu-id="d8046-167">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="d8046-168">Pokud externí poskytovatel přihlášení není dostupný, nebudete se moct přihlásit k webu.</span><span class="sxs-lookup"><span data-stu-id="d8046-168">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="d8046-169">Pokud chcete vytvořit heslo a přihlásit se pomocí e-mailu, který jste nastavili během procesu přihlašování pomocí externích zprostředkovatelů:</span><span class="sxs-lookup"><span data-stu-id="d8046-169">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="d8046-170">Vyberte odkaz **e &lt;-mailový alias&gt; Hello** v pravém horním rohu a přejděte do zobrazení **Správa** .</span><span class="sxs-lookup"><span data-stu-id="d8046-170">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Správa zobrazení webové aplikace](index/_static/pass1a.png)

* <span data-ttu-id="d8046-172">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d8046-172">Select **Create**</span></span>

![Nastavení stránky pro heslo](index/_static/pass2a.png)

* <span data-ttu-id="d8046-174">Nastavte platné heslo a můžete ho použít k přihlášení pomocí e-mailu.</span><span class="sxs-lookup"><span data-stu-id="d8046-174">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d8046-175">Další postup</span><span class="sxs-lookup"><span data-stu-id="d8046-175">Next steps</span></span>

* <span data-ttu-id="d8046-176">Tento článek představil externí ověřování a vysvětluje požadavky potřebné k přidání externích přihlášení do vaší aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8046-176">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>

* <span data-ttu-id="d8046-177">Stránky specifické pro poskytovatele odkazů pro konfiguraci přihlášení pro poskytovatele vyžadované vaší aplikací</span><span class="sxs-lookup"><span data-stu-id="d8046-177">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>

* <span data-ttu-id="d8046-178">Můžete chtít zachovat další data o uživateli a jejich přístup a aktualizovat tokeny.</span><span class="sxs-lookup"><span data-stu-id="d8046-178">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="d8046-179">Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="d8046-179">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
