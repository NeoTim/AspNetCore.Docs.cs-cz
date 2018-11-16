::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../../tutorials/first-web-api/samples/2.0/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="8dfa1-101">Předcházející kód definuje třídu kontroleru rozhraní API bez metody.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-101">The preceding code defines an API controller class without methods.</span></span> <span data-ttu-id="8dfa1-102">V následujících částech jsou přidány metody k implementaci rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-102">In the next sections, methods are added to implement the API.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../../tutorials/first-web-api/samples/2.1/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="8dfa1-103">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="8dfa1-103">The preceding code:</span></span>

* <span data-ttu-id="8dfa1-104">Definuje třídu kontroleru rozhraní API bez metody.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-104">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="8dfa1-105">Vytvoří nový úkol položky, když `TodoItems` je prázdný.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-105">Creates a new Todo item when `TodoItems` is empty.</span></span> <span data-ttu-id="8dfa1-106">Není možné odstranit všechny položky Todo protože konstruktoru vytvoří novou jeden if `TodoItems` je prázdný.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-106">You won't be able to delete all the Todo items because the constructor creates a new one if `TodoItems` is empty.</span></span>

<span data-ttu-id="8dfa1-107">V následujících částech jsou přidány metody k implementaci rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-107">In the next sections, methods are added to implement the API.</span></span> <span data-ttu-id="8dfa1-108">Třída je opatřen poznámkou `[ApiController]` atribut pro povolení některé vhodné funkce.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-108">The class is annotated with an `[ApiController]` attribute to enable some convenient features.</span></span> <span data-ttu-id="8dfa1-109">Informace o funkcích, které jsou povolené v atributu naleznete v tématu [Poznámka atributem objektu ApiController](xref:web-api/index#annotation-with-apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="8dfa1-109">For information on features enabled by the attribute, see [Annotation with ApiController attribute](xref:web-api/index#annotation-with-apicontroller-attribute).</span></span>

::: moniker-end

<span data-ttu-id="8dfa1-110">Kontroleru konstruktor používá [injektáž závislostí](xref:fundamentals/dependency-injection) vkládat kontext databáze (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-110">The controller's constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="8dfa1-111">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-111">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span> <span data-ttu-id="8dfa1-112">Konstruktor přidá položku do databáze v paměti, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-112">The constructor adds an item to the in-memory database if one doesn't exist.</span></span>

## <a name="get-to-do-items"></a><span data-ttu-id="8dfa1-113">Získat položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="8dfa1-113">Get to-do items</span></span>

<span data-ttu-id="8dfa1-114">Získat položky úkolů, přidejte následující metody, které `TodoController` třídy:</span><span class="sxs-lookup"><span data-stu-id="8dfa1-114">To get to-do items, add the following methods to the `TodoController` class:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../../tutorials/first-web-api/samples/2.0/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../../tutorials/first-web-api/samples/2.1/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

::: moniker-end

<span data-ttu-id="8dfa1-115">Tyto metody implementovat tyto dvě metody GET:</span><span class="sxs-lookup"><span data-stu-id="8dfa1-115">These methods implement the two GET methods:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="8dfa1-116">Tady je ukázková odpověď HTTP pro `GetAll` metody:</span><span class="sxs-lookup"><span data-stu-id="8dfa1-116">Here's a sample HTTP response for the `GetAll` method:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

<span data-ttu-id="8dfa1-117">V pozdější části kurzu ukážeme, jak se dají zobrazit odpověď HTTP [Postman](https://www.getpostman.com/) nebo [curl](https://curl.haxx.se/docs/manpage.html).</span><span class="sxs-lookup"><span data-stu-id="8dfa1-117">Later in the tutorial, I'll show how the HTTP response can be viewed with [Postman](https://www.getpostman.com/) or [curl](https://curl.haxx.se/docs/manpage.html).</span></span>

### <a name="routing-and-url-paths"></a><span data-ttu-id="8dfa1-118">Směrování a adresa URL cesty</span><span class="sxs-lookup"><span data-stu-id="8dfa1-118">Routing and URL paths</span></span>

<span data-ttu-id="8dfa1-119">`[HttpGet]` Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-119">The `[HttpGet]` attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="8dfa1-120">Cesta adresy URL pro každou metodu se vypočte takto:</span><span class="sxs-lookup"><span data-stu-id="8dfa1-120">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="8dfa1-121">Využijte řetězec šablony v kontroleru `Route` atribut:</span><span class="sxs-lookup"><span data-stu-id="8dfa1-121">Take the template string in the controller's `Route` attribute:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../../tutorials/first-web-api/samples/2.0/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../../tutorials/first-web-api/samples/2.1/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

::: moniker-end

* <span data-ttu-id="8dfa1-122">Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru".</span><span class="sxs-lookup"><span data-stu-id="8dfa1-122">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="8dfa1-123">V tomto příkladu je název třídy kontroleru **Todo**řadiče a názvu kořenového je "todo".</span><span class="sxs-lookup"><span data-stu-id="8dfa1-123">For this sample, the controller class name is **Todo**Controller and the root name is "todo".</span></span> <span data-ttu-id="8dfa1-124">ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-124">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="8dfa1-125">Pokud `[HttpGet]` atribut má šablona trasy (například `[HttpGet("/products")]`, připojení, která k cestě.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-125">If the `[HttpGet]` attribute has a route template (such as `[HttpGet("/products")]`, append that to the path.</span></span> <span data-ttu-id="8dfa1-126">Tato ukázka nepoužívá šablony.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-126">This sample doesn't use a template.</span></span> <span data-ttu-id="8dfa1-127">Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="8dfa1-127">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="8dfa1-128">V následujícím `GetById` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-128">In the following `GetById` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="8dfa1-129">Když `GetById` je vyvolána, přiřadí hodnotu `"{id}"` v adrese URL do metody `id` parametru.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-129">When `GetById` is invoked, it assigns the value of `"{id}"` in the URL to the method's `id` parameter.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../../tutorials/first-web-api/samples/2.0/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../../tutorials/first-web-api/samples/2.1/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

::: moniker-end

<span data-ttu-id="8dfa1-130">`Name = "GetTodo"` Vytvoří trasu s tímto názvem.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-130">`Name = "GetTodo"` creates a named route.</span></span> <span data-ttu-id="8dfa1-131">Pojmenované trasy:</span><span class="sxs-lookup"><span data-stu-id="8dfa1-131">Named routes:</span></span>

* <span data-ttu-id="8dfa1-132">Povolte aplikaci vytvářet propojení HTTP pomocí názvu trasy.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-132">Enable the app to create an HTTP link using the route name.</span></span>
* <span data-ttu-id="8dfa1-133">Jsou vysvětleny dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-133">Are explained later in the tutorial.</span></span>

### <a name="return-values"></a><span data-ttu-id="8dfa1-134">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="8dfa1-134">Return values</span></span>

<span data-ttu-id="8dfa1-135">`GetAll` Metoda vrátí kolekci `TodoItem` objekty.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-135">The `GetAll` method returns a collection of `TodoItem` objects.</span></span> <span data-ttu-id="8dfa1-136">MVC automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-136">MVC automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="8dfa1-137">Kód odpovědi pro tuto metodu je 200, za předpokladu, že nejsou žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-137">The response code for this method is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="8dfa1-138">Nezpracované výjimky jsou přeloženy do chyby 5xx.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-138">Unhandled exceptions are translated into 5xx errors.</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="8dfa1-139">Naproti tomu `GetById` metoda vrátí obecnější [IActionResult typ](xref:web-api/action-return-types#iactionresult-type), která představuje širokou škálu návratové typy.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-139">In contrast, the `GetById` method returns the more general [IActionResult type](xref:web-api/action-return-types#iactionresult-type), which represents a wide range of return types.</span></span> <span data-ttu-id="8dfa1-140">`GetById` má dva různé typy vrácené hodnoty:</span><span class="sxs-lookup"><span data-stu-id="8dfa1-140">`GetById` has two different return types:</span></span>

* <span data-ttu-id="8dfa1-141">Pokud žádná položka odpovídá požadovaným ID, metoda vrátí chybu 404.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-141">If no item matches the requested ID, the method returns a 404 error.</span></span> <span data-ttu-id="8dfa1-142">Vrací [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) vrátí odpověď HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-142">Returning [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) returns an HTTP 404 response.</span></span>
* <span data-ttu-id="8dfa1-143">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-143">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="8dfa1-144">Vrací [Ok](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.ok) výsledkem odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-144">Returning [Ok](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.ok) results in an HTTP 200 response.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8dfa1-145">Naproti tomu `GetById` vrátí metoda [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type), která představuje širokou škálu návratové typy.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-145">In contrast, the `GetById` method returns the [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type), which represents a wide range of return types.</span></span> <span data-ttu-id="8dfa1-146">`GetById` má dva různé typy vrácené hodnoty:</span><span class="sxs-lookup"><span data-stu-id="8dfa1-146">`GetById` has two different return types:</span></span>

* <span data-ttu-id="8dfa1-147">Pokud žádná položka odpovídá požadovaným ID, metoda vrátí chybu 404.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-147">If no item matches the requested ID, the method returns a 404 error.</span></span> <span data-ttu-id="8dfa1-148">Vrací [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) vrátí odpověď HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-148">Returning [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) returns an HTTP 404 response.</span></span>
* <span data-ttu-id="8dfa1-149">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-149">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="8dfa1-150">Vrací `item` výsledkem odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="8dfa1-150">Returning `item` results in an HTTP 200 response.</span></span>

::: moniker-end
