---
title: Částí aplikace v ASP.NET Core
author: ardalis
description: Další informace o použití částí aplikace, které jsou abstrakce nad prostředky, které aplikace, vyhledat nebo Vyhněte se načítání funkcí ze sestavení.
ms.author: riande
ms.date: 01/04/2017
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 5d5e79d3afe0eee6d0b126d4642ccd5f61bff409
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901209"
---
# <a name="application-parts-in-aspnet-core"></a>Částí aplikace v ASP.NET Core

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([stažení](xref:index#how-to-download-a-sample))

*Aplikace část* je abstrakcí nad prostředky, které aplikace, ze kterého MVC funkce, jako je řadiče zobrazení komponenty, nebo může být zjištěny pomocných rutin značek. Jedním z příkladů části aplikace je AssemblyPart, který zapouzdřuje odkaz na sestavení a zpřístupňuje typy a odkazy na sestavení. *Funkce poskytovatele* fungují s částí aplikace k naplnění funkce aplikace ASP.NET Core MVC. Případ použití hlavní částí aplikace je k tomu, abyste do konfigurace aplikace pro zjišťování (nebo vyloučit načítání) funkcemi technologie MVC ze sestavení.

## <a name="introducing-application-parts"></a>Úvod do částí aplikace

Aplikace MVC načítat jejich funkcí z [částí aplikace](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart). Zejména v případě [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) třída reprezentuje aplikace část, která je založená na sestavení. Tyto třídy můžete zjišťovat a načíst funkcemi technologie MVC., jako jsou řadiče, zobrazení komponenty, pomocných rutin značek a razor kompilace zdrojů. [ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) zodpovídá za sledování částí aplikace a poskytovatelů funkcí, které jsou k dispozici pro aplikaci MVC. Můžete pracovat `ApplicationPartManager` v `Startup` při konfiguraci MVC:

```csharp
// create an assembly part from a class's assembly
var assembly = typeof(Startup).GetTypeInfo().Assembly;
services.AddMvc()
    .AddApplicationPart(assembly);

// OR
var assembly = typeof(Startup).GetTypeInfo().Assembly;
var part = new AssemblyPart(assembly);
services.AddMvc()
    .ConfigureApplicationPartManager(apm => apm.ApplicationParts.Add(part));
```

Ve výchozím nastavení bude MVC vyhledání stromu závislostí a vyhledat řadiče (i v jiných sestavení). Načtení libovolného sestavení (například z modulu plug-in, který se neodkazuje v době kompilace), můžete použít jako součást aplikace.

Můžete použít částí aplikace na *vyhnout* hledáte řadiče v konkrétní sestavení nebo umístění. Můžete řídit, které části (nebo sestavení) jsou k dispozici pro aplikaci tak, že upravíte `ApplicationParts` kolekce `ApplicationPartManager`. Pořadí položek v `ApplicationParts` kolekce není důležité. Je potřeba nakonfigurovat plně `ApplicationPartManager` před použitím konfigurace služby v kontejneru. Například byste měli plně nakonfigurovat `ApplicationPartManager` před vyvoláním `AddControllersAsServices`. Pokud tak neučiníte, znamená, že je, že to nebude mít vliv volání metody řadiče v částí aplikace přidá za (nebude zaregistrovat jako služba) což může způsobit nesprávné chování vaší aplikace.

Pokud máte sestavení, která obsahuje řadiče nechcete používat, odeberte ho z `ApplicationPartManager`:

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm =>
    {
        var dependentLibrary = apm.ApplicationParts
            .FirstOrDefault(part => part.Name == "DependentLibrary");

        if (dependentLibrary != null)
        {
           apm.ApplicationParts.Remove(dependentLibrary);
        }
    })
```

Kromě sestavení vašeho projektu a jeho závislých sestavení `ApplicationPartManager` bude obsahovat části pro `Microsoft.AspNetCore.Mvc.TagHelpers` a `Microsoft.AspNetCore.Mvc.Razor` ve výchozím nastavení.

## <a name="application-feature-providers"></a>Poskytovatelé funkce aplikací

Funkce poskytovatelů aplikací zkontrolujte částí aplikace a poskytují funkce pro tyto součásti. Existují zprostředkovatelé integrovaná funkce pro následující funkce MVC:

* [Kontrolery](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Odkaz na metadata](/dotnet/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [Pomocné rutiny značek](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Komponenty zobrazení](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Poskytovatelé funkce dědit z `IApplicationFeatureProvider<T>`, kde `T` je typ funkce. Můžete implementovat vlastní funkce, kterou poskytovatelů pro všechny typy MVC funkce uvedené výše. Pořadí poskytovatelů funkcí v `ApplicationPartManager.FeatureProviders` kolekce může být důležité, od poskytovatelů novější můžete reagovat na akce provedené předchozí poskytovatelů.

### <a name="sample-generic-controller-feature"></a>Ukázka: Funkce obecného adaptér

Ve výchozím nastavení, ASP.NET Core MVC ignoruje obecný řadiče (například `SomeController<T>`). Tato ukázka používá poskytovatele funkce kontroleru, který se spustí po výchozího zprostředkovatele a přidá instance obecného kontroleru pro zadaný seznam typů (definované v `EntityTypes.Types`):

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

Typy entit:

[!code-csharp[](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

Poskytovatel funkce bude přidána do `Startup`:

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm => 
        apm.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

Ve výchozím nastavení, názvy obecný kontroleru používaný ke směrování by formuláře *GenericController'1 [widgetu]* místo *Widget*. Tento atribut se používá k úpravě názvu tak, aby odpovídaly obecný typ používaný řadičem:

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

Třída `GenericController`:

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

Výsledek, pokud se požaduje odpovídající trasy:

![Příklad výstupu z ukázkové aplikace načte "Hello z obecného velkými ozubenými řadiče."](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a>Ukázka: Zobrazit dostupné funkce

Můžete iterovat mají údaj vyplněný funkce, které jsou do vaší aplikace pomocí žádosti `ApplicationPartManager` prostřednictvím [injektáž závislostí](../../fundamentals/dependency-injection.md) a použít ho k naplnění instancí odpovídající funkce:

[!code-csharp[](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

Příklad výstupu:

![Ukázkový výstup z ukázkové aplikace](app-parts/_static/available-features.png)
