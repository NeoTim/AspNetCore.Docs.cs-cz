---
title: Reusable Razor UI in class libraries with ASP.NET Core
author: Rick-Anderson
description: Vysvětluje, jak vytvořit opakovaně použitelné holicí strojek uI pomocí částečných zobrazení v knihovně tříd v ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: f24dc62eba345a8a3d35143805b4966cb51832fa
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667564"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="2f3c7-103">Vytvoření opakovaně použitelného ui pomocí projektu knihovny tříd razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2f3c7-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="2f3c7-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2f3c7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2f3c7-105">Zobrazení holicí strojek, stránky, řadiče, modely stránek, [komponenty Razor](xref:blazor/class-libraries), [komponenty Zobrazení](xref:mvc/views/view-components)a datové modely mohou být integrovány do knihovny tříd Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="2f3c7-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="2f3c7-106">RCL lze zabalit a znovu použít.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="2f3c7-107">Aplikace mohou obsahovat seznam RCL a přepsat zobrazení a stránky, které obsahuje.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="2f3c7-108">Když se ve webové aplikaci a v RCL nachází zobrazení, částečné zobrazení nebo stránka Razor, má přednost značka Razor (*soubor .cshtml)* ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="2f3c7-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2f3c7-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="2f3c7-110">Vytvoření knihovny tříd obsahující razor uI</span><span class="sxs-lookup"><span data-stu-id="2f3c7-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f3c7-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f3c7-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2f3c7-112">V sadě Visual Studio vyberte **Vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="2f3c7-113">Vyberte **knihovnu** > tříd razor **další**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="2f3c7-114">Pojmenujte knihovnu (například "RazorClassLib"), > **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="2f3c7-115">Chcete-li zabránit kolizi názvu souboru s knihovnou generovaného `.Views`zobrazení, zajistěte, aby název knihovny neskončil .</span><span class="sxs-lookup"><span data-stu-id="2f3c7-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="2f3c7-116">Pokud potřebujete podporovat zobrazení, vyberte **stránky a zobrazení podpory.**</span><span class="sxs-lookup"><span data-stu-id="2f3c7-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="2f3c7-117">Ve výchozím nastavení jsou podporovány pouze razor stránky.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="2f3c7-118">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-118">Select **Create**.</span></span>

