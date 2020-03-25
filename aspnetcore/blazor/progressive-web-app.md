---
title: Sestavování progresivních webových aplikací pomocí ASP.NET Core Blazor webovém sestavení
author: guardrex
description: Naučte se vytvářet progresivní webové aplikace založené na Blazor(PWA), které používají moderní funkce prohlížeče, aby se chovala jako desktopová aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/progressive-web-app
ms.openlocfilehash: fe69e51aefae9c80e5bb4b78151d384ce25d41a7
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218944"
---
# <a name="build-progressive-web-applications-with-aspnet-core-blazor-webassembly"></a>Sestavování progresivních webových aplikací pomocí ASP.NET Core Blazor WebAssembly

Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Progresivní webová aplikace (PWA) je jediná stránková aplikace (SPA), která používá moderní rozhraní API a možnosti prohlížeče, aby se choval jako desktopová aplikace. Blazor WebAssembly je platforma webových aplikací založená na standardech na straně klienta, takže může používat libovolné rozhraní API prohlížeče, včetně rozhraní API PWA potřebných pro následující funkce:

* Pracujete offline a okamžitě se načítají nezávisle na rychlosti sítě.
* Spuštění ve vlastním okně aplikace, ne pouze v okně prohlížeče.
* Spouští se z nabídky Start v operačním systému hostitele, ukotvení nebo na domovské obrazovce.
* Příjem nabízených oznámení ze serveru back-end, a to i v případě, že uživatel aplikaci nepoužívá.
* Automatické aktualizace na pozadí.

K popisu takových aplikací se používá slovo *progresivní* :

* Uživatel může aplikaci nejprve vyhledat a používat v rámci webového prohlížeče jako jakékoli jiné zabezpečené ověřování hesla.
* Později uživatel postupuje jeho instalaci v operačním systému a povolení nabízených oznámení.

## <a name="create-a-project-from-the-pwa-template"></a>Vytvoření projektu ze šablony PWA

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Při vytváření nové **aplikace WebAssembly v Blazor** v dialogovém okně **vytvořit nový projekt** vyberte zaškrtávací políčko **průběh webová aplikace** :

