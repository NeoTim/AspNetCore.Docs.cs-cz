---
title: Facebook, Google a externí zprostředkovatel ověřování v ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje vytvoření ASP.NET Core 2.x aplikace pomocí externího zprostředkovatele ověřování OAuth 2.0.
ms.author: riande
ms.custom: mvc
ms.date: 1/19/2019
uid: security/authentication/social/index
ms.openlocfilehash: 48dd8b772234ff18158423a36ed1716102bc2f31
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396139"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="11c35-103">Facebook, Google a externí zprostředkovatel ověřování v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="11c35-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="11c35-104">Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="11c35-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="11c35-105">Tento kurz ukazuje, jak vytvořit aplikaci 2.2 technologie ASP.NET Core, která umožňuje uživatelům přihlášení pomocí přihlašovacích údajů z externího zprostředkovatele ověřování OAuth 2.0.</span><span class="sxs-lookup"><span data-stu-id="11c35-105">This tutorial demonstrates how to build an ASP.NET Core 2.2 app that enables users to log in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="11c35-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), a [Microsoft](xref:security/authentication/microsoft-logins) poskytovatelé jsou popsané v následujících částech.</span><span class="sxs-lookup"><span data-stu-id="11c35-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections.</span></span> <span data-ttu-id="11c35-107">Ostatní zprostředkovatelé jsou k dispozici v balíčky třetích stran, jako [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) a [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span><span class="sxs-lookup"><span data-stu-id="11c35-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

![Ikony sociálních médií pro Facebook, Twitter, Google, plus a Windows](index/_static/social.png)

