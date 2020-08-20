---
title: Naučte se migrovat z ASP.NET MVC na ASP.NET Core MVC
author: wadepickett
description: Naučte se, jak začít migrovat projekt ASP.NET MVC do ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: cd1a7ff57d911f96f0adfe4b548fa80ec844886d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632236"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="bdd4d-103">Migrace z ASP.NET MVC na ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="bdd4d-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bdd4d-104">Tento článek ukazuje, jak zahájit migraci projektu ASP.NET MVC na [ASP.NET Core MVC](xref:mvc/overview).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="bdd4d-105">V tomto procesu zvýrazní související změny z ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="bdd4d-106">Migrace z ASP.NET MVC je proces s více kroky.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="bdd4d-107">Tento článek popisuje:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-107">This article covers:</span></span>

* <span data-ttu-id="bdd4d-108">Počáteční nastavení.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-108">Initial setup.</span></span>
* <span data-ttu-id="bdd4d-109">Základní řadiče a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-109">Basic controllers and views.</span></span>
* <span data-ttu-id="bdd4d-110">Statický obsah.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-110">Static content.</span></span>
* <span data-ttu-id="bdd4d-111">Závislosti na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-111">Client-side dependencies.</span></span>

<span data-ttu-id="bdd4d-112">Informace o migraci konfigurace a Identity kódu najdete v tématu [migrace konfigurace do ASP.NET Core](xref:migration/configuration) a [migrace ověřování a Identity ASP.NET Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-112">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bdd4d-113">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="bdd4d-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="bdd4d-114">Vytvoření projektu počáteční ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="bdd4d-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="bdd4d-115">V aplikaci Visual Studio vytvořte ukázkový projekt ASP.NET MVC k migraci:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="bdd4d-116">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-116">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="bdd4d-117">Vyberte **ASP.NET webová aplikace (.NET Framework)** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next**.</span></span>
1. <span data-ttu-id="bdd4d-118">Pojmenujte projekt *WebApp1* , aby obor názvů odpovídal ASP.NET Coremu projektu vytvořenému v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="bdd4d-119">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-119">Select **Create**.</span></span>
1. <span data-ttu-id="bdd4d-120">Vyberte **MVC**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-120">Select **MVC**, and then select **Create**.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="bdd4d-121">Vytvoření projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bdd4d-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="bdd4d-122">Vytvořte nové řešení s novým projektem ASP.NET Core pro migraci na:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="bdd4d-123">Spusťte druhou instanci sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="bdd4d-124">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-124">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="bdd4d-125">Vyberte **Webová aplikace webové jádro ASP.NET** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-125">Select **ASP.NET Web Core Web Application** and then select **Next**.</span></span>
1. <span data-ttu-id="bdd4d-126">V dialogovém okně **Konfigurovat nový projekt** pojmenujte projekt *WebApp1*.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-126">In the **Configure your new project** dialog, Name the project *WebApp1*.</span></span>
1. <span data-ttu-id="bdd4d-127">Nastavte umístění na jiný adresář než předchozí projekt pro použití stejného názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="bdd4d-128">Použití stejného oboru názvů usnadňuje kopírování kódu mezi dvěma projekty.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="bdd4d-129">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-129">Select **Create**.</span></span>
1. <span data-ttu-id="bdd4d-130">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="bdd4d-131">Vyberte šablonu projektu **Webová aplikace (model-zobrazení-kontroler)** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create**.</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="bdd4d-132">Konfigurace lokality ASP.NET Core pro použití MVC</span><span class="sxs-lookup"><span data-stu-id="bdd4d-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="bdd4d-133">V ASP.NET Core 3,0 a novějších projektech .NET Framework již není podporovanou cílovou architekturou.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="bdd4d-134">Projekt musí být cílen na .NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-134">Your project must target .NET Core.</span></span> <span data-ttu-id="bdd4d-135">ASP.NET Core sdílené rozhraní, které zahrnuje MVC, je součástí instalace modulu runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="bdd4d-136">Sdílené rozhraní je automaticky odkazováno při použití `Microsoft.NET.Sdk.Web` sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="bdd4d-137">Další informace naleznete v tématu [Reference k rozhraní](xref:migration/22-to-30#framework-reference).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="bdd4d-138">V ASP.NET Core `Startup` Třída:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="bdd4d-139">Nahradí *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="bdd4d-140">Zpracuje všechny úlohy při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="bdd4d-141">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="bdd4d-142">V projektu ASP.NET Core otevřete soubor *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="bdd4d-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="bdd4d-143">Aplikace ASP.NET Core musí souhlasit s funkcemi architektury se middlewarem.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="bdd4d-144">Předchozí kód generovaný šablonou přidá tyto služby a middleware:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="bdd4d-145"><xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>Metoda rozšíření registruje podporu služby MVC pro řadiče, funkce a zobrazení související s rozhraním API.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="bdd4d-146">Další informace o možnostech registrace služby MVC najdete v tématu [Registrace služby MVC](xref:migration/22-to-30#mvc-service-registration) .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="bdd4d-147"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>Metoda rozšíření přidá obslužnou rutinu statického souboru `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="bdd4d-148">`UseStaticFiles`Metoda rozšíření musí být volána před `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="bdd4d-149">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="bdd4d-150"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>Metoda rozšíření přidává směrování.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="bdd4d-151">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="bdd4d-152">Tato existující konfigurace zahrnuje to, co je potřeba k migraci ukázkového projektu ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="bdd4d-153">Další informace o ASP.NET Core možností middlewaru naleznete v tématu <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="bdd4d-154">Migrace řadičů a zobrazení</span><span class="sxs-lookup"><span data-stu-id="bdd4d-154">Migrate controllers and views</span></span>

<span data-ttu-id="bdd4d-155">V projektu ASP.NET Core by byla přidána nová prázdná třída kontroleru a třídy zobrazení, která bude sloužit jako zástupné symboly pomocí stejných názvů jako kontrolér a třídy zobrazení v jakémkoli projektu ASP.NET MVC pro migraci z.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="bdd4d-156">Projekt ASP.NET Core *WebApp1* již obsahuje minimální ukázkový kontroler a zobrazení se stejným názvem jako projekt ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="bdd4d-157">Ty budou sloužit jako zástupné symboly pro kontroler MVC ASP.NET a zobrazení, která se mají migrovat z projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="bdd4d-158">Zkopírujte metody z ASP.NET MVC, `HomeController` abyste nahradili nové metody ASP.NET Core `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="bdd4d-159">Není nutné měnit návratový typ metod akce.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="bdd4d-160">Zabudovaná metoda akčního typu metody kontroleru ASP.NET MVC je [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); ve ASP.NET Core MVC se místo toho vrací metody akcí `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-160">The ASP.NET MVC built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="bdd4d-161">`ActionResult` implementuje `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="bdd4d-162">V projektu ASP.NET Core klikněte pravým tlačítkem myši na *zobrazení/domovský* adresář, vyberte možnost **Přidat** > **existující položku**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="bdd4d-163">V dialogovém okně **Přidat existující položku** přejděte do *zobrazení/domovského* adresáře projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="bdd4d-164">Vyberte soubory *About. cshtml*, *Contact. cshtml*a *index. cshtml* Razor zobrazení a pak vyberte **Přidat**a nahraďte stávající soubory.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-164">Select the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files, then select **Add**, replacing the existing files.</span></span>

<span data-ttu-id="bdd4d-165">Další informace naleznete v tématech <xref:mvc/controllers/actions> a <xref:mvc/views/overview>.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="bdd4d-166">Testování jednotlivých metod</span><span class="sxs-lookup"><span data-stu-id="bdd4d-166">Test each method</span></span>

<span data-ttu-id="bdd4d-167">Každý koncový bod řadiče lze otestovat, ale rozložení a styly jsou popsány dále v dokumentu.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="bdd4d-168">Spusťte aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="bdd4d-169">Vyvolání vykreslených zobrazení z prohlížeče na běžící ASP.NET Core aplikaci nahraďte aktuální číslo portu číslem portu použitým v projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="bdd4d-170">Například, `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="bdd4d-171">Migrace statického obsahu</span><span class="sxs-lookup"><span data-stu-id="bdd4d-171">Migrate static content</span></span>

<span data-ttu-id="bdd4d-172">V ASP.NET MVC 5 a starších byl statický obsah hostovaný z kořenového adresáře webového projektu a byl vzájemně promíchán se soubory na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="bdd4d-173">V ASP.NET Core jsou statické soubory uloženy v rámci [webového kořenového](xref:fundamentals/index#web-root) adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="bdd4d-174">Výchozí adresář je *{root obsahu}/wwwroot*, ale může se změnit.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-174">The default directory is *{content root}/wwwroot*, but it can be changed.</span></span> <span data-ttu-id="bdd4d-175">Další informace najdete v tématu [statické soubory v ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="bdd4d-176">Zkopírujte statický obsah z projektu ASP.NET MVC *WebApp1* do adresáře *wwwroot* v projektu ASP.NET Core *WebApp1* :</span><span class="sxs-lookup"><span data-stu-id="bdd4d-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="bdd4d-177">V projektu ASP.NET Core klikněte pravým tlačítkem myši na adresář *wwwroot* , vyberte možnost **Přidat** > **existující položku**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="bdd4d-178">V dialogovém okně **Přidat existující položku** přejděte do projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="bdd4d-179">Vyberte soubor *favicon. ico* a pak vyberte **Přidat**a nahraďte existující soubor.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-179">Select the *favicon.ico* file, then select **Add**, replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="bdd4d-180">Migrace souborů rozložení</span><span class="sxs-lookup"><span data-stu-id="bdd4d-180">Migrate the layout files</span></span>

<span data-ttu-id="bdd4d-181">Zkopírujte soubory rozložení projektu ASP.NET MVC do projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="bdd4d-182">V projektu ASP.NET Core klikněte pravým tlačítkem myši na adresář *zobrazení* a vyberte možnost **Přidat** > **existující položku**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="bdd4d-183">V dialogovém okně **Přidat existující položku** přejděte do adresáře *zobrazení* projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="bdd4d-184">Vyberte soubor *_ViewStart. cshtml* a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-184">Select the *_ViewStart.cshtml* file then select **Add**.</span></span>

<span data-ttu-id="bdd4d-185">Zkopírujte soubory sdíleného rozložení projektu ASP.NET MVC do projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="bdd4d-186">V projektu ASP.NET Core klikněte pravým tlačítkem na *zobrazení/sdílený* adresář a vyberte **Přidat** > **existující položku**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="bdd4d-187">V dialogovém okně **Přidat existující položku** přejděte do *zobrazení/sdíleného* adresáře projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="bdd4d-188">Vyberte soubor *_Layout. cshtml* a pak vyberte **Přidat**a nahraďte existující soubor.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-188">Select the *_Layout.cshtml* file, then select **Add**, replacing the existing file.</span></span>

<span data-ttu-id="bdd4d-189">V projektu ASP.NET Core otevřete *_Layout soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="bdd4d-190">Proveďte následující změny, aby odpovídaly dokončenému kódu zobrazenému níže:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="bdd4d-191">Aktualizujte zahrnutí šablon stylů CSS tak, aby odpovídala následujícímu dokončenému kódu:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="bdd4d-192">Nahraďte `@Styles.Render("~/Content/css")` `<link>` elementem pro načtení *bootstrap. CSS* (viz níže).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="bdd4d-193">Odebrat `@Scripts.Render("~/bundles/modernizr")` .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="bdd4d-194">Dokončené nahrazující značky pro spuštění šablony stylů spuštění:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="bdd4d-195">Aktualizujte zahrnutí JavaScriptu a Bootstrap pro JavaScript tak, aby odpovídalo následujícímu dokončenému kódu:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="bdd4d-196">Nahraďte `@Scripts.Render("~/bundles/jquery")` `<script>` elementem (viz níže).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="bdd4d-197">Nahraďte `@Scripts.Render("~/bundles/bootstrap")` `<script>` elementem (viz níže).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="bdd4d-198">Hotové nahrazující označení pro zahrnutí a zavedení JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="bdd4d-199">Aktualizovaný soubor *_Layout. cshtml* je uveden níže:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="bdd4d-200">Zobrazí web v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-200">View the site in the browser.</span></span> <span data-ttu-id="bdd4d-201">Měl by se vykreslovat s očekávanými styly.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="bdd4d-202">Konfigurace sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="bdd4d-202">Configure bundling and minification</span></span>

<span data-ttu-id="bdd4d-203">ASP.NET Core je kompatibilní s několika Open Source řešeními a minifikace řešeními, jako je [Weboptimalizátor](https://github.com/ligershark/WebOptimizer) a další podobné knihovny.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="bdd4d-204">ASP.NET Core neposkytuje nativní řešení pro sdružování a minifikace.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="bdd4d-205">Informace o konfiguraci sdružování a minifikace najdete v článku [sdružování a minifikace](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="bdd4d-206">Řešení chyb HTTP 500</span><span class="sxs-lookup"><span data-stu-id="bdd4d-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="bdd4d-207">Existuje mnoho problémů, které mohou způsobit chybovou zprávu HTTP 500, která neobsahuje žádné informace o zdroji problému.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="bdd4d-208">Například pokud soubor *views/_ViewImports. cshtml* obsahuje obor názvů, který v projektu neexistuje, je vygenerována chyba HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="bdd4d-209">Ve výchozím nastavení se v ASP.NET Core aplikace `UseDeveloperExceptionPage` rozšíření přidá do `IApplicationBuilder` a spustí se při *vývoji*prostředí.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development*.</span></span> <span data-ttu-id="bdd4d-210">Toto je podrobně popsáno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="bdd4d-211">ASP.NET Core převede neošetřené výjimky na chybové odpovědi HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="bdd4d-212">V těchto odpovědích obvykle nejsou podrobnosti o chybě zahrnuty, aby nedocházelo k odhalení potenciálně citlivých informací o serveru.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="bdd4d-213">Další informace najdete na [stránce s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="bdd4d-214">Další kroky</span><span class="sxs-lookup"><span data-stu-id="bdd4d-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="bdd4d-215">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="bdd4d-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="bdd4d-216">Tento článek ukazuje, jak zahájit migraci projektu ASP.NET MVC na [ASP.NET Core mvc](xref:mvc/overview) 2,2.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="bdd4d-217">V tomto procesu se zvýrazní spousta věcí, které se změnily z ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="bdd4d-218">Migrace z ASP.NET MVC je proces s více kroky.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="bdd4d-219">Tento článek popisuje:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-219">This article covers:</span></span>

* <span data-ttu-id="bdd4d-220">Počáteční nastavení</span><span class="sxs-lookup"><span data-stu-id="bdd4d-220">Initial setup</span></span>
* <span data-ttu-id="bdd4d-221">Základní řadiče a zobrazení</span><span class="sxs-lookup"><span data-stu-id="bdd4d-221">Basic controllers and views</span></span>
* <span data-ttu-id="bdd4d-222">Statický obsah</span><span class="sxs-lookup"><span data-stu-id="bdd4d-222">Static content</span></span>
* <span data-ttu-id="bdd4d-223">Závislosti na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-223">Client-side dependencies.</span></span>

<span data-ttu-id="bdd4d-224">Informace o migraci konfigurace a Identity kódu naleznete v tématu <xref:migration/configuration> a <xref:migration/identity> .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-224">For migrating configuration and Identity code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="bdd4d-225">Čísla verzí v ukázkách nemusí být aktuální, aktualizujte projekty odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="bdd4d-226">Vytvoření projektu počáteční ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="bdd4d-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="bdd4d-227">K předvedení upgradu začneme vytvořením aplikace ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="bdd4d-228">Vytvořte ho s názvem *WebApp1* , aby obor názvů odpovídal ASP.NET Coremu projektu vytvořenému v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Dialogové okno Nový projekt sady Visual Studio](mvc/_static/new-project.png)

![Dialogové okno Nová webová aplikace: šablona projektu MVC vybraná v panelu šablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="bdd4d-231">*Volitelné:* Změňte název řešení z *WebApp1* na *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="bdd4d-232">Visual Studio zobrazí nový název řešení (*Mvc5*), který usnadňuje podávání tohoto projektu z dalšího projektu.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-232">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="bdd4d-233">Vytvoření projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bdd4d-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="bdd4d-234">Vytvořte novou *prázdnou* ASP.NET Core webovou aplikaci se stejným názvem jako předchozí projekt (*WebApp1*), aby se obory názvů ve dvou projektech shodovaly.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="bdd4d-235">Stejný obor názvů usnadňuje kopírování kódu mezi dvěma projekty.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="bdd4d-236">Tento projekt vytvořte v jiném adresáři než předchozí projekt, abyste mohli použít stejný název.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![Dialogové okno Nový projekt](mvc/_static/new_core.png)

![Dialog nové webové aplikace v ASP.NET: prázdná šablona projektu vybraná na panelu šablon ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="bdd4d-239">*Volitelné:* Vytvořte novou aplikaci ASP.NET Core pomocí šablony projektu *webové aplikace* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="bdd4d-240">Pojmenujte projekt *WebApp1*a vyberte možnost ověřování **jednotlivých uživatelských účtů**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-240">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="bdd4d-241">Přejmenujte tuto aplikaci na *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-241">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="bdd4d-242">Při vytváření tohoto projektu se šetří čas v převodu.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="bdd4d-243">Konečný výsledek lze zobrazit v kódu generovaném šablonou, kód může být zkopírován do projektu pro převod nebo v porovnání s projektem generovaným šablonou.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="bdd4d-244">Konfigurace lokality pro použití MVC</span><span class="sxs-lookup"><span data-stu-id="bdd4d-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="bdd4d-245">Při cílení na .NET Core se ve výchozím nastavení odkazuje na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="bdd4d-246">Tento balíček obsahuje balíčky běžně používané aplikacemi MVC.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="bdd4d-247">Pokud cílíte .NET Framework, musí být odkazy na balíček uvedeny jednotlivě v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="bdd4d-248">`Microsoft.AspNetCore.Mvc` je ASP.NET Core architekturu MVC.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="bdd4d-249">`Microsoft.AspNetCore.StaticFiles` je obslužná rutina statického souboru.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="bdd4d-250">Aplikace ASP.NET Core výslovně přihlašovat pro middleware, například pro obsluhu statických souborů.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="bdd4d-251">Další informace najdete v tématu [statické soubory](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="bdd4d-252">Otevřete soubor *Startup.cs* a změňte kód tak, aby odpovídal následujícímu:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="bdd4d-253"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Metoda rozšíření přidá obslužnou rutinu statického souboru.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="bdd4d-254">Další informace najdete v tématu [spuštění](xref:fundamentals/startup) a [Směrování](xref:fundamentals/routing)aplikace.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="bdd4d-255">Přidat kontroler a zobrazení</span><span class="sxs-lookup"><span data-stu-id="bdd4d-255">Add a controller and view</span></span>

<span data-ttu-id="bdd4d-256">V této části se přidávají minimální kontroler a zobrazení, které slouží jako zástupné symboly pro kontroler MVC ASP.NET a zobrazení migrované v další části.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="bdd4d-257">Přidejte adresář *řadičů* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="bdd4d-258">Přidejte do adresáře *Controllers* **třídu controller** s názvem *HomeController.cs* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Dialogové okno Přidat novou položku](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="bdd4d-260">Přidejte adresář *zobrazení* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="bdd4d-261">Přidání *zobrazení/domovského* adresáře</span><span class="sxs-lookup"><span data-stu-id="bdd4d-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="bdd4d-262">Přidejte \*\* Razor zobrazení\*\* s názvem *index. cshtml* do *zobrazení/domovského* adresáře.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-262">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Dialogové okno Přidat novou položku](mvc/_static/view.png)

<span data-ttu-id="bdd4d-264">Struktura projektu je zobrazena níže:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-264">The project structure is shown below:</span></span>

![Průzkumník řešení zobrazení souborů a adresářů WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="bdd4d-266">Obsah souboru *views/Home/index. cshtml* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="bdd4d-267">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-267">Run the app.</span></span>

![Webová aplikace otevřená v Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="bdd4d-269">Další informace najdete v tématu [řadiče](xref:mvc/controllers/actions) a [zobrazení](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="bdd4d-270">Následující funkce vyžadují migraci z ukázkového projektu ASP.NET MVC do projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="bdd4d-271">obsah na straně klienta (šablony stylů CSS, písma a skripty)</span><span class="sxs-lookup"><span data-stu-id="bdd4d-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="bdd4d-272">kontrolery</span><span class="sxs-lookup"><span data-stu-id="bdd4d-272">controllers</span></span>

* <span data-ttu-id="bdd4d-273">zobrazení</span><span class="sxs-lookup"><span data-stu-id="bdd4d-273">views</span></span>

* <span data-ttu-id="bdd4d-274">modely</span><span class="sxs-lookup"><span data-stu-id="bdd4d-274">models</span></span>

* <span data-ttu-id="bdd4d-275">sdružování</span><span class="sxs-lookup"><span data-stu-id="bdd4d-275">bundling</span></span>

* <span data-ttu-id="bdd4d-276">filtry</span><span class="sxs-lookup"><span data-stu-id="bdd4d-276">filters</span></span>

* <span data-ttu-id="bdd4d-277">Přihlaste se nebo se přihlaste Identity (to se provádí v dalším kurzu).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-277">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="bdd4d-278">Řadiče a zobrazení</span><span class="sxs-lookup"><span data-stu-id="bdd4d-278">Controllers and views</span></span>

* <span data-ttu-id="bdd4d-279">Zkopírujte každou z metod z ASP.NET MVC `HomeController` do nové `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="bdd4d-280">V ASP.NET MVC je jako návratový typ metody typu "předdefinovaná šablona" [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); ve ASP.NET Core MVC se místo toho vrací metody akcí `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-280">In ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="bdd4d-281">`ActionResult` implementuje `IActionResult` , takže není nutné měnit návratový typ metod akce.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="bdd4d-282">Zkopírujte soubory *About. cshtml*, *Contact. cshtml*a *index. cshtml* Razor zobrazení z projektu ASP.NET MVC do projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-282">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="bdd4d-283">Testování jednotlivých metod</span><span class="sxs-lookup"><span data-stu-id="bdd4d-283">Test each method</span></span>

<span data-ttu-id="bdd4d-284">Soubor rozložení a styly nebyly dosud migrovány, takže vykreslená zobrazení obsahují pouze obsah v zobrazení souborů.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="bdd4d-285">Soubory rozložení vygenerované odkazy pro `About` zobrazení a `Contact` nebudou zatím k dispozici.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="bdd4d-286">Vyvolání vygenerovaných zobrazení z prohlížeče na běžící aplikaci ASP.NET Core nahraďte aktuální číslo portu číslem portu použitým v projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="bdd4d-287">Například: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-287">For example: `https://localhost:44375/home/about`.</span></span>

![Stránka kontaktu](mvc/_static/contact-page.png)

<span data-ttu-id="bdd4d-289">Všimněte si nedostatku stylů a položek nabídky.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="bdd4d-290">Styl bude opraven v následující části.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="bdd4d-291">Statický obsah</span><span class="sxs-lookup"><span data-stu-id="bdd4d-291">Static content</span></span>

<span data-ttu-id="bdd4d-292">V ASP.NET MVC 5 a starších byl statický obsah hostovaný z kořenového adresáře webového projektu a byl vzájemně promíchán se soubory na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="bdd4d-293">V ASP.NET Core je statický obsah hostovaný v adresáři *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="bdd4d-294">Zkopírujte statický obsah z aplikace ASP.NET MVC do adresáře *wwwroot* v projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="bdd4d-295">V tomto ukázkovém převodu:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-295">In this sample conversion:</span></span>

* <span data-ttu-id="bdd4d-296">Zkopírujte soubor *favicon. ico* z projektu ASP.NET MVC do adresáře *wwwroot* v projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="bdd4d-297">Projekt ASP.NET MVC používá pro svůj styl [bootstrap](https://getbootstrap.com/) a ukládá spouštěcí soubory do adresářů *obsahu* a *skriptů* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="bdd4d-298">Šablona, která vygenerovala projekt ASP.NET MVC, odkazuje na Bootstrap v souboru rozložení (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="bdd4d-299">Soubory *bootstrap.js* a *bootstrap. CSS* mohou být zkopírovány z projektu ASP.NET MVC do adresáře *wwwroot* v novém projektu.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="bdd4d-300">Místo toho tento dokument přidá podporu pro Bootstrap (a další knihovny na straně klienta) pomocí sítě CDN v následující části.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="bdd4d-301">Migrovat soubor rozložení</span><span class="sxs-lookup"><span data-stu-id="bdd4d-301">Migrate the layout file</span></span>

* <span data-ttu-id="bdd4d-302">Zkopírujte soubor *_ViewStart. cshtml* z adresáře *zobrazení* projektu ASP.NET MVC do adresáře *zobrazení* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="bdd4d-303">Soubor *_ViewStart. cshtml* se v ASP.NET Core MVC nezměnil.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="bdd4d-304">Vytvořte *zobrazení/sdílený* adresář.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="bdd4d-305">*Volitelné:* Zkopírujte *_ViewImports. cshtml* z adresáře *zobrazení* projektu *FullAspNetCore* MVC do adresáře *zobrazení* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="bdd4d-306">Odeberte všechny deklarace oboru názvů v souboru *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="bdd4d-307">Soubor *_ViewImports. cshtml* poskytuje obory názvů pro všechny soubory zobrazení a přináší [pomocníkům značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="bdd4d-308">V novém souboru rozložení se používají pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="bdd4d-309">Soubor *_ViewImports. cshtml* je pro ASP.NET Core nový.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="bdd4d-310">Zkopírujte soubor *_Layout. cshtml* z *zobrazení/sdíleného* adresáře projektu ASP.NET MVC do *zobrazení/* sdíleného adresáře ASP.NET Core projektu.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="bdd4d-311">Otevřete soubor *_Layout. cshtml* a proveďte následující změny (dokončený kód je zobrazen níže):</span><span class="sxs-lookup"><span data-stu-id="bdd4d-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="bdd4d-312">Nahraďte `@Styles.Render("~/Content/css")` `<link>` elementem pro načtení *bootstrap. CSS* (viz níže).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="bdd4d-313">Odebrat `@Scripts.Render("~/bundles/modernizr")` .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="bdd4d-314">Odkomentujte `@Html.Partial("_LoginPartial")` řádek kolem řádku (Obklopte řádek s `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="bdd4d-315">Další informace najdete v tématu [migrace ověřování a Identity ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="bdd4d-315">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="bdd4d-316">Nahraďte `@Scripts.Render("~/bundles/jquery")` `<script>` elementem (viz níže).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="bdd4d-317">Nahraďte `@Scripts.Render("~/bundles/bootstrap")` `<script>` elementem (viz níže).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="bdd4d-318">Náhradní značky pro spuštění šablony stylů Bootstrap:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="bdd4d-319">Náhradní označení pro zahrnutí a zavedení JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="bdd4d-320">Aktualizovaný soubor *_Layout. cshtml* je uveden níže:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="bdd4d-321">Zobrazí web v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-321">View the site in the browser.</span></span> <span data-ttu-id="bdd4d-322">Měl by se teď správně načíst s očekávanými styly.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="bdd4d-323">*Volitelné:* Zkuste použít nový soubor rozložení.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="bdd4d-324">Zkopírujte soubor rozložení z projektu *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="bdd4d-325">Nový soubor rozložení používá [pomocníky značek](xref:mvc/views/tag-helpers/intro) a má další vylepšení.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="bdd4d-326">Konfigurace sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="bdd4d-326">Configure bundling and minification</span></span>

<span data-ttu-id="bdd4d-327">Informace o tom, jak nakonfigurovat sdružování a minifikace, najdete v článku [sdružování a minifikace](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="bdd4d-328">Řešení chyb HTTP 500</span><span class="sxs-lookup"><span data-stu-id="bdd4d-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="bdd4d-329">Existuje mnoho problémů, které mohou způsobit chybové zprávy HTTP 500, které neobsahují žádné informace o zdroji problému.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="bdd4d-330">Například pokud soubor *views/_ViewImports. cshtml* obsahuje obor názvů, který v projektu neexistuje, je vygenerována chyba HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="bdd4d-331">Ve výchozím nastavení se v ASP.NET Core aplikace `UseDeveloperExceptionPage` rozšíření přidá do `IApplicationBuilder` a spustí se při *vývoji*konfigurace.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="bdd4d-332">Podívejte se na příklad v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="bdd4d-333">ASP.NET Core převede neošetřené výjimky na chybové odpovědi HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="bdd4d-334">V těchto odpovědích obvykle nejsou podrobnosti o chybě zahrnuty, aby nedocházelo k odhalení potenciálně citlivých informací o serveru.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="bdd4d-335">Další informace najdete na [stránce s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bdd4d-336">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="bdd4d-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="bdd4d-337">Tento článek ukazuje, jak zahájit migraci projektu ASP.NET MVC na [ASP.NET Core mvc](xref:mvc/overview) 2,1.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="bdd4d-338">V tomto procesu se zvýrazní spousta věcí, které se změnily z ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="bdd4d-339">Migrace z ASP.NET MVC je proces s více kroky.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="bdd4d-340">Tento článek popisuje:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-340">This article covers:</span></span>

* <span data-ttu-id="bdd4d-341">Počáteční nastavení</span><span class="sxs-lookup"><span data-stu-id="bdd4d-341">Initial setup</span></span>
* <span data-ttu-id="bdd4d-342">Základní řadiče a zobrazení</span><span class="sxs-lookup"><span data-stu-id="bdd4d-342">Basic controllers and views</span></span>
* <span data-ttu-id="bdd4d-343">Statický obsah</span><span class="sxs-lookup"><span data-stu-id="bdd4d-343">Static content</span></span>
* <span data-ttu-id="bdd4d-344">Závislosti na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-344">Client-side dependencies.</span></span>

<span data-ttu-id="bdd4d-345">Informace o migraci konfigurace a Identity kódu najdete v tématu [migrace konfigurace do ASP.NET Core](xref:migration/configuration) a [migrace ověřování a Identity ASP.NET Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-345">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="bdd4d-346">Čísla verzí v ukázkách nemusí být aktuální, aktualizujte projekty odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="bdd4d-347">Vytvoření projektu počáteční ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="bdd4d-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="bdd4d-348">K předvedení upgradu začneme vytvořením aplikace ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="bdd4d-349">Vytvořte ho s názvem *WebApp1* , aby obor názvů odpovídal ASP.NET Coremu projektu vytvořenému v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Dialogové okno Nový projekt sady Visual Studio](mvc/_static/new-project.png)

![Dialogové okno Nová webová aplikace: šablona projektu MVC vybraná v panelu šablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="bdd4d-352">*Volitelné:* Změňte název řešení z *WebApp1* na *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="bdd4d-353">Visual Studio zobrazí nový název řešení (*Mvc5*), který usnadňuje podávání tohoto projektu z dalšího projektu.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-353">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="bdd4d-354">Vytvoření projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bdd4d-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="bdd4d-355">Vytvořte novou *prázdnou* ASP.NET Core webovou aplikaci se stejným názvem jako předchozí projekt (*WebApp1*), aby se obory názvů ve dvou projektech shodovaly.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="bdd4d-356">Stejný obor názvů usnadňuje kopírování kódu mezi dvěma projekty.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="bdd4d-357">Tento projekt vytvořte v jiném adresáři než předchozí projekt, abyste mohli použít stejný název.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![Dialogové okno Nový projekt](mvc/_static/new_core.png)

![Dialog nové webové aplikace v ASP.NET: prázdná šablona projektu vybraná na panelu šablon ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="bdd4d-360">*Volitelné:* Vytvořte novou aplikaci ASP.NET Core pomocí šablony projektu *webové aplikace* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="bdd4d-361">Pojmenujte projekt *WebApp1*a vyberte možnost ověřování **jednotlivých uživatelských účtů**.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-361">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="bdd4d-362">Přejmenujte tuto aplikaci na *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-362">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="bdd4d-363">Při vytváření tohoto projektu se šetří čas v převodu.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="bdd4d-364">Konečný výsledek lze zobrazit v kódu generovaném šablonou, kód může být zkopírován do projektu pro převod nebo v porovnání s projektem generovaným šablonou.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="bdd4d-365">Konfigurace lokality pro použití MVC</span><span class="sxs-lookup"><span data-stu-id="bdd4d-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="bdd4d-366">Při cílení na .NET Core se ve výchozím nastavení odkazuje na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="bdd4d-367">Tento balíček obsahuje balíčky běžně používané aplikacemi MVC.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="bdd4d-368">Pokud cílíte .NET Framework, musí být odkazy na balíček uvedeny jednotlivě v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="bdd4d-369">`Microsoft.AspNetCore.Mvc` je ASP.NET Core architekturu MVC.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="bdd4d-370">`Microsoft.AspNetCore.StaticFiles` je obslužná rutina statického souboru.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="bdd4d-371">Aplikace ASP.NET Core výslovně přihlašovat pro middleware, například pro obsluhu statických souborů.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="bdd4d-372">Další informace najdete v tématu [statické soubory](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="bdd4d-373">Otevřete soubor *Startup.cs* a změňte kód tak, aby odpovídal následujícímu:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="bdd4d-374"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Metoda rozšíření přidá obslužnou rutinu statického souboru.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="bdd4d-375">`UseMvc`Metoda rozšíření přidává směrování.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="bdd4d-376">Další informace najdete v tématu [spuštění](xref:fundamentals/startup) a [Směrování](xref:fundamentals/routing)aplikace.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="bdd4d-377">Přidat kontroler a zobrazení</span><span class="sxs-lookup"><span data-stu-id="bdd4d-377">Add a controller and view</span></span>

<span data-ttu-id="bdd4d-378">V této části se přidávají minimální kontroler a zobrazení, které slouží jako zástupné symboly pro kontroler MVC ASP.NET a zobrazení migrované v další části.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="bdd4d-379">Přidejte adresář *řadičů* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="bdd4d-380">Přidejte do adresáře *Controllers* **třídu controller** s názvem *HomeController.cs* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Dialogové okno Přidat novou položku](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="bdd4d-382">Přidejte adresář *zobrazení* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="bdd4d-383">Přidání *zobrazení/domovského* adresáře</span><span class="sxs-lookup"><span data-stu-id="bdd4d-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="bdd4d-384">Přidejte \*\* Razor zobrazení\*\* s názvem *index. cshtml* do *zobrazení/domovského* adresáře.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-384">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Dialogové okno Přidat novou položku](mvc/_static/view.png)

<span data-ttu-id="bdd4d-386">Struktura projektu je zobrazena níže:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-386">The project structure is shown below:</span></span>

![Průzkumník řešení zobrazení souborů a adresářů WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="bdd4d-388">Obsah souboru *views/Home/index. cshtml* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="bdd4d-389">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-389">Run the app.</span></span>

![Webová aplikace otevřená v Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="bdd4d-391">Další informace najdete v tématu [řadiče](xref:mvc/controllers/actions) a [zobrazení](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="bdd4d-392">Následující funkce vyžadují migraci z ukázkového projektu ASP.NET MVC do projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="bdd4d-393">obsah na straně klienta (šablony stylů CSS, písma a skripty)</span><span class="sxs-lookup"><span data-stu-id="bdd4d-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="bdd4d-394">kontrolery</span><span class="sxs-lookup"><span data-stu-id="bdd4d-394">controllers</span></span>

* <span data-ttu-id="bdd4d-395">zobrazení</span><span class="sxs-lookup"><span data-stu-id="bdd4d-395">views</span></span>

* <span data-ttu-id="bdd4d-396">modely</span><span class="sxs-lookup"><span data-stu-id="bdd4d-396">models</span></span>

* <span data-ttu-id="bdd4d-397">sdružování</span><span class="sxs-lookup"><span data-stu-id="bdd4d-397">bundling</span></span>

* <span data-ttu-id="bdd4d-398">filtry</span><span class="sxs-lookup"><span data-stu-id="bdd4d-398">filters</span></span>

* <span data-ttu-id="bdd4d-399">Přihlaste se nebo se přihlaste Identity (to se provádí v dalším kurzu).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-399">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="bdd4d-400">Řadiče a zobrazení</span><span class="sxs-lookup"><span data-stu-id="bdd4d-400">Controllers and views</span></span>

* <span data-ttu-id="bdd4d-401">Zkopírujte každou z metod z ASP.NET MVC `HomeController` do nové `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="bdd4d-402">V ASP.NET MVC je jako návratový typ metody typu "předdefinovaná šablona" [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); ve ASP.NET Core MVC se místo toho vrací metody akcí `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-402">In ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="bdd4d-403">`ActionResult` implementuje `IActionResult` , takže není nutné měnit návratový typ metod akce.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="bdd4d-404">Zkopírujte soubory *About. cshtml*, *Contact. cshtml*a *index. cshtml* Razor zobrazení z projektu ASP.NET MVC do projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-404">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="bdd4d-405">Testování jednotlivých metod</span><span class="sxs-lookup"><span data-stu-id="bdd4d-405">Test each method</span></span>

<span data-ttu-id="bdd4d-406">Soubor rozložení a styly nebyly dosud migrovány, takže vykreslená zobrazení obsahují pouze obsah v zobrazení souborů.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="bdd4d-407">Soubory rozložení vygenerované odkazy pro `About` zobrazení a `Contact` nebudou zatím k dispozici.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="bdd4d-408">Vyvolání vygenerovaných zobrazení z prohlížeče na běžící aplikaci ASP.NET Core nahraďte aktuální číslo portu číslem portu použitým v projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="bdd4d-409">Například: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-409">For example: `https://localhost:44375/home/about`.</span></span>

![Stránka kontaktu](mvc/_static/contact-page.png)

<span data-ttu-id="bdd4d-411">Všimněte si nedostatku stylů a položek nabídky.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="bdd4d-412">Styl bude opraven v následující části.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="bdd4d-413">Statický obsah</span><span class="sxs-lookup"><span data-stu-id="bdd4d-413">Static content</span></span>

<span data-ttu-id="bdd4d-414">V ASP.NET MVC 5 a starších byl statický obsah hostovaný z kořenového adresáře webového projektu a byl vzájemně promíchán se soubory na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="bdd4d-415">V ASP.NET Core je statický obsah hostovaný v adresáři *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="bdd4d-416">Zkopírujte statický obsah z aplikace ASP.NET MVC do adresáře *wwwroot* v projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="bdd4d-417">V tomto ukázkovém převodu:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-417">In this sample conversion:</span></span>

* <span data-ttu-id="bdd4d-418">Zkopírujte soubor *favicon. ico* z projektu ASP.NET MVC do adresáře *wwwroot* v projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="bdd4d-419">Projekt ASP.NET MVC používá pro svůj styl [bootstrap](https://getbootstrap.com/) a ukládá spouštěcí soubory do adresářů *obsahu* a *skriptů* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="bdd4d-420">Šablona, která vygenerovala projekt ASP.NET MVC, odkazuje na Bootstrap v souboru rozložení (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="bdd4d-421">Soubory *bootstrap.js* a *bootstrap. CSS* mohou být zkopírovány z projektu ASP.NET MVC do adresáře *wwwroot* v novém projektu.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="bdd4d-422">Místo toho tento dokument přidá podporu pro Bootstrap (a další knihovny na straně klienta) pomocí sítě CDN v následující části.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="bdd4d-423">Migrovat soubor rozložení</span><span class="sxs-lookup"><span data-stu-id="bdd4d-423">Migrate the layout file</span></span>

* <span data-ttu-id="bdd4d-424">Zkopírujte soubor *_ViewStart. cshtml* z adresáře *zobrazení* projektu ASP.NET MVC do adresáře *zobrazení* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="bdd4d-425">Soubor *_ViewStart. cshtml* se v ASP.NET Core MVC nezměnil.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="bdd4d-426">Vytvořte *zobrazení/sdílený* adresář.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="bdd4d-427">*Volitelné:* Zkopírujte *_ViewImports. cshtml* z adresáře *zobrazení* projektu *FullAspNetCore* MVC do adresáře *zobrazení* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="bdd4d-428">Odeberte všechny deklarace oboru názvů v souboru *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="bdd4d-429">Soubor *_ViewImports. cshtml* poskytuje obory názvů pro všechny soubory zobrazení a přináší [pomocníkům značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="bdd4d-430">V novém souboru rozložení se používají pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="bdd4d-431">Soubor *_ViewImports. cshtml* je pro ASP.NET Core nový.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="bdd4d-432">Zkopírujte soubor *_Layout. cshtml* z *zobrazení/sdíleného* adresáře projektu ASP.NET MVC do *zobrazení/* sdíleného adresáře ASP.NET Core projektu.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="bdd4d-433">Otevřete soubor *_Layout. cshtml* a proveďte následující změny (dokončený kód je zobrazen níže):</span><span class="sxs-lookup"><span data-stu-id="bdd4d-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="bdd4d-434">Nahraďte `@Styles.Render("~/Content/css")` `<link>` elementem pro načtení *bootstrap. CSS* (viz níže).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="bdd4d-435">Odebrat `@Scripts.Render("~/bundles/modernizr")` .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="bdd4d-436">Odkomentujte `@Html.Partial("_LoginPartial")` řádek kolem řádku (Obklopte řádek s `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="bdd4d-437">Další informace najdete v tématu [migrace ověřování a Identity ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="bdd4d-437">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="bdd4d-438">Nahraďte `@Scripts.Render("~/bundles/jquery")` `<script>` elementem (viz níže).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="bdd4d-439">Nahraďte `@Scripts.Render("~/bundles/bootstrap")` `<script>` elementem (viz níže).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="bdd4d-440">Náhradní značky pro spuštění šablony stylů Bootstrap:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="bdd4d-441">Náhradní označení pro zahrnutí a zavedení JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="bdd4d-442">Aktualizovaný soubor *_Layout. cshtml* je uveden níže:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="bdd4d-443">Zobrazí web v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-443">View the site in the browser.</span></span> <span data-ttu-id="bdd4d-444">Měl by se teď správně načíst s očekávanými styly.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="bdd4d-445">*Volitelné:* Zkuste použít nový soubor rozložení.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="bdd4d-446">Zkopírujte soubor rozložení z projektu *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="bdd4d-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="bdd4d-447">Nový soubor rozložení používá [pomocníky značek](xref:mvc/views/tag-helpers/intro) a má další vylepšení.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="bdd4d-448">Konfigurace sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="bdd4d-448">Configure bundling and minification</span></span>

<span data-ttu-id="bdd4d-449">Informace o tom, jak nakonfigurovat sdružování a minifikace, najdete v článku [sdružování a minifikace](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="bdd4d-450">Řešení chyb HTTP 500</span><span class="sxs-lookup"><span data-stu-id="bdd4d-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="bdd4d-451">Existuje mnoho problémů, které mohou způsobit chybové zprávy HTTP 500, které neobsahují žádné informace o zdroji problému.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="bdd4d-452">Například pokud soubor *views/_ViewImports. cshtml* obsahuje obor názvů, který v projektu neexistuje, je vygenerována chyba HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="bdd4d-453">Ve výchozím nastavení se v ASP.NET Core aplikace `UseDeveloperExceptionPage` rozšíření přidá do `IApplicationBuilder` a spustí se při *vývoji*konfigurace.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="bdd4d-454">Podívejte se na příklad v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="bdd4d-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="bdd4d-455">ASP.NET Core převede neošetřené výjimky na chybové odpovědi HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="bdd4d-456">V těchto odpovědích obvykle nejsou podrobnosti o chybě zahrnuty, aby nedocházelo k odhalení potenciálně citlivých informací o serveru.</span><span class="sxs-lookup"><span data-stu-id="bdd4d-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="bdd4d-457">Další informace najdete na [stránce s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="bdd4d-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bdd4d-458">Další materiály</span><span class="sxs-lookup"><span data-stu-id="bdd4d-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
