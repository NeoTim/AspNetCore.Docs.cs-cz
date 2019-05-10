<a name="scaffold"></a>

### <a name="scaffold-the-movie-model"></a>Vygenerované uživatelské rozhraní Video modelu

* Spusťte následující příkaz z příkazového řádku (v adresáři projektu, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory):

  ```console
  dotnet restore
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

Pokud dojde k chybě:

  ```
  No executable found matching command "dotnet-aspnet-codegenerator"
  ```

Předchozí chybu se stane, když jsou do nesprávného adresáře. Otevřete příkazové okno k adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory), a pak spusťte ve výstupu předchozího příkazu.

Pokud dojde k chybě:

  ```
  The process cannot access the file
 'RazorPagesMovie/bin/Debug/netcoreapp2.0/RazorPagesMovie.dll'
  because it is being used by another process.
  ```

Ukončení sady Visual Studio a spusťte příkaz znovu.
