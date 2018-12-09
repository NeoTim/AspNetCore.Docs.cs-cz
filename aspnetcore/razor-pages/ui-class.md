---
title: Opakovaně použitelné Razor uživatelského rozhraní v knihovnách tříd pomocí ASP.NET Core
author: Rick-Anderson
description: Vysvětluje, jak vytvářet opakovaně použitelné uživatelské rozhraní Razor pomocí částečných zobrazení v knihovně tříd v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/07/2018
ms.custom: seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: e5f329dcc423a7b7d6c247d0d359d35d95283de4
ms.sourcegitcommit: 49faca2644590fc081d86db46ea5e29edfc28b7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2018
ms.locfileid: "53121489"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="0153e-103">Vytvoření opakovaně použitelné uživatelské rozhraní v ASP.NET Core pomocí projektu knihovny tříd Razor</span><span class="sxs-lookup"><span data-stu-id="0153e-103">Create reusable UI using the Razor Class Library project in ASP.NET Core</span></span>

<span data-ttu-id="0153e-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0153e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0153e-105">Zobrazení syntaxe Razor, stránky, řadiče, stránka modely [zobrazení součástí](xref:mvc/views/view-components), a datových modelů může být sestaven do knihovny tříd Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="0153e-105">Razor views, pages, controllers, page models, [View components](xref:mvc/views/view-components), and data models can be built into a Razor Class Library (RCL).</span></span> <span data-ttu-id="0153e-106">RCL můžete zabalit a znovu použít.</span><span class="sxs-lookup"><span data-stu-id="0153e-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="0153e-107">Aplikace můžete zahrnout RCL a přepsání, zobrazení a stránky, které obsahuje.</span><span class="sxs-lookup"><span data-stu-id="0153e-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="0153e-108">Při zobrazení, částečná zobrazení nebo stránky Razor se nachází v webové aplikace a RCL kód Razor (*.cshtml* soubor) na webu aplikace má přednost.</span><span class="sxs-lookup"><span data-stu-id="0153e-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="0153e-109">Tato funkce vyžaduje [!INCLUDE[](~/includes/2.1-SDK.md)]</span><span class="sxs-lookup"><span data-stu-id="0153e-109">This feature requires [!INCLUDE[](~/includes/2.1-SDK.md)]</span></span>

