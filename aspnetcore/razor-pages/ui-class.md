---
title: Opakovaně použitelné Razor uživatelské rozhraní v knihovnách tříd pomocí ASP.NET Core
author: Rick-Anderson
description: Vysvětluje, jak vytvořit opakovaně použitelné Razor uživatelské rozhraní pomocí částečných zobrazení v knihovně tříd v ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/ui-class
ms.openlocfilehash: adfcc281f285892583ab8a21c014d6fcb120af9c
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102824"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="bee9d-103">Vytvoření opakovaně použitelného uživatelského rozhraní pomocí Razor projektu knihovny tříd v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bee9d-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="bee9d-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bee9d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

Razor<span data-ttu-id="bee9d-105">zobrazení, stránky, řadiče, modely stránek, [ Razor komponenty](xref:blazor/components/class-libraries), [zobrazení komponent](xref:mvc/views/view-components)a datové modely lze integrovat do Razor knihovny tříd (RCL).</span><span class="sxs-lookup"><span data-stu-id="bee9d-105"> views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="bee9d-106">RCL může být zabalen a znovu použit.</span><span class="sxs-lookup"><span data-stu-id="bee9d-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="bee9d-107">Aplikace mohou zahrnovat RCL a přepsat zobrazení a stránky, které obsahuje.</span><span class="sxs-lookup"><span data-stu-id="bee9d-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="bee9d-108">V případě, že Razor je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka, má Razor přednost označení (soubor *. cshtml* ) ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bee9d-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="bee9d-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bee9d-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="bee9d-110">Vytvoření knihovny tříd obsahující Razor uživatelské rozhraní</span><span class="sxs-lookup"><span data-stu-id="bee9d-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bee9d-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bee9d-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bee9d-112">V aplikaci Visual Studio vyberte **vytvořit nový nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="bee9d-113">Jako další vyberte \*\* Razor knihovnu tříd\*\* > **Next**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="bee9d-114">Pojmenujte knihovnu (například "RazorClassLib"), > **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="bee9d-115">Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="bee9d-116">Pokud potřebujete podporovat zobrazení, vyberte možnost **stránky podpory a zobrazení** .</span><span class="sxs-lookup"><span data-stu-id="bee9d-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="bee9d-117">Ve výchozím nastavení Razor jsou podporovány pouze stránky.</span><span class="sxs-lookup"><span data-stu-id="bee9d-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="bee9d-118">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-118">Select **Create**.</span></span>

<span data-ttu-id="bee9d-119">RazorŠablona knihovny tříd (RCL) se standardně Razor používá pro vývoj komponent.</span><span class="sxs-lookup"><span data-stu-id="bee9d-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="bee9d-120">Možnost **stránky podpory a zobrazení** podporuje stránky a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="bee9d-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="bee9d-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bee9d-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bee9d-122">Z příkazového řádku spusťte příkaz `dotnet new razorclasslib` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="bee9d-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="bee9d-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="bee9d-124">RazorŠablona knihovny tříd (RCL) se standardně Razor používá pro vývoj komponent.</span><span class="sxs-lookup"><span data-stu-id="bee9d-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="bee9d-125">Předat `--support-pages-and-views` možnost ( `dotnet new razorclasslib --support-pages-and-views` ) pro poskytnutí podpory pro stránky a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="bee9d-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="bee9d-126">Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="bee9d-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="bee9d-127">Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="bee9d-128">Přidejte Razor soubory do RCL.</span><span class="sxs-lookup"><span data-stu-id="bee9d-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="bee9d-129">Šablony ASP.NET Core předpokládají, že obsah RCL je ve složce *oblasti* .</span><span class="sxs-lookup"><span data-stu-id="bee9d-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="bee9d-130">Další informace najdete v tématu [RCL Page Layout (rozložení stránek](#rcl-pages-layout) ) pro vytvoření RCL, které zpřístupňuje obsah `~/Pages` místo `~/Areas/Pages` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="bee9d-131">Odkaz na obsah RCL</span><span class="sxs-lookup"><span data-stu-id="bee9d-131">Reference RCL content</span></span>

