---
title: ASP.NET Core Razor komponenty knihovny třídy
author: guardrex
description: Zjistěte, jak komponenty mohou být součástí Blazor aplikací z knihovny externí komponenta.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/class-libraries
ms.openlocfilehash: e99dd63200dc863552f099b5d715f78a9732165c
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538505"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="d6104-103">ASP.NET Core Razor komponenty knihovny třídy</span><span class="sxs-lookup"><span data-stu-id="d6104-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="d6104-104">Podle [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="d6104-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="d6104-105">Součástí je možné sdílet v [knihovny tříd Razor (RCL)](xref:razor-pages/ui-class) napříč projekty.</span><span class="sxs-lookup"><span data-stu-id="d6104-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="d6104-106">A *knihovna tříd Razor komponenty* půjdou zahrnout od:</span><span class="sxs-lookup"><span data-stu-id="d6104-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="d6104-107">Jiný projekt v řešení.</span><span class="sxs-lookup"><span data-stu-id="d6104-107">Another project in the solution.</span></span>
* <span data-ttu-id="d6104-108">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="d6104-108">A NuGet package.</span></span>
* <span data-ttu-id="d6104-109">Odkazované knihovny .NET.</span><span class="sxs-lookup"><span data-stu-id="d6104-109">A referenced .NET library.</span></span>

<span data-ttu-id="d6104-110">Stejně, jako jsou komponenty regulární typy .NET, jsou součástí RCL normální sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="d6104-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="d6104-111">Vytvoření RCL</span><span class="sxs-lookup"><span data-stu-id="d6104-111">Create an RCL</span></span>

<span data-ttu-id="d6104-112">Postupujte podle pokynů v <xref:blazor/get-started> článku ke konfiguraci prostředí pro Blazor.</span><span class="sxs-lookup"><span data-stu-id="d6104-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d6104-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d6104-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d6104-114">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="d6104-114">Create a new project.</span></span>
1. <span data-ttu-id="d6104-115">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="d6104-115">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="d6104-116">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="d6104-116">Select **Next**.</span></span>
1. <span data-ttu-id="d6104-117">Zadejte název projektu **název projektu** pole nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="d6104-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="d6104-118">V příkladech v tomto tématu se používá název projektu `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="d6104-118">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="d6104-119">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d6104-119">Select **Create**.</span></span>
1. <span data-ttu-id="d6104-120">V **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány.</span><span class="sxs-lookup"><span data-stu-id="d6104-120">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="d6104-121">Vyberte **knihovny tříd Razor** šablony.</span><span class="sxs-lookup"><span data-stu-id="d6104-121">Select the **Razor Class Library** template.</span></span> <span data-ttu-id="d6104-122">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d6104-122">Select **Create**.</span></span>
1. <span data-ttu-id="d6104-123">Přidáte do řešení RCL:</span><span class="sxs-lookup"><span data-stu-id="d6104-123">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="d6104-124">Klikněte pravým tlačítkem na řešení.</span><span class="sxs-lookup"><span data-stu-id="d6104-124">Right-click the solution.</span></span> <span data-ttu-id="d6104-125">Vyberte **přidat** > **existující projekt**.</span><span class="sxs-lookup"><span data-stu-id="d6104-125">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="d6104-126">Přejděte k souboru projektu RCL.</span><span class="sxs-lookup"><span data-stu-id="d6104-126">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="d6104-127">Vyberte soubor projektu RCL ( *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="d6104-127">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="d6104-128">Přidejte odkaz RCL z aplikace:</span><span class="sxs-lookup"><span data-stu-id="d6104-128">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="d6104-129">Klikněte pravým tlačítkem na projekt aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6104-129">Right-click the app project.</span></span> <span data-ttu-id="d6104-130">Vyberte **přidat** > **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="d6104-130">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="d6104-131">Vyberte projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="d6104-131">Select the RCL project.</span></span> <span data-ttu-id="d6104-132">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="d6104-132">Select **OK**.</span></span>

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="d6104-133">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d6104-133">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

1. <span data-ttu-id="d6104-134">Použití **knihovny tříd Razor** šablony (`razorclasslib`) se [dotnet nové](/dotnet/core/tools/dotnet-new) příkazu v příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="d6104-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="d6104-135">V následujícím příkladu je vytvořena s názvem RCL `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="d6104-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="d6104-136">Složky obsahující `MyComponentLib1` je vytvořen automaticky při spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="d6104-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="d6104-137">Chcete-li přidat knihovnu do existujícího projektu, použijte [se příkaz dotnet add odkaz](/dotnet/core/tools/dotnet-add-reference) příkazu v příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="d6104-137">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="d6104-138">V následujícím příkladu se přidá RCL do aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6104-138">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="d6104-139">Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:</span><span class="sxs-lookup"><span data-stu-id="d6104-139">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="rcls-not-supported-for-client-side-apps"></a><span data-ttu-id="d6104-140">RCLs není podporována pro aplikace na straně klienta</span><span class="sxs-lookup"><span data-stu-id="d6104-140">RCLs not supported for client-side apps</span></span>

<span data-ttu-id="d6104-141">V aktuální ASP.NET Core náhledu 3.0 nejsou kompatibilní s aplikacemi na straně klienta Blazor knihovny tříd Razor.</span><span class="sxs-lookup"><span data-stu-id="d6104-141">In the current ASP.NET Core 3.0 Preview, Razor class libraries aren't compatible with Blazor client-side apps.</span></span> <span data-ttu-id="d6104-142">Blazor aplikace na straně klienta, použijte knihovně komponent Blazor vytvořené `blazorlib` šablony v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="d6104-142">For Blazor client-side apps, use a Blazor component library created by the `blazorlib` template in a command shell:</span></span>

```console
dotnet new blazorlib -o MyComponentLib1
```

<span data-ttu-id="d6104-143">Pomocí knihovny součástí `blazorlib` šablona může obsahovat statické soubory, jako jsou obrázky, JavaScript a šablony stylů.</span><span class="sxs-lookup"><span data-stu-id="d6104-143">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="d6104-144">V okamžiku sestavení, statické soubory jsou vloženy do souborů sestavení ( *.dll*), která umožňuje využití komponent bez nutnosti starat o tom, jak zahrnout svoje prostředky.</span><span class="sxs-lookup"><span data-stu-id="d6104-144">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="d6104-145">Všechny soubory zahrnuté v `content` adresáře jsou označeny jako vložený prostředek.</span><span class="sxs-lookup"><span data-stu-id="d6104-145">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="d6104-146">Využívat komponentu knihovny</span><span class="sxs-lookup"><span data-stu-id="d6104-146">Consume a library component</span></span>

<span data-ttu-id="d6104-147">Aby bylo možné využívat součásti definované v knihovně v jiném projektu, použijte některou z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="d6104-147">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="d6104-148">Použijte úplný název typu s oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="d6104-148">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="d6104-149">Použití syntaxe Razor pro [ \@pomocí](xref:mvc/views/razor#using) směrnice.</span><span class="sxs-lookup"><span data-stu-id="d6104-149">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="d6104-150">Jednotlivé komponenty mohou být přidány podle názvu.</span><span class="sxs-lookup"><span data-stu-id="d6104-150">Individual components can be added by name.</span></span>

<span data-ttu-id="d6104-151">V následujících příkladech `MyComponentLib1` je součástí knihovny obsahující `SalesReport` komponenty.</span><span class="sxs-lookup"><span data-stu-id="d6104-151">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="d6104-152">`SalesReport` Komponenty lze odkazovat pomocí jeho úplný název typu s oborem názvů:</span><span class="sxs-lookup"><span data-stu-id="d6104-152">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="d6104-153">Komponentu lze také odkazovat Pokud knihovny je přenesena do rozsahu pomocí `@using` – direktiva:</span><span class="sxs-lookup"><span data-stu-id="d6104-153">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="d6104-154">Zahrnout `@using MyComponentLib1` direktivu na nejvyšší úrovni *_Import.razor* souboru pro zpřístupnění knihovny součásti pro celý projekt.</span><span class="sxs-lookup"><span data-stu-id="d6104-154">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="d6104-155">Přidat direktivu do *_Import.razor* souboru na libovolné úrovni použít obor názvů na jednu stránku nebo sadu stránek ve složce.</span><span class="sxs-lookup"><span data-stu-id="d6104-155">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="d6104-156">Sestavení, aktualizací Service pack a příjemce pro NuGet</span><span class="sxs-lookup"><span data-stu-id="d6104-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="d6104-157">Protože jsou součástí knihovny knihovny .NET standard, balení a je na cestě k NuGet se nijak neliší od balení a přesouvání všechny knihovny nuget.</span><span class="sxs-lookup"><span data-stu-id="d6104-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="d6104-158">Balení se provádí pomocí [balíčku dotnet](/dotnet/core/tools/dotnet-pack) příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="d6104-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```console
dotnet pack
```

<span data-ttu-id="d6104-159">Nahrání balíčku NuGet pomocí [dotnet nuget publikovat](/dotnet/core/tools/dotnet-nuget-push) příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="d6104-159">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command in a command shell:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="d6104-160">Při použití `blazorlib` šablonu, statických prostředků jsou obsažené v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="d6104-160">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="d6104-161">Příjemci knihovny automaticky obdrží skripty a šablony stylů, takže příjemci nejsou potřeba ručně instalovat prostředky.</span><span class="sxs-lookup"><span data-stu-id="d6104-161">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="d6104-162">Vytvoření knihovny tříd Razor součásti s statické prostředky</span><span class="sxs-lookup"><span data-stu-id="d6104-162">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="d6104-163">RCL můžete zahrnout statické prostředky.</span><span class="sxs-lookup"><span data-stu-id="d6104-163">An RCL can include static assets.</span></span> <span data-ttu-id="d6104-164">Statické prostředky jsou dostupné pro každou aplikaci, která využívá knihovny.</span><span class="sxs-lookup"><span data-stu-id="d6104-164">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="d6104-165">Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="d6104-165">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d6104-166">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d6104-166">Additional resources</span></span>

* <xref:razor-pages/ui-class>
