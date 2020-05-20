---
<span data-ttu-id="ba080-101">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-101">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-103">'Blazor'</span></span>
- <span data-ttu-id="ba080-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-104">'Identity'</span></span>
- <span data-ttu-id="ba080-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-106">'Razor'</span></span>
- <span data-ttu-id="ba080-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-107">'SignalR' uid:</span></span> 

---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="ba080-108">Konfigurace linkeru pro ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="ba080-108">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="ba080-109">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ba080-109">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="ba080-110">Při vystřihování nepotřebného IL z výstupních sestavení aplikace provede rozhraní WebAssembly propojení s [pokročilým jazykem (IL)](/dotnet/standard/managed-code#intermediate-language--execution) během sestavení.</span><span class="sxs-lookup"><span data-stu-id="ba080-110"> WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="ba080-111">Linker je při sestavování konfigurace ladění zakázán.</span><span class="sxs-lookup"><span data-stu-id="ba080-111">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="ba080-112">Aby bylo možné linker povolit, musí aplikace sestavit v konfiguraci vydání.</span><span class="sxs-lookup"><span data-stu-id="ba080-112">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="ba080-113">Při nasazování aplikací WebAssembly doporučujeme sestavovat v vydaných verzích Blazor .</span><span class="sxs-lookup"><span data-stu-id="ba080-113">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="ba080-114">Propojení aplikace se optimalizuje pro velikost, ale může mít škodlivé účinky.</span><span class="sxs-lookup"><span data-stu-id="ba080-114">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="ba080-115">Aplikace, které používají reflexi nebo související dynamické funkce, mohou být při oříznutí přerušeny, protože linker neví o tomto dynamickém chování a nemůže určit obecně, které typy jsou požadovány pro reflexi za běhu.</span><span class="sxs-lookup"><span data-stu-id="ba080-115">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="ba080-116">Aby bylo možné tyto aplikace oříznout, musí být linker informován o jakýchkoli typech vyžadovaných odrazem v kódu a v balíčcích nebo architekturách, na kterých aplikace závisí.</span><span class="sxs-lookup"><span data-stu-id="ba080-116">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="ba080-117">Aby se zajistilo, že bude aplikace po nasazení správně fungovat, je důležité otestovat sestavení vydaných verzí aplikace často během vývoje.</span><span class="sxs-lookup"><span data-stu-id="ba080-117">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="ba080-118">Odkazy na Blazor aplikace lze konfigurovat pomocí těchto funkcí nástroje MSBuild:</span><span class="sxs-lookup"><span data-stu-id="ba080-118">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="ba080-119">Nakonfigurujte globálně propojení s [vlastností MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="ba080-119">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="ba080-120">Řízení propojení podle jednotlivých sestavení pomocí [konfiguračního souboru](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="ba080-120">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="ba080-121">Řízení propojení pomocí vlastnosti MSBuild</span><span class="sxs-lookup"><span data-stu-id="ba080-121">Control linking with an MSBuild property</span></span>

<span data-ttu-id="ba080-122">Propojení je povoleno, pokud je aplikace integrována v `Release` konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="ba080-122">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="ba080-123">Chcete-li toto nastavení změnit, nakonfigurujte `BlazorWebAssemblyEnableLinking` vlastnost MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="ba080-123">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="ba080-124">Řízení propojování pomocí konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="ba080-124">Control linking with a configuration file</span></span>

