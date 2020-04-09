# <a name="blazor-webassembly-sample-app"></a>Ukázková aplikace Blazor WebAssembly

Tato ukázka ilustruje použití blazorských scénářů popsaných v dokumentaci Blazor.

## <a name="call-web-api-example"></a>Příklad webového rozhraní API volání

Příklad webového rozhraní API vyžaduje spuštěné webové rozhraní API založené na ukázkové aplikaci pro <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">vytvoření webového rozhraní API s tématem ASP.NET Core,</a> které ve výchozím nastavení používá stejný port HTTPS (5001) jako ukázkovou aplikaci Blazor. Chcete-li používat obě aplikace ve stejném počítači současně, změňte port webového rozhraní API (například použijte port 10000). Ukázková aplikace provádí požadavky na `https://localhost:10000/api/TodoItems`webové rozhraní API na adrese . Pokud se používá jiná adresa webového rozhraní API, aktualizujte `ServiceEndpoint` konstantní hodnotu v bloku komponenty `@code` Razor.</p>

Ukázková aplikace vytvoří požadavek na sdílení prostředků `http://localhost:5000` mezi `https://localhost:5001` <a href="https://docs.microsoft.com/aspnet/core/security/cors">zdroji (CORS)</a> z webového rozhraní API nebo do něj. Pověření (autorizační soubory cookie/záhlaví) jsou povolena. Přidejte do metody webového `Startup.Configure` rozhraní API následující konfiguraci middlewaru CORS:</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Upravte domény a `WithOrigins` porty podle potřeby pro aplikaci Blazor.

Webové rozhraní API je nakonfigurováno pro CORS k povolení autorizačních souborů cookie/ záhlaví a požadavků z klientského kódu, ale webové rozhraní API vytvořené kurzem ve skutečnosti neautorizuje požadavky. Pokyny k implementaci najdete v <a href="https://docs.microsoft.com/aspnet/core/security/">článcích ASP.NET základní zabezpečení a identity.</a>
