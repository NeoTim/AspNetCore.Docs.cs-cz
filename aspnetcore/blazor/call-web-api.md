---
title: Volání webového rozhraní API z ASP.NET Core Blazor
author: guardrex
description: Naučte se volat webové rozhraní API z aplikace Blazor pomocí pomocníků JSON, včetně vytváření žádostí o sdílení prostředků mezi zdroji (CORS).
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/call-web-api
ms.openlocfilehash: 60ebd01bc07da22cd1dcd0b16297ee54c97867fc
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030386"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Volání webového rozhraní API z ASP.NET Core Blazor

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

Blazor klientské aplikace volají webová rozhraní API pomocí předem nakonfigurované `HttpClient` služby. Požadavky na sestavení, které mohou zahrnovat možnosti [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScriptu, použití pomocníků Blazor <xref:System.Net.Http.HttpRequestMessage>JSON nebo s.

Blazor aplikace na straně serveru volají webová rozhraní API <xref:System.Net.Http.HttpClient> pomocí instancí obvykle vytvořených <xref:System.Net.Http.IHttpClientFactory>pomocí. Další informace naleznete v tématu <xref:fundamentals/http-requests>.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))

Příklady Blazor na straně klienta najdete v části ukázková aplikace v následujících součástech:

* Volání webového rozhraní API (*Pages/CallWebAPI. Razor*)
* Tester požadavků HTTP (*Components/HTTPRequestTester. Razor*)

## <a name="httpclient-and-json-helpers"></a>HttpClient a pomocníky JSON

V Blazor klientských aplikacích je [HttpClient](xref:fundamentals/http-requests) k dispozici jako předkonfigurovaná služba pro poskytování požadavků zpět na zdrojový server. Chcete- `HttpClient` li používat pomocníky JSON, přidejte odkaz na `Microsoft.AspNetCore.Blazor.HttpClient`balíček do. `HttpClient`a pomocníkům JSON se taky používají k volání koncových bodů webového rozhraní API třetích stran. `HttpClient`je implementováno pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejných zásad původu.

Základní adresa klienta je nastavena na adresu původního serveru. `HttpClient` Vložení instance `@inject` pomocí direktivy:

```cshtml
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

* `GetJsonAsync`&ndash; Pošle požadavek HTTP GET a analyzuje tělo odpovědi JSON pro vytvoření objektu.

  V následujícím kódu `_todoItems` se zobrazí součást. Metoda je aktivována při vykreslování komponenty (OnInitializedAsync).[](xref:blazor/components#lifecycle-methods) `GetTodoItems` Kompletní příklad najdete v ukázkové aplikaci.

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/todo");
  }
  ```

* `PostJsonAsync`&ndash; Pošle požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON pro vytvoření objektu.

  V následujícím kódu `_newItemName` je poskytován vázaným prvkem komponenty. Metoda je aktivována `<button>` výběrem prvku. `AddItem` Kompletní příklad najdete v ukázkové aplikaci.

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostJsonAsync("api/todo", addItem);
      }
  }
  ```

* `PutJsonAsync`&ndash; Odešle požadavek HTTP PUT, včetně obsahu kódovaného JSON.

  V následujícím kódu `_editItem` jsou hodnoty pro `Name` a `IsCompleted` poskytovány pomocí vázaných prvků součásti. Položka `Id` je nastavena, když je položka vybrána v jiné části uživatelského rozhraní a `EditItem` je volána. Metoda je aktivována výběrem možnosti Uložit `<button>` element. `SaveItem` Kompletní příklad najdete v ukázkové aplikaci.

  ```cshtml
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
          await Http.PutJsonAsync($"api/todo/{_editItem.Id}, _editItem);
  }
  ```

<xref:System.Net.Http>zahrnuje další metody rozšíření pro posílání požadavků HTTP a příjem odpovědí HTTP. [HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se používá k odeslání požadavku HTTP Delete webovému rozhraní API.

V následujícím kódu element Delete `<button>` `DeleteItem` volá metodu. Vázaný `<input>` element`id` poskytuje položku, která se má odstranit. Kompletní příklad najdete v ukázkové aplikaci.

```cshtml
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/todo/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a>Sdílení prostředků mezi zdroji (CORS)

Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhuje. Toto omezení se nazývá *zásady stejného původu*. Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality. Aby bylo možné podávat požadavky z prohlížeče na koncový bod s jiným zdrojem, musí *koncový bod* umožňovat [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/).

Ukázková aplikace ukazuje použití CORS v součásti volání webového rozhraní API (Pages */CallWebAPI. Razor*).

Pokud chcete jiným webům umožnit, aby vaše aplikace provedla požadavky na sdílení prostředků mezi zdroji (CORS <xref:security/cors>), přečtěte si téma.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient a zprávy HttpRequestMessage s možnostmi žádosti o rozhraní API pro načtení

Při spuštění na WebAssembly v Blazor aplikaci na straně klienta použijte [HttpClient](xref:fundamentals/http-requests) a <xref:System.Net.Http.HttpRequestMessage> k přizpůsobení požadavků. Můžete například zadat identifikátor URI žádosti, metodu HTTP a všechny požadované hlavičky požadavků.

Zadejte možnosti žádosti pro základní [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScriptu pomocí `WebAssemblyHttpMessageHandler.FetchArgs` vlastnosti v žádosti. Jak je znázorněno v následujícím příkladu, `credentials` je vlastnost nastavena na některou z následujících hodnot:

* `FetchCredentialsOption.Include`("include") &ndash; Doporučuje prohlížeči odesílat přihlašovací údaje (jako jsou soubory cookie nebo hlavičky ověřování HTTP) i pro žádosti mezi zdroji. Povoleno jenom v případě, že je zásada CORS nakonfigurovaná tak, aby povolovala přihlašovací údaje.
* `FetchCredentialsOption.Omit`("vynechat") &ndash; Doporučuje prohlížeči, aby nikdy neodesílal přihlašovací údaje (například soubory cookie nebo hlavičky ověřování HTTP).
* `FetchCredentialsOption.SameOrigin`("stejný-původ") &ndash; Doporučuje prohlížeči odesílat přihlašovací údaje (jako jsou soubory cookie nebo hlavičky ověřování HTTP) jenom v případě, že cílová adresa URL je na stejném zdroji jako volající aplikace.

```cshtml
@using System.Net.Http
@using System.Net.Http.Headers
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        Http.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/todo"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");
        
        requestMessage.Properties[WebAssemblyHttpMessageHandler.FetchArgs] = new
        { 
            credentials = FetchCredentialsOption.Include
        };

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

Další informace o možnostech načtení rozhraní API najdete v [tématu MDN web Docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Při odesílání přihlašovacích údajů (souborů cookie autorizace/hlaviček) na žádostech `Authorization` CORS musí být záhlaví povoleno zásadami CORS.

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

Další informace najdete v tématu <xref:security/cors> a součásti testera požadavku HTTP ukázkové aplikace (Components */HTTPRequestTester. Razor*).

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/http-requests>
* [Sdílení prostředků mezi zdroji (CORS) ve W3C](https://www.w3.org/TR/cors/)
