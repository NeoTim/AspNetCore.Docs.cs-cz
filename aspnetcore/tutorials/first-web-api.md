---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 5e5215f246c6c7a805a4c99f485d51a2fb3c712d
ms.sourcegitcommit: cf9ffcce4fe0b69fe795aae9ae06e99fdb18bdfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71306668"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Mike Wasson](https://github.com/mikewasson)

V tomto kurzu se naučíte se základy vytváření webových rozhraní API pomocí ASP.NET Core.

::: moniker range=">= aspnetcore-3.0"

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte projekt webového rozhraní API.
> * Přidejte třídu modelu a kontext databáze.
> * Generování uživatelského rozhraní pro kontroler pomocí metod CRUD
> * Nakonfigurujte směrování, cesty URL a návratové hodnoty.
> * Volání webového rozhraní API pomocí nástroje Postman.

Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.

## <a name="overview"></a>Přehled

Tento kurz vytvoří následující rozhraní API:

|rozhraní API | Popis | Text požadavku | Text odpovědi |
|--- | ---- | ---- | ---- |
|ZÍSKAT/api/TodoItems | Získat všechny položky seznamu úkolů | Žádné | Pole položek úkolů|
|ZÍSKAT/api/TodoItems/{id} | Získat položky podle ID | Žádná | Položky seznamu úkolů|
|PŘÍSPĚVEK/api/TodoItems | Přidat novou položku | Položky seznamu úkolů | Položky seznamu úkolů |
|Vložit/api/TodoItems/{id} | Aktualizovat existující položku &nbsp; | Položky seznamu úkolů | Žádné |
|Odstranit/api/TodoItems/{id} &nbsp;&nbsp; | Odstranění položky &nbsp; &nbsp; | Žádné | Žádná|

Následující diagram znázorňuje návrh aplikace.

