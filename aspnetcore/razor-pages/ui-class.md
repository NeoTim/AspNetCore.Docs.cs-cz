---
title: Opakovaně použitelné Razor uživatelského rozhraní v knihovnách tříd pomocí ASP.NET Core
author: Rick-Anderson
description: Vysvětluje, jak vytvářet opakovaně použitelné uživatelské rozhraní Razor pomocí částečných zobrazení v knihovně tříd v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/08/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: d656e924033f1b217cdd8c86f7d00411c5d71beb
ms.sourcegitcommit: 73a451e9a58ac7102f90b608d661d8c23dd9bbaf
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037586"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="3402c-103">Vytvoření opakovaně použitelného uživatelského rozhraní pomocí projektu knihovny tříd Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3402c-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="3402c-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3402c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3402c-105">Zobrazení Razor, stránky, řadiče, modely stránek, [komponenty Razor](xref:blazor/class-libraries), [zobrazení komponent](xref:mvc/views/view-components)a datové modely lze integrovat do knihovny tříd Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="3402c-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="3402c-106">RCL můžete zabalit a znovu použít.</span><span class="sxs-lookup"><span data-stu-id="3402c-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="3402c-107">Aplikace můžete zahrnout RCL a přepsání, zobrazení a stránky, které obsahuje.</span><span class="sxs-lookup"><span data-stu-id="3402c-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="3402c-108">Při zobrazení, částečná zobrazení nebo stránky Razor se nachází v webové aplikace a RCL kód Razor ( *.cshtml* soubor) na webu aplikace má přednost.</span><span class="sxs-lookup"><span data-stu-id="3402c-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="3402c-109">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3402c-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="3402c-110">Vytvoření knihovny tříd obsahující Razor uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="3402c-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3402c-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3402c-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3402c-112">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="3402c-112">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3402c-113">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3402c-113">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="3402c-114">Název knihovny (například "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="3402c-114">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="3402c-115">Aby se zabránilo kolize názvů souborů s knihovnou generované zobrazení, ujistěte se ale nekončí název knihovny v `.Views`.</span><span class="sxs-lookup"><span data-stu-id="3402c-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="3402c-116">Ověřte, zda je vybrána **ASP.NET Core 3,0** nebo novější.</span><span class="sxs-lookup"><span data-stu-id="3402c-116">Verify **ASP.NET Core 3.0** or later is selected.</span></span>
* <span data-ttu-id="3402c-117">Vyberte **knihovnu tříd Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="3402c-117">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="3402c-118">Šablona knihovny tříd Razor (RCL) je ve výchozím nastavení standardně pro vývoj komponent Razor.</span><span class="sxs-lookup"><span data-stu-id="3402c-118">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="3402c-119">Možnost šablony v aplikaci Visual Studio poskytuje podporu šablon pro stránky a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3402c-119">A template option in Visual Studio provides template support for pages and views.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3402c-120">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3402c-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3402c-121">Z příkazového řádku, spusťte `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="3402c-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="3402c-122">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3402c-122">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="3402c-123">Šablona knihovny tříd Razor (RCL) je ve výchozím nastavení standardně pro vývoj komponent Razor.</span><span class="sxs-lookup"><span data-stu-id="3402c-123">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="3402c-124">Pokud chcete poskytovat podporu pro stránky a zobrazení, předejte možnost `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`).</span><span class="sxs-lookup"><span data-stu-id="3402c-124">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="3402c-125">Další informace najdete v tématu [dotnet nové](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="3402c-125">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="3402c-126">Aby se zabránilo kolize názvů souborů s knihovnou generované zobrazení, ujistěte se ale nekončí název knihovny v `.Views`.</span><span class="sxs-lookup"><span data-stu-id="3402c-126">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="3402c-127">Přidáte soubory Razor RCL.</span><span class="sxs-lookup"><span data-stu-id="3402c-127">Add Razor files to the RCL.</span></span>

