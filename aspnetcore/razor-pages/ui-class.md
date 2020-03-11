---
title: Opakovaně použitelné Razor uživatelského rozhraní v knihovnách tříd pomocí ASP.NET Core
author: Rick-Anderson
description: Vysvětluje, jak vytvářet opakovaně použitelné uživatelské rozhraní Razor pomocí částečných zobrazení v knihovně tříd v ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: f24dc62eba345a8a3d35143805b4966cb51832fa
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667564"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="1291f-103">Vytvoření opakovaně použitelného uživatelského rozhraní pomocí projektu knihovny tříd Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1291f-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="1291f-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1291f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1291f-105">Zobrazení Razor, stránky, řadiče, modely stránek, [komponenty Razor](xref:blazor/class-libraries), [zobrazení komponent](xref:mvc/views/view-components)a datové modely lze integrovat do knihovny tříd Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="1291f-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="1291f-106">RCL můžete zabalit a znovu použít.</span><span class="sxs-lookup"><span data-stu-id="1291f-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="1291f-107">Aplikace můžete zahrnout RCL a přepsání, zobrazení a stránky, které obsahuje.</span><span class="sxs-lookup"><span data-stu-id="1291f-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="1291f-108">V případě, že je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka Razor, má přednost označení Razor (soubor *. cshtml* ) ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1291f-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="1291f-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1291f-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="1291f-110">Vytvoření knihovny tříd obsahující Razor uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="1291f-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1291f-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1291f-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1291f-112">V aplikaci Visual Studio vyberte **vytvořit nový nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="1291f-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="1291f-113">> **Další**vyberte **knihovnu tříd Razor** .</span><span class="sxs-lookup"><span data-stu-id="1291f-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="1291f-114">Pojmenujte knihovnu (například "RazorClassLib"), > **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1291f-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="1291f-115">Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views`.</span><span class="sxs-lookup"><span data-stu-id="1291f-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="1291f-116">Pokud potřebujete podporovat zobrazení, vyberte možnost **stránky podpory a zobrazení** .</span><span class="sxs-lookup"><span data-stu-id="1291f-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="1291f-117">Ve výchozím nastavení jsou podporovány pouze Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="1291f-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="1291f-118">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1291f-118">Select **Create**.</span></span>

<span data-ttu-id="1291f-119">Šablona knihovny tříd Razor (RCL) je ve výchozím nastavení standardně pro vývoj komponent Razor.</span><span class="sxs-lookup"><span data-stu-id="1291f-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="1291f-120">Možnost **stránky podpory a zobrazení** podporuje stránky a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1291f-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1291f-121">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="1291f-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1291f-122">Z příkazového řádku spusťte `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="1291f-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="1291f-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="1291f-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="1291f-124">Šablona knihovny tříd Razor (RCL) je ve výchozím nastavení standardně pro vývoj komponent Razor.</span><span class="sxs-lookup"><span data-stu-id="1291f-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="1291f-125">Předáním možnosti `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`) zajistíte podporu pro stránky a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1291f-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="1291f-126">Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="1291f-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="1291f-127">Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views`.</span><span class="sxs-lookup"><span data-stu-id="1291f-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="1291f-128">Přidáte soubory Razor RCL.</span><span class="sxs-lookup"><span data-stu-id="1291f-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="1291f-129">Šablony ASP.NET Core předpokládají, že obsah RCL je ve složce *oblasti* .</span><span class="sxs-lookup"><span data-stu-id="1291f-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="1291f-130">Pokud chcete vytvořit RCL, který zpřístupňuje obsah v `~/Pages` místo `~/Areas/Pages`, přečtěte si téma [rozložení stránek RCL](#rcl-pages-layout) .</span><span class="sxs-lookup"><span data-stu-id="1291f-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="1291f-131">Odkaz na obsah RCL</span><span class="sxs-lookup"><span data-stu-id="1291f-131">Reference RCL content</span></span>

