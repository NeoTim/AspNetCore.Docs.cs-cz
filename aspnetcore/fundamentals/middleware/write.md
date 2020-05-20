---
<span data-ttu-id="0223b-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0223b-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0223b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0223b-102">'Blazor'</span></span>
- <span data-ttu-id="0223b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0223b-103">'Identity'</span></span>
- <span data-ttu-id="0223b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0223b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="0223b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0223b-105">'Razor'</span></span>
- <span data-ttu-id="0223b-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0223b-106">'SignalR' uid:</span></span> 

---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="0223b-107">Zápis vlastního middlewaru ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0223b-107">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="0223b-108">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0223b-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0223b-109">Middleware je software, který se sestaví do kanálu aplikace za účelem zpracování požadavků a odpovědí.</span><span class="sxs-lookup"><span data-stu-id="0223b-109">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="0223b-110">ASP.NET Core poskytuje bohatou sadu integrovaných komponent middlewaru, ale v některých případech můžete chtít napsat vlastní middleware.</span><span class="sxs-lookup"><span data-stu-id="0223b-110">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="0223b-111">Toto téma popisuje, jak napsat middleware *založený na konvencích* .</span><span class="sxs-lookup"><span data-stu-id="0223b-111">This topic describes how to write *convention-based* middleware.</span></span> <span data-ttu-id="0223b-112">Přístup, který používá silné psaní a aktivaci podle požadavků, najdete v tématu <xref:fundamentals/middleware/extensibility> .</span><span class="sxs-lookup"><span data-stu-id="0223b-112">For an approach that uses strong typing and per-request activation, see <xref:fundamentals/middleware/extensibility>.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="0223b-113">Middleware – třída</span><span class="sxs-lookup"><span data-stu-id="0223b-113">Middleware class</span></span>

<span data-ttu-id="0223b-114">Middleware je obecně zapouzdřena ve třídě a vystavena s metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="0223b-114">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="0223b-115">Vezměte v úvahu následující middleware, který nastaví jazykovou verzi pro aktuální požadavek z řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="0223b-115">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="0223b-116">Předchozí vzorový kód slouží k předvedení vytváření komponenty middlewaru.</span><span class="sxs-lookup"><span data-stu-id="0223b-116">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="0223b-117">Integrovanou podporu lokalizace ASP.NET Core najdete v tématu <xref:fundamentals/localization> .</span><span class="sxs-lookup"><span data-stu-id="0223b-117">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="0223b-118">Otestujte middleware předáním v jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="0223b-118">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="0223b-119">Například Request `https://localhost:5001/?culture=no` .</span><span class="sxs-lookup"><span data-stu-id="0223b-119">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="0223b-120">Následující kód přesune delegáta middlewaru do třídy:</span><span class="sxs-lookup"><span data-stu-id="0223b-120">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="0223b-121">Třída middleware musí zahrnovat:</span><span class="sxs-lookup"><span data-stu-id="0223b-121">The middleware class must include:</span></span>

* <span data-ttu-id="0223b-122">Veřejný konstruktor s parametrem typu <xref:Microsoft.AspNetCore.Http.RequestDelegate> .</span><span class="sxs-lookup"><span data-stu-id="0223b-122">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="0223b-123">Veřejná metoda s názvem `Invoke` nebo `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="0223b-123">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="0223b-124">Tato metoda musí:</span><span class="sxs-lookup"><span data-stu-id="0223b-124">This method must:</span></span>
  * <span data-ttu-id="0223b-125">Vrátí `Task` .</span><span class="sxs-lookup"><span data-stu-id="0223b-125">Return a `Task`.</span></span>
  * <span data-ttu-id="0223b-126">Přijměte první parametr typu <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="0223b-126">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="0223b-127">Další parametry pro konstruktor a `Invoke` / `InvokeAsync` jsou vyplněny pomocí [Injektáže závislosti (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0223b-127">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="0223b-128">Závislosti middlewaru</span><span class="sxs-lookup"><span data-stu-id="0223b-128">Middleware dependencies</span></span>

<span data-ttu-id="0223b-129">Middleware by měly následovat po [principu explicitní závislosti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) tím, že vystaví jeho závislosti ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0223b-129">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="0223b-130">Middleware je postaven jednou za *dobu života aplikace*.</span><span class="sxs-lookup"><span data-stu-id="0223b-130">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="0223b-131">Pokud potřebujete v rámci žádosti sdílet služby se middlewarem, podívejte se na část [závislosti middlewaru na žádost](#per-request-middleware-dependencies) .</span><span class="sxs-lookup"><span data-stu-id="0223b-131">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="0223b-132">Komponenty middlewaru mohou vyřešit své závislosti z [Injektáže závislosti (di)](xref:fundamentals/dependency-injection) prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0223b-132">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="0223b-133">[UseMiddleware &lt; T &gt; ](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) může také přijmout další parametry přímo.</span><span class="sxs-lookup"><span data-stu-id="0223b-133">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="0223b-134">Závislosti middlewaru na požadavek</span><span class="sxs-lookup"><span data-stu-id="0223b-134">Per-request middleware dependencies</span></span>

<span data-ttu-id="0223b-135">Vzhledem k tomu, že middleware se vytvářejí při spuštění aplikace, ne na vyžádání, jsou *služby životnosti* , které jsou používány konstruktory middleware, sdíleny s jinými typy vloženými závislostmi během každé žádosti.</span><span class="sxs-lookup"><span data-stu-id="0223b-135">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="0223b-136">Pokud potřebujete sdílet *vymezenou* službu mezi middlewarem a jinými typy, přidejte tyto služby do `Invoke` signatury metody.</span><span class="sxs-lookup"><span data-stu-id="0223b-136">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="0223b-137">`Invoke`Metoda může přijmout další parametry, které jsou vyplněny parametrem di:</span><span class="sxs-lookup"><span data-stu-id="0223b-137">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a><span data-ttu-id="0223b-138">Metoda rozšíření middlewaru</span><span class="sxs-lookup"><span data-stu-id="0223b-138">Middleware extension method</span></span>

<span data-ttu-id="0223b-139">Následující rozšiřující metoda zpřístupňuje middleware prostřednictvím <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> :</span><span class="sxs-lookup"><span data-stu-id="0223b-139">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="0223b-140">Následující kód volá middleware z `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="0223b-140">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="0223b-141">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0223b-141">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
