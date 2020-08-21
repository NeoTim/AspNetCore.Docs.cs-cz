<a name="csc"></a>

Vezměte v úvahu následující `ConfigureServices` metodu, která registruje služby a nakonfiguruje možnosti:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Související skupiny registrací lze přesunout do metody rozšíření pro registraci služeb. Například konfigurační služby jsou přidány do následující třídy:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

Zbývající služby jsou registrovány v podobné třídě. Následující `ConfigureServices` Metoda používá nové metody rozšíření k registraci služeb:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

**_Poznámka:_** Každá `services.Add{GROUP_NAME}` metoda rozšíření přidá a případně nakonfiguruje služby. Například <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> Přidání řadičů služby MVC s zobrazeními vyžaduje a <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> přidá služby Razor Pages vyžaduje. Doporučujeme, aby aplikace dodržovaly tyto zásady vytváření názvů. <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName>Do oboru názvů umístěte metody rozšíření pro zapouzdření skupin registrací služby.
