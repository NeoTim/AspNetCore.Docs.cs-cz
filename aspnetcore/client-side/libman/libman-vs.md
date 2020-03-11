---
title: Použití LibMan s ASP.NET Core v aplikaci Visual Studio
author: scottaddie
description: Naučte se používat LibMan v projektu ASP.NET Core se sadou Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658310"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="21541-103">Použití LibMan s ASP.NET Core v aplikaci Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21541-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="21541-104">[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="21541-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="21541-105">Visual Studio obsahuje integrovanou podporu pro [LibMan](xref:client-side/libman/index) v projektech ASP.NET Core, včetně:</span><span class="sxs-lookup"><span data-stu-id="21541-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="21541-106">Podpora pro konfiguraci a spuštění operací obnovení LibMan při sestavení.</span><span class="sxs-lookup"><span data-stu-id="21541-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="21541-107">Položky nabídky, které aktivují LibMan operace obnovení a vyčištění</span><span class="sxs-lookup"><span data-stu-id="21541-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="21541-108">Dialogové okno Hledat pro hledání knihoven a přidávání souborů do projektu.</span><span class="sxs-lookup"><span data-stu-id="21541-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="21541-109">Úprava podpory pro *Libman. json*&mdash;souboru manifestu Libman.</span><span class="sxs-lookup"><span data-stu-id="21541-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="21541-110">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="21541-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="21541-111">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="21541-111">Prerequisites</span></span>

* <span data-ttu-id="21541-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="21541-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="21541-113">Přidat soubory knihovny</span><span class="sxs-lookup"><span data-stu-id="21541-113">Add library files</span></span>

<span data-ttu-id="21541-114">Soubory knihovny lze přidat do projektu ASP.NET Core dvěma různými způsoby:</span><span class="sxs-lookup"><span data-stu-id="21541-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="21541-115">Použití dialogového okna přidat knihovnu na straně klienta</span><span class="sxs-lookup"><span data-stu-id="21541-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="21541-116">Ruční konfigurace položek souboru manifestu LibMan</span><span class="sxs-lookup"><span data-stu-id="21541-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="21541-117">Použití dialogového okna přidat knihovnu na straně klienta</span><span class="sxs-lookup"><span data-stu-id="21541-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="21541-118">Pomocí těchto kroků nainstalujete knihovnu na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="21541-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="21541-119">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku projektu, do které by se měly přidat soubory.</span><span class="sxs-lookup"><span data-stu-id="21541-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="21541-120">Vyberte možnost **přidat** > **knihovnu na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="21541-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="21541-121">Zobrazí se dialogové okno **přidat knihovnu na straně klienta** :</span><span class="sxs-lookup"><span data-stu-id="21541-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Dialogové okno Přidat knihovnu na straně klienta](_static/add-library-dialog.png)

