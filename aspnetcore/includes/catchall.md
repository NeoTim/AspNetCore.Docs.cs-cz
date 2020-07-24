> [!WARNING]
> Parametr **catch-All** může nesprávně odpovídat trasy z důvodu [chyby](https://github.com/dotnet/aspnetcore/issues/18677) v směrování. Aplikace ovlivněné touto chybou mají následující vlastnosti:
>
> * Například trasa typu catch-ALL.`{**slug}"`
> * Trasa catch-All nesplňuje požadavky, které by se měla shodovat.
> * Při odebrání jiných tras bude vše začít pracovat.
>
> Příklady přístupů k této chybě najdete v tématu chyby GitHubu [18677](https://github.com/dotnet/aspnetcore/issues/18677) a [16579](https://github.com/dotnet/aspnetcore/issues/16579) .
>
> Oprava pro tuto chybu je obsažená v [sadě .NET Core 3.1.301 SDK a novější](https://dotnet.microsoft.com/download/dotnet-core/3.1). Následující kód nastaví interní přepínač, který vyřeší tuto chybu:
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```