<span data-ttu-id="bee9d-132">Na RCL může odkazovat:</span><span class="sxs-lookup"><span data-stu-id="bee9d-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="bee9d-133">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="bee9d-133">NuGet package.</span></span> <span data-ttu-id="bee9d-134">Viz téma [vytváření balíčků NuGet](/nuget/create-packages/creating-a-package) a [dotnet přidání balíčku](/dotnet/core/tools/dotnet-add-package) a [Vytvoření a publikování balíčku NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="bee9d-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="bee9d-135">*{ProjectName}. csproj*.</span><span class="sxs-lookup"><span data-stu-id="bee9d-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="bee9d-136">Viz [dotnet – přidat odkaz](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="bee9d-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="bee9d-137">Přepsat zobrazení, částečná zobrazení a stránky</span><span class="sxs-lookup"><span data-stu-id="bee9d-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="bee9d-138">V případě, že Razor je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka, má Razor přednost označení (soubor *. cshtml* ) ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bee9d-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="bee9d-139">Například přidejte *WebApp1/areas/MyFeature/Pages/Page1. cshtml* do WebApp1 a Page1 v WebApp1 bude mít přednost před Page1 v RCL.</span><span class="sxs-lookup"><span data-stu-id="bee9d-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="bee9d-140">V ukázce stažení, přejmenujte *WebApp1/areas/MyFeature2* na *WebApp1/areas/MyFeature* na test priority.</span><span class="sxs-lookup"><span data-stu-id="bee9d-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="bee9d-141">Zkopírujte částečné zobrazení *RazorUIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml* do *WebApp1/areas/MyFeature/Pages/shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bee9d-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="bee9d-142">Aktualizujte kód tak, aby označoval nové umístění.</span><span class="sxs-lookup"><span data-stu-id="bee9d-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="bee9d-143">Sestavte a spusťte aplikaci, abyste ověřili, že se používá částečná verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="bee9d-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="bee9d-144">Rozložení stránek RCL</span><span class="sxs-lookup"><span data-stu-id="bee9d-144">RCL Pages layout</span></span>

<span data-ttu-id="bee9d-145">Pokud chcete odkazovat na obsah RCL, jako by byl *součástí složky webových* aplikací, vytvořte projekt RCL s následující strukturou souborů:</span><span class="sxs-lookup"><span data-stu-id="bee9d-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="bee9d-146">*RazorUIClassLib/stránky*</span><span class="sxs-lookup"><span data-stu-id="bee9d-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="bee9d-147">*RazorUIClassLib/stránky/sdílené*</span><span class="sxs-lookup"><span data-stu-id="bee9d-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="bee9d-148">Předpokládejme, že *RazorUIClassLib/Pages/Shared* obsahují dva částečné soubory: *_Header. cshtml* a *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bee9d-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="bee9d-149">Do `<partial>` souboru *_Layout. cshtml* se dají přidat značky:</span><span class="sxs-lookup"><span data-stu-id="bee9d-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="bee9d-150">Vytvoření RCL se statickými prostředky</span><span class="sxs-lookup"><span data-stu-id="bee9d-150">Create an RCL with static assets</span></span>

<span data-ttu-id="bee9d-151">RCL může vyžadovat doprovodné statické prostředky, na které lze odkazovat buď RCL, nebo náročné aplikace RCL.</span><span class="sxs-lookup"><span data-stu-id="bee9d-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="bee9d-152">ASP.NET Core umožňuje vytváření RCLs, které zahrnují statické prostředky, které jsou k dispozici pro náročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="bee9d-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="bee9d-153">Chcete-li zahrnout doprovodné materiály jako součást RCL, vytvořte složku *wwwroot* v knihovně tříd a zahrňte všechny požadované soubory do této složky.</span><span class="sxs-lookup"><span data-stu-id="bee9d-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="bee9d-154">Při balení RCL jsou do balíčku automaticky zahrnuty všechny doprovodné materiály ve složce *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="bee9d-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

