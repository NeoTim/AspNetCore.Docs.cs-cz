---
title: ASP.NET Core knihovny tříd součástí Razor
author: guardrex
description: Zjistěte, jak mohou být součásti součástí aplikací Blazor z externí knihovny součástí.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/class-libraries
ms.openlocfilehash: 402b7b072554f63f85e7cf5e55336104d235a071
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68913906"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="f7dde-103">ASP.NET Core knihovny tříd součástí Razor</span><span class="sxs-lookup"><span data-stu-id="f7dde-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="f7dde-104">Od [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="f7dde-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="f7dde-105">Komponenty lze sdílet v [knihovně tříd Razor (RCL)](xref:razor-pages/ui-class) napříč projekty.</span><span class="sxs-lookup"><span data-stu-id="f7dde-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="f7dde-106">*Knihovna tříd prvků Razor* může obsahovat:</span><span class="sxs-lookup"><span data-stu-id="f7dde-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="f7dde-107">Jiný projekt v řešení.</span><span class="sxs-lookup"><span data-stu-id="f7dde-107">Another project in the solution.</span></span>
* <span data-ttu-id="f7dde-108">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="f7dde-108">A NuGet package.</span></span>
* <span data-ttu-id="f7dde-109">Odkazovaná knihovna .NET.</span><span class="sxs-lookup"><span data-stu-id="f7dde-109">A referenced .NET library.</span></span>

<span data-ttu-id="f7dde-110">Stejně jako komponenty jsou běžné typy .NET, komponenty poskytované RCL jsou normální sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="f7dde-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="f7dde-111">Vytvoření RCL</span><span class="sxs-lookup"><span data-stu-id="f7dde-111">Create an RCL</span></span>

<span data-ttu-id="f7dde-112">Pokud chcete nakonfigurovat prostředí pro <xref:blazor/get-started> Blazor, postupujte podle pokynů v článku.</span><span class="sxs-lookup"><span data-stu-id="f7dde-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f7dde-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7dde-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f7dde-114">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="f7dde-114">Create a new project.</span></span>
1. <span data-ttu-id="f7dde-115">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f7dde-115">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f7dde-116">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="f7dde-116">Select **Next**.</span></span>
1. <span data-ttu-id="f7dde-117">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="f7dde-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f7dde-118">V příkladech v tomto tématu se používá název `MyComponentLib1`projektu.</span><span class="sxs-lookup"><span data-stu-id="f7dde-118">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="f7dde-119">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="f7dde-119">Select **Create**.</span></span>
1. <span data-ttu-id="f7dde-120">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="f7dde-120">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="f7dde-121">Vyberte šablonu **knihovny tříd Razor** .</span><span class="sxs-lookup"><span data-stu-id="f7dde-121">Select the **Razor Class Library** template.</span></span> <span data-ttu-id="f7dde-122">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="f7dde-122">Select **Create**.</span></span>
1. <span data-ttu-id="f7dde-123">Přidat RCL do řešení:</span><span class="sxs-lookup"><span data-stu-id="f7dde-123">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="f7dde-124">Klikněte pravým tlačítkem na řešení.</span><span class="sxs-lookup"><span data-stu-id="f7dde-124">Right-click the solution.</span></span> <span data-ttu-id="f7dde-125">Vyberte **Přidat** > **existující projekt**.</span><span class="sxs-lookup"><span data-stu-id="f7dde-125">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="f7dde-126">Přejděte do souboru projektu RCL.</span><span class="sxs-lookup"><span data-stu-id="f7dde-126">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="f7dde-127">Vyberte soubor projektu RCL ( *. csproj*).</span><span class="sxs-lookup"><span data-stu-id="f7dde-127">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="f7dde-128">Přidejte odkaz na RCL z aplikace:</span><span class="sxs-lookup"><span data-stu-id="f7dde-128">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="f7dde-129">Klikněte pravým tlačítkem na projekt aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7dde-129">Right-click the app project.</span></span> <span data-ttu-id="f7dde-130">Vyberte **Přidat** > **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="f7dde-130">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="f7dde-131">Vyberte projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="f7dde-131">Select the RCL project.</span></span> <span data-ttu-id="f7dde-132">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="f7dde-132">Select **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f7dde-133">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7dde-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="f7dde-134">Použijte šablonu **knihovny tříd Razor** (`razorclasslib`) pomocí příkazu [dotnet New](/dotnet/core/tools/dotnet-new) v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="f7dde-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="f7dde-135">V následujícím příkladu je vytvořen RCL s názvem `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="f7dde-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="f7dde-136">Složka, která je `MyComponentLib1` uložena, je vytvořena automaticky při spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="f7dde-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="f7dde-137">Chcete-li přidat knihovnu do existujícího projektu, použijte příkaz [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="f7dde-137">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="f7dde-138">V následujícím příkladu se RCL přidá do aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7dde-138">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="f7dde-139">Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:</span><span class="sxs-lookup"><span data-stu-id="f7dde-139">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="rcls-not-supported-for-client-side-apps"></a><span data-ttu-id="f7dde-140">RCLs se nepodporuje pro aplikace na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f7dde-140">RCLs not supported for client-side apps</span></span>

<span data-ttu-id="f7dde-141">V aktuálním ASP.NET Core 3,0 Preview nejsou knihovny tříd Razor kompatibilní s Blazor aplikacemi na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f7dde-141">In the current ASP.NET Core 3.0 Preview, Razor class libraries aren't compatible with Blazor client-side apps.</span></span> <span data-ttu-id="f7dde-142">Pro Blazor aplikace na straně klienta použijte Blazor knihovnu komponent vytvořenou `blazorlib` šablonou v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="f7dde-142">For Blazor client-side apps, use a Blazor component library created by the `blazorlib` template in a command shell:</span></span>

```console
dotnet new blazorlib -o MyComponentLib1
```

<span data-ttu-id="f7dde-143">Knihovny komponent, které `blazorlib` používají šablonu, můžou zahrnovat statické soubory, jako jsou obrázky, JavaScript a šablony stylů.</span><span class="sxs-lookup"><span data-stu-id="f7dde-143">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="f7dde-144">V době sestavování jsou statické soubory vloženy do sestaveného souboru sestavení ( *. dll*), což umožňuje spotřebovat komponenty bez obav, jak zahrnout jejich prostředky.</span><span class="sxs-lookup"><span data-stu-id="f7dde-144">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="f7dde-145">Všechny soubory zahrnuté v `content` adresáři jsou označené jako vložené prostředky.</span><span class="sxs-lookup"><span data-stu-id="f7dde-145">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="f7dde-146">Využití komponenty knihovny</span><span class="sxs-lookup"><span data-stu-id="f7dde-146">Consume a library component</span></span>

<span data-ttu-id="f7dde-147">Aby bylo možné využívat komponenty definované v knihovně v jiném projektu, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="f7dde-147">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="f7dde-148">Použijte úplný název typu s oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="f7dde-148">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="f7dde-149">Použijte direktivu [ \@using](xref:mvc/views/razor#using) Razor.</span><span class="sxs-lookup"><span data-stu-id="f7dde-149">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="f7dde-150">Jednotlivé komponenty lze přidat podle názvu.</span><span class="sxs-lookup"><span data-stu-id="f7dde-150">Individual components can be added by name.</span></span>

<span data-ttu-id="f7dde-151">V následujících příkladech `MyComponentLib1` je knihovna komponent `SalesReport` obsahující komponentu.</span><span class="sxs-lookup"><span data-stu-id="f7dde-151">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="f7dde-152">Na `SalesReport` komponentu lze odkazovat pomocí jejího úplného názvu typu s oborem názvů:</span><span class="sxs-lookup"><span data-stu-id="f7dde-152">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="f7dde-153">Na komponentu lze také odkazovat, je-li knihovna přenesena do rozsahu s `@using` direktivou:</span><span class="sxs-lookup"><span data-stu-id="f7dde-153">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="f7dde-154">Zahrňte direktivu do souboru *_Import. Razor* na nejvyšší úrovni, aby komponenty knihovny byly dostupné pro celý projekt. `@using MyComponentLib1`</span><span class="sxs-lookup"><span data-stu-id="f7dde-154">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="f7dde-155">Přidáním direktivy do souboru *_Import. Razor* na libovolné úrovni můžete použít obor názvů na jednu stránku nebo sadu stránek v rámci složky.</span><span class="sxs-lookup"><span data-stu-id="f7dde-155">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="f7dde-156">Sestavení, balení a odeslání do NuGet</span><span class="sxs-lookup"><span data-stu-id="f7dde-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="f7dde-157">Knihovny součástí jsou standardní knihovny .NET, balení a jejich odeslání do NuGet se neliší od balení a předává jakékoli knihovny do NuGet.</span><span class="sxs-lookup"><span data-stu-id="f7dde-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="f7dde-158">Balení se provádí pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="f7dde-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```console
dotnet pack
```

<span data-ttu-id="f7dde-159">Nahrajte balíček do NuGet pomocí příkazu [dotnet NuGet pro publikování](/dotnet/core/tools/dotnet-nuget-push) v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="f7dde-159">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command in a command shell:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="f7dde-160">Při použití `blazorlib` šablony jsou do balíčku NuGet zahrnuty statické prostředky.</span><span class="sxs-lookup"><span data-stu-id="f7dde-160">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="f7dde-161">Příjemci knihovny automaticky přijímají skripty a šablony stylů, takže uživatelé nejsou k ruční instalaci prostředků potřebovat.</span><span class="sxs-lookup"><span data-stu-id="f7dde-161">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="f7dde-162">Vytvoření knihovny tříd součástí Razor se statickými prostředky</span><span class="sxs-lookup"><span data-stu-id="f7dde-162">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="f7dde-163">RCL může zahrnovat statické prostředky.</span><span class="sxs-lookup"><span data-stu-id="f7dde-163">An RCL can include static assets.</span></span> <span data-ttu-id="f7dde-164">Statické prostředky jsou k dispozici pro všechny aplikace, které knihovnu využívají.</span><span class="sxs-lookup"><span data-stu-id="f7dde-164">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="f7dde-165">Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="f7dde-165">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7dde-166">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f7dde-166">Additional resources</span></span>

* <xref:razor-pages/ui-class>
