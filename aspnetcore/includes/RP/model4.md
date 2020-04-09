<a name="codegenerator"></a>V následující tabulce jsou uvedeny parametry generátoru kódu ASP.NET jádra:

| Parametr               | Popis|
| ----------------- | ------------ |
| -m  | Název modelu |
| -dc (směr ový)  | Třída, `DbContext` která má být používána. |
| -udl | Použijte výchozí rozložení. |
| -outDir | Relativní cesta výstupní složky k vytvoření zobrazení. |
| --referenceScriptLibraries | Přidá `_ValidationScriptsPartial` do upravit a vytvořit stránky |

Pomocí `h` přepínače získáte nápovědu k příkazu: `aspnet-codegenerator razorpage`

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Další informace naleznete [v tématu dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
