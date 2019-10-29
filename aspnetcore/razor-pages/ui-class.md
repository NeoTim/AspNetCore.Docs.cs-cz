---
title: Opakovaně použitelné uživatelské rozhraní Razor v knihovnách tříd s ASP.NET Core
author: Rick-Anderson
description: Vysvětluje, jak vytvořit opakovaně použitelné uživatelské rozhraní Razor pomocí částečných zobrazení v knihovně tříd v ASP.NET Core.
ms.author: riande
ms.date: 10/26/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: ff12eea5406c4f5392a466728741000e3dd16fc1
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034223"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="84c38-103">Vytvoření opakovaně použitelného uživatelského rozhraní pomocí projektu knihovny tříd Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84c38-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="84c38-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="84c38-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84c38-105">Zobrazení Razor, stránky, řadiče, modely stránek, [komponenty Razor](xref:blazor/class-libraries), [zobrazení komponent](xref:mvc/views/view-components)a datové modely lze integrovat do knihovny tříd Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="84c38-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="84c38-106">RCL může být zabalen a znovu použit.</span><span class="sxs-lookup"><span data-stu-id="84c38-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="84c38-107">Aplikace mohou zahrnovat RCL a přepsat zobrazení a stránky, které obsahuje.</span><span class="sxs-lookup"><span data-stu-id="84c38-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="84c38-108">V případě, že je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka Razor, má přednost označení Razor (soubor *. cshtml* ) ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c38-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="84c38-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="84c38-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="84c38-110">Vytvoření knihovny tříd obsahující uživatelské rozhraní Razor</span><span class="sxs-lookup"><span data-stu-id="84c38-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="84c38-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84c38-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="84c38-112">V aplikaci Visual Studio vyberte **vytvořit nový nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="84c38-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="84c38-113">> **Další**vyberte **knihovnu tříd Razor** .</span><span class="sxs-lookup"><span data-stu-id="84c38-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="84c38-114">Pojmenujte knihovnu (například "RazorClassLib"), > **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="84c38-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="84c38-115">Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views`.</span><span class="sxs-lookup"><span data-stu-id="84c38-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="84c38-116">Pokud potřebujete podporovat zobrazení, vyberte možnost **stránky podpory a zobrazení** .</span><span class="sxs-lookup"><span data-stu-id="84c38-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="84c38-117">Ve výchozím nastavení jsou podporovány pouze Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="84c38-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="84c38-118">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="84c38-118">Select **Create**.</span></span>

<span data-ttu-id="84c38-119">Šablona knihovny tříd Razor (RCL) je ve výchozím nastavení standardně pro vývoj komponent Razor.</span><span class="sxs-lookup"><span data-stu-id="84c38-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="84c38-120">Možnost **stránky podpory a zobrazení** podporuje stránky a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="84c38-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="84c38-121">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="84c38-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="84c38-122">Z příkazového řádku spusťte `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="84c38-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="84c38-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="84c38-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="84c38-124">Šablona knihovny tříd Razor (RCL) je ve výchozím nastavení standardně pro vývoj komponent Razor.</span><span class="sxs-lookup"><span data-stu-id="84c38-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="84c38-125">Pokud chcete poskytovat podporu pro stránky a zobrazení, předejte možnost `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`).</span><span class="sxs-lookup"><span data-stu-id="84c38-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="84c38-126">Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="84c38-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="84c38-127">Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views`.</span><span class="sxs-lookup"><span data-stu-id="84c38-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="84c38-128">Přidejte soubory Razor do RCL.</span><span class="sxs-lookup"><span data-stu-id="84c38-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="84c38-129">Šablony ASP.NET Core předpokládají, že obsah RCL je ve složce *oblasti* .</span><span class="sxs-lookup"><span data-stu-id="84c38-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="84c38-130">Pokud chcete vytvořit RCL, který zpřístupňuje obsah v `~/Pages` místo `~/Areas/Pages`, přečtěte si téma [rozložení stránek RCL](#rcl-pages-layout) .</span><span class="sxs-lookup"><span data-stu-id="84c38-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="84c38-131">Odkaz na obsah RCL</span><span class="sxs-lookup"><span data-stu-id="84c38-131">Reference RCL content</span></span>

<span data-ttu-id="84c38-132">Na RCL může odkazovat:</span><span class="sxs-lookup"><span data-stu-id="84c38-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="84c38-133">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="84c38-133">NuGet package.</span></span> <span data-ttu-id="84c38-134">Viz téma [vytváření balíčků NuGet](/nuget/create-packages/creating-a-package) a [dotnet přidání balíčku](/dotnet/core/tools/dotnet-add-package) a [Vytvoření a publikování balíčku NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="84c38-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="84c38-135">*{ProjectName}. csproj*.</span><span class="sxs-lookup"><span data-stu-id="84c38-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="84c38-136">Viz [dotnet – přidat odkaz](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="84c38-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="84c38-137">Přepsat zobrazení, částečná zobrazení a stránky</span><span class="sxs-lookup"><span data-stu-id="84c38-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="84c38-138">V případě, že je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka Razor, má přednost označení Razor (soubor *. cshtml* ) ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c38-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="84c38-139">Například přidejte *WebApp1/areas/MyFeature/Pages/Page1. cshtml* do WebApp1 a Page1 v WebApp1 bude mít přednost před Page1 v RCL.</span><span class="sxs-lookup"><span data-stu-id="84c38-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="84c38-140">V ukázce stažení, přejmenujte *WebApp1/areas/MyFeature2* na *WebApp1/areas/MyFeature* na test priority.</span><span class="sxs-lookup"><span data-stu-id="84c38-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="84c38-141">Zkopírujte částečné zobrazení *RazorUIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml* na *WebApp1/areas/MyFeature/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="84c38-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="84c38-142">Aktualizujte kód tak, aby označoval nové umístění.</span><span class="sxs-lookup"><span data-stu-id="84c38-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="84c38-143">Sestavte a spusťte aplikaci, abyste ověřili, že se používá částečná verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="84c38-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="84c38-144">Rozložení stránek RCL</span><span class="sxs-lookup"><span data-stu-id="84c38-144">RCL Pages layout</span></span>

<span data-ttu-id="84c38-145">Pokud chcete odkazovat na obsah RCL, jako by byl *součástí složky webových* aplikací, vytvořte projekt RCL s následující strukturou souborů:</span><span class="sxs-lookup"><span data-stu-id="84c38-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="84c38-146">*RazorUIClassLib/stránky*</span><span class="sxs-lookup"><span data-stu-id="84c38-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="84c38-147">*RazorUIClassLib/stránky/sdílené*</span><span class="sxs-lookup"><span data-stu-id="84c38-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="84c38-148">Předpokládejme, že *RazorUIClassLib/Pages/Shared* obsahují dva částečné soubory: *_Header. cshtml* a *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="84c38-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="84c38-149">Do souboru *_Layout. cshtml* se dají přidat značky `<partial>`:</span><span class="sxs-lookup"><span data-stu-id="84c38-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="84c38-150">Vytvoření RCL se statickými prostředky</span><span class="sxs-lookup"><span data-stu-id="84c38-150">Create an RCL with static assets</span></span>

<span data-ttu-id="84c38-151">RCL může vyžadovat doprovodné statické prostředky, které mohou být odkazovány využitím aplikace RCL.</span><span class="sxs-lookup"><span data-stu-id="84c38-151">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="84c38-152">ASP.NET Core umožňuje vytváření RCLs, které zahrnují statické prostředky, které jsou k dispozici pro náročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="84c38-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="84c38-153">Chcete-li zahrnout doprovodné materiály jako součást RCL, vytvořte složku *wwwroot* v knihovně tříd a zahrňte všechny požadované soubory do této složky.</span><span class="sxs-lookup"><span data-stu-id="84c38-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="84c38-154">Při balení RCL jsou do balíčku automaticky zahrnuty všechny doprovodné materiály ve složce *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="84c38-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="84c38-155">Vyloučení statických prostředků</span><span class="sxs-lookup"><span data-stu-id="84c38-155">Exclude static assets</span></span>

<span data-ttu-id="84c38-156">Chcete-li vyloučit statické prostředky, přidejte požadovanou cestu vyloučení do skupiny vlastností `$(DefaultItemExcludes)` v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="84c38-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="84c38-157">Jednotlivé položky oddělte středníkem (`;`).</span><span class="sxs-lookup"><span data-stu-id="84c38-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="84c38-158">V následujícím příkladu není šablona *lib. CSS* ve složce *wwwroot* považována za statický prostředek a není obsažena v publikovaném RCL:</span><span class="sxs-lookup"><span data-stu-id="84c38-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="84c38-159">Integrace TypeScriptu</span><span class="sxs-lookup"><span data-stu-id="84c38-159">Typescript integration</span></span>

<span data-ttu-id="84c38-160">Zahrnutí souborů TypeScriptu do RCL:</span><span class="sxs-lookup"><span data-stu-id="84c38-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="84c38-161">Soubory TypeScript ( *. TS*) umístěte mimo složku *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="84c38-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="84c38-162">Umístěte například soubory do složky *klienta* .</span><span class="sxs-lookup"><span data-stu-id="84c38-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="84c38-163">Nakonfigurujte výstup sestavení TypeScriptu pro složku *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="84c38-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="84c38-164">Nastavte vlastnost `TypescriptOutDir` uvnitř `PropertyGroup` v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="84c38-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="84c38-165">Přidejte cíl TypeScript jako závislost `ResolveCurrentProjectStaticWebAssets` a přidejte následující cíl do `PropertyGroup` v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="84c38-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="84c38-166">Využití obsahu z odkazovaného RCL</span><span class="sxs-lookup"><span data-stu-id="84c38-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="84c38-167">Soubory zahrnuté ve složce *wwwroot* v RCL jsou zpřístupněné aplikacím, které jsou v předponě `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="84c38-167">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="84c38-168">Například knihovna s názvem *Razor. Class. lib* má za následek cestu k statickému obsahu v `_content/Razor.Class.Lib/`.</span><span class="sxs-lookup"><span data-stu-id="84c38-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span>