<span data-ttu-id="2f3c7-119">Šablona třídy Razor (RCL) ve výchozím nastavení ve výchozím nastavení na vývoj komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="2f3c7-120">Možnost **Podpora stránek a zobrazení** podporuje stránky a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2f3c7-121">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="2f3c7-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2f3c7-122">Z příkazového řádku `dotnet new razorclasslib`spusťte .</span><span class="sxs-lookup"><span data-stu-id="2f3c7-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="2f3c7-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="2f3c7-124">Šablona třídy Razor (RCL) ve výchozím nastavení ve výchozím nastavení na vývoj komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="2f3c7-125">Pass `--support-pages-and-views` možnost`dotnet new razorclasslib --support-pages-and-views`( ) poskytovat podporu pro stránky a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="2f3c7-126">Další informace naleznete [v tématu dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="2f3c7-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="2f3c7-127">Chcete-li zabránit kolizi názvu souboru s knihovnou generovaného `.Views`zobrazení, zajistěte, aby název knihovny neskončil .</span><span class="sxs-lookup"><span data-stu-id="2f3c7-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="2f3c7-128">Přidejte soubory Razor do rcl.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="2f3c7-129">Šablony ASP.NET Core předpokládají, že obsah RCL je ve složce *Oblasti.*</span><span class="sxs-lookup"><span data-stu-id="2f3c7-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="2f3c7-130">Viz [Rozložení Stránek RCL](#rcl-pages-layout) k vytvoření rcl, `~/Areas/Pages`který zveřejňuje obsah v aplikaci `~/Pages` , nikoli .</span><span class="sxs-lookup"><span data-stu-id="2f3c7-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="2f3c7-131">Referenční obsah RCL</span><span class="sxs-lookup"><span data-stu-id="2f3c7-131">Reference RCL content</span></span>

<span data-ttu-id="2f3c7-132">RCL lze odkazovat:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="2f3c7-133">NuGet balíček.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-133">NuGet package.</span></span> <span data-ttu-id="2f3c7-134">Viz [Vytváření balíčků NuGet](/nuget/create-packages/creating-a-package) a [dotnet přidat balíček](/dotnet/core/tools/dotnet-add-package) a [vytvořit a publikovat balíček NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="2f3c7-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="2f3c7-135">*{Název_projektu}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="2f3c7-136">Viz [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="2f3c7-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="2f3c7-137">Přepsání zobrazení, částečných zobrazení a stránek</span><span class="sxs-lookup"><span data-stu-id="2f3c7-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="2f3c7-138">Když se ve webové aplikaci a v RCL nachází zobrazení, částečné zobrazení nebo stránka Razor, má přednost značka Razor (*soubor .cshtml)* ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="2f3c7-139">Například přidejte *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* do WebApp1 a Stránka1 ve WebApp1 bude mít přednost před stránkou1 v RCL.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="2f3c7-140">V ukázkovém stažení přejmenujte *WebApp1/Areas/MyFeature2* na *WebApp1/Areas/MyFeature,* abyste otestovali prioritu.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="2f3c7-141">Zkopírujte částečné zobrazení *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* do částečného zobrazení *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="2f3c7-142">Aktualizujte značky označující nové umístění.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="2f3c7-143">Vytvořte a spusťte aplikaci a ověřte, zda se používá verze částečné části aplikace.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="2f3c7-144">Rozložení Stránky RCL</span><span class="sxs-lookup"><span data-stu-id="2f3c7-144">RCL Pages layout</span></span>

<span data-ttu-id="2f3c7-145">Chcete-li odkazovat na obsah RCL, jako by byl součástí složky *Stránky* webové aplikace, vytvořte projekt RCL s následující strukturou souborů:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="2f3c7-146">*RazoruiClassLib/Stránky*</span><span class="sxs-lookup"><span data-stu-id="2f3c7-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="2f3c7-147">*RazoruiClassLib/Stránky/Sdílené*</span><span class="sxs-lookup"><span data-stu-id="2f3c7-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="2f3c7-148">Předpokládejme, že *RazorUIClassLib/Pages/Shared* obsahuje dva částečné soubory: *_Header.cshtml* a *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="2f3c7-149">Značky `<partial>` by mohly být přidány do souboru *_Layout.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="2f3c7-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="2f3c7-150">Vytvoření rcl se statickými datovými zdroji</span><span class="sxs-lookup"><span data-stu-id="2f3c7-150">Create an RCL with static assets</span></span>

<span data-ttu-id="2f3c7-151">RCL může vyžadovat doprovodné statické prostředky, na které lze odkazovat rcl nebo náročnou aplikací RCL.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="2f3c7-152">ASP.NET Core umožňuje vytváření zapsanic Než LL, které obsahují statické prostředky, které jsou k dispozici pro náročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="2f3c7-153">Chcete-li zahrnout doprovodné datové zdroje jako součást rcl, vytvořte složku *wwwroot* v knihovně tříd a do této složky zahrňte všechny požadované soubory.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="2f3c7-154">Při balení RCL jsou všechny doprovodné datové zdroje ve složce *wwwroot* automaticky zahrnuty do balíčku.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="2f3c7-155">Vyloučit statická aktiva</span><span class="sxs-lookup"><span data-stu-id="2f3c7-155">Exclude static assets</span></span>

<span data-ttu-id="2f3c7-156">Chcete-li vyloučit statické datové zdroje, `$(DefaultItemExcludes)` přidejte požadovanou cestu vyloučení do skupiny vlastností v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="2f3c7-157">Samostatné položky s`;`středníkem ( ).</span><span class="sxs-lookup"><span data-stu-id="2f3c7-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="2f3c7-158">V následujícím příkladu není šablona stylů *lib.css* ve složce *wwwroot* považována za statický datový zdroj a není zahrnuta v publikovaném seznamu RCL:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="2f3c7-159">Integrace psacího stroje</span><span class="sxs-lookup"><span data-stu-id="2f3c7-159">Typescript integration</span></span>

<span data-ttu-id="2f3c7-160">Zahrnutí souborů jazyka TypeScript do rcl:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="2f3c7-161">Umístěte soubory jazyka TypeScript (*Ts*) mimo složku *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="2f3c7-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="2f3c7-162">Soubory můžete například umístit do složky *Klient.*</span><span class="sxs-lookup"><span data-stu-id="2f3c7-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="2f3c7-163">Nakonfigurujte výstup sestavení jazyka TypeScript pro složku *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="2f3c7-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="2f3c7-164">Nastavte `TypescriptOutDir` vlastnost uvnitř `PropertyGroup` a v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="2f3c7-165">Zahrňte cíl Jazyka TypeScript `ResolveCurrentProjectStaticWebAssets` jako závislost cíle přidáním `PropertyGroup` následujícího cíle do souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="2f3c7-166">Využití obsahu z odkazované hod-rcl</span><span class="sxs-lookup"><span data-stu-id="2f3c7-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="2f3c7-167">Soubory obsažené ve složce *wwwroot* rcl jsou vystaveny rcl nebo náročné aplikace `_content/{LIBRARY NAME}/`pod předponou .</span><span class="sxs-lookup"><span data-stu-id="2f3c7-167">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="2f3c7-168">Například knihovna s názvem *Razor.Class.Lib* má za `_content/Razor.Class.Lib/`následek cestu ke statickému obsahu na .</span><span class="sxs-lookup"><span data-stu-id="2f3c7-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="2f3c7-169">Při vytváření balíčku NuGet a název sestavení není stejný jako ID balíčku, použijte `{LIBRARY NAME}`ID balíčku pro .</span><span class="sxs-lookup"><span data-stu-id="2f3c7-169">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="2f3c7-170">Náročná aplikace odkazuje na statické datové zdroje `<script>` `<style>`poskytované `<img>`knihovnou pomocí značek , , a dalších značek HTML.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-170">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="2f3c7-171">Náročná aplikace musí mít [povolenou podporu statických souborů](xref:fundamentals/static-files) v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-171">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="2f3c7-172">Při spuštění náročné aplikace z`dotnet run`výstupu sestavení ( ), statické webové prostředky jsou povoleny ve výchozím nastavení ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-172">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="2f3c7-173">Chcete-li podporovat prostředky v jiných prostředích `UseStaticWebAssets` při spuštění z výstupu sestavení, volejte tvůrce hostitele v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-173">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="2f3c7-174">Volání `UseStaticWebAssets` není vyžadováno při spuštění aplikace z`dotnet publish`publikovaného výstupu ( ).</span><span class="sxs-lookup"><span data-stu-id="2f3c7-174">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="2f3c7-175">Vývoj více projektů</span><span class="sxs-lookup"><span data-stu-id="2f3c7-175">Multi-project development flow</span></span>

<span data-ttu-id="2f3c7-176">Při spuštění náročné aplikace:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-176">When the consuming app runs:</span></span>

* <span data-ttu-id="2f3c7-177">Datové zdroje v rcl zůstávají v původních složkách.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-177">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="2f3c7-178">Datové zdroje se nepřesunou do náročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-178">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="2f3c7-179">Všechny změny v rámci *složky wwwroot* RCL se projeví v náročné aplikaci po rcl je znovu a bez opětovnésestavení náročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-179">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="2f3c7-180">Při sestavení rcl je vytvořen manifest, který popisuje umístění statických webových datových zdrojů.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-180">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="2f3c7-181">Náročná aplikace čte manifest za běhu, aby spotřebovával prostředky z odkazovaných projektů a balíčků.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-181">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="2f3c7-182">Když je nový datový zdroj přidán do rcl, rcl musí být znovu sestaveny aktualizovat svůj manifest před náročné aplikace přístup k nového datového zdroje.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-182">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="2f3c7-183">Publikování</span><span class="sxs-lookup"><span data-stu-id="2f3c7-183">Publish</span></span>

<span data-ttu-id="2f3c7-184">Po publikování aplikace se doprovodné datové zdroje ze všech odkazovaných projektů a balíčků zkopírují `_content/{LIBRARY NAME}/`do *složky wwwroot* publikované aplikace v části .</span><span class="sxs-lookup"><span data-stu-id="2f3c7-184">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2f3c7-185">Zobrazení holicí strojek, stránky, řadiče, modely stránek, [komponenty Razor](xref:blazor/class-libraries), [komponenty Zobrazení](xref:mvc/views/view-components)a datové modely mohou být integrovány do knihovny tříd Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="2f3c7-185">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="2f3c7-186">RCL lze zabalit a znovu použít.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-186">The RCL can be packaged and reused.</span></span> <span data-ttu-id="2f3c7-187">Aplikace mohou obsahovat seznam RCL a přepsat zobrazení a stránky, které obsahuje.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-187">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="2f3c7-188">Když se ve webové aplikaci a v RCL nachází zobrazení, částečné zobrazení nebo stránka Razor, má přednost značka Razor (*soubor .cshtml)* ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-188">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="2f3c7-189">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2f3c7-189">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="2f3c7-190">Vytvoření knihovny tříd obsahující razor uI</span><span class="sxs-lookup"><span data-stu-id="2f3c7-190">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f3c7-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f3c7-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2f3c7-192">V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2f3c7-193">Vyberte **ASP.NET základní webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-193">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="2f3c7-194">Pojmenujte knihovnu (například "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-194">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="2f3c7-195">Chcete-li zabránit kolizi názvu souboru s knihovnou generovaného `.Views`zobrazení, zajistěte, aby název knihovny neskončil .</span><span class="sxs-lookup"><span data-stu-id="2f3c7-195">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="2f3c7-196">Ověřte, ASP.NET je **vybrána možnost Core 2.1** nebo novější.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-196">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="2f3c7-197">Vyberte **knihovnu** > tříd razor **OK**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-197">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="2f3c7-198">RCL má následující soubor projektu:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-198">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="2f3c7-199">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="2f3c7-199">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2f3c7-200">Z příkazového řádku `dotnet new razorclasslib`spusťte .</span><span class="sxs-lookup"><span data-stu-id="2f3c7-200">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="2f3c7-201">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-201">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="2f3c7-202">Další informace naleznete [v tématu dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="2f3c7-202">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="2f3c7-203">Chcete-li zabránit kolizi názvu souboru s knihovnou generovaného `.Views`zobrazení, zajistěte, aby název knihovny neskončil .</span><span class="sxs-lookup"><span data-stu-id="2f3c7-203">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="2f3c7-204">Přidejte soubory Razor do rcl.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-204">Add Razor files to the RCL.</span></span>

<span data-ttu-id="2f3c7-205">Šablony ASP.NET Core předpokládají, že obsah RCL je ve složce *Oblasti.*</span><span class="sxs-lookup"><span data-stu-id="2f3c7-205">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="2f3c7-206">Viz [Rozložení Stránek RCL](#rcl-pages-layout) k vytvoření rcl, `~/Areas/Pages`který zveřejňuje obsah v aplikaci `~/Pages` , nikoli .</span><span class="sxs-lookup"><span data-stu-id="2f3c7-206">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="2f3c7-207">Referenční obsah RCL</span><span class="sxs-lookup"><span data-stu-id="2f3c7-207">Reference RCL content</span></span>

<span data-ttu-id="2f3c7-208">RCL lze odkazovat:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-208">The RCL can be referenced by:</span></span>

* <span data-ttu-id="2f3c7-209">NuGet balíček.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-209">NuGet package.</span></span> <span data-ttu-id="2f3c7-210">Viz [Vytváření balíčků NuGet](/nuget/create-packages/creating-a-package) a [dotnet přidat balíček](/dotnet/core/tools/dotnet-add-package) a [vytvořit a publikovat balíček NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="2f3c7-210">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="2f3c7-211">*{Název_projektu}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-211">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="2f3c7-212">Viz [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="2f3c7-212">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="2f3c7-213">Návod: Vytvoření projektu RCL a použití z projektu Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2f3c7-213">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="2f3c7-214">Můžete si stáhnout [celý projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) a otestovat jej, nikoli jej vytvořit.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-214">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="2f3c7-215">Ukázkové stažení obsahuje další kód a odkazy, které usnadňují testování projektu.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-215">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="2f3c7-216">Můžete zanechat zpětnou vazbu v [tomto problému GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6098) s komentáři ke stažení ukázky versus podrobný návod.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-216">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="2f3c7-217">Testování aplikace pro stažení</span><span class="sxs-lookup"><span data-stu-id="2f3c7-217">Test the download app</span></span>

<span data-ttu-id="2f3c7-218">Pokud jste nestáhli dokončenou aplikaci a raději byste vytvořili projekt návodu, přejděte k [další části](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="2f3c7-218">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f3c7-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f3c7-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2f3c7-220">Otevřete soubor *.sln* v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-220">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="2f3c7-221">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-221">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2f3c7-222">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="2f3c7-222">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2f3c7-223">Z příkazového řádku v *adresáři cli* vytvořte Seznam RCL a webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-223">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="2f3c7-224">Přejděte do adresáře *WebApp1* a spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-224">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="2f3c7-225">Postupujte podle pokynů v [aplikaci Test WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="2f3c7-225">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="2f3c7-226">Vytvoření rcl</span><span class="sxs-lookup"><span data-stu-id="2f3c7-226">Create an RCL</span></span>

<span data-ttu-id="2f3c7-227">V této části je vytvořen rcl.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-227">In this section, an RCL is created.</span></span> <span data-ttu-id="2f3c7-228">Razor soubory jsou přidány do RCL.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-228">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f3c7-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f3c7-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2f3c7-230">Vytvořte projekt RCL:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-230">Create the RCL project:</span></span>

* <span data-ttu-id="2f3c7-231">V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-231">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2f3c7-232">Vyberte **ASP.NET základní webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-232">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="2f3c7-233">Název aplikace **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-233">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="2f3c7-234">Ověřte, ASP.NET je **vybrána možnost Core 2.1** nebo novější.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-234">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="2f3c7-235">Vyberte **knihovnu** > tříd razor **OK**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-235">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="2f3c7-236">Přidejte soubor částečného zobrazení Razor s názvem *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-236">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2f3c7-237">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="2f3c7-237">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2f3c7-238">Z příkazového řádku spusťte následující:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-238">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="2f3c7-239">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-239">The preceding commands:</span></span>

* <span data-ttu-id="2f3c7-240">Vytvoří `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-240">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="2f3c7-241">Vytvoří _Message stránku a přidá ji do rcl.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-241">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="2f3c7-242">Parametr `-np` vytvoří stránku `PageModel`bez .</span><span class="sxs-lookup"><span data-stu-id="2f3c7-242">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="2f3c7-243">Vytvoří soubor [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) a přidá jej do rcl.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-243">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="2f3c7-244">Soubor *_ViewStart.cshtml* je nutné použít rozložení projektu Razor Pages (který je přidán v další části).</span><span class="sxs-lookup"><span data-stu-id="2f3c7-244">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="2f3c7-245">Přidání souborů a složek Razor do projektu</span><span class="sxs-lookup"><span data-stu-id="2f3c7-245">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="2f3c7-246">Nahraďte značky v *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="2f3c7-247">Nahraďte značky v *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="2f3c7-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`je nutné použít částečný`<partial name="_Message" />`pohled ( ).</span><span class="sxs-lookup"><span data-stu-id="2f3c7-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="2f3c7-249">Místo zahrnutí `@addTagHelper` směrnice můžete přidat soubor *_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="2f3c7-249">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="2f3c7-250">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-250">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="2f3c7-251">Další informace o *_ViewImports.cshtml*najdete [v tématu Import sdílených směrnic](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="2f3c7-251">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="2f3c7-252">Vytvořte knihovnu tříd a ověřte, že neexistují žádné chyby kompilátoru:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-252">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="2f3c7-253">Výstup sestavení obsahuje *RazorUIClassLib.dll* a *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-253">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="2f3c7-254">*RazorUIClassLib.Views.dll* obsahuje zkompilovaný obsah Razor.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-254">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="2f3c7-255">Použití knihovny Razor UI z projektu Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2f3c7-255">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f3c7-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f3c7-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2f3c7-257">Vytvořte webovou aplikaci Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-257">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="2f3c7-258">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na řešení > **Přidat** > **nový projekt**.  </span><span class="sxs-lookup"><span data-stu-id="2f3c7-258">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="2f3c7-259">Vyberte **ASP.NET základní webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-259">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="2f3c7-260">Pojmenujte aplikaci **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-260">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="2f3c7-261">Ověřte, ASP.NET je **vybrána možnost Core 2.1** nebo novější.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-261">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="2f3c7-262">Vyberte **webovou aplikaci** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-262">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="2f3c7-263">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **WebApp1** a vyberte **nastavit jako počáteční projekt**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-263">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="2f3c7-264">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **webapp1** a vyberte **možnost Sestavení závislostí** > **projektů**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-264">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="2f3c7-265">Zkontrolujte **RazorUIClassLib** jako závislost **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-265">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="2f3c7-266">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **webapp1** a vyberte **přidat** > **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-266">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="2f3c7-267">V dialogovém **okně Správce odkazů** zaškrtněte **políčko RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-267">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="2f3c7-268">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-268">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2f3c7-269">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="2f3c7-269">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2f3c7-270">Vytvořte webovou aplikaci Razor Pages a soubor řešení obsahující aplikaci Razor Pages a RCL:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-270">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="2f3c7-271">Vytvoření a spuštění webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-271">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="2f3c7-272">Test WebApp1</span><span class="sxs-lookup"><span data-stu-id="2f3c7-272">Test WebApp1</span></span>

<span data-ttu-id="2f3c7-273">Vyhledejte `/MyFeature/Page1` ověření, zda je používána knihovna tříd rozhraní Razor.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-273">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="2f3c7-274">Přepsání zobrazení, částečných zobrazení a stránek</span><span class="sxs-lookup"><span data-stu-id="2f3c7-274">Override views, partial views, and pages</span></span>

<span data-ttu-id="2f3c7-275">Když se ve webové aplikaci a v RCL nachází zobrazení, částečné zobrazení nebo stránka Razor, má přednost značka Razor (*soubor .cshtml)* ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-275">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="2f3c7-276">Například přidejte *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* do WebApp1 a Stránka1 ve WebApp1 bude mít přednost před stránkou1 v RCL.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-276">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="2f3c7-277">V ukázkovém stažení přejmenujte *WebApp1/Areas/MyFeature2* na *WebApp1/Areas/MyFeature,* abyste otestovali prioritu.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-277">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="2f3c7-278">Zkopírujte částečné zobrazení *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* do částečného zobrazení *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-278">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="2f3c7-279">Aktualizujte značky označující nové umístění.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-279">Update the markup to indicate the new location.</span></span> <span data-ttu-id="2f3c7-280">Vytvořte a spusťte aplikaci a ověřte, zda se používá verze částečné části aplikace.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-280">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="2f3c7-281">Rozložení Stránky RCL</span><span class="sxs-lookup"><span data-stu-id="2f3c7-281">RCL Pages layout</span></span>

<span data-ttu-id="2f3c7-282">Chcete-li odkazovat na obsah RCL, jako by byl součástí složky *Stránky* webové aplikace, vytvořte projekt RCL s následující strukturou souborů:</span><span class="sxs-lookup"><span data-stu-id="2f3c7-282">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="2f3c7-283">*RazoruiClassLib/Stránky*</span><span class="sxs-lookup"><span data-stu-id="2f3c7-283">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="2f3c7-284">*RazoruiClassLib/Stránky/Sdílené*</span><span class="sxs-lookup"><span data-stu-id="2f3c7-284">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="2f3c7-285">Předpokládejme, že *RazorUIClassLib/Pages/Shared* obsahuje dva částečné soubory: *_Header.cshtml* a *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2f3c7-285">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="2f3c7-286">Značky `<partial>` by mohly být přidány do souboru *_Layout.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="2f3c7-286">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2f3c7-287">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2f3c7-287">Additional resources</span></span>

* <xref:blazor/class-libraries>
