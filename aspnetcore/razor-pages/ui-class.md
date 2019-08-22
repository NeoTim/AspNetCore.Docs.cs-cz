---
title: Opakovaně použitelné Razor uživatelského rozhraní v knihovnách tříd pomocí ASP.NET Core
author: Rick-Anderson
description: Vysvětluje, jak vytvářet opakovaně použitelné uživatelské rozhraní Razor pomocí částečných zobrazení v knihovně tříd v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/20/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 468d961c291810ca4dfbe615acd972cfd6e7572a
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886396"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="49c3b-103">Vytvoření opakovaně použitelného uživatelského rozhraní pomocí projektu knihovny tříd Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49c3b-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="49c3b-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="49c3b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="49c3b-105">Zobrazení Razor, stránky, řadiče, modely stránek, [komponenty Razor](xref:blazor/class-libraries), [zobrazení komponent](xref:mvc/views/view-components)a datové modely lze integrovat do knihovny tříd Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="49c3b-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="49c3b-106">RCL můžete zabalit a znovu použít.</span><span class="sxs-lookup"><span data-stu-id="49c3b-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="49c3b-107">Aplikace můžete zahrnout RCL a přepsání, zobrazení a stránky, které obsahuje.</span><span class="sxs-lookup"><span data-stu-id="49c3b-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="49c3b-108">Při zobrazení, částečná zobrazení nebo stránky Razor se nachází v webové aplikace a RCL kód Razor ( *.cshtml* soubor) na webu aplikace má přednost.</span><span class="sxs-lookup"><span data-stu-id="49c3b-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="49c3b-109">Tato funkce vyžaduje [!INCLUDE[](~/includes/2.1-SDK.md)]</span><span class="sxs-lookup"><span data-stu-id="49c3b-109">This feature requires [!INCLUDE[](~/includes/2.1-SDK.md)]</span></span>

