::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../../tutorials/first-web-api/samples/2.0/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Předcházející kód definuje třídu kontroleru rozhraní API bez metody. V následujících částech jsou přidány metody k implementaci rozhraní API.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../../tutorials/first-web-api/samples/2.1/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Předchozí kód:

* Definuje třídu kontroleru rozhraní API bez metody.
* Vytvoří nový úkol položky, když `TodoItems` je prázdný. Není možné odstranit všechny položky Todo protože konstruktoru vytvoří novou jeden if `TodoItems` je prázdný.

V následujících částech jsou přidány metody k implementaci rozhraní API. Třída je opatřen poznámkou `[ApiController]` atribut pro povolení některé vhodné funkce. Informace o funkcích, které jsou povolené v atributu naleznete v tématu [Poznámka atributem objektu ApiController](xref:web-api/index#annotation-with-apicontroller-attribute).

::: moniker-end

Kontroleru konstruktor používá [injektáž závislostí](xref:fundamentals/dependency-injection) vkládat kontext databáze (`TodoContext`) do kontroleru. Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru. Konstruktor přidá položku do databáze v paměti, pokud neexistuje.

## <a name="get-to-do-items"></a>Získat položky seznamu úkolů

Získat položky úkolů, přidejte následující metody, které `TodoController` třídy:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../../tutorials/first-web-api/samples/2.0/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../../tutorials/first-web-api/samples/2.1/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

::: moniker-end

Tyto metody implementovat tyto dvě metody GET:

* `GET /api/todo`
* `GET /api/todo/{id}`

Tady je ukázková odpověď HTTP pro `GetAll` metody:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

V pozdější části kurzu ukážeme, jak se dají zobrazit odpověď HTTP [Postman](https://www.getpostman.com/) nebo [curl](https://curl.haxx.se/docs/manpage.html).

### <a name="routing-and-url-paths"></a>Směrování a adresa URL cesty

`[HttpGet]` Atribut označuje metodu, která reaguje na požadavek HTTP GET. Cesta adresy URL pro každou metodu se vypočte takto:

* Využijte řetězec šablony v kontroleru `Route` atribut:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../../tutorials/first-web-api/samples/2.0/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../../tutorials/first-web-api/samples/2.1/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

::: moniker-end

* Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru". V tomto příkladu je název třídy kontroleru **Todo**řadiče a názvu kořenového je "todo". ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.
* Pokud `[HttpGet]` atribut má šablona trasy (například `[HttpGet("/products")]`, připojení, která k cestě. Tato ukázka nepoužívá šablony. Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

V následujícím `GetById` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů. Když `GetById` je vyvolána, přiřadí hodnotu `"{id}"` v adrese URL do metody `id` parametru.

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../../tutorials/first-web-api/samples/2.0/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../../tutorials/first-web-api/samples/2.1/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

::: moniker-end

`Name = "GetTodo"` Vytvoří trasu s tímto názvem. Pojmenované trasy:

* Povolte aplikaci vytvářet propojení HTTP pomocí názvu trasy.
* Jsou vysvětleny dále v tomto kurzu.

### <a name="return-values"></a>Vrácené hodnoty

`GetAll` Metoda vrátí kolekci `TodoItem` objekty. MVC automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON. Kód odpovědi pro tuto metodu je 200, za předpokladu, že nejsou žádné neošetřené výjimky. Nezpracované výjimky jsou přeloženy do chyby 5xx.

::: moniker range="<= aspnetcore-2.0"

Naproti tomu `GetById` metoda vrátí obecnější [IActionResult typ](xref:web-api/action-return-types#iactionresult-type), která představuje širokou škálu návratové typy. `GetById` má dva různé typy vrácené hodnoty:

* Pokud žádná položka odpovídá požadovaným ID, metoda vrátí chybu 404. Vrací [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) vrátí odpověď HTTP 404.
* V opačném případě vrátí metoda 200 s těla odpovědi JSON. Vrací [Ok](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.ok) výsledkem odpověď HTTP 200.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Naproti tomu `GetById` vrátí metoda [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type), která představuje širokou škálu návratové typy. `GetById` má dva různé typy vrácené hodnoty:

* Pokud žádná položka odpovídá požadovaným ID, metoda vrátí chybu 404. Vrací [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) vrátí odpověď HTTP 404.
* V opačném případě vrátí metoda 200 s těla odpovědi JSON. Vrací `item` výsledkem odpověď HTTP 200.

::: moniker-end