* <span data-ttu-id="21541-123">V rozevíracím seznamu **zprostředkovatelů** vyberte poskytovatele knihovny.</span><span class="sxs-lookup"><span data-stu-id="21541-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="21541-124">CDNJS je výchozí zprostředkovatel.</span><span class="sxs-lookup"><span data-stu-id="21541-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="21541-125">Do textového pole **Knihovna** zadejte název knihovny, která se má načíst.</span><span class="sxs-lookup"><span data-stu-id="21541-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="21541-126">IntelliSense nabízí seznam knihoven začínajících zadaným textem.</span><span class="sxs-lookup"><span data-stu-id="21541-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="21541-127">Vyberte knihovnu ze seznamu technologie IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="21541-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="21541-128">Všimněte si, že název knihovny je zaregistrovaný symbolem `@` a nejnovější stabilní verzí známou pro vybraného zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="21541-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="21541-129">Rozhodněte, které soubory se mají zahrnout:</span><span class="sxs-lookup"><span data-stu-id="21541-129">Decide which files to include:</span></span>
  * <span data-ttu-id="21541-130">Výběrem přepínače **Zahrnout všechny soubory knihovny** Zahrňte všechny soubory knihovny.</span><span class="sxs-lookup"><span data-stu-id="21541-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="21541-131">Vyberte přepínač **zvolte konkrétní soubory** a přidejte tak podmnožinu souborů knihovny.</span><span class="sxs-lookup"><span data-stu-id="21541-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="21541-132">Je-li vybrán přepínač, je povolen strom pro výběr souboru.</span><span class="sxs-lookup"><span data-stu-id="21541-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="21541-133">Zaškrtněte políčka nalevo od názvů souborů ke stažení.</span><span class="sxs-lookup"><span data-stu-id="21541-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="21541-134">Zadejte složku projektu pro uložení souborů do textového pole **cílové umístění** .</span><span class="sxs-lookup"><span data-stu-id="21541-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="21541-135">Jako doporučení uložte každou knihovnu do samostatné složky.</span><span class="sxs-lookup"><span data-stu-id="21541-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="21541-136">Navrhovaná složka **umístění cíle** je založena na umístění, ze kterého byl dialog spuštěn:</span><span class="sxs-lookup"><span data-stu-id="21541-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="21541-137">Při spuštění z kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="21541-137">If launched from the project root:</span></span>
    * <span data-ttu-id="21541-138">*wwwroot/lib* se používá, pokud existuje *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="21541-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="21541-139">*lib* se používá, pokud neexistuje *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="21541-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="21541-140">Při spuštění ze složky projektu se použije odpovídající název složky.</span><span class="sxs-lookup"><span data-stu-id="21541-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="21541-141">Návrh složky má příponu s názvem knihovny.</span><span class="sxs-lookup"><span data-stu-id="21541-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="21541-142">Následující tabulka popisuje návrhy složek při instalaci jQuery do Razor Pages projektu.</span><span class="sxs-lookup"><span data-stu-id="21541-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="21541-143">Umístění pro spuštění</span><span class="sxs-lookup"><span data-stu-id="21541-143">Launch location</span></span>                           |<span data-ttu-id="21541-144">Navrhovaná složka</span><span class="sxs-lookup"><span data-stu-id="21541-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="21541-145">kořen projektu (pokud existuje *wwwroot* )</span><span class="sxs-lookup"><span data-stu-id="21541-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="21541-146">*wwwroot/lib/jQuery/*</span><span class="sxs-lookup"><span data-stu-id="21541-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="21541-147">kořen projektu (Pokud neexistuje *wwwroot* )</span><span class="sxs-lookup"><span data-stu-id="21541-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="21541-148">*lib/jQuery/*</span><span class="sxs-lookup"><span data-stu-id="21541-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="21541-149">Složka *stránky* v projektu</span><span class="sxs-lookup"><span data-stu-id="21541-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="21541-150">*Stránky/jQuery/*</span><span class="sxs-lookup"><span data-stu-id="21541-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="21541-151">Klikněte na tlačítko **instalovat** a Stáhněte soubory podle konfigurace v souboru *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="21541-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="21541-152">Podrobnosti o instalaci najdete v informačním kanálu **Správce knihovny** v okně **výstup** .</span><span class="sxs-lookup"><span data-stu-id="21541-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="21541-153">Příklad:</span><span class="sxs-lookup"><span data-stu-id="21541-153">For example:</span></span>

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

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="21541-154">Ruční konfigurace položek souboru manifestu LibMan</span><span class="sxs-lookup"><span data-stu-id="21541-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="21541-155">Všechny operace LibMan v aplikaci Visual Studio jsou založené na obsahu manifestu LibMan kořenu projektu (*LibMan. JSON*).</span><span class="sxs-lookup"><span data-stu-id="21541-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="21541-156">Můžete ručně upravit *Libman. JSON* a nakonfigurovat soubory knihovny pro projekt.</span><span class="sxs-lookup"><span data-stu-id="21541-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="21541-157">Po uložení souboru *Libman. JSON* obnoví aplikace Visual Studio všechny soubory knihoven.</span><span class="sxs-lookup"><span data-stu-id="21541-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="21541-158">Pro otevření *Libman. JSON* pro úpravy existují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="21541-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="21541-159">Dvakrát klikněte na soubor *Libman. JSON* v **Průzkumník řešení**.</span><span class="sxs-lookup"><span data-stu-id="21541-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="21541-160">V **Průzkumník řešení** klikněte pravým tlačítkem na projekt a vyberte **Spravovat knihovny na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="21541-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="21541-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="21541-161">**&#8224;**</span></span>
* <span data-ttu-id="21541-162">V nabídce **projektu** Visual Studio vyberte **Spravovat knihovny na straně klienta** .</span><span class="sxs-lookup"><span data-stu-id="21541-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="21541-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="21541-163">**&#8224;**</span></span>