<span data-ttu-id="49c3b-110">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49c3b-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="49c3b-111">Vytvoření knihovny tříd obsahující Razor uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="49c3b-111">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="49c3b-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49c3b-112">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49c3b-113">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-113">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="49c3b-114">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-114">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="49c3b-115">Název knihovny (například "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-115">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="49c3b-116">Aby se zabránilo kolize názvů souborů s knihovnou generované zobrazení, ujistěte se ale nekončí název knihovny v `.Views`.</span><span class="sxs-lookup"><span data-stu-id="49c3b-116">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="49c3b-117">Ověřte **ASP.NET Core 2.1** nebo novější je vybrána.</span><span class="sxs-lookup"><span data-stu-id="49c3b-117">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="49c3b-118">Vyberte **knihovny tříd Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-118">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="49c3b-119">RCL má následující soubor projektu:</span><span class="sxs-lookup"><span data-stu-id="49c3b-119">An RCL has the following project file:</span></span>

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="49c3b-120">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="49c3b-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="49c3b-121">Z příkazového řádku, spusťte `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="49c3b-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="49c3b-122">Příklad:</span><span class="sxs-lookup"><span data-stu-id="49c3b-122">For example:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="49c3b-123">Další informace najdete v tématu [dotnet nové](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="49c3b-123">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="49c3b-124">Aby se zabránilo kolize názvů souborů s knihovnou generované zobrazení, ujistěte se ale nekončí název knihovny v `.Views`.</span><span class="sxs-lookup"><span data-stu-id="49c3b-124">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="49c3b-125">Přidáte soubory Razor RCL.</span><span class="sxs-lookup"><span data-stu-id="49c3b-125">Add Razor files to the RCL.</span></span>

<span data-ttu-id="49c3b-126">Šablony ASP.NET Core předpokládat RCL obsah je *oblasti* složky.</span><span class="sxs-lookup"><span data-stu-id="49c3b-126">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="49c3b-127">Další informace najdete v `~/Pages` tématu [RCL Page Layout (rozložení stránek](#afs) ) pro vytvoření `~/Areas/Pages`RCL, které zpřístupňuje obsah místo.</span><span class="sxs-lookup"><span data-stu-id="49c3b-127">See [RCL Pages layout](#afs) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="referencing-rcl-content"></a><span data-ttu-id="49c3b-128">Odkazování na obsah RCL</span><span class="sxs-lookup"><span data-stu-id="49c3b-128">Referencing RCL content</span></span>

<span data-ttu-id="49c3b-129">RCL lze odkazovat pomocí:</span><span class="sxs-lookup"><span data-stu-id="49c3b-129">The RCL can be referenced by:</span></span>

* <span data-ttu-id="49c3b-130">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="49c3b-130">NuGet package.</span></span> <span data-ttu-id="49c3b-131">V tématu [balíčky NuGet vytváření](/nuget/create-packages/creating-a-package) a [se příkaz dotnet add package](/dotnet/core/tools/dotnet-add-package) a [vytvoření a publikování balíčku NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="49c3b-131">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="49c3b-132">*{ProjectName} .csproj*.</span><span class="sxs-lookup"><span data-stu-id="49c3b-132">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="49c3b-133">Zobrazit [dotnet-přidat odkaz na](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="49c3b-133">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="49c3b-134">Návod: Vytvoření projektu RCL a použití z Razor Pagesho projektu</span><span class="sxs-lookup"><span data-stu-id="49c3b-134">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="49c3b-135">Můžete stáhnout [dokončený projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) a otestovat ho namísto jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="49c3b-135">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="49c3b-136">Vzorku ke stažení obsahuje další kódu a odkazy, které usnadňuje testování projektu.</span><span class="sxs-lookup"><span data-stu-id="49c3b-136">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="49c3b-137">Zanecháte zpětnou vazbu v [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/6098) s komentáře na stažení ukázky a podrobné pokyny.</span><span class="sxs-lookup"><span data-stu-id="49c3b-137">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="49c3b-138">Testování aplikace ke stažení</span><span class="sxs-lookup"><span data-stu-id="49c3b-138">Test the download app</span></span>

<span data-ttu-id="49c3b-139">Pokud jste nestáhli dokončené aplikace a by místo toho vytvořte projekt návodu, pokračujte [další části](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="49c3b-139">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="49c3b-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49c3b-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49c3b-141">Otevřít *.sln* souboru v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="49c3b-141">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="49c3b-142">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="49c3b-142">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="49c3b-143">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="49c3b-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="49c3b-144">Z příkazového řádku v *rozhraní příkazového řádku* adresáře, vytvářet RCL a webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="49c3b-144">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```console
dotnet build
```

<span data-ttu-id="49c3b-145">Přesunout *WebApp1* adresáře a spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="49c3b-145">Move to the *WebApp1* directory and run the app:</span></span>

```console
dotnet run
```

---

<span data-ttu-id="49c3b-146">Postupujte podle pokynů v [WebApp1 testu](#test)</span><span class="sxs-lookup"><span data-stu-id="49c3b-146">Follow the instructions in [Test WebApp1](#test)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="49c3b-147">Vytvoření RCL</span><span class="sxs-lookup"><span data-stu-id="49c3b-147">Create an RCL</span></span>

<span data-ttu-id="49c3b-148">V této části se vytvoří RCL.</span><span class="sxs-lookup"><span data-stu-id="49c3b-148">In this section, an RCL is created.</span></span> <span data-ttu-id="49c3b-149">Soubory Razor jsou přidány do RCL.</span><span class="sxs-lookup"><span data-stu-id="49c3b-149">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="49c3b-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49c3b-150">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49c3b-151">Vytvoření projektu RCL:</span><span class="sxs-lookup"><span data-stu-id="49c3b-151">Create the RCL project:</span></span>

* <span data-ttu-id="49c3b-152">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="49c3b-153">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="49c3b-154">Pojmenujte aplikaci **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-154">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="49c3b-155">Ověřte **ASP.NET Core 2.1** nebo novější je vybrána.</span><span class="sxs-lookup"><span data-stu-id="49c3b-155">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="49c3b-156">Vyberte **knihovny tříd Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-156">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="49c3b-157">Přidejte do ní soubor částečného zobrazení Razor *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="49c3b-157">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="49c3b-158">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="49c3b-158">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="49c3b-159">Z příkazového řádku spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="49c3b-159">From the command line, run the following:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="49c3b-160">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="49c3b-160">The preceding commands:</span></span>

* <span data-ttu-id="49c3b-161">`RazorUIClassLib` Vytvoří RCL.</span><span class="sxs-lookup"><span data-stu-id="49c3b-161">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="49c3b-162">Vytvoří stránku Razor _TEXT a přidá jej RCL.</span><span class="sxs-lookup"><span data-stu-id="49c3b-162">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="49c3b-163">`-np` Parametr vytvoří, aniž by `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="49c3b-163">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="49c3b-164">Vytvoří [soubor _ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) soubor a přidá jej RCL.</span><span class="sxs-lookup"><span data-stu-id="49c3b-164">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="49c3b-165">*Soubor _ViewStart.cshtml* soubor je vyžadován pro rozložení stránky Razor projektu (která se přidá v další části).</span><span class="sxs-lookup"><span data-stu-id="49c3b-165">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="49c3b-166">Přidání Razor souborů a složek do projektu</span><span class="sxs-lookup"><span data-stu-id="49c3b-166">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="49c3b-167">Nahraďte kód v *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="49c3b-167">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="49c3b-168">Nahraďte kód v *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="49c3b-168">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

<span data-ttu-id="49c3b-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` je potřeba používat částečné zobrazení (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="49c3b-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="49c3b-170">Místo včetně `@addTagHelper` direktiv, můžete přidat *_ViewImports.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="49c3b-170">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="49c3b-171">Příklad:</span><span class="sxs-lookup"><span data-stu-id="49c3b-171">For example:</span></span>

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="49c3b-172">Další informace o *_ViewImports.cshtml*, naleznete v tématu [import sdílených direktivy](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="49c3b-172">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="49c3b-173">Sestavení knihovny tříd pro ověření, že zde nejsou žádné chyby kompilátoru:</span><span class="sxs-lookup"><span data-stu-id="49c3b-173">Build the class library to verify there are no compiler errors:</span></span>

```console
dotnet build RazorUIClassLib
```

<span data-ttu-id="49c3b-174">Výstup sestavení obsahuje *RazorUIClassLib.dll* a *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="49c3b-174">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="49c3b-175">*RazorUIClassLib.Views.dll* obsahuje kompilovaný obsah Razor.</span><span class="sxs-lookup"><span data-stu-id="49c3b-175">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="49c3b-176">Použití knihovny uživatelského rozhraní Razor z projektu pro stránky Razor</span><span class="sxs-lookup"><span data-stu-id="49c3b-176">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="49c3b-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49c3b-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49c3b-178">Vytvoření webové aplikace stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="49c3b-178">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="49c3b-179">Z **Průzkumníka řešení**, klikněte pravým tlačítkem na řešení > **přidat** >  **nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-179">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="49c3b-180">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-180">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="49c3b-181">Pojmenujte aplikaci **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-181">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="49c3b-182">Ověřte **ASP.NET Core 2.1** nebo novější je vybrána.</span><span class="sxs-lookup"><span data-stu-id="49c3b-182">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="49c3b-183">Vyberte **webovou aplikaci** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-183">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="49c3b-184">Z **Průzkumníka řešení**, klikněte pravým tlačítkem na **WebApp1** a vyberte **nastavit jako spouštěný projekt**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-184">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="49c3b-185">Z **Průzkumníka řešení**, klikněte pravým tlačítkem na **WebApp1** a vyberte **závislosti sestavení** > **závislosti projektu**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-185">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="49c3b-186">Zkontrolujte **RazorUIClassLib** jako závislost **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-186">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="49c3b-187">Z **Průzkumníka řešení**, klikněte pravým tlačítkem na **WebApp1** a vyberte **přidat** > **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-187">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="49c3b-188">V **správce odkazů** dialogové okno Kontrola **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="49c3b-188">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="49c3b-189">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="49c3b-189">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="49c3b-190">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="49c3b-190">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="49c3b-191">Vytvoření webové aplikace Razor Pages a souboru řešení obsahujícího aplikaci Razor Pages a RCL:</span><span class="sxs-lookup"><span data-stu-id="49c3b-191">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="49c3b-192">Sestavení a spuštění webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="49c3b-192">Build and run the web app:</span></span>

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a><span data-ttu-id="49c3b-193">WebApp1 testu</span><span class="sxs-lookup"><span data-stu-id="49c3b-193">Test WebApp1</span></span>

<span data-ttu-id="49c3b-194">Ověřte, že se knihovna tříd uživatelského rozhraní Razor používá:</span><span class="sxs-lookup"><span data-stu-id="49c3b-194">Verify the Razor UI class library is in use:</span></span>

* <span data-ttu-id="49c3b-195">Přejděte do `/MyFeature/Page1`.</span><span class="sxs-lookup"><span data-stu-id="49c3b-195">Browse to `/MyFeature/Page1`.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="49c3b-196">Přepsání, zobrazení, částečná zobrazení a stránky</span><span class="sxs-lookup"><span data-stu-id="49c3b-196">Override views, partial views, and pages</span></span>

<span data-ttu-id="49c3b-197">Při zobrazení, částečná zobrazení nebo stránky Razor se nachází v webové aplikace a RCL kód Razor ( *.cshtml* soubor) na webu aplikace má přednost.</span><span class="sxs-lookup"><span data-stu-id="49c3b-197">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="49c3b-198">Například přidejte *WebApp1/areas/MyFeature/Pages/Page1. cshtml* do WebApp1 a Page1 v WebApp1 bude mít přednost před Page1 v RCL.</span><span class="sxs-lookup"><span data-stu-id="49c3b-198">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="49c3b-199">Ve vzorku ke stažení, přejmenujte *WebApp1/oblasti/MyFeature2* k *WebApp1/oblasti/MyFeature* otestovat prioritu.</span><span class="sxs-lookup"><span data-stu-id="49c3b-199">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="49c3b-200">Kopírovat *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* částečné zobrazení k *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="49c3b-200">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="49c3b-201">Aktualizace značky k označení nového umístění.</span><span class="sxs-lookup"><span data-stu-id="49c3b-201">Update the markup to indicate the new location.</span></span> <span data-ttu-id="49c3b-202">Sestavení a spuštění aplikace a zkontrolujte, že verze aplikace s částečným se používá.</span><span class="sxs-lookup"><span data-stu-id="49c3b-202">Build and run the app to verify the app's version of the partial is being used.</span></span>

<a name="afs"></a>

### <a name="rcl-pages-layout"></a><span data-ttu-id="49c3b-203">Rozložení stránek RCL</span><span class="sxs-lookup"><span data-stu-id="49c3b-203">RCL Pages layout</span></span>

<span data-ttu-id="49c3b-204">K odkazu RCL obsah, jako by šlo součást webové aplikace *stránky* složku, vytvořte projekt RCL s následující strukturou souboru:</span><span class="sxs-lookup"><span data-stu-id="49c3b-204">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="49c3b-205">*RazorUIClassLib/stránky*</span><span class="sxs-lookup"><span data-stu-id="49c3b-205">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="49c3b-206">*RazorUIClassLib/stránek/Shared*</span><span class="sxs-lookup"><span data-stu-id="49c3b-206">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="49c3b-207">Předpokládejme, že *RazorUIClassLib/stránek/Shared* obsahuje dva soubory částečné: *_Header.cshtml* a *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="49c3b-207">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="49c3b-208">`<partial>` Značky může být přidán do *_Layout.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="49c3b-208">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker range=">= aspnetcore-3.0"

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="49c3b-209">Vytvoření RCL se statickými prostředky</span><span class="sxs-lookup"><span data-stu-id="49c3b-209">Create an RCL with static assets</span></span>

<span data-ttu-id="49c3b-210">RCL může vyžadovat doprovodné statické prostředky, které mohou být odkazovány využitím aplikace RCL.</span><span class="sxs-lookup"><span data-stu-id="49c3b-210">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="49c3b-211">ASP.NET Core umožňuje vytváření RCLs, které zahrnují statické prostředky, které jsou k dispozici pro náročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="49c3b-211">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="49c3b-212">Chcete-li zahrnout doprovodné materiály jako součást RCL, vytvořte složku *wwwroot* v knihovně tříd a zahrňte všechny požadované soubory do této složky.</span><span class="sxs-lookup"><span data-stu-id="49c3b-212">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="49c3b-213">Při balení RCL jsou do balíčku automaticky zahrnuty všechny doprovodné materiály ve složce *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="49c3b-213">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="49c3b-214">Využití obsahu z odkazovaného RCL</span><span class="sxs-lookup"><span data-stu-id="49c3b-214">Consume content from a referenced RCL</span></span>

<span data-ttu-id="49c3b-215">Soubory zahrnuté ve složce *wwwroot* v RCL jsou zpřístupněné aplikacím, které jsou v předponě `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="49c3b-215">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="49c3b-216">Například knihovna s názvem *Razor. Class. lib* má za následek cestu ke statickému obsahu v `_content/Razor.Class.Lib/`.</span><span class="sxs-lookup"><span data-stu-id="49c3b-216">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span>

<span data-ttu-id="49c3b-217">Vybírající aplikace odkazuje na statické prostředky poskytované knihovnou `<script>`s `<style>`, `<img>`, a dalšími značkami HTML.</span><span class="sxs-lookup"><span data-stu-id="49c3b-217">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="49c3b-218">Vybírající aplikace musí mít povolenou [podporu statických souborů](xref:fundamentals/static-files) v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="49c3b-218">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="49c3b-219">Při spuštění náročné aplikace z výstupu sestavení (`dotnet run`) jsou ve výchozím nastavení ve vývojovém prostředí povoleny statické webové prostředky.</span><span class="sxs-lookup"><span data-stu-id="49c3b-219">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="49c3b-220">Pro podporu prostředků v jiných prostředích při spuštění z výstupu sestavení zavolejte `UseStaticWebAssets` na tvůrce hostitele v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="49c3b-220">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="49c3b-221">Při `UseStaticWebAssets` spuštění aplikace z publikovaného výstupu (`dotnet publish`) se volání nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="49c3b-221">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="49c3b-222">Vývojový tok pro více projektů</span><span class="sxs-lookup"><span data-stu-id="49c3b-222">Multi-project development flow</span></span>

<span data-ttu-id="49c3b-223">Když pracujete na náročné aplikaci:</span><span class="sxs-lookup"><span data-stu-id="49c3b-223">When the consuming app runs:</span></span>

* <span data-ttu-id="49c3b-224">Prostředky v RCL zůstanou v původních složkách.</span><span class="sxs-lookup"><span data-stu-id="49c3b-224">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="49c3b-225">Prostředky se nepřesunou do nenáročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="49c3b-225">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="49c3b-226">Jakákoli změna ve složce *WWWROOT* RCL se projeví v aplikaci po resestavení RCL a bez opětovného sestavení náročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="49c3b-226">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="49c3b-227">Při sestavení RCL je vytvořen manifest, který popisuje umístění statických webových prostředků.</span><span class="sxs-lookup"><span data-stu-id="49c3b-227">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="49c3b-228">Vybírající aplikace načte manifest za běhu za účelem využití prostředků z odkazovaných projektů a balíčků.</span><span class="sxs-lookup"><span data-stu-id="49c3b-228">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="49c3b-229">Když se do RCL přidá nový Asset, musí se RCL znovu sestavit, aby se aktualizoval jeho manifest předtím, než bude mít k novému assetu přístup.</span><span class="sxs-lookup"><span data-stu-id="49c3b-229">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="49c3b-230">Publikování</span><span class="sxs-lookup"><span data-stu-id="49c3b-230">Publish</span></span>

<span data-ttu-id="49c3b-231">Po publikování aplikace se doprovodné prostředky ze všech odkazovaných projektů a balíčků zkopírují do složky *wwwroot* publikované aplikace v části `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="49c3b-231">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end
