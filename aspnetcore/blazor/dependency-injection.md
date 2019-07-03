---
title: Injektáž závislostí ASP.NET Core Blazor
author: guardrex
description: Podívejte se, jak aplikace Blazor služby vložit do komponenty.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/dependency-injection
ms.openlocfilehash: 394d99656ba52f6c561007121e63634c7cb84f37
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538474"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>Injektáž závislostí ASP.NET Core Blazor

Podle [Rainer Stropek](https://www.timecockpit.com)

Podporuje Blazor [injektáž závislostí (DI)](xref:fundamentals/dependency-injection). Aplikace můžete použít integrované služby vkládání do komponenty. Aplikace můžete také definovat a registrovat vlastní služby a zpřístupnit je v celé aplikaci prostřednictvím DI.

DI je technika, pro přístup ke službám, které jsou nakonfigurované v centrálním umístění. To může být užitečné v Blazor aplikací:

* Sdílet jednu instanci třídy služby mezi řadu součástí, označované jako *singleton* služby.
* Pomocí odkazu abstrakce oddělte součásti od tříd konkrétní služby. Představte si třeba rozhraní `IDataAccess` pro přístup k datům v aplikaci. Rozhraní je implementováno konkrétní `DataAccess` třídy a zaregistrováno jako služba v kontejneru aplikace služby. Pokud součást používá DI přijímat `IDataAccess` implementace, komponenta není spojeny s konkrétní typ. Implementace, je možné Prohodit, třeba pro imitovanou implementaci při testech jednotek.

## <a name="default-services"></a>Výchozí služby

Výchozí služby jsou automaticky přidány do aplikace služby kolekce.

| Služba | Životnost | Popis |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | Poskytuje metody pro odesílání požadavků HTTP a příjem odpovědí HTTP ze zdroje identifikovaného identifikátorem URI. Všimněte si, že tato instance `HttpClient` používá prohlížeč pro zpracování provozu HTTP na pozadí. [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) se automaticky nastaví na základní identifikátor URI předponu aplikace. Další informace naleznete v tématu <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton | Představuje instanci modulu runtime jazyka JavaScript, kde jsou odeslány volání JavaScriptu. Další informace naleznete v tématu <xref:blazor/javascript-interop>. |
| `IUriHelper` | Singleton | Obsahuje pomocné rutiny pro práci s identifikátory URI a navigační stav. Další informace najdete v tématu [identifikátor URI a navigační stav pomocné rutiny](xref:blazor/routing#uri-and-navigation-state-helpers). |

Vlastní poskytovatele neposkytuje automaticky výchozí služby uvedené v tabulce. Pokud používáte vlastní poskytovatele a kterékoli ze služeb, které jsou uvedené v tabulce vyžadují, přidejte do nového poskytovatele služby požadované služby.

## <a name="add-services-to-an-app"></a>Přidání služby do aplikace

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
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor klientů aktuálně nemá koncept DI obory. `Scoped`-registrovaných služeb se chovat jako `Singleton` služby. Ale podporuje model hostingu na straně serveru `Scoped` životnost. V komponentě Razor registrace vymezené služby působí na připojení. Z tohoto důvodu vymezené služby je upřednostňována pro služby, které by měly být omezeny aktuálního uživatele, i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | Vytvoří DI *jednu instanci* služby. Všechny součásti, které vyžadují `Singleton` služba přijímat instance ve stejné službě. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Vždy, když součást získá instanci `Transient` služby z kontejneru služeb přijme *novou instanci* služby. |

Systém DI je založen na systému DI v ASP.NET Core. Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Žádosti o službu v komponentě

Po přidání služby do kolekce služeb, vložit do komponenty pomocí služeb [ \@vložit](xref:mvc/views/razor#section-4) direktivu Razor. `@inject` má dva parametry:

* Typ &ndash; typ služby k vložení.
* Vlastnost &ndash; název vlastnosti příjem service vložené aplikace. Vlastnost nevyžaduje ruční vytvoření. Kompilátor vytvoří vlastnost.

Další informace naleznete v tématu <xref:mvc/views/dependency-injection>.

Použití více `@inject` příkazy vkládat různé služby.

Následující příklad ukazuje, jak používat `@inject`. Implementace služby `Services.IDataAccess` se vloží do komponenty vlastnosti `DataRepository`. Všimněte si, jak kód využívá jenom `IDataAccess` abstrakce:

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Interně jsou vygenerované vlastnosti (`DataRepository`) je upravená pomocí `InjectAttribute` atribut. Tento atribut se obvykle nepoužívá přímo. Pokud základní třída je povinné pro součásti a vloženého vlastnosti se rovněž vyžadují pro základní třídu, ručně přidejte `InjectAttribute`:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
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

## <a name="use-di-in-services"></a>Použití DI služby

Komplexní služby můžou vyžadovat další služby. V předchozím příkladu `DataAccess` může vyžadovat `HttpClient` výchozí služby. `@inject` (nebo `InjectAttribute`) není k dispozici pro použití služby. *Konstruktor vkládání* musí použít. Přidat parametry do konstruktoru služby přidají požadované služby. Když DI vytvoří službu, rozpozná služby vyžaduje v konstruktoru a poskytuje je odpovídajícím způsobem.

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

* Jeden konstruktor musí existovat, jehož argumenty lze všechny splnit DI. Další parametry, které se nevztahuje DI jsou povolené, pokud se určení výchozích hodnot.
* Použít konstruktor musí být *veřejné*.
* Jeden použít konstruktor, musí existovat. V případě nejednoznačnost DI vyvolá výjimku.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
