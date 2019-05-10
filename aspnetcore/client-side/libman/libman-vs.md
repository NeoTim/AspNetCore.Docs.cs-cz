---
title: LibMan pomocí ASP.NET Core v sadě Visual Studio
author: scottaddie
description: Další informace o použití LibMan v projektu aplikace ASP.NET Core pomocí sady Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: 41a5a41c8921b04290784d26441ecb46aea753e7
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901026"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="8b2ee-103">LibMan pomocí ASP.NET Core v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b2ee-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="8b2ee-104">Podle [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="8b2ee-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="8b2ee-105">Visual Studio obsahuje integrovanou podporu [LibMan](xref:client-side/libman/index) v projektech ASP.NET Core, včetně:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="8b2ee-106">Podpora pro konfiguraci a spuštění operace obnovení LibMan na sestavení.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="8b2ee-107">Položky nabídky, která aktivuje LibMan obnovení a vyčistit operace.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="8b2ee-108">Dialog pro hledání pro hledání knihoven a přidávání souborů do projektu.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="8b2ee-109">Podporu pro editaci *libman.json*&mdash;LibMan souboru manifestu.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="8b2ee-110">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="8b2ee-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8b2ee-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="8b2ee-111">Prerequisites</span></span>

* <span data-ttu-id="8b2ee-112">Visual Studio 2017 verze 15,8 nebo novější s **vývoj pro ASP.NET a web** pracovního vytížení</span><span class="sxs-lookup"><span data-stu-id="8b2ee-112">Visual Studio 2017 version 15.8 or later with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="8b2ee-113">Přidejte soubory knihovny</span><span class="sxs-lookup"><span data-stu-id="8b2ee-113">Add library files</span></span>

<span data-ttu-id="8b2ee-114">Soubory knihovny můžete přidat do projektu aplikace ASP.NET Core dvěma různými způsoby:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="8b2ee-115">Použijte dialogové okno Přidat knihovnu na straně klienta</span><span class="sxs-lookup"><span data-stu-id="8b2ee-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="8b2ee-116">Ruční konfigurace LibMan položek souboru manifestu</span><span class="sxs-lookup"><span data-stu-id="8b2ee-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="8b2ee-117">Použijte dialogové okno Přidat knihovnu na straně klienta</span><span class="sxs-lookup"><span data-stu-id="8b2ee-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="8b2ee-118">Postupujte podle těchto kroků nainstalujte klientské knihovny:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="8b2ee-119">V **Průzkumníka řešení**, klikněte pravým tlačítkem na složku projektu, ve kterém soubory přidaly.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="8b2ee-120">Zvolte **přidat** > **knihoven na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="8b2ee-121">**Přidat knihovnu na straně klienta** se zobrazí dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Přidat dialog knihoven na straně klienta](_static/add-library-dialog.png)

