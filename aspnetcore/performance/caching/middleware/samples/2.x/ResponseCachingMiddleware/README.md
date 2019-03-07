# <a name="aspnet-core-response-caching-sample"></a>Ukázka ukládání odpovědi ASP.NET Core do mezipaměti

Tento příklad ukazuje použití ASP.NET Core [middleware pro ukládání odpovědi do mezipaměti](https://docs.microsoft.com/aspnet/core/performance/caching/middleware).

Aplikace odpoví stránkou Index, včetně hlavičky `Cache-Control`, která konfiguruje chování ukládání do mezipaměti. Aplikace také pošle hlavičku `Vary` a nastaví tím mezipaměť tak, aby poslala odpověď pouze v případě, že hlavička `Accept-Encoding` následujících požadavků je stejná jako hlavička původního požadavku.

Při spuštění ukázky pochází stránka Index z mezipaměti, kde je uložena až po dobu 10 sekund.
