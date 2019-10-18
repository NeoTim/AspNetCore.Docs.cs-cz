# <a name="blazor-webassembly-sample-app"></a>Ukázková aplikace Blazor WebAssembly

Tato ukázka znázorňuje použití scénářů Blazor popsaných v dokumentaci k Blazor.

## <a name="call-web-api-example"></a>Příklad volání webového rozhraní API

Příklad webového rozhraní API vyžaduje běžící webové rozhraní API na základě ukázkové aplikace pro <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">Vytvoření webového rozhraní API s ASP.NET Coreým</a> tématem, které ve výchozím nastavení používá stejný port HTTPS (5001) jako ukázková aplikace Blazor. Pokud chcete současně použít obě aplikace na stejném počítači, změňte port webového rozhraní API (například použijte port 10000). Ukázková aplikace dává žádosti na webové rozhraní API na `https://localhost:10000/api/TodoItems`. Pokud se používá jiná adresa webového rozhraní API, aktualizujte `ServiceEndpoint` hodnotu konstanty v bloku `@code` komponenty Razor.</p>

Ukázková aplikace vytvoří žádost o <a href="https://docs.microsoft.com/aspnet/core/security/cors">sdílení prostředků mezi zdroji (CORS)</a> od `http://localhost:5000` nebo `https://localhost:5001` k webovému rozhraní API. Přihlašovací údaje (autorizační soubory cookie/hlavičky) jsou povolené. Do metody `Startup.Configure` webového rozhraní API přidejte následující konfiguraci middlewaru CORS:</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Upravte domény a porty `WithOrigins` podle potřeby aplikace Blazor.

Webové rozhraní API je nakonfigurované pro CORS a povoluje autorizační soubory cookie, hlavičky a požadavky z klientského kódu, ale webové rozhraní API, jak ho vytvořil kurz, ve skutečnosti neautorizuje žádosti. Pokyny k implementaci najdete v <a href="https://docs.microsoft.com/aspnet/core/security/">článcích ASP.NET Core zabezpečení a identita</a> .