![Zaškrtávací políčko progresivní webová aplikace je zaškrtnuto v dialogovém okně Nový projekt aplikace Visual Studio.](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

Vytvořte projekt PWA v příkazovém prostředí s přepínačem `--pwa`:

```dotnetcli
dotnet new blazorwasm -o MyNewProject --pwa
```

---

V případě potřeby lze aplikaci PWA nakonfigurovat pro aplikaci vytvořenou z hostované šablony ASP.NET Core. Scénář aplikace PWA je nezávislý na modelu hostování.

## <a name="installation-and-app-manifest"></a>Instalace a manifest aplikace

Při návštěvě aplikace vytvořené pomocí šablony PWA mají uživatelé možnost instalovat aplikaci do nabídky Start, Dock nebo Home v operačním systému. Způsob, jakým je tato možnost uvedená, závisí na prohlížeči uživatele. Při použití prohlížečů založených na desktopových Chromech, jako je například Edge nebo Chrome, se na panelu Adresa URL zobrazí tlačítko **Přidat** . Jakmile uživatel vybere tlačítko **Přidat** , obdrží potvrzovací dialog:

![Diaglog potvrzení na Google Chrome prezentuje uživateli tlačítko pro instalaci aplikace MyBlazorPwa.](progressive-web-app/_static/image2.png)

V systému iOS můžou Návštěvníci nainstalovat aplikaci PWA pomocí tlačítka pro **sdílení** v prohlížeči Safari a její možnosti **Přidat do homescreen** . V Chrome pro Android by uživatelé měli vybrat tlačítko **nabídky** v pravém horním rohu a potom **Přidat na domovskou obrazovku**.

Po instalaci se aplikace zobrazí ve vlastním okně bez adresního řádku:

![Aplikace MyBlazorPwa běží na Google Chrome bez adresního řádku.](progressive-web-app/_static/image3.png)

Chcete-li přizpůsobit název okna, barevné schéma, ikonu nebo jiné podrobnosti, přečtěte si soubor *manifest. JSON* v adresáři *wwwroot* projektu. Schéma tohoto souboru je definováno webovými standardy. Další informace najdete v tématu [MDN web Docs: manifest webové aplikace](https://developer.mozilla.org/docs/Web/Manifest).

## <a name="offline-support"></a>Podpora offline

Ve výchozím nastavení mají aplikace vytvořené pomocí možnosti šablony PWA podporu pro spuštění offline. Uživatel musí nejdřív aplikaci navštívit, zatímco je online. Prohlížeč automaticky stáhne a zapíše do mezipaměti všechny prostředky, které jsou potřeba pro práci offline.

> [!IMPORTANT]
> Vývojová podpora by vedla ke konfliktu s obvyklým vývojovým cyklem provádění změn a jejich testování. Podpora offline je proto povolená jenom pro *publikované* aplikace. 

> [!WARNING]
> Pokud máte v úmyslu distribuovat offline s povolenou aplikaci PWA, je k dispozici [několik důležitých upozornění a aspektů](#caveats-for-offline-pwas). Tyto scénáře jsou podstatou pro offline PWAs a nespecifická pro Blazor. Nezapomeňte si tyto upozornění přečíst a pochopit před tím, než začnete vytvářet předpoklady pro fungování offline aplikace.

Pokud chcete zjistit, jak funguje podpora offline:

1. Publikujte aplikaci. Další informace naleznete v tématu <xref:host-and-deploy/blazor/index#publish-the-app>.
1. Nasaďte aplikaci na server, který podporuje protokol HTTPS, a získejte přístup k aplikaci v prohlížeči pomocí zabezpečené adresy HTTPS.
1. Otevřete vývojové nástroje v prohlížeči a ověřte, zda je na kartě **aplikace** zaregistrován *pracovní proces služby* pro hostitele:

   ![Karta aplikace Google Chrome Developer Tools zobrazuje aktivované a běžící pracovní proces služby.](progressive-web-app/_static/image4.png)

1. Znovu načtěte stránku a prověřte kartu **síť** . **pracovní proces služby** nebo **mezipaměť paměti** jsou uvedené jako zdroje pro všechny prostředky stránky:

   ![Karta sítě Google Chrome Developer Tools zobrazuje zdroje pro všechny prostředky stránky.](progressive-web-app/_static/image5.png)

1. Chcete-li ověřit, že prohlížeč není závislý na síťovém přístupu pro načtení aplikace, postupujte takto:

   * Vypněte webový server a podívejte se, jak aplikace nadále funguje normálně, což zahrnuje opětovné načtení stránky. Podobně aplikace funguje normálně i v případě, že dojde k pomalému připojení k síti.
   * Poskytněte prohlížeči simulaci offline režimu na kartě **síť** :

   ![Karta sítě Google Chrome Developer Tools s rozevíracím seznamem režimu prohlížeče se změní z online na offline.](progressive-web-app/_static/image6.png)

Podpora offline pomocí pracovního procesu služby je webový standard, který není specifický pro Blazor. Další informace o pracovních procesech služby najdete v tématu [MDN web Docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API). Další informace o běžných způsobech použití pro pracovníky služeb najdete v tématu [Google Web: životní cyklus pracovního procesu služby](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).

Šablona aplikace BlazorPWA vytvoří dva pracovní pracovní soubory služby:

* *wwwroot/Service-Worker. js*, který se používá při vývoji.
* *wwwroot/Service-Worker. Publikováno. js*, který se používá po publikování aplikace.

Chcete-li sdílet logiku mezi dvěma pracovními soubory služby, vezměte v úvahu následující postup:

* Přidejte třetí soubor JavaScriptu pro uložení běžné logiky.
* Pomocí [sebe. importScripts](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) načtěte společnou logiku do obou souborů pracovních procesů služby.

### <a name="cache-first-fetch-strategy"></a>Strategie prvního načtení mezipaměti

Vestavěný pracovní proces služby *Service-Worker. Publish. js* řeší požadavky pomocí strategie *mezipaměti-First* . To znamená, že pracovní proces služby dává přednost vrácení obsahu v mezipaměti bez ohledu na to, jestli má uživatel přístup k síti nebo novější obsah dostupný na serveru.

Strategie pro první mezipaměť je užitečná z těchto důvodů:

* **Zajišťuje spolehlivost.** přístup k síti &ndash; není logický stav. Uživatel není jednoduše online nebo offline:

  * Zařízení uživatele může předpokládat, že je online, ale síť může být tak pomalá, aby nedošlo k nepraktickému čekání.
  * Síť může u některých adres URL vracet neplatné výsledky, například když je k dispozici nějaký portál WIFI, který aktuálně blokuje nebo přesměrovává určité požadavky.
  
  Důvodem je to, proč `navigator.onLine` rozhraní API prohlížeče není spolehlivé a nemělo by být závislé na.

* **Zajišťuje správnost.** Při vytváření mezipaměti offline prostředků používá pracovní proces služby k zajištění toho, aby získal úplný a samostatný snímek prostředků v jednom okamžitém čase. &ndash; Tato mezipaměť se pak použije jako atomická jednotka. Neexistuje žádný bod, který požaduje síť pro novější prostředky, protože jediné požadované verze jsou ty, které jsou už uložené v mezipaměti. Cokoli jiného nekonzistence rizik a nekompatibilita (například pokus o použití verzí sestavení .NET, která nebyla zkompilována dohromady).

### <a name="background-updates"></a>Aktualizace na pozadí

V rámci duševního modelu si můžete představit, jak se v režimu offline, tak jak se chová jako mobilní aplikace, které se dají nainstalovat. Aplikace se okamžitě spustí bez ohledu na připojení k síti, ale nainstalovaná logika aplikace pochází z snímku v čase, který nemusí být nejnovější verze.

Šablona Blazor PWA vytváří aplikace, které se automaticky pokusí o aktualizaci na pozadí, kdykoli uživatel navštíví a má fungující síťové připojení. Způsob funguje takto:

* Během kompilace projekt generuje *manifest assetů pracovního procesu služby*. Ve výchozím nastavení se nazývá *Service-Worker-assets. js*. Manifest obsahuje seznam všech statických prostředků, které aplikace vyžaduje, aby fungovala offline, například sestavení .NET, soubory JavaScriptu a CSS, včetně jejich hodnot hash obsahu. Seznam prostředků je načten pracovním procesem služby, aby věděl, které prostředky se mají ukládat do mezipaměti.
* Pokaždé, když uživatel navštíví aplikaci, prohlížeč znovu vyžádá *Service-Worker. js* a *Service-Worker-assets. js* na pozadí. Soubory jsou porovnány bajtů-pro bajt s existujícím pracovním procesem nainstalovaného služby. Pokud server vrátí změněný obsah některého z těchto souborů, pokusí se pracovní proces služby nainstalovat novou verzi.
* Při instalaci nové verze samotné pracovní proces služby vytvoří novou a samostatnou mezipaměť pro offline prostředky a začne naplnit mezipaměť prostředky uvedenými v *Service-Worker-assets. js*. Tato logika je implementována ve funkci `onInstall` v rámci *Service-Worker. Publish. js*.
* Proces se úspěšně dokončí, když se všechny prostředky načtou bez chyb a všechny hodnoty hash obsahu se shodují. V případě úspěchu nový pracovní proces služby vstoupí do stavu *čekání na aktivaci* . Jakmile uživatel aplikaci zavře (žádné zbývající karty aplikace nebo Windows), bude nový pracovní proces služby *aktivní* a použije se pro následné návštěvy aplikace. Původní pracovní proces služby a jeho mezipaměť se odstraní.
* Pokud se proces neúspěšně nedokončí, nová instance pracovního procesu služby se zahodí. V případě, že snad klient má lepší síťové připojení, které může dokončit požadavky, se proces aktualizace znovu pokusí o další návštěvu uživatele.

Tento proces si upravíte úpravou logiky pracovního procesu služby. Žádné z předchozích chování není specifické pro Blazor, ale je pouze výchozím prostředím poskytovaným možností šablony PWA. Další informace najdete v tématu [MDN web Docs: API Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).

### <a name="how-requests-are-resolved"></a>Jak se řeší požadavky

Jak je popsáno v části [strategie načítající mezipaměť](#cache-first-fetch-strategy) , výchozí pracovní proces služby používá strategii *cache-First* , což znamená, že se při dostupnosti pokusí o poskytování obsahu v mezipaměti. Pokud není obsah uložený v mezipaměti pro určitou adresu URL, například při vyžádání dat z back-endu API, pracovní proces služby se vrátí na běžný síťový požadavek. Pokud je server dostupný, požadavek na síť je úspěšný. Tato logika je implementována uvnitř funkce `onFetch` v rámci *Service-Worker. Publish. js*.

Pokud komponenty Razor aplikace spoléhají na požadavky na data z back-endové rozhraní API a chcete poskytnout uživatelsky přívětivé uživatelské prostředí pro neúspěšné požadavky z důvodu nedostupnosti sítě, implementujte logiku v rámci komponent aplikace. Můžete například použít `try/catch` kolem `HttpClient` požadavků.

### <a name="support-server-rendered-pages"></a>Server podpory – vykreslené stránky

Zvažte, co se stane, když uživatel poprvé přejde na adresu URL, například `/counter` nebo jakýkoli jiný přímý odkaz v aplikaci. V těchto případech nechcete vracet obsah uložený v mezipaměti jako `/counter`, ale místo toho musí prohlížeč načíst obsah uložený v mezipaměti jako `/index.html` pro spuštění aplikace Blazor WebAssembly. Tyto počáteční požadavky se označují jako požadavky na *navigaci* , a to na rozdíl od:

* požadavky na *podzdroje* pro obrázky, šablony stylů nebo jiné soubory.
* *načte/XHR* požadavky na data rozhraní API.

Výchozí pracovní proces služby obsahuje zvláštní případovou logiku pro požadavky na navigaci. Pracovník služby tyto požadavky vyřeší tak, že vrátí obsah uložený v mezipaměti pro `/index.html`bez ohledu na požadovanou adresu URL. Tato logika je implementována ve funkci `onFetch` v rámci *Service-Worker. Publish. js*.

Pokud má vaše aplikace určité adresy URL, které musí vracet Server HTML vykreslený serverem, a neobsluhuje `/index.html` z mezipaměti, pak je potřeba upravit logiku pracovního procesu služby. Pokud všechny adresy URL obsahující `/Identity/` musí být zpracovány jako běžné požadavky pouze online na server a pak upravit logiku `onFetch` *Service-Worker. Publish. js* . Vyhledejte následující kód:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Změňte kód následujícím způsobem:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

Pokud to neuděláte, nezávisle na připojení k síti zachytí pracovník služby požadavky na takové adresy URL a vyřeší je pomocí `/index.html`.

### <a name="control-asset-caching"></a>Ukládání prostředků do mezipaměti ovládacích prvků

Pokud projekt definuje vlastnost `ServiceWorkerAssetsManifest` MSBuild, nástroj pro sestavení Blazorvygeneruje manifest prostředků pracovního procesu služby se zadaným názvem. Výchozí šablona PWA vytvoří soubor projektu obsahující následující vlastnost:

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

Soubor je umístěn do výstupního adresáře *wwwroot* , takže prohlížeč může tento soubor načíst požadavkem `/service-worker-assets.js`. Chcete-li zobrazit obsah tohoto souboru, otevřete */bin/Debug/{Target Framework}/wwwroot/Service-Worker-assets.js* v textovém editoru. Neupravujte ale soubor, protože se znovu vygeneroval u každého sestavení.

Ve výchozím nastavení tento manifest uvádí:

* Všechny prostředky spravované Blazor, jako jsou například sestavení .NET a soubory modulu runtime .NET WebAssembly, jsou nutné pro funkci offline.
* Všechny prostředky pro publikování do adresáře *wwwroot* aplikace, jako jsou obrázky, šablony stylů a soubory JavaScriptu, včetně statických webových prostředků poskytovaných externími projekty a balíčky NuGet.

Úpravou logiky v `onInstall` v *Service-Worker. Publish. js*můžete řídit, které z těchto prostředků jsou načteny a ukládány do mezipaměti pracovním procesem služby. Ve výchozím nastavení pracovní proces služby načítá a ukládá do mezipaměti soubory, které odpovídají typickým příponám názvů webů, jako je například *. html*, *. CSS*, *. js*a *. wasm*, plus typy souborů specifické pro Blazor WebAssembly ( *. dll*, *. pdb*).

Chcete-li zahrnout další prostředky, které nejsou k dispozici v adresáři *wwwroot* aplikace, definujte další položky `ItemGroup` MSBuild, jak je znázorněno v následujícím příkladu:

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

Metadata `AssetUrl` určují základní relativní adresu URL, kterou by měl prohlížeč použít při načítání prostředku do mezipaměti. To může být nezávislé na původním názvu zdrojového souboru na disku.

> [!IMPORTANT]
> Přidání `ServiceWorkerAssetsManifestItem` nezpůsobí publikování souboru v adresáři *wwwroot* aplikace. Výstup publikování musí být řízen samostatně. `ServiceWorkerAssetsManifestItem` způsobí, že se v manifestu Asset Service Worker zobrazí další položka.

## <a name="push-notifications"></a>Nabízená oznámení

Stejně jako jakékoli jiné PWA může Blazor WebAssembly PWA přijímat nabízená oznámení ze serveru back-end. Server může odesílat nabízená oznámení kdykoli, i když uživatel tuto aplikaci aktivně nepoužívá. Nabízená oznámení lze například odeslat, když jiný uživatel provede příslušnou akci.

Mechanismus pro odeslání nabízeného oznámení je zcela nezávislý na Blazor WebAssembly, protože je implementovaný back-end serverem, který může používat libovolnou technologii. Pokud chcete zasílat nabízená oznámení ze serveru ASP.NET Core, zvažte [použití techniky, která je podobná metodě pořízené v neuvěřitelně Pizza Workshop](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications).

Mechanismus pro příjem a zobrazování nabízených oznámení na straně klienta je také nezávislý na Blazorm WebAssembly, protože je implementován v souboru JavaScriptu pracovníka služby. Příklad najdete v tématu [přístup k použití v neuvěřitelně Pizza Workshop](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).

## <a name="caveats-for-offline-pwas"></a>Upozornění pro offline PWAs

Ne všechny aplikace by se měly pokusit o podporu offline použití. Podpora offline přináší značnou složitost, ale ne vždycky důležitá pro případy použití, které se vyžadují.

Podpora offline je obvykle relevantní pouze:

* Pokud je primární úložiště dat místní pro prohlížeč. Například přístup je relevantní v aplikaci s uživatelským rozhraním pro zařízení [IoT](https://en.wikipedia.org/wiki/Internet_of_things) , které ukládá data v `localStorage` nebo [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).
* Pokud aplikace provede značnou práci, která načte a uloží data rozhraní API back-endu relevantní pro každého uživatele, aby mohli přecházet mezi daty v režimu offline. Pokud je nutné, aby aplikace podporovala úpravy, je nutné sestavit systém pro sledování změn a synchronizaci dat s back-endu.
* Pokud cílem je zaručit, že se aplikace načte hned bez ohledu na stav sítě. Implementujte vhodné uživatelské prostředí kolem požadavků back-end rozhraní API, abyste zobrazili průběh požadavků a pracovali se řádným, když požadavky selžou kvůli nedostupnosti sítě.

Kromě toho musí PWAs s podporou offline řešit řadu dalších komplikací. Vývojáři by se měli pečlivě seznámit s upozorněními v následujících oddílech.

### <a name="offline-support-only-when-published"></a>Podpora offline pouze v případě publikování

Během vývoje obvykle chcete zobrazit každou změnu, která se odrazí v prohlížeči, aniž byste museli procházet proces aktualizace na pozadí. Proto šablona BlazorPWA povolí podporu offline jenom v případě, že je publikovaná.

Při sestavování aplikace podporující offline není dostatečné testování aplikace ve vývojovém prostředí. Aby bylo možné pochopit, jak reaguje na různé síťové podmínky, musíte aplikaci otestovat v jejím publikovaném stavu.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Dokončení aktualizace po navigaci uživatele z aplikace

Aktualizace se nedokončí, dokud uživatel nepřejde z aplikace na všech kartách. Jak je vysvětleno v části [aktualizace na pozadí](#background-updates) , po nasazení aktualizace do aplikace Prohlížeč načte aktualizované pracovní soubory služby pro zahájení procesu aktualizace.

Co překvapením mnoho vývojářů, i když se tato aktualizace dokončí, **neprojeví** se, dokud uživatel nepřejde na všechny karty. **Není dostačující aktualizovat** kartu, která aplikaci zobrazuje, i v případě, že se jedná o jedinou kartu, na které se aplikace zobrazuje. Dokud nebude vaše aplikace zcela uzavřená, nový pracovní proces služby zůstane ve stavu *čekání na aktivaci* . **Nejedná se o konkrétní Blazor, ale spíše je standardní chování webové platformy.**

To běžně vydává problémy vývojářům, kteří se pokoušejí testovat aktualizace pro pracovní pracovníky služby nebo offline prostředky v mezipaměti. Pokud se do vývojářských nástrojů v prohlížeči pokusíte vrátit se změnami, může se zobrazit něco jako v následujících případech:

![Karta aplikace Google Chrome zobrazuje, že pracovní proces služby aplikace čeká na aktivaci.](progressive-web-app/_static/image7.png)

Pokud seznam "klienti", které jsou na kartách nebo ve Windows, který zobrazuje vaši aplikaci, není prázdný, pracovní proces pokračuje v čekání. Důvodem je, že pracovníci služby mají zaručenou konzistenci. Konzistence znamená, že všechny prostředky jsou načteny ze stejné atomické mezipaměti.

Při testování se může stát, že kliknete na odkaz "skipWaiting", jak je znázorněno na předchozím snímku obrazovky, a pak znovu načíst stránku. Tuto akci můžete automatizovat pro všechny uživatele pomocí kódování pracovního procesu služby, který [přeskočí fázi čekání a okamžitě aktivuje při aktualizaci](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase). Pokud přeskočíte čekací fázi, zajistíte tím záruku, že se prostředky vždy načítají konzistentně ze stejné instance mezipaměti.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Uživatelé můžou spouštět všechny historické verze aplikace.

Vývojáři na webu většinou očekávají, že uživatelé spouštějí jenom nejnovější nasazenou verzi své webové aplikace, protože to je v tradičním modelu pro distribuci webů normální. Ale offline první aplikace PWA je podobají na nativní mobilní aplikaci, kde uživatelé nemusí nutně používat nejnovější verzi.

Jak je vysvětleno v části [aktualizace na pozadí](#background-updates) , poté, co nasadíte aktualizaci do vaší aplikace, **bude mít každý stávající uživatel nadále k dispozici předchozí verzi pro alespoň jednu další návštěvu** , protože tato aktualizace probíhá na pozadí a není aktivována, dokud uživatel nepřejde pryč. Předchozí používaná verze navíc nemusí nutně odpovídat předchozí verzi, kterou jste nasadili. Předchozí verze může být *libovolná* historická verze v závislosti na tom, kdy uživatel poslední aktualizaci dokončil.

To může být problém, pokud části front-end a back-end aplikace v aplikaci vyžadují smlouvu o schématu pro požadavky rozhraní API. Nekompatibilní změny schématu rozhraní API není nutné nasazovat, dokud nebudete mít jistotu, že všichni uživatelé byli upgradováni. Případně můžete uživatelům zabránit v používání nekompatibilních starších verzí aplikace. Tento požadavek na scénář je stejný jako u nativních mobilních aplikací. Pokud nasadíte zásadní změnu v rozhraních API serveru, klientská aplikace se pro uživatele, kteří ještě neaktualizovali, přeruší.

Pokud je to možné, nesaďte nenasazené změny rozhraní API back-endu. Pokud to musíte udělat, zvažte použití [rozhraní API pro standardní službu Service Worker](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) , jako je například ServiceWorkerRegistration, k určení, jestli je aplikace aktuální, a pokud ne, abyste zabránili použití.

### <a name="interference-with-server-rendered-pages"></a>Rušení se stránkami vykreslenými serverem

Jak je popsáno v části [Server podpory – vykreslené stránky](#support-server-rendered-pages) , pokud chcete obejít chování pracovního procesu pro vracení `/index.html` obsahu pro všechny požadavky na navigaci, upravte logiku pracovního procesu služby.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Ve výchozím nastavení se veškerý obsah manifestu Asset Service Worker ukládá do mezipaměti.

Jak je popsáno v části [ukládání prostředků do mezipaměti](#control-asset-caching) , je soubor *Service-Worker-assets. js* vygenerovaný během sestavování a obsahuje seznam všech prostředků, které by pracovní proces služby měl načíst a Uložit do mezipaměti.

Vzhledem k tomu, že tento seznam ve výchozím nastavení zahrnuje vše generované do *wwwroot*, včetně obsahu dodávaného externími balíčky a projekty, je nutné, abyste měli pozor, abyste do něj neumístili příliš mnoho obsahu. Pokud adresář *wwwroot* obsahuje miliony imagí, pracovní podproces služby se pokusí je načíst a Uložit do mezipaměti, což spotřebovává nadměrné šířky pásma a pravděpodobně se neúspěšně dokončuje.

Implementujte libovolnou logiku pro řízení, která podmnožina obsahu manifestu by měla být načtena a ukládána do mezipaměti úpravou funkce `onInstall` v *Service-Worker. Publish. js*.

### <a name="interaction-with-authentication"></a>Interakce s ověřováním

V kombinaci s možnostmi ověřování je možné použít šablonu PWA. Aplikace PWA podporující offline může také podporovat ověřování, když má uživatel připojení k síti.

Pokud uživatel nemá připojení k síti, nemůže ověřit ani získat přístupové tokeny. Ve výchozím nastavení se při pokusu o návštěvě přihlašovací stránky bez přístupu k síti zobrazí zpráva "Chyba sítě".

Je nutné navrhnout tok uživatelského rozhraní, který uživateli umožňuje provádět užitečné akce, pokud se nepokusí ověřit nebo získat přístupové tokeny. Alternativně můžete aplikaci navrhnout tak, aby nebyla úspěšná, pokud síť není k dispozici. Pokud to není u vaší aplikace možné, možná nebudete chtít povolit podporu offline.