<span data-ttu-id="11c35-109">Umožňuje uživatelům přihlašovat se pomocí existujících přihlašovacích údajů je pro uživatele pohodlný a posune mnoho složitých úkolů při správě procesu přihlašování do jiného výrobce.</span><span class="sxs-lookup"><span data-stu-id="11c35-109">Enabling users to sign in with their existing credentials is convenient for the users and shifts many of the complexities of managing the sign-in process onto a third party.</span></span> <span data-ttu-id="11c35-110">Příklady, jak sociální přihlášení můžete jednotka provoz a zákazníka převody, naleznete v tématu případové studie podle [Facebook](https://www.facebook.com/unsupportedbrowser) a [Twitter](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="11c35-110">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="11c35-111">Vytvořte nový projekt ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="11c35-111">Create a New ASP.NET Core Project</span></span>

* <span data-ttu-id="11c35-112">V sadě Visual Studio 2017, vytvořte nový projekt z úvodní stránky, nebo prostřednictvím **souboru** > **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="11c35-112">In Visual Studio 2017, create a new project from the Start Page, or via **File** > **New** > **Project**.</span></span>

* <span data-ttu-id="11c35-113">Vyberte **webové aplikace ASP.NET Core** šablony, které jsou k dispozici v **Visual C#**   >  **.NET Core** kategorie:</span><span class="sxs-lookup"><span data-stu-id="11c35-113">Select the **ASP.NET Core Web Application** template available in the **Visual C#** > **.NET Core** category:</span></span>
* <span data-ttu-id="11c35-114">Vyberte **změna ověřování** a ověřování sady **jednotlivé uživatelské účty**.</span><span class="sxs-lookup"><span data-stu-id="11c35-114">Select **Change Authentication** and set authentication to **Individual User Accounts**.</span></span>

## <a name="apply-migrations"></a><span data-ttu-id="11c35-115">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="11c35-115">Apply migrations</span></span>

* <span data-ttu-id="11c35-116">Spusťte aplikaci a vyberte **zaregistrovat** odkaz.</span><span class="sxs-lookup"><span data-stu-id="11c35-116">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="11c35-117">Zadejte e-mail a heslo pro nový účet a potom vyberte **zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="11c35-117">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="11c35-118">Postupujte podle pokynů a použití migrace.</span><span class="sxs-lookup"><span data-stu-id="11c35-118">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="11c35-119">Pomocí SecretManager ukládat tokeny přiřadil zprostředkovatele přihlášení</span><span class="sxs-lookup"><span data-stu-id="11c35-119">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="11c35-120">Přiřadit zprostředkovatele přihlášení prostřednictvím sociální sítě **Id aplikace** a **tajný klíč aplikace** tokeny během procesu registrace.</span><span class="sxs-lookup"><span data-stu-id="11c35-120">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="11c35-121">Přesné token názvy lišit podle zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="11c35-121">The exact token names vary by provider.</span></span> <span data-ttu-id="11c35-122">Tyto tokeny představují přihlašovací údaje, které vaše aplikace používá pro přístup k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="11c35-122">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="11c35-123">Tokeny představují "tajné", které lze propojit s díky konfiguraci aplikací [manažera tajných](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="11c35-123">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="11c35-124">Tajný klíč správce je bezpečnější alternativu pro ukládání tokenů v konfiguračním souboru, například *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="11c35-124">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="11c35-125">Tajný klíč správce je jenom pro účely vývoje.</span><span class="sxs-lookup"><span data-stu-id="11c35-125">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="11c35-126">Můžete ukládat a chránit Azure testovací a produkční tajných kódů pomocí [poskytovatele konfigurace služby Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="11c35-126">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="11c35-127">Postupujte podle kroků v [bezpečné ukládání tajných kódů aplikace při vývoji v ASP.NET Core](xref:security/app-secrets) tématu pro ukládání tokenů přiřadil každý zprostředkovatele přihlášení níže.</span><span class="sxs-lookup"><span data-stu-id="11c35-127">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="11c35-128">Instalační program zprostředkovatele přihlášení požadovaná vaší aplikací</span><span class="sxs-lookup"><span data-stu-id="11c35-128">Setup login providers required by your application</span></span>

<span data-ttu-id="11c35-129">Ke konfiguraci vaší aplikaci použít příslušné poskytovatele použijte následující témata:</span><span class="sxs-lookup"><span data-stu-id="11c35-129">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="11c35-130">[Facebook](xref:security/authentication/facebook-logins) pokyny</span><span class="sxs-lookup"><span data-stu-id="11c35-130">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="11c35-131">[Twitter](xref:security/authentication/twitter-logins) pokyny</span><span class="sxs-lookup"><span data-stu-id="11c35-131">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="11c35-132">[Google](xref:security/authentication/google-logins) pokyny</span><span class="sxs-lookup"><span data-stu-id="11c35-132">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="11c35-133">[Microsoft](xref:security/authentication/microsoft-logins) pokyny</span><span class="sxs-lookup"><span data-stu-id="11c35-133">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="11c35-134">[Jiný poskytovatel](xref:security/authentication/otherlogins) pokyny</span><span class="sxs-lookup"><span data-stu-id="11c35-134">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="11c35-135">Volitelně můžete nastavit heslo</span><span class="sxs-lookup"><span data-stu-id="11c35-135">Optionally set password</span></span>

<span data-ttu-id="11c35-136">Při registraci se externího zprostředkovatele přihlášení, není nutné heslo registrován s aplikací.</span><span class="sxs-lookup"><span data-stu-id="11c35-136">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="11c35-137">To řeší jste od vytvoření a zapamatování hesla pro lokalitu, ale také umožňuje závisí na externího zprostředkovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="11c35-137">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="11c35-138">Pokud není k dispozici na externího zprostředkovatele přihlášení, nebudete moct přihlásit k webu.</span><span class="sxs-lookup"><span data-stu-id="11c35-138">If the external login provider is unavailable, you won't be able to log in to the web site.</span></span>

<span data-ttu-id="11c35-139">Chcete-li vytvořit heslo a přihlaste se pomocí e-mailu, kterou jste nastavili během procesu přihlašování s externí zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="11c35-139">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="11c35-140">Vyberte **Hello &lt;e-mailový alias&gt;**  v pravém horním rohu přejít na odkaz **spravovat** zobrazení.</span><span class="sxs-lookup"><span data-stu-id="11c35-140">Select the **Hello &lt;email alias&gt;** link at the top right corner to navigate to the **Manage** view.</span></span>

![Zobrazení Správa webové aplikace](index/_static/pass1a.png)

* <span data-ttu-id="11c35-142">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="11c35-142">Select **Create**</span></span>

![Nastavte heslo stránku](index/_static/pass2a.png)

* <span data-ttu-id="11c35-144">Nastavili platné heslo a může být využit k přihlášení pomocí e-mailu.</span><span class="sxs-lookup"><span data-stu-id="11c35-144">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="11c35-145">Další kroky</span><span class="sxs-lookup"><span data-stu-id="11c35-145">Next steps</span></span>

* <span data-ttu-id="11c35-146">Tento článek zavedené externího ověřování a vysvětlení požadovaných součástí pro přidání externí přihlášení do aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="11c35-146">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>

* <span data-ttu-id="11c35-147">Referenční stránky specifickým pro zprostředkovatele konfigurace přihlášení pro zprostředkovatele, které vaše aplikace vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="11c35-147">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>

* <span data-ttu-id="11c35-148">Můžete chtít zachovat další data o uživateli a jejich přístup a obnovovací tokeny.</span><span class="sxs-lookup"><span data-stu-id="11c35-148">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="11c35-149">Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="11c35-149">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