<span data-ttu-id="21541-164">**&#8224;** Pokud soubor *Libman. JSON* již v kořenu projektu neexistuje, bude vytvořen s obsahem šablony výchozí položky.</span><span class="sxs-lookup"><span data-stu-id="21541-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="21541-165">Visual Studio nabízí bohatou podporu úprav JSON, jako je například zabarvení, formátování, IntelliSense a ověřování schématu.</span><span class="sxs-lookup"><span data-stu-id="21541-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="21541-166">Schéma JSON manifestu LibMan se nachází na [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span><span class="sxs-lookup"><span data-stu-id="21541-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="21541-167">Pomocí následujícího souboru manifestu LibMan načte soubory podle konfigurace definované ve vlastnosti `libraries`.</span><span class="sxs-lookup"><span data-stu-id="21541-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="21541-168">Vysvětlení objektů literálů definovaných v rámci `libraries` následující:</span><span class="sxs-lookup"><span data-stu-id="21541-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="21541-169">Z poskytovatele CDNJS je načtena podmnožina [jQuery](https://jquery.com/) verze 3.3.1.</span><span class="sxs-lookup"><span data-stu-id="21541-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="21541-170">Podmnožina je definována ve vlastnosti `files`&mdash;*jQuery. min. js*, *jQuery. js*a *jQuery. min. map*.</span><span class="sxs-lookup"><span data-stu-id="21541-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="21541-171">Soubory jsou umístěny ve složce *wwwroot/lib/jQuery* projektu.</span><span class="sxs-lookup"><span data-stu-id="21541-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="21541-172">Celá část [bootstrap](https://getbootstrap.com/) verze 4.1.3 se načte a umístí do složky *wwwroot/lib/Bootstrap* .</span><span class="sxs-lookup"><span data-stu-id="21541-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="21541-173">Vlastnost `provider` literálu objektu Přepisuje hodnotu vlastnosti `defaultProvider`.</span><span class="sxs-lookup"><span data-stu-id="21541-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="21541-174">LibMan načte spouštěcí soubory od poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="21541-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="21541-175">Podmnožina [Lodash](https://lodash.com/) byla schválena tělem pro řízení v rámci organizace.</span><span class="sxs-lookup"><span data-stu-id="21541-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="21541-176">Soubory *lodash. js* a *lodash. js* jsou načteny z místního systému souborů v *C:\\Temp\\lodash\\* .</span><span class="sxs-lookup"><span data-stu-id="21541-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="21541-177">Soubory jsou zkopírovány do složky *wwwroot/lib/lodash* projektu.</span><span class="sxs-lookup"><span data-stu-id="21541-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="21541-178">LibMan podporuje pouze jednu verzi každé knihovny od každého poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="21541-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="21541-179">V souboru *Libman. JSON* se ověřování schématu nezdařilo, pokud obsahuje dvě knihovny se stejným názvem knihovny pro daného zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="21541-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="21541-180">Obnovit soubory knihovny</span><span class="sxs-lookup"><span data-stu-id="21541-180">Restore library files</span></span>

