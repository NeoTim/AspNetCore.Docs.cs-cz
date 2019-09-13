# <a name="blazor-webassembly-sample-app"></a>Ukázková aplikace Blazor WebAssembly

Tato ukázka znázorňuje použití scénářů Blazor popsaných v dokumentaci k Blazor.

## <a name="call-web-api-example"></a>Příklad volání webového rozhraní API

Příklad webového rozhraní API vyžaduje běžící webové rozhraní API na základě ukázkové aplikace pro tento <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core MVC</a> Ukázková aplikace vytváří požadavky na webové rozhraní API na `https://localhost:10000/api/todo`adrese. Pokud se používá jiná adresa webového rozhraní API, aktualizujte `ServiceEndpoint` hodnotu konstanty v `@functions` bloku komponenty Razor.</p>

Ukázková aplikace vytvoří žádost o <a href="https://docs.microsoft.com/aspnet/core/security/cors">sdílení prostředků mezi zdroji (CORS)</a> od `http://localhost:5000` webového rozhraní `https://localhost:5001` API nebo do něj. Přihlašovací údaje (autorizační soubory cookie/hlavičky) jsou povolené. Před voláním `Startup.Configure` `UseMvc`metody webového rozhraní API přidejte následující konfiguraci middleware CORS:</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Upravte domény a porty `WithOrigins` podle potřeby pro aplikaci Blazor.

Webové rozhraní API je nakonfigurované pro CORS a povoluje autorizační soubory cookie, hlavičky a požadavky z klientského kódu, ale webové rozhraní API, jak ho vytvořil kurz, ve skutečnosti neautorizuje žádosti. Pokyny k implementaci najdete v <a href="https://docs.microsoft.com/aspnet/core/security/">článcích ASP.NET Core zabezpečení a identita</a> .
