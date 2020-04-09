### <a name="use-sqlite-for-development-sql-server-for-production"></a>Použití SQLite pro vývoj, SQL Server pro výrobu

Když je vybránsQLite, vygenerovaný kód šablony je připraven k vývoji. Následující kód ukazuje, <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> jak vložit do spuštění. `IWebHostEnvironment`je injektován tak `ConfigureServices` lze použít SQLite ve vývoji a SQL Server v produkčním prostředí.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]
