---
title: Injektáž závislostí součásti syntaxe Razor
author: guardrex
description: Podívejte se jak Blazor a Razor komponenty aplikace můžou používat integrované služby tak, že je vloženy do komponenty.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/dependency-injection
ms.openlocfilehash: 04be8371b6e746929448b24487b07c08cf03cb78
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55668119"
---
# <a name="razor-components-dependency-injection"></a>Injektáž závislostí součásti syntaxe Razor

Podle [Rainer Stropek](https://www.timecockpit.com)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

[Injektáž závislostí (DI)](/aspnet/core/fundamentals/dependency-injection) je integrovaný. Aplikace můžou používat integrované služby tak, že je vloženy do komponenty. Aplikace můžete také definovat vlastní služby a zpřístupnit prostřednictvím DI.

## <a name="dependency-injection"></a>Injektáž závislostí

DI je technika, pro přístup ke službám, které jsou nakonfigurované v centrálním umístění. To může být užitečné:

* Sdílet jednu instanci třídy služby napříč mnoha komponenty (označované jako *singleton* služby).
* Oddělit součásti od tříd konkrétní konkrétní službu a odkazovat pouze na abstrakce. Například rozhraní `IDataAccess` je implementováno konkrétní třída `DataAccess`. Pokud součást používá DI přijímat `IDataAccess` implementace, komponenta není spojeny s konkrétní typ. Implementace, je možné Prohodit, možná na imitovanou implementaci při testech jednotek.

Systém DI je odpovědný za poskytnutí instance služeb součástí. DI také odstraňuje rekurzivně závislosti, takže můžete vlastních služeb závisí na dalších služeb. DI nastaven při spuštění aplikace. Příklad je uveden dále v tomto tématu.

## <a name="add-services-to-di"></a>Přidání služeb do DI

Po vytvoření nové aplikace, zkontrolujte `Startup.ConfigureServices` metody:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

`ConfigureServices` Metodě se předává [IServiceCollection](/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection), což je seznam objektů služeb popisovač ([ServiceDescriptor](/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor)). Služby jsou přidány tím, že poskytuje služby popisovače ke kolekci služby. Následující příklad kódu ukazuje koncept:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

Služby můžete nakonfigurovat následující životní cyklus:

| Metoda      | Popis |
| ----------- | ----------- |
| [Singleton](/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.singleton#Microsoft_Extensions_DependencyInjection_ServiceDescriptor_Singleton__1_System_Func_System_IServiceProvider___0__) | Vytvoří DI *jednu instanci* služby. Všechny součásti, které vyžadují tuto službu zobrazí odkaz na tuto instanci. |
| [Transient (přechodná)](/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.transient) | Pokaždé, když komponenta vyžaduje této službě, obdrží *novou instanci* služby. |
| [Scoped (vymezená)](/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.scoped) | Na straně klienta Blazor aktuálně nemá koncept DI oborů. `Scoped` se chová jako `Singleton`. Ale podporují ASP.NET Core Razor komponenty `Scoped` životnost. V komponentě Razor registrace vymezené služby působí na připojení. Z tohoto důvodu vymezené služby je upřednostňována pro služby, které by měly být omezeny na aktuální uživatel (i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči). |

Systém DI je založen na systému DI v ASP.NET Core. Další informace najdete v tématu [injektáž závislostí v ASP.NET Core](/aspnet/core/fundamentals/dependency-injection).

## <a name="default-services"></a>Výchozí služby

Výchozí služby se automaticky přidají ke kolekci služby aplikace. V následující tabulce jsou uvedeny některé užitečné výchozí služby k dispozici.

| Metoda       | Popis |
| ------------ | ----------- |
| [HttpClient](/dotnet/api/system.net.http.httpclient) | Poskytuje metody pro odesílání požadavků HTTP a příjem odpovědí HTTP ze zdroje identifikovaného identifikátorem URI (singleton). Všimněte si, že tato instance `HttpClient` používá prohlížeč pro zpracování provozu HTTP na pozadí. [HttpClient.BaseAddress](/dotnet/api/system.net.http.httpclient.baseaddress) se automaticky nastaví na základní identifikátor URI předponu aplikace. `HttpClient` je k dispozici pouze na straně klienta Blazor aplikace. |
| `IJSRuntime` | Představuje instanci modulu runtime jazyka JavaScript, ke kterému může být odesláno volání. Další informace naleznete v tématu <xref:razor-components/javascript-interop>. |
| `IUriHelper` | Pomocné rutiny pro práci se stavem identifikátory URI a navigace (jednotlivý prvek). `IUriHelper` je k dispozici pro obě aplikace Blazor a ASP.NET Core Razor komponenty na straně klienta. |

Všimněte si, že je možné použít vlastní služby poskytovatele namísto výchozího zprostředkovatele služby, která je přičtena pomocí výchozí šablony. Vlastní poskytovatele neposkytuje automaticky výchozí služby uvedené v tabulce. Tyto služby musíte explicitně přidat nového poskytovatele služby.

## <a name="request-a-service-in-a-component"></a>Žádosti o službu v komponentě

Jakmile služby jsou přidány do kolekce služeb, může být vloženy do komponenty šablony Razor pomocí `@inject` direktivu Razor. `@inject` má dva parametry:

* Název typu: Typ služby k vložení.
* Název vlastnosti: Název vlastnosti příjem service vložené aplikace. Všimněte si, že vlastnost nevyžaduje ruční vytvoření. Kompilátor vytvoří vlastnost.

Více `@inject` příkazy lze vložit různé služby.

Následující příklad ukazuje, jak používat `@inject`. Implementace služby `Services.IDataAccess` se vloží do komponenty vlastnosti `DataRepository`. Všimněte si, jak kód využívá jenom `IDataAccess` abstrakce:

```csharp
@page "/customer-list"
@using Services
@inject IDataAccess DataRepository

<ul>
    @if (Customers != null)
    {
        @foreach (var customer in Customers)
        {
            <li>@customer.FirstName @customer.LastName</li>
        }
    }
</ul>

@functions {
    private IReadOnlyList<Customer> Customers;

    protected override async Task OnInitAsync()
    {
        // The property DataRepository received an implementation
        // of IDataAccess through dependency injection. Use 
        // DataRepository to obtain data from the server.
        Customers = await DataRepository.GetAllCustomersAsync();
    }
}
```

Interně jsou vygenerované vlastnosti (`DataRepository`) je upravená pomocí `InjectAttribute` atribut. Tento atribut se obvykle nepoužívá přímo. Pokud základní třída je povinné pro součásti a vloženého vlastnosti se rovněž vyžadují pro základní třídu `InjectAttribute` můžete ručně přidat:

```csharp
public class ComponentBase : BlazorComponent
{
    // Dependency injection works even if using the
    // InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

V součásti odvozené ze základní třídy `@inject` – direktiva není povinné. `InjectAttribute` Základní třídy je dostačující:

```csharp
@page "/demo"
@inherits ComponentBase

<h1>...</h1>
...
```

## <a name="dependency-injection-in-services"></a>Injektáž závislostí služby

Komplexní služby můžou vyžadovat další služby. V předchozím příkladu `DataAccess` může vyžadovat `HttpClient` výchozí služby. `@inject` nebo `InjectAttribute` nelze dají využívat ve službách. *Konstruktor vkládání* musí použít. Přidat parametry do konstruktoru služby přidají požadované služby. Při vkládání závislostí vytvoří službu, rozpozná služby vyžaduje v konstruktoru a poskytuje je odpovídajícím způsobem.

Následující příklad kódu ukazuje koncept:

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
    ...
}
```

Vezměte na vědomí následující požadavky pro vkládání konstruktor:

* Musí existovat jeden konstruktor, jehož argumenty lze všechny splnit vkládání závislostí. Všimněte si, že není pokrytá DI další parametry jsou povoleny, pokud jsou výchozí hodnoty zadané pro ně.
* Použít konstruktor musí být *veřejné*.
* Musí existovat pouze jeden použít konstruktor. V případě nejednoznačnost DI vyvolá výjimku.

## <a name="additional-resources"></a>Další zdroje

* [Injektáž závislostí v ASP.NET Core](/aspnet/core/fundamentals/dependency-injection)