* <span data-ttu-id="8b2ee-123">Vyberte zprostředkovatele knihovny z **poskytovatele** rozevírací seznam.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="8b2ee-124">CDNJS je výchozím zprostředkovatelem.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="8b2ee-125">Zadejte název knihovny k načtení v **knihovny** textového pole.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="8b2ee-126">Technologie IntelliSense poskytuje seznam knihoven počínaje zadaný text.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="8b2ee-127">Vyberte knihovnu ze seznamu technologie IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="8b2ee-128">Všimněte si, že je název knihovny příponu `@` symbolů a nejnovější stabilní verze označuje pro vybraného zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="8b2ee-129">Určete soubory, které chcete zahrnout:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-129">Decide which files to include:</span></span>
  * <span data-ttu-id="8b2ee-130">Vyberte **zahrnutí všech souborů knihovny** přepínací tlačítko, které zahrnují všechny soubory knihovně.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="8b2ee-131">Vyberte **zvolte konkrétní soubory** přepínací tlačítko, které zahrnují podmnožinu souborů knihovny.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="8b2ee-132">Když je přepínač vybrán, stromu výběru souboru je povolen.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="8b2ee-133">Zaškrtněte políčka nalevo od názvu souboru ke stažení.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="8b2ee-134">Zadejte složku projektu pro ukládání souborů do **cílové umístění** textového pole.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="8b2ee-135">Jako doporučení uložte každou knihovnu do samostatné složky.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="8b2ee-136">Návrhy **cílové umístění** složky podle umístění, ze kterého se spustí dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="8b2ee-137">Pokud se spustí z kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-137">If launched from the project root:</span></span>
    * <span data-ttu-id="8b2ee-138">*Wwwroot/lib* se používá v případě *wwwroot* existuje.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="8b2ee-139">*lib* se používá v případě *wwwroot* neexistuje.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="8b2ee-140">Pokud se spustí ze složky projektu, se používá odpovídající název složky.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="8b2ee-141">Návrh složky je přidán s názvem knihovny.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="8b2ee-142">Následující tabulka uvádí návrhy složky při instalaci jQuery v projektu pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="8b2ee-143">Spustit umístění</span><span class="sxs-lookup"><span data-stu-id="8b2ee-143">Launch location</span></span>                           |<span data-ttu-id="8b2ee-144">Navrhované složky</span><span class="sxs-lookup"><span data-stu-id="8b2ee-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="8b2ee-145">kořen projektu (Pokud *wwwroot* existuje)</span><span class="sxs-lookup"><span data-stu-id="8b2ee-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="8b2ee-146">*wwwroot/lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="8b2ee-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="8b2ee-147">kořen projektu (Pokud *wwwroot* neexistuje)</span><span class="sxs-lookup"><span data-stu-id="8b2ee-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="8b2ee-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="8b2ee-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="8b2ee-149">*Stránky* složky v projektu</span><span class="sxs-lookup"><span data-stu-id="8b2ee-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="8b2ee-150">*Pages/jquery/*</span><span class="sxs-lookup"><span data-stu-id="8b2ee-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="8b2ee-151">Klikněte na tlačítko **nainstalovat** tlačítko a stáhněte si soubory na konfiguraci v *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="8b2ee-152">Zkontrolujte **Správce knihovny** informačního kanálu **výstup** okno pro podrobné informace o instalaci.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="8b2ee-153">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="8b2ee-154">Ruční konfigurace LibMan položek souboru manifestu</span><span class="sxs-lookup"><span data-stu-id="8b2ee-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="8b2ee-155">Všechny operace LibMan v sadě Visual Studio jsou založeny na obsah manifestu LibMan kořen projektu (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="8b2ee-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="8b2ee-156">Můžete ručně upravit *libman.json* konfigurace soubory knihovny pro projekt.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="8b2ee-157">Visual Studio obnoví všechny soubory knihovny jednou *libman.json* se uloží.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="8b2ee-158">Chcete-li otevřít *libman.json* pro úpravy, existují tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="8b2ee-159">Dvakrát klikněte *libman.json* ve **Průzkumníka řešení**.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="8b2ee-160">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **správu knihoven na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="8b2ee-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="8b2ee-161">**&#8224;**</span></span>
* <span data-ttu-id="8b2ee-162">Vyberte **správu knihoven na straně klienta** ze sady Visual Studio **projektu** nabídky.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="8b2ee-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="8b2ee-163">**&#8224;**</span></span>

<span data-ttu-id="8b2ee-164">**&#8224;** Pokud *libman.json* soubor v kořenové složce projektu již neexistuje, vytvoří se s obsahem výchozí položku šablony.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="8b2ee-165">Visual Studio nabízí bohaté možnosti JSON úpravy podpory, jako je například zabarvení, formátování, technologie IntelliSense a ověřování schématu.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="8b2ee-166">Schéma JSON manifestu LibMan se nachází v umístění [ http://json.schemastore.org/libman ](http://json.schemastore.org/libman).</span><span class="sxs-lookup"><span data-stu-id="8b2ee-166">The LibMan manifest's JSON schema is found at [http://json.schemastore.org/libman](http://json.schemastore.org/libman).</span></span>

