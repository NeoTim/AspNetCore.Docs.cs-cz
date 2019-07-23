# <a name="blazor-client-side-sample-app"></a>Ukázková aplikace Blazor (na straně klienta)

Tento příklad ukazuje použití metody Blazor scénáře popsaného v dokumentaci k Blazor.

## <a name="call-web-api-example"></a>Volání webového rozhraní API příklad

Příklad webového rozhraní API vyžaduje spuštění webového rozhraní API založené na ukázkovou aplikaci pro <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">kurzu: Vytvoření webového rozhraní API pomocí ASP.NET Core MVC</a> tématu. Ukázková aplikace se vytvářejí požadavky do webového rozhraní API v `https://localhost:10000/api/todo`. Pokud se používá jiné webové rozhraní API adresu, aktualizujte `ServiceEndpoint` konstantní hodnota v komponentě Razor `@functions` bloku.</p>

Vytvoří ukázkovou aplikaci <a href="https://docs.microsoft.com/aspnet/core/security/cors">prostředků mezi zdroji (CORS) pro sdílení obsahu</a> žádat `http://localhost:5000` nebo `https://localhost:5001` do webového rozhraní API. Přihlašovací údaje (soubory cookie a hlavičky ověření) nejsou povoleny. Přidejte následující konfiguraci middleware CORS webového rozhraní API `Startup.Configure` před voláním metody `UseMvc`:</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Nastavení domény a porty `WithOrigins` podle potřeby Blazor aplikace.

Webové rozhraní API je nakonfigurovaný pro CORS tak, aby povolovala soubory cookie a hlavičky ověření a požadavky z klientského kódu, ale webového rozhraní API jako vytvořené podle tohoto kurzu není ve skutečnosti autorizaci požadavků. Najdete v článku <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core zabezpečení a identita články</a> pro pokyny k implementaci.