<span data-ttu-id="1291f-132">RCL lze odkazovat pomocí:</span><span class="sxs-lookup"><span data-stu-id="1291f-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="1291f-133">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="1291f-133">NuGet package.</span></span> <span data-ttu-id="1291f-134">Viz téma [vytváření balíčků NuGet](/nuget/create-packages/creating-a-package) a [dotnet přidání balíčku](/dotnet/core/tools/dotnet-add-package) a [Vytvoření a publikování balíčku NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="1291f-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="1291f-135">*{ProjectName}. csproj*.</span><span class="sxs-lookup"><span data-stu-id="1291f-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="1291f-136">Viz [dotnet – přidat odkaz](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="1291f-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="1291f-137">Přepsání, zobrazení, částečná zobrazení a stránky</span><span class="sxs-lookup"><span data-stu-id="1291f-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="1291f-138">V případě, že je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka Razor, má přednost označení Razor (soubor *. cshtml* ) ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1291f-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="1291f-139">Například přidejte *WebApp1/areas/MyFeature/Pages/Page1. cshtml* do WebApp1 a Page1 v WebApp1 bude mít přednost před Page1 v RCL.</span><span class="sxs-lookup"><span data-stu-id="1291f-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="1291f-140">V ukázce stažení, přejmenujte *WebApp1/areas/MyFeature2* na *WebApp1/areas/MyFeature* na test priority.</span><span class="sxs-lookup"><span data-stu-id="1291f-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="1291f-141">Zkopírujte částečné zobrazení *RazorUIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml* do *WebApp1/areas/MyFeature/Pages/shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1291f-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="1291f-142">Aktualizace značky k označení nového umístění.</span><span class="sxs-lookup"><span data-stu-id="1291f-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="1291f-143">Sestavení a spuštění aplikace a zkontrolujte, že verze aplikace s částečným se používá.</span><span class="sxs-lookup"><span data-stu-id="1291f-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="1291f-144">Rozložení stránek RCL</span><span class="sxs-lookup"><span data-stu-id="1291f-144">RCL Pages layout</span></span>

<span data-ttu-id="1291f-145">Pokud chcete odkazovat na obsah RCL, jako by byl *součástí složky webových* aplikací, vytvořte projekt RCL s následující strukturou souborů:</span><span class="sxs-lookup"><span data-stu-id="1291f-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="1291f-146">*RazorUIClassLib/stránky*</span><span class="sxs-lookup"><span data-stu-id="1291f-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="1291f-147">*RazorUIClassLib/stránky/sdílené*</span><span class="sxs-lookup"><span data-stu-id="1291f-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="1291f-148">Předpokládejme, že *RazorUIClassLib/Pages/Shared* obsahují dva částečné soubory: *_Header. cshtml* a *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1291f-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="1291f-149">Do souboru *_Layout. cshtml* se dají přidat `<partial>` značky:</span><span class="sxs-lookup"><span data-stu-id="1291f-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="1291f-150">Vytvoření RCL se statickými prostředky</span><span class="sxs-lookup"><span data-stu-id="1291f-150">Create an RCL with static assets</span></span>

<span data-ttu-id="1291f-151">RCL může vyžadovat doprovodné statické prostředky, na které lze odkazovat buď RCL, nebo náročné aplikace RCL.</span><span class="sxs-lookup"><span data-stu-id="1291f-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="1291f-152">ASP.NET Core umožňuje vytváření RCLs, které zahrnují statické prostředky, které jsou k dispozici pro náročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="1291f-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="1291f-153">Chcete-li zahrnout doprovodné materiály jako součást RCL, vytvořte složku *wwwroot* v knihovně tříd a zahrňte všechny požadované soubory do této složky.</span><span class="sxs-lookup"><span data-stu-id="1291f-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="1291f-154">Při balení RCL jsou do balíčku automaticky zahrnuty všechny doprovodné materiály ve složce *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="1291f-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="1291f-155">Vyloučení statických prostředků</span><span class="sxs-lookup"><span data-stu-id="1291f-155">Exclude static assets</span></span>

<span data-ttu-id="1291f-156">Chcete-li vyloučit statické prostředky, přidejte požadovanou cestu vyloučení do skupiny vlastností `$(DefaultItemExcludes)` v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="1291f-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="1291f-157">Jednotlivé položky oddělte středníkem (`;`).</span><span class="sxs-lookup"><span data-stu-id="1291f-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="1291f-158">V následujícím příkladu není šablona *lib. CSS* ve složce *wwwroot* považována za statický prostředek a není obsažena v publikovaném RCL:</span><span class="sxs-lookup"><span data-stu-id="1291f-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="1291f-159">Integrace TypeScriptu</span><span class="sxs-lookup"><span data-stu-id="1291f-159">Typescript integration</span></span>

