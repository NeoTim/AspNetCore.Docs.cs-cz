---
uid: web-forms/overview/performance-and-caching/using-asynchronous-methods-in-aspnet-45
title: Použití asynchronních metod v ASP.NET 4.5 | Dokumentace Microsoftu
author: Rick-Anderson
description: V tomto kurzu se seznámíte se základy vytváření asynchronní aplikace webových formulářů ASP.NET pomocí Visual Studio Express 2012 pro Web, který je bezplatná...
ms.author: riande
ms.date: 01/02/2019
ms.assetid: a585c9a2-7c8e-478b-9706-90f3739c50d1
msc.legacyurl: /web-forms/overview/performance-and-caching/using-asynchronous-methods-in-aspnet-45
msc.type: authoredcontent
ms.openlocfilehash: c36749f82051ee8965035eca9c2e4e57a5dbd616
ms.sourcegitcommit: e1cc4c1ef6c9e07918a609d5ad7fadcb6abe3e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53997341"
---
<a name="using-asynchronous-methods-in-aspnet-45"></a>Použití asynchronních metod v ASP.NET 4.5
====================
Podle [Rick Anderson]((https://twitter.com/RickAndMSFT))

> V tomto kurzu se seznámíte se základy vytváření asynchronní aplikace webových formulářů ASP.NET pomocí [Visual Studio Express 2012 pro Web](https://www.microsoft.com/visualstudio/11), což je bezplatná verze sady Microsoft Visual Studio. Můžete také použít [Visual Studio 2012](https://www.microsoft.com/visualstudio/11). V následujících částech jsou zahrnuté v tomto kurzu.
> 
> - [Zpracování požadavků ve fondu vláken](#HowRequestsProcessedByTP)
> - [Výběr synchronní nebo asynchronní metody](#ChoosingSyncVasync)
> - [Ukázkové aplikace](#SampleApp)
> - [Na stránce synchronní Gizma](#GizmosSynch)
> - [Vytvoření asynchronní Gizma stránky](#CreatingAsynchGizmos)
> - [Paralelní provádění více operací](#Parallel)
> - [Pomocí tokenu zrušení](#CancelToken)
> - [Konfigurace serveru pro volání webové služby vysokou souběžnosti/vysoká latence](#ServerConfig)
> 
> Úplnou ukázku je k dispozici pro účely tohoto kurzu na  
>  [https://github.com/RickAndMSFT/Async-ASP.NET/](https://github.com/RickAndMSFT/Async-ASP.NET/) na [Githubu](https://github.com/) lokality.


ASP.NET 4.5 – webové stránky v kombinaci [.NET 4.5](https://msdn.microsoft.com/library/w0x726c2(VS.110).aspx) Zde můžete registrovat asynchronní metody, které vracejí objekt typu [úloh](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx). Rozhraní .NET Framework 4 zavedena asynchronní programovací koncept se označuje jako [úloh](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) a podporuje technologii ASP.NET 4.5 [úloh](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx). Úkoly jsou reprezentovány **úloh** typu a souvisejících typů v [System.Threading.Tasks](https://msdn.microsoft.com/library/system.threading.tasks.aspx) oboru názvů. Rozhraní .NET Framework 4.5 je založena na této asynchronní podporu [await](https://msdn.microsoft.com/library/hh156528(VS.110).aspx) a [asynchronní](https://msdn.microsoft.com/library/hh156513(VS.110).aspx) klíčová slova, která usnadňuje práci s [úloh](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) objekty mnohem méně složitý než předchozí asynchronní přístupy. [Await](https://msdn.microsoft.com/library/hh156528(VS.110).aspx) – klíčové slovo je syntaktické sdružená hodnota určující, které jsou části kódu by měla asynchronně čekat na další část kódu. [Asynchronní](https://msdn.microsoft.com/library/hh156513(VS.110).aspx) – klíčové slovo představuje pomocného parametru, který můžete použít k označení metod jako úkolově orientovanou asynchronní metody. Kombinace **await**, **asynchronní**a **úloh** objektu je snazší pro vás bude psaní asynchronního kódu v rozhraní .NET 4.5. Nový model pro asynchronní metody je volána *Task-based Asynchronous Pattern* (**klepněte**). Tento kurz předpokládá, že máte některé znalost asynchronní programování pomocí [await](https://msdn.microsoft.com/library/hh156528(VS.110).aspx) a [asynchronní](https://msdn.microsoft.com/library/hh156513(VS.110).aspx) klíčová slova a [úloh](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) oboru názvů.

Další informace na pomocí [await](https://msdn.microsoft.com/library/hh156528(VS.110).aspx) a [asynchronní](https://msdn.microsoft.com/library/hh156513(VS.110).aspx) klíčová slova a [úloh](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) obor názvů, naleznete v následujících odkazech.

- [Dokument White Paper: Asynchronii v rozhraní .NET](https://go.microsoft.com/fwlink/?LinkId=204844)
- [Async/Await – nejčastější dotazy](https://blogs.msdn.com/b/pfxteam/archive/2012/04/12/10293335.aspx)
- [Asynchronní programování Visual Studio](https://msdn.microsoft.com/vstudio/gg316360)

## <a id="HowRequestsProcessedByTP"></a>  Zpracování požadavků ve fondu vláken

Rozhraní .NET Framework na webový server udržuje fondu vláken, které se používají ke zpracování požadavků ASP.NET. Když přijde požadavek, vlákno z fondu odesílá ke zpracování této žádosti. Pokud je požadavek zpracován synchronně, vlákna, která zpracuje požadavek je zaneprázdněn při žádost se zpracovává, a že vlákno nemůže jiná žádost.   
  
To nemusí být problém, protože fond vláken můžete převést na dostatečně velký, aby mnoho zaneprázdněná vlákna. Ale je omezený počet vláken ve fondu vláken (výchozí maximum pro rozhraní .NET 4.5 je 5 000). Ve velkých aplikací s vysokou souběžnosti dlouhodobé požadavky může být k dispozici všechna vlákna zaneprázdněn. Tento stav se označuje jako vyčerpání vlákna. Když je dosaženo této podmínce, webový server zařadí do fronty požadavků. Pokud zaplnění fronty požadavků, webový server odmítá požadavky se stavem HTTP 503 (příliš zaneprázdněný Server). Fondu vláken CLR má omezení na nové vlákno injektáže. Pokud je souběžnost vzorcům (to znamená, vaše webová stránka náhle získáte velký počet požadavků) a všechna vlákna k dispozici žádosti je zaneprázdněná kvůli volání back-end s vysokou latencí, míra vkládání omezené vlákna můžete provádět reagovat velmi špatný. Kromě toho má každém novém vláknu přidán do fondu vláken režijní náklady (jako je například 1 MB paměti zásobníku). V případě webové aplikace pomocí synchronní metody volání mezi službami vysokou latencí, kde fondu vláken se zvětšuje výchozí rozhraní .NET 4.5 maximálně 5 000 vlákna by využívat přibližně 5 GB více paměti, než je možné aplikaci na stejné žádosti o službu pomocí asynchronní metody a pouze 50 vláken. Když provádíte asynchronní práce, ne vždy používáte vlákno. Například pokud provedete požadavek asynchronní webové služby, ASP.NET nebudete používat žádného vlákna mezi **asynchronní** volání metody a **await**. Použití fondu vláken k žádosti o služby s vysokou latencí může vést k velké paměťové nároky a nízké využití hardwaru serveru.

## <a name="processing-asynchronous-requests"></a>Asynchronní zpracování požadavků

Ve webových aplikacích, které najdete v článku velký počet souběžných požadavků na objem požadovaný při spuštění nebo má vzorcům zatížení (kde souběžnosti prudce zvyšuje) aby asynchronní volání webové služby zvýší rychlost reakce aplikace. Asynchronní požadavek trvá stejně jako požadavek na synchronní zpracování. Například pokud požadavek webové služby volání, které vyžaduje dvou sekund na dokončení, požadavek trvá 2 sekundy, zda je provedena synchronně nebo asynchronně. Ale při asynchronním volání není vlákno blokováno reagovat na další požadavky a bude čekat na dokončení první žádosti. Proto asynchronních žádostí zabránit růstu žádosti o služby Řízení front a vlákna fondu pokud existuje velký počet souběžných požadavků, které vyvolají dlouho běžící operace.

## <a id="ChoosingSyncVasync"></a>  Výběr synchronní nebo asynchronní metody

Tato část uvádí pokyny k použití synchronní nebo asynchronní metody. Toto jsou jen pokyny; Prozkoumejte každou aplikaci zvlášť pro určení, zda asynchronní metody pomoci s výkonem.

Obecně platí používejte synchronní metody byly splněny následující podmínky:

- Operace jsou jednoduché nebo krátce běžící.
- Zjednodušení je mnohem důležitější než efektivitu.
- Operace jsou primárně procesoru operace místo operace, které zahrnují rozsáhlou disku nebo nároky na síť. Použití asynchronních metod pro operace vázané na procesor žádné výhody a má za následek další režie.

Obecně platí použijte asynchronní metody byly splněny následující podmínky:

- Volání služby, které lze využívat pomocí asynchronní metody a používáte .NET 4.5 nebo vyšší.
- Operace jsou vázané na síť nebo vstupně-výstupní místo vázané na procesor.
- Paralelismu je důležitější než zjednodušení kódu.
- Chcete poskytovat mechanismus, který umožňuje uživatelům zrušit dlouho probíhající požadavek.
- Když výhodu, že přepínání vlákna větší váhu než náklady na přepnutí kontextu. Obecně platí měli byste zajistit metodu asynchronní Pokud se synchronní metoda blokuje vlákno požadavku ASP.NET v průběhu zpracování operace žádná práce. Tím, že volání asynchronní, není blokován vlákna požadavku ASP.NET dělat nic a bude čekat na dokončení žádosti webové služby.
- Testování ukazuje, že jsou blokující operace problémové místo výkonu webu a, že služba IIS můžete další žádosti o služby s použitím těchto blokujících volání asynchronní metody.

  Ukázky ke stažení ukazuje, jak efektivně používat asynchronní metody. Poskytnutou ukázkou, je navržená k poskytování jednoduchého ukázka asynchronního programování v technologii ASP.NET 4.5. Ukázka nemá představovat referenční architekturu pro asynchronní programování v technologii ASP.NET. Ukázkový program volání [rozhraní ASP.NET Web API](../../../web-api/index.md) metody, které pak volat [Task.Delay](https://msdn.microsoft.com/library/hh139096(VS.110).aspx) pro simulaci volání dlouhotrvající webové služby. Většinu provozních aplikací nezobrazí takové zřejmé výhody použití asynchronních metod.   
  
Několik aplikace vyžadují všechny metody, chcete-li být asynchronní. Převod několik synchronní metody na asynchronní metody často, poskytuje nejlepší účinnosti zvýšení množství práce potřebné.

## <a id="SampleApp"></a>  Ukázkové aplikace

Můžete stáhnout ukázkovou aplikaci z [ https://github.com/RickAndMSFT/Async-ASP.NET ](https://github.com/RickAndMSFT/Async-ASP.NET) na [Githubu](https://github.com/) lokality. Úložiště se skládá ze tří projektů:

- *WebAppAsync*: Projekt webových formulářů ASP.NET, která využívá rozhraní Web API **WebAPIpwg** služby. Většinu kódu pro tento kurz je z tohoto projektu.
- *WebAPIpgw*: Projekt ASP.NET MVC 4 webového rozhraní API, který implementuje `Products, Gizmos and Widgets` řadiče. Poskytuje data pro *WebAppAsync* projektu a *Mvc4Async* projektu.
- *Mvc4Async*: Projekt ASP.NET MVC 4, který obsahuje kód použitý v další kurz. Provede volání webového rozhraní API **WebAPIpwg** služby.

## <a id="GizmosSynch"></a>  Na stránce synchronní Gizma

 Následující kód ukazuje `Page_Load` synchronní metoda, která se používá k zobrazení seznamu gizma. (Pro účely tohoto článku gizmo je fiktivní mechanickým zařízení.) 

[!code-csharp[Main](using-asynchronous-methods-in-aspnet-45/samples/sample1.cs)]

Následující kód ukazuje `GetGizmos` metody gizmo služby.

[!code-csharp[Main](using-asynchronous-methods-in-aspnet-45/samples/sample2.cs)]

`GizmoService GetGizmos` Metoda předává identifikátor URI služby technologie ASP.NET webové rozhraní API HTTP, který vrátí seznam hodnot gizma data. *WebAPIpgw* projekt obsahuje implementaci webového rozhraní API `gizmos, widget` a `product` řadiče.  
Následující obrázek ukazuje stránku gizma z ukázkového projektu.

![Gizma](using-asynchronous-methods-in-aspnet-45/_static/image1.png)

## <a id="CreatingAsynchGizmos"></a>  Vytvoření asynchronní Gizma stránky

Ukázka používá nový [asynchronní](https://msdn.microsoft.com/library/hh156513(VS.110).aspx) a [await](https://msdn.microsoft.com/library/hh156528(VS.110).aspx) klíčová slova (k dispozici v rozhraní .NET 4.5 a Visual Studio 2012) nechat kompilátor, zodpovídá za údržbu složitější transformace nezbytné pro asynchronní programování. Kompilátor umožňuje psát kód pomocí konstrukce jazyka C# pro synchronní řízení toku a kompilátor automaticky použije transformace, která je potřeba použít zpětná volání k zabránění blokování vlákna.

Musí zahrnovat asynchronní stránky technologie ASP.NET [stránky](https://msdn.microsoft.com/library/ydy4x04a.aspx) s `Async` nastavený atribut na hodnotu "true". Následující kód ukazuje [stránky](https://msdn.microsoft.com/library/ydy4x04a.aspx) s `Async` nastavený na hodnotu "true" atribut pro *GizmosAsync.aspx* stránky.

[!code-aspx[Main](using-asynchronous-methods-in-aspnet-45/samples/sample3.aspx?highlight=1)]

Následující kód ukazuje `Gizmos` synchronní `Page_Load` metoda a `GizmosAsync` asynchronní stránky. Pokud váš prohlížeč podporuje [HTML 5 &lt;označit&gt; element](http://www.w3.org/wiki/HTML/Elements/mark), uvidíte změny v `GizmosAsync` v žlutý zvýraznění.

[!code-csharp[Main](using-asynchronous-methods-in-aspnet-45/samples/sample4.cs)]

Asynchronní verze:

[!code-csharp[Main](using-asynchronous-methods-in-aspnet-45/samples/sample5.cs?highlight=3,6-7,9,11)]

 Následující změny byly použity na Povolit `GizmosAsync` stránka byla asynchronní.

- [Stránky](https://msdn.microsoft.com/library/ydy4x04a.aspx) direktiva musí mít `Async` nastavený atribut na hodnotu "true".
- `RegisterAsyncTask` Metoda se používá k registraci asynchronní úloha obsahující kód, který se spouští asynchronně.
- Nové `GetGizmosSvcAsync` metoda je označena [asynchronní](https://msdn.microsoft.com/library/hh156513(VS.110).aspx) – klíčové slovo, které instruuje kompilátor generovat zpětná volání pro část textu a automaticky vytvářet `Task` , která je vrácena.
- &quot;Asynchronní&quot; byl připojeným k názvu asynchronní metody. Připojení "Async" se nevyžadují, ale je konvence při psaní asynchronních metod.
- Návratový typ nového `GetGizmosSvcAsync` je metoda `Task`. Návratový typ `Task` představuje probíhající práci a poskytuje volající metody s popisovačem, prostřednictvím které se má počkat na dokončení asynchronní operace.
- [Await](https://msdn.microsoft.com/library/hh156528(VS.110).aspx) – klíčové slovo byla použita k volání webové služby.
- Volala se asynchronní webového rozhraní API služby (`GetGizmosAsync`).

Uvnitř `GetGizmosSvcAsync` body jiné asynchronní metody, metoda `GetGizmosAsync` je volána. `GetGizmosAsync` okamžitě se vrátí `Task<List<Gizmo>>` , který se nakonec dokončí když jsou data dostupná. Vzhledem k tomu, že nechcete dělat nic dalšího, dokud nebudete mít gizmo data, kód očekává úkol (pomocí **await** – klíčové slovo). Můžete použít **await** – klíčové slovo pouze v metodách opatřen poznámkou **asynchronní** – klíčové slovo.

**Await** – klíčové slovo neblokuje vlákno, dokud je úloha dokončena. Zaregistruje pro zbývající metody jako zpětné volání na úkolu a okamžitě se vrátí. Když nakonec dokončení očekávaného úkolu, bude vyvolání tohoto zpětného volání a tak pokračuje v provádění metody vpravo tam, kde skončila. Další informace o použití [await](https://msdn.microsoft.com/library/hh156528(VS.110).aspx) a [asynchronní](https://msdn.microsoft.com/library/hh156513(VS.110).aspx) klíčová slova a [úloh](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) obor názvů, najdete v článku [asynchronní odkazy](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/async).

Následující kód ukazuje `GetGizmos` a `GetGizmosAsync` metody.

[!code-csharp[Main](using-asynchronous-methods-in-aspnet-45/samples/sample6.cs)]

[!code-csharp[Main](using-asynchronous-methods-in-aspnet-45/samples/sample7.cs?highlight=1,4-8)]

 Asynchronní změny jsou podobné těm, které jsou provedeny **GizmosAsync** výše. 

- Podpis metody je opatřen poznámkou [asynchronní](https://msdn.microsoft.com/library/hh156513(VS.110).aspx) – klíčové slovo, návratový typ se změnil na `Task<List<Gizmo>>`, a *asynchronní* byl připojeným k názvu metody.
- Asynchronní [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(VS.110).aspx) třída se používá místo synchronní [WebClient](https://msdn.microsoft.com/library/system.net.webclient.aspx) třídy.
- [Await](https://msdn.microsoft.com/library/hh156528(VS.110).aspx) – klíčové slovo aplikováno [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(VS.110).aspx)[GetAsync](https://msdn.microsoft.com/library/hh158944(VS.110).aspx) asynchronní metody.

Následující obrázek ukazuje asynchronní gizmo zobrazení.

![async](using-asynchronous-methods-in-aspnet-45/_static/image2.png)

Prezentace prohlížeče gizma dat je stejný jako zobrazení vytvořené pomocí synchronní volání. Jediným rozdílem je, že asynchronní verze může být výkonnější pod velkým zatížením.

## <a name="registerasynctask-notes"></a>RegisterAsyncTask poznámky

Metody připojili pomocí `RegisterAsyncTask` spustí ihned po [PreRender](https://msdn.microsoft.com/library/ms178472.aspx). Události async void stránky můžete také použít přímo, jak je znázorněno v následujícím kódu:

[!code-csharp[Main](using-asynchronous-methods-in-aspnet-45/samples/sample8.cs)]

Nevýhodou události async void je, že vývojáři už má plnou kontrolu nad při spuštění události. Například pokud obě aspx a. Hlavní definovat `Page_Load` události a jedna nebo obě z nich jsou asynchronní, nelze zaručit pořadí provádění. Stejné pořadí indeterminiate jiné obslužné rutiny (jako například `async void Button_Click` ) se vztahuje. Pro většinu vývojářů to by měl být přijatelný, ale rozhraní API, jako jsou ty, kteří vyžadují úplnou kontrolu nad pořadí provádění měli používat jenom `RegisterAsyncTask` , které využívají metody, které vracejí objekt úlohy.

## <a id="Parallel"></a>  Paralelní provádění více operací

Asynchronní metody mají výraznou výhodu nad synchronních metod při akci musíte provést několik nezávislých operací. V ukázce k dispozici, stránce synchronní *PWG.aspx*(pro produkty, Widgetů a Gizma) zobrazuje výsledky zobrazíte seznam produktů, widgetů a gizma tři volání webové služby. [Rozhraní ASP.NET Web API](../../../web-api/index.md) projekt, který poskytuje tyto služby používá [Task.Delay](https://msdn.microsoft.com/library/hh139096(VS.110).aspx) k simulaci latence pomalého volání. Pokud zpoždění nastavená na 500 milisekund, asynchronní *PWGasync.aspx* stránky trvá o něco více než 500 milisekund do dokončení při synchronní `PWG` verze převezme 1 500 milisekund. Synchronní *PWG.aspx* stránka je zobrazena v následujícím kódu.

[!code-csharp[Main](using-asynchronous-methods-in-aspnet-45/samples/sample9.cs)]

Asynchronní `PWGasync` kódu na pozadí je uveden níže.

[!code-csharp[Main](using-asynchronous-methods-in-aspnet-45/samples/sample10.cs?highlight=5,11,21)]

Následující obrázek ukazuje zobrazení vrácený asynchronní *PWGasync.aspx* stránky.

![](using-asynchronous-methods-in-aspnet-45/_static/image3.png)

## <a id="CancelToken"></a>  Pomocí tokenu zrušení

Asynchronní metody vracející `Task`jsou možné zrušit, které se přijaly [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken(VS.110).aspx) parametr, pokud je k dispozici s `AsyncTimeout` atribut [stránky](https://msdn.microsoft.com/library/ydy4x04a.aspx) – direktiva. Následující kód ukazuje *GizmosCancelAsync.aspx* stránky s časovým na druhé.

[!code-aspx[Main](using-asynchronous-methods-in-aspnet-45/samples/sample11.aspx?highlight=1)]

Následující kód ukazuje *GizmosCancelAsync.aspx.cs* souboru.

[!code-csharp[Main](using-asynchronous-methods-in-aspnet-45/samples/sample12.cs?highlight=6,9)]

V ukázkové aplikaci k dispozici, vyberete *GizmosCancelAsync* propojit volání *GizmosCancelAsync.aspx* stránce a ukazuje, kterém jste předplatné zrušili (podle vypršení časového limitu) asynchronního volání. Doba zpoždění je náhodné rozsahu, potřebujete aktualizovat na stránce několikrát se zobrazí chybová zpráva vypršení časového limitu.

## <a id="ServerConfig"></a>  Konfigurace serveru pro volání webové služby vysokou souběžnosti/vysoká latence

Jak začít využívat výhod asynchronní webovou aplikaci, můžete potřebovat udělat nějaké změny ve výchozí konfiguraci serveru. Zachovat následující v úvahu při konfiguraci a zátěžové testování asynchronní webové aplikace.

- Windows 7, Windows Vista, Windows 8, Windows klienta operačních systémů a mít maximálně 10 souběžných požadavků. Je třeba operační systém Windows Server na jeho výhody asynchronních metod v případě velkého zatížení.
- Registrace rozhraní .NET 4.5 se službou IIS z příkazového řádku se zvýšenými oprávněními pomocí následujícího příkazu:  
  %windir%\Microsoft.NET\Framework64 \v4.0.30319\aspnet\_regiis -i  
  Zobrazit [ASP.NET IIS Registrační nástroj (Aspnet\_regiis.exe)](https://msdn.microsoft.com/library/k6h9cz8h.aspx)
- Možná budete muset zvýšit [HTTP.sys](https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture) limit fronty z výchozí hodnoty 1 000 až 5 000. Pokud je nastavení příliš nízké, může se zobrazit [HTTP.sys](https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture) zamítnout žádosti se stavem HTTP 503. Chcete-li změnit HTTP.sys limit fronty:

    - Otevřete Správce služby IIS a přejděte do podokna fondy aplikací.
    - Klikněte pravým tlačítkem myši na cílový fond aplikací a vyberte **Upřesnit nastavení**.  
        ![Upřesnit](using-asynchronous-methods-in-aspnet-45/_static/image4.png)
    - V **Upřesnit nastavení** dialogovém okně Změnit *délka fronty* od 1 do 5 000 000.  
        ![Délka fronty](using-asynchronous-methods-in-aspnet-45/_static/image5.png)  
  
  Poznámka: výše uvedené, Image rozhraní .NET framework je uveden jako verzi 4.0, i když se fond aplikací používá rozhraní .NET 4.5. Informace o tom této nesrovnalosti, naleznete v následujících tématech:

- [Správa verzí rozhraní .NET a cílení na více platforem - .NET 4.5 je místní upgrade na rozhraní .NET 4.0](http://www.hanselman.com/blog/NETVersioningAndMultiTargetingNET45IsAnInplaceUpgradeToNET40.aspx)
- [Nastavení aplikace služby IIS nebo fondu aplikací, používat technologii ASP.NET 3.5 spíše než 2.0](http://www.hanselman.com/blog/HowToSetAnIISApplicationOrAppPoolToUseASPNET35RatherThan20.aspx)
- [Verze rozhraní .NET framework a závislosti](https://msdn.microsoft.com/library/bb822049(VS.110).aspx)

- Pokud vaše aplikace používá webové služby nebo System.NET ke komunikaci s back-end pomocí protokolu HTTP budete možná muset zvýšit [connectionManagement/maxconnection](https://msdn.microsoft.com/library/fb6y0fyc(VS.110).aspx) elementu. Pro aplikace ASP.NET je omezen pomocí funkce automatické konfigurace 12krát větší počet procesorů. To znamená, že na quad-proc, můžete mít nejvýše 12 \* 4 = 48 souběžných připojení na koncový bod IP. Protože to je vázán na [autoConfig](https://msdn.microsoft.com/library/7w2sway1(VS.110).aspx), nejjednodušší způsob, jak zvýšit `maxconnection` v ASP.NET, aplikace je nastavit [System.Net.ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(VS.110).aspx) programově v z `Application_Start` metodu *global.asax* souboru. Najdete v ukázce stahovat pro příklad.
- V rozhraní .NET 4.5, výchozí 5000 pro [maxconcurrentrequestspercpu technologie](https://blogs.msdn.com/tmarq/archive/2007/07/21/asp-net-thread-usage-on-iis-7-0-and-6-0.aspx) by měla být v pořádku.

## <a name="contributors"></a>Contributors

- [Levi Broderick](http://stackoverflow.com/users/59641/levi)
- [Petr Dykstra](http://www.bing.com/search?q=site%3Aasp.net+%22Tom+Dykstra%22+-forums.asp.net&amp;qs=n&amp;form=QBRE&amp;pq=site%3Aasp.net+%22tom+dykstra%22+-forums.asp.net&amp;sc=8-42&amp;sp=-1&amp;sk=)
- [Brad Wilson](http://bradwilson.typepad.com/)
- [HongMei Ge](https://blogs.msdn.com/b/hongmeig/)