<span data-ttu-id="84c38-169">Vybírající aplikace odkazuje na statické prostředky poskytované knihovnou s `<script>`, `<style>`, `<img>` a dalšími značkami HTML.</span><span class="sxs-lookup"><span data-stu-id="84c38-169">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="84c38-170">Vybírající aplikace musí mít povolenou [podporu statických souborů](xref:fundamentals/static-files) v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="84c38-170">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="84c38-171">Při spuštění náročné aplikace z výstupu sestavení (`dotnet run`) jsou ve výchozím nastavení ve vývojovém prostředí povoleny statické webové prostředky.</span><span class="sxs-lookup"><span data-stu-id="84c38-171">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="84c38-172">Pro podporu prostředků v jiných prostředích při spuštění z výstupu sestavení volejte `UseStaticWebAssets` na tvůrci hostitele v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="84c38-172">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="84c38-173">Při spuštění aplikace z publikovaného výstupu se nevyžaduje volání `UseStaticWebAssets` (`dotnet publish`).</span><span class="sxs-lookup"><span data-stu-id="84c38-173">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="84c38-174">Vývojový tok pro více projektů</span><span class="sxs-lookup"><span data-stu-id="84c38-174">Multi-project development flow</span></span>

<span data-ttu-id="84c38-175">Když pracujete na náročné aplikaci:</span><span class="sxs-lookup"><span data-stu-id="84c38-175">When the consuming app runs:</span></span>

