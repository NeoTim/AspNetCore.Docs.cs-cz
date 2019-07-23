---
title: Volání webového rozhraní API z ASP.NET Core Blazor
author: guardrex
description: Zjistěte, jak volat webové rozhraní API z aplikace Blazor pomocí pomocné rutiny JSON, včetně vytváření prostředků mezi zdroji (CORS) žádosti o sdílení.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2019
uid: blazor/call-web-api
ms.openlocfilehash: 1a13f9f1f9e660b39a1df584e49198c4bbb61533
ms.sourcegitcommit: 47cc13ab90913af9a2887cef0896bb4e9aba4dd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2019
ms.locfileid: "67399179"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Volání webového rozhraní API z ASP.NET Core Blazor

Podle [Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)

Aplikace na straně klienta Blazor volání webového rozhraní API pomocí předkonfigurovaného `HttpClient` služby. Vytváření požadavků, které mohou zahrnovat JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) možnosti použití pomocné rutiny Blazor JSON nebo s <xref:System.Net.Http.HttpRequestMessage>.

Blazor serverové aplikace volání webového rozhraní API pomocí <xref:System.Net.Http.HttpClient> instance většinou vytvořené využitím <xref:System.Net.Http.IHttpClientFactory>. Další informace naleznete v tématu <xref:fundamentals/http-requests>.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))

Příklady Blazor na straně klienta najdete v následující součásti v ukázkové aplikaci:

* Volání webového rozhraní API (*Pages/CallWebAPI.razor*)
* Testování požadavku HTTP (*Components/HTTPRequestTester.razor*)

## <a name="httpclient-and-json-helpers"></a>Pomocné rutiny HttpClient a JSON

V aplikacích na straně klienta Blazor [HttpClient](xref:fundamentals/http-requests) je k dispozici jako předem nakonfigurované služby pro zasílání požadavků zpět na původním serveru. `HttpClient` a pomocné rutiny JSON se také používají k volání koncových bodů třetích stran webové rozhraní API. `HttpClient` je implementováno pomocí prohlížeče [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) a podléhá jeho omezení, včetně vynucení zásada stejného zdroje.

Základní adresa klienta je nastavena na původním serveru adresu. Vložení `HttpClient` instance pomocí `@inject` – direktiva:

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

V následujících příkladech Todo webové rozhraní API procesu vytvoření, čtení, aktualizace a odstranění (CRUD) operací. Příklady jsou založeny na `TodoItem` třídu, která ukládá:

* ID (`Id`, `long`) &ndash; jedinečné ID položky.
* Název (`Name`, `string`) &ndash; název položky.
* Stav (`IsComplete`, `bool`) &ndash; údaj, zda je dokončené položky seznamu úkolů.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Metody helper JSON odesílání požadavků na identifikátor URI (webového rozhraní API v následujících příkladech) a zpracování odpovědi:

