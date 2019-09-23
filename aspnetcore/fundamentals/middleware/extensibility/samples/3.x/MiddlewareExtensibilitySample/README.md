# <a name="aspnet-core-middleware-extensibility-sample"></a>Ukázka rozšiřitelnosti ASP.NET Core middlewaru

Tato ukázka předvádí scénáře popsané v tématu [Aktivace middlewaru založeného na výrobě v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).

Ukázková aplikace demonstruje middleware aktivované:

* Úmluva. Další informace o konvenční aktivaci middlewaru najdete v tématu [middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/) .
* Implementace [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) Výchozí třída [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) aktivuje middleware.

Implementace middlewaru fungují identicky a zaznamenávají hodnotu poskytnutou parametrem řetězce dotazu (`key`). Middleware používají vložený databázový kontext (Oborová služba) k záznamu hodnoty řetězce dotazu do databáze v paměti.