<span data-ttu-id="bee9d-155">`dotnet pack`Místo verze NuGet.exe použijte příkaz `nuget pack` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-155">Use the `dotnet pack` command rather than the NuGet.exe version `nuget pack`.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="bee9d-156">Vyloučení statických prostředků</span><span class="sxs-lookup"><span data-stu-id="bee9d-156">Exclude static assets</span></span>

<span data-ttu-id="bee9d-157">Chcete-li vyloučit statické prostředky, přidejte požadovanou cestu vyloučení do `$(DefaultItemExcludes)` skupiny vlastností v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="bee9d-157">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="bee9d-158">Jednotlivé položky oddělte středníkem ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="bee9d-158">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="bee9d-159">V následujícím příkladu není šablona *lib. CSS* ve složce *wwwroot* považována za statický prostředek a není obsažena v publikovaném RCL:</span><span class="sxs-lookup"><span data-stu-id="bee9d-159">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="bee9d-160">Integrace TypeScriptu</span><span class="sxs-lookup"><span data-stu-id="bee9d-160">Typescript integration</span></span>

<span data-ttu-id="bee9d-161">Zahrnutí souborů TypeScriptu do RCL:</span><span class="sxs-lookup"><span data-stu-id="bee9d-161">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="bee9d-162">Soubory TypeScript (*. TS*) umístěte mimo složku *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="bee9d-162">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="bee9d-163">Umístěte například soubory do složky *klienta* .</span><span class="sxs-lookup"><span data-stu-id="bee9d-163">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="bee9d-164">Nakonfigurujte výstup sestavení TypeScriptu pro složku *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="bee9d-164">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="bee9d-165">Nastavte `TypescriptOutDir` vlastnost uvnitř `PropertyGroup` souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="bee9d-165">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="bee9d-166">Přidejte cíl TypeScript jako závislost `ResolveCurrentProjectStaticWebAssets` cíle přidáním následujícího cíle do `PropertyGroup` v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="bee9d-166">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="bee9d-167">Využití obsahu z odkazovaného RCL</span><span class="sxs-lookup"><span data-stu-id="bee9d-167">Consume content from a referenced RCL</span></span>

<span data-ttu-id="bee9d-168">Soubory zahrnuté ve složce *wwwroot* v RCL jsou zpřístupněny buď RCL, nebo aplikaci, která je v rámci předpony `_content/{LIBRARY NAME}/` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-168">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="bee9d-169">Například knihovna s názvem \* Razor . Class. lib\* má za následek cestu k statickému obsahu `_content/Razor.Class.Lib/` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-169">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="bee9d-170">Když vytváříte balíček NuGet a název sestavení není stejný jako ID balíčku, použijte ID balíčku pro `{LIBRARY NAME}` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-170">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="bee9d-171">Vybírající aplikace odkazuje na statické prostředky poskytované knihovnou s `<script>` , `<style>` , `<img>` a dalšími značkami HTML.</span><span class="sxs-lookup"><span data-stu-id="bee9d-171">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="bee9d-172">Vybírající aplikace musí mít povolenou [podporu statických souborů](xref:fundamentals/static-files) v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="bee9d-172">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="bee9d-173">Při spuštění náročné aplikace z výstupu sestavení ( `dotnet run` ) jsou ve výchozím nastavení ve vývojovém prostředí povoleny statické webové prostředky.</span><span class="sxs-lookup"><span data-stu-id="bee9d-173">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="bee9d-174">Pro podporu prostředků v jiných prostředích při spuštění z výstupu sestavení zavolejte `UseStaticWebAssets` na tvůrce hostitele v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="bee9d-174">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="bee9d-175">`UseStaticWebAssets`Při spuštění aplikace z publikovaného výstupu () se volání nevyžaduje `dotnet publish` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-175">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="bee9d-176">Vývojový tok pro více projektů</span><span class="sxs-lookup"><span data-stu-id="bee9d-176">Multi-project development flow</span></span>

