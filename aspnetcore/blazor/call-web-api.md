---
title: Volání webového rozhraní API z ASP.NET Core Blazor WebAssembly
author: guardrex
description: Naučte se volat webové rozhraní API z Blazor WebAssembly aplikace pomocí pomocníků JSON, včetně vytváření žádostí o sdílení prostředků mezi zdroji (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: aa769554f54d6f480815d65de18e3dba5729d07d
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876734"
---
# <a name="call-a-web-api-from-aspnet-core-no-locblazor"></a>Volání webového rozhraní API z ASP.NET Core Blazor

Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Juan de la Cruz](https://github.com/juandelacruz23)

> [!NOTE]
> Toto téma se týká Blazor WebAssembly . [Blazor Server](xref:blazor/hosting-models#blazor-server) aplikace volají webová rozhraní API pomocí <xref:System.Net.Http.HttpClient> instancí, které se obvykle vytvářejí pomocí <xref:System.Net.Http.IHttpClientFactory> . Pokyny, které se týkají Blazor Server , najdete v tématu <xref:fundamentals/http-requests> .

[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) aplikace volají webová rozhraní API pomocí předem nakonfigurované <xref:System.Net.Http.HttpClient> služby. Požadavky na sestavení, které mohou zahrnovat možnosti [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScriptu, použití Blazor pomocníků JSON nebo s <xref:System.Net.Http.HttpRequestMessage> . <xref:System.Net.Http.HttpClient>Služba v Blazor WebAssembly aplikacích se zaměřuje na poskytování požadavků zpět na server původu. Pokyny v tomto tématu se vztahují jenom na Blazor WebAssembly aplikace.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)): vyberte `BlazorWebAssemblySample` aplikaci.

Podívejte se na následující komponenty v `BlazorWebAssemblySample` ukázkové aplikaci:

* Volání webového rozhraní API ( `Pages/CallWebAPI.razor` )
* Tester požadavků HTTP ( `Components/HTTPRequestTester.razor` )

## <a name="packages"></a>Balíčky

Odkázat na [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) balíček NuGet v souboru projektu.

## <a name="add-the-httpclient-service"></a>Přidání služby HttpClient

V nástroji `Program.Main` přidejte <xref:System.Net.Http.HttpClient> službu, pokud ještě neexistuje:

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient a pomocníky JSON

V Blazor WebAssembly aplikaci [`HttpClient`](xref:fundamentals/http-requests) je k dispozici jako předkonfigurovaná služba pro zpracování požadavků zpátky na zdrojový server.

Blazor ServerAplikace <xref:System.Net.Http.HttpClient> ve výchozím nastavení neobsahuje službu. Poskytněte <xref:System.Net.Http.HttpClient> aplikaci do aplikace pomocí [ `HttpClient` produkční infrastruktury](xref:fundamentals/http-requests).

<xref:System.Net.Http.HttpClient> a pomocníkům JSON se taky používají k volání koncových bodů webového rozhraní API třetích stran. <xref:System.Net.Http.HttpClient> je implementováno pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejných zásad původu.

Základní adresa klienta je nastavena na adresu původního serveru. Vložení <xref:System.Net.Http.HttpClient> instance pomocí [`@inject`](xref:mvc/views/razor#inject) direktivy:

```razor
@using System.Net.Http
@inject HttpClient Http
```

V následujících příkladech zpracovává webové rozhraní API TODO operace vytvoření, čtení, aktualizace a odstranění (CRUD). Příklady jsou založeny na `TodoItem` třídě, která ukládá:

* ID ( `Id` , `long` ): jedinečné ID položky.
* Název ( `Name` , `string` ): název položky.
* Status ( `IsComplete` , `bool` ): indikace, zda je položka TODO dokončena.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Pomocné metody JSON odesílají požadavky na identifikátor URI (webové rozhraní API v následujících příkladech) a zpracovávají odpověď:

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Odešle požadavek HTTP GET a analyzuje tělo odpovědi JSON pro vytvoření objektu.

  V následujícím kódu se `todoItems` zobrazí součást. `GetTodoItems`Metoda je aktivována, když je dokončena vykreslování součásti ( [`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) ). Kompletní příklad najdete v ukázkové aplikaci.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Odesílá požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON pro vytvoření objektu.

  V následujícím kódu `newItemName` je poskytován vázaným prvkem komponenty. `AddItem`Metoda je aktivována výběrem `<button>` prvku. Kompletní příklad najdete v ukázkové aplikaci.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  Volání <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> vrátí <xref:System.Net.Http.HttpResponseMessage> . K deserializaci obsahu JSON ze zprávy odpovědi použijte `ReadFromJsonAsync<T>` metodu rozšíření:
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Odešle požadavek HTTP PUT, včetně obsahu kódovaného JSON.

  V následujícím kódu `editItem` jsou hodnoty pro `Name` a `IsCompleted` poskytovány pomocí vázaných prvků součásti. Položka `Id` je nastavena, když je položka vybrána v jiné části uživatelského rozhraní a `EditItem` je volána. `SaveItem`Metoda je aktivována výběrem možnosti Uložit `<button>` element. Kompletní příklad najdete v ukázkové aplikaci.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  Volání <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> vrátí <xref:System.Net.Http.HttpResponseMessage> . K deserializaci obsahu JSON ze zprávy odpovědi použijte <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> metodu rozšíření:
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http> zahrnuje další metody rozšíření pro posílání požadavků HTTP a příjem odpovědí HTTP. <xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> slouží k odeslání požadavku HTTP DELETE webovému rozhraní API.

V následujícím kódu `<button>` element Delete volá `DeleteItem` metodu. Vázaný `<input>` element poskytuje položku, `id` která se má odstranit. Kompletní příklad najdete v ukázkové aplikaci.

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a>S názvem HttpClient s IHttpClientFactory

<xref:System.Net.Http.IHttpClientFactory> podporují se služby a konfigurace s názvem <xref:System.Net.Http.HttpClient> .

Odkázat na [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) balíček NuGet v souboru projektu.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

`FetchData` součást ( `Pages/FetchData.razor` ):

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a>Typové HttpClient

<xref:System.Net.Http.HttpClient> <xref:System.Net.Http.HttpClient> Pro návratová data z jednoho nebo více koncových bodů webového rozhraní API používá typ jednu nebo více instancí aplikace, výchozí nebo pojmenované.

`WeatherForecastClient.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastClient
{
    private readonly HttpClient client;

    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];
    
        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }
    
        return forecasts;
    }
}
```

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Komponenty vkládají typ <xref:System.Net.Http.HttpClient> pro volání webového rozhraní API.

`FetchData` součást ( `Pages/FetchData.razor` ):

```razor
@inject WeatherForecastClient Client

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Client.GetForecastAsync();
    }
}
```

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>`HttpClient` a `HttpRequestMessage` s možnostmi požadavku rozhraní API pro načtení

Při spuštění na WebAssembly v Blazor WebAssembly aplikaci [`HttpClient`](xref:fundamentals/http-requests) ([dokumentace k rozhraní API](xref:System.Net.Http.HttpClient)) a <xref:System.Net.Http.HttpRequestMessage> dá se použít k přizpůsobení požadavků. Můžete například zadat metodu HTTP a hlavičku požadavku. Následující komponenta vytvoří požadavek na `POST` koncový bod rozhraní API seznamu na serveru a zobrazí tělo odpovědi:

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

Implementace rozhraní .NET WebAssembly pro <xref:System.Net.Http.HttpClient> používá [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). Načtení umožňuje nakonfigurovat několik [možností specifických pro požadavky](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Možnosti požadavku HTTP Fetch lze konfigurovat pomocí <xref:System.Net.Http.HttpRequestMessage> rozšiřujících metod, které jsou uvedeny v následující tabulce.

| Metoda rozšíření | Načíst vlastnost žádosti |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Další možnosti můžete nastavit pomocí obecnější <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> metody rozšíření.
 
Odpověď HTTP je obvykle ukládána do vyrovnávací paměti v Blazor WebAssembly aplikaci, aby umožňovala podporu pro čtení v obsahu odpovědi. Pokud chcete povolit podporu pro streamování odpovědí, použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> metodu rozšíření v žádosti.

Pokud chcete do žádosti o více zdrojů zahrnout přihlašovací údaje, použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> metodu rozšíření:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Další informace o možnostech načtení rozhraní API naleznete v tématu [MDN web Docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

## <a name="handle-errors"></a>Ošetření chyb

Když při interakci s webovým rozhraním API dojde k chybám, mohou být zpracovány vývojářským kódem. Například <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> očekává odpověď JSON z rozhraní API serveru s `Content-Type` `application/json` . Pokud odpověď není ve formátu JSON, ověření obsahu vyvolá <xref:System.NotSupportedException> .

V následujícím příkladu je koncový bod identifikátoru URI pro požadavek na data předpovědi počasí špatně napsaný. Identifikátor URI by měl být, `WeatherForecast` ale zobrazí se ve volání jako `WeatherForcast` (chybí "e").

<xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>Volání očekává vrácení formátu JSON, ale server vrátí kód HTML pro neošetřenou výjimku na serveru s `Content-Type` `text/html` . Neošetřená výjimka probíhá na serveru, protože nebyla nalezena cesta a middleware nemůže pro požadavek obsluhovat stránku nebo zobrazení.

V <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> klientovi <xref:System.NotSupportedException> je vyvolána, když je obsah odpovědi ověřen jako jiný než JSON. Výjimka je zachycena v `catch` bloku, kde vlastní logika by mohla protokolovat chybu nebo prezentovat uživateli popisnou chybovou zprávu:

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> Předchozí příklad je pro demonstrační účely. Serverová aplikace webového rozhraní API se dá nakonfigurovat tak, aby vracela JSON, i když koncový bod neexistuje nebo dojde k neošetřené výjimce na serveru.

Další informace naleznete v tématu <xref:blazor/fundamentals/handle-errors>.

## <a name="cross-origin-resource-sharing-cors"></a>Sdílení prostředků mezi zdroji (CORS)

Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhuje. Toto omezení se nazývá *zásady stejného původu*. Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality. Aby bylo možné podávat požadavky z prohlížeče na koncový bod s jiným zdrojem, musí *koncový bod* umožňovat [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/).

[ Blazor WebAssembly Ukázková aplikace ( Blazor WebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje použití CORS v součásti volání webového rozhraní API ( `Pages/CallWebAPI.razor` ).

Další informace o CORS se zabezpečenými požadavky v Blazor aplikacích najdete v tématu <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors> .

Obecné informace o CORS s ASP.NET Core aplikacemi najdete v tématu <xref:security/cors> .

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/security/webassembly/additional-scenarios>: Obsahuje pokrytí použití <xref:System.Net.Http.HttpClient> pro zabezpečené požadavky webového rozhraní API.
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Konfigurace koncového bodu HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Sdílení prostředků mezi zdroji (CORS) ve W3C](https://www.w3.org/TR/cors/)
