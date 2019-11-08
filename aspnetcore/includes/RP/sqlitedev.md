### <a name="use-sqlite-for-development-sql-server-for-production"></a>Použití SQLite pro vývoj, SQL Server pro produkci

Když je vybrána možnost SQLite, je kód vygenerovaný šablonou připraven pro vývoj. Následující kód ukazuje, jak vložit <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do startupu. `IWebHostEnvironment` je vloženo, aby `ConfigureServices` mohl použít SQLite ve vývoji a SQL Server v produkčním prostředí.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]