![Klient je reprezentován polem vlevo. Odešle požadavek a přijme odpověď z aplikace, pole nakreslené na pravé straně. V dialogovém okně aplikace tři pole představují kontroleru, model a vrstva přístupu k datům. Žádost vstupu do kontroleru aplikace a operace čtení a zápisu, ke kterým dochází mezi kontrolerem a vrstva přístupu k datům. Model je serializován a vrátí klientovi v odpovědi.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a>Vytvoření webového projektu

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **soubor** vyberte možnost **Nový** > **projekt**.
* Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.
* Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.
* V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** . Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.

![VS – dialogové okno nového projektu](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.
* Spusťte následující příkazy:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.

  Předchozí příkazy:

  * Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.
  * Přidá balíčky NuGet, které jsou nutné v další části.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* Vyberte rozhraní > **API** > aplikace> .NET Core další.

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte **cílovou architekturu** * *.NET Core 3,0*.

* Zadejte *TodoApi* pro **název projektu** a pak vyberte **vytvořit**.

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>Testovat rozhraní API

Šablona projektu vytvoří `WeatherForecast` rozhraní API. Volání `Get` metoda v prohlížeči a testování aplikace.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Stisknutím kláves Ctrl + F5 spusťte aplikaci. Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast`, kde `<port>` je číslo portu náhodně vybrané.

Pokud se zobrazí dialogové okno s dotazem, pokud by měla důvěřovat certifikátu služby IIS Express, vyberte **Ano**. V **upozornění zabezpečení** dialogové okno, které se zobrazí další, vyberte **Ano**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kláves Ctrl + F5 spusťte aplikaci. V prohlížeči přejděte na následující adrese URL: [ https://localhost:5001/WeatherForecast ](https://localhost:5001/WeatherForecast).

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vyberte **Spustit** > **ladění** a spusťte aplikaci. Spuštění prohlížeče Visual Studio pro Mac a přejde na `https://localhost:<port>`, kde `<port>` je číslo portu náhodně vybrané. Vrátí chybu HTTP 404 (Nenalezeno). Připojit `/WeatherForecast` na adresu URL (změňte adresu URL na `https://localhost:<port>/WeatherForecast`).

---

Vrátí se JSON podobný následujícímu:

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a>Přidejte třídu modelu

A *modelu* je sada tříd, které představují data, která aplikace spravuje. Model pro tuto aplikaci je jedinou `TodoItem` třídy.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt. Vyberte **přidat** > **novou složku**. Název složky *modely*.

* Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**. Název třídy *TodoItem* a vyberte **přidat**.

* Nahraďte kód šablony následujícím kódem:

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidat složku s názvem *modely*.

* Přidat `TodoItem` třídu *modely* složka s následujícím kódem:

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na projekt. Vyberte **přidat** > **novou složku**. Název složky *modely*.

  ![Nová složka](first-web-api-mac/_static/folder.png)

* Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.

* Název třídy *TodoItem*a potom klikněte na tlačítko **nový**.

* Nahraďte kód šablony následujícím kódem:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.

Třídy modelu můžete kamkoliv v projektu, ale *modely* složky používají konvence.

## <a name="add-a-database-context"></a>Přidat kontext databáze

*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model. Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a>Přidat Microsoft. EntityFrameworkCore. SqlServer

* V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.
* Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .
* V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .
* Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.
* Pomocí předchozích pokynů přidejte `Microsoft.EntityFrameworkCore.InMemory` balíček NuGet.

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>Přidání kontextu databáze TodoContext

* Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**. Název třídy *TodoContext* a klikněte na tlačítko **přidat**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* Přidat `TodoContext` třídu *modely* složky.

---

* Zadejte následující kód:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Zaregistrujte kontext databáze

V ASP.NET Core, služeb, jako je kontext databáze, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru. Kontejner poskytuje služby řadiče.

Aktualizace *Startup.cs* s následující zvýrazněný kód:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Předchozí kód:

* Odebere nevyužité `using` deklarace.
* Přidá do kontejneru DI kontext databáze.
* Určuje, zda kontext databáze bude používat databázi v paměti.

## <a name="scaffold-a-controller"></a>Generování uživatelského rozhraní kontroleru

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem myši *řadiče* složky.
* Vyberte **Přidat** > **novou vygenerované položky**.
* Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.
* V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :

  * V **třídě modelu**vyberte **TodoItem (TodoAPI. Models)** .
  * Ve **třídě Context data**vyberte **TodoContext (TodoAPI. Models)** .
  * Vyberte **Přidat**

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

Spusťte následující příkazy:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

Předchozí příkazy:

* Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.
* Nainstaluje modul generování uživatelského rozhraní (`dotnet-aspnet-codegenerator`).
* Generování uživatelského rozhraní `TodoItemsController`.

---

Generovaný kód:

* Definuje třídu kontroleru rozhraní API bez metody.
* Upraví třídu atributem [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) . Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API. Informace o konkrétním chování, které atribut povoluje, naleznete v <xref:web-api/index>tématu.
* Vložit kontext databáze používá DI (`TodoContext`) do kontroleru. Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.

## <a name="examine-the-posttodoitem-create-method"></a>Projděte si metodu PostTodoItem Create.

Nahraďte příkaz return v `PostTodoItem` operátoru k použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Předchozí kód je metoda HTTP POST, je určeno [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atribut. Metoda získá hodnotu položky úkolů z textu požadavku HTTP.

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> Metody:

* Pokud je úspěšná, vrátí stavový kód HTTP 201. HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.
* Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi. Záhlaví Určuje identifikátor URI nově vytvořené položky. [](https://developer.mozilla.org/docs/Glossary/URI) `Location` Další informace najdete v tématu [10.2.2 201 – vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odkazuje na `GetTodoItem` akci `Location` vytvoření identifikátoru URI hlavičky. Klíčové slovo se používá k zamezení hardwarového kódování názvu `CreatedAtAction` akce ve volání. C# `nameof`

### <a name="install-postman"></a>Nainstalovat post

Tento kurz používá Postman k otestování webové rozhraní API.

* Nainstalujte [Postman](https://www.getpostman.com/downloads/)
* Spusťte webovou aplikaci.
* Spusťte Postman.
* Zakázat **ověření certifikátu SSL**
* Z **soubor > Nastavení** (**Obecné* kartu), zakažte **ověření certifikátu SSL**.
    > [!WARNING]
    > Znovu povolte ověření certifikátu SSL po otestování kontroleru.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>PostTodoItem testu s použitím post

* Vytvořte novou žádost.
* Nastavte metodu HTTP na `POST`.
* Vyberte **tělo** kartu.
* Vyberte **nezpracovaná** přepínač.
* Nastavte typ **JSON (application/json)** .
* V textu požadavku zadejte JSON pro úkol:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Vyberte **Poslat**.

  ![Postman se vytvořit žádost](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Testování hlavičku location identifikátoru URI

* Vyberte **záhlaví** kartu **odpovědi** podokně.
* Kopírovat **umístění** hodnota hlavičky:

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/3/create.png)

* Nastavte jako metodu GET.
* Vložte identifikátor URI (například `https://localhost:5001/api/TodoItems/1`)
* Vyberte **Poslat**.

## <a name="examine-the-get-methods"></a>Projděte si metody GET.

Tyto metody implementovat dva koncové body GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání. Příklad:

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

Odpověď podobná následující je vytvořena voláním metody `GetTodoItems`:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Test Get s použitím metody post

* Vytvořte novou žádost.
* Nastavte jako metodu HTTP **získat**.
* Nastavení adresy URL požadavku `https://localhost:<port>/api/TodoItems`. Například, `https://localhost:5001/api/TodoItems`.
* Nastavte **dvě podokna zobrazení** v nástroji Postman.
* Vyberte **Poslat**.

Tato aplikace používá databázi v paměti. Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data. Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.

## <a name="routing-and-url-paths"></a>Směrování a adresa URL cesty

[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET. Cesta adresy URL pro každou metodu se vypočte takto:

* Začněte s řetězec šablony v kontroleru `Route` atribut:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru". V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems". ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.
* `[HttpGet("products")]`Pokud má `[HttpGet]` atribut směrovací šablonu (například), přidejte ji k cestě. Tato ukázka nepoužívá šablony. Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

V následujícím `GetTodoItem` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů. Když `GetTodoItem` je vyvolána, hodnota `"{id}"` v adrese URL je k dispozici v metodě jeho`id` parametru.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Vrácené hodnoty

Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON. Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky. Nezpracované výjimky jsou přeloženy do chyby 5xx.

`ActionResult` typy vrácených hodnot může představovat stavové kódy HTTP široký rozsah. Například `GetTodoItem` může vrátit hodnoty dvou různých stavu:

* Pokud žádná položka odpovídá požadovaným ID, vrátí metoda 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) kód chyby.
* V opačném případě vrátí metoda 200 s těla odpovědi JSON. Vrací `item` výsledkem odpověď HTTP 200.

## <a name="the-puttodoitem-method"></a>Metoda PutTodoItem

Projděte `PutTodoItem` si metodu:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` je podobný `PostTodoItem`, s výjimkou používá HTTP PUT. Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny. Chcete-li podporovat částečné aktualizace, použijte [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Pokud se zobrazí chyba při volání `PutTodoItem` `GET` , zajistěte, aby byla položka v databázi.

### <a name="test-the-puttodoitem-method"></a>Test PutTodoItem – metoda

Tato ukázka používá databázi v paměti, která musí být při každém spuštění aplikace inicializovaná. Před provedením volání PUT musí existovat položka v databázi. Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.

Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Následující obrázek ukazuje Postman aktualizace:

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Metoda DeleteTodoItem

Projděte `DeleteTodoItem` si metodu:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

`DeleteTodoItem` Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Test DeleteTodoItem – metoda

Pomocí nástroje Postman odstraňte položku úkolu:

* Nastavte jako metodu `DELETE`.
* Nastavte identifikátor URI objektu odstranit, například `https://localhost:5001/api/TodoItems/1`
* Vyberte **odeslat**

## <a name="call-the-web-api-with-javascript"></a>Volání webového rozhraní API pomocí JavaScriptu

Viz [kurz: Zavolejte ASP.NET Core Web API pomocí JavaScriptu](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte projekt webového rozhraní API.
> * Přidejte třídu modelu a kontext databáze.
> * Přidání kontroleru.
> * Přidejte metody CRUD.
> * Konfigurace směrování a cesty adresy URL.
> * Zadejte návratové hodnoty.
> * Volání webového rozhraní API pomocí nástroje Postman.
> * Zavolejte webové rozhraní API pomocí JavaScriptu.

Na konci máte webové rozhraní API, která může spravovat "úkolů" položky uložené v relační databázi.

## <a name="overview"></a>Přehled

Tento kurz vytvoří následující rozhraní API:

|rozhraní API | Popis | Text požadavku | Text odpovědi |
|--- | ---- | ---- | ---- |
|ZÍSKAT/api/TodoItems | Získat všechny položky seznamu úkolů | Žádné | Pole položek úkolů|
|ZÍSKAT/api/TodoItems/{id} | Získat položky podle ID | Žádná | Položky seznamu úkolů|
|PŘÍSPĚVEK/api/TodoItems | Přidat novou položku | Položky seznamu úkolů | Položky seznamu úkolů |
|Vložit/api/TodoItems/{id} | Aktualizovat existující položku &nbsp; | Položky seznamu úkolů | Žádné |
|Odstranit/api/TodoItems/{id} &nbsp;&nbsp; | Odstranění položky &nbsp; &nbsp; | Žádné | Žádná|

Následující diagram znázorňuje návrh aplikace.

![Klient je reprezentován polem vlevo. Odešle požadavek a přijme odpověď z aplikace, pole nakreslené na pravé straně. V dialogovém okně aplikace tři pole představují kontroleru, model a vrstva přístupu k datům. Žádost vstupu do kontroleru aplikace a operace čtení a zápisu, ke kterým dochází mezi kontrolerem a vrstva přístupu k datům. Model je serializován a vrátí klientovi v odpovědi.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a>Vytvoření webového projektu

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **soubor** vyberte možnost **Nový** > **projekt**.
* Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.
* Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.
* V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 2,2** . Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**. **Nevybírejte možnost** **Povolit podporu Docker**.

![VS – dialogové okno nového projektu](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.
* Spusťte následující příkazy:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci Visual Studio Code v nové složce projektu.

* Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* Vyberte rozhraní > **API** > aplikace> .NET Core další.

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* V **nakonfigurovat nové technologie ASP.NET Core webové rozhraní API** dialogového okna, přijměte výchozí nastavení **Cílová architektura** z * *.NET Core 2.2*.

* Zadejte *TodoApi* pro **název projektu** a pak vyberte **vytvořit**.

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>Testovat rozhraní API

Šablona projektu vytvoří `values` rozhraní API. Volání `Get` metoda v prohlížeči a testování aplikace.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Stisknutím kláves Ctrl + F5 spusťte aplikaci. Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values`, kde `<port>` je číslo portu náhodně vybrané.

Pokud se zobrazí dialogové okno s dotazem, pokud by měla důvěřovat certifikátu služby IIS Express, vyberte **Ano**. V **upozornění zabezpečení** dialogové okno, které se zobrazí další, vyberte **Ano**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kláves Ctrl + F5 spusťte aplikaci. V prohlížeči přejděte na následující adrese URL: [ https://localhost:5001/api/values ](https://localhost:5001/api/values).

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vyberte **Spustit** > **ladění** a spusťte aplikaci. Spuštění prohlížeče Visual Studio pro Mac a přejde na `https://localhost:<port>`, kde `<port>` je číslo portu náhodně vybrané. Vrátí chybu HTTP 404 (Nenalezeno). Připojit `/api/values` na adresu URL (změňte adresu URL na `https://localhost:<port>/api/values`).

---

Vrátí následující JSON:

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>Přidejte třídu modelu

A *modelu* je sada tříd, které představují data, která aplikace spravuje. Model pro tuto aplikaci je jedinou `TodoItem` třídy.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt. Vyberte **přidat** > **novou složku**. Název složky *modely*.

* Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**. Název třídy *TodoItem* a vyberte **přidat**.

* Nahraďte kód šablony následujícím kódem:

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidat složku s názvem *modely*.

* Přidat `TodoItem` třídu *modely* složka s následujícím kódem:

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na projekt. Vyberte **přidat** > **novou složku**. Název složky *modely*.

  ![Nová složka](first-web-api-mac/_static/folder.png)

* Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.

* Název třídy *TodoItem*a potom klikněte na tlačítko **nový**.

* Nahraďte kód šablony následujícím kódem:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.

Třídy modelu můžete kamkoliv v projektu, ale *modely* složky používají konvence.

## <a name="add-a-database-context"></a>Přidat kontext databáze

*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model. Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**. Název třídy *TodoContext* a klikněte na tlačítko **přidat**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* Přidat `TodoContext` třídu *modely* složky.

---

* Nahraďte kód šablony následujícím kódem:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Zaregistrujte kontext databáze

V ASP.NET Core, služeb, jako je kontext databáze, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru. Kontejner poskytuje služby řadiče.

Aktualizace *Startup.cs* s následující zvýrazněný kód:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

Předchozí kód:

* Odebere nevyužité `using` deklarace.
* Přidá do kontejneru DI kontext databáze.
* Určuje, zda kontext databáze bude používat databázi v paměti.

## <a name="add-a-controller"></a>Přidání kontroleru

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem myši *řadiče* složky.
* Vyberte možnost **Přidat** > **novou položku**.
* V **přidat novou položku** dialogového okna, vyberte **třída Kontroleru rozhraní API** šablony.
* Název třídy *TodoController*a vyberte **přidat**.

  ![Přidání nové položky dialogové okno s kontrolerem v vyhledávací pole a webové rozhraní api kontroleru vybrané](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* V *řadiče* složku, vytvořte třídu s názvem `TodoController`.

---

* Nahraďte kód šablony následujícím kódem:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Předchozí kód:

* Definuje třídu kontroleru rozhraní API bez metody.
* Upraví třídu atributem [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) . Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API. Informace o konkrétním chování, které atribut povoluje, naleznete v <xref:web-api/index>tématu.
* Vložit kontext databáze používá DI (`TodoContext`) do kontroleru. Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.
* Přidá položku s názvem `Item1` k databázi, pokud databáze je prázdný. Tento kód je v konstruktoru, proto se spustí pokaždé, když se nový požadavek HTTP. Pokud odstraníte všechny položky, vytvoří konstruktor `Item1` znovu při příštím volání metody rozhraní API. Proto může účet vypadat třeba odstranění akce nebyla úspěšná, když se ve skutečnosti fungovalo.

## <a name="add-get-methods"></a>Přidejte metody Get

Chcete-li poskytují rozhraní API, který načte položky, přidejte následující metody, které `TodoController` třídy:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Tyto metody implementovat dva koncové body GET:

* `GET /api/todo`
* `GET /api/todo/{id}`

Pokud je pořád spuštěná, zastavte aplikaci. Pak ji znovu spusťte, aby obsahovala nejnovější změny.

Testování aplikace pomocí volání dva koncové body v prohlížeči. Příklad:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

Následující odpověď HTTP je vytvořen voláním `GetTodoItems`:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>Směrování a adresa URL cesty

[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET. Cesta adresy URL pro každou metodu se vypočte takto:

* Začněte s řetězec šablony v kontroleru `Route` atribut:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru". V tomto příkladu je název třídy kontroleru **Todo**Kontroleru, názvu kontroleru je "todo". ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.
* `[HttpGet("products")]`Pokud má `[HttpGet]` atribut směrovací šablonu (například), přidejte ji k cestě. Tato ukázka nepoužívá šablony. Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

V následujícím `GetTodoItem` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů. Když `GetTodoItem` je vyvolána, hodnota `"{id}"` v adrese URL je k dispozici v metodě jeho`id` parametru.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Vrácené hodnoty

Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON. Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky. Nezpracované výjimky jsou přeloženy do chyby 5xx.

`ActionResult` typy vrácených hodnot může představovat stavové kódy HTTP široký rozsah. Například `GetTodoItem` může vrátit hodnoty dvou různých stavu:

* Pokud žádná položka odpovídá požadovaným ID, vrátí metoda 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) kód chyby.
* V opačném případě vrátí metoda 200 s těla odpovědi JSON. Vrací `item` výsledkem odpověď HTTP 200.

## <a name="test-the-gettodoitems-method"></a>Test GetTodoItems – metoda

Tento kurz používá Postman k otestování webové rozhraní API.

* Nainstalujte [Postman](https://www.getpostman.com/downloads/)
* Spusťte webovou aplikaci.
* Spusťte Postman.
* Zakázat **ověření certifikátu SSL**

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Nastavení** **souboru** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* V možnosti**předvolba** **post** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**. Případně vyberte klíče a vyberte **Nastavení**a pak zakažte ověřování certifikátu SSL.

---
  
> [!WARNING]
> Znovu povolte ověření certifikátu SSL po otestování kontroleru.

* Vytvořte novou žádost.
  * Nastavte jako metodu HTTP **získat**.
  * Nastavení adresy URL požadavku `https://localhost:<port>/api/todo`. Například, `https://localhost:5001/api/todo`.
* Nastavte **dvě podokna zobrazení** v nástroji Postman.
* Vyberte **Poslat**.

![Postman se požadavek Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>Přidání metody vytvoření

Přidejte následující `PostTodoItem` metodu do *Controllers/TodoController. cs*: 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Předchozí kód je metoda HTTP POST, je určeno [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atribut. Metoda získá hodnotu položky úkolů z textu požadavku HTTP.

`CreatedAtAction` Metody:

* Vrátí stavový kód HTTP 201, pokud bylo úspěšné. HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.
* `Location` Přidá hlavičku k odpovědi. `Location` Záhlaví Určuje identifikátor URI nově vytvořené položky. Další informace najdete v tématu [10.2.2 201 – vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odkazuje na `GetTodoItem` akci `Location` vytvoření identifikátoru URI hlavičky. Klíčové slovo se používá k zamezení hardwarového kódování názvu `CreatedAtAction` akce ve volání. C# `nameof`

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>Test PostTodoItem – metoda

* Sestavte projekt.
* V nástroji Postman, nastavte jako metodu HTTP `POST`.
* Vyberte **tělo** kartu.
* Vyberte **nezpracovaná** přepínač.
* Nastavte typ **JSON (application/json)** .
* V textu požadavku zadejte JSON pro úkol:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Vyberte **Poslat**.

  ![Postman se vytvořit žádost](first-web-api/_static/create.png)

  Pokud se zobrazí chyba metoda 405 není povolená, je pravděpodobné, že po přidání `PostTodoItem` metody nebude projekt zkompilován.

### <a name="test-the-location-header-uri"></a>Testování hlavičku location identifikátoru URI

* Vyberte **záhlaví** kartu **odpovědi** podokně.
* Kopírovat **umístění** hodnota hlavičky:

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/pmc2.png)

* Nastavte jako metodu GET.
* Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2`)
* Vyberte **Poslat**.

## <a name="add-a-puttodoitem-method"></a>Přidejte metodu PutTodoItem

Přidejte následující `PutTodoItem` metody:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem` je podobný `PostTodoItem`, s výjimkou používá HTTP PUT. Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny. Chcete-li podporovat částečné aktualizace, použijte [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Pokud se zobrazí chyba při volání `PutTodoItem` `GET` , zajistěte, aby byla položka v databázi.

### <a name="test-the-puttodoitem-method"></a>Test PutTodoItem – metoda

Tato ukázka používá databázi v paměti, která musí být při každém spuštění aplikace inicializovaná. Před provedením volání PUT musí existovat položka v databázi. Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.

Aktualizovat položku seznamu úkolů, která má id = 1 a nastavte její název na "informačního kanálu ryb":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Následující obrázek ukazuje Postman aktualizace:

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>Přidejte metodu DeleteTodoItem

Přidejte následující `DeleteTodoItem` metody:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

`DeleteTodoItem` Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Test DeleteTodoItem – metoda

Pomocí nástroje Postman odstraňte položku úkolu:

* Nastavte jako metodu `DELETE`.
* Nastavte identifikátor URI objektu odstranit, například `https://localhost:5001/api/todo/1`
* Vyberte **odeslat**

Ukázková aplikace umožňuje odstranit všechny položky. Když je však poslední položka odstraněna, vytvoří se nový konstruktor třídy modelu při příštím volání rozhraní API.

## <a name="call-the-web-api-with-javascript"></a>Volání webového rozhraní API pomocí JavaScriptu

V této části se přidá stránka HTML, která pomocí JavaScriptu volá webové rozhraní API. Načtené rozhraní API iniciuje požadavek. JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.

Nakonfigurujte aplikaci tak, aby [sloužila pro statické soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , a [Povolte výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Vytvoření *wwwroot* složku v adresáři projektu.

Přidat soubor HTML s názvem *index.html* k *wwwroot* adresáře. Nahraďte jeho obsah následujícím kódem:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Přidejte soubor JavaScriptu s názvem *site.js* k *wwwroot* adresáře. Nahraďte jeho obsah následujícím kódem:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Ke změně nastavení spouštěcí projekt ASP.NET Core může být nutné testovací stránka HTML místně:

* Otevřít *Properties\launchSettings.json*.
* Odeberte `launchUrl` vlastnost, aby se aplikace na *index.html*&mdash;výchozí soubor projektu.

Tato ukázka volá všechny metody CRUD webového rozhraní API. Následuje vysvětlení volání rozhraní API.

### <a name="get-a-list-of-to-do-items"></a>Získání seznamu úkolů

Načtení odešle požadavek HTTP GET do webového rozhraní API, které vrátí JSON představující pole položek úkolů. `success` Funkce zpětného volání je vyvolána, pokud neproběhne. Při zpětném volání modelu DOM se aktualizuje informacemi úkolů.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Přidat položku seznamu úkolů

Metoda Fetch odešle požadavek HTTP POST s položkou k žádosti v textu požadavku. `accepts` a `contentType` možnosti jsou nastaveny na `application/json` zadat typ média, který se přijalo a odeslalo. Položky seznamu úkolů je převést na JSON pomocí [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Rozhraní API po návratu úspěšné stavový kód, `getData` funkce se vyvolala aktualizovat tabulku HTML.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Aktualizace položky seznamu úkolů

Aktualizace položky úkolu je podobné jako přidání jednoho. `url` Změny přidat jedinečný identifikátor položky a `type` je `PUT`.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Odstranit úkol

Odstranění položky úkolu se provádí tak, že nastavíte `type` při volání AJAX `DELETE` a zadáte jedinečný identifikátor položky v adrese URL.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a>Přidání podpory ověřování do webového rozhraní API

Viz kurz k [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) .

## <a name="additional-resources"></a>Další zdroje

[Zobrazení nebo stažení ukázkového kódu pro účely tohoto kurzu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). Zobrazit [stažení](xref:index#how-to-download-a-sample).

Další informace naleznete v následujících materiálech:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Verze YouTube tohoto kurzu](https://www.youtube.com/watch?v=TTkhEyGBfAk)