* <span data-ttu-id="84c38-176">Prostředky v RCL zůstanou v původních složkách.</span><span class="sxs-lookup"><span data-stu-id="84c38-176">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="84c38-177">Prostředky se nepřesunou do nenáročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="84c38-177">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="84c38-178">Jakákoli změna ve složce *WWWROOT* RCL se projeví v aplikaci po resestavení RCL a bez opětovného sestavení náročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="84c38-178">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="84c38-179">Při sestavení RCL je vytvořen manifest, který popisuje umístění statických webových prostředků.</span><span class="sxs-lookup"><span data-stu-id="84c38-179">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="84c38-180">Vybírající aplikace načte manifest za běhu za účelem využití prostředků z odkazovaných projektů a balíčků.</span><span class="sxs-lookup"><span data-stu-id="84c38-180">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="84c38-181">Když se do RCL přidá nový Asset, musí se RCL znovu sestavit, aby se aktualizoval jeho manifest předtím, než bude mít k novému assetu přístup.</span><span class="sxs-lookup"><span data-stu-id="84c38-181">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="84c38-182">Opětovn</span><span class="sxs-lookup"><span data-stu-id="84c38-182">Publish</span></span>

<span data-ttu-id="84c38-183">Po publikování aplikace se doprovodné prostředky ze všech odkazovaných projektů a balíčků zkopírují do složky *wwwroot* publikované aplikace v části `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="84c38-183">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="84c38-184">Zobrazení Razor, stránky, řadiče, modely stránek, [komponenty Razor](xref:blazor/class-libraries), [zobrazení komponent](xref:mvc/views/view-components)a datové modely lze integrovat do knihovny tříd Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="84c38-184">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="84c38-185">RCL může být zabalen a znovu použit.</span><span class="sxs-lookup"><span data-stu-id="84c38-185">The RCL can be packaged and reused.</span></span> <span data-ttu-id="84c38-186">Aplikace mohou zahrnovat RCL a přepsat zobrazení a stránky, které obsahuje.</span><span class="sxs-lookup"><span data-stu-id="84c38-186">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="84c38-187">V případě, že je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka Razor, má přednost označení Razor (soubor *. cshtml* ) ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c38-187">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="84c38-188">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="84c38-188">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="84c38-189">Vytvoření knihovny tříd obsahující uživatelské rozhraní Razor</span><span class="sxs-lookup"><span data-stu-id="84c38-189">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="84c38-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84c38-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="84c38-191">V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** **projekt**>.</span><span class="sxs-lookup"><span data-stu-id="84c38-191">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="84c38-192">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="84c38-192">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="84c38-193">Pojmenujte knihovnu (například "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="84c38-193">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="84c38-194">Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views`.</span><span class="sxs-lookup"><span data-stu-id="84c38-194">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="84c38-195">Ověřte, zda je vybrána **ASP.NET Core 2,1** nebo novější.</span><span class="sxs-lookup"><span data-stu-id="84c38-195">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="84c38-196">Vyberte **knihovnu tříd Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="84c38-196">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="84c38-197">RCL má následující soubor projektu:</span><span class="sxs-lookup"><span data-stu-id="84c38-197">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="84c38-198">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="84c38-198">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="84c38-199">Z příkazového řádku spusťte `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="84c38-199">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="84c38-200">Příklad:</span><span class="sxs-lookup"><span data-stu-id="84c38-200">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="84c38-201">Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="84c38-201">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="84c38-202">Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views`.</span><span class="sxs-lookup"><span data-stu-id="84c38-202">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="84c38-203">Přidejte soubory Razor do RCL.</span><span class="sxs-lookup"><span data-stu-id="84c38-203">Add Razor files to the RCL.</span></span>

