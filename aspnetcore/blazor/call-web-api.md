---
title: Volání webového rozhraní API z ASP.NET Core Blazor WebAssembly
author: guardrex
description: Naučte se volat webové rozhraní API z Blazor aplikace WebAssembly pomocí pomocníků JSON, včetně vytváření žádostí o sdílení prostředků mezi zdroji (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: abc546cc0079a01e3999b2c7c083235d3fff9b06
ms.sourcegitcommit: e94ecfae6a3ef568fa197da791c8bc595917d17a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82122222"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Volání webového rozhraní API z ASP.NET Core Blazor

Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Juan de la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Blazor aplikace WebAssembly](xref:blazor/hosting-models#blazor-webassembly) volají webová rozhraní API pomocí předem nakonfigurované `HttpClient` služby. Požadavky na sestavení, které mohou zahrnovat možnosti [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScriptu, použití pomocníků Blazor <xref:System.Net.Http.HttpRequestMessage>JSON nebo s. `HttpClient` Služba v Blazorch aplikacích WebAssembly se zaměřuje na poskytování požadavků zpátky na původní server. Pokyny v tomto tématu se vztahují pouze k Blazor aplikacím WebAssembly.

[Serverové aplikace Blazor](xref:blazor/hosting-models#blazor-server) volají webová rozhraní API <xref:System.Net.Http.HttpClient> pomocí instancí, které se <xref:System.Net.Http.IHttpClientFactory>obvykle vytvářejí pomocí. Pokyny v tomto tématu se nevztahují na Blazor serverové aplikace. Při vývoji aplikací serveru Blazor postupujte podle pokynů v <xref:fundamentals/http-requests>části.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)) &ndash; vyberte aplikaci *BlazorWebAssemblySample* .

Podívejte se na následující komponenty v ukázkové aplikaci *BlazorWebAssemblySample* :

* Volání webového rozhraní API (*Pages/CallWebAPI. Razor*)
* Tester požadavků HTTP (*Components/HTTPRequestTester. Razor*)

## <a name="packages"></a>Balíčky

V souboru projektu se odkázat na balíček NuGet [System .NET. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) .

## <a name="add-the-httpclient-service"></a>Přidání služby HttpClient

V `Program.Main`nástroji přidejte `HttpClient` službu, pokud ještě neexistuje:

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient a pomocníky JSON

V aplikaci Blazor WebAssembly je [HttpClient](xref:fundamentals/http-requests) k dispozici jako předkonfigurovaná služba pro podání požadavků zpět na zdrojový server.

Aplikace serveru Blazor ve výchozím nastavení neobsahuje `HttpClient` službu. `HttpClient` Poskytněte aplikaci s využitím [infrastruktury HttpClient Factory](xref:fundamentals/http-requests).

`HttpClient`a pomocníkům JSON se taky používají k volání koncových bodů webového rozhraní API třetích stran. `HttpClient`je implementováno pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejných zásad původu.

Základní adresa klienta je nastavena na adresu původního serveru. Vložení `HttpClient` instance pomocí `@inject` direktivy:

```razor
@using System.Net.Http
@inject HttpClient Http
```

V následujících příkladech zpracovává webové rozhraní API TODO operace vytvoření, čtení, aktualizace a odstranění (CRUD). Příklady jsou založeny na `TodoItem` třídě, která ukládá:

* ID (`Id`, `long`) &ndash; jedinečné ID položky
* Název (`Name`, `string`) &ndash; název položky.
* Stav (`IsComplete`, `bool`) &ndash; označuje, zda je položka TODO dokončena.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Pomocné metody JSON odesílají požadavky na identifikátor URI (webové rozhraní API v následujících příkladech) a zpracovávají odpověď:

* `GetFromJsonAsync`&ndash; Pošle požadavek HTTP GET a analyzuje tělo odpovědi JSON pro vytvoření objektu.

  V následujícím kódu `_todoItems` se zobrazí součást. `GetTodoItems` Metoda je aktivována při vykreslování komponenty ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Kompletní příklad najdete v ukázkové aplikaci.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync`&ndash; Pošle požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON pro vytvoření objektu.

  V následujícím kódu `_newItemName` je poskytován vázaným prvkem komponenty. `AddItem` Metoda je aktivována výběrem `<button>` prvku. Kompletní příklad najdete v ukázkové aplikaci.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  Volání `PostAsJsonAsync` vrátí <xref:System.Net.Http.HttpResponseMessage>. K deserializaci obsahu JSON ze zprávy odpovědi použijte metodu `ReadFromJsonAsync<T>` rozšíření:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync`&ndash; ODEŠLE požadavek HTTP PUT, včetně obsahu kódovaného JSON.

  V následujícím kódu jsou `_editItem` hodnoty pro `Name` a `IsCompleted` poskytovány pomocí vázaných prvků součásti. Položka `Id` je nastavena, když je položka vybrána v jiné části uživatelského rozhraní a `EditItem` je volána. `SaveItem` Metoda je aktivována výběrem možnosti Uložit `<button>` element. Kompletní příklad najdete v ukázkové aplikaci.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```
  
  Volání `PutAsJsonAsync` vrátí <xref:System.Net.Http.HttpResponseMessage>. K deserializaci obsahu JSON ze zprávy odpovědi použijte metodu `ReadFromJsonAsync<T>` rozšíření:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>zahrnuje další metody rozšíření pro posílání požadavků HTTP a příjem odpovědí HTTP. [HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se používá k odeslání požadavku HTTP Delete webovému rozhraní API.

V následujícím kódu element Delete `<button>` volá `DeleteItem` metodu. Vázaný `<input>` element poskytuje `id` položku, která se má odstranit. Kompletní příklad najdete v ukázkové aplikaci.

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a>Sdílení prostředků mezi zdroji (CORS)

Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhuje. Toto omezení se nazývá *zásady stejného původu*. Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality. Aby bylo možné podávat požadavky z prohlížeče na koncový bod s jiným zdrojem, musí *koncový bod* umožňovat [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/).

[Ukázková aplikace Blazor WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje použití CORS v součásti webového rozhraní API volání (*Pages/CallWebAPI. Razor*).

Pokud chcete jiným webům umožnit, aby vaše aplikace provedla požadavky na sdílení prostředků mezi zdroji (CORS <xref:security/cors>), přečtěte si téma.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient a zprávy HttpRequestMessage s možnostmi žádosti o rozhraní API pro načtení

Při spuštění na WebAssembly v Blazor aplikaci WebAssembly použijte [HttpClient](xref:fundamentals/http-requests) a <xref:System.Net.Http.HttpRequestMessage> k přizpůsobení požadavků. Můžete například zadat identifikátor URI žádosti, metodu HTTP a všechny požadované hlavičky požadavků.

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                JsonContent.Create(new TodoItem
                { 
                    Name: "A New Todo Item",
                    IsComplete: false
                })
        };
        
        requestMessage.Headers.Authorization = 
           new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

Implementace rozhraní .NET WebAssembly pro `HttpClient` používá [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). Načtení umožňuje nakonfigurovat několik [možností specifických pro požadavky](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Možnosti požadavku HTTP Fetch lze konfigurovat pomocí `HttpRequestMessage` rozšiřujících metod, které jsou uvedeny v následující tabulce.

| `HttpRequestMessage`Metoda rozšíření | Načíst vlastnost žádosti |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [přihlašovací údaje](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [uchovávat](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [Mode](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [způsobilost](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Další možnosti můžete nastavit pomocí obecnější metody `SetBrowserRequestOption` rozšíření.
 
Odpověď HTTP je obvykle ukládána do vyrovnávací paměti Blazor v aplikaci WebAssembly, aby umožnila podporu pro čtení v obsahu odpovědi. Pokud chcete povolit podporu pro streamování odpovědí, `SetBrowserResponseStreamingEnabled` použijte metodu rozšíření v žádosti.

Pokud chcete do žádosti o více zdrojů zahrnout přihlašovací údaje, použijte `SetBrowserRequestCredentials` metodu rozšíření:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Další informace o možnostech načtení rozhraní API naleznete v tématu [MDN web Docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Při odesílání přihlašovacích údajů (souborů cookie autorizace/hlaviček) na žádostech CORS musí být `Authorization` záhlaví povoleno zásadami CORS.

Následující zásady zahrnují konfiguraci pro:

* Původ žádosti (`http://localhost:5000`, `https://localhost:5001`).
* Libovolná metoda (příkaz).
* `Content-Type`a `Authorization` hlavičky. Chcete-li pro vlastní hlavičku (například `x-custom-header`), uveďte záhlaví při volání. <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>
* Přihlašovací údaje nastavené kódem JavaScriptu na straně klienta`credentials` (vlastnost je `include`nastavena na hodnotu).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Další informace najdete v tématu <xref:security/cors> a součásti testera požadavku HTTP ukázkové aplikace (*Components/HTTPRequestTester. Razor*).

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Vyžádání dalších přístupových tokenů](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Připojit tokeny k odchozím žádostem](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Konfigurace koncového bodu HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Sdílení prostředků mezi zdroji (CORS) ve W3C](https://www.w3.org/TR/cors/)