<span data-ttu-id="1291f-160">Zahrnutí souborů TypeScriptu do RCL:</span><span class="sxs-lookup"><span data-stu-id="1291f-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="1291f-161">Soubory TypeScript ( *. TS*) umístěte mimo složku *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="1291f-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="1291f-162">Umístěte například soubory do složky *klienta* .</span><span class="sxs-lookup"><span data-stu-id="1291f-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="1291f-163">Nakonfigurujte výstup sestavení TypeScriptu pro složku *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="1291f-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="1291f-164">Nastavte vlastnost `TypescriptOutDir` v rámci `PropertyGroup` v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="1291f-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="1291f-165">Přidejte cíl TypeScript jako závislost cíle `ResolveCurrentProjectStaticWebAssets` přidáním následujícího cíle do `PropertyGroup` v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="1291f-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="1291f-166">Využití obsahu z odkazovaného RCL</span><span class="sxs-lookup"><span data-stu-id="1291f-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="1291f-167">Soubory zahrnuté ve složce *wwwroot* v RCL jsou zpřístupněny buď RCL, nebo aplikacím, které využívají, v předponě `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="1291f-167">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="1291f-168">Například knihovna s názvem *Razor. Class. lib* má za následek cestu k statickému obsahu v `_content/Razor.Class.Lib/`.</span><span class="sxs-lookup"><span data-stu-id="1291f-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="1291f-169">Když vytváříte balíček NuGet a název sestavení není stejný jako ID balíčku, použijte ID balíčku pro `{LIBRARY NAME}`.</span><span class="sxs-lookup"><span data-stu-id="1291f-169">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="1291f-170">Nenáročné aplikace odkazují na statické prostředky poskytované knihovnou pomocí `<script>`, `<style>`, `<img>`a dalších značek HTML.</span><span class="sxs-lookup"><span data-stu-id="1291f-170">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="1291f-171">Vybírající aplikace musí mít povolenou [podporu statických souborů](xref:fundamentals/static-files) v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1291f-171">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="1291f-172">Při spuštění náročné aplikace z výstupu sestavení (`dotnet run`) jsou ve výchozím nastavení ve vývojovém prostředí povoleny statické webové prostředky.</span><span class="sxs-lookup"><span data-stu-id="1291f-172">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="1291f-173">Pro podporu prostředků v jiných prostředích při spuštění z výstupu sestavení volejte `UseStaticWebAssets` na tvůrci hostitele v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1291f-173">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="1291f-174">Při spuštění aplikace z publikovaného výstupu (`dotnet publish`) se nevyžaduje volání `UseStaticWebAssets`.</span><span class="sxs-lookup"><span data-stu-id="1291f-174">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="1291f-175">Vývojový tok pro více projektů</span><span class="sxs-lookup"><span data-stu-id="1291f-175">Multi-project development flow</span></span>

<span data-ttu-id="1291f-176">Když pracujete na náročné aplikaci:</span><span class="sxs-lookup"><span data-stu-id="1291f-176">When the consuming app runs:</span></span>

* <span data-ttu-id="1291f-177">Prostředky v RCL zůstanou v původních složkách.</span><span class="sxs-lookup"><span data-stu-id="1291f-177">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="1291f-178">Prostředky se nepřesunou do nenáročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="1291f-178">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="1291f-179">Jakákoli změna ve složce *WWWROOT* RCL se projeví v aplikaci po resestavení RCL a bez opětovného sestavení náročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="1291f-179">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="1291f-180">Při sestavení RCL je vytvořen manifest, který popisuje umístění statických webových prostředků.</span><span class="sxs-lookup"><span data-stu-id="1291f-180">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="1291f-181">Vybírající aplikace načte manifest za běhu za účelem využití prostředků z odkazovaných projektů a balíčků.</span><span class="sxs-lookup"><span data-stu-id="1291f-181">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="1291f-182">Když se do RCL přidá nový Asset, musí se RCL znovu sestavit, aby se aktualizoval jeho manifest předtím, než bude mít k novému assetu přístup.</span><span class="sxs-lookup"><span data-stu-id="1291f-182">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="1291f-183">Publikování</span><span class="sxs-lookup"><span data-stu-id="1291f-183">Publish</span></span>