<span data-ttu-id="bee9d-177">Když pracujete na náročné aplikaci:</span><span class="sxs-lookup"><span data-stu-id="bee9d-177">When the consuming app runs:</span></span>

* <span data-ttu-id="bee9d-178">Prostředky v RCL zůstanou v původních složkách.</span><span class="sxs-lookup"><span data-stu-id="bee9d-178">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="bee9d-179">Prostředky se nepřesunou do nenáročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="bee9d-179">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="bee9d-180">Jakákoli změna ve složce *WWWROOT* RCL se projeví v aplikaci po resestavení RCL a bez opětovného sestavení náročné aplikace.</span><span class="sxs-lookup"><span data-stu-id="bee9d-180">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="bee9d-181">Při sestavení RCL je vytvořen manifest, který popisuje umístění statických webových prostředků.</span><span class="sxs-lookup"><span data-stu-id="bee9d-181">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="bee9d-182">Vybírající aplikace načte manifest za běhu za účelem využití prostředků z odkazovaných projektů a balíčků.</span><span class="sxs-lookup"><span data-stu-id="bee9d-182">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="bee9d-183">Když se do RCL přidá nový Asset, musí se RCL znovu sestavit, aby se aktualizoval jeho manifest předtím, než bude mít k novému assetu přístup.</span><span class="sxs-lookup"><span data-stu-id="bee9d-183">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="bee9d-184">Publikování</span><span class="sxs-lookup"><span data-stu-id="bee9d-184">Publish</span></span>

<span data-ttu-id="bee9d-185">Po publikování aplikace se doprovodné prostředky ze všech odkazovaných projektů a balíčků zkopírují do složky *wwwroot* publikované aplikace v části `_content/{LIBRARY NAME}/` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-185">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor<span data-ttu-id="bee9d-186">zobrazení, stránky, řadiče, modely stránek, [ Razor komponenty](xref:blazor/components/class-libraries), [zobrazení komponent](xref:mvc/views/view-components)a datové modely lze integrovat do Razor knihovny tříd (RCL).</span><span class="sxs-lookup"><span data-stu-id="bee9d-186"> views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="bee9d-187">RCL může být zabalen a znovu použit.</span><span class="sxs-lookup"><span data-stu-id="bee9d-187">The RCL can be packaged and reused.</span></span> <span data-ttu-id="bee9d-188">Aplikace mohou zahrnovat RCL a přepsat zobrazení a stránky, které obsahuje.</span><span class="sxs-lookup"><span data-stu-id="bee9d-188">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="bee9d-189">V případě, že Razor je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka, má Razor přednost označení (soubor *. cshtml* ) ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bee9d-189">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="bee9d-190">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bee9d-190">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="bee9d-191">Vytvoření knihovny tříd obsahující Razor uživatelské rozhraní</span><span class="sxs-lookup"><span data-stu-id="bee9d-191">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bee9d-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bee9d-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bee9d-193">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="bee9d-194">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-194">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="bee9d-195">Pojmenujte knihovnu (například "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-195">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="bee9d-196">Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-196">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="bee9d-197">Ověřte, zda je vybrána **ASP.NET Core 2,1** nebo novější.</span><span class="sxs-lookup"><span data-stu-id="bee9d-197">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="bee9d-198">Vyberte \*\* Razor Knihovna tříd\*\* > **OK**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-198">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="bee9d-199">RCL má následující soubor projektu:</span><span class="sxs-lookup"><span data-stu-id="bee9d-199">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="bee9d-200">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bee9d-200">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bee9d-201">Z příkazového řádku spusťte příkaz `dotnet new razorclasslib` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-201">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="bee9d-202">Příklad:</span><span class="sxs-lookup"><span data-stu-id="bee9d-202">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="bee9d-203">Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="bee9d-203">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="bee9d-204">Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-204">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="bee9d-205">Přidejte Razor soubory do RCL.</span><span class="sxs-lookup"><span data-stu-id="bee9d-205">Add Razor files to the RCL.</span></span>

