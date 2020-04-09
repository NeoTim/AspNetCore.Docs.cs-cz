V následující tabulce jsou uvedeny parametry generátoru kódu ASP.NET jádra:

| Parametr               | Popis|
| ----------------- | ------------ |
| -m  | Název modelu |
| -dc (směr ový)  | Kontext dat. |
| -udl | Použijte výchozí rozložení. |
| --relativeFolderPath | Relativní cesta výstupní složky k vytvoření souborů. |
| --useDefaultLayout | Pro zobrazení by mělo být použito výchozí rozložení. |
| --referenceScriptLibraries | Přidá `_ValidationScriptsPartial` do upravit a vytvořit stránky |

Pomocí `h` přepínače získáte nápovědu k příkazu: `aspnet-codegenerator controller`

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Další informace naleznete [v tématu dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)