<span data-ttu-id="84c38-204">Šablony ASP.NET Core předpokládají, že obsah RCL je ve složce *oblasti* .</span><span class="sxs-lookup"><span data-stu-id="84c38-204">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="84c38-205">Pokud chcete vytvořit RCL, který zpřístupňuje obsah v `~/Pages` místo `~/Areas/Pages`, přečtěte si téma [rozložení stránek RCL](#rcl-pages-layout) .</span><span class="sxs-lookup"><span data-stu-id="84c38-205">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="84c38-206">Odkaz na obsah RCL</span><span class="sxs-lookup"><span data-stu-id="84c38-206">Reference RCL content</span></span>

<span data-ttu-id="84c38-207">Na RCL může odkazovat:</span><span class="sxs-lookup"><span data-stu-id="84c38-207">The RCL can be referenced by:</span></span>

* <span data-ttu-id="84c38-208">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="84c38-208">NuGet package.</span></span> <span data-ttu-id="84c38-209">Viz téma [vytváření balíčků NuGet](/nuget/create-packages/creating-a-package) a [dotnet přidání balíčku](/dotnet/core/tools/dotnet-add-package) a [Vytvoření a publikování balíčku NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="84c38-209">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="84c38-210">*{ProjectName}. csproj*.</span><span class="sxs-lookup"><span data-stu-id="84c38-210">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="84c38-211">Viz [dotnet – přidat odkaz](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="84c38-211">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="84c38-212">Návod: vytvoření projektu RCL a použití z Razor Pages projektu</span><span class="sxs-lookup"><span data-stu-id="84c38-212">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="84c38-213">Můžete stáhnout [celý projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) a otestovat ho místo jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="84c38-213">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="84c38-214">Ukázkový soubor ke stažení obsahuje další kód a odkazy, které usnadňují testování projektu.</span><span class="sxs-lookup"><span data-stu-id="84c38-214">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="84c38-215">Zpětnou vazbu k [tomuto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/6098) si můžete nechat s vašimi komentáři ke stažení ukázek a podrobnými pokyny.</span><span class="sxs-lookup"><span data-stu-id="84c38-215">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="84c38-216">Testování aplikace pro stažení</span><span class="sxs-lookup"><span data-stu-id="84c38-216">Test the download app</span></span>

<span data-ttu-id="84c38-217">Pokud jste nestáhli dokončenou aplikaci a chcete místo toho vytvořit návodový projekt, přejděte k [Další části](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="84c38-217">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="84c38-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84c38-218">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="84c38-219">Otevřete soubor *. sln* v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="84c38-219">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="84c38-220">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c38-220">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="84c38-221">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="84c38-221">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="84c38-222">Z příkazového řádku v adresáři *CLI* Sestavte RCL a webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c38-222">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="84c38-223">Přejděte do adresáře *WebApp1* a spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="84c38-223">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="84c38-224">Postupujte podle pokynů v části [test WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="84c38-224">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="84c38-225">Vytvoření RCL</span><span class="sxs-lookup"><span data-stu-id="84c38-225">Create an RCL</span></span>

<span data-ttu-id="84c38-226">V této části se vytvoří RCL.</span><span class="sxs-lookup"><span data-stu-id="84c38-226">In this section, an RCL is created.</span></span> <span data-ttu-id="84c38-227">Soubory Razor jsou přidány do RCL.</span><span class="sxs-lookup"><span data-stu-id="84c38-227">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="84c38-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84c38-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="84c38-229">Vytvořte projekt RCL:</span><span class="sxs-lookup"><span data-stu-id="84c38-229">Create the RCL project:</span></span>

* <span data-ttu-id="84c38-230">V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** **projekt**>.</span><span class="sxs-lookup"><span data-stu-id="84c38-230">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="84c38-231">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="84c38-231">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="84c38-232">Pojmenujte aplikaci **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="84c38-232">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="84c38-233">Ověřte, zda je vybrána **ASP.NET Core 2,1** nebo novější.</span><span class="sxs-lookup"><span data-stu-id="84c38-233">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="84c38-234">Vyberte **knihovnu tříd Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="84c38-234">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="84c38-235">Přidejte soubor částečného zobrazení Razor s názvem *RazorUIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="84c38-235">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="84c38-236">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="84c38-236">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="84c38-237">Z příkazového řádku spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="84c38-237">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="84c38-238">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="84c38-238">The preceding commands:</span></span>

* <span data-ttu-id="84c38-239">Vytvoří `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="84c38-239">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="84c38-240">Vytvoří stránku _Message Razor a přidá ji do RCL.</span><span class="sxs-lookup"><span data-stu-id="84c38-240">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="84c38-241">Parametr `-np` vytvoří stránku bez `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="84c38-241">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="84c38-242">Vytvoří soubor [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) a přidá ho do RCL.</span><span class="sxs-lookup"><span data-stu-id="84c38-242">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="84c38-243">K použití rozložení Razor Pagesho projektu (který je přidán v další části) je vyžadován soubor *_ViewStart. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="84c38-243">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="84c38-244">Přidat soubory a složky Razor do projektu</span><span class="sxs-lookup"><span data-stu-id="84c38-244">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="84c38-245">Nahraďte označení v *RazorUIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="84c38-245">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="84c38-246">Nahraďte označení v *RazorUIClassLib/areas/MyFeature/Pages/Page1. cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="84c38-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="84c38-247">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` se vyžaduje pro použití částečného zobrazení (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="84c38-247">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="84c38-248">Místo zahrnutí direktivy `@addTagHelper` můžete přidat soubor *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="84c38-248">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="84c38-249">Příklad:</span><span class="sxs-lookup"><span data-stu-id="84c38-249">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="84c38-250">Další informace o *_ViewImports. cshtml*najdete v tématu [Import sdílených direktiv](xref:mvc/views/layout#importing-shared-directives) .</span><span class="sxs-lookup"><span data-stu-id="84c38-250">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="84c38-251">Sestavení knihovny tříd pro ověření, že nejsou k dispozici žádné chyby kompilátoru:</span><span class="sxs-lookup"><span data-stu-id="84c38-251">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="84c38-252">Výstup sestavení obsahuje *RazorUIClassLib. dll* a *RazorUIClassLib. views. dll*.</span><span class="sxs-lookup"><span data-stu-id="84c38-252">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="84c38-253">*RazorUIClassLib. views. dll* obsahuje zkompilovaný obsah Razor.</span><span class="sxs-lookup"><span data-stu-id="84c38-253">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="84c38-254">Použití knihovny uživatelského rozhraní Razor z Razor Pagesho projektu</span><span class="sxs-lookup"><span data-stu-id="84c38-254">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="84c38-255">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84c38-255">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="84c38-256">Vytvoření webové aplikace Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="84c38-256">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="84c38-257">V **Průzkumník řešení**klikněte pravým tlačítkem na řešení > **přidat** **Nový projekt**>.</span><span class="sxs-lookup"><span data-stu-id="84c38-257">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="84c38-258">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="84c38-258">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="84c38-259">Pojmenujte aplikaci **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="84c38-259">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="84c38-260">Ověřte, zda je vybrána **ASP.NET Core 2,1** nebo novější.</span><span class="sxs-lookup"><span data-stu-id="84c38-260">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="84c38-261">Vyberte možnost **Webová aplikace** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="84c38-261">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="84c38-262">Z **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte **nastavit jako spouštěný projekt**.</span><span class="sxs-lookup"><span data-stu-id="84c38-262">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="84c38-263">V **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte závislosti **sestavení** > **závislosti projektu**.</span><span class="sxs-lookup"><span data-stu-id="84c38-263">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="84c38-264">Ověřte **RazorUIClassLib** jako závislost **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="84c38-264">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="84c38-265">V **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte **Přidat** **odkaz**>.</span><span class="sxs-lookup"><span data-stu-id="84c38-265">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="84c38-266">V dialogovém okně **Správce odkazů** ověřte **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="84c38-266">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="84c38-267">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c38-267">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="84c38-268">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="84c38-268">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="84c38-269">Vytvoření webové aplikace Razor Pages a souboru řešení obsahujícího aplikaci Razor Pages a RCL:</span><span class="sxs-lookup"><span data-stu-id="84c38-269">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="84c38-270">Sestavte a spusťte webovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="84c38-270">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="84c38-271">WebApp1 testu</span><span class="sxs-lookup"><span data-stu-id="84c38-271">Test WebApp1</span></span>

<span data-ttu-id="84c38-272">Přejděte na `/MyFeature/Page1` a ověřte, zda se knihovna tříd uživatelského rozhraní Razor používá.</span><span class="sxs-lookup"><span data-stu-id="84c38-272">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="84c38-273">Přepsat zobrazení, částečná zobrazení a stránky</span><span class="sxs-lookup"><span data-stu-id="84c38-273">Override views, partial views, and pages</span></span>

<span data-ttu-id="84c38-274">V případě, že je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka Razor, má přednost označení Razor (soubor *. cshtml* ) ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c38-274">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="84c38-275">Například přidejte *WebApp1/areas/MyFeature/Pages/Page1. cshtml* do WebApp1 a Page1 v WebApp1 bude mít přednost před Page1 v RCL.</span><span class="sxs-lookup"><span data-stu-id="84c38-275">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="84c38-276">V ukázce stažení, přejmenujte *WebApp1/areas/MyFeature2* na *WebApp1/areas/MyFeature* na test priority.</span><span class="sxs-lookup"><span data-stu-id="84c38-276">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="84c38-277">Zkopírujte částečné zobrazení *RazorUIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml* na *WebApp1/areas/MyFeature/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="84c38-277">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="84c38-278">Aktualizujte kód tak, aby označoval nové umístění.</span><span class="sxs-lookup"><span data-stu-id="84c38-278">Update the markup to indicate the new location.</span></span> <span data-ttu-id="84c38-279">Sestavte a spusťte aplikaci, abyste ověřili, že se používá částečná verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="84c38-279">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="84c38-280">Rozložení stránek RCL</span><span class="sxs-lookup"><span data-stu-id="84c38-280">RCL Pages layout</span></span>

<span data-ttu-id="84c38-281">Pokud chcete odkazovat na obsah RCL, jako by byl *součástí složky webových* aplikací, vytvořte projekt RCL s následující strukturou souborů:</span><span class="sxs-lookup"><span data-stu-id="84c38-281">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="84c38-282">*RazorUIClassLib/stránky*</span><span class="sxs-lookup"><span data-stu-id="84c38-282">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="84c38-283">*RazorUIClassLib/stránky/sdílené*</span><span class="sxs-lookup"><span data-stu-id="84c38-283">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="84c38-284">Předpokládejme, že *RazorUIClassLib/Pages/Shared* obsahují dva částečné soubory: *_Header. cshtml* a *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="84c38-284">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="84c38-285">Do souboru *_Layout. cshtml* se dají přidat značky `<partial>`:</span><span class="sxs-lookup"><span data-stu-id="84c38-285">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end