<span data-ttu-id="1291f-184">Po publikování aplikace se doprovodné prostředky ze všech odkazovaných projektů a balíčků zkopírují do složky *wwwroot* publikované aplikace v části `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="1291f-184">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1291f-185">Zobrazení Razor, stránky, řadiče, modely stránek, [komponenty Razor](xref:blazor/class-libraries), [zobrazení komponent](xref:mvc/views/view-components)a datové modely lze integrovat do knihovny tříd Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="1291f-185">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="1291f-186">RCL můžete zabalit a znovu použít.</span><span class="sxs-lookup"><span data-stu-id="1291f-186">The RCL can be packaged and reused.</span></span> <span data-ttu-id="1291f-187">Aplikace můžete zahrnout RCL a přepsání, zobrazení a stránky, které obsahuje.</span><span class="sxs-lookup"><span data-stu-id="1291f-187">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="1291f-188">V případě, že je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka Razor, má přednost označení Razor (soubor *. cshtml* ) ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1291f-188">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="1291f-189">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1291f-189">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="1291f-190">Vytvoření knihovny tříd obsahující Razor uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="1291f-190">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1291f-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1291f-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1291f-192">V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="1291f-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1291f-193">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="1291f-193">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="1291f-194">Pojmenujte knihovnu (například "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="1291f-194">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="1291f-195">Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views`.</span><span class="sxs-lookup"><span data-stu-id="1291f-195">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="1291f-196">Ověřte, zda je vybrána **ASP.NET Core 2,1** nebo novější.</span><span class="sxs-lookup"><span data-stu-id="1291f-196">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="1291f-197">Vyberte **knihovnu tříd Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="1291f-197">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="1291f-198">RCL má následující soubor projektu:</span><span class="sxs-lookup"><span data-stu-id="1291f-198">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="1291f-199">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="1291f-199">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1291f-200">Z příkazového řádku spusťte `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="1291f-200">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="1291f-201">Příklad:</span><span class="sxs-lookup"><span data-stu-id="1291f-201">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="1291f-202">Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="1291f-202">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="1291f-203">Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views`.</span><span class="sxs-lookup"><span data-stu-id="1291f-203">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="1291f-204">Přidáte soubory Razor RCL.</span><span class="sxs-lookup"><span data-stu-id="1291f-204">Add Razor files to the RCL.</span></span>