* `GetJsonAsync` &ndash; Pošle požadavek HTTP GET a analyzuje těla odpovědi JSON pro vytvoření objektu.

  V následujícím kódu `_todoItems` zobrazují komponentou. `GetTodoItems` Metoda se aktivuje, když je součást dokončena vykreslování ([OnInitAsync](xref:blazor/components#lifecycle-methods)). Zobrazte ukázkovou aplikaci pro kompletní příklad.

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/todo");
  }
  ```

* `PostJsonAsync` &ndash; Odešle požadavek HTTP POST, včetně obsah kódovaný ve formátu JSON a Parsuje těla odpovědi JSON pro vytvoření objektu.

  V následujícím kódu `_newItemName` poskytuje vázaný prvek součásti. `AddItem` Metoda se aktivuje při výběru `<button>` elementu. Zobrazte ukázkovou aplikaci pro kompletní příklad.

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

* `PutJsonAsync` &ndash; Odešle požadavek HTTP PUT, včetně obsah kódovaný ve formátu JSON.

  V následujícím kódu `_editItem` hodnoty `Name` a `IsCompleted` jsou k dispozici v vázaných prvků komponenty. Položky `Id` nastavena při výběru položky v jiné části uživatelského rozhraní a `EditItem` je volána. `SaveItem` Metoda se aktivuje tak, že vyberete Uložit `<button>` elementu. Zobrazte ukázkovou aplikaci pro kompletní příklad.

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

<xref:System.Net.Http> zahrnuje další rozšiřující metody pro odesílání požadavků HTTP a příjmu odpovědi protokolu HTTP. [HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se používá k odeslání do webového rozhraní API požadavek HTTP DELETE.

V následujícím kódu, odstranění `<button>` element volání `DeleteItem` metody. Je mez `<input>` element poskytuje `id` položky k odstranění. Zobrazte ukázkovou aplikaci pro kompletní příklad.

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

## <a name="cross-origin-resource-sharing-cors"></a>Prostředků mezi zdroji (CORS) pro sdílení obsahu

Zabezpečení prohlížečů brání webovou stránku zasílání požadavků na jiné doméně než ten, který obsluhuje webovou stránku. Toto omezení je volána *zásada stejného zdroje*. Zásada stejného zdroje brání škodlivým webům ve čtení citlivých dat z jiné lokality. Aby požadavky z prohlížeče pro koncový bod s jinou původu *koncový bod* musíte povolit [prostředků mezi zdroji (CORS) pro sdílení obsahu](https://www.w3.org/TR/cors/).

Ukázková aplikace předvádí použití CORS v komponentě volání webového rozhraní API (*Pages/CallWebAPI.razor*).

Povolení prostředků různého původu (CORS) žádosti do své aplikace pro sdílení obsahu na jiných serverech, najdete v článku <xref:security/cors>.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>Možnosti žádosti HttpClient a objekt HttpRequestMessage načíst rozhraní API

Při spuštění na WebAssembly v aplikaci na straně klienta Blazor, použijte [HttpClient](xref:fundamentals/http-requests) a <xref:System.Net.Http.HttpRequestMessage> přizpůsobení požadavky. Můžete například zadat identifikátoru URI požadavku, metodu HTTP a hlavičky všechny požadované žádosti.

Zadat možnosti žádosti pro základní jazyk JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pomocí `WebAssemblyHttpMessageHandler.FetchArgs` vlastnosti požadavku. Jak je znázorněno v následujícím příkladu `credentials` vlastnost je nastavena na jednu z následujících hodnot:

* `FetchCredentialsOption.Include` ("zahrnutí") &ndash; Výzva prohlížeče k odeslání přihlašovací údaje (například soubory cookie nebo ověřování hlaviček protokolu HTTP) i pro požadavky cross-origin. Povoleny, pouze je-li povolit přihlašovací údaje jsou nakonfigurované zásady CORS.
* `FetchCredentialsOption.Omit` ("vynechat") &ndash; Vás informuje o tom prohlížeč nikdy odeslat přihlašovací údaje (například soubory cookie a hlavičky ověření HTTP).
* `FetchCredentialsOption.SameOrigin` ("stejného zdroje") &ndash; Výzva prohlížeče k odeslání přihlašovací údaje (například soubory cookie a hlavičky ověření HTTP), pouze pokud je cílová adresa URL na stejného původu jako volající aplikace.

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

Další informace o možnostech rozhraní Fetch API najdete v tématu [MDN web dokumentace: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Při odesílání přihlašovací údaje (soubory cookie a hlavičky ověření) u požadavků CORS `Authorization` záhlaví musí být povoleno zásadou CORS.

Zahrnuje tyto zásady konfigurace pro:

* Požádat o původu (`http://localhost:5000`, `https://localhost:5001`).
* Libovolné metody (akce).
* `Content-Type` a `Authorization` záhlaví. Povolit vlastní hlavičky (například `x-custom-header`), vypsat hlavičku při volání metody <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.
* Přihlašovací údaje sadu podle kódu jazyka JavaScript na straně klienta (`credentials` nastavenou na `include`).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Další informace najdete v tématu <xref:security/cors> a součást testování požadavku HTTP ukázkovou aplikaci (*Components/HTTPRequestTester.razor*).

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/http-requests>
* [Různé sdílení prostředků zdroji (CORS na W3C)](https://www.w3.org/TR/cors/)
