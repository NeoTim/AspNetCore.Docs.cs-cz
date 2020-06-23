---
title: RazorKnihovny tříd ASP.NET Core komponenty
author: guardrex
description: Zjistěte, jak mohou být součásti součástí Blazor aplikací z externí knihovny součástí.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: 0c6f1330a5bac8ab37c957c5ed4e81678fe1f27d
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242508"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="3be1f-103">RazorKnihovny tříd ASP.NET Core komponenty</span><span class="sxs-lookup"><span data-stu-id="3be1f-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="3be1f-104">Od [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="3be1f-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="3be1f-105">Komponenty lze sdílet v [ Razor knihovně tříd (RCL)](xref:razor-pages/ui-class) napříč projekty.</span><span class="sxs-lookup"><span data-stu-id="3be1f-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="3be1f-106">\* Razor Knihovna tříd komponent\* může obsahovat:</span><span class="sxs-lookup"><span data-stu-id="3be1f-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="3be1f-107">Jiný projekt v řešení.</span><span class="sxs-lookup"><span data-stu-id="3be1f-107">Another project in the solution.</span></span>
* <span data-ttu-id="3be1f-108">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="3be1f-108">A NuGet package.</span></span>
* <span data-ttu-id="3be1f-109">Odkazovaná knihovna .NET.</span><span class="sxs-lookup"><span data-stu-id="3be1f-109">A referenced .NET library.</span></span>

<span data-ttu-id="3be1f-110">Stejně jako komponenty jsou běžné typy .NET, komponenty poskytované RCL jsou normální sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="3be1f-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="3be1f-111">Vytvoření RCL</span><span class="sxs-lookup"><span data-stu-id="3be1f-111">Create an RCL</span></span>

<span data-ttu-id="3be1f-112">Pokud <xref:blazor/get-started> chcete nakonfigurovat prostředí pro, postupujte podle pokynů v článku Blazor .</span><span class="sxs-lookup"><span data-stu-id="3be1f-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3be1f-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3be1f-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3be1f-114">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="3be1f-114">Create a new project.</span></span>
1. <span data-ttu-id="3be1f-115">Vyberte možnost \*\* Razor Knihovna tříd\*\*.</span><span class="sxs-lookup"><span data-stu-id="3be1f-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="3be1f-116">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3be1f-116">Select **Next**.</span></span>
1. <span data-ttu-id="3be1f-117">V dialogovém okně **vytvořit novou Razor knihovnu tříd** vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3be1f-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="3be1f-118">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="3be1f-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="3be1f-119">V příkladech v tomto tématu se používá název projektu `MyComponentLib1` .</span><span class="sxs-lookup"><span data-stu-id="3be1f-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="3be1f-120">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3be1f-120">Select **Create**.</span></span>
1. <span data-ttu-id="3be1f-121">Přidat RCL do řešení:</span><span class="sxs-lookup"><span data-stu-id="3be1f-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="3be1f-122">Klikněte pravým tlačítkem na řešení.</span><span class="sxs-lookup"><span data-stu-id="3be1f-122">Right-click the solution.</span></span> <span data-ttu-id="3be1f-123">Vyberte **Přidat**  >  **existující projekt**.</span><span class="sxs-lookup"><span data-stu-id="3be1f-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="3be1f-124">Přejděte do souboru projektu RCL.</span><span class="sxs-lookup"><span data-stu-id="3be1f-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="3be1f-125">Vyberte soubor projektu RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="3be1f-125">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="3be1f-126">Přidejte odkaz na RCL z aplikace:</span><span class="sxs-lookup"><span data-stu-id="3be1f-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="3be1f-127">Klikněte pravým tlačítkem na projekt aplikace.</span><span class="sxs-lookup"><span data-stu-id="3be1f-127">Right-click the app project.</span></span> <span data-ttu-id="3be1f-128">Vyberte **Přidat**  >  **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="3be1f-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="3be1f-129">Vyberte projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="3be1f-129">Select the RCL project.</span></span> <span data-ttu-id="3be1f-130">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="3be1f-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="3be1f-131">Pokud je zaškrtnuto políčko **stránky podpory a zobrazení** při generování RCL ze šablony, přidejte také `_Imports.razor` soubor do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožní Razor vytváření komponent:</span><span class="sxs-lookup"><span data-stu-id="3be1f-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="3be1f-132">Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.</span><span class="sxs-lookup"><span data-stu-id="3be1f-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3be1f-133">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3be1f-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="3be1f-134">Použijte šablonu \*\* Razor knihovny tříd\*\* ( `razorclasslib` ) s [`dotnet new`](/dotnet/core/tools/dotnet-new) příkazem v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="3be1f-134">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="3be1f-135">V následujícím příkladu je vytvořen RCL s názvem `MyComponentLib1` .</span><span class="sxs-lookup"><span data-stu-id="3be1f-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="3be1f-136">Složka, která je uložena, `MyComponentLib1` je vytvořena automaticky při spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="3be1f-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="3be1f-137">Pokud `-s|--support-pages-and-views` je použit přepínač při generování RCL ze šablony, pak přidejte `_Imports.razor` soubor do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožní Razor vytváření komponent:</span><span class="sxs-lookup"><span data-stu-id="3be1f-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="3be1f-138">Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.</span><span class="sxs-lookup"><span data-stu-id="3be1f-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="3be1f-139">Chcete-li přidat knihovnu do existujícího projektu, použijte [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) příkaz v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="3be1f-139">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="3be1f-140">V následujícím příkladu se RCL přidá do aplikace.</span><span class="sxs-lookup"><span data-stu-id="3be1f-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="3be1f-141">Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:</span><span class="sxs-lookup"><span data-stu-id="3be1f-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="3be1f-142">Využití komponenty knihovny</span><span class="sxs-lookup"><span data-stu-id="3be1f-142">Consume a library component</span></span>

<span data-ttu-id="3be1f-143">Aby bylo možné využívat komponenty definované v knihovně v jiném projektu, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="3be1f-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="3be1f-144">Použijte úplný název typu s oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="3be1f-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="3be1f-145">Razor [`@using`](xref:mvc/views/razor#using) Direktiva use.</span><span class="sxs-lookup"><span data-stu-id="3be1f-145">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="3be1f-146">Jednotlivé komponenty lze přidat podle názvu.</span><span class="sxs-lookup"><span data-stu-id="3be1f-146">Individual components can be added by name.</span></span>

<span data-ttu-id="3be1f-147">V následujících příkladech `MyComponentLib1` je knihovna komponent obsahující `SalesReport` komponentu.</span><span class="sxs-lookup"><span data-stu-id="3be1f-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="3be1f-148">Na `SalesReport` komponentu lze odkazovat pomocí jejího úplného názvu typu s oborem názvů:</span><span class="sxs-lookup"><span data-stu-id="3be1f-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="3be1f-149">Na komponentu lze také odkazovat, je-li knihovna přenesena do rozsahu s `@using` direktivou:</span><span class="sxs-lookup"><span data-stu-id="3be1f-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="3be1f-150">Zahrňte `@using MyComponentLib1` direktivu do souboru nejvyšší úrovně `_Import.razor` , aby komponenty knihovny byly dostupné pro celý projekt.</span><span class="sxs-lookup"><span data-stu-id="3be1f-150">Include the `@using MyComponentLib1` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="3be1f-151">Přidáním direktivy do `_Import.razor` souboru na libovolné úrovni můžete použít obor názvů na jednu stránku nebo sadu stránek v rámci složky.</span><span class="sxs-lookup"><span data-stu-id="3be1f-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="3be1f-152">Vytvoření Razor knihovny tříd komponent se statickými prostředky</span><span class="sxs-lookup"><span data-stu-id="3be1f-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="3be1f-153">RCL může zahrnovat statické prostředky.</span><span class="sxs-lookup"><span data-stu-id="3be1f-153">An RCL can include static assets.</span></span> <span data-ttu-id="3be1f-154">Statické prostředky jsou k dispozici pro všechny aplikace, které knihovnu využívají.</span><span class="sxs-lookup"><span data-stu-id="3be1f-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="3be1f-155">Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="3be1f-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="3be1f-156">Sestavení, balení a odeslání do NuGet</span><span class="sxs-lookup"><span data-stu-id="3be1f-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="3be1f-157">Knihovny součástí jsou standardní knihovny .NET, balení a jejich odeslání do NuGet se neliší od balení a předává jakékoli knihovny do NuGet.</span><span class="sxs-lookup"><span data-stu-id="3be1f-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="3be1f-158">Balení se provádí pomocí [`dotnet pack`](/dotnet/core/tools/dotnet-pack) příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="3be1f-158">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="3be1f-159">Nahrajte balíček do NuGet pomocí [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) příkazu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="3be1f-159">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3be1f-160">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3be1f-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="3be1f-161">Přidání konfiguračního souboru linkeru XML do knihovny</span><span class="sxs-lookup"><span data-stu-id="3be1f-161">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
