---
title: Části aplikace v ASP.NET Core
author: ardalis
description: Naučte se, jak používat části aplikace, které jsou abstrakcemi pro prostředky aplikace, ke zjišťování nebo zamezení načítání funkcí ze sestavení.
ms.author: riande
ms.date: 01/04/2017
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 4900ccf5589500db076f8cecd9da198c6a7ceea4
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886467"
---
<!-- DO NOT MAKE CHANGES BEFORE https://github.com/aspnet/AspNetCore.Docs/pull/12376 Merges -->

# <a name="application-parts-in-aspnet-core"></a>Části aplikace v ASP.NET Core

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([stažení](xref:index#how-to-download-a-sample))

*Část aplikace* je abstrakcí pro prostředky aplikace, ze kterých mohou být zjištěny funkce MVC, jako jsou řadiče, zobrazit součásti nebo pomocníky značek. Jedním z příkladů části aplikace je AssemblyPart, který zapouzdřuje odkaz na sestavení a zpřístupňuje typy a odkazy na kompilaci. *Poskytovatelé funkcí* fungují s součástmi aplikace k naplnění funkcí ASP.NET Core aplikace MVC. Hlavním případem použití součástí aplikace je umožnění konfigurace aplikace pro zjišťování (nebo zamezení načítání) funkcí MVC ze sestavení.

## <a name="introducing-application-parts"></a>Představení částí aplikace

Aplikace MVC načítají své funkce z [částí aplikace](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart). Konkrétně třída [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart) představuje součást aplikace, která je zajištěna sestavením. Tyto třídy můžete použít ke zjišťování a načítání funkcí MVC, například řadičů, zobrazení komponent, pomocníků značek a zdrojů kompilace Razor. [ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) zodpovídá za sledování částí aplikace a poskytovatelů funkcí dostupných pro aplikaci MVC. Při konfiguraci MVC můžete s `ApplicationPartManager` nástrojem pracovat: `Startup`

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

Ve výchozím nastavení MVC bude hledat strom závislosti a vyhledat řadiče (dokonce i v jiných sestaveních). Chcete-li načíst libovolné sestavení (například z modulu plug-in, který není odkazován v době kompilace), můžete použít část aplikace.

Můžete použít části aplikace, abyste *se* vyhnuli hledání řadičů v konkrétním sestavení nebo umístění. Můžete určit, které části (nebo sestavení) jsou k dispozici pro aplikaci úpravou `ApplicationParts` kolekce. `ApplicationPartManager` Pořadí položek v `ApplicationParts` kolekci není důležité. Je důležité, abyste před tím, `ApplicationPartManager` než je použijete ke konfiguraci služeb v kontejneru, plně nakonfigurovali. Například je třeba `ApplicationPartManager` před vyvoláním `AddControllersAsServices`plně nakonfigurovat. Pokud se to nepovede, znamená to, že řadiče v součástech aplikace přidané po volání této metody nebudou ovlivněny (nebudou se registrovat jako služby), což by mohlo vést k nesprávnému chování vaší aplikace.

Pokud máte sestavení, které obsahuje řadiče, které nechcete používat, odeberte ho z `ApplicationPartManager`:

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

Kromě sestavení projektu a jeho závislých sestavení `ApplicationPartManager` bude obsahovat části pro `Microsoft.AspNetCore.Mvc.TagHelpers` a `Microsoft.AspNetCore.Mvc.Razor` ve výchozím nastavení.

## <a name="application-feature-providers"></a>Poskytovatelé funkcí aplikace

Poskytovatelé funkcí aplikací zkoumají části aplikace a poskytují funkce pro tyto části. K dispozici jsou předdefinovaná poskytovatelé funkcí pro následující funkce MVC:

* [Kontrolery](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Odkaz na metadata](/dotnet/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [Pomocné rutiny značek](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Zobrazit součásti](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Poskytovatelé funkcí dědí `IApplicationFeatureProvider<T>`z, `T` kde je typ funkce. Můžete implementovat své vlastní poskytovatele funkcí pro všechny typy funkcí MVC uvedené výše. Pořadí poskytovatelů funkcí v `ApplicationPartManager.FeatureProviders` kolekci může být důležité, protože další poskytovatelé můžou reagovat na akce prováděné předchozími poskytovateli.

### <a name="sample-generic-controller-feature"></a>Vzorku Funkce obecného kontroleru

Ve výchozím nastavení ASP.NET Core MVC ignoruje obecné řadiče (například `SomeController<T>`). Tato ukázka používá poskytovatele funkcí kontroleru, který se spouští po výchozím zprostředkovateli a přidává instance obecného kontroleru pro zadaný seznam typů (definované v `EntityTypes.Types`):

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

Typy entit:

[!code-csharp[](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

Poskytovatel funkce je přidaný v `Startup`:

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm => 
        apm.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

Ve výchozím nastavení mají názvy obecného kontroleru používané pro směrování tvar *GenericController ' 1 [widget]* namísto *widgetu*. Následující atribut se používá ke změně názvu tak, aby odpovídal obecnému typu používanému řadičem:

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

Třída `GenericController`:

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

Výsledek, pokud je požadována vyhovující trasa:

![Ukázkový výstup z ukázkového čtení aplikace "Hello z obecného Sprocket kontroleru"](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a>Vzorku Zobrazit dostupné funkce

Vyplněné funkce, které jsou k dispozici pro vaši aplikaci, `ApplicationPartManager` můžete iterovat vyžádáním [vložením závislosti](../../fundamentals/dependency-injection.md) a jejím použitím k naplnění instancí příslušných funkcí:

[!code-csharp[](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

Příklad výstupu:

![Příklad výstupu ukázkové aplikace](app-parts/_static/available-features.png)
