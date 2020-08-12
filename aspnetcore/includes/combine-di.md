<a name="csc"></a>

## <a name="combining-service-collection"></a>Kombinování kolekce služeb

Vezměte v úvahu následující skutečnosti `ConfigureServices` , které obsahují několik kolekcí služeb:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Související skupiny registrací lze přesunout do metody rozšíření pro registraci služeb. Například konfigurační služby jsou přidány do následující třídy:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfgServiceCollectionExtensions.cs)]

Zbývající služby jsou registrovány v podobné třídě. Následující nástroj `ConfigureServices` používá nové metody rozšíření k registraci služeb:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

***Poznámka:*** Každá `services.Add{SERVICE_NAME}` metoda rozšíření přidá a případně nakonfiguruje služby. Například <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> Přidání řadičů služby MVC s zobrazeními vyžaduje. <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>Přidá služby Razor Pages vyžaduje. Doporučujeme, aby aplikace dodržovaly tyto zásady vytváření názvů. Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.