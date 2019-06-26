---
title: ASP.NET Core Razor komponenty knihovny třídy
author: guardrex
description: Zjistěte, jak komponenty mohou být součástí Blazor aplikací z knihovny externí komponenta.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2019
uid: blazor/class-libraries
ms.openlocfilehash: 8676e0fd660b7d281c80d06d24d5593c2df6348b
ms.sourcegitcommit: 763af2cbdab0da62d1f1cfef4bcf787f251dfb5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394621"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="504b1-103">ASP.NET Core Razor komponenty knihovny třídy</span><span class="sxs-lookup"><span data-stu-id="504b1-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="504b1-104">Podle [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="504b1-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="504b1-105">Součástí je možné sdílet v [knihovny tříd Razor (RCL)](xref:razor-pages/ui-class) napříč projekty.</span><span class="sxs-lookup"><span data-stu-id="504b1-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="504b1-106">A *knihovna tříd Razor komponenty* půjdou zahrnout od:</span><span class="sxs-lookup"><span data-stu-id="504b1-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="504b1-107">Jiný projekt v řešení.</span><span class="sxs-lookup"><span data-stu-id="504b1-107">Another project in the solution.</span></span>
* <span data-ttu-id="504b1-108">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="504b1-108">A NuGet package.</span></span>
* <span data-ttu-id="504b1-109">Odkazované knihovny .NET.</span><span class="sxs-lookup"><span data-stu-id="504b1-109">A referenced .NET library.</span></span>

<span data-ttu-id="504b1-110">Stejně, jako jsou komponenty regulární typy .NET, jsou součástí RCL normální sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="504b1-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="504b1-111">Vytvoření RCL</span><span class="sxs-lookup"><span data-stu-id="504b1-111">Create an RCL</span></span>

<span data-ttu-id="504b1-112">Postupujte podle pokynů v <xref:blazor/get-started> článku ke konfiguraci prostředí pro Blazor.</span><span class="sxs-lookup"><span data-stu-id="504b1-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="504b1-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="504b1-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="504b1-114">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="504b1-114">Create a new project.</span></span>
1. <span data-ttu-id="504b1-115">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="504b1-115">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="504b1-116">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="504b1-116">Select **Next**.</span></span>
1. <span data-ttu-id="504b1-117">Zadejte název projektu **název projektu** pole nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="504b1-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="504b1-118">V příkladech v tomto tématu se používá název projektu `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="504b1-118">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="504b1-119">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="504b1-119">Select **Create**.</span></span>
1. <span data-ttu-id="504b1-120">V **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány.</span><span class="sxs-lookup"><span data-stu-id="504b1-120">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="504b1-121">Vyberte **knihovny tříd Razor** šablony.</span><span class="sxs-lookup"><span data-stu-id="504b1-121">Select the **Razor Class Library** template.</span></span> <span data-ttu-id="504b1-122">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="504b1-122">Select **Create**.</span></span>
1. <span data-ttu-id="504b1-123">Přidáte do řešení RCL:</span><span class="sxs-lookup"><span data-stu-id="504b1-123">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="504b1-124">Klikněte pravým tlačítkem na řešení.</span><span class="sxs-lookup"><span data-stu-id="504b1-124">Right-click the solution.</span></span> <span data-ttu-id="504b1-125">Vyberte **přidat** > **existující projekt**.</span><span class="sxs-lookup"><span data-stu-id="504b1-125">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="504b1-126">Přejděte k souboru projektu RCL.</span><span class="sxs-lookup"><span data-stu-id="504b1-126">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="504b1-127">Vyberte soubor projektu RCL ( *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="504b1-127">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="504b1-128">Přidejte odkaz RCL z aplikace:</span><span class="sxs-lookup"><span data-stu-id="504b1-128">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="504b1-129">Klikněte pravým tlačítkem na projekt aplikace.</span><span class="sxs-lookup"><span data-stu-id="504b1-129">Right-click the app project.</span></span> <span data-ttu-id="504b1-130">Vyberte **přidat** > **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="504b1-130">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="504b1-131">Vyberte projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="504b1-131">Select the RCL project.</span></span> <span data-ttu-id="504b1-132">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="504b1-132">Select **OK**.</span></span>

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="504b1-133">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="504b1-133">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

1. <span data-ttu-id="504b1-134">Šablona knihovny tříd Razor (`razorclasslib`) se [dotnet nové](/dotnet/core/tools/dotnet-new) z příkazové okno.</span><span class="sxs-lookup"><span data-stu-id="504b1-134">Use the Razor Class Library template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="504b1-135">V následujícím příkladu je vytvořena s názvem RCL `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="504b1-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="504b1-136">Složky obsahující `MyComponentLib1` je vytvořen automaticky při spuštění příkazu.</span><span class="sxs-lookup"><span data-stu-id="504b1-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed.</span></span>

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="504b1-137">Přidání knihovny do existujícího projektu, použijte [se příkaz dotnet add odkaz](/dotnet/core/tools/dotnet-add-reference) z příkazové okno.</span><span class="sxs-lookup"><span data-stu-id="504b1-137">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command from a command shell.</span></span> <span data-ttu-id="504b1-138">V následujícím příkladu se přidá RCL do aplikace.</span><span class="sxs-lookup"><span data-stu-id="504b1-138">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="504b1-139">Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:</span><span class="sxs-lookup"><span data-stu-id="504b1-139">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

<span data-ttu-id="504b1-140">Přidat soubory součástí Razor ( *.razor*) k RCL.</span><span class="sxs-lookup"><span data-stu-id="504b1-140">Add Razor component files (*.razor*) to the RCL.</span></span>

## <a name="rcls-not-supported-for-client-side-apps"></a><span data-ttu-id="504b1-141">RCLs není podporována pro aplikace na straně klienta</span><span class="sxs-lookup"><span data-stu-id="504b1-141">RCLs not supported for client-side apps</span></span>

<span data-ttu-id="504b1-142">ASP.NET Core 3.0 ve verzi Preview nejsou kompatibilní s aplikacemi na straně klienta Blazor knihovny tříd Razor.</span><span class="sxs-lookup"><span data-stu-id="504b1-142">In ASP.NET Core 3.0 Preview, Razor class libraries aren't compatible with Blazor client-side apps.</span></span>

<span data-ttu-id="504b1-143">Blazor aplikace na straně klienta, použijte knihovně komponent Blazor vytvořené `blazorlib` šablonu z příkazové prostředí:</span><span class="sxs-lookup"><span data-stu-id="504b1-143">For Blazor client-side apps, use a Blazor component library created by the `blazorlib` template from a command shell:</span></span>

```console
dotnet new blazorlib -o MyComponentLib1
```

<span data-ttu-id="504b1-144">Pomocí knihovny součástí `blazorlib` šablona může obsahovat statické soubory, jako jsou obrázky, JavaScript a šablony stylů.</span><span class="sxs-lookup"><span data-stu-id="504b1-144">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="504b1-145">V okamžiku sestavení, statické soubory jsou vloženy do souborů sestavení ( *.dll*), která umožňuje využití komponent bez nutnosti starat o tom, jak zahrnout svoje prostředky.</span><span class="sxs-lookup"><span data-stu-id="504b1-145">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="504b1-146">Všechny soubory zahrnuté v `content` adresáře jsou označeny jako vložený prostředek.</span><span class="sxs-lookup"><span data-stu-id="504b1-146">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="504b1-147">Využívat komponentu knihovny</span><span class="sxs-lookup"><span data-stu-id="504b1-147">Consume a library component</span></span>

<span data-ttu-id="504b1-148">Aby bylo možné využívat součásti definované v knihovně v jiném projektu, použijte některou z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="504b1-148">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="504b1-149">Použijte úplný název typu s oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="504b1-149">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="504b1-150">Použití syntaxe Razor pro [ \@pomocí](xref:mvc/views/razor#using) směrnice.</span><span class="sxs-lookup"><span data-stu-id="504b1-150">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="504b1-151">Jednotlivé komponenty mohou být přidány podle názvu.</span><span class="sxs-lookup"><span data-stu-id="504b1-151">Individual components can be added by name.</span></span>

<span data-ttu-id="504b1-152">V následujících příkladech `MyComponentLib1` je součást Knihovna, která obsahuje prodejní sestavu (`SalesReport`) komponenty.</span><span class="sxs-lookup"><span data-stu-id="504b1-152">In the following examples, `MyComponentLib1` is a component library containing a Sales Report (`SalesReport`) component.</span></span>

<span data-ttu-id="504b1-153">Komponenta prodejní sestavu můžete odkazovat pomocí jeho úplný název typu s oborem názvů:</span><span class="sxs-lookup"><span data-stu-id="504b1-153">The Sales Report component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="504b1-154">Komponentu lze také odkazovat Pokud knihovny je přenesena do rozsahu pomocí `@using` – direktiva:</span><span class="sxs-lookup"><span data-stu-id="504b1-154">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="504b1-155">Zahrnout `@using MyComponentLib1` direktivu na nejvyšší úrovni *_Import.razor* souboru pro zpřístupnění knihovny součásti pro celý projekt.</span><span class="sxs-lookup"><span data-stu-id="504b1-155">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="504b1-156">Přidat direktivu do *_Import.razor* souboru na libovolné úrovni použít obor názvů na jednu stránku nebo sadu stránek ve složce.</span><span class="sxs-lookup"><span data-stu-id="504b1-156">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="504b1-157">Sestavení, aktualizací Service pack a příjemce pro NuGet</span><span class="sxs-lookup"><span data-stu-id="504b1-157">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="504b1-158">Protože jsou součástí knihovny knihovny .NET standard, balení a je na cestě k NuGet se nijak neliší od balení a přesouvání všechny knihovny nuget.</span><span class="sxs-lookup"><span data-stu-id="504b1-158">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="504b1-159">Balení se provádí pomocí [balíčku dotnet](/dotnet/core/tools/dotnet-pack) z příkazové prostředí:</span><span class="sxs-lookup"><span data-stu-id="504b1-159">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command from a command shell:</span></span>

```console
dotnet pack
```

<span data-ttu-id="504b1-160">Nahrání balíčku NuGet pomocí [dotnet nuget publikovat](/dotnet/core/tools/dotnet-nuget-push) z příkazové prostředí:</span><span class="sxs-lookup"><span data-stu-id="504b1-160">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command from a command shell:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="504b1-161">Při použití `blazorlib` šablonu, statických prostředků jsou obsažené v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="504b1-161">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="504b1-162">Příjemci knihovny automaticky obdrží skripty a šablony stylů, takže příjemci nejsou potřeba ručně instalovat prostředky.</span><span class="sxs-lookup"><span data-stu-id="504b1-162">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="504b1-163">Vytvoření knihovny tříd Razor součásti s statické prostředky</span><span class="sxs-lookup"><span data-stu-id="504b1-163">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="504b1-164">RCL můžete zahrnout statické prostředky.</span><span class="sxs-lookup"><span data-stu-id="504b1-164">An RCL can include static assets.</span></span> <span data-ttu-id="504b1-165">Statické prostředky jsou dostupné pro každou aplikaci, která využívá knihovny.</span><span class="sxs-lookup"><span data-stu-id="504b1-165">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="504b1-166">Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="504b1-166">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="504b1-167">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="504b1-167">Additional resources</span></span>

* <xref:razor-pages/ui-class>