<span data-ttu-id="8b2ee-167">Pomocí následující soubor manifestu LibMan načte soubory za definované v konfiguraci `libraries` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="8b2ee-168">Vysvětlení literály objektů definovaných v rámci `libraries` následující:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="8b2ee-169">Podmnožinu [jQuery](https://jquery.com/) verze 3.3.1 je načten z CDNJS zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="8b2ee-170">Dílčí je definována v `files` vlastnost&mdash;*jquery.min.js*, *jquery.js*, a *jquery.min.map*.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="8b2ee-171">Soubory jsou umístěny v projektu *wwwroot/lib/jquery* složky.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="8b2ee-172">Podkladové [Bootstrap](https://getbootstrap.com/) verze 4.1.3 je načten a je umístěná v *wwwroot/lib/bootstrap* složky.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="8b2ee-173">Literál objektu `provider` vlastnosti přepsání `defaultProvider` hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="8b2ee-174">LibMan načte Bootstrap soubory z unpkg zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="8b2ee-175">Podmnožinu [Lodash](https://lodash.com/) schválila orgán v rámci organizace.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="8b2ee-176">*Lodash.js* a *lodash.min.js* soubory se načítají z místního systému souborů na *C:\\temp\\lodash\\*.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="8b2ee-177">Soubory se zkopírují do projektu *wwwroot/lib/lodash* složky.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="8b2ee-178">LibMan podporuje pouze jednu verzi každého knihovny od každého poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="8b2ee-179">*Libman.json* souboru schématu ověřování nezdaří, pokud obsahuje dvě knihovny se stejným názvem knihovny pro daného zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="8b2ee-180">Obnovit soubory knihoven</span><span class="sxs-lookup"><span data-stu-id="8b2ee-180">Restore library files</span></span>

<span data-ttu-id="8b2ee-181">Obnovení souborů knihovny ze sady Visual Studio, musí být platný *libman.json* soubor v kořenové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="8b2ee-182">Obnovené soubory jsou umístěny v projektu na umístění zadaném pro každou knihovnu.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="8b2ee-183">Soubory knihovny je možné obnovit v projektu aplikace ASP.NET Core dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="8b2ee-184">Obnovení souborů během sestavování</span><span class="sxs-lookup"><span data-stu-id="8b2ee-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="8b2ee-185">Ruční obnovení souborů</span><span class="sxs-lookup"><span data-stu-id="8b2ee-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="8b2ee-186">Obnovení souborů během sestavování</span><span class="sxs-lookup"><span data-stu-id="8b2ee-186">Restore files during build</span></span>

<span data-ttu-id="8b2ee-187">LibMan můžete obnovit soubory knihovny definované jako součást procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="8b2ee-188">Ve výchozím nastavení *obnovení na build* zakázané chování.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="8b2ee-189">Povolit a otestovat chování obnovení na sestavení:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="8b2ee-190">Klikněte pravým tlačítkem na *libman.json* v **Průzkumníka řešení** a vyberte **povolit obnovení na straně klienta knihovny na sestavení** v místní nabídce.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="8b2ee-191">Klikněte na tlačítko **Ano** tlačítko po zobrazení výzvy k instalaci balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="8b2ee-192">[Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) se přidá balíček NuGet do projektu:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="8b2ee-193">Sestavte projekt, abyste potvrdili, že dojde k obnovení souborů LibMan.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="8b2ee-194">`Microsoft.Web.LibraryManager.Build` Balíček vkládá cíl nástroje MSBuild, na kterém běží LibMan během operace sestavení projektu.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="8b2ee-195">Zkontrolujte **sestavení** informačního kanálu **výstup** okna pro protokol aktivit LibMan:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="8b2ee-196">Pokud je povoleno chování obnovení na sestavení, *libman.json* zobrazí místní nabídka **zakázat obnovení klientské knihovny na sestavení** možnost.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="8b2ee-197">Tato volba odstraní `Microsoft.Web.LibraryManager.Build` balíček odkaz ze souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="8b2ee-198">V důsledku toho budou na každé sestavení už obnoveny knihoven na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="8b2ee-199">Bez ohledu na nastavení obnovení na sestavení, můžete ručně obnovit kdykoli *libman.json* kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="8b2ee-200">Další informace najdete v tématu [ručně obnovit soubory](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="8b2ee-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="8b2ee-201">Ruční obnovení souborů</span><span class="sxs-lookup"><span data-stu-id="8b2ee-201">Restore files manually</span></span>

<span data-ttu-id="8b2ee-202">Chcete-li obnovit ručně soubory knihoven:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-202">To manually restore library files:</span></span>

* <span data-ttu-id="8b2ee-203">Pro všechny projekty v řešení:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="8b2ee-204">Klikněte pravým tlačítkem na název řešení v **Průzkumníka řešení**.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="8b2ee-205">Vyberte **obnovení knihoven na straně klienta** možnost.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="8b2ee-206">Pro konkrétní projekt:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-206">For a specific project:</span></span>
  * <span data-ttu-id="8b2ee-207">Klikněte pravým tlačítkem myši *libman.json* ve **Průzkumníka řešení**.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="8b2ee-208">Vyberte **obnovení knihoven na straně klienta** možnost.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="8b2ee-209">Během operace obnovení:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-209">While the restore operation is running:</span></span>

* <span data-ttu-id="8b2ee-210">Centrum stavu úloh (TSC) ikonu na stavovém řádku sady Visual Studio bude při animaci pohybovat a přečte *obnovení byla zahájena operace*.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="8b2ee-211">Kliknutím na ikonu otevře popisek výpisu úloh na pozadí známé.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="8b2ee-212">Zprávy se odešlou do stavového řádku a **Správce knihovny** informačního kanálu **výstup** okna.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="8b2ee-213">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="8b2ee-214">Odstranit soubory knihoven</span><span class="sxs-lookup"><span data-stu-id="8b2ee-214">Delete library files</span></span>

<span data-ttu-id="8b2ee-215">K provedení *čisté* operaci, která odstraní soubory knihoven, které byly obnoveny v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="8b2ee-216">Klikněte pravým tlačítkem myši *libman.json* ve **Průzkumníka řešení**.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="8b2ee-217">Vyberte **čisté knihoven na straně klienta** možnost.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="8b2ee-218">Aby se zabránilo neúmyslnému odebrání souborů mimo knihovnu, operace vyčištění nedojde k odstranění celé adresáře.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="8b2ee-219">Odebere pouze soubory, které byly součástí předchozích obnovení.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="8b2ee-220">Během operace čištění:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-220">While the clean operation is running:</span></span>

* <span data-ttu-id="8b2ee-221">TSC ikonu na stavovém řádku sady Visual Studio bude při animaci pohybovat a přečte *klientské knihovny operaci spustit*.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="8b2ee-222">Kliknutím na ikonu otevře popisek výpisu úloh na pozadí známé.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="8b2ee-223">Odeslání zpráv stavového řádku a **Správce knihovny** informačního kanálu **výstup** okna.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="8b2ee-224">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="8b2ee-225">Operace vyčištění odstraní jenom soubory z projektu.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="8b2ee-226">Soubory knihoven zůstat v mezipaměti pro rychlejší načítání na budoucí obnovení operací.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="8b2ee-227">Ke správě knihovny soubory uložené v mezipaměti místním počítači, použijte [LibMan CLI](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="8b2ee-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="8b2ee-228">Odinstalace soubory knihoven</span><span class="sxs-lookup"><span data-stu-id="8b2ee-228">Uninstall library files</span></span>

<span data-ttu-id="8b2ee-229">Chcete-li odinstalovat soubory knihovny:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-229">To uninstall library files:</span></span>

* <span data-ttu-id="8b2ee-230">Open *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-230">Open *libman.json*.</span></span>
* <span data-ttu-id="8b2ee-231">Pozici blikajícího kurzoru dovnitř odpovídající `libraries` literálu objektu.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="8b2ee-232">Klikněte na ikonu žárovky, která se zobrazí u levého okraje a vyberte **odinstalovat \<library_name > @\<library_version >**:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Knihovna možnost místní nabídky odinstalovat](_static/uninstall-menu-option.png)

<span data-ttu-id="8b2ee-234">Alternativně můžete ručně upravit a uložit LibMan manifest (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="8b2ee-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="8b2ee-235">[Operace obnovení](#restore-library-files) spustí, když je soubor uložen.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="8b2ee-236">Soubory knihoven, které jsou již definovány v *libman.json* jsou odebrány z projektu.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="8b2ee-237">Aktualizujte verzi knihovny</span><span class="sxs-lookup"><span data-stu-id="8b2ee-237">Update library version</span></span>

<span data-ttu-id="8b2ee-238">Vyhledat aktualizovanou knihovní verze:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-238">To check for an updated library version:</span></span>

* <span data-ttu-id="8b2ee-239">Open *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-239">Open *libman.json*.</span></span>
* <span data-ttu-id="8b2ee-240">Pozici blikajícího kurzoru dovnitř odpovídající `libraries` literálu objektu.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="8b2ee-241">Klikněte na ikonu žárovky, která se zobrazí na levém okraji.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="8b2ee-242">Najeďte myší na **vyhledávat aktualizace**.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="8b2ee-243">LibMan zkontroluje verzi knihovny, která je novější než verze nainstalovaná.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="8b2ee-244">Může dojít následujících situací:</span><span class="sxs-lookup"><span data-stu-id="8b2ee-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="8b2ee-245">A **nenašly se žádné aktualizace** se zobrazí zpráva, pokud už je nainstalovaná nejnovější verze.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="8b2ee-246">Nejnovější stabilní verze je zobrazen Pokud ještě není nainstalované.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-246">The latest stable version is displayed if not already installed.</span></span>

  ![Zkontrolovat aktualizace možnost místní nabídky](_static/update-menu-option.png)

* <span data-ttu-id="8b2ee-248">Pokud se předběžné verze novější než nainstalovaná verze je k dispozici, zobrazí se předběžné verze.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="8b2ee-249">Přejít na starší verzi knihovny, na ručně upravit *libman.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="8b2ee-250">Když je soubor uložen, LibMan [operace obnovení](#restore-library-files):</span><span class="sxs-lookup"><span data-stu-id="8b2ee-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="8b2ee-251">Odebere nadbytečné soubory z předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="8b2ee-252">Přidá nové a aktualizované soubory z nové verze.</span><span class="sxs-lookup"><span data-stu-id="8b2ee-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b2ee-253">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8b2ee-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="8b2ee-254">Úložiště LibMan GitHub</span><span class="sxs-lookup"><span data-stu-id="8b2ee-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
