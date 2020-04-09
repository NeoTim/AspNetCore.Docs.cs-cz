---
title: ASP.NET knihovny tříd komponent Core Razor
author: guardrex
description: Zjistěte, jak lze Blazor komponenty zahrnout do aplikací z knihovny externích komponent.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218763"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="ee92a-103">ASP.NET knihovny tříd komponent Core Razor</span><span class="sxs-lookup"><span data-stu-id="ee92a-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="ee92a-104">Podle [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="ee92a-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="ee92a-105">Součásti mohou být sdíleny v [knihovně třídy Razor (RCL)](xref:razor-pages/ui-class) napříč projekty.</span><span class="sxs-lookup"><span data-stu-id="ee92a-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="ee92a-106">Knihovnu *tříd komponent Razor* lze zahrnout z:</span><span class="sxs-lookup"><span data-stu-id="ee92a-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="ee92a-107">Další projekt v řešení.</span><span class="sxs-lookup"><span data-stu-id="ee92a-107">Another project in the solution.</span></span>
* <span data-ttu-id="ee92a-108">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="ee92a-108">A NuGet package.</span></span>
* <span data-ttu-id="ee92a-109">Odkazovaná knihovna .NET.</span><span class="sxs-lookup"><span data-stu-id="ee92a-109">A referenced .NET library.</span></span>

<span data-ttu-id="ee92a-110">Stejně jako součásti jsou běžné typy .NET, součásti poskytované rcl jsou normální sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="ee92a-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="ee92a-111">Vytvoření rcl</span><span class="sxs-lookup"><span data-stu-id="ee92a-111">Create an RCL</span></span>

<span data-ttu-id="ee92a-112">Postupujte podle <xref:blazor/get-started> pokynů v článku nakonfigurovat prostředí pro Blazor.</span><span class="sxs-lookup"><span data-stu-id="ee92a-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ee92a-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ee92a-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ee92a-114">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="ee92a-114">Create a new project.</span></span>
1. <span data-ttu-id="ee92a-115">Vyberte **knihovnu tříd razor**.</span><span class="sxs-lookup"><span data-stu-id="ee92a-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="ee92a-116">Vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="ee92a-116">Select **Next**.</span></span>
1. <span data-ttu-id="ee92a-117">V **dialogovém** okně Vytvořit novou knihovnu tříd Razor vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ee92a-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="ee92a-118">Zadejte název projektu do pole **Název projektu** nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="ee92a-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="ee92a-119">Příklady v tomto tématu `MyComponentLib1`používají název projektu .</span><span class="sxs-lookup"><span data-stu-id="ee92a-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="ee92a-120">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ee92a-120">Select **Create**.</span></span>
1. <span data-ttu-id="ee92a-121">Přidejte RCL do řešení:</span><span class="sxs-lookup"><span data-stu-id="ee92a-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="ee92a-122">Klikněte pravým tlačítkem myši na řešení.</span><span class="sxs-lookup"><span data-stu-id="ee92a-122">Right-click the solution.</span></span> <span data-ttu-id="ee92a-123">Vyberte **Přidat** > **existující projekt**.</span><span class="sxs-lookup"><span data-stu-id="ee92a-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="ee92a-124">Přejděte do souboru projektu rcl.</span><span class="sxs-lookup"><span data-stu-id="ee92a-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="ee92a-125">Vyberte soubor projektu RCL (*.csproj*).</span><span class="sxs-lookup"><span data-stu-id="ee92a-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="ee92a-126">Přidejte odkaz na RCL z aplikace:</span><span class="sxs-lookup"><span data-stu-id="ee92a-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="ee92a-127">Klikněte pravým tlačítkem myši na projekt aplikace.</span><span class="sxs-lookup"><span data-stu-id="ee92a-127">Right-click the app project.</span></span> <span data-ttu-id="ee92a-128">Vyberte **Přidat** > **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="ee92a-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="ee92a-129">Vyberte projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="ee92a-129">Select the RCL project.</span></span> <span data-ttu-id="ee92a-130">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="ee92a-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="ee92a-131">Pokud je při generování rcl ze šablony zaškrtnuto **políčko Stránky podpory a zobrazení,** přidejte také soubor *_Imports.razor* do kořenového adresáře generovaného projektu s následujícím obsahem, který umožní vytváření komponent Razor:</span><span class="sxs-lookup"><span data-stu-id="ee92a-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="ee92a-132">Ručně přidejte kořenový adresář generovaného projektu.</span><span class="sxs-lookup"><span data-stu-id="ee92a-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ee92a-133">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="ee92a-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="ee92a-134">Použijte šablonu **Knihovny tříd razor** (`razorclasslib`) s novým příkazem [dotnet](/dotnet/core/tools/dotnet-new) v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="ee92a-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="ee92a-135">V následujícím příkladu je vytvořen `MyComponentLib1`rcl s názvem .</span><span class="sxs-lookup"><span data-stu-id="ee92a-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="ee92a-136">Složka, která `MyComponentLib1` obsahuje, je vytvořena automaticky při spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="ee92a-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="ee92a-137">Pokud `-s|--support-pages-and-views` se přepínač používá při generování RCL ze šablony, přidejte také soubor *_Imports.razor* do kořenového adresáře generovaného projektu s následujícím obsahem, který umožní vytváření komponent Razor:</span><span class="sxs-lookup"><span data-stu-id="ee92a-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="ee92a-138">Ručně přidejte kořenový adresář generovaného projektu.</span><span class="sxs-lookup"><span data-stu-id="ee92a-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="ee92a-139">Chcete-li přidat knihovnu do existujícího projektu, použijte příkaz [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="ee92a-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="ee92a-140">V následujícím příkladu rcl je přidán do aplikace.</span><span class="sxs-lookup"><span data-stu-id="ee92a-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="ee92a-141">Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:</span><span class="sxs-lookup"><span data-stu-id="ee92a-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="ee92a-142">Využití komponenty knihovny</span><span class="sxs-lookup"><span data-stu-id="ee92a-142">Consume a library component</span></span>

<span data-ttu-id="ee92a-143">Chcete-li využívat součásti definované v knihovně v jiném projektu, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="ee92a-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="ee92a-144">Použijte úplný název typu s oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="ee92a-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="ee92a-145">Použijte Razor [ \@pomocí](xref:mvc/views/razor#using) směrnice.</span><span class="sxs-lookup"><span data-stu-id="ee92a-145">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="ee92a-146">Jednotlivé součásti lze přidat podle názvu.</span><span class="sxs-lookup"><span data-stu-id="ee92a-146">Individual components can be added by name.</span></span>

<span data-ttu-id="ee92a-147">V následujících příkladech `MyComponentLib1` je knihovna `SalesReport` komponent obsahující komponentu.</span><span class="sxs-lookup"><span data-stu-id="ee92a-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="ee92a-148">Na `SalesReport` komponentu lze odkazovat pomocí úplného názvu typu s oborem názvů:</span><span class="sxs-lookup"><span data-stu-id="ee92a-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="ee92a-149">Komponenta může být také odkazováno, pokud `@using` je knihovna uvedena do oboru pomocí směrnice:</span><span class="sxs-lookup"><span data-stu-id="ee92a-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="ee92a-150">Zahrnout `@using MyComponentLib1` direktivu do souboru *_Import.razor* nejvyšší úrovně, aby byly komponenty knihovny k dispozici pro celý projekt.</span><span class="sxs-lookup"><span data-stu-id="ee92a-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="ee92a-151">Přidejte direktivu do souboru *_Import.razor* na libovolné úrovni, abyste použili obor názvů na jednu stránku nebo sadu stránek ve složce.</span><span class="sxs-lookup"><span data-stu-id="ee92a-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="ee92a-152">Vytvoření knihovny tříd komponent Razor se statickými datovými zdroji</span><span class="sxs-lookup"><span data-stu-id="ee92a-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="ee92a-153">RCL může obsahovat statické prostředky.</span><span class="sxs-lookup"><span data-stu-id="ee92a-153">An RCL can include static assets.</span></span> <span data-ttu-id="ee92a-154">Statické datové zdroje jsou k dispozici pro všechny aplikace, které spotřebovává knihovnu.</span><span class="sxs-lookup"><span data-stu-id="ee92a-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="ee92a-155">Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="ee92a-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="ee92a-156">Sestavení, balení a odeslání do NuGet</span><span class="sxs-lookup"><span data-stu-id="ee92a-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="ee92a-157">Vzhledem k tomu, že knihovny komponent jsou standardní knihovny .NET, balení a jejich odesílání do NuGet se nijak neliší od balení a odesílání libovolné knihovny do NuGet.</span><span class="sxs-lookup"><span data-stu-id="ee92a-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="ee92a-158">Balení se provádí pomocí příkazu [dotnet pack](/dotnet/core/tools/dotnet-pack) v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="ee92a-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="ee92a-159">Nahrajte balíček do NuGet pomocí příkazu [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="ee92a-159">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ee92a-160">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ee92a-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="ee92a-161">Přidání konfiguračního souboru linkeru XML do knihovny</span><span class="sxs-lookup"><span data-stu-id="ee92a-161">Add an XML linker configuration file to a library</span></span>](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