<span data-ttu-id="0153e-110">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0153e-110">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="0153e-111">Vytvoření knihovny tříd obsahující Razor uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="0153e-111">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0153e-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0153e-112">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0153e-113">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="0153e-113">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0153e-114">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0153e-114">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0153e-115">Název knihovny (například "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="0153e-115">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="0153e-116">Aby se zabránilo kolize názvů souborů s knihovnou generované zobrazení, ujistěte se ale nekončí název knihovny v `.Views`.</span><span class="sxs-lookup"><span data-stu-id="0153e-116">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="0153e-117">Ověřte **ASP.NET Core 2.1** nebo novější je vybrána.</span><span class="sxs-lookup"><span data-stu-id="0153e-117">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="0153e-118">Vyberte **knihovny tříd Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="0153e-118">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="0153e-119">Knihovny tříd Razor má následující soubor projektu:</span><span class="sxs-lookup"><span data-stu-id="0153e-119">A Razor Class Library has the following project file:</span></span>

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="0153e-120">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="0153e-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0153e-121">Z příkazového řádku, spusťte `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="0153e-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="0153e-122">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0153e-122">For example:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="0153e-123">Další informace najdete v tématu [dotnet nové](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="0153e-123">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="0153e-124">Aby se zabránilo kolize názvů souborů s knihovnou generované zobrazení, ujistěte se ale nekončí název knihovny v `.Views`.</span><span class="sxs-lookup"><span data-stu-id="0153e-124">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

------
<span data-ttu-id="0153e-125">Přidáte soubory Razor RCL.</span><span class="sxs-lookup"><span data-stu-id="0153e-125">Add Razor files to the RCL.</span></span>

<span data-ttu-id="0153e-126">Šablony ASP.NET Core předpokládat RCL obsah je *oblasti* složky.</span><span class="sxs-lookup"><span data-stu-id="0153e-126">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="0153e-127">V tématu [rozložení stránek RCL](#afs) k vytvoření obsahu v RCL, který zpřístupňuje `~/Pages` spíše než `~/Areas/Pages`.</span><span class="sxs-lookup"><span data-stu-id="0153e-127">See [RCL Pages layout](#afs) to create a RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="referencing-razor-class-library-content"></a><span data-ttu-id="0153e-128">Odkazování na obsah knihovny tříd Razor</span><span class="sxs-lookup"><span data-stu-id="0153e-128">Referencing Razor Class Library content</span></span>

<span data-ttu-id="0153e-129">RCL lze odkazovat pomocí:</span><span class="sxs-lookup"><span data-stu-id="0153e-129">The RCL can be referenced by:</span></span>

* <span data-ttu-id="0153e-130">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="0153e-130">NuGet package.</span></span> <span data-ttu-id="0153e-131">V tématu [balíčky NuGet vytváření](/nuget/create-packages/creating-a-package) a [se příkaz dotnet add package](/dotnet/core/tools/dotnet-add-package) a [vytvoření a publikování balíčku NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="0153e-131">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="0153e-132">*{ProjectName} .csproj*.</span><span class="sxs-lookup"><span data-stu-id="0153e-132">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="0153e-133">Zobrazit [dotnet-přidat odkaz na](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="0153e-133">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-a-razor-class-library-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="0153e-134">Návod: Vytvoření projektu knihovny tříd Razor a používat z projektu pro stránky Razor</span><span class="sxs-lookup"><span data-stu-id="0153e-134">Walkthrough: Create a Razor Class Library project and use from a Razor Pages project</span></span>

<span data-ttu-id="0153e-135">Můžete stáhnout [dokončený projekt](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) a otestovat ho namísto jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="0153e-135">You can download the [complete project](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="0153e-136">Vzorku ke stažení obsahuje další kódu a odkazy, které usnadňuje testování projektu.</span><span class="sxs-lookup"><span data-stu-id="0153e-136">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="0153e-137">Zanecháte zpětnou vazbu v [tento problém Githubu](https://github.com/aspnet/Docs/issues/6098) s komentáře na stažení ukázky a podrobné pokyny.</span><span class="sxs-lookup"><span data-stu-id="0153e-137">You can leave feedback in [this GitHub issue](https://github.com/aspnet/Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="0153e-138">Testování aplikace ke stažení</span><span class="sxs-lookup"><span data-stu-id="0153e-138">Test the download app</span></span>

<span data-ttu-id="0153e-139">Pokud jste nestáhli dokončené aplikace a by místo toho vytvořte projekt návodu, pokračujte [další části](#create-a-razor-class-library).</span><span class="sxs-lookup"><span data-stu-id="0153e-139">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-a-razor-class-library).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0153e-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0153e-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0153e-141">Otevřít *.sln* souboru v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0153e-141">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="0153e-142">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0153e-142">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="0153e-143">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="0153e-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0153e-144">Z příkazového řádku v *rozhraní příkazového řádku* adresáře, vytvářet RCL a webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="0153e-144">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```console
dotnet build
```

<span data-ttu-id="0153e-145">Přesunout *WebApp1* adresáře a spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0153e-145">Move to the *WebApp1* directory and run the app:</span></span>

```console
dotnet run
```

------

<span data-ttu-id="0153e-146">Postupujte podle pokynů v [WebApp1 testu](#test)</span><span class="sxs-lookup"><span data-stu-id="0153e-146">Follow the instructions in [Test WebApp1](#test)</span></span>

## <a name="create-a-razor-class-library"></a><span data-ttu-id="0153e-147">Vytvoření knihovny tříd Razor</span><span class="sxs-lookup"><span data-stu-id="0153e-147">Create a Razor Class Library</span></span>

<span data-ttu-id="0153e-148">V této části se vytvoří knihovny tříd Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="0153e-148">In this section, a Razor Class Library (RCL) is created.</span></span> <span data-ttu-id="0153e-149">Soubory Razor jsou přidány do RCL.</span><span class="sxs-lookup"><span data-stu-id="0153e-149">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0153e-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0153e-150">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0153e-151">Vytvoření projektu RCL:</span><span class="sxs-lookup"><span data-stu-id="0153e-151">Create the RCL project:</span></span>

* <span data-ttu-id="0153e-152">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="0153e-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0153e-153">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0153e-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0153e-154">Pojmenujte aplikaci **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="0153e-154">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="0153e-155">Ověřte **ASP.NET Core 2.1** nebo novější je vybrána.</span><span class="sxs-lookup"><span data-stu-id="0153e-155">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="0153e-156">Vyberte **knihovny tříd Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="0153e-156">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="0153e-157">Přidejte do ní soubor částečného zobrazení Razor *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0153e-157">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="0153e-158">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="0153e-158">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0153e-159">Z příkazového řádku spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="0153e-159">From the command line, run the following:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="0153e-160">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="0153e-160">The preceding commands:</span></span>

* <span data-ttu-id="0153e-161">Vytvoří `RazorUIClassLib` knihovny tříd Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="0153e-161">Creates the `RazorUIClassLib` Razor Class Library (RCL).</span></span>
* <span data-ttu-id="0153e-162">Vytvoří stránku Razor _TEXT a přidá jej RCL.</span><span class="sxs-lookup"><span data-stu-id="0153e-162">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="0153e-163">`-np` Parametr vytvoří, aniž by `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="0153e-163">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="0153e-164">Vytvoří [soubor _ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) soubor a přidá jej RCL.</span><span class="sxs-lookup"><span data-stu-id="0153e-164">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="0153e-165">*Soubor _ViewStart.cshtml* soubor je vyžadován pro rozložení stránky Razor projektu (která se přidá v další části).</span><span class="sxs-lookup"><span data-stu-id="0153e-165">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

------

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="0153e-166">Přidání Razor souborů a složek do projektu</span><span class="sxs-lookup"><span data-stu-id="0153e-166">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="0153e-167">Nahraďte kód v *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0153e-167">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="0153e-168">Nahraďte kód v *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="0153e-168">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

<span data-ttu-id="0153e-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` je potřeba používat částečné zobrazení (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="0153e-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="0153e-170">Místo včetně `@addTagHelper` direktiv, můžete přidat *_ViewImports.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="0153e-170">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="0153e-171">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0153e-171">For example:</span></span>

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="0153e-172">Další informace o *_ViewImports.cshtml*, naleznete v tématu [import sdílených direktivy](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="0153e-172">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="0153e-173">Sestavení knihovny tříd pro ověření, že zde nejsou žádné chyby kompilátoru:</span><span class="sxs-lookup"><span data-stu-id="0153e-173">Build the class library to verify there are no compiler errors:</span></span>

```console
dotnet build RazorUIClassLib
```

<span data-ttu-id="0153e-174">Výstup sestavení obsahuje *RazorUIClassLib.dll* a *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="0153e-174">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="0153e-175">*RazorUIClassLib.Views.dll* obsahuje kompilovaný obsah Razor.</span><span class="sxs-lookup"><span data-stu-id="0153e-175">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="0153e-176">Použití knihovny uživatelského rozhraní Razor z projektu pro stránky Razor</span><span class="sxs-lookup"><span data-stu-id="0153e-176">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0153e-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0153e-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0153e-178">Vytvoření webové aplikace stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="0153e-178">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="0153e-179">Z **Průzkumníka řešení**, klikněte pravým tlačítkem na řešení > **přidat** >  **nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="0153e-179">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="0153e-180">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0153e-180">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0153e-181">Pojmenujte aplikaci **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="0153e-181">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="0153e-182">Ověřte **ASP.NET Core 2.1** nebo novější je vybrána.</span><span class="sxs-lookup"><span data-stu-id="0153e-182">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="0153e-183">Vyberte **webovou aplikaci** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="0153e-183">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="0153e-184">Z **Průzkumníka řešení**, klikněte pravým tlačítkem na **WebApp1** a vyberte **nastavit jako spouštěný projekt**.</span><span class="sxs-lookup"><span data-stu-id="0153e-184">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="0153e-185">Z **Průzkumníka řešení**, klikněte pravým tlačítkem na **WebApp1** a vyberte **závislosti sestavení** > **závislosti projektu**.</span><span class="sxs-lookup"><span data-stu-id="0153e-185">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="0153e-186">Zkontrolujte **RazorUIClassLib** jako závislost **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="0153e-186">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="0153e-187">Z **Průzkumníka řešení**, klikněte pravým tlačítkem na **WebApp1** a vyberte **přidat** > **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="0153e-187">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="0153e-188">V **správce odkazů** dialogové okno Kontrola **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="0153e-188">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="0153e-189">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0153e-189">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="0153e-190">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="0153e-190">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0153e-191">Vytvoření webová aplikace Razor Pages a soubor řešení, který obsahuje aplikace Razor Pages a knihovny tříd Razor:</span><span class="sxs-lookup"><span data-stu-id="0153e-191">Create a Razor Pages web app and a solution file containing the Razor Pages app and the Razor Class Library:</span></span>

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="0153e-192">Sestavení a spuštění webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="0153e-192">Build and run the web app:</span></span>

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a><span data-ttu-id="0153e-193">WebApp1 testu</span><span class="sxs-lookup"><span data-stu-id="0153e-193">Test WebApp1</span></span>

<span data-ttu-id="0153e-194">Ověřte, že se používá knihovny tříd Razor uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0153e-194">Verify the Razor UI class library is being used.</span></span>

* <span data-ttu-id="0153e-195">Přejděte do `/MyFeature/Page1`.</span><span class="sxs-lookup"><span data-stu-id="0153e-195">Browse to `/MyFeature/Page1`.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="0153e-196">Přepsání, zobrazení, částečná zobrazení a stránky</span><span class="sxs-lookup"><span data-stu-id="0153e-196">Override views, partial views, and pages</span></span>

<span data-ttu-id="0153e-197">Při zobrazení, částečná zobrazení nebo stránky Razor se nachází v webové aplikace a knihovny tříd Razor, kód Razor (*.cshtml* soubor) na webu aplikace má přednost.</span><span class="sxs-lookup"><span data-stu-id="0153e-197">When a view, partial view, or Razor Page is found in both the web app and the Razor Class Library, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="0153e-198">Například přidejte *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* k WebApp1, a Page1 v WebApp1 přednost Page1 v knihovně tříd Razor.</span><span class="sxs-lookup"><span data-stu-id="0153e-198">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the Razor Class Library.</span></span>

<span data-ttu-id="0153e-199">Ve vzorku ke stažení, přejmenujte *WebApp1/oblasti/MyFeature2* k *WebApp1/oblasti/MyFeature* otestovat prioritu.</span><span class="sxs-lookup"><span data-stu-id="0153e-199">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="0153e-200">Kopírovat *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* částečné zobrazení k *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0153e-200">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="0153e-201">Aktualizace značky k označení nového umístění.</span><span class="sxs-lookup"><span data-stu-id="0153e-201">Update the markup to indicate the new location.</span></span> <span data-ttu-id="0153e-202">Sestavení a spuštění aplikace a zkontrolujte, že verze aplikace s částečným se používá.</span><span class="sxs-lookup"><span data-stu-id="0153e-202">Build and run the app to verify the app's version of the partial is being used.</span></span>

<a name="afs"></a>

### <a name="rcl-pages-layout"></a><span data-ttu-id="0153e-203">Rozložení stránek RCL</span><span class="sxs-lookup"><span data-stu-id="0153e-203">RCL Pages layout</span></span>

<span data-ttu-id="0153e-204">K odkazu RCL obsah, jako by šlo součást webové aplikace *stránky* složku, vytvořte projekt RCL s následující strukturou souboru:</span><span class="sxs-lookup"><span data-stu-id="0153e-204">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="0153e-205">*RazorUIClassLib/stránky*</span><span class="sxs-lookup"><span data-stu-id="0153e-205">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="0153e-206">*RazorUIClassLib/stránek/Shared*</span><span class="sxs-lookup"><span data-stu-id="0153e-206">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="0153e-207">Předpokládejme, že *RazorUIClassLib/stránek/Shared* obsahuje dva soubory částečné: *_Header.cshtml* a *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0153e-207">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="0153e-208">`<partial>` Značky může být přidán do *_Layout.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="0153e-208">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>
  
```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```
