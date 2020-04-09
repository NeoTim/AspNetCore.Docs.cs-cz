---
title: Vytváření progresivních webových Blazor aplikací pomocí ASP.NET základní webové sestavy
author: guardrex
description: Naučte se, jak vytvořit progresivní webovou aplikaci (PWA) založenou Blazorna tom, která používá moderní funkce prohlížeče, aby se chovala jako desktopová aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/progressive-web-app
ms.openlocfilehash: fe69e51aefae9c80e5bb4b78151d384ce25d41a7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218944"
---
# <a name="build-progressive-web-applications-with-aspnet-core-blazor-webassembly"></a>Vytváření progresivních webových aplikací s ASP.NET Core Blazor WebAssembly

Podle [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Progresivní webová aplikace (PWA) je jednostránková aplikace (SPA), která používá moderní prohlížeče API a možnosti chovat jako desktopové aplikace. Blazor WebAssembly je platforma webových aplikací založená na standardech na straně klienta, takže může používat libovolné rozhraní API prohlížeče, včetně rozhraní PWA API vyžadovaných pro následující funkce:

* Práce offline a okamžité načítání, nezávisle na rychlosti sítě.
* Běží ve vlastním okně aplikace, ne jen v okně prohlížeče.
* Spuštění z nabídky startování operačního systému hostitele, doku nebo domovské obrazovky.
* Příjem nabízených oznámení z back-endového serveru, i když uživatel aplikaci nepoužívá.
* Automatická aktualizace na pozadí.

Slovo *progresivní* se používá k popisu těchto aplikací, protože:

* Uživatel může nejprve objevit a používat aplikaci ve svém webovém prohlížeči jako každý jiný SPA.
* Později uživatel přejde k jeho instalaci do operačního systému a povolení nabízených oznámení.

## <a name="create-a-project-from-the-pwa-template"></a>Vytvoření projektu ze šablony pwa

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Při vytváření nové **aplikace Blazor WebAssembly** v **dialogovém** okně Vytvořit nový projekt zaškrtněte políčko **Progress Web Application:**

![Zaškrtávací políčko Progresivní webová aplikace je zaškrtnuto v dialogovém okně nového projektu sady Visual Studio.](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code / .NET Core CLI](#tab/visual-studio-code+netcore-cli)

Vytvořte projekt pwa v příkazovém prostředí s přepínačem: `--pwa`

```dotnetcli
dotnet new blazorwasm -o MyNewProject --pwa
```

---

Volitelně lze aplikaci PWA nakonfigurovat pro aplikaci vytvořenou ze ASP.NET šablonu Hostované jádra. Scénář PWA je nezávislý na modelu hostování.

## <a name="installation-and-app-manifest"></a>Manifest instalace a aplikace

Při návštěvě aplikace vytvořené pomocí šablony PWA mají uživatelé možnost nainstalovat aplikaci do nabídky Start, docku nebo domovské obrazovky operačního systému. Způsob, jakým je tato možnost prezentována, závisí na prohlížeči uživatele. Při používání prohlížečů založených na chromu na ploše, jako je Edge nebo Chrome, se na panelu URL zobrazí tlačítko **Přidat.** Poté, co uživatel vybere tlačítko **Přidat,** obdrží potvrzovací dialog:

![Potvrzovací diaglog v prohlížeči Google Chrome představuje tlačítko Instalovat uživateli pro aplikaci "MyBlazorPwa".](progressive-web-app/_static/image2.png)

V systému iOS mohou návštěvníci nainstalovat aplikaci PWA pomocí tlačítka **Sdílení** safari a možnosti **Přidat na domovskou obrazovku.** V Chromu pro Android by uživatelé měli vybrat tlačítko **Menu** v pravém horním rohu, následované **add to home screen**.

Po instalaci se aplikace zobrazí ve vlastním okně bez adresního řádku:

![Aplikace "MyBlazorPwa" běží v prohlížeči Google Chrome bez adresního řádku.](progressive-web-app/_static/image3.png)

Chcete-li přizpůsobit název okna, barevné schéma, ikonu nebo jiné podrobnosti, podívejte se na soubor *manifest.json* v adresáři *wwwroot* projektu. Schéma tohoto souboru je definováno webovými standardy. Další informace naleznete v [tématu MDN web docs: Web App Manifest](https://developer.mozilla.org/docs/Web/Manifest).

## <a name="offline-support"></a>Podpora offline

Ve výchozím nastavení mají aplikace vytvořené pomocí možnosti šablony PWA podporu pro spouštění offline. Uživatel musí nejprve navštívit aplikaci, když je online. Prohlížeč automaticky stáhne a uloží do mezipaměti všechny prostředky potřebné k provozu offline.

> [!IMPORTANT]
> Rozvojová podpora by narušila obvyklý vývojový cyklus provádění změn a jejich testování. Proto je podpora offline povolena pouze pro *publikované* aplikace. 

> [!WARNING]
> Pokud máte v úmyslu distribuovat offline povoleno uda, existuje [několik důležitých upozornění a upozornění](#caveats-for-offline-pwas). Tyto scénáře jsou vlastní offline PWA Blazora nejsou specifické pro . Nezapomeňte si přečíst a pochopit tyto upozornění před provedením předpoklady o tom, jak bude aplikace s podporou offline fungovat.

Jak funguje offline podpora:

1. Publikujte aplikaci. Další informace naleznete v tématu <xref:host-and-deploy/blazor/index#publish-the-app>.
1. Nasaďte aplikaci na server, který podporuje protokol HTTPS, a získejte přístup k aplikaci v prohlížeči na zabezpečené adrese HTTPS.
1. Otevřete vývojářské nástroje prohlížeče a ověřte, zda je pro hostitele registrován *pracovník služby* na kartě **Aplikace:**

   ![Google Chrome vývojářské nástroje 'Aplikace' karta zobrazuje servisní pracovník aktivován a běží.](progressive-web-app/_static/image4.png)

1. Znovu načtěte stránku a **Service Worker** zkontrolujte **memory cache** kartu **Síť.**

   ![Google Chrome vývojářské nástroje 'Síť' karta zobrazující zdroje pro všechny stránky podkladů.](progressive-web-app/_static/image5.png)

1. Chcete-li ověřit, že prohlížeč není závislý na přístupu k síti pro načtení aplikace, buď:

   * Vypněte webový server a podívejte se, jak aplikace nadále funguje normálně, což zahrnuje opětovné načtení stránky. Stejně tak aplikace nadále fungovat normálně, když je pomalé připojení k síti.
   * Na kartě **Síť** můžete prohlížeči dát pokyn k simulaci režimu offline:

   ![Google Chrome vývojářské nástroje 'Síť' kartu s režimem prohlížeče dropdown se mění z 'Online' na 'Offline'.](progressive-web-app/_static/image6.png)

Podpora offline pomocí servisního pracovníka je Blazorwebový standard, který není specifický pro aplikaci . Další informace o servisních pracovnících naleznete v [tématu MDN web docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API). Další informace o běžných vzorcích používání pracovníků služeb naleznete v [tématu Web Google: Životní cyklus servisního pracovníka](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).

BlazorŠablona PWA společnosti vytváří dva soubory servisních pracovníků:

* *wwwroot/service-worker.js*, který se používá během vývoje.
* *wwwroot/service-worker.published.js*, který se používá po publikování aplikace.

Chcete-li sdílet logiku mezi dvěma soubory pracovníka služby, zvažte následující přístup:

* Přidejte třetí soubor JavaScriptu, který bude obsahovat společnou logiku.
* Pomocí [self.importScripts](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) načtěte společnou logiku do obou souborů servisních pracovníků.

### <a name="cache-first-fetch-strategy"></a>Strategie načítání první cache

Předdefinovaný pracovník *služby.published.js* řeší požadavky pomocí strategie *první mezipaměti.* To znamená, že pracovník služby upřednostňuje vrácení obsahu uloženého v mezipaměti bez ohledu na to, zda má uživatel přístup k síti nebo je na serveru k dispozici novější obsah.

Strategie první cache je cenná, protože:

* **Zajišťuje spolehlivost.** &ndash;Přístup k síti není logický stav. Uživatel není jednoduše online nebo offline:

  * Zařízení uživatele může předpokládat, že je online, ale síť může být tak pomalá, že je nepraktická.
  * Síť může vrátit neplatné výsledky pro určité adresy URL, například když je závislý portál WIFI, který v současné době blokuje nebo přesměrovává určité požadavky.
  
  To je důvod, `navigator.onLine` proč rozhraní API prohlížeče není spolehlivé a nemělo by být závislé.

* **Zajišťuje správnost.** &ndash;Při vytváření mezipaměti offline prostředků, pracovník služby používá hash obsahu zaručit, že načte kompletní a samokonzistentní snímek prostředků v jednom okamžiku v čase. Tato mezipaměť se pak používá jako atomická jednotka. Nemá smysl žádat síť o novější zdroje, protože jediné požadované verze jsou ty, které již byly uloženy v mezipaměti. Cokoli jiného riskuje nekonzistenci a nekompatibilitu (například při pokusu o použití verzí sestavení .NET, které nebyly zkompilovány společně).

### <a name="background-updates"></a>Aktualizace na pozadí

Jako mentální model si můžete myslet, že offline aplikace PWA se chová jako mobilní aplikace, kterou lze nainstalovat. Aplikace se spustí okamžitě bez ohledu na připojení k síti, ale nainstalované aplikace logika pochází z point-in-time snímek, který nemusí být nejnovější verze.

Šablona Blazor PWA vytváří aplikace, které se automaticky pokoušejí aktualizovat na pozadí, kdykoli uživatel navštíví a má funkční síťové připojení. Způsob, jakým to funguje, je následující:

* Během kompilace projekt generuje *manifest prostředků servisního pracovníka*. Ve výchozím nastavení se nazývá *service-worker-assets.js*. Manifest obsahuje seznam všech statických prostředků, které aplikace potřebuje k fungování offline, například sestavení .NET, soubory JavaScript a CSS, včetně jejich hodnot hash obsahu. Seznam prostředků je načten pracovníkem služby tak, aby věděl, které prostředky do mezipaměti.
* Pokaždé, když uživatel navštíví aplikaci, prohlížeč znovu požádá *service-worker.js* a *service-worker-assets.js* na pozadí. Soubory jsou porovnány bajt pro bajt s existujícím nainstalovaným servisním pracovníkem. Pokud server vrátí změněný obsah pro některý z těchto souborů, pracovník služby se pokusí nainstalovat novou verzi sebe sama.
* Při instalaci nové verze sebe sama vytvoří pracovník služby novou samostatnou mezipaměť pro offline prostředky a spustí vyplnění mezipaměti prostředky uvedenými v *souboru service-worker-assets.js*. Tato logika je `onInstall` implementována ve funkci uvnitř *service-worker.published.js*.
* Proces se úspěšně dokončí, když jsou načteny všechny prostředky bez chyby a všechny hashy obsahu odpovídají. Pokud je úspěšná, nový pracovník služby přejde čekání na stav *aktivace.* Jakmile uživatel aplikaci zavře (žádné zbývající karty aplikace nebo okna), *nový* pracovník služby se aktivuje a použije se pro následné návštěvy aplikací. Starý pracovník služby a jeho mezipaměť jsou odstraněny.
* Pokud proces není úspěšně dokončen, je zahozena instance nového servisního pracovníka. Proces aktualizace je pokus znovu na další návštěvu uživatele, když doufejme, že klient má lepší síťové připojení, které lze dokončit požadavky.

Přizpůsobte tento proces úpravou logiky servisního pracovníka. Žádné z předchozích chování Blazor je specifické, ale je pouze výchozí prostředí poskytované možnosti šablony PWA. Další informace naleznete v [tématu MDN web docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).

### <a name="how-requests-are-resolved"></a>Jak jsou požadavky vyřešeny

Jak je popsáno v části [Strategie načítání první cache,](#cache-first-fetch-strategy) výchozí pracovník služby používá strategii *první cache,* což znamená, že se pokusí poskytovat obsah uložený v mezipaměti, pokud je k dispozici. Pokud není k dispozici žádný obsah uložený v mezipaměti pro určitou adresu URL, například při vyžádání dat z rozhraní API back-endu, pracovník služby se vrátí k pravidelnému síťovému požadavku. Požadavek na síť je úspěšný, pokud je server dostupný. Tato logika `onFetch` je implementována uvnitř funkce v rámci *service-worker.published.js*.

Pokud komponenty Razor aplikace spoléhají na vyžádání dat z rozhraní API back-endu a chcete poskytnout popisné uživatelské prostředí pro neúspěšné požadavky z důvodu nedostupnosti sítě, implementujte logiku v rámci komponent aplikace. Můžete například `try/catch` `HttpClient` použít kolem požadavků.

### <a name="support-server-rendered-pages"></a>Podpora stránek vykreslených serverem

Zvažte, co se stane, když uživatel `/counter` poprvé přejde na adresu URL, jako je například nebo jakýkoli jiný přímý odkaz v aplikaci. V těchto případech nechcete vracet obsah uložený `/counter`v mezipaměti jako , ale místo `/index.html` toho potřebujete Blazor prohlížeč k načtení obsahu uloženého v mezipaměti, aby bylo nutné spustit aplikaci WebAssembly. Tyto počáteční požadavky jsou označovány jako *navigační* požadavky, na rozdíl od:

* *požadavky na dílčí zdroje* pro obrazy, šablony stylů nebo jiné soubory.
* *načíst/XHR* požadavky na data rozhraní API.

Výchozí pracovník služby obsahuje logiku zvláštního případu pro navigační požadavky. Pracovník služby vyřeší požadavky vrácením obsahu uloženého `/index.html`v mezipaměti pro , bez ohledu na požadovanou adresu URL. Tato logika je `onFetch` implementována ve funkci uvnitř *service-worker.published.js*.

Pokud vaše aplikace obsahuje určité adresy URL, které musí vrátit `/index.html` serverem vykreslené HTML a neslouží z mezipaměti, pak je třeba upravit logiku v pracovníkovi služby. Pokud všechny adresy URL `/Identity/` obsahující je třeba zpracovat jako pravidelné požadavky pouze online na server, upravte *logiku service-worker.published.js.* `onFetch` Vyhledejte následující kód:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Změňte kód na následující:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

Pokud tak neučiníte, pak bez ohledu na připojení k síti pracovník služby zachytí požadavky `/index.html`na tyto adresy URL a vyřeší je pomocí .

### <a name="control-asset-caching"></a>Řízení ukládání majetku do mezipaměti

Pokud váš projekt `ServiceWorkerAssetsManifest` definuje vlastnost MSBuild, Blazornástroj sestavení sestavení společnosti generuje manifest prostředků pracovníka služby se zadaným názvem. Výchozí šablona aplikace PWA vytvoří soubor projektu obsahující následující vlastnost:

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

Soubor je umístěn do výstupního *adresáře wwwroot,* takže prohlížeč `/service-worker-assets.js`může načíst tento soubor vyžádáním . Chcete-li zobrazit obsah tohoto souboru, otevřete */bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js* v textovém editoru. Neupravujte však soubor, protože je znovu vygenerován v každém sestavení.

Ve výchozím nastavení tento manifest uvádí:

* Všechny Blazorspravované prostředky, například sestavení .NET a runtime soubory .NET WebAssembly potřebné k fungování offline.
* Všechny prostředky pro publikování do adresáře *wwwroot* aplikace, jako jsou obrázky, styly a soubory JavaScript, včetně statických webových datových zdrojů dodávaných externími projekty a balíčky NuGet.

Můžete určit, které z těchto prostředků jsou načteny a uloženy `onInstall` do mezipaměti pracovníka služby úpravou logiky v *service-worker.published.js*. Ve výchozím nastavení pracovník služby načítá a ukládá soubory odpovídající typickým příponek webových souborů, například *.html* Blazor , *.css*, *.js*a *.wasm*, plus typy souborů specifické pro WebAssembly (*DLL*, *.pdb*).

Chcete-li zahrnout další prostředky, které nejsou k dispozici v adresáři `ItemGroup` *wwwroot* aplikace, definujte další položky MSBuild, jak je znázorněno v následujícím příkladu:

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

Metadata `AssetUrl` určují adresu URL základní relativní, kterou by měl prohlížeč použít při načítání prostředku do mezipaměti. To může být nezávislé na jeho původní název zdrojového souboru na disku.

> [!IMPORTANT]
> Přidání `ServiceWorkerAssetsManifestItem` a nezpůsobí, že soubor bude publikován v adresáři *wwwroot* aplikace. Výstup publikování musí být řízen samostatně. Pouze `ServiceWorkerAssetsManifestItem` způsobí, že další položka se zobrazí v manifestu služby pracovníka.

## <a name="push-notifications"></a>Nabízená oznámení

Stejně jako všechny Blazor ostatní pwa, WebAssembly PWA může přijímat nabízená oznámení z back-endového serveru. Server může odesílat nabízená oznámení kdykoli, i když uživatel aplikaci aktivně nepoužívá. Nabízená oznámení lze například odeslat, když jiný uživatel provede příslušnou akci.

Mechanismus pro odesílání nabízených oznámení je Blazor zcela nezávislý na webassembly, protože je implementován back-endserver, který může používat libovolnou technologii. Pokud chcete odesílat nabízená oznámení ze serveru ASP.NET Core, zvažte [použití techniky podobné přístupu přijatému v dílně Blazing Pizza](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications).

Mechanismus pro příjem a zobrazení nabízeného oznámení na Blazor straně klienta je také nezávislý na webové sestavě, protože je implementován v souboru JavaScript u servisního pracovníka. Například viz [přístup použitý v dílně Blazing Pizza](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).

## <a name="caveats-for-offline-pwas"></a>Upozornění pro offline pwa

Ne všechny aplikace by se měly pokoušet podporovat offline použití. Podpora offline přidává značnou složitost, zatímco ne vždy relevantní pro případy použití požadované.

Podpora offline je obvykle relevantní pouze:

* Pokud je primární úložiště dat místní v prohlížeči. Například přístup je relevantní v aplikaci s ui pro zařízení [IoT,](https://en.wikipedia.org/wiki/Internet_of_things) které ukládá data v `localStorage` nebo [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).
* Pokud aplikace provádí značné množství práce načíst a ukládat do mezipaměti data rozhraní API pro každého uživatele tak, aby mohli procházet data v režimu offline. Pokud aplikace musí podporovat úpravy, musí být vytvořen systém pro sledování změn a synchronizaci dat s back-endem.
* Pokud je cílem zaručit, že se aplikace načte okamžitě bez ohledu na podmínky v síti. Implementujte vhodné uživatelské prostředí kolem požadavků rozhraní API back-endu, abyste zobrazili průběh požadavků a řádně se chovali, když se požadavky nezdaří z důvodu nedostupnosti sítě.

Kromě toho se pwa s podporou offline musí vypořádat s řadou dalších komplikací. Vývojáři by se měli pečlivě seznámit s upozorněními v následujících částech.

### <a name="offline-support-only-when-published"></a>Podpora offline pouze při publikování

Během vývoje obvykle chcete vidět každou změnu, která se okamžitě projeví v prohlížeči, aniž byste museli projít procesem aktualizace na pozadí. Proto Blazoršablona pwa aplikace umožňuje podporu offline pouze při publikování.

Při vytváření aplikace podporující offline nestačí otestovat aplikaci ve vývojovém prostředí. Aplikaci je nutné otestovat v publikovaném stavu, abyste pochopili, jak reaguje na různé podmínky sítě.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Aktualizace dokončení po navigaci uživatele mimo aplikaci

Aktualizace se nedokončí, dokud uživatel nepřejde mimo aplikaci na všech kartách. Jak je vysvětleno v části [Aktualizace na pozadí,](#background-updates) po nasazení aktualizace do aplikace prohlížeč načte aktualizované soubory pracovníka služby a zahájí proces aktualizace.

Co překvapuje mnoho vývojářů je, že i když tato aktualizace dokončí, **to se projeví,** dokud uživatel má navigovat pryč ve všech kartách. **Nestačí** aktualizovat kartu zobrazující aplikaci, i když je to jediná karta zobrazující aplikaci. Dokud nebude aplikace úplně zavřená, zůstane nový servisní pracovník v čekání na *aktivaci* stavu. **To není specifické Blazorpro , ale je standardní chování webové platformy.**

To obvykle trápí vývojáře, kteří se pokoušejí otestovat aktualizace svého servisního pracovníka nebo prostředků v mezipaměti offline. Pokud zaškrtnete vývojářské nástroje prohlížeče, může se zobrazit něco jako následující:

![Google Chrome 'Aplikace' karta ukazuje, že servisní pracovník aplikace je 'čeká na aktivaci'.](progressive-web-app/_static/image7.png)

Tak dlouho, dokud seznam "klientů", které jsou karty nebo okna zobrazující vaši aplikaci, je neprázdný, pracovník pokračuje v čekání. Důvodem, proč to servisní pracovníci dělají, je zaručit konzistenci. Konzistence znamená, že všechny prostředky jsou načteny ze stejné atomové mezipaměti.

Při testování změn může být vhodné kliknout na odkaz "skipWaiting", jak je znázorněno na předchozím snímku obrazovky, a pak znovu načíst stránku. Můžete to automatizovat pro všechny uživatele kódováním servisního pracovníka [přeskočit "čekání" fáze a okamžitě aktivovat na aktualizaci](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase). Pokud přeskočíte fázi čekání, vzdáváte se záruky, že prostředky jsou vždy načteny konzistentně ze stejné instance mezipaměti.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Uživatelé mohou spustit libovolnou historickou verzi aplikace

Weboví vývojáři obvykle očekávají, že uživatelé spustí pouze nejnovější nasazenou verzi své webové aplikace, protože to je normální v rámci tradičního modelu webové distribuce. Server PWA, který je první v offline programu, se však více podomábá nativní mobilní aplikaci, kde uživatelé nemusí nutně spouštět nejnovější verzi.

Jak je vysvětleno v části [Aktualizace na pozadí,](#background-updates) po nasazení aktualizace do aplikace **každý existující uživatel nadále používá předchozí verzi alespoň jednu další návštěvu,** protože aktualizace probíhá na pozadí a není aktivována, dokud uživatel poté nepřejde pryč. Navíc předchozí verze, která se používá, nemusí být nutně předchozí verze, kterou jste nasadili. Předchozí verze může být *libovolná* historická verze, v závislosti na tom, kdy uživatel naposledy dokončil aktualizaci.

To může být problém, pokud front-endové a back-endové části aplikace vyžadují souhlas o schématu pro požadavky rozhraní API. Nesmíte nasadit změny schématu zpětného nekompatibilního rozhraní API, dokud si nejste jisti, že všichni uživatelé inovovali. Případně zablokujte uživatelům používání nekompatibilních starších verzí aplikace. Tento scénář požadavek je stejný jako pro nativní mobilní aplikace. Pokud nasadíte narušující změny v serverových api, klientská aplikace je přerušena pro uživatele, kteří ještě nejsou aktualizovány.

Pokud je to možné, nenasazujte narušující změny do back-endových api. Pokud je to nutné, zvažte použití [standardních api servisního pracovníka, jako je ServiceWorkerRegistration](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) k určení, zda je aplikace aktuální a pokud ne, abyste zabránili použití.

### <a name="interference-with-server-rendered-pages"></a>Rušení stránek vykreslených serverem

Jak je popsáno v části [Stránky vykreslené serverem podpory,](#support-server-rendered-pages) pokud chcete obejít chování servisního pracovníka vrácení `/index.html` obsahu pro všechny požadavky navigace, upravte logiku v pracovníkovi služby.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Veškerý obsah manifestu majetku pracovníka servisu je ve výchozím nastavení uložen do mezipaměti.

Jak je popsáno v části [Řízení ukládání majetku do mezipaměti,](#control-asset-caching) soubor *service-worker-assets.js* je generován během sestavení a uvádí všechny prostředky, které by měl pracovník služby načíst a uložit do mezipaměti.

Vzhledem k tomu, že tento seznam ve výchozím nastavení obsahuje vše, co je vydáváno na *wwwroot*, včetně obsahu dodávaného externími balíčky a projekty, musíte být opatrní, abyste tam nevložili příliš mnoho obsahu. Pokud adresář *wwwroot* obsahuje miliony bitových kopií, pracovník služby se pokusí načíst a uložit do mezipaměti všechny, spotřebovává nadměrnou šířku pásma a s největší pravděpodobností není úspěšně dokončena.

Implementujte libovolnou logiku pro řízení, která podmnožina obsahu manifestu `onInstall` by měla být načtena a uložena do mezipaměti úpravou funkce v *service-worker.published.js*.

### <a name="interaction-with-authentication"></a>Interakce s ověřováním

Možnost šablony PWA je možné použít ve spojení s možnostmi ověřování. Server PWA s podporou offline může také podporovat ověřování, pokud má uživatel připojení k síti.

Pokud uživatel nemá připojení k síti, nemůže ověřit ani získat přístupové tokeny. Ve výchozím nastavení má pokus o návštěvu přihlašovací stránky bez přístupu k síti za následek zprávu o chybě v síti.

Je nutné navrhnout tok uživatelského rozhraní, který umožňuje uživateli dělat užitečné věci v offline bez pokusu o ověření nebo získání přístupových tokenů. Případně můžete navrhnout aplikaci tak, aby se nezdařila, když síť není k dispozici. Pokud to ve vaší aplikaci není možné, možná nebudete chtít povolit offline podporu.
