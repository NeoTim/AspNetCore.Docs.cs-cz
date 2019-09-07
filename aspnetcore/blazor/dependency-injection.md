---
title: Vkládání závislostí ASP.NET Core Blazor
author: guardrex
description: Podívejte se, jak aplikace Blazor můžou vkládat služby do součástí.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/dependency-injection
ms.openlocfilehash: 0b48cd0cbe14d2b07627f56ab78611bbd3209fa1
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800391"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>Vkládání závislostí ASP.NET Core Blazor

Od [Rainer Stropek](https://www.timecockpit.com)

Blazor podporuje [vkládání závislostí (di)](xref:fundamentals/dependency-injection). Aplikace mohou používat vestavěné služby jejich vložením do komponent. Aplikace můžou také definovat a registrovat vlastní služby a zpřístupnit je v celé aplikaci přes DI.

DI je technika přístupu ke službám nakonfigurovaným v centrálním umístění. To může být užitečné v aplikacích Blazor k těmto akcím:

* Sdílejte jednu instanci třídy služby napříč mnoha komponentami, která se označuje jako služba typu *singleton* .
* Oddělit komponenty od konkrétních tříd služeb pomocí abstrakcí odkazů. Představte si třeba rozhraní `IDataAccess` pro přístup k datům v aplikaci. Rozhraní je implementováno konkrétní `DataAccess` třídou a registrováno jako služba v kontejneru služby aplikace. Pokud komponenta používá di pro příjem `IDataAccess` implementace, komponenta není spojena se konkrétním typem. Implementaci je možné prohodit, třeba pro podrobnější implementaci v testování částí.

## <a name="default-services"></a>Výchozí služby

Výchozí služby se automaticky přidají do kolekce služeb aplikace.

| Služba | Životnost | Popis |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | Poskytuje metody pro posílání požadavků HTTP a příjem odpovědí HTTP z prostředku identifikovaného identifikátorem URI. Všimněte si, že tato `HttpClient` instance nástroje používá prohlížeč pro zpracování provozu http na pozadí. [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) se automaticky nastaví na základní PŘEDPONu identifikátoru URI aplikace. Další informace naleznete v tématu <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton | Představuje instanci modulu runtime jazyka JavaScript, kde jsou odesílána volání jazyka JavaScript. Další informace naleznete v tématu <xref:blazor/javascript-interop>. |
| `NavigationManager` | Singleton | Obsahuje nápovědu pro práci s identifikátory URI a stavem navigace. Další informace najdete v tématu věnovaném [identifikátorům URI a nápovědě k informacím o stavu navigace](xref:blazor/routing#uri-and-navigation-state-helpers). |

Vlastní zprostředkovatel služeb automaticky neposkytuje výchozí služby uvedené v tabulce. Pokud používáte vlastního poskytovatele služeb a potřebujete některou ze služeb zobrazených v tabulce, přidejte požadované služby k novému poskytovateli služeb.

## <a name="add-services-to-an-app"></a>Přidání služeb do aplikace

Po vytvoření nové aplikace si Projděte tuto `Startup.ConfigureServices` metodu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Metoda je předána, což je seznam objektů deskriptoru služby (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>). <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> `ConfigureServices` Služby se přidávají tím, že se do kolekce služeb poskytují popisovače služby. Následující příklad ukazuje koncept s `IDataAccess` rozhraním a jeho konkrétní implementací: `DataAccess`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

Služby je možné konfigurovat s životností, která jsou uvedená v následující tabulce.

| Životnost | Popis |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor aplikace pro WebAssembly aktuálně nemají koncept typu DI obory. `Scoped`– registrované služby se chovají jako `Singleton` služby. Model hostování na straně serveru však podporuje `Scoped` dobu života. V aplikacích Blazor Server je obor registrace služby vymezená na *připojení*. Z tohoto důvodu je vhodnější použití oboru služeb pro služby, které by měly být vymezeny na aktuálního uživatele, a to i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI vytvoří *jednu instanci* služby. Všechny součásti, které `Singleton` vyžadují službu, obdrží instanci stejné služby. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Pokaždé, když komponenta získá instanci `Transient` služby z kontejneru služby, obdrží *novou instanci* služby. |

Systém DI je založený na systému DI v ASP.NET Core. Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Vyžádání služby v součásti

Po přidání služeb do kolekce služeb tyto služby vloží do komponent pomocí [ \@](xref:mvc/views/razor#inject) direktivy vložení Razor. `@inject`má dva parametry:

* Zadejte &ndash; typ služby, kterou chcete vložit.
* Vlastnost &ndash; název vlastnosti, která přijímá vloženou službu App Service. Vlastnost nevyžaduje ruční vytvoření. Kompilátor vytvoří vlastnost.

Další informace naleznete v tématu <xref:mvc/views/dependency-injection>.

Pro vložení `@inject` různých služeb použijte více příkazů.

Následující příklad ukazuje, jak použít `@inject`. Implementace `Services.IDataAccess` služby je vložena do vlastnosti `DataRepository`komponenty. Všimněte si, jak kód používá `IDataAccess` abstrakci:

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Interně je vygenerovaná vlastnost`DataRepository`() upravena `InjectAttribute` atributem. Obvykle se tento atribut nepoužívá přímo. Pokud je vyžadována základní třída pro součásti a vložené vlastnosti jsou také požadovány pro základní třídu, přidejte `InjectAttribute`ručně:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

V součástech odvozených ze základní třídy `@inject` není direktiva vyžadována. `InjectAttribute` Základní třída je dostačující:

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Použití DI v službách

Komplexní služby můžou vyžadovat další služby. V předchozím příkladu `DataAccess` může `HttpClient` vyžadovat výchozí službu. `@inject`(nebo) `InjectAttribute`nejsou k dispozici pro použití v rámci služeb. Místo toho se musí použít *Injektáže konstruktoru* . Požadované služby jsou přidány přidáním parametrů do konstruktoru služby. Když DI vytvoří službu, rozpoznává služby, které vyžaduje v konstruktoru, a odpovídajícím způsobem je poskytne.

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

Předpoklady pro vložení konstruktoru:

* Je nutné, aby jeden konstruktor existoval, jehož argumenty mohou být splněny pomocí DI. Další parametry, které nejsou pokryty parametrem DI, jsou povoleny, pokud určují výchozí hodnoty.
* Příslušný konstruktor musí být *veřejný*.
* Musí existovat jeden použitelný konstruktor. V případě nejednoznačnosti Vyvolá příkaz DI výjimku.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Základní třídy komponenty nástroje pro správu oboru DI

V aplikacích ASP.NET Core jsou oborové služby obvykle vymezeny na aktuální požadavek. Po dokončení žádosti se v systému DI odstraní všechny obory nebo přechodné služby. V aplikacích Blazor Server je rozsah požadavků po dobu trvání připojení klienta, což může vést k přechodným a oborům služeb, které jsou delší, než se očekávalo.

Chcete-li obor služeb omezit na životnost komponenty, lze použít `OwningComponentBase` základní třídy a. `OwningComponentBase<TService>` Tyto základní třídy zpřístupňují `ScopedServices` vlastnost typu `IServiceProvider` , která řeší služby s rozsahem životnosti komponenty. Chcete-li vytvořit komponentu, která dědí ze základní třídy ve Razor, použijte `@inherits` direktivu.

```cshtml
@page "/users"
@attribute [Authorize]
@inherits OwningComponentBase<Data.ApplicationDbContext>

<h1>Users (@Service.Users.Count())</h1>
<ul>
    @foreach (var user in Service.Users)
    {
        <li>@user.UserName</li>
    }
</ul>
```

> [!NOTE]
> Služby vložené do komponenty pomocí `@inject` `InjectAttribute` nebo nejsou vytvořeny v oboru komponenty a jsou svázány s oborem požadavku.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