<span data-ttu-id="21541-181">Chcete-li obnovit soubory knihovny z aplikace Visual Studio, musí být v kořenovém adresáři projektu platný soubor *Libman. JSON* .</span><span class="sxs-lookup"><span data-stu-id="21541-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="21541-182">Obnovené soubory jsou umístěny v projektu v umístění určeném pro každou knihovnu.</span><span class="sxs-lookup"><span data-stu-id="21541-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="21541-183">Soubory knihovny lze obnovit v ASP.NET Core projektu dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="21541-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="21541-184">Obnovení souborů během sestavování</span><span class="sxs-lookup"><span data-stu-id="21541-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="21541-185">Ruční obnovení souborů</span><span class="sxs-lookup"><span data-stu-id="21541-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="21541-186">Obnovení souborů během sestavování</span><span class="sxs-lookup"><span data-stu-id="21541-186">Restore files during build</span></span>

<span data-ttu-id="21541-187">LibMan může obnovit definované soubory knihovny jako součást procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="21541-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="21541-188">Ve výchozím nastavení je chování funkce *obnovení při sestavení* zakázané.</span><span class="sxs-lookup"><span data-stu-id="21541-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="21541-189">Postup při povolení a testování chování při obnovení při sestavení:</span><span class="sxs-lookup"><span data-stu-id="21541-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="21541-190">V **Průzkumník řešení** klikněte pravým tlačítkem na *Libman. JSON* a v místní nabídce vyberte **Povolit obnovení knihoven na straně klienta v sestavení** .</span><span class="sxs-lookup"><span data-stu-id="21541-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="21541-191">Po zobrazení výzvy k instalaci balíčku NuGet klikněte na tlačítko **Ano** .</span><span class="sxs-lookup"><span data-stu-id="21541-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="21541-192">Do projektu se přidá balíček NuGet [Microsoft. Web. LibraryManager. Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) :</span><span class="sxs-lookup"><span data-stu-id="21541-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="21541-193">Sestavte projekt, abyste se ujistili, že dojde k obnovení souboru LibMan.</span><span class="sxs-lookup"><span data-stu-id="21541-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="21541-194">Balíček `Microsoft.Web.LibraryManager.Build` vloží cíl nástroje MSBuild, který spouští LibMan během operace sestavení projektu.</span><span class="sxs-lookup"><span data-stu-id="21541-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="21541-195">Přečtěte si kanál **sestavení** okna **výstup** pro protokol aktivit LibMan:</span><span class="sxs-lookup"><span data-stu-id="21541-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

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