<span data-ttu-id="bee9d-206">Šablony ASP.NET Core předpokládají, že obsah RCL je ve složce *oblasti* .</span><span class="sxs-lookup"><span data-stu-id="bee9d-206">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="bee9d-207">Další informace najdete v tématu [RCL Page Layout (rozložení stránek](#rcl-pages-layout) ) pro vytvoření RCL, které zpřístupňuje obsah `~/Pages` místo `~/Areas/Pages` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-207">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="bee9d-208">Odkaz na obsah RCL</span><span class="sxs-lookup"><span data-stu-id="bee9d-208">Reference RCL content</span></span>

<span data-ttu-id="bee9d-209">Na RCL může odkazovat:</span><span class="sxs-lookup"><span data-stu-id="bee9d-209">The RCL can be referenced by:</span></span>

* <span data-ttu-id="bee9d-210">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="bee9d-210">NuGet package.</span></span> <span data-ttu-id="bee9d-211">Viz téma [vytváření balíčků NuGet](/nuget/create-packages/creating-a-package) a [dotnet přidání balíčku](/dotnet/core/tools/dotnet-add-package) a [Vytvoření a publikování balíčku NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="bee9d-211">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="bee9d-212">*{ProjectName}. csproj*.</span><span class="sxs-lookup"><span data-stu-id="bee9d-212">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="bee9d-213">Viz [dotnet – přidat odkaz](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="bee9d-213">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="bee9d-214">Návod: vytvoření projektu RCL a použití z Razor projektu stránky</span><span class="sxs-lookup"><span data-stu-id="bee9d-214">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="bee9d-215">Můžete stáhnout [celý projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) a otestovat ho místo jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="bee9d-215">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="bee9d-216">Ukázkový soubor ke stažení obsahuje další kód a odkazy, které usnadňují testování projektu.</span><span class="sxs-lookup"><span data-stu-id="bee9d-216">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="bee9d-217">Zpětnou vazbu k [tomuto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/6098) si můžete nechat s vašimi komentáři ke stažení ukázek a podrobnými pokyny.</span><span class="sxs-lookup"><span data-stu-id="bee9d-217">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="bee9d-218">Testování aplikace pro stažení</span><span class="sxs-lookup"><span data-stu-id="bee9d-218">Test the download app</span></span>

<span data-ttu-id="bee9d-219">Pokud jste nestáhli dokončenou aplikaci a chcete místo toho vytvořit návodový projekt, přejděte k [Další části](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="bee9d-219">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bee9d-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bee9d-220">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bee9d-221">Otevřete soubor *. sln* v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bee9d-221">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="bee9d-222">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bee9d-222">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="bee9d-223">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bee9d-223">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bee9d-224">Z příkazového řádku v adresáři *CLI* Sestavte RCL a webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bee9d-224">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="bee9d-225">Přejděte do adresáře *WebApp1* a spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="bee9d-225">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="bee9d-226">Postupujte podle pokynů v části [test WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="bee9d-226">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="bee9d-227">Vytvoření RCL</span><span class="sxs-lookup"><span data-stu-id="bee9d-227">Create an RCL</span></span>

<span data-ttu-id="bee9d-228">V této části se vytvoří RCL.</span><span class="sxs-lookup"><span data-stu-id="bee9d-228">In this section, an RCL is created.</span></span> Razor<span data-ttu-id="bee9d-229">soubory jsou přidány do RCL.</span><span class="sxs-lookup"><span data-stu-id="bee9d-229"> files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bee9d-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bee9d-230">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bee9d-231">Vytvořte projekt RCL:</span><span class="sxs-lookup"><span data-stu-id="bee9d-231">Create the RCL project:</span></span>

* <span data-ttu-id="bee9d-232">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-232">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="bee9d-233">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-233">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="bee9d-234">Pojmenujte aplikaci **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-234">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="bee9d-235">Ověřte, zda je vybrána **ASP.NET Core 2,1** nebo novější.</span><span class="sxs-lookup"><span data-stu-id="bee9d-235">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="bee9d-236">Vyberte \*\* Razor Knihovna tříd\*\* > **OK**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-236">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="bee9d-237">Přidejte Razor soubor částečného zobrazení s názvem *RazorUIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bee9d-237">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="bee9d-238">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bee9d-238">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bee9d-239">Z příkazového řádku spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="bee9d-239">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="bee9d-240">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="bee9d-240">The preceding commands:</span></span>

* <span data-ttu-id="bee9d-241">Vytvoří `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="bee9d-241">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="bee9d-242">Vytvoří Razor stránku _Message a přidá ji do RCL.</span><span class="sxs-lookup"><span data-stu-id="bee9d-242">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="bee9d-243">`-np`Parametr vytvoří stránku bez `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="bee9d-243">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="bee9d-244">Vytvoří soubor [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) a přidá jej do RCL.</span><span class="sxs-lookup"><span data-stu-id="bee9d-244">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="bee9d-245">Soubor *_ViewStart. cshtml* je vyžadován pro použití rozložení Razor projektu stránky (který je přidán v další části).</span><span class="sxs-lookup"><span data-stu-id="bee9d-245">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="bee9d-246">Přidat Razor soubory a složky do projektu</span><span class="sxs-lookup"><span data-stu-id="bee9d-246">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="bee9d-247">Nahraďte označení v *RazorUIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="bee9d-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="bee9d-248">Nahraďte označení v *RazorUIClassLib/areas/MyFeature/Pages/Page1. cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="bee9d-248">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="bee9d-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`je vyžadováno pro použití částečného zobrazení ( `<partial name="_Message" />` ).</span><span class="sxs-lookup"><span data-stu-id="bee9d-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="bee9d-250">Místo zahrnutí `@addTagHelper` direktivy můžete přidat *_ViewImports soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bee9d-250">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="bee9d-251">Příklad:</span><span class="sxs-lookup"><span data-stu-id="bee9d-251">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="bee9d-252">Další informace o *_ViewImports. cshtml*naleznete v tématu [importing Shared direktivy](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="bee9d-252">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="bee9d-253">Sestavení knihovny tříd pro ověření, že nejsou k dispozici žádné chyby kompilátoru:</span><span class="sxs-lookup"><span data-stu-id="bee9d-253">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="bee9d-254">Výstup sestavení obsahuje *RazorUIClassLib.dll* a *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="bee9d-254">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="bee9d-255">*RazorUIClassLib.Views.dll* obsahuje zkompilovaný Razor obsah.</span><span class="sxs-lookup"><span data-stu-id="bee9d-255">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="bee9d-256">Použití Razor knihovny uživatelského rozhraní z Razor projektu stránky</span><span class="sxs-lookup"><span data-stu-id="bee9d-256">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bee9d-257">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bee9d-257">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bee9d-258">Vytvoření Razor webové aplikace Pages:</span><span class="sxs-lookup"><span data-stu-id="bee9d-258">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="bee9d-259">Z **Průzkumník řešení**klikněte pravým tlačítkem myši na řešení > **Přidat** > **Nový projekt**.  </span><span class="sxs-lookup"><span data-stu-id="bee9d-259">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="bee9d-260">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-260">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="bee9d-261">Pojmenujte aplikaci **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-261">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="bee9d-262">Ověřte, zda je vybrána **ASP.NET Core 2,1** nebo novější.</span><span class="sxs-lookup"><span data-stu-id="bee9d-262">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="bee9d-263">Vyberte **Webová aplikace** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-263">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="bee9d-264">Z **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte **nastavit jako spouštěný projekt**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-264">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="bee9d-265">Z **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte **sestavení** závislosti > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-265">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="bee9d-266">Ověřte **RazorUIClassLib** jako závislost **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-266">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="bee9d-267">V **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte **Přidat** > **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-267">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="bee9d-268">V dialogovém okně **Správce odkazů** , vraťte **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="bee9d-268">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="bee9d-269">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bee9d-269">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="bee9d-270">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bee9d-270">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bee9d-271">Vytvořte Razor webovou aplikaci stránky a soubor řešení obsahující Razor aplikaci Pages a RCL:</span><span class="sxs-lookup"><span data-stu-id="bee9d-271">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="bee9d-272">Sestavte a spusťte webovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="bee9d-272">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="bee9d-273">WebApp1 testu</span><span class="sxs-lookup"><span data-stu-id="bee9d-273">Test WebApp1</span></span>

<span data-ttu-id="bee9d-274">Vyhledejte a `/MyFeature/Page1` Ověřte, zda Razor je knihovna tříd uživatelského rozhraní používána.</span><span class="sxs-lookup"><span data-stu-id="bee9d-274">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="bee9d-275">Přepsat zobrazení, částečná zobrazení a stránky</span><span class="sxs-lookup"><span data-stu-id="bee9d-275">Override views, partial views, and pages</span></span>

<span data-ttu-id="bee9d-276">V případě, že Razor je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka, má Razor přednost označení (soubor *. cshtml* ) ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bee9d-276">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="bee9d-277">Například přidejte *WebApp1/areas/MyFeature/Pages/Page1. cshtml* do WebApp1 a Page1 v WebApp1 bude mít přednost před Page1 v RCL.</span><span class="sxs-lookup"><span data-stu-id="bee9d-277">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="bee9d-278">V ukázce stažení, přejmenujte *WebApp1/areas/MyFeature2* na *WebApp1/areas/MyFeature* na test priority.</span><span class="sxs-lookup"><span data-stu-id="bee9d-278">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="bee9d-279">Zkopírujte částečné zobrazení *RazorUIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml* do *WebApp1/areas/MyFeature/Pages/shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bee9d-279">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="bee9d-280">Aktualizujte kód tak, aby označoval nové umístění.</span><span class="sxs-lookup"><span data-stu-id="bee9d-280">Update the markup to indicate the new location.</span></span> <span data-ttu-id="bee9d-281">Sestavte a spusťte aplikaci, abyste ověřili, že se používá částečná verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="bee9d-281">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="bee9d-282">Rozložení stránek RCL</span><span class="sxs-lookup"><span data-stu-id="bee9d-282">RCL Pages layout</span></span>

<span data-ttu-id="bee9d-283">Pokud chcete odkazovat na obsah RCL, jako by byl *součástí složky webových* aplikací, vytvořte projekt RCL s následující strukturou souborů:</span><span class="sxs-lookup"><span data-stu-id="bee9d-283">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="bee9d-284">*RazorUIClassLib/stránky*</span><span class="sxs-lookup"><span data-stu-id="bee9d-284">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="bee9d-285">*RazorUIClassLib/stránky/sdílené*</span><span class="sxs-lookup"><span data-stu-id="bee9d-285">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="bee9d-286">Předpokládejme, že *RazorUIClassLib/Pages/Shared* obsahují dva částečné soubory: *_Header. cshtml* a *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bee9d-286">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="bee9d-287">Do `<partial>` souboru *_Layout. cshtml* se dají přidat značky:</span><span class="sxs-lookup"><span data-stu-id="bee9d-287">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="bee9d-288">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bee9d-288">Additional resources</span></span>

* <xref:blazor/components/class-libraries>
