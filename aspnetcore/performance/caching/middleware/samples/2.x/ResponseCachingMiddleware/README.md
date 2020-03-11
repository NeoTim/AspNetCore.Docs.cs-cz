# <a name="aspnet-core-response-caching-sample"></a>Ukázka ukládání odpovědí do mezipaměti ASP.NET Core

V této ukázce se ukazuje použití [middlewaru pro ukládání do mezipaměti s odpovědí](https://docs.microsoft.com/aspnet/core/performance/caching/middleware)ASP.NET Core.

Aplikace odpoví se stránkou indexu, včetně hlavičky `Cache-Control` pro konfiguraci chování ukládání do mezipaměti. Aplikace také nastaví hlavičku `Vary` pro konfiguraci mezipaměti, aby poskytovala odpověď pouze v případě, že `Accept-Encoding` záhlaví dalších požadavků odpovídá původnímu požadavku.

Při spuštění ukázky se stránka index zpracovává z mezipaměti, když se uloží a ukládá do mezipaměti po dobu až 10 sekund.

Postup při testování chování ukládání do mezipaměti:

* Nepoužívejte prohlížeč k testování chování ukládání do mezipaměti. Prohlížeče často při opětovném načtení přidávají hlavičku řízení mezipaměti, která brání middlewaru v obsluze stránky v mezipaměti. Prohlížeč může například přidat hlavičku `Cache-Control` s hodnotou `max-age=0`).
* Použijte vývojářský nástroj, který umožňuje explicitně nastavit hlavičky požadavků, jako je <a href="https://www.telerik.com/fiddler">Fiddler</a> nebo <a href="https://www.getpostman.com/">post</a>.
