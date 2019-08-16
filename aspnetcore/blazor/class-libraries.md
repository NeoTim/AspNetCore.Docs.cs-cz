---
title: ASP.NET Core knihovny tříd součástí Razor
author: guardrex
description: Zjistěte, jak mohou být součásti součástí aplikací Blazor z externí knihovny součástí.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/class-libraries
ms.openlocfilehash: b5857f2cf22bde801deeeaf227817fdf99862f4a
ms.sourcegitcommit: 4cb0c7e74355f2e87c60e2a196f842b937247a99
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69545782"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="10077-103">ASP.NET Core knihovny tříd součástí Razor</span><span class="sxs-lookup"><span data-stu-id="10077-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="10077-104">Od [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="10077-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="10077-105">Komponenty lze sdílet v [knihovně tříd Razor (RCL)](xref:razor-pages/ui-class) napříč projekty.</span><span class="sxs-lookup"><span data-stu-id="10077-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="10077-106">*Knihovna tříd prvků Razor* může obsahovat:</span><span class="sxs-lookup"><span data-stu-id="10077-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="10077-107">Jiný projekt v řešení.</span><span class="sxs-lookup"><span data-stu-id="10077-107">Another project in the solution.</span></span>
* <span data-ttu-id="10077-108">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="10077-108">A NuGet package.</span></span>
* <span data-ttu-id="10077-109">Odkazovaná knihovna .NET.</span><span class="sxs-lookup"><span data-stu-id="10077-109">A referenced .NET library.</span></span>

<span data-ttu-id="10077-110">Stejně jako komponenty jsou běžné typy .NET, komponenty poskytované RCL jsou normální sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="10077-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="10077-111">Vytvoření RCL</span><span class="sxs-lookup"><span data-stu-id="10077-111">Create an RCL</span></span>

<span data-ttu-id="10077-112">Pokud chcete nakonfigurovat prostředí pro <xref:blazor/get-started> Blazor, postupujte podle pokynů v článku.</span><span class="sxs-lookup"><span data-stu-id="10077-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="10077-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10077-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="10077-114">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="10077-114">Create a new project.</span></span>
1. <span data-ttu-id="10077-115">Vyberte **knihovnu tříd Razor**.</span><span class="sxs-lookup"><span data-stu-id="10077-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="10077-116">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="10077-116">Select **Next**.</span></span>
1. <span data-ttu-id="10077-117">V dialogovém okně **vytvořit novou knihovnu tříd Razor** vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="10077-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="10077-118">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="10077-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="10077-119">V příkladech v tomto tématu se používá název `MyComponentLib1`projektu.</span><span class="sxs-lookup"><span data-stu-id="10077-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="10077-120">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="10077-120">Select **Create**.</span></span>
1. <span data-ttu-id="10077-121">Přidat RCL do řešení:</span><span class="sxs-lookup"><span data-stu-id="10077-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="10077-122">Klikněte pravým tlačítkem na řešení.</span><span class="sxs-lookup"><span data-stu-id="10077-122">Right-click the solution.</span></span> <span data-ttu-id="10077-123">Vyberte **Přidat** > **existující projekt**.</span><span class="sxs-lookup"><span data-stu-id="10077-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="10077-124">Přejděte do souboru projektu RCL.</span><span class="sxs-lookup"><span data-stu-id="10077-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="10077-125">Vyberte soubor projektu RCL ( *. csproj*).</span><span class="sxs-lookup"><span data-stu-id="10077-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="10077-126">Přidejte odkaz na RCL z aplikace:</span><span class="sxs-lookup"><span data-stu-id="10077-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="10077-127">Klikněte pravým tlačítkem na projekt aplikace.</span><span class="sxs-lookup"><span data-stu-id="10077-127">Right-click the app project.</span></span> <span data-ttu-id="10077-128">Vyberte **Přidat** > **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="10077-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="10077-129">Vyberte projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="10077-129">Select the RCL project.</span></span> <span data-ttu-id="10077-130">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="10077-130">Select **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="10077-131">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="10077-131">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="10077-132">Použijte šablonu **knihovny tříd Razor** (`razorclasslib`) pomocí příkazu [dotnet New](/dotnet/core/tools/dotnet-new) v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="10077-132">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="10077-133">V následujícím příkladu je vytvořen RCL s názvem `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="10077-133">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="10077-134">Složka, která je `MyComponentLib1` uložena, je vytvořena automaticky při spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="10077-134">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="10077-135">Chcete-li přidat knihovnu do existujícího projektu, použijte příkaz [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="10077-135">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="10077-136">V následujícím příkladu se RCL přidá do aplikace.</span><span class="sxs-lookup"><span data-stu-id="10077-136">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="10077-137">Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:</span><span class="sxs-lookup"><span data-stu-id="10077-137">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="10077-138">Využití komponenty knihovny</span><span class="sxs-lookup"><span data-stu-id="10077-138">Consume a library component</span></span>

<span data-ttu-id="10077-139">Aby bylo možné využívat komponenty definované v knihovně v jiném projektu, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="10077-139">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="10077-140">Použijte úplný název typu s oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="10077-140">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="10077-141">Použijte direktivu [ \@using](xref:mvc/views/razor#using) Razor.</span><span class="sxs-lookup"><span data-stu-id="10077-141">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="10077-142">Jednotlivé komponenty lze přidat podle názvu.</span><span class="sxs-lookup"><span data-stu-id="10077-142">Individual components can be added by name.</span></span>

<span data-ttu-id="10077-143">V následujících příkladech `MyComponentLib1` je knihovna komponent `SalesReport` obsahující komponentu.</span><span class="sxs-lookup"><span data-stu-id="10077-143">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="10077-144">Na `SalesReport` komponentu lze odkazovat pomocí jejího úplného názvu typu s oborem názvů:</span><span class="sxs-lookup"><span data-stu-id="10077-144">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="10077-145">Na komponentu lze také odkazovat, je-li knihovna přenesena do rozsahu s `@using` direktivou:</span><span class="sxs-lookup"><span data-stu-id="10077-145">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="10077-146">Zahrňte direktivu do souboru *_Import. Razor* na nejvyšší úrovni, aby komponenty knihovny byly dostupné pro celý projekt. `@using MyComponentLib1`</span><span class="sxs-lookup"><span data-stu-id="10077-146">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="10077-147">Přidáním direktivy do souboru *_Import. Razor* na libovolné úrovni můžete použít obor názvů na jednu stránku nebo sadu stránek v rámci složky.</span><span class="sxs-lookup"><span data-stu-id="10077-147">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="10077-148">Sestavení, balení a odeslání do NuGet</span><span class="sxs-lookup"><span data-stu-id="10077-148">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="10077-149">Knihovny součástí jsou standardní knihovny .NET, balení a jejich odeslání do NuGet se neliší od balení a předává jakékoli knihovny do NuGet.</span><span class="sxs-lookup"><span data-stu-id="10077-149">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="10077-150">Balení se provádí pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="10077-150">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```console
dotnet pack
```

<span data-ttu-id="10077-151">Nahrajte balíček do NuGet pomocí příkazu [dotnet NuGet pro publikování](/dotnet/core/tools/dotnet-nuget-push) v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="10077-151">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command in a command shell:</span></span>

```console
dotnet nuget publish
```

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="10077-152">Vytvoření knihovny tříd součástí Razor se statickými prostředky</span><span class="sxs-lookup"><span data-stu-id="10077-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="10077-153">RCL může zahrnovat statické prostředky.</span><span class="sxs-lookup"><span data-stu-id="10077-153">An RCL can include static assets.</span></span> <span data-ttu-id="10077-154">Statické prostředky jsou k dispozici pro všechny aplikace, které knihovnu využívají.</span><span class="sxs-lookup"><span data-stu-id="10077-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="10077-155">Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="10077-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10077-156">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="10077-156">Additional resources</span></span>

* <xref:razor-pages/ui-class>
