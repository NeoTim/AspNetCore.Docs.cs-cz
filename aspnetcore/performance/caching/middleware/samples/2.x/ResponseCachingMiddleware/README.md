# <a name="aspnet-core-response-caching-sample"></a>Ukázka ukládání odpovědi ASP.NET Core do mezipaměti

Tento příklad ukazuje použití ASP.NET Core [middleware pro ukládání odpovědi do mezipaměti](https://docs.microsoft.com/aspnet/core/performance/caching/middleware).

Aplikace odpoví stránkou Index, včetně hlavičky `Cache-Control`, která konfiguruje chování ukládání do mezipaměti. Aplikace také pošle hlavičku `Vary` a nastaví tím mezipaměť tak, aby poslala odpověď pouze v případě, že hlavička `Accept-Encoding` následujících požadavků je stejná jako hlavička původního požadavku.

Při spuštění ukázky pochází stránka Index z mezipaměti, kde je uložena až po dobu 10 sekund.

K otestování chování ukládání do mezipaměti:

* Nepoužívejte k otestování chování ukládání do mezipaměti prohlížeče. Prohlížeče často přidat záhlaví mezipaměti ovládacího prvku při opakovaném načtení znovu, který middleware zabránit obsluhující stránky v mezipaměti. For example `Cache-Control` záhlaví s hodnotou `max-age=0`) mohou být přidány v prohlížeči.
* Použijte nástroj pro vývojáře, která umožňuje nastavení hlavičky požadavku explicitně, jako například <a href="https://www.telerik.com/fiddler">Fiddler</a> nebo <a href="https://www.getpostman.com/">Postman</a>.