<span data-ttu-id="21541-196">Když je chování funkce obnovení při sestavení povoleno, zobrazí se v místní nabídce *Libman. JSON* možnost **Zakázat obnovení knihoven na straně klienta při sestavení** .</span><span class="sxs-lookup"><span data-stu-id="21541-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="21541-197">Výběrem této možnosti odeberete `Microsoft.Web.LibraryManager.Build` odkaz na balíček ze souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="21541-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="21541-198">V důsledku toho se knihovny na straně klienta již v každém sestavení neobnovují.</span><span class="sxs-lookup"><span data-stu-id="21541-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="21541-199">Bez ohledu na nastavení obnovení při sestavení můžete kdykoli obnovit ručně z kontextové nabídky *Libman. JSON* .</span><span class="sxs-lookup"><span data-stu-id="21541-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="21541-200">Další informace najdete v tématu [Ruční obnovení souborů](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="21541-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="21541-201">Ruční obnovení souborů</span><span class="sxs-lookup"><span data-stu-id="21541-201">Restore files manually</span></span>

<span data-ttu-id="21541-202">Ruční obnovení souborů knihovny:</span><span class="sxs-lookup"><span data-stu-id="21541-202">To manually restore library files:</span></span>

* <span data-ttu-id="21541-203">Pro všechny projekty v řešení:</span><span class="sxs-lookup"><span data-stu-id="21541-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="21541-204">Klikněte pravým tlačítkem myši na název řešení v **Průzkumník řešení**.</span><span class="sxs-lookup"><span data-stu-id="21541-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="21541-205">Vyberte možnost **obnovit knihovny na straně klienta** .</span><span class="sxs-lookup"><span data-stu-id="21541-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="21541-206">Pro určitý projekt:</span><span class="sxs-lookup"><span data-stu-id="21541-206">For a specific project:</span></span>
  * <span data-ttu-id="21541-207">V **Průzkumník řešení**klikněte pravým tlačítkem na soubor *Libman. JSON* .</span><span class="sxs-lookup"><span data-stu-id="21541-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="21541-208">Vyberte možnost **obnovit knihovny na straně klienta** .</span><span class="sxs-lookup"><span data-stu-id="21541-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="21541-209">I když je spuštěná operace obnovení:</span><span class="sxs-lookup"><span data-stu-id="21541-209">While the restore operation is running:</span></span>

* <span data-ttu-id="21541-210">Ikona Centrum stavu úloh (TSC) na stavovém řádku sady Visual Studio bude animovaná a spustí se *operace obnovení*.</span><span class="sxs-lookup"><span data-stu-id="21541-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="21541-211">Po kliknutí na ikonu se zobrazí popis známých úloh na pozadí.</span><span class="sxs-lookup"><span data-stu-id="21541-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="21541-212">Zprávy se odešlou do stavového řádku a do informačního kanálu **Správce knihovny** v okně **výstup** .</span><span class="sxs-lookup"><span data-stu-id="21541-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="21541-213">Příklad:</span><span class="sxs-lookup"><span data-stu-id="21541-213">For example:</span></span>

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

## <a name="delete-library-files"></a><span data-ttu-id="21541-214">Odstranit soubory knihovny</span><span class="sxs-lookup"><span data-stu-id="21541-214">Delete library files</span></span>

<span data-ttu-id="21541-215">Chcete-li provést operaci *Vyčištění* , která odstraní soubory knihovny, které byly dříve obnoveny v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="21541-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="21541-216">V **Průzkumník řešení**klikněte pravým tlačítkem na soubor *Libman. JSON* .</span><span class="sxs-lookup"><span data-stu-id="21541-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="21541-217">Vyberte možnost **vyčistit knihovny na straně klienta** .</span><span class="sxs-lookup"><span data-stu-id="21541-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="21541-218">Aby nedocházelo k neúmyslnému odebrání souborů bez knihoven, operace čištění neodstraní celé adresáře.</span><span class="sxs-lookup"><span data-stu-id="21541-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="21541-219">Odstraní jenom soubory, které byly zahrnuté do předchozího obnovení.</span><span class="sxs-lookup"><span data-stu-id="21541-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="21541-220">I když je spuštěná operace čištění:</span><span class="sxs-lookup"><span data-stu-id="21541-220">While the clean operation is running:</span></span>

* <span data-ttu-id="21541-221">Ikona čítače TSC na stavovém řádku sady Visual Studio bude animovaná a spustí se *operace čtení klientských knihoven*.</span><span class="sxs-lookup"><span data-stu-id="21541-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="21541-222">Po kliknutí na ikonu se zobrazí popis známých úloh na pozadí.</span><span class="sxs-lookup"><span data-stu-id="21541-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="21541-223">Zprávy jsou odesílány do stavového řádku a kanálu **Správce knihovny** v okně **výstup** .</span><span class="sxs-lookup"><span data-stu-id="21541-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="21541-224">Příklad:</span><span class="sxs-lookup"><span data-stu-id="21541-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="21541-225">Operace čištění odstraní pouze soubory z projektu.</span><span class="sxs-lookup"><span data-stu-id="21541-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="21541-226">Soubory knihovny zůstávají v mezipaměti, aby bylo možné rychlejší načítání budoucích operací obnovení.</span><span class="sxs-lookup"><span data-stu-id="21541-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="21541-227">Chcete-li spravovat soubory knihovny uložené v mezipaměti místního počítače, použijte rozhraní příkazového [řádku LibMan](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="21541-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="21541-228">Odinstalace souborů knihovny</span><span class="sxs-lookup"><span data-stu-id="21541-228">Uninstall library files</span></span>

<span data-ttu-id="21541-229">Odinstalace souborů knihovny:</span><span class="sxs-lookup"><span data-stu-id="21541-229">To uninstall library files:</span></span>

* <span data-ttu-id="21541-230">Otevřete *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="21541-230">Open *libman.json*.</span></span>
* <span data-ttu-id="21541-231">Umístit blikající kurzor dovnitř odpovídajícího `libraries` literálu objektu.</span><span class="sxs-lookup"><span data-stu-id="21541-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="21541-232">Klikněte na ikonu žárovky, která se zobrazí na levém okraji, a vyberte **odinstalovat \<library_name > @\<library_version >** :</span><span class="sxs-lookup"><span data-stu-id="21541-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Možnost místní nabídky pro odinstalaci knihovny](_static/uninstall-menu-option.png)

<span data-ttu-id="21541-234">Alternativně můžete ručně upravit a uložit manifest LibMan (*LibMan. JSON*).</span><span class="sxs-lookup"><span data-stu-id="21541-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="21541-235">[Operace obnovení](#restore-library-files) se spustí při uložení souboru.</span><span class="sxs-lookup"><span data-stu-id="21541-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="21541-236">Soubory knihoven, které již nejsou definovány v souboru *Libman. JSON* , jsou odebrány z projektu.</span><span class="sxs-lookup"><span data-stu-id="21541-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="21541-237">Aktualizovat verzi knihovny</span><span class="sxs-lookup"><span data-stu-id="21541-237">Update library version</span></span>

<span data-ttu-id="21541-238">Vyhledání aktualizované verze knihovny:</span><span class="sxs-lookup"><span data-stu-id="21541-238">To check for an updated library version:</span></span>

* <span data-ttu-id="21541-239">Otevřete *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="21541-239">Open *libman.json*.</span></span>
* <span data-ttu-id="21541-240">Umístit blikající kurzor dovnitř odpovídajícího `libraries` literálu objektu.</span><span class="sxs-lookup"><span data-stu-id="21541-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="21541-241">Klikněte na ikonu žárovky, která se zobrazí na levém okraji.</span><span class="sxs-lookup"><span data-stu-id="21541-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="21541-242">Najeďte myší na **kontrolu aktualizací**.</span><span class="sxs-lookup"><span data-stu-id="21541-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="21541-243">LibMan vyhledá verzi knihovny novější než verze nainstalovaná.</span><span class="sxs-lookup"><span data-stu-id="21541-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="21541-244">Může dojít k následujícím výsledkům:</span><span class="sxs-lookup"><span data-stu-id="21541-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="21541-245">Pokud je už nainstalovaná nejnovější verze, nezobrazí se žádná zpráva o **nalezených aktualizacích** .</span><span class="sxs-lookup"><span data-stu-id="21541-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="21541-246">Pokud ještě není nainstalovaná, zobrazí se nejnovější stabilní verze.</span><span class="sxs-lookup"><span data-stu-id="21541-246">The latest stable version is displayed if not already installed.</span></span>

  ![Možnost místní nabídky vyhledat aktualizace](_static/update-menu-option.png)

* <span data-ttu-id="21541-248">Pokud je k dispozici předběžná verze novější než nainstalovaná verze, zobrazí se předběžná verze.</span><span class="sxs-lookup"><span data-stu-id="21541-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="21541-249">Pokud chcete downgradovat na starší verzi knihovny, ručně upravte soubor *Libman. JSON* .</span><span class="sxs-lookup"><span data-stu-id="21541-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="21541-250">Po uložení souboru se LibMan [operace obnovení](#restore-library-files):</span><span class="sxs-lookup"><span data-stu-id="21541-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="21541-251">Odstraní nadbytečné soubory z předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="21541-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="21541-252">Přidá nové a aktualizované soubory z nové verze.</span><span class="sxs-lookup"><span data-stu-id="21541-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="21541-253">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="21541-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="21541-254">Úložiště GitHub LibMan</span><span class="sxs-lookup"><span data-stu-id="21541-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
