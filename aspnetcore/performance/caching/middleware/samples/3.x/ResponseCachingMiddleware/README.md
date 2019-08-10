# <a name="aspnet-core-response-caching-sample"></a>Ukázka ukládání odpovědi ASP.NET Core do mezipaměti

Tento příklad ukazuje použití ASP.NET Core [middleware pro ukládání odpovědi do mezipaměti](https://docs.microsoft.com/aspnet/core/performance/caching/middleware).

Aplikace odpoví stránkou Index, včetně hlavičky `Cache-Control`, která konfiguruje chování ukládání do mezipaměti. Aplikace také pošle hlavičku `Vary` a nastaví tím mezipaměť tak, aby poslala odpověď pouze v případě, že hlavička `Accept-Encoding` následujících požadavků je stejná jako hlavička původního požadavku.

Při spuštění ukázky pochází stránka Index z mezipaměti, kde je uložena až po dobu 10 sekund.

Postup při testování chování ukládání do mezipaměti:

* Nepoužívejte prohlížeč k testování chování ukládání do mezipaměti. Prohlížeče často při opětovném načtení přidávají hlavičku řízení mezipaměti, která brání middlewaru v obsluze stránky v mezipaměti. Například `Cache-Control` záhlaví s `max-age=0`hodnotou) může být přidáno prohlížečem.
* Použijte vývojářský nástroj, který umožňuje explicitně nastavit hlavičky požadavků, jako je <a href="https://www.telerik.com/fiddler">Fiddler</a> nebo <a href="https://www.getpostman.com/">post</a>.