<span data-ttu-id="3402c-128">Šablony ASP.NET Core předpokládat RCL obsah je *oblasti* složky.</span><span class="sxs-lookup"><span data-stu-id="3402c-128">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="3402c-129">Pokud chcete vytvořit RCL, který zpřístupňuje obsah v `~/Pages` místo `~/Areas/Pages`, přečtěte si téma [rozložení stránek RCL](#rcl-pages-layout) .</span><span class="sxs-lookup"><span data-stu-id="3402c-129">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="3402c-130">Odkaz na obsah RCL</span><span class="sxs-lookup"><span data-stu-id="3402c-130">Reference RCL content</span></span>

<span data-ttu-id="3402c-131">RCL lze odkazovat pomocí:</span><span class="sxs-lookup"><span data-stu-id="3402c-131">The RCL can be referenced by:</span></span>

* <span data-ttu-id="3402c-132">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="3402c-132">NuGet package.</span></span> <span data-ttu-id="3402c-133">V tématu [balíčky NuGet vytváření](/nuget/create-packages/creating-a-package) a [se příkaz dotnet add package](/dotnet/core/tools/dotnet-add-package) a [vytvoření a publikování balíčku NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="3402c-133">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="3402c-134">*{ProjectName} .csproj*.</span><span class="sxs-lookup"><span data-stu-id="3402c-134">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="3402c-135">Zobrazit [dotnet-přidat odkaz na](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="3402c-135">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="3402c-136">Přepsání, zobrazení, částečná zobrazení a stránky</span><span class="sxs-lookup"><span data-stu-id="3402c-136">Override views, partial views, and pages</span></span>

<span data-ttu-id="3402c-137">Při zobrazení, částečná zobrazení nebo stránky Razor se nachází v webové aplikace a RCL kód Razor ( *.cshtml* soubor) na webu aplikace má přednost.</span><span class="sxs-lookup"><span data-stu-id="3402c-137">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="3402c-138">Například přidejte *WebApp1/areas/MyFeature/Pages/Page1. cshtml* do WebApp1 a Page1 v WebApp1 bude mít přednost před Page1 v RCL.</span><span class="sxs-lookup"><span data-stu-id="3402c-138">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="3402c-139">Ve vzorku ke stažení, přejmenujte *WebApp1/oblasti/MyFeature2* k *WebApp1/oblasti/MyFeature* otestovat prioritu.</span><span class="sxs-lookup"><span data-stu-id="3402c-139">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="3402c-140">Kopírovat *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* částečné zobrazení k *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3402c-140">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="3402c-141">Aktualizace značky k označení nového umístění.</span><span class="sxs-lookup"><span data-stu-id="3402c-141">Update the markup to indicate the new location.</span></span> <span data-ttu-id="3402c-142">Sestavení a spuštění aplikace a zkontrolujte, že verze aplikace s částečným se používá.</span><span class="sxs-lookup"><span data-stu-id="3402c-142">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="3402c-143">Rozložení stránek RCL</span><span class="sxs-lookup"><span data-stu-id="3402c-143">RCL Pages layout</span></span>

<span data-ttu-id="3402c-144">K odkazu RCL obsah, jako by šlo součást webové aplikace *stránky* složku, vytvořte projekt RCL s následující strukturou souboru:</span><span class="sxs-lookup"><span data-stu-id="3402c-144">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="3402c-145">*RazorUIClassLib/stránky*</span><span class="sxs-lookup"><span data-stu-id="3402c-145">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="3402c-146">*RazorUIClassLib/stránek/Shared*</span><span class="sxs-lookup"><span data-stu-id="3402c-146">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="3402c-147">Předpokládejme, že *RazorUIClassLib/stránek/Shared* obsahuje dva soubory částečné: *_Header.cshtml* a *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3402c-147">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="3402c-148">`<partial>` Značky může být přidán do *_Layout.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="3402c-148">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="3402c-149">Vytvoření RCL se statickými prostředky</span><span class="sxs-lookup"><span data-stu-id="3402c-149">Create an RCL with static assets</span></span>

<span data-ttu-id="3402c-150">RCL může vyžadovat doprovodné statické prostředky, které mohou být odkazovány využitím aplikace RCL.</span><span class="sxs-lookup"><span data-stu-id="3402c-150">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="3402c-151">ASP.NET Core umožňuje vytváření RCLs, které zahrnují statické prostředky, které jsou k dispozici pro náročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="3402c-151">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="3402c-152">Chcete-li zahrnout doprovodné materiály jako součást RCL, vytvořte složku *wwwroot* v knihovně tříd a zahrňte všechny požadované soubory do této složky.</span><span class="sxs-lookup"><span data-stu-id="3402c-152">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="3402c-153">Při balení RCL jsou do balíčku automaticky zahrnuty všechny doprovodné materiály ve složce *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="3402c-153">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="3402c-154">Vyloučení statických prostředků</span><span class="sxs-lookup"><span data-stu-id="3402c-154">Exclude static assets</span></span>

<span data-ttu-id="3402c-155">Chcete-li vyloučit statické prostředky, přidejte požadovanou cestu vyloučení do skupiny vlastností `$(DefaultItemExcludes)` v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="3402c-155">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="3402c-156">Jednotlivé položky oddělte středníkem (`;`).</span><span class="sxs-lookup"><span data-stu-id="3402c-156">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="3402c-157">V následujícím příkladu není šablona *lib. CSS* ve složce *wwwroot* považována za statický prostředek a není obsažena v publikovaném RCL:</span><span class="sxs-lookup"><span data-stu-id="3402c-157">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="3402c-158">Integrace TypeScriptu</span><span class="sxs-lookup"><span data-stu-id="3402c-158">Typescript integration</span></span>

<span data-ttu-id="3402c-159">Zahrnutí souborů TypeScriptu do RCL:</span><span class="sxs-lookup"><span data-stu-id="3402c-159">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="3402c-160">Soubory TypeScript ( *. TS*) umístěte mimo složku *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="3402c-160">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="3402c-161">Umístěte například soubory do složky *klienta* .</span><span class="sxs-lookup"><span data-stu-id="3402c-161">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="3402c-162">Nakonfigurujte výstup sestavení TypeScriptu pro složku *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="3402c-162">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="3402c-163">Nastavte vlastnost `TypescriptOutDir` uvnitř `PropertyGroup` v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="3402c-163">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="3402c-164">Přidejte cíl TypeScript jako závislost `ResolveCurrentProjectStaticWebAssets` a přidejte následující cíl do `PropertyGroup` v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="3402c-164">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     TypeScriptCompile;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="3402c-165">Využití obsahu z odkazovaného RCL</span><span class="sxs-lookup"><span data-stu-id="3402c-165">Consume content from a referenced RCL</span></span>

<span data-ttu-id="3402c-166">Soubory zahrnuté ve složce *wwwroot* v RCL jsou zpřístupněné aplikacím s předponou `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="3402c-166">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="3402c-167">Například knihovna s názvem *Razor. Class. lib* má za následek cestu k statickému obsahu v `_content/Razor.Class.Lib/`.</span><span class="sxs-lookup"><span data-stu-id="3402c-167">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span>

<span data-ttu-id="3402c-168">Vybírající aplikace odkazuje na statické prostředky poskytované knihovnou s `<script>`, `<style>`, `<img>` a dalšími značkami HTML.</span><span class="sxs-lookup"><span data-stu-id="3402c-168">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="3402c-169">Vybírající aplikace musí mít povolenou [podporu statických souborů](xref:fundamentals/static-files) v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3402c-169">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="3402c-170">Při spuštění náročné aplikace z výstupu sestavení (`dotnet run`) jsou ve výchozím nastavení ve vývojovém prostředí povoleny statické webové prostředky.</span><span class="sxs-lookup"><span data-stu-id="3402c-170">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="3402c-171">Pro podporu prostředků v jiných prostředích při spuštění z výstupu sestavení volejte `UseStaticWebAssets` na tvůrci hostitele v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3402c-171">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="3402c-172">Při spuštění aplikace z publikovaného výstupu se nevyžaduje volání `UseStaticWebAssets` (`dotnet publish`).</span><span class="sxs-lookup"><span data-stu-id="3402c-172">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="3402c-173">Vývojový tok pro více projektů</span><span class="sxs-lookup"><span data-stu-id="3402c-173">Multi-project development flow</span></span>

<span data-ttu-id="3402c-174">Když pracujete na náročné aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3402c-174">When the consuming app runs:</span></span>

* <span data-ttu-id="3402c-175">Prostředky v RCL zůstanou v původních složkách.</span><span class="sxs-lookup"><span data-stu-id="3402c-175">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="3402c-176">Prostředky se nepřesunou do nenáročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="3402c-176">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="3402c-177">Jakákoli změna ve složce *WWWROOT* RCL se projeví v aplikaci po resestavení RCL a bez opětovného sestavení náročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="3402c-177">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="3402c-178">Při sestavení RCL je vytvořen manifest, který popisuje umístění statických webových prostředků.</span><span class="sxs-lookup"><span data-stu-id="3402c-178">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="3402c-179">Vybírající aplikace načte manifest za běhu za účelem využití prostředků z odkazovaných projektů a balíčků.</span><span class="sxs-lookup"><span data-stu-id="3402c-179">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="3402c-180">Když se do RCL přidá nový Asset, musí se RCL znovu sestavit, aby se aktualizoval jeho manifest předtím, než bude mít k novému assetu přístup.</span><span class="sxs-lookup"><span data-stu-id="3402c-180">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="3402c-181">Publikování</span><span class="sxs-lookup"><span data-stu-id="3402c-181">Publish</span></span>

<span data-ttu-id="3402c-182">Po publikování aplikace se doprovodné prostředky ze všech odkazovaných projektů a balíčků zkopírují do složky *wwwroot* publikované aplikace v části `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="3402c-182">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3402c-183">Zobrazení Razor, stránky, řadiče, modely stránek, [komponenty Razor](xref:blazor/class-libraries), [zobrazení komponent](xref:mvc/views/view-components)a datové modely lze integrovat do knihovny tříd Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="3402c-183">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="3402c-184">RCL můžete zabalit a znovu použít.</span><span class="sxs-lookup"><span data-stu-id="3402c-184">The RCL can be packaged and reused.</span></span> <span data-ttu-id="3402c-185">Aplikace můžete zahrnout RCL a přepsání, zobrazení a stránky, které obsahuje.</span><span class="sxs-lookup"><span data-stu-id="3402c-185">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="3402c-186">Při zobrazení, částečná zobrazení nebo stránky Razor se nachází v webové aplikace a RCL kód Razor ( *.cshtml* soubor) na webu aplikace má přednost.</span><span class="sxs-lookup"><span data-stu-id="3402c-186">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="3402c-187">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3402c-187">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="3402c-188">Vytvoření knihovny tříd obsahující Razor uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="3402c-188">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3402c-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3402c-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3402c-190">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="3402c-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3402c-191">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3402c-191">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="3402c-192">Název knihovny (například "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="3402c-192">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="3402c-193">Aby se zabránilo kolize názvů souborů s knihovnou generované zobrazení, ujistěte se ale nekončí název knihovny v `.Views`.</span><span class="sxs-lookup"><span data-stu-id="3402c-193">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="3402c-194">Ověřte **ASP.NET Core 2.1** nebo novější je vybrána.</span><span class="sxs-lookup"><span data-stu-id="3402c-194">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="3402c-195">Vyberte **knihovnu tříd Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="3402c-195">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="3402c-196">RCL má následující soubor projektu:</span><span class="sxs-lookup"><span data-stu-id="3402c-196">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3402c-197">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3402c-197">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3402c-198">Z příkazového řádku, spusťte `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="3402c-198">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="3402c-199">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3402c-199">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="3402c-200">Další informace najdete v tématu [dotnet nové](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="3402c-200">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="3402c-201">Aby se zabránilo kolize názvů souborů s knihovnou generované zobrazení, ujistěte se ale nekončí název knihovny v `.Views`.</span><span class="sxs-lookup"><span data-stu-id="3402c-201">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="3402c-202">Přidáte soubory Razor RCL.</span><span class="sxs-lookup"><span data-stu-id="3402c-202">Add Razor files to the RCL.</span></span>

<span data-ttu-id="3402c-203">Šablony ASP.NET Core předpokládat RCL obsah je *oblasti* složky.</span><span class="sxs-lookup"><span data-stu-id="3402c-203">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="3402c-204">Pokud chcete vytvořit RCL, který zpřístupňuje obsah v `~/Pages` místo `~/Areas/Pages`, přečtěte si téma [rozložení stránek RCL](#rcl-pages-layout) .</span><span class="sxs-lookup"><span data-stu-id="3402c-204">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="3402c-205">Odkaz na obsah RCL</span><span class="sxs-lookup"><span data-stu-id="3402c-205">Reference RCL content</span></span>

<span data-ttu-id="3402c-206">RCL lze odkazovat pomocí:</span><span class="sxs-lookup"><span data-stu-id="3402c-206">The RCL can be referenced by:</span></span>

* <span data-ttu-id="3402c-207">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="3402c-207">NuGet package.</span></span> <span data-ttu-id="3402c-208">V tématu [balíčky NuGet vytváření](/nuget/create-packages/creating-a-package) a [se příkaz dotnet add package](/dotnet/core/tools/dotnet-add-package) a [vytvoření a publikování balíčku NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="3402c-208">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="3402c-209">*{ProjectName} .csproj*.</span><span class="sxs-lookup"><span data-stu-id="3402c-209">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="3402c-210">Zobrazit [dotnet-přidat odkaz na](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="3402c-210">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="3402c-211">Návod: Vytvoření projektu RCL a použití z Razor Pagesho projektu</span><span class="sxs-lookup"><span data-stu-id="3402c-211">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="3402c-212">Můžete stáhnout [dokončený projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) a otestovat ho namísto jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="3402c-212">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="3402c-213">Vzorku ke stažení obsahuje další kódu a odkazy, které usnadňuje testování projektu.</span><span class="sxs-lookup"><span data-stu-id="3402c-213">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="3402c-214">Zanecháte zpětnou vazbu v [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/6098) s komentáře na stažení ukázky a podrobné pokyny.</span><span class="sxs-lookup"><span data-stu-id="3402c-214">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="3402c-215">Testování aplikace ke stažení</span><span class="sxs-lookup"><span data-stu-id="3402c-215">Test the download app</span></span>

<span data-ttu-id="3402c-216">Pokud jste nestáhli dokončené aplikace a by místo toho vytvořte projekt návodu, pokračujte [další části](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="3402c-216">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3402c-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3402c-217">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3402c-218">Otevřít *.sln* souboru v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3402c-218">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="3402c-219">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3402c-219">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3402c-220">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3402c-220">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3402c-221">Z příkazového řádku v *rozhraní příkazového řádku* adresáře, vytvářet RCL a webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="3402c-221">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="3402c-222">Přesunout *WebApp1* adresáře a spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3402c-222">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="3402c-223">Postupujte podle pokynů v [WebApp1 testu](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="3402c-223">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="3402c-224">Vytvoření RCL</span><span class="sxs-lookup"><span data-stu-id="3402c-224">Create an RCL</span></span>

<span data-ttu-id="3402c-225">V této části se vytvoří RCL.</span><span class="sxs-lookup"><span data-stu-id="3402c-225">In this section, an RCL is created.</span></span> <span data-ttu-id="3402c-226">Soubory Razor jsou přidány do RCL.</span><span class="sxs-lookup"><span data-stu-id="3402c-226">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3402c-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3402c-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3402c-228">Vytvoření projektu RCL:</span><span class="sxs-lookup"><span data-stu-id="3402c-228">Create the RCL project:</span></span>

* <span data-ttu-id="3402c-229">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="3402c-229">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3402c-230">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3402c-230">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="3402c-231">Pojmenujte aplikaci **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="3402c-231">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="3402c-232">Ověřte **ASP.NET Core 2.1** nebo novější je vybrána.</span><span class="sxs-lookup"><span data-stu-id="3402c-232">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="3402c-233">Vyberte **knihovnu tříd Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="3402c-233">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="3402c-234">Přidejte do ní soubor částečného zobrazení Razor *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3402c-234">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3402c-235">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3402c-235">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3402c-236">Z příkazového řádku spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3402c-236">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="3402c-237">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="3402c-237">The preceding commands:</span></span>

* <span data-ttu-id="3402c-238">Vytvoří `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="3402c-238">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="3402c-239">Vytvoří stránku Razor _TEXT a přidá jej RCL.</span><span class="sxs-lookup"><span data-stu-id="3402c-239">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="3402c-240">`-np` Parametr vytvoří, aniž by `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="3402c-240">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="3402c-241">Vytvoří [soubor _ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) soubor a přidá jej RCL.</span><span class="sxs-lookup"><span data-stu-id="3402c-241">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="3402c-242">*Soubor _ViewStart.cshtml* soubor je vyžadován pro rozložení stránky Razor projektu (která se přidá v další části).</span><span class="sxs-lookup"><span data-stu-id="3402c-242">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="3402c-243">Přidání Razor souborů a složek do projektu</span><span class="sxs-lookup"><span data-stu-id="3402c-243">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="3402c-244">Nahraďte kód v *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3402c-244">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="3402c-245">Nahraďte kód v *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3402c-245">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="3402c-246">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` je potřeba používat částečné zobrazení (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="3402c-246">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="3402c-247">Místo včetně `@addTagHelper` direktiv, můžete přidat *_ViewImports.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="3402c-247">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="3402c-248">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3402c-248">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="3402c-249">Další informace o *_ViewImports.cshtml*, naleznete v tématu [import sdílených direktivy](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="3402c-249">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="3402c-250">Sestavení knihovny tříd pro ověření, že zde nejsou žádné chyby kompilátoru:</span><span class="sxs-lookup"><span data-stu-id="3402c-250">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="3402c-251">Výstup sestavení obsahuje *RazorUIClassLib.dll* a *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="3402c-251">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="3402c-252">*RazorUIClassLib.Views.dll* obsahuje kompilovaný obsah Razor.</span><span class="sxs-lookup"><span data-stu-id="3402c-252">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="3402c-253">Použití knihovny uživatelského rozhraní Razor z projektu pro stránky Razor</span><span class="sxs-lookup"><span data-stu-id="3402c-253">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3402c-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3402c-254">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3402c-255">Vytvoření webové aplikace stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="3402c-255">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="3402c-256">V **Průzkumník řešení**klikněte pravým tlačítkem na řešení > **přidat** **Nový projekt**>.</span><span class="sxs-lookup"><span data-stu-id="3402c-256">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="3402c-257">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3402c-257">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="3402c-258">Pojmenujte aplikaci **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="3402c-258">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="3402c-259">Ověřte **ASP.NET Core 2.1** nebo novější je vybrána.</span><span class="sxs-lookup"><span data-stu-id="3402c-259">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="3402c-260">Vyberte možnost **Webová aplikace** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="3402c-260">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="3402c-261">Z **Průzkumníka řešení**, klikněte pravým tlačítkem na **WebApp1** a vyberte **nastavit jako spouštěný projekt**.</span><span class="sxs-lookup"><span data-stu-id="3402c-261">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="3402c-262">V **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte závislosti **sestavení** > **závislosti projektu**.</span><span class="sxs-lookup"><span data-stu-id="3402c-262">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="3402c-263">Zkontrolujte **RazorUIClassLib** jako závislost **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="3402c-263">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="3402c-264">V **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte **Přidat** **odkaz**>.</span><span class="sxs-lookup"><span data-stu-id="3402c-264">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="3402c-265">V dialogovém okně **Správce odkazů** ověřte **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="3402c-265">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="3402c-266">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3402c-266">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3402c-267">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3402c-267">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3402c-268">Vytvoření webové aplikace Razor Pages a souboru řešení obsahujícího aplikaci Razor Pages a RCL:</span><span class="sxs-lookup"><span data-stu-id="3402c-268">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="3402c-269">Sestavení a spuštění webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="3402c-269">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="3402c-270">WebApp1 testu</span><span class="sxs-lookup"><span data-stu-id="3402c-270">Test WebApp1</span></span>

<span data-ttu-id="3402c-271">Přejděte na `/MyFeature/Page1` a ověřte, zda se knihovna tříd uživatelského rozhraní Razor používá.</span><span class="sxs-lookup"><span data-stu-id="3402c-271">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="3402c-272">Přepsání, zobrazení, částečná zobrazení a stránky</span><span class="sxs-lookup"><span data-stu-id="3402c-272">Override views, partial views, and pages</span></span>

<span data-ttu-id="3402c-273">Při zobrazení, částečná zobrazení nebo stránky Razor se nachází v webové aplikace a RCL kód Razor ( *.cshtml* soubor) na webu aplikace má přednost.</span><span class="sxs-lookup"><span data-stu-id="3402c-273">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="3402c-274">Například přidejte *WebApp1/areas/MyFeature/Pages/Page1. cshtml* do WebApp1 a Page1 v WebApp1 bude mít přednost před Page1 v RCL.</span><span class="sxs-lookup"><span data-stu-id="3402c-274">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="3402c-275">Ve vzorku ke stažení, přejmenujte *WebApp1/oblasti/MyFeature2* k *WebApp1/oblasti/MyFeature* otestovat prioritu.</span><span class="sxs-lookup"><span data-stu-id="3402c-275">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="3402c-276">Kopírovat *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* částečné zobrazení k *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3402c-276">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="3402c-277">Aktualizace značky k označení nového umístění.</span><span class="sxs-lookup"><span data-stu-id="3402c-277">Update the markup to indicate the new location.</span></span> <span data-ttu-id="3402c-278">Sestavení a spuštění aplikace a zkontrolujte, že verze aplikace s částečným se používá.</span><span class="sxs-lookup"><span data-stu-id="3402c-278">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="3402c-279">Rozložení stránek RCL</span><span class="sxs-lookup"><span data-stu-id="3402c-279">RCL Pages layout</span></span>

<span data-ttu-id="3402c-280">K odkazu RCL obsah, jako by šlo součást webové aplikace *stránky* složku, vytvořte projekt RCL s následující strukturou souboru:</span><span class="sxs-lookup"><span data-stu-id="3402c-280">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="3402c-281">*RazorUIClassLib/stránky*</span><span class="sxs-lookup"><span data-stu-id="3402c-281">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="3402c-282">*RazorUIClassLib/stránek/Shared*</span><span class="sxs-lookup"><span data-stu-id="3402c-282">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="3402c-283">Předpokládejme, že *RazorUIClassLib/stránek/Shared* obsahuje dva soubory částečné: *_Header.cshtml* a *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3402c-283">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="3402c-284">`<partial>` Značky může být přidán do *_Layout.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="3402c-284">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end
