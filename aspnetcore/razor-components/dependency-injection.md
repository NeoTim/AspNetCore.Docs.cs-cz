---
title: Injektáž závislostí součásti syntaxe Razor
author: guardrex
description: Podívejte se jak Blazor a Razor komponenty aplikace můžou používat služby ve vkládání do komponenty.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2019
uid: razor-components/dependency-injection
ms.openlocfilehash: 804fdf0254723f5e5913f23c815f485bbf094321
ms.sourcegitcommit: d913bca90373c07f89b1d1df01af5fc01fc908ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57978500"
---
# <a name="razor-components-dependency-injection"></a>Injektáž závislostí součásti syntaxe Razor

Podle [Rainer Stropek](https://www.timecockpit.com)

Podporuje Razor komponenty [injektáž závislostí (DI)](xref:fundamentals/dependency-injection). Aplikace můžete použít integrované služby vkládání do komponenty. Aplikace můžete také definovat a registrovat vlastní služby a zpřístupnit je v celé aplikaci prostřednictvím DI.

## <a name="dependency-injection"></a>Injektáž závislostí

DI je technika, pro přístup ke službám, které jsou nakonfigurované v centrálním umístění. To může být užitečné v Razor součásti aplikace:

* Sdílet jednu instanci třídy služby mezi řadu součástí, označované jako *singleton* služby.
* Pomocí odkazu abstrakce oddělte součásti od tříd konkrétní služby. Představte si třeba rozhraní `IDataAccess` pro přístup k datům v aplikaci. Rozhraní je implementováno konkrétní `DataAccess` třídy a zaregistrováno jako služba v kontejneru aplikace služby. Pokud součást používá DI přijímat `IDataAccess` implementace, komponenta není spojeny s konkrétní typ. Implementace, je možné Prohodit, možná na imitovanou implementaci při testech jednotek.

Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="add-services-to-di"></a>Přidání služeb do DI

Po vytvoření nové aplikace, zkontrolujte `Startup.ConfigureServices` metody:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

`ConfigureServices` Metodě se předává <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, což je seznam objektů služeb popisovač (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>). Služby jsou přidány tím, že poskytuje služby popisovače ke kolekci služby. Následující příklad ukazuje neznají koncept `IDataAccess` rozhraní a jeho konkrétní implementaci `DataAccess`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

Služby můžete nakonfigurovat s životností je znázorněno v následující tabulce.

| Životnost | Popis |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | Vytvoří DI *jednu instanci* služby. Všechny součásti, které vyžadují tuto službu zobrazí odkaz na tuto instanci. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Pokaždé, když komponenta vyžaduje této službě, obdrží *novou instanci* služby. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Na straně klienta Blazor aktuálně nemá koncept DI oborů. `Scoped` se chová jako `Singleton`. Ale podporují ASP.NET Core Razor komponenty `Scoped` životnost. V komponentě Razor registrace vymezené služby působí na připojení. Z tohoto důvodu vymezené služby je upřednostňována pro služby, které by měly být omezeny aktuálního uživatele, i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči. |

Systém DI je založen na systému DI v ASP.NET Core. Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="default-services"></a>Výchozí služby

Výchozí služby jsou automaticky přidány do aplikace služby kolekce.

| Služba | Popis |
| ------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Poskytuje metody pro odesílání požadavků HTTP a příjem odpovědí HTTP ze zdroje identifikovaného identifikátorem URI (singleton). Všimněte si, že tato instance `HttpClient` používá prohlížeč pro zpracování provozu HTTP na pozadí. [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) se automaticky nastaví na základní identifikátor URI předponu aplikace. `HttpClient` je k dispozici pouze na straně klienta Blazor aplikace. |
| `IJSRuntime` | Představuje instanci modulu runtime jazyka JavaScript, ke kterému může být odesláno volání. Další informace naleznete v tématu <xref:razor-components/javascript-interop>. |
| `IUriHelper` | Obsahuje pomocné rutiny pro práci se stavem identifikátory URI a navigace (jednotlivý prvek). `IUriHelper` je k dispozici na Blazor a Razor součásti aplikace. |

Je možné použít vlastní poskytovatele namísto výchozího poskytovatele služby přidá výchozí šablony. Vlastní poskytovatele neposkytuje automaticky výchozí služby uvedené v tabulce. Pokud používáte vlastní poskytovatele a kterékoli ze služeb, které jsou uvedené v tabulce vyžadují, přidejte do nového poskytovatele služby požadované služby.

## <a name="request-a-service-in-a-component"></a>Žádosti o službu v komponentě

Po přidání služby do kolekce služeb, vložit do šablony Razor komponenty pomocí služeb [ @inject ](xref:mvc/views/razor#section-4) direktivu Razor. `@inject` má dva parametry:

* Název typu: Typ služby k vložení.
* Název vlastnosti: Název vlastnosti příjem service vložené aplikace. Všimněte si, že vlastnost nevyžaduje ruční vytvoření. Kompilátor vytvoří vlastnost.

Další informace naleznete v tématu <xref:mvc/views/dependency-injection>.

Použití více `@inject` příkazy vkládat různé služby.

Následující příklad ukazuje, jak používat `@inject`. Implementace služby `Services.IDataAccess` se vloží do komponenty vlastnosti `DataRepository`. Všimněte si, jak kód využívá jenom `IDataAccess` abstrakce:

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.cshtml?highlight=2-3,23)]

Interně jsou vygenerované vlastnosti (`DataRepository`) je upravená pomocí `InjectAttribute` atribut. Tento atribut se obvykle nepoužívá přímo. Pokud základní třída je povinné pro součásti a vloženého vlastnosti se rovněž vyžadují pro základní třídu `InjectAttribute` můžete ručně přidat:

```csharp
public class ComponentBase : IComponent
{
    // Dependency injection works even if using the
    // InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

V součásti odvozené ze základní třídy `@inject` – direktiva není povinné. `InjectAttribute` Základní třídy je dostačující:

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="dependency-injection-in-services"></a>Injektáž závislostí služby

Komplexní služby můžou vyžadovat další služby. V předchozím příkladu `DataAccess` může vyžadovat `HttpClient` výchozí služby. `@inject` (nebo `InjectAttribute`) není k dispozici pro použití služby. *Konstruktor vkládání* musí použít. Přidat parametry do konstruktoru služby přidají požadované služby. Při vkládání závislostí vytvoří službu, rozpozná služby vyžaduje v konstruktoru a poskytuje je odpovídajícím způsobem.

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

Předpoklady pro vkládání konstruktor:

* Musí existovat jeden konstruktor, jehož argumenty lze všechny splnit vkládání závislostí. Všimněte si, že není pokrytá DI další parametry jsou povoleny, pokud se určení výchozích hodnot.
* Použít konstruktor musí být *veřejné*.
* Musí existovat pouze jeden použít konstruktor. V případě nejednoznačnost DI vyvolá výjimku.

## <a name="additional-resources"></a>Další zdroje

* < xref:fundamentals / injektáž závislostí
* <xref:mvc/views/dependency-injection>
