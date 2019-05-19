---
title: Facebook, Google a externí zprostředkovatel ověřování v ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje vytvoření ASP.NET Core 2.x aplikace pomocí externího zprostředkovatele ověřování OAuth 2.0.
ms.author: riande
ms.custom: mvc
ms.date: 05/10/2019
uid: security/authentication/social/index
ms.openlocfilehash: 8dac8a8a2276388414b6bb1211e970617b001637
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874812"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="a1a41-103">Facebook, Google a externí zprostředkovatel ověřování v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1a41-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="a1a41-104">Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a1a41-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a1a41-105">Tento kurz ukazuje, jak vytvořit aplikaci 2.2 technologie ASP.NET Core, který umožňuje uživatelům přihlášení pomocí přihlašovacích údajů z externího zprostředkovatele ověřování OAuth 2.0.</span><span class="sxs-lookup"><span data-stu-id="a1a41-105">This tutorial demonstrates how to build an ASP.NET Core 2.2 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="a1a41-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), a [Microsoft](xref:security/authentication/microsoft-logins) poskytovatelé jsou popsané v následujících částech.</span><span class="sxs-lookup"><span data-stu-id="a1a41-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections.</span></span> <span data-ttu-id="a1a41-107">Ostatní zprostředkovatelé jsou k dispozici v balíčky třetích stran, jako [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) a [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span><span class="sxs-lookup"><span data-stu-id="a1a41-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

![Ikony sociálních médií pro Facebook, Twitter, Google, plus a Windows](index/_static/social.png)

<span data-ttu-id="a1a41-109">Umožňuje uživatelům přihlašovat se pomocí existujících přihlašovacích údajů:</span><span class="sxs-lookup"><span data-stu-id="a1a41-109">Enabling users to sign in with their existing credentials:</span></span>
* <span data-ttu-id="a1a41-110">Je vhodné pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="a1a41-110">Is convenient for the users.</span></span>
* <span data-ttu-id="a1a41-111">Posune mnoho složitých úkolů při správě procesu přihlašování do jiného výrobce.</span><span class="sxs-lookup"><span data-stu-id="a1a41-111">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span> 

<span data-ttu-id="a1a41-112">Příklady, jak sociální přihlášení můžete jednotka provoz a zákazníka převody, naleznete v tématu případové studie podle [Facebook](https://www.facebook.com/unsupportedbrowser) a [Twitter](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="a1a41-112">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="a1a41-113">Vytvořte nový projekt ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1a41-113">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a1a41-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1a41-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a1a41-115">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="a1a41-115">Create a new project.</span></span>
* <span data-ttu-id="a1a41-116">Vyberte **webová aplikace ASP.NET Core** a **Další**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-116">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="a1a41-117">Zadejte **název projektu** a potvrďte nebo změňte **umístění**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-117">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="a1a41-118">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-118">Select **Create**.</span></span>
* <span data-ttu-id="a1a41-119">Vyberte **2.2 technologie ASP.NET Core** v rozevíracím seznamu.</span><span class="sxs-lookup"><span data-stu-id="a1a41-119">Select **ASP.NET Core 2.2** in the drop down.</span></span> <span data-ttu-id="a1a41-120">Vyberte **webovou aplikaci** v seznamu šablon.</span><span class="sxs-lookup"><span data-stu-id="a1a41-120">Select **Web Application** in the template list.</span></span>
* <span data-ttu-id="a1a41-121">V části **ověřování**vyberte **změnu** a nastavte ověřování na **jednotlivé uživatelské účty**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-121">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="a1a41-122">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-122">Select **OK**.</span></span>
* <span data-ttu-id="a1a41-123">V **vytvořit novou webovou aplikaci ASP.NET Core** okně **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-123">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a1a41-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a1a41-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a1a41-125">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a1a41-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="a1a41-126">Změňte adresář (`cd`) do složky, která bude obsahovat projektu.</span><span class="sxs-lookup"><span data-stu-id="a1a41-126">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="a1a41-127">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="a1a41-127">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o WebApp1 -au Individual -uld
  code -r WebApp1
  ```

  * <span data-ttu-id="a1a41-128">`dotnet new` Příkaz vytvoří nový projekt v Razor Pages *WebApp1* složky.</span><span class="sxs-lookup"><span data-stu-id="a1a41-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="a1a41-129">`-uld` používá LocalDB namísto SQLite.</span><span class="sxs-lookup"><span data-stu-id="a1a41-129">`-uld` uses LocalDB instead of SQLite.</span></span> <span data-ttu-id="a1a41-130">Vynechat `-uld` použít SQLite.</span><span class="sxs-lookup"><span data-stu-id="a1a41-130">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="a1a41-131">`-au Individual` vytvoří kód pro jednotlivé ověřování.</span><span class="sxs-lookup"><span data-stu-id="a1a41-131">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="a1a41-132">`code` Příkaz otevře *WebApp1* složky v nové instanci sady Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a1a41-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

* <span data-ttu-id="a1a41-133">Zobrazí se dialogové okno s **'WebApp1' chybí požadované prostředky pro sestavení a ladění. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="a1a41-133">A dialog box appears with **Required assets to build and debug are missing from 'WebApp1'. Add them?**</span></span> <span data-ttu-id="a1a41-134">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-134">Select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a1a41-135">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a1a41-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a1a41-136">Vyberte **souboru** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-136">Select **File** > **New Solution**.</span></span>
* <span data-ttu-id="a1a41-137">Vyberte **.NET Core** > **aplikace** na bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="a1a41-137">Select **.NET Core** > **App** in the sidebar.</span></span> <span data-ttu-id="a1a41-138">Vyberte **webovou aplikaci** šablony.</span><span class="sxs-lookup"><span data-stu-id="a1a41-138">Select the **Web Application** template.</span></span> <span data-ttu-id="a1a41-139">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-139">Select **Next**.</span></span>
* <span data-ttu-id="a1a41-140">Nastavte **Cílová architektura** přetažením dolů **.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-140">Set the **Target Framework** drop down to **.NET Core 2.2**.</span></span> <span data-ttu-id="a1a41-141">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-141">Select **Next**.</span></span>
* <span data-ttu-id="a1a41-142">Zadejte **název projektu**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-142">Provide a **Project Name**.</span></span> <span data-ttu-id="a1a41-143">Potvrďte nebo změňte **umístění**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-143">Confirm or change the **Location**.</span></span> <span data-ttu-id="a1a41-144">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-144">Select **Create**.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="a1a41-145">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="a1a41-145">Apply migrations</span></span>

* <span data-ttu-id="a1a41-146">Spusťte aplikaci a vyberte **zaregistrovat** odkaz.</span><span class="sxs-lookup"><span data-stu-id="a1a41-146">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="a1a41-147">Zadejte e-mail a heslo pro nový účet a potom vyberte **zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-147">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="a1a41-148">Postupujte podle pokynů a použití migrace.</span><span class="sxs-lookup"><span data-stu-id="a1a41-148">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="a1a41-149">Pomocí SecretManager ukládat tokeny přiřadil zprostředkovatele přihlášení</span><span class="sxs-lookup"><span data-stu-id="a1a41-149">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="a1a41-150">Přiřadit zprostředkovatele přihlášení prostřednictvím sociální sítě **Id aplikace** a **tajný klíč aplikace** tokeny během procesu registrace.</span><span class="sxs-lookup"><span data-stu-id="a1a41-150">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="a1a41-151">Přesné token názvy lišit podle zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="a1a41-151">The exact token names vary by provider.</span></span> <span data-ttu-id="a1a41-152">Tyto tokeny představují přihlašovací údaje, které vaše aplikace používá pro přístup k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a1a41-152">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="a1a41-153">Tokeny představují "tajné", které lze propojit s díky konfiguraci aplikací [manažera tajných](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="a1a41-153">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="a1a41-154">Tajný klíč správce je bezpečnější alternativu pro ukládání tokenů v konfiguračním souboru, například *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a1a41-154">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a1a41-155">Tajný klíč správce je jenom pro účely vývoje.</span><span class="sxs-lookup"><span data-stu-id="a1a41-155">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="a1a41-156">Můžete ukládat a chránit Azure testovací a produkční tajných kódů pomocí [poskytovatele konfigurace služby Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="a1a41-156">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="a1a41-157">Postupujte podle kroků v [bezpečné ukládání tajných kódů aplikace při vývoji v ASP.NET Core](xref:security/app-secrets) tématu pro ukládání tokenů přiřadil každý zprostředkovatele přihlášení níže.</span><span class="sxs-lookup"><span data-stu-id="a1a41-157">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="a1a41-158">Instalační program zprostředkovatele přihlášení požadovaná vaší aplikací</span><span class="sxs-lookup"><span data-stu-id="a1a41-158">Setup login providers required by your application</span></span>

<span data-ttu-id="a1a41-159">Ke konfiguraci vaší aplikaci použít příslušné poskytovatele použijte následující témata:</span><span class="sxs-lookup"><span data-stu-id="a1a41-159">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="a1a41-160">[Facebook](xref:security/authentication/facebook-logins) pokyny</span><span class="sxs-lookup"><span data-stu-id="a1a41-160">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="a1a41-161">[Twitter](xref:security/authentication/twitter-logins) pokyny</span><span class="sxs-lookup"><span data-stu-id="a1a41-161">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="a1a41-162">[Google](xref:security/authentication/google-logins) pokyny</span><span class="sxs-lookup"><span data-stu-id="a1a41-162">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="a1a41-163">[Microsoft](xref:security/authentication/microsoft-logins) pokyny</span><span class="sxs-lookup"><span data-stu-id="a1a41-163">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="a1a41-164">[Jiný poskytovatel](xref:security/authentication/otherlogins) pokyny</span><span class="sxs-lookup"><span data-stu-id="a1a41-164">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="a1a41-165">Volitelně můžete nastavit heslo</span><span class="sxs-lookup"><span data-stu-id="a1a41-165">Optionally set password</span></span>

<span data-ttu-id="a1a41-166">Při registraci se externího zprostředkovatele přihlášení, není nutné heslo registrován s aplikací.</span><span class="sxs-lookup"><span data-stu-id="a1a41-166">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="a1a41-167">To řeší jste od vytvoření a zapamatování hesla pro lokalitu, ale také umožňuje závisí na externího zprostředkovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="a1a41-167">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="a1a41-168">Pokud není k dispozici na externího zprostředkovatele přihlášení, nebudete moct přihlásit k webu.</span><span class="sxs-lookup"><span data-stu-id="a1a41-168">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="a1a41-169">Chcete-li vytvořit heslo a přihlaste se pomocí e-mailu, kterou jste nastavili během procesu přihlašování s externí zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="a1a41-169">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="a1a41-170">Vyberte **Hello &lt;e-mailový alias&gt;**  v pravém horním rohu, přejděte na odkaz **spravovat** zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a1a41-170">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Zobrazení Správa webové aplikace](index/_static/pass1a.png)

* <span data-ttu-id="a1a41-172">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="a1a41-172">Select **Create**</span></span>

![Nastavte heslo stránku](index/_static/pass2a.png)

* <span data-ttu-id="a1a41-174">Nastavili platné heslo a může být využit k přihlášení pomocí e-mailu.</span><span class="sxs-lookup"><span data-stu-id="a1a41-174">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a1a41-175">Další kroky</span><span class="sxs-lookup"><span data-stu-id="a1a41-175">Next steps</span></span>

* <span data-ttu-id="a1a41-176">Tento článek zavedené externího ověřování a vysvětlení požadovaných součástí pro přidání externí přihlášení do aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1a41-176">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>

* <span data-ttu-id="a1a41-177">Referenční stránky specifickým pro zprostředkovatele konfigurace přihlášení pro zprostředkovatele, které vaše aplikace vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="a1a41-177">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>

* <span data-ttu-id="a1a41-178">Můžete chtít zachovat další data o uživateli a jejich přístup a obnovovací tokeny.</span><span class="sxs-lookup"><span data-stu-id="a1a41-178">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="a1a41-179">Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="a1a41-179">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
