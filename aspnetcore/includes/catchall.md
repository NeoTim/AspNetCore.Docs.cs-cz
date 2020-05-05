> [!WARNING]
> Parametr **catch-All** může nesprávně odpovídat trasy z důvodu [chyby](https://github.com/dotnet/aspnetcore/issues/18677) v směrování. Aplikace ovlivněné touto chybou mají následující vlastnosti:
>
> * Například trasa typu catch-ALL.`{**slug}"`
> * Trasa catch-All nesplňuje požadavky, které by se měla shodovat.
> * Při odebrání jiných tras bude vše začít pracovat.
>
> Příklady přístupů k této chybě najdete v tématu chyby GitHubu [18677](https://github.com/dotnet/aspnetcore/issues/18677) a [16579](https://github.com/dotnet/aspnetcore/issues/16579) .
>
> Pro tuto chybu se plánuje oprava pro výslovný souhlas. Tento dokument bude aktualizován po vydání opravy. Po vydání opravy bude následující kód nastavit interní přepínač, který tuto chybu vyřeší:
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```