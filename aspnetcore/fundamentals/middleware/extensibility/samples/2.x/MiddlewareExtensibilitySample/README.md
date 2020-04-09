# <a name="aspnet-core-middleware-extensibility-sample"></a>ukázka rozšiřitelnosti ASP.NET základního souboru Middleware

Tato ukázka ukazuje scénáře popsané v [aktivaci middlewaru založené na výrobě v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).

Ukázková aplikace ukazuje middleware aktivovaný:

* Úmluvy. Další informace o konvenční aktivaci middlewaru naleznete v tématu [Middleware.](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/)
* Implementace [IMiddleware.](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) Výchozí třída [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) aktivuje middleware.

Implementace middlewaru fungují identicky a zaznamenávají hodnotu`key`poskytovanou parametrem řetězce dotazu ( ). Middlewares použít vložené kontextu databáze (oborové služby) zaznamenat hodnotu řetězce dotazu v databázi v paměti.