<span data-ttu-id="ba080-125">Řízení propojení na základě sestavení zadáním konfiguračního souboru XML a zadáním souboru jako položky MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="ba080-125">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="ba080-126">*LinkerConfig. XML*:</span><span class="sxs-lookup"><span data-stu-id="ba080-126">*LinkerConfig.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="ba080-127">Další informace a příklady naleznete v tématu [formáty dat (úložiště GitHub mono/Linker)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="ba080-127">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="ba080-128">Přidání konfiguračního souboru linkeru XML do knihovny</span><span class="sxs-lookup"><span data-stu-id="ba080-128">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="ba080-129">Chcete-li nakonfigurovat linker pro konkrétní knihovnu, přidejte konfigurační soubor linkeru XML do knihovny jako vložený prostředek.</span><span class="sxs-lookup"><span data-stu-id="ba080-129">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="ba080-130">Vložený prostředek musí mít stejný název jako sestavení.</span><span class="sxs-lookup"><span data-stu-id="ba080-130">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="ba080-131">V následujícím příkladu je soubor *LinkerConfig. XML* zadán jako vložený prostředek, který má stejný název jako sestavení knihovny:</span><span class="sxs-lookup"><span data-stu-id="ba080-131">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="ba080-132">Konfigurace linkeru pro mezinárodní využití</span><span class="sxs-lookup"><span data-stu-id="ba080-132">Configure the linker for internationalization</span></span>

<span data-ttu-id="ba080-133">Ve výchozím nastavení se Blazor Konfigurace linkeru pro Blazor aplikace pro WebAssembly odříznout informace o mezinárodním prostředí s výjimkou výslovně požadovaných místních hodnot.</span><span class="sxs-lookup"><span data-stu-id="ba080-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="ba080-134">Odebrání těchto sestavení minimalizuje velikost aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-134">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="ba080-135">Chcete-li určit, která sestavení I18N jsou zachována, nastavte `<BlazorWebAssemblyI18NAssemblies>` vlastnost MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="ba080-135">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="ba080-136">Hodnota oblasti</span><span class="sxs-lookup"><span data-stu-id="ba080-136">Region Value</span></span>     | <span data-ttu-id="ba080-137">Sestavení oblasti mono</span><span class="sxs-lookup"><span data-stu-id="ba080-137">Mono region assembly</span></span>    |
| ---
<span data-ttu-id="ba080-138">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-138">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-139">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-140">'Blazor'</span></span>
- <span data-ttu-id="ba080-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-141">'Identity'</span></span>
- <span data-ttu-id="ba080-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-143">'Razor'</span></span>
- <span data-ttu-id="ba080-144">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-144">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba080-145">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-145">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-146">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-147">'Blazor'</span></span>
- <span data-ttu-id="ba080-148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-148">'Identity'</span></span>
- <span data-ttu-id="ba080-149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-149">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-150">'Razor'</span></span>
- <span data-ttu-id="ba080-151">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-151">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba080-152">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-152">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-153">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-154">'Blazor'</span></span>
- <span data-ttu-id="ba080-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-155">'Identity'</span></span>
- <span data-ttu-id="ba080-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-157">'Razor'</span></span>
- <span data-ttu-id="ba080-158">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba080-159">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-159">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-160">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-161">'Blazor'</span></span>
- <span data-ttu-id="ba080-162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-162">'Identity'</span></span>
- <span data-ttu-id="ba080-163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-163">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-164">'Razor'</span></span>
- <span data-ttu-id="ba080-165">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba080-166">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-166">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-167">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-168">'Blazor'</span></span>
- <span data-ttu-id="ba080-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-169">'Identity'</span></span>
- <span data-ttu-id="ba080-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-171">'Razor'</span></span>
- <span data-ttu-id="ba080-172">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba080-173">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-173">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-174">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-175">'Blazor'</span></span>
- <span data-ttu-id="ba080-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-176">'Identity'</span></span>
- <span data-ttu-id="ba080-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-178">'Razor'</span></span>
- <span data-ttu-id="ba080-179">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-179">'SignalR' uid:</span></span> 

<span data-ttu-id="ba080-180">-------- | ---title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-180">-------- | --- title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-181">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-182">'Blazor'</span></span>
- <span data-ttu-id="ba080-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-183">'Identity'</span></span>
- <span data-ttu-id="ba080-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-185">'Razor'</span></span>
- <span data-ttu-id="ba080-186">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba080-187">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-187">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-188">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-189">'Blazor'</span></span>
- <span data-ttu-id="ba080-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-190">'Identity'</span></span>
- <span data-ttu-id="ba080-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-192">'Razor'</span></span>
- <span data-ttu-id="ba080-193">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba080-194">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-194">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-195">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-196">'Blazor'</span></span>
- <span data-ttu-id="ba080-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-197">'Identity'</span></span>
- <span data-ttu-id="ba080-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-199">'Razor'</span></span>
- <span data-ttu-id="ba080-200">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba080-201">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-201">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-202">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-203">'Blazor'</span></span>
- <span data-ttu-id="ba080-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-204">'Identity'</span></span>
- <span data-ttu-id="ba080-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-206">'Razor'</span></span>
- <span data-ttu-id="ba080-207">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba080-208">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-208">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-209">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-210">'Blazor'</span></span>
- <span data-ttu-id="ba080-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-211">'Identity'</span></span>
- <span data-ttu-id="ba080-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-213">'Razor'</span></span>
- <span data-ttu-id="ba080-214">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba080-215">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-215">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-216">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-217">'Blazor'</span></span>
- <span data-ttu-id="ba080-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-218">'Identity'</span></span>
- <span data-ttu-id="ba080-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-220">'Razor'</span></span>
- <span data-ttu-id="ba080-221">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba080-222">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-222">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-223">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-224">'Blazor'</span></span>
- <span data-ttu-id="ba080-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-225">'Identity'</span></span>
- <span data-ttu-id="ba080-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-227">'Razor'</span></span>
- <span data-ttu-id="ba080-228">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba080-229">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-229">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-230">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-231">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-231">'Blazor'</span></span>
- <span data-ttu-id="ba080-232">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-232">'Identity'</span></span>
- <span data-ttu-id="ba080-233">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-233">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-234">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-234">'Razor'</span></span>
- <span data-ttu-id="ba080-235">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba080-236">title: ' konfigurace linkeru pro ASP.NET Core Blazor ' Author: Description: ' Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba080-236">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="ba080-237">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba080-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba080-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba080-238">'Blazor'</span></span>
- <span data-ttu-id="ba080-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba080-239">'Identity'</span></span>
- <span data-ttu-id="ba080-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba080-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba080-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba080-241">'Razor'</span></span>
- <span data-ttu-id="ba080-242">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba080-242">'SignalR' uid:</span></span> 

<span data-ttu-id="ba080-243">------------ | | `all`            | Všechna sestavení včetně | | `cjk`             |  *I18n. CJK. dll* | | `mideast`         |  *I18n. MidEast. dll* | | `none`(výchozí) | Žádné | | `other`           |  *I18n. Jiné. dll* | | `rare`            |  *I18n. Vzácná knihovna DLL* | | `west`            |  *I18n. Západ. dll*         |</span><span class="sxs-lookup"><span data-stu-id="ba080-243">------------ | | `all`            | All assemblies included | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (default) | None                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span></span>

<span data-ttu-id="ba080-244">Oddělte více hodnot pomocí čárky (například `mideast,west` ).</span><span class="sxs-lookup"><span data-stu-id="ba080-244">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="ba080-245">Další informace najdete v tématu [i18n: knihovna rozhraní Pnetlib pro mezinárodní navýšení (mono/mono úložiště GitHub)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="ba080-245">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ba080-246">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ba080-246">Additional resources</span></span>

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
