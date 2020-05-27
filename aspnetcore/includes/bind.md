<span data-ttu-id="74a9e-101">Upřednostňovaným způsobem, jak číst související konfigurační hodnoty, je použít [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="74a9e-101">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="74a9e-102">Například pro čtení následujících konfiguračních hodnot:</span><span class="sxs-lookup"><span data-stu-id="74a9e-102">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="74a9e-103">Vytvořte následující `PositionOptions` třídu:</span><span class="sxs-lookup"><span data-stu-id="74a9e-103">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="74a9e-104">Třída možností:</span><span class="sxs-lookup"><span data-stu-id="74a9e-104">An options class:</span></span>

* <span data-ttu-id="74a9e-105">Musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="74a9e-105">Must be non-abstract with a public parameterless constructor.</span></span>
* <span data-ttu-id="74a9e-106">Všechny veřejné vlastnosti pro čtení i zápis typu jsou vázané.</span><span class="sxs-lookup"><span data-stu-id="74a9e-106">All public read-write properties of the type are bound.</span></span>
* <span data-ttu-id="74a9e-107">Pole nejsou ***svázána*** .</span><span class="sxs-lookup"><span data-stu-id="74a9e-107">Fields are ***not*** bound.</span></span> <span data-ttu-id="74a9e-108">V předchozím kódu `Position` není svázána.</span><span class="sxs-lookup"><span data-stu-id="74a9e-108">In the preceding code, `Position` is not bound.</span></span> <span data-ttu-id="74a9e-109">Tato `Position` vlastnost se používá, takže řetězec `"Position"` nemusí být v aplikaci pevně kódovaný při vytváření vazby třídy ke zprostředkovateli konfigurace.</span><span class="sxs-lookup"><span data-stu-id="74a9e-109">The `Position` property is used so the string `"Position"` doesn't need to be hard coded in the app when binding the class to a configuration provider.</span></span>

<span data-ttu-id="74a9e-110">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="74a9e-110">The following code:</span></span>

* <span data-ttu-id="74a9e-111">Volá [ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) pro svázání `PositionOptions` třídy s `Position` oddílem.</span><span class="sxs-lookup"><span data-stu-id="74a9e-111">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="74a9e-112">Zobrazí `Position` konfigurační data.</span><span class="sxs-lookup"><span data-stu-id="74a9e-112">Displays the `Position` configuration data.</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="74a9e-113">V předchozím kódu se ve výchozím nastavení změny konfiguračního souboru JSON po spuštění aplikace čtou.</span><span class="sxs-lookup"><span data-stu-id="74a9e-113">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="74a9e-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)vytvoří vazby a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="74a9e-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="74a9e-115">`ConfigurationBinder.Get<T>`může být pohodlnější než použití `ConfigurationBinder.Bind` .</span><span class="sxs-lookup"><span data-stu-id="74a9e-115">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="74a9e-116">Následující kód ukazuje, jak použít `ConfigurationBinder.Get<T>` s `PositionOptions` třídou:</span><span class="sxs-lookup"><span data-stu-id="74a9e-116">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="74a9e-117">V předchozím kódu se ve výchozím nastavení změny konfiguračního souboru JSON po spuštění aplikace čtou.</span><span class="sxs-lookup"><span data-stu-id="74a9e-117">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="74a9e-118">Alternativním přístupem při použití ***vzoru možností*** je vytvořit vazby `Position` oddílu a přidat ho do [kontejneru služby pro vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="74a9e-118">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="74a9e-119">V následujícím kódu `PositionOptions` je přidán do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a vázaný na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="74a9e-119">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="74a9e-120">Pomocí předchozího kódu přečte následující kód možnosti pozice:</span><span class="sxs-lookup"><span data-stu-id="74a9e-120">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="74a9e-121">V předchozím kódu se změny konfiguračního souboru JSON po zahájení aplikace ***nečtou.***</span><span class="sxs-lookup"><span data-stu-id="74a9e-121">In the preceding code, changes to the JSON configuration file after the app has started are ***not*** read.</span></span> <span data-ttu-id="74a9e-122">Pokud chcete po spuštění aplikace přečíst změny, použijte [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span><span class="sxs-lookup"><span data-stu-id="74a9e-122">To read changes after the app has started, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span></span>
