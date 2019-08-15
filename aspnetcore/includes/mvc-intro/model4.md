Následující tabulka podrobně popisuje ASP.NET Core parametry generátoru kódu:

| Parametr               | Popis|
| ----------------- | ------------ |
| -m  | Název modelu. |
| -dc  | Kontext dat |
| – UDL | Použijte výchozí rozložení. |
| --relativeFolderPath | Relativní cesta k výstupní složce pro vytvoření souborů. |
| --useDefaultLayout | Pro zobrazení je nutné použít výchozí rozložení. |
| --referenceScriptLibraries | Přidá `_ValidationScriptsPartial` k úpravám a vytváření stránek. |

Nápovědu k příkazu získáte pomocí `h`přepínače `aspnet-codegenerator controller` :

```console
dotnet aspnet-codegenerator controller -h
```

Další informace najdete v tématu [dotnet ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)
