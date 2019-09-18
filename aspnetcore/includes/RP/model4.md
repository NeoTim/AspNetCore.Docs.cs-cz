<a name="codegenerator"></a>Následující tabulka podrobně popisuje ASP.NET Core parametry generátoru kódu:

| Parametr               | Popis|
| ----------------- | ------------ |
| -m  | Název modelu. |
| -dc  | `DbContext` Třída, která se má použít |
| – UDL | Použijte výchozí rozložení. |
| – outDir | Relativní cesta k výstupní složce pro vytvoření zobrazení |
| --referenceScriptLibraries | Přidá `_ValidationScriptsPartial` k úpravám a vytváření stránek. |

Nápovědu k příkazu získáte pomocí `h`přepínače `aspnet-codegenerator razorpage` :

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Další informace najdete v tématu [dotnet ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator) 