<span data-ttu-id="1291f-205">Šablony ASP.NET Core předpokládají, že obsah RCL je ve složce *oblasti* .</span><span class="sxs-lookup"><span data-stu-id="1291f-205">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="1291f-206">Pokud chcete vytvořit RCL, který zpřístupňuje obsah v `~/Pages` místo `~/Areas/Pages`, přečtěte si téma [rozložení stránek RCL](#rcl-pages-layout) .</span><span class="sxs-lookup"><span data-stu-id="1291f-206">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="1291f-207">Odkaz na obsah RCL</span><span class="sxs-lookup"><span data-stu-id="1291f-207">Reference RCL content</span></span>

<span data-ttu-id="1291f-208">RCL lze odkazovat pomocí:</span><span class="sxs-lookup"><span data-stu-id="1291f-208">The RCL can be referenced by:</span></span>

* <span data-ttu-id="1291f-209">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="1291f-209">NuGet package.</span></span> <span data-ttu-id="1291f-210">Viz téma [vytváření balíčků NuGet](/nuget/create-packages/creating-a-package) a [dotnet přidání balíčku](/dotnet/core/tools/dotnet-add-package) a [Vytvoření a publikování balíčku NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="1291f-210">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="1291f-211">*{ProjectName}. csproj*.</span><span class="sxs-lookup"><span data-stu-id="1291f-211">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="1291f-212">Viz [dotnet – přidat odkaz](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="1291f-212">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="1291f-213">Návod: vytvoření projektu RCL a použití z Razor Pages projektu</span><span class="sxs-lookup"><span data-stu-id="1291f-213">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="1291f-214">Můžete stáhnout [celý projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) a otestovat ho místo jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="1291f-214">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="1291f-215">Vzorku ke stažení obsahuje další kódu a odkazy, které usnadňuje testování projektu.</span><span class="sxs-lookup"><span data-stu-id="1291f-215">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="1291f-216">Zpětnou vazbu k [tomuto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/6098) si můžete nechat s vašimi komentáři ke stažení ukázek a podrobnými pokyny.</span><span class="sxs-lookup"><span data-stu-id="1291f-216">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="1291f-217">Testování aplikace ke stažení</span><span class="sxs-lookup"><span data-stu-id="1291f-217">Test the download app</span></span>

<span data-ttu-id="1291f-218">Pokud jste nestáhli dokončenou aplikaci a chcete místo toho vytvořit návodový projekt, přejděte k [Další části](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="1291f-218">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1291f-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1291f-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1291f-220">Otevřete soubor *. sln* v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1291f-220">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="1291f-221">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1291f-221">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1291f-222">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="1291f-222">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1291f-223">Z příkazového řádku v adresáři *CLI* Sestavte RCL a webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1291f-223">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="1291f-224">Přejděte do adresáře *WebApp1* a spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="1291f-224">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="1291f-225">Postupujte podle pokynů v části [test WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="1291f-225">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="1291f-226">Vytvoření RCL</span><span class="sxs-lookup"><span data-stu-id="1291f-226">Create an RCL</span></span>

<span data-ttu-id="1291f-227">V této části se vytvoří RCL.</span><span class="sxs-lookup"><span data-stu-id="1291f-227">In this section, an RCL is created.</span></span> <span data-ttu-id="1291f-228">Soubory Razor jsou přidány do RCL.</span><span class="sxs-lookup"><span data-stu-id="1291f-228">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1291f-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1291f-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1291f-230">Vytvoření projektu RCL:</span><span class="sxs-lookup"><span data-stu-id="1291f-230">Create the RCL project:</span></span>

* <span data-ttu-id="1291f-231">V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="1291f-231">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1291f-232">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="1291f-232">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="1291f-233">Pojmenujte aplikaci **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="1291f-233">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="1291f-234">Ověřte, zda je vybrána **ASP.NET Core 2,1** nebo novější.</span><span class="sxs-lookup"><span data-stu-id="1291f-234">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="1291f-235">Vyberte **knihovnu tříd Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="1291f-235">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="1291f-236">Přidejte soubor částečného zobrazení Razor s názvem *RazorUIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1291f-236">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1291f-237">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="1291f-237">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1291f-238">Z příkazového řádku spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1291f-238">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="1291f-239">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="1291f-239">The preceding commands:</span></span>

* <span data-ttu-id="1291f-240">Vytvoří `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="1291f-240">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="1291f-241">Vytvoří stránku Razor _TEXT a přidá jej RCL.</span><span class="sxs-lookup"><span data-stu-id="1291f-241">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="1291f-242">Parametr `-np` vytvoří stránku bez `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="1291f-242">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="1291f-243">Vytvoří soubor [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) a přidá jej do RCL.</span><span class="sxs-lookup"><span data-stu-id="1291f-243">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="1291f-244">Soubor *_ViewStart. cshtml* je vyžadován pro použití rozložení Razor Pagesho projektu (který je přidán v další části).</span><span class="sxs-lookup"><span data-stu-id="1291f-244">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="1291f-245">Přidání Razor souborů a složek do projektu</span><span class="sxs-lookup"><span data-stu-id="1291f-245">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="1291f-246">Nahraďte označení v *RazorUIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1291f-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="1291f-247">Nahraďte označení v *RazorUIClassLib/areas/MyFeature/Pages/Page1. cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1291f-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="1291f-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` se vyžaduje pro použití částečného zobrazení (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="1291f-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="1291f-249">Místo zahrnutí direktivy `@addTagHelper` můžete přidat *_ViewImports soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1291f-249">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="1291f-250">Příklad:</span><span class="sxs-lookup"><span data-stu-id="1291f-250">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="1291f-251">Další informace o *_ViewImports. cshtml*naleznete v tématu [importing Shared direktivy](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="1291f-251">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="1291f-252">Sestavení knihovny tříd pro ověření, že zde nejsou žádné chyby kompilátoru:</span><span class="sxs-lookup"><span data-stu-id="1291f-252">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="1291f-253">Výstup sestavení obsahuje *RazorUIClassLib. dll* a *RazorUIClassLib. views. dll*.</span><span class="sxs-lookup"><span data-stu-id="1291f-253">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="1291f-254">*RazorUIClassLib. views. dll* obsahuje zkompilovaný obsah Razor.</span><span class="sxs-lookup"><span data-stu-id="1291f-254">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="1291f-255">Použití knihovny uživatelského rozhraní Razor z projektu pro stránky Razor</span><span class="sxs-lookup"><span data-stu-id="1291f-255">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1291f-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1291f-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1291f-257">Vytvoření webové aplikace stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="1291f-257">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="1291f-258">Z **Průzkumník řešení**klikněte pravým tlačítkem myši na řešení > **Přidat** >**Nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="1291f-258">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="1291f-259">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="1291f-259">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="1291f-260">Pojmenujte aplikaci **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="1291f-260">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="1291f-261">Ověřte, zda je vybrána **ASP.NET Core 2,1** nebo novější.</span><span class="sxs-lookup"><span data-stu-id="1291f-261">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="1291f-262">Vyberte **Webová aplikace** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="1291f-262">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="1291f-263">Z **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte **nastavit jako spouštěný projekt**.</span><span class="sxs-lookup"><span data-stu-id="1291f-263">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="1291f-264">Z **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte **závislosti sestavení** > **závislosti projektu**.</span><span class="sxs-lookup"><span data-stu-id="1291f-264">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="1291f-265">Ověřte **RazorUIClassLib** jako závislost **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="1291f-265">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="1291f-266">V **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte **Přidat** > **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="1291f-266">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="1291f-267">V dialogovém okně **Správce odkazů** ověřte **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="1291f-267">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="1291f-268">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1291f-268">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1291f-269">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="1291f-269">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1291f-270">Vytvoření webové aplikace Razor Pages a souboru řešení obsahujícího aplikaci Razor Pages a RCL:</span><span class="sxs-lookup"><span data-stu-id="1291f-270">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="1291f-271">Sestavení a spuštění webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="1291f-271">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="1291f-272">WebApp1 testu</span><span class="sxs-lookup"><span data-stu-id="1291f-272">Test WebApp1</span></span>

<span data-ttu-id="1291f-273">Přejděte na `/MyFeature/Page1` a ověřte, že se knihovna tříd uživatelského rozhraní Razor používá.</span><span class="sxs-lookup"><span data-stu-id="1291f-273">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="1291f-274">Přepsání, zobrazení, částečná zobrazení a stránky</span><span class="sxs-lookup"><span data-stu-id="1291f-274">Override views, partial views, and pages</span></span>

<span data-ttu-id="1291f-275">V případě, že je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka Razor, má přednost označení Razor (soubor *. cshtml* ) ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1291f-275">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="1291f-276">Například přidejte *WebApp1/areas/MyFeature/Pages/Page1. cshtml* do WebApp1 a Page1 v WebApp1 bude mít přednost před Page1 v RCL.</span><span class="sxs-lookup"><span data-stu-id="1291f-276">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="1291f-277">V ukázce stažení, přejmenujte *WebApp1/areas/MyFeature2* na *WebApp1/areas/MyFeature* na test priority.</span><span class="sxs-lookup"><span data-stu-id="1291f-277">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="1291f-278">Zkopírujte částečné zobrazení *RazorUIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml* do *WebApp1/areas/MyFeature/Pages/shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1291f-278">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="1291f-279">Aktualizace značky k označení nového umístění.</span><span class="sxs-lookup"><span data-stu-id="1291f-279">Update the markup to indicate the new location.</span></span> <span data-ttu-id="1291f-280">Sestavení a spuštění aplikace a zkontrolujte, že verze aplikace s částečným se používá.</span><span class="sxs-lookup"><span data-stu-id="1291f-280">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="1291f-281">Rozložení stránek RCL</span><span class="sxs-lookup"><span data-stu-id="1291f-281">RCL Pages layout</span></span>

<span data-ttu-id="1291f-282">Pokud chcete odkazovat na obsah RCL, jako by byl *součástí složky webových* aplikací, vytvořte projekt RCL s následující strukturou souborů:</span><span class="sxs-lookup"><span data-stu-id="1291f-282">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="1291f-283">*RazorUIClassLib/stránky*</span><span class="sxs-lookup"><span data-stu-id="1291f-283">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="1291f-284">*RazorUIClassLib/stránky/sdílené*</span><span class="sxs-lookup"><span data-stu-id="1291f-284">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="1291f-285">Předpokládejme, že *RazorUIClassLib/Pages/Shared* obsahují dva částečné soubory: *_Header. cshtml* a *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1291f-285">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="1291f-286">Do souboru *_Layout. cshtml* se dají přidat `<partial>` značky:</span><span class="sxs-lookup"><span data-stu-id="1291f-286">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1291f-287">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1291f-287">Additional resources</span></span>

* <xref:blazor/class-libraries>
