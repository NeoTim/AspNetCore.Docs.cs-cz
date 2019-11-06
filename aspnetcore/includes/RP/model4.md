<a name="codegenerator"></a>Následující tabulka podrobně popisuje ASP.NET Core parametry generátoru kódu:

| Parametr               | Popis|
| ----------------- | ------------ |
| -m  | Název modelu. |
| – DC  | Třída `DbContext`, která se má použít |
| – UDL | Použijte výchozí rozložení. |
| – outDir | Relativní cesta k výstupní složce pro vytvoření zobrazení |
| --referenceScriptLibraries | Přidá `_ValidationScriptsPartial` k úpravám a vytváření stránek. |

Nápovědu k příkazu `aspnet-codegenerator razorpage` získáte pomocí `h`ovém přepínači:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Další informace najdete v tématu [dotnet ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
