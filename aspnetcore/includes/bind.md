Upřednostňovaným způsobem, jak číst související konfigurační hodnoty, je použít [vzor možností](xref:fundamentals/configuration/options). Například pro čtení následujících konfiguračních hodnot:

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

Vytvořte následující `PositionOptions` třídu:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

Třída možností:

* Musí být neabstraktní s veřejným konstruktorem bez parametrů.
* Všechny veřejné vlastnosti pro čtení i zápis typu jsou vázané.
* Pole nejsou ***svázána*** . V předchozím kódu `Position` není svázána. Tato `Position` vlastnost se používá, takže řetězec `"Position"` nemusí být v aplikaci pevně kódovaný při vytváření vazby třídy ke zprostředkovateli konfigurace.

Následující kód:

* Volá [ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) pro svázání `PositionOptions` třídy s `Position` oddílem.
* Zobrazí `Position` konfigurační data.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

V předchozím kódu se ve výchozím nastavení změny konfiguračního souboru JSON po spuštění aplikace čtou.

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)vytvoří vazby a vrátí zadaný typ. `ConfigurationBinder.Get<T>`může být pohodlnější než použití `ConfigurationBinder.Bind` . Následující kód ukazuje, jak použít `ConfigurationBinder.Get<T>` s `PositionOptions` třídou:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

V předchozím kódu se ve výchozím nastavení změny konfiguračního souboru JSON po spuštění aplikace čtou.

Alternativním přístupem při použití ***vzoru možností*** je vytvořit vazby `Position` oddílu a přidat ho do [kontejneru služby pro vkládání závislostí](xref:fundamentals/dependency-injection). V následujícím kódu `PositionOptions` je přidán do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a vázaný na konfiguraci:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

Pomocí předchozího kódu přečte následující kód možnosti pozice:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

V předchozím kódu se změny konfiguračního souboru JSON po zahájení aplikace ***nečtou.*** Pokud chcete po spuštění aplikace přečíst změny, použijte [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).
