---
title: Správa balíčků na straně klienta nástrojem Bower v ASP.NET Core
author: rick-anderson
description: Správa balíčků na straně klienta nástrojem Bower.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 08/09/2018
uid: client-side/bower
ms.openlocfilehash: 1292b75bdfe1cea56d9c8bc282b0dd90fc945a33
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813533"
---
# <a name="manage-client-side-packages-with-bower-in-aspnet-core"></a><span data-ttu-id="1ab53-103">Správa balíčků na straně klienta nástrojem Bower v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1ab53-103">Manage client-side packages with Bower in ASP.NET Core</span></span>

<span data-ttu-id="1ab53-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Noel rýže](https://twitter.com/noelrice1), a [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="1ab53-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Noel Rice](https://twitter.com/noelrice1), and [Scott Addie](https://scottaddie.com)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1ab53-105">Zatímco Bower byla zachována jeho programu doporučujeme používat jiné řešení.</span><span class="sxs-lookup"><span data-stu-id="1ab53-105">While Bower is maintained, its maintainers recommend using a different solution.</span></span> <span data-ttu-id="1ab53-106">[Správce knihovny](https://blogs.msdn.microsoft.com/webdev/2018/04/18/what-happened-to-bower/) (LibMan zkráceně) je nástroj pro získání nové knihoven na straně klienta ze sady Visual Studio (Visual Studio 15,8 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="1ab53-106">[Library Manager](https://blogs.msdn.microsoft.com/webdev/2018/04/18/what-happened-to-bower/) (LibMan for short) is Visual Studio's new client-side library acquisition tool (Visual Studio 15.8 or later).</span></span> <span data-ttu-id="1ab53-107">Další informace naleznete v tématu <xref:client-side/libman/index>.</span><span class="sxs-lookup"><span data-stu-id="1ab53-107">For more information, see <xref:client-side/libman/index>.</span></span> <span data-ttu-id="1ab53-108">Bower je podporováno v sadě Visual Studio přes verzi 15.5.</span><span class="sxs-lookup"><span data-stu-id="1ab53-108">Bower is supported in Visual Studio through version 15.5.</span></span>
>
> <span data-ttu-id="1ab53-109">Yarn s Webpacku je jeden oblíbenou alternativu, pro kterou [pokyny k migraci](https://bower.io/blog/2017/how-to-migrate-away-from-bower/) jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="1ab53-109">Yarn with Webpack is one popular alternative for which [migration instructions](https://bower.io/blog/2017/how-to-migrate-away-from-bower/) are available.</span></span>

<span data-ttu-id="1ab53-110">[Bower](https://bower.io/) zavolá sama sebe "Správce balíčků pro web".</span><span class="sxs-lookup"><span data-stu-id="1ab53-110">[Bower](https://bower.io/) calls itself "A package manager for the web".</span></span> <span data-ttu-id="1ab53-111">Vyplní void zanechaný neschopnost Nugetu doručování statických souborů obsahu v ekosystému .NET.</span><span class="sxs-lookup"><span data-stu-id="1ab53-111">Within the .NET ecosystem, it fills the void left by NuGet's inability to deliver static content files.</span></span> <span data-ttu-id="1ab53-112">Pro projekty ASP.NET Core, jsou tyto statické soubory přináší knihoven na straně klienta, jako je [jQuery](https://jquery.com/) a [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="1ab53-112">For ASP.NET Core projects, these static files are inherent to client-side libraries like [jQuery](https://jquery.com/) and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="1ab53-113">Pro knihovny .NET, můžete dál používat [NuGet](https://www.nuget.org/) Správce balíčků.</span><span class="sxs-lookup"><span data-stu-id="1ab53-113">For .NET libraries, you still use [NuGet](https://www.nuget.org/) package manager.</span></span>

<span data-ttu-id="1ab53-114">Proces sestavení nové projekty vytvořené pomocí šablon projektů ASP.NET Core, nastavení na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="1ab53-114">New projects created with the ASP.NET Core project templates set up the client-side build process.</span></span> <span data-ttu-id="1ab53-115">[jQuery](https://jquery.com/) a [Bootstrap](https://getbootstrap.com/) jsou nainstalované a podporované Bower.</span><span class="sxs-lookup"><span data-stu-id="1ab53-115">[jQuery](https://jquery.com/) and [Bootstrap](https://getbootstrap.com/) are installed, and Bower is supported.</span></span>

<span data-ttu-id="1ab53-116">Balíčky na straně klienta jsou uvedené v *bower.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="1ab53-116">Client-side packages are listed in the *bower.json* file.</span></span> <span data-ttu-id="1ab53-117">Šablony projektů ASP.NET Core nakonfiguruje *bower.json* s Bootstrap, jQuery a k ověřování jQuery.</span><span class="sxs-lookup"><span data-stu-id="1ab53-117">The ASP.NET Core project templates configures *bower.json* with jQuery, jQuery validation, and Bootstrap.</span></span>

<span data-ttu-id="1ab53-118">V tomto kurzu přidáme podporu [knihovnou aplikací Font Awesome](http://fontawesome.io).</span><span class="sxs-lookup"><span data-stu-id="1ab53-118">In this tutorial, we'll add support for [Font Awesome](http://fontawesome.io).</span></span> <span data-ttu-id="1ab53-119">Dá se nainstalovat balíčky bower s **spravovat balíčky Bower** uživatelského rozhraní nebo ručně v *bower.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="1ab53-119">Bower packages can be installed with the **Manage Bower Packages** UI or manually in the *bower.json* file.</span></span>

### <a name="installation-via-manage-bower-packages-ui"></a><span data-ttu-id="1ab53-120">Instalace přes spravovat balíčky Bower uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="1ab53-120">Installation via Manage Bower Packages UI</span></span>

* <span data-ttu-id="1ab53-121">Vytvoření nové aplikace ASP.NET Core Web s **webová aplikace ASP.NET Core (.NET Core)** šablony.</span><span class="sxs-lookup"><span data-stu-id="1ab53-121">Create a new ASP.NET Core Web app with the **ASP.NET Core Web Application (.NET Core)** template.</span></span> <span data-ttu-id="1ab53-122">Vyberte **webovou aplikaci** a **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="1ab53-122">Select **Web Application** and **No Authentication**.</span></span>

* <span data-ttu-id="1ab53-123">Klikněte pravým tlačítkem na projekt v Průzkumníku řešení a vyberte **spravovat balíčky Bower** (případně v hlavní nabídce **projektu** > **spravovat balíčky Bower**).</span><span class="sxs-lookup"><span data-stu-id="1ab53-123">Right-click the project in Solution Explorer and select **Manage Bower Packages** (alternatively from the main menu, **Project** > **Manage Bower Packages**).</span></span>

* <span data-ttu-id="1ab53-124">V **Bower: \<název projektu\>**  okna, klikněte na kartu "Procházet" a vyfiltrujte seznam balíčků tak, že zadáte `font-awesome` do vyhledávacího pole:</span><span class="sxs-lookup"><span data-stu-id="1ab53-124">In the **Bower: \<project name\>** window, click the "Browse" tab, and then filter the packages list by entering `font-awesome` in the search box:</span></span>

  ![spravovat balíčky bower](bower/_static/manage-bower-packages.png)

* <span data-ttu-id="1ab53-126">Ujistěte se, že "uložit změny do *bower.json*" zaškrtávací políčko zaškrtnuto.</span><span class="sxs-lookup"><span data-stu-id="1ab53-126">Confirm that the "Save changes to *bower.json*" check box is checked.</span></span> <span data-ttu-id="1ab53-127">Z rozevíracího seznamu vyberte verzi a klikněte na tlačítko **nainstalovat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="1ab53-127">Select a version from the drop-down list and click the **Install** button.</span></span> <span data-ttu-id="1ab53-128">**Výstup** okno zobrazuje podrobné informace o instalaci.</span><span class="sxs-lookup"><span data-stu-id="1ab53-128">The **Output** window shows the installation details.</span></span>

### <a name="manual-installation-in-bowerjson"></a><span data-ttu-id="1ab53-129">Ruční instalace v bower.json</span><span class="sxs-lookup"><span data-stu-id="1ab53-129">Manual installation in bower.json</span></span>

<span data-ttu-id="1ab53-130">Otevřít *bower.json* a přidejte k závislostem "knihovnou aplikací font awesome".</span><span class="sxs-lookup"><span data-stu-id="1ab53-130">Open the *bower.json* file and add "font-awesome" to the dependencies.</span></span> <span data-ttu-id="1ab53-131">Technologie IntelliSense zobrazuje dostupné balíčky.</span><span class="sxs-lookup"><span data-stu-id="1ab53-131">IntelliSense shows the available packages.</span></span> <span data-ttu-id="1ab53-132">Když je vybraný balíček, se zobrazí dostupné verze.</span><span class="sxs-lookup"><span data-stu-id="1ab53-132">When a package is selected, the available versions are displayed.</span></span> <span data-ttu-id="1ab53-133">Následující obrázky jsou starší a nebudou shodovat.</span><span class="sxs-lookup"><span data-stu-id="1ab53-133">The images below are older and won't match what you see.</span></span>

![IntelliSense Průzkumníku balíčků bower](bower/_static/add-package.png)

![verze technologie IntelliSense pro bower](bower/_static/version-intelliSense.png)

<span data-ttu-id="1ab53-136">Bower používá [sémantické správy verzí](https://semver.org/) k uspořádání závislosti.</span><span class="sxs-lookup"><span data-stu-id="1ab53-136">Bower uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="1ab53-137">Sémantické správy verzí, označované také jako SemVer identifikuje balíčky se schéma číslování \<hlavní >.\< podverze >. \<opravy >.</span><span class="sxs-lookup"><span data-stu-id="1ab53-137">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="1ab53-138">Technologie IntelliSense zjednodušuje tím, že zobrazuje pouze několik běžné volby sémantické správy verzí.</span><span class="sxs-lookup"><span data-stu-id="1ab53-138">IntelliSense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="1ab53-139">Horní položku v seznamu technologie IntelliSense (4.6.3 v příkladu výše) se považuje za nejnovější stabilní verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="1ab53-139">The top item in the IntelliSense list (4.6.3 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="1ab53-140">Symbol stříšky (^) odpovídá nejnovější hlavní verzi a tilda (~) odpovídá nejnovější dílčí verzi.</span><span class="sxs-lookup"><span data-stu-id="1ab53-140">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span>

<span data-ttu-id="1ab53-141">Uložit *bower.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="1ab53-141">Save the *bower.json* file.</span></span> <span data-ttu-id="1ab53-142">Visual Studio sleduje *bower.json* změny v souboru.</span><span class="sxs-lookup"><span data-stu-id="1ab53-142">Visual Studio watches the *bower.json* file for changes.</span></span> <span data-ttu-id="1ab53-143">Po uložení, *nainstalovat bower* spuštění příkazu.</span><span class="sxs-lookup"><span data-stu-id="1ab53-143">Upon saving, the *bower install* command is executed.</span></span> <span data-ttu-id="1ab53-144">Najdete v okně Výstup **Bower/npm** zobrazení pro přesný příkaz spustit.</span><span class="sxs-lookup"><span data-stu-id="1ab53-144">See the Output window's **Bower/npm** view for the exact command executed.</span></span>

<span data-ttu-id="1ab53-145">Otevřít *.bowerrc* soubor *bower.json*.</span><span class="sxs-lookup"><span data-stu-id="1ab53-145">Open the *.bowerrc* file under *bower.json*.</span></span> <span data-ttu-id="1ab53-146">`directory` Je nastavena na *wwwroot/lib* označující umístění Bower nainstaluje balíček prostředků.</span><span class="sxs-lookup"><span data-stu-id="1ab53-146">The `directory` property is set to *wwwroot/lib* which indicates the location Bower will install the package assets.</span></span>

```json
{
 "directory": "wwwroot/lib"
}
```

<span data-ttu-id="1ab53-147">Pole Hledat v Průzkumníku řešení můžete použít k vyhledání a knihovnou aplikací font awesome balíček zobrazíte.</span><span class="sxs-lookup"><span data-stu-id="1ab53-147">You can use the search box in Solution Explorer to find and display the font-awesome package.</span></span>

<span data-ttu-id="1ab53-148">Otevřít *Views\Shared\_Layout.cshtml* a knihovnou aplikací font awesome soubor CSS přidejte do prostředí [pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) pro `Development`.</span><span class="sxs-lookup"><span data-stu-id="1ab53-148">Open the *Views\Shared\_Layout.cshtml* file and add the font-awesome CSS file to the environment [Tag Helper](xref:mvc/views/tag-helpers/intro) for `Development`.</span></span> <span data-ttu-id="1ab53-149">Z Průzkumníka řešení, přetáhněte a umístěte *písmo awesome.css* uvnitř `<environment names="Development">` elementu.</span><span class="sxs-lookup"><span data-stu-id="1ab53-149">From Solution Explorer, drag and drop *font-awesome.css* inside the `<environment names="Development">` element.</span></span>

[!code-html[](bower/sample/_Layout.cshtml?highlight=4&range=9-13)]

<span data-ttu-id="1ab53-150">V produkční aplikaci přidat *písmo awesome.min.css* k pomocná rutina značky prostředí pro `Staging,Production`.</span><span class="sxs-lookup"><span data-stu-id="1ab53-150">In a production app you would add *font-awesome.min.css* to the environment tag helper for `Staging,Production`.</span></span>

<span data-ttu-id="1ab53-151">Nahraďte obsah *Views\Home\About.cshtml* Razor souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1ab53-151">Replace the contents of the *Views\Home\About.cshtml* Razor file with the following markup:</span></span>

[!code-html[](bower/sample/About.cshtml)]

<span data-ttu-id="1ab53-152">Spusťte aplikaci a přejděte do zobrazení o zkontrolujte, jestli funguje knihovnou aplikací font awesome balíčku.</span><span class="sxs-lookup"><span data-stu-id="1ab53-152">Run the app and navigate to the About view to verify the font-awesome package works.</span></span>

## <a name="exploring-the-client-side-build-process"></a><span data-ttu-id="1ab53-153">Zkoumání proces sestavení na straně klienta</span><span class="sxs-lookup"><span data-stu-id="1ab53-153">Exploring the client-side build process</span></span>

<span data-ttu-id="1ab53-154">Většina šablony projektů ASP.NET Core je již nakonfigurován pro použití Bower.</span><span class="sxs-lookup"><span data-stu-id="1ab53-154">Most ASP.NET Core project templates are already configured to use Bower.</span></span> <span data-ttu-id="1ab53-155">Tento návod další začíná prázdný projekt ASP.NET Core a přidá každého jednotlivého ručně, abyste získali představu pro použití Bower v projektu.</span><span class="sxs-lookup"><span data-stu-id="1ab53-155">This next walkthrough starts with an empty ASP.NET Core project and adds each piece manually, so you can get a feel for how Bower is used in a project.</span></span> <span data-ttu-id="1ab53-156">Zobrazí se, co se stane strukturu projektu a modul runtime výstup jak každé změně konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1ab53-156">You can see what happens to the project structure and the runtime output as each configuration change is made.</span></span>

<span data-ttu-id="1ab53-157">Obecné kroky pro použití s Bowerem proces sestavení na straně klienta jsou:</span><span class="sxs-lookup"><span data-stu-id="1ab53-157">The general steps to use the client-side build process with Bower are:</span></span>

* <span data-ttu-id="1ab53-158">Definujte balíčky používané ve vašem projektu.</span><span class="sxs-lookup"><span data-stu-id="1ab53-158">Define packages used in your project.</span></span> <!-- once defined, you don't need to download them, VS does -->
* <span data-ttu-id="1ab53-159">Referenční dokumentace k balíčkům z webových stránek.</span><span class="sxs-lookup"><span data-stu-id="1ab53-159">Reference packages from your web pages.</span></span>

### <a name="define-packages"></a><span data-ttu-id="1ab53-160">Definování balíčků</span><span class="sxs-lookup"><span data-stu-id="1ab53-160">Define packages</span></span>

<span data-ttu-id="1ab53-161">Jakmile se seznam balíčků v *bower.json* souboru, Visual Studio stáhne.</span><span class="sxs-lookup"><span data-stu-id="1ab53-161">Once you list packages in the *bower.json* file, Visual Studio will download them.</span></span> <span data-ttu-id="1ab53-162">Následující příklad používá k načtení jQuery a Bootstrap pro Bower *wwwroot* složky.</span><span class="sxs-lookup"><span data-stu-id="1ab53-162">The following example uses Bower to load jQuery and Bootstrap to the *wwwroot* folder.</span></span>

* <span data-ttu-id="1ab53-163">Vytvoření nové aplikace ASP.NET Core Web s **webová aplikace ASP.NET Core (.NET Core)** šablony.</span><span class="sxs-lookup"><span data-stu-id="1ab53-163">Create a new ASP.NET Core Web app with the **ASP.NET Core Web Application (.NET Core)** template.</span></span> <span data-ttu-id="1ab53-164">Vyberte **prázdný** šablony projektu a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="1ab53-164">Select the **Empty** project template and click **OK**.</span></span>

* <span data-ttu-id="1ab53-165">V Průzkumníku řešení klikněte pravým tlačítkem na projekt > **přidat novou položku** a vyberte **konfigurační soubor Bower**.</span><span class="sxs-lookup"><span data-stu-id="1ab53-165">In Solution Explorer, right-click the project > **Add New Item** and select **Bower Configuration File**.</span></span> <span data-ttu-id="1ab53-166">Poznámka: A *.bowerrc* také přidá soubor.</span><span class="sxs-lookup"><span data-stu-id="1ab53-166">Note: A *.bowerrc* file is also added.</span></span>

* <span data-ttu-id="1ab53-167">Otevřít *bower.json*a přidat jquery a bootstrap na `dependencies` oddílu.</span><span class="sxs-lookup"><span data-stu-id="1ab53-167">Open *bower.json*, and add jquery and bootstrap to the `dependencies` section.</span></span> <span data-ttu-id="1ab53-168">Výsledná *bower.json* soubor bude vypadat jako v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="1ab53-168">The resulting *bower.json* file will look like the following example.</span></span> <span data-ttu-id="1ab53-169">Verze bude v průběhu času měnit a nemusí odpovídat na následujícím obrázku.</span><span class="sxs-lookup"><span data-stu-id="1ab53-169">The versions will change over time and may not match the image below.</span></span>

[!code-json[](bower/sample/bower.json?highlight=5,6)]

* <span data-ttu-id="1ab53-170">Uložit *bower.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="1ab53-170">Save the *bower.json* file.</span></span>

  <span data-ttu-id="1ab53-171">Ověřte, projekt obsahuje *bootstrap* a *jQuery* adresáře v *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="1ab53-171">Verify the project includes the *bootstrap* and *jQuery* directories in *wwwroot/lib*.</span></span> <span data-ttu-id="1ab53-172">Používá pro bower *.bowerrc* sloužící k instalaci prostředky v *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="1ab53-172">Bower uses the *.bowerrc* file to install the assets in *wwwroot/lib*.</span></span>

  <span data-ttu-id="1ab53-173">Poznámka: Uživatelské rozhraní "Spravovat balíčky Bower" poskytuje alternativu k úpravě ručních souboru.</span><span class="sxs-lookup"><span data-stu-id="1ab53-173">Note: The "Manage Bower Packages" UI provides an alternative to manual file editing.</span></span>

### <a name="enable-static-files"></a><span data-ttu-id="1ab53-174">Povolte statické soubory</span><span class="sxs-lookup"><span data-stu-id="1ab53-174">Enable static files</span></span>

* <span data-ttu-id="1ab53-175">Přidat `Microsoft.AspNetCore.StaticFiles` do projektu balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="1ab53-175">Add the `Microsoft.AspNetCore.StaticFiles` NuGet package to the project.</span></span>
* <span data-ttu-id="1ab53-176">Povolte statické soubory ke zpracování se [middleware se statickými soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions).</span><span class="sxs-lookup"><span data-stu-id="1ab53-176">Enable static files to be served with the [Static file middleware](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions).</span></span> <span data-ttu-id="1ab53-177">Přidejte volání do [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions) k `Configure` metoda `Startup`.</span><span class="sxs-lookup"><span data-stu-id="1ab53-177">Add a call to [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions) to the `Configure` method of `Startup`.</span></span>

[!code-csharp[](bower/sample/Startup.cs?highlight=9)]

### <a name="reference-packages"></a><span data-ttu-id="1ab53-178">Odkaz na balíčky</span><span class="sxs-lookup"><span data-stu-id="1ab53-178">Reference packages</span></span>

<span data-ttu-id="1ab53-179">V této části vytvoříte stránku HTML k ověření, že může přistupovat k nasazených balíčků.</span><span class="sxs-lookup"><span data-stu-id="1ab53-179">In this section, you will create an HTML page to verify it can access the deployed packages.</span></span>

* <span data-ttu-id="1ab53-180">Přidejte novou stránku HTML s názvem *Index.html* k *wwwroot* složky.</span><span class="sxs-lookup"><span data-stu-id="1ab53-180">Add a new HTML page named *Index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="1ab53-181">Poznámka: Je nutné přidat soubor HTML k *wwwroot* složky.</span><span class="sxs-lookup"><span data-stu-id="1ab53-181">Note: You must add the HTML file to the *wwwroot* folder.</span></span> <span data-ttu-id="1ab53-182">Ve výchozím nastavení, statický obsah nelze zpracovat v mimo *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="1ab53-182">By default, static content cannot be served outside *wwwroot*.</span></span> <span data-ttu-id="1ab53-183">Zobrazit [statické soubory](xref:fundamentals/static-files) Další informace.</span><span class="sxs-lookup"><span data-stu-id="1ab53-183">See [Static files](xref:fundamentals/static-files) for more information.</span></span>

  <span data-ttu-id="1ab53-184">Nahraďte obsah *Index.html* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1ab53-184">Replace the contents of *Index.html* with the following markup:</span></span>

[!code-html[](bower/sample/Index.html)]

* <span data-ttu-id="1ab53-185">Spusťte aplikaci a přejděte do `http://localhost:<port>/Index.html`.</span><span class="sxs-lookup"><span data-stu-id="1ab53-185">Run the app and navigate to `http://localhost:<port>/Index.html`.</span></span> <span data-ttu-id="1ab53-186">Můžete také s *Index.html* otevřen, stiskněte klávesu `Ctrl+Shift+W`.</span><span class="sxs-lookup"><span data-stu-id="1ab53-186">Alternatively, with *Index.html* opened, press `Ctrl+Shift+W`.</span></span> <span data-ttu-id="1ab53-187">Ověřte, že se použije jumbotron stylu, kód jazyka jQuery reaguje, když dojde ke kliknutí na tlačítko a, že na spouštěcí tlačítko změní stav.</span><span class="sxs-lookup"><span data-stu-id="1ab53-187">Verify that the jumbotron styling is applied, the jQuery code responds when the button is clicked, and that the Bootstrap button changes state.</span></span>

  ![jumbotron stylem](bower/_static/jumbotron.png)
