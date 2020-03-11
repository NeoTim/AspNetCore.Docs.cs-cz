---
title: ASP.NET Core knihovny tříd součástí Razor
author: guardrex
description: Zjistěte, jak mohou být součásti součástí aplikace Blazor z externí knihovny součástí.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: 32088b43f91174596f6b9251d36782e806f966b9
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660487"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="ed948-103">ASP.NET Core knihovny tříd součástí Razor</span><span class="sxs-lookup"><span data-stu-id="ed948-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="ed948-104">Od [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="ed948-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="ed948-105">Komponenty lze sdílet v [knihovně tříd Razor (RCL)](xref:razor-pages/ui-class) napříč projekty.</span><span class="sxs-lookup"><span data-stu-id="ed948-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="ed948-106">*Knihovna tříd prvků Razor* může obsahovat:</span><span class="sxs-lookup"><span data-stu-id="ed948-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="ed948-107">Jiný projekt v řešení.</span><span class="sxs-lookup"><span data-stu-id="ed948-107">Another project in the solution.</span></span>
* <span data-ttu-id="ed948-108">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="ed948-108">A NuGet package.</span></span>
* <span data-ttu-id="ed948-109">Odkazovaná knihovna .NET.</span><span class="sxs-lookup"><span data-stu-id="ed948-109">A referenced .NET library.</span></span>

<span data-ttu-id="ed948-110">Stejně jako komponenty jsou běžné typy .NET, komponenty poskytované RCL jsou normální sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="ed948-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="ed948-111">Vytvoření RCL</span><span class="sxs-lookup"><span data-stu-id="ed948-111">Create an RCL</span></span>

<span data-ttu-id="ed948-112">Pokud chcete nakonfigurovat prostředí pro Blazor, postupujte podle pokynů v článku o <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="ed948-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ed948-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ed948-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ed948-114">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="ed948-114">Create a new project.</span></span>
1. <span data-ttu-id="ed948-115">Vyberte **knihovnu tříd Razor**.</span><span class="sxs-lookup"><span data-stu-id="ed948-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="ed948-116">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="ed948-116">Select **Next**.</span></span>
1. <span data-ttu-id="ed948-117">V dialogovém okně **vytvořit novou knihovnu tříd Razor** vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ed948-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="ed948-118">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="ed948-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="ed948-119">Příklady v tomto tématu používají název projektu `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="ed948-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="ed948-120">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ed948-120">Select **Create**.</span></span>
1. <span data-ttu-id="ed948-121">Přidat RCL do řešení:</span><span class="sxs-lookup"><span data-stu-id="ed948-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="ed948-122">Klikněte pravým tlačítkem na řešení.</span><span class="sxs-lookup"><span data-stu-id="ed948-122">Right-click the solution.</span></span> <span data-ttu-id="ed948-123">Vyberte **přidat** > **existující projekt**.</span><span class="sxs-lookup"><span data-stu-id="ed948-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="ed948-124">Přejděte do souboru projektu RCL.</span><span class="sxs-lookup"><span data-stu-id="ed948-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="ed948-125">Vyberte soubor projektu RCL ( *. csproj*).</span><span class="sxs-lookup"><span data-stu-id="ed948-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="ed948-126">Přidejte odkaz na RCL z aplikace:</span><span class="sxs-lookup"><span data-stu-id="ed948-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="ed948-127">Klikněte pravým tlačítkem na projekt aplikace.</span><span class="sxs-lookup"><span data-stu-id="ed948-127">Right-click the app project.</span></span> <span data-ttu-id="ed948-128">Vyberte **přidat** > **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="ed948-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="ed948-129">Vyberte projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="ed948-129">Select the RCL project.</span></span> <span data-ttu-id="ed948-130">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="ed948-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="ed948-131">Pokud je zaškrtnuté políčko **stránky podpory a zobrazení** při generování RCL ze šablony, přidejte také soubor *_Imports. Razor* do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožní vytváření komponent Razor:</span><span class="sxs-lookup"><span data-stu-id="ed948-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="ed948-132">Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.</span><span class="sxs-lookup"><span data-stu-id="ed948-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ed948-133">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="ed948-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="ed948-134">Použijte šablonu **knihovny tříd Razor** (`razorclasslib`) pomocí příkazu [dotnet New](/dotnet/core/tools/dotnet-new) v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="ed948-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="ed948-135">V následujícím příkladu je vytvořena RCL s názvem `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="ed948-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="ed948-136">Složka, která obsahuje `MyComponentLib1`, je vytvořena automaticky při spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="ed948-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="ed948-137">Pokud se při generování RCL ze šablony používá přepínač `-s|--support-pages-and-views`, přidejte také soubor *_Imports. Razor* do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožňuje vytváření komponent Razor:</span><span class="sxs-lookup"><span data-stu-id="ed948-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="ed948-138">Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.</span><span class="sxs-lookup"><span data-stu-id="ed948-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="ed948-139">Chcete-li přidat knihovnu do existujícího projektu, použijte příkaz [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="ed948-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="ed948-140">V následujícím příkladu se RCL přidá do aplikace.</span><span class="sxs-lookup"><span data-stu-id="ed948-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="ed948-141">Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:</span><span class="sxs-lookup"><span data-stu-id="ed948-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="ed948-142">Využití komponenty knihovny</span><span class="sxs-lookup"><span data-stu-id="ed948-142">Consume a library component</span></span>

<span data-ttu-id="ed948-143">Aby bylo možné využívat komponenty definované v knihovně v jiném projektu, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="ed948-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="ed948-144">Použijte úplný název typu s oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="ed948-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="ed948-145">Pomocí direktivy [using\@](xref:mvc/views/razor#using) Razor.</span><span class="sxs-lookup"><span data-stu-id="ed948-145">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="ed948-146">Jednotlivé komponenty lze přidat podle názvu.</span><span class="sxs-lookup"><span data-stu-id="ed948-146">Individual components can be added by name.</span></span>

<span data-ttu-id="ed948-147">V následujících příkladech je `MyComponentLib1` knihovnou komponent obsahující `SalesReport` komponentu.</span><span class="sxs-lookup"><span data-stu-id="ed948-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="ed948-148">Na součást `SalesReport` lze odkazovat pomocí jejího úplného názvu typu s oborem názvů:</span><span class="sxs-lookup"><span data-stu-id="ed948-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="ed948-149">Na komponentu lze také odkazovat, je-li knihovna přenesena do rozsahu s `@using` direktivou:</span><span class="sxs-lookup"><span data-stu-id="ed948-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="ed948-150">Zahrňte direktivu `@using MyComponentLib1` do souboru *_Import. Razor* nejvyšší úrovně, aby komponenty knihovny byly dostupné pro celý projekt.</span><span class="sxs-lookup"><span data-stu-id="ed948-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="ed948-151">Přidáním direktivy do souboru *_Import. Razor* na libovolné úrovni můžete použít obor názvů na jednu stránku nebo sadu stránek v rámci složky.</span><span class="sxs-lookup"><span data-stu-id="ed948-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="ed948-152">Sestavení, balení a odeslání do NuGet</span><span class="sxs-lookup"><span data-stu-id="ed948-152">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="ed948-153">Knihovny součástí jsou standardní knihovny .NET, balení a jejich odeslání do NuGet se neliší od balení a předává jakékoli knihovny do NuGet.</span><span class="sxs-lookup"><span data-stu-id="ed948-153">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="ed948-154">Balení se provádí pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="ed948-154">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="ed948-155">Nahrajte balíček do NuGet pomocí příkazu [dotnet NuGet push](/dotnet/core/tools/dotnet-nuget-push) v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="ed948-155">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="ed948-156">Vytvoření knihovny tříd součástí Razor se statickými prostředky</span><span class="sxs-lookup"><span data-stu-id="ed948-156">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="ed948-157">RCL může zahrnovat statické prostředky.</span><span class="sxs-lookup"><span data-stu-id="ed948-157">An RCL can include static assets.</span></span> <span data-ttu-id="ed948-158">Statické prostředky jsou k dispozici pro všechny aplikace, které knihovnu využívají.</span><span class="sxs-lookup"><span data-stu-id="ed948-158">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="ed948-159">Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="ed948-159">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ed948-160">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ed948-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
