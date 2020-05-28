---
Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core zátěžové nebo zátěžové testování

Zátěžové testování a zátěžové testování jsou důležité k zajištění toho, aby byla webová aplikace výkonná a škálovatelná. Jejich cíle se liší, i když často sdílejí podobné testy.

**Zátěžové testy**: Otestujte, jestli aplikace dokáže zpracovat zadané zatížení uživatelů pro určitý scénář a přitom stále vyhovuje cíli odpovědi. Aplikace se spouští za běžných podmínek.

**Zátěžové testy**: stabilita testovací aplikace při provozu za extrémních podmínek, často po dlouhou dobu. Testy zadávají vysoké uživatelské zatížení, buď špičky nebo postupně zvyšují zatížení, v aplikaci, nebo omezují výpočetní prostředky aplikace.

Zátěžové testy určují, jestli se aplikace v rámci zátěže může zotavit z chyby a řádně se vrátit k očekávanému chování. V případě zátěže není aplikace spouštěna za běžných podmínek.

Visual Studio 2019 je poslední verzí sady Visual Studio s funkcemi zátěžového testu. Pro zákazníky, kteří potřebují nástroje pro testování zatížení v budoucnu, doporučujeme použít alternativní nástroje, jako je Apache JMeter, Akamai CloudTest a BlazeMeter. Další informace naleznete v [poznámkách k verzi sady Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

## <a name="visual-studio-tools"></a>Nástroje sady Visual Studio

Visual Studio umožňuje uživatelům vytvářet, vyvíjet a ladit testy výkonu a zátěže webu. Možnost je k dispozici pro vytváření testů pomocí zaznamenávání akcí ve webovém prohlížeči.

Informace o tom, jak vytvořit, nakonfigurovat a spustit projekty zátěžového testu pomocí sady Visual Studio 2017, naleznete v tématu [rychlý Start: vytvoření projektu zátěžového testu](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).

Zátěžové testy lze nakonfigurovat tak, aby běžely místně nebo běžely v cloudu s využitím Azure DevOps.

## <a name="third-party-tools"></a>Nástroje třetích stran

Následující seznam obsahuje nástroje webového výkonu jiných výrobců s různými sadami funkcí:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (AB)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Svatojánského](https://locust.io/)
* [Webwind v západním větru](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

