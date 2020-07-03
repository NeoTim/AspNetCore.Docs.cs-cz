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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: b172059407f9a08dacc0fadd804864c7aee7fb90
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944502"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="fe2a4-103">RazorKnihovny tříd ASP.NET Core komponenty</span><span class="sxs-lookup"><span data-stu-id="fe2a4-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="fe2a4-104">Od [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="fe2a4-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="fe2a4-105">Komponenty lze sdílet v [ Razor knihovně tříd (RCL)](xref:razor-pages/ui-class) napříč projekty.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="fe2a4-106">\* Razor Knihovna tříd komponent\* může obsahovat:</span><span class="sxs-lookup"><span data-stu-id="fe2a4-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="fe2a4-107">Jiný projekt v řešení.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-107">Another project in the solution.</span></span>
* <span data-ttu-id="fe2a4-108">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-108">A NuGet package.</span></span>
* <span data-ttu-id="fe2a4-109">Odkazovaná knihovna .NET.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-109">A referenced .NET library.</span></span>

<span data-ttu-id="fe2a4-110">Stejně jako komponenty jsou běžné typy .NET, komponenty poskytované RCL jsou normální sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="fe2a4-111">Vytvoření RCL</span><span class="sxs-lookup"><span data-stu-id="fe2a4-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe2a4-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe2a4-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="fe2a4-113">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="fe2a4-113">Create a new project.</span></span>
1. <span data-ttu-id="fe2a4-114">Vyberte možnost \*\* Razor Knihovna tříd\*\*.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="fe2a4-115">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-115">Select **Next**.</span></span>
1. <span data-ttu-id="fe2a4-116">V dialogovém okně **vytvořit novou Razor knihovnu tříd** vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="fe2a4-117">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="fe2a4-118">V příkladech v tomto tématu se používá název projektu `MyComponentLib1` .</span><span class="sxs-lookup"><span data-stu-id="fe2a4-118">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="fe2a4-119">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-119">Select **Create**.</span></span>
1. <span data-ttu-id="fe2a4-120">Přidat RCL do řešení:</span><span class="sxs-lookup"><span data-stu-id="fe2a4-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="fe2a4-121">Klikněte pravým tlačítkem na řešení.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-121">Right-click the solution.</span></span> <span data-ttu-id="fe2a4-122">Vyberte **Přidat**  >  **existující projekt**.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="fe2a4-123">Přejděte do souboru projektu RCL.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="fe2a4-124">Vyberte soubor projektu RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="fe2a4-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="fe2a4-125">Přidejte odkaz na RCL z aplikace:</span><span class="sxs-lookup"><span data-stu-id="fe2a4-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="fe2a4-126">Klikněte pravým tlačítkem na projekt aplikace.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-126">Right-click the app project.</span></span> <span data-ttu-id="fe2a4-127">Vyberte **Přidat**  >  **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="fe2a4-128">Vyberte projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-128">Select the RCL project.</span></span> <span data-ttu-id="fe2a4-129">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="fe2a4-130">Pokud je zaškrtnuto políčko **stránky podpory a zobrazení** při generování RCL ze šablony, přidejte také `_Imports.razor` soubor do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožní Razor vytváření komponent:</span><span class="sxs-lookup"><span data-stu-id="fe2a4-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="fe2a4-131">Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="fe2a4-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="fe2a4-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="fe2a4-133">Použijte šablonu \*\* Razor knihovny tříd\*\* ( `razorclasslib` ) s [`dotnet new`](/dotnet/core/tools/dotnet-new) příkazem v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="fe2a4-134">V následujícím příkladu je vytvořen RCL s názvem `MyComponentLib1` .</span><span class="sxs-lookup"><span data-stu-id="fe2a4-134">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="fe2a4-135">Složka, která je uložena, `MyComponentLib1` je vytvořena automaticky při spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="fe2a4-135">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="fe2a4-136">Pokud `-s|--support-pages-and-views` je použit přepínač při generování RCL ze šablony, pak přidejte `_Imports.razor` soubor do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožní Razor vytváření komponent:</span><span class="sxs-lookup"><span data-stu-id="fe2a4-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="fe2a4-137">Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="fe2a4-138">Chcete-li přidat knihovnu do existujícího projektu, použijte [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) příkaz v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="fe2a4-139">V následujícím příkladu se RCL přidá do aplikace.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="fe2a4-140">Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:</span><span class="sxs-lookup"><span data-stu-id="fe2a4-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="fe2a4-141">Využití komponenty knihovny</span><span class="sxs-lookup"><span data-stu-id="fe2a4-141">Consume a library component</span></span>

<span data-ttu-id="fe2a4-142">Aby bylo možné využívat komponenty definované v knihovně v jiném projektu, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fe2a4-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="fe2a4-143">Použijte úplný název typu s oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="fe2a4-144">Razor [`@using`](xref:mvc/views/razor#using) Direktiva use.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="fe2a4-145">Jednotlivé komponenty lze přidat podle názvu.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-145">Individual components can be added by name.</span></span>

<span data-ttu-id="fe2a4-146">V následujících příkladech `MyComponentLib1` je knihovna komponent obsahující `SalesReport` komponentu.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-146">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="fe2a4-147">Na `SalesReport` komponentu lze odkazovat pomocí jejího úplného názvu typu s oborem názvů:</span><span class="sxs-lookup"><span data-stu-id="fe2a4-147">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="fe2a4-148">Na komponentu lze také odkazovat, je-li knihovna přenesena do rozsahu s `@using` direktivou:</span><span class="sxs-lookup"><span data-stu-id="fe2a4-148">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="fe2a4-149">Zahrňte `@using MyComponentLib1` direktivu do souboru nejvyšší úrovně `_Import.razor` , aby komponenty knihovny byly dostupné pro celý projekt.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-149">Include the `@using MyComponentLib1` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="fe2a4-150">Přidáním direktivy do `_Import.razor` souboru na libovolné úrovni můžete použít obor názvů na jednu stránku nebo sadu stránek v rámci složky.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-150">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="fe2a4-151">Vytvoření Razor knihovny tříd komponent se statickými prostředky</span><span class="sxs-lookup"><span data-stu-id="fe2a4-151">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="fe2a4-152">RCL může zahrnovat statické prostředky.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-152">An RCL can include static assets.</span></span> <span data-ttu-id="fe2a4-153">Statické prostředky jsou k dispozici pro všechny aplikace, které knihovnu využívají.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-153">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="fe2a4-154">Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-154">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="fe2a4-155">Sestavení, balení a odeslání do NuGet</span><span class="sxs-lookup"><span data-stu-id="fe2a4-155">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="fe2a4-156">Knihovny součástí jsou standardní knihovny .NET, balení a jejich odeslání do NuGet se neliší od balení a předává jakékoli knihovny do NuGet.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-156">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="fe2a4-157">Balení se provádí pomocí [`dotnet pack`](/dotnet/core/tools/dotnet-pack) příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="fe2a4-157">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="fe2a4-158">Nahrajte balíček do NuGet pomocí [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) příkazu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="fe2a4-158">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe2a4-159">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fe2a4-159">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="fe2a4-160">Přidání konfiguračního souboru linkeru XML do knihovny</span><span class="sxs-lookup"><span data-stu-id="fe2a4-160">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
