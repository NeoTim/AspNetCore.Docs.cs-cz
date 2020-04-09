---
title: Použití LibMan a ASP.NET jádra v sadě Visual Studio
author: scottaddie
description: Naučte se používat LibMan v projektu ASP.NET Core s Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658310"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="81eea-103">Použití LibMan a ASP.NET jádra v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81eea-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="81eea-104">Podle [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="81eea-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="81eea-105">Visual Studio má integrovanou podporu [libmana](xref:client-side/libman/index) v projektech ASP.NET Core, včetně:</span><span class="sxs-lookup"><span data-stu-id="81eea-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="81eea-106">Podpora konfigurace a spuštění operací obnovení LibMan na sestavení.</span><span class="sxs-lookup"><span data-stu-id="81eea-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="81eea-107">Položky nabídky pro spuštění obnovení LibMan a čištění operací.</span><span class="sxs-lookup"><span data-stu-id="81eea-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="81eea-108">Dialogové okno hledání pro hledání knihoven a přidávání souborů do projektu.</span><span class="sxs-lookup"><span data-stu-id="81eea-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="81eea-109">Podpora úprav pro soubor manifestu *Libman.json*&mdash;LibMan.</span><span class="sxs-lookup"><span data-stu-id="81eea-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="81eea-110">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="81eea-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="81eea-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="81eea-111">Prerequisites</span></span>

* <span data-ttu-id="81eea-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **ASP.NET a zatížením vývoje webu**</span><span class="sxs-lookup"><span data-stu-id="81eea-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="81eea-113">Přidání souborů knihovny</span><span class="sxs-lookup"><span data-stu-id="81eea-113">Add library files</span></span>

<span data-ttu-id="81eea-114">Soubory knihovny lze do projektu ASP.NET Core přidat dvěma různými způsoby:</span><span class="sxs-lookup"><span data-stu-id="81eea-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="81eea-115">Použití dialogového okna Přidat knihovnu na straně klienta</span><span class="sxs-lookup"><span data-stu-id="81eea-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="81eea-116">Ruční konfigurace položek souboru manifestu LibMan</span><span class="sxs-lookup"><span data-stu-id="81eea-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="81eea-117">Použití dialogového okna Přidat knihovnu na straně klienta</span><span class="sxs-lookup"><span data-stu-id="81eea-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="81eea-118">Chcete-li nainstalovat knihovnu na straně klienta, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="81eea-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="81eea-119">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na složku projektu, do které mají být soubory přidány.</span><span class="sxs-lookup"><span data-stu-id="81eea-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="81eea-120">Zvolte **Přidat** > **knihovnu na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="81eea-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="81eea-121">Zobrazí se dialogové okno **Přidat knihovnu na straně klienta:**</span><span class="sxs-lookup"><span data-stu-id="81eea-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Dialogové okno Přidat knihovnu na straně klienta](_static/add-library-dialog.png)

* <span data-ttu-id="81eea-123">V rozevíracím souboru Zprostředkovatel vyberte **zprostředkovatele.**</span><span class="sxs-lookup"><span data-stu-id="81eea-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="81eea-124">CDNJS je výchozím zprostředkovatelem.</span><span class="sxs-lookup"><span data-stu-id="81eea-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="81eea-125">Do textového pole **Knihovna** zadejte název knihovny, který chcete načíst.</span><span class="sxs-lookup"><span data-stu-id="81eea-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="81eea-126">Technologie IntelliSense poskytuje seznam knihoven začínajících na poskytnutý text.</span><span class="sxs-lookup"><span data-stu-id="81eea-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="81eea-127">Vyberte knihovnu ze seznamu IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="81eea-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="81eea-128">Všimněte si, že název knihovny je opatřen `@` symbolem a nejnovější stabilní verzí známou vybranému zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="81eea-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="81eea-129">Rozhodněte, které soubory chcete zahrnout:</span><span class="sxs-lookup"><span data-stu-id="81eea-129">Decide which files to include:</span></span>
  * <span data-ttu-id="81eea-130">Vyberte **přepínací tlačítko Zahrnout všechny soubory knihovny,** chcete-li zahrnout všechny soubory knihovny.</span><span class="sxs-lookup"><span data-stu-id="81eea-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="81eea-131">Vyberte přepínač **Zvolit konkrétní soubory,** chcete-li zahrnout podmnožinu souborů knihovny.</span><span class="sxs-lookup"><span data-stu-id="81eea-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="81eea-132">Je-li vybráno přepínací tlačítko, je povolen strom pro výběr souborů.</span><span class="sxs-lookup"><span data-stu-id="81eea-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="81eea-133">Zaškrtněte políčka nalevo od názvů souborů, které chcete stáhnout.</span><span class="sxs-lookup"><span data-stu-id="81eea-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="81eea-134">Určete složku projektu pro ukládání souborů do textového pole **Cílové umístění.**</span><span class="sxs-lookup"><span data-stu-id="81eea-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="81eea-135">Jako doporučení uložte každou knihovnu do samostatné složky.</span><span class="sxs-lookup"><span data-stu-id="81eea-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="81eea-136">Navrhovaná složka **Cílová poloha** je založena na umístění, ze kterého bylo dialogové okno spuštěno:</span><span class="sxs-lookup"><span data-stu-id="81eea-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="81eea-137">Pokud je spuštěn z kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="81eea-137">If launched from the project root:</span></span>
    * <span data-ttu-id="81eea-138">*wwwroot/lib* se používá, pokud *wwwroot* existuje.</span><span class="sxs-lookup"><span data-stu-id="81eea-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="81eea-139">*lib* se používá, pokud *wwwroot* neexistuje.</span><span class="sxs-lookup"><span data-stu-id="81eea-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="81eea-140">Pokud je spuštěn ze složky projektu, bude použit odpovídající název složky.</span><span class="sxs-lookup"><span data-stu-id="81eea-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="81eea-141">Návrh složky je opatřen názvem knihovny.</span><span class="sxs-lookup"><span data-stu-id="81eea-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="81eea-142">Následující tabulka ilustruje návrhy složek při instalaci jQuery v projektu Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="81eea-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="81eea-143">Místo spuštění</span><span class="sxs-lookup"><span data-stu-id="81eea-143">Launch location</span></span>                           |<span data-ttu-id="81eea-144">Navrhovaná složka</span><span class="sxs-lookup"><span data-stu-id="81eea-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="81eea-145">kořen projektu (pokud *existuje wwwroot)*</span><span class="sxs-lookup"><span data-stu-id="81eea-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="81eea-146">*wwwroot/lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="81eea-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="81eea-147">kořen projektu (pokud *wwwroot* neexistuje)</span><span class="sxs-lookup"><span data-stu-id="81eea-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="81eea-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="81eea-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="81eea-149">*Složka Stránky* v projektu</span><span class="sxs-lookup"><span data-stu-id="81eea-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="81eea-150">*Stránky/jquery/*</span><span class="sxs-lookup"><span data-stu-id="81eea-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="81eea-151">Klepnutím na tlačítko **Instalovat** stáhnete soubory podle konfigurace v *souboru libman.json*.</span><span class="sxs-lookup"><span data-stu-id="81eea-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="81eea-152">Podrobnosti o instalaci naleznete v informačním kanálu **Správce knihovny** v okně **Výstup.**</span><span class="sxs-lookup"><span data-stu-id="81eea-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="81eea-153">Příklad:</span><span class="sxs-lookup"><span data-stu-id="81eea-153">For example:</span></span>

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

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="81eea-154">Ruční konfigurace položek souboru manifestu LibMan</span><span class="sxs-lookup"><span data-stu-id="81eea-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="81eea-155">Všechny operace LibMan v sadě Visual Studio jsou založeny na obsahu manifestu LibMan kořenového projektu (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="81eea-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="81eea-156">Chcete-li nakonfigurovat soubory knihovny pro projekt, můžete ručně upravit *soubor libman.json.*</span><span class="sxs-lookup"><span data-stu-id="81eea-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="81eea-157">Visual Studio obnoví všechny soubory knihovny po uložení *souboru libman.json.*</span><span class="sxs-lookup"><span data-stu-id="81eea-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="81eea-158">Chcete-li otevřít *soubor libman.json* pro úpravy, existují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="81eea-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="81eea-159">Poklepejte na soubor *libman.json* v **Průzkumníku řešení**.</span><span class="sxs-lookup"><span data-stu-id="81eea-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="81eea-160">Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **spravovat knihovny na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="81eea-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="81eea-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="81eea-161">**&#8224;**</span></span>
* <span data-ttu-id="81eea-162">Z nabídky **Aplikace Visual** Studio Project vyberte **Spravovat knihovny na straně klienta.**</span><span class="sxs-lookup"><span data-stu-id="81eea-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="81eea-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="81eea-163">**&#8224;**</span></span>

<span data-ttu-id="81eea-164">**&#8224;** Pokud soubor *libman.json* ještě v kořenovém adresáři projektu neexistuje, bude vytvořen s výchozím obsahem šablony položky.</span><span class="sxs-lookup"><span data-stu-id="81eea-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="81eea-165">Visual Studio nabízí bohatou podporu úprav JSON, jako je vybarvení, formátování, technologie IntelliSense a ověřování schématu.</span><span class="sxs-lookup"><span data-stu-id="81eea-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="81eea-166">Schéma JSON manifestu LibMan se nachází [https://json.schemastore.org/libman](https://json.schemastore.org/libman)na adrese .</span><span class="sxs-lookup"><span data-stu-id="81eea-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="81eea-167">S následujícím souborem manifestu LibMan načte soubory `libraries` podle konfigurace definované ve vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="81eea-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="81eea-168">Vysvětlení literály objektu definované `libraries` v jenásledující:</span><span class="sxs-lookup"><span data-stu-id="81eea-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="81eea-169">Podmnožina [jQuery](https://jquery.com/) verze 3.3.1 je načtena od zprostředkovatele CDNJS.</span><span class="sxs-lookup"><span data-stu-id="81eea-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="81eea-170">Podmnožina je `files` definována ve vlastnostech&mdash;*jquery.min.js*, *jquery.js*a *jquery.min.map*.</span><span class="sxs-lookup"><span data-stu-id="81eea-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="81eea-171">Soubory jsou umístěny ve složce *wwwroot/lib/jquery* projektu.</span><span class="sxs-lookup"><span data-stu-id="81eea-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="81eea-172">Celá [Bootstrap](https://getbootstrap.com/) verze 4.1.3 je načtena a umístěna do složky *wwwroot/ lib / bootstrap.*</span><span class="sxs-lookup"><span data-stu-id="81eea-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="81eea-173">`provider` Vlastnost literálu objektu přepíše hodnotu vlastnosti. `defaultProvider`</span><span class="sxs-lookup"><span data-stu-id="81eea-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="81eea-174">LibMan načte soubory Bootstrap od poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="81eea-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="81eea-175">Podmnožina [Lodash](https://lodash.com/) byla schválena řídícím orgánem v rámci organizace.</span><span class="sxs-lookup"><span data-stu-id="81eea-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="81eea-176">*Lodash.js* a *lodash.min.js* soubory jsou načteny z místního systému souborů na *C:\\temp\\lodash\\*.</span><span class="sxs-lookup"><span data-stu-id="81eea-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="81eea-177">Soubory jsou zkopírovány do složky *wwwroot/lib/lodash* projektu.</span><span class="sxs-lookup"><span data-stu-id="81eea-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="81eea-178">LibMan podporuje pouze jednu verzi každé knihovny od každého poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="81eea-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="81eea-179">Soubor *libman.json* se nezdaří ověření schématu, pokud obsahuje dvě knihovny se stejným názvem knihovny pro daného zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="81eea-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="81eea-180">Obnovení souborů knihovny</span><span class="sxs-lookup"><span data-stu-id="81eea-180">Restore library files</span></span>

<span data-ttu-id="81eea-181">Chcete-li obnovit soubory knihovny z aplikace Visual Studio, musí být platný soubor *libman.json* v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="81eea-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="81eea-182">Obnovené soubory jsou umístěny v projektu v umístění určeném pro každou knihovnu.</span><span class="sxs-lookup"><span data-stu-id="81eea-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="81eea-183">Soubory knihovny lze obnovit v projektu ASP.NET Core dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="81eea-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="81eea-184">Obnovení souborů během sestavení</span><span class="sxs-lookup"><span data-stu-id="81eea-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="81eea-185">Ruční obnovení souborů</span><span class="sxs-lookup"><span data-stu-id="81eea-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="81eea-186">Obnovení souborů během sestavení</span><span class="sxs-lookup"><span data-stu-id="81eea-186">Restore files during build</span></span>

<span data-ttu-id="81eea-187">LibMan můžete obnovit definované soubory knihovny jako součást procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="81eea-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="81eea-188">Ve výchozím nastavení je zakázáno chování *obnovení na sestavení.*</span><span class="sxs-lookup"><span data-stu-id="81eea-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="81eea-189">Povolení a testování chování obnovení na sestavení:</span><span class="sxs-lookup"><span data-stu-id="81eea-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="81eea-190">Klepněte pravým tlačítkem myši na *soubor libman.json* v **Průzkumníku řešení** a z kontextové nabídky vyberte **Povolit obnovení knihoven na straně klienta v buildu.**</span><span class="sxs-lookup"><span data-stu-id="81eea-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="81eea-191">Po zobrazení výzvy k instalaci balíčku NuGet klepněte na tlačítko **Ano.**</span><span class="sxs-lookup"><span data-stu-id="81eea-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="81eea-192">Balíček [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet je přidán do projektu:</span><span class="sxs-lookup"><span data-stu-id="81eea-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="81eea-193">Vytvořte projekt pro potvrzení obnovení souboru LibMan.</span><span class="sxs-lookup"><span data-stu-id="81eea-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="81eea-194">Balíček `Microsoft.Web.LibraryManager.Build` vloží Cíl MSBuild, který spouští LibMan během operace sestavení projektu.</span><span class="sxs-lookup"><span data-stu-id="81eea-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="81eea-195">Projděte **si informační** kanál sestavení **výstupního** okna pro protokol aktivit LibMan:</span><span class="sxs-lookup"><span data-stu-id="81eea-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

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

<span data-ttu-id="81eea-196">Pokud je povoleno chování obnovení na sestavení, kontextová nabídka *libman.json* zobrazí možnost **Zakázat obnovení knihovny na straně klienta v sestavení.**</span><span class="sxs-lookup"><span data-stu-id="81eea-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="81eea-197">Výběrem této možnosti `Microsoft.Web.LibraryManager.Build` odeberete odkaz na balíček ze souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="81eea-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="81eea-198">V důsledku toho knihovny na straně klienta již nejsou obnoveny v každém sestavení.</span><span class="sxs-lookup"><span data-stu-id="81eea-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="81eea-199">Bez ohledu na nastavení obnovení na sestavení můžete kdykoli ručně obnovit z kontextové nabídky *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="81eea-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="81eea-200">Další informace naleznete [v tématu Ruční obnovení souborů](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="81eea-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="81eea-201">Ruční obnovení souborů</span><span class="sxs-lookup"><span data-stu-id="81eea-201">Restore files manually</span></span>

<span data-ttu-id="81eea-202">Ruční obnovení souborů knihovny:</span><span class="sxs-lookup"><span data-stu-id="81eea-202">To manually restore library files:</span></span>

* <span data-ttu-id="81eea-203">Pro všechny projekty v řešení:</span><span class="sxs-lookup"><span data-stu-id="81eea-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="81eea-204">Klepněte pravým tlačítkem myši na název řešení v **Průzkumníku řešení**.</span><span class="sxs-lookup"><span data-stu-id="81eea-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="81eea-205">Vyberte možnost **Obnovit knihovny na straně klienta.**</span><span class="sxs-lookup"><span data-stu-id="81eea-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="81eea-206">Pro konkrétní projekt:</span><span class="sxs-lookup"><span data-stu-id="81eea-206">For a specific project:</span></span>
  * <span data-ttu-id="81eea-207">Klepněte pravým tlačítkem myši na soubor *libman.json* v **Průzkumníku řešení**.</span><span class="sxs-lookup"><span data-stu-id="81eea-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="81eea-208">Vyberte možnost **Obnovit knihovny na straně klienta.**</span><span class="sxs-lookup"><span data-stu-id="81eea-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="81eea-209">Během operace obnovení je spuštěna:</span><span class="sxs-lookup"><span data-stu-id="81eea-209">While the restore operation is running:</span></span>

* <span data-ttu-id="81eea-210">Ikona Centrum stavu úloh (TSC) na stavovém řádku sady Visual Studio bude animovaná a bude číst *zahájení operace obnovení*.</span><span class="sxs-lookup"><span data-stu-id="81eea-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="81eea-211">Kliknutím na ikonu se otevře popisek se známými úkoly na pozadí.</span><span class="sxs-lookup"><span data-stu-id="81eea-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="81eea-212">Zprávy budou odeslány na stavový řádek a informační kanál **Správce knihovny** v okně **Výstup.**</span><span class="sxs-lookup"><span data-stu-id="81eea-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="81eea-213">Příklad:</span><span class="sxs-lookup"><span data-stu-id="81eea-213">For example:</span></span>

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

## <a name="delete-library-files"></a><span data-ttu-id="81eea-214">Odstranění souborů knihovny</span><span class="sxs-lookup"><span data-stu-id="81eea-214">Delete library files</span></span>

<span data-ttu-id="81eea-215">Chcete-li provést *čistou* operaci, která odstraní soubory knihovny dříve obnovené v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="81eea-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="81eea-216">Klepněte pravým tlačítkem myši na soubor *libman.json* v **Průzkumníku řešení**.</span><span class="sxs-lookup"><span data-stu-id="81eea-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="81eea-217">Vyberte možnost **Vyčistit knihovny na straně klienta.**</span><span class="sxs-lookup"><span data-stu-id="81eea-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="81eea-218">Aby se zabránilo nechtěnému odebrání souborů, které nejsou součástí knihovny, čistá operace neodstraní celé adresáře.</span><span class="sxs-lookup"><span data-stu-id="81eea-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="81eea-219">Odebere pouze soubory, které byly zahrnuty v předchozím obnovení.</span><span class="sxs-lookup"><span data-stu-id="81eea-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="81eea-220">Během čisté operace je spuštěna:</span><span class="sxs-lookup"><span data-stu-id="81eea-220">While the clean operation is running:</span></span>

* <span data-ttu-id="81eea-221">Ikona TSC na stavovém řádku sady Visual Studio bude animovaná a bude číst *spuštění operace klientských knihoven*.</span><span class="sxs-lookup"><span data-stu-id="81eea-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="81eea-222">Kliknutím na ikonu se otevře popisek se známými úkoly na pozadí.</span><span class="sxs-lookup"><span data-stu-id="81eea-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="81eea-223">Zprávy jsou odesílány na stavový řádek a informační kanál **Správce knihovny** v okně **Výstup.**</span><span class="sxs-lookup"><span data-stu-id="81eea-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="81eea-224">Příklad:</span><span class="sxs-lookup"><span data-stu-id="81eea-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="81eea-225">Čistá operace pouze odstraní soubory z projektu.</span><span class="sxs-lookup"><span data-stu-id="81eea-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="81eea-226">Soubory knihovny zůstávají v mezipaměti pro rychlejší načítání budoucích operací obnovení.</span><span class="sxs-lookup"><span data-stu-id="81eea-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="81eea-227">Chcete-li spravovat soubory knihovny uložené v mezipaměti místního počítače, použijte [cli LibMan](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="81eea-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="81eea-228">Odinstalace souborů knihovny</span><span class="sxs-lookup"><span data-stu-id="81eea-228">Uninstall library files</span></span>

<span data-ttu-id="81eea-229">Jak odinstalovat soubory knihovny:</span><span class="sxs-lookup"><span data-stu-id="81eea-229">To uninstall library files:</span></span>

* <span data-ttu-id="81eea-230">Otevřete *soubor libman.json*.</span><span class="sxs-lookup"><span data-stu-id="81eea-230">Open *libman.json*.</span></span>
* <span data-ttu-id="81eea-231">Umístěte stříšku `libraries` uvnitř odpovídajícího literálu objektu.</span><span class="sxs-lookup"><span data-stu-id="81eea-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="81eea-232">Klikněte na ikonu žárovky, která se zobrazí na levém okraji, a vyberte **Odinstalovat \<library_name>@\<library_version>**:</span><span class="sxs-lookup"><span data-stu-id="81eea-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Možnost odinstalace kontextové nabídky knihovny](_static/uninstall-menu-option.png)

<span data-ttu-id="81eea-234">Případně můžete ručně upravit a uložit manifest LibMan *(libman.json).*</span><span class="sxs-lookup"><span data-stu-id="81eea-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="81eea-235">[Operace obnovení](#restore-library-files) se spustí při uložení souboru.</span><span class="sxs-lookup"><span data-stu-id="81eea-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="81eea-236">Soubory knihovny, které již nejsou definovány v *souboru libman.json,* budou z projektu odebrány.</span><span class="sxs-lookup"><span data-stu-id="81eea-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="81eea-237">Aktualizovat verzi knihovny</span><span class="sxs-lookup"><span data-stu-id="81eea-237">Update library version</span></span>

<span data-ttu-id="81eea-238">Kontrola aktualizované verze knihovny:</span><span class="sxs-lookup"><span data-stu-id="81eea-238">To check for an updated library version:</span></span>

* <span data-ttu-id="81eea-239">Otevřete *soubor libman.json*.</span><span class="sxs-lookup"><span data-stu-id="81eea-239">Open *libman.json*.</span></span>
* <span data-ttu-id="81eea-240">Umístěte stříšku `libraries` uvnitř odpovídajícího literálu objektu.</span><span class="sxs-lookup"><span data-stu-id="81eea-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="81eea-241">Klikněte na ikonu žárovky, která se zobrazí na levém okraji.</span><span class="sxs-lookup"><span data-stu-id="81eea-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="81eea-242">Najeďte **na: Vyhledat aktualizace**.</span><span class="sxs-lookup"><span data-stu-id="81eea-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="81eea-243">LibMan zkontroluje novější verzi knihovny, než je nainstalovaná verze.</span><span class="sxs-lookup"><span data-stu-id="81eea-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="81eea-244">Mohou nastat následující výsledky:</span><span class="sxs-lookup"><span data-stu-id="81eea-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="81eea-245">Pokud je již nainstalována nejnovější verze, zobrazí se zpráva **Bez nalezených aktualizací.**</span><span class="sxs-lookup"><span data-stu-id="81eea-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="81eea-246">Pokud již není nainstalována, zobrazí se nejnovější stabilní verze.</span><span class="sxs-lookup"><span data-stu-id="81eea-246">The latest stable version is displayed if not already installed.</span></span>

  ![Vyhledat možnost kontextové nabídky aktualizací](_static/update-menu-option.png)

* <span data-ttu-id="81eea-248">Pokud je k dispozici novější verze, než je nainstalovaná verze, zobrazí se předběžná verze.</span><span class="sxs-lookup"><span data-stu-id="81eea-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="81eea-249">Chcete-li přejít na starší verzi knihovny, ručně upravte soubor *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="81eea-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="81eea-250">Po uložení souboru se [operace obnovení](#restore-library-files)LibMan :</span><span class="sxs-lookup"><span data-stu-id="81eea-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="81eea-251">Odebere redundantní soubory z předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="81eea-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="81eea-252">Přidá nové a aktualizované soubory z nové verze.</span><span class="sxs-lookup"><span data-stu-id="81eea-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="81eea-253">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="81eea-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="81eea-254">Úložiště LibMan GitHub</span><span class="sxs-lookup"><span data-stu-id="81eea-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
