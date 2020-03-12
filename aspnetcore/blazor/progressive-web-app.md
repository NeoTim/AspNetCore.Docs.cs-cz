---
title: Sestavování progresivních webových aplikací pomocí ASP.NET Core Blazor webovém sestavení
author: guardrex
description: Naučte se vytvářet progresivní webové aplikace (PWAs) založené na Blazor, webové aplikace, které používají moderní funkce prohlížeče, se chovají jako desktopové aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: blazor/progressive-web-app
ms.openlocfilehash: f1c1ce50f20bf579e67e1d4eb02cc7d9d681e354
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083718"
---
# <a name="build-progressive-web-applications-with-aspnet-core-opno-locblazor-webassembly"></a>Sestavování progresivních webových aplikací pomocí ASP.NET Core Blazor webovém sestavení

Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Progresivní webová aplikace (PWA) je webová aplikace, která používá moderní rozhraní API a možnosti prohlížeče, aby se choval jako desktopová aplikace. Tyto možnosti mohou zahrnovat:

* Práce v režimu offline a okamžité načítání vždy nezávisle na rychlosti sítě
* Může běžet ve vlastním okně aplikace, ne jenom v okně prohlížeče.
* Spouští se z nabídky Start (OS) hostitelského operačního systému, ukotvení nebo domovské obrazovky.
* Příjem nabízených oznámení ze serveru back-end, a to i v případě, že uživatel aplikaci nepoužívá
* Automatické aktualizace na pozadí

Uživatel může nejdříve vyhledat a použít aplikaci v rámci webového prohlížeče, jako je jakákoli jiná jednostránková aplikace (SPA), a potom později postup nainstalovat do svého operačního systému a povolit nabízená oznámení. Proto používáme termín *progresivní*.

Blazor WebAssembly je skutečnou platformou webové aplikace na straně klienta, takže může používat libovolné rozhraní API prohlížeče, včetně rozhraní API PWA potřebných pro výše uvedené možnosti. Může pracovat offline stejně jako jakékoli jiné webové technologie na straně klienta.

## <a name="pwa-template"></a>Šablona PWA

Při vytváření nové aplikace Blazor WebAssembly vám nabídnete možnost Přidat funkce PWA. V aplikaci Visual Studio je možnost uvedena jako zaškrtávací políčko v dialogovém okně pro vytvoření projektu:

![image](https://user-images.githubusercontent.com/1101362/76207411-a6b54200-61f5-11ea-9dfc-6acd87a91428.png)

Pokud vytváříte projekt na příkazovém řádku, můžete použít příznak `--pwa`. Například:

```dotnetcli
dotnet new blazorwasm --pwa -o MyNewProject
```

V obou případech je můžete kombinovat s možností "ASP.NET Core Hosted", pokud chcete, ale nemusíte to dělat. Funkce PWA jsou nezávislé na modelu hostování.

## <a name="installation-and-app-manifest"></a>Instalace a manifest aplikace

Když se navštíví aplikace vytvořená pomocí možnosti šablony PWA, uživatelé mají možnost nainstalovat aplikaci do nabídky Start, Dock nebo Home v operačním systému.

Způsob, jakým je tato možnost uvedená, závisí na prohlížeči uživatele. Například při použití prohlížečů založených na desktopových Chromech, jako jsou Edge nebo Chrome, se na panelu Adresa URL zobrazí tlačítko *Přidat* :

![image](https://user-images.githubusercontent.com/1101362/76208127-f7796a80-61f6-11ea-8aea-7fba704be787.png)

V systému iOS můžou Návštěvníci nainstalovat aplikaci PWA pomocí tlačítka pro *sdílení* v prohlížeči Safari a její možnosti *Přidat do homescreen* . V Chrome pro Android by uživatelé měli klepnout na tlačítko *nabídky* v pravém horním rohu a pak zvolit *Přidat na domovskou obrazovku*.

Po instalaci se aplikace zobrazí ve vlastním okně bez jakéhokoli panelu Adresa.

![image](https://user-images.githubusercontent.com/1101362/76208588-e2e9a200-61f7-11ea-85e1-8c3fc849b252.png)

Chcete-li přizpůsobit název okna, barevné schéma, ikonu nebo jiné podrobnosti, přečtěte si soubor `manifest.json` v adresáři *wwwroot* vašeho projektu. Schéma tohoto souboru je definováno webovými standardy. Podrobnou dokumentaci najdete v tématu https://developer.mozilla.org/docs/Web/Manifest.

## <a name="offline-support"></a>Podpora offline

Ve výchozím nastavení mají aplikace vytvořené pomocí možnosti šablony PWA podporu pro spuštění offline. Uživatel musí nejdřív aplikaci navštívit, pokud jsou online, a pak bude automaticky stahovat a ukládat do mezipaměti všechny prostředky potřebné k provozu offline.

> [!IMPORTANT]
> Podpora offline je povolená jenom pro *publikované* aplikace. Během vývoje není povolený. Důvodem je to, že by došlo ke konfliktu s obvyklým vývojovým cyklem provádění změn a jejich testování.

> [!WARNING]
> Pokud máte v úmyslu dodávat offline s povolenou aplikaci PWA, je k dispozici [několik důležitých upozornění a aspektů](#caveats-for-offline-pwas) , které je potřeba pochopit. Jsou podstatou pro offline PWAs a nespecifická pro Blazor. Nezapomeňte si tyto upozornění přečíst a pochopit před tím, než začnete vytvářet předpoklady pro práci offline aplikace.

Pokud chcete zjistit, jak funguje podpora offline, nejprve [publikujte aplikaci](https://docs.microsoft.com/aspnet/core/host-and-deploy/blazor/?view=aspnetcore-3.1&tabs=visual-studio#publish-the-app)a hostovat ji na serveru podporujícím protokol HTTPS. Při návštěvě aplikace byste měli být schopni otevřít vývojové nástroje v prohlížeči a ověřit, zda je *pracovní proces služby* zaregistrován pro vašeho hostitele:

![image](https://user-images.githubusercontent.com/1101362/76210294-bd5e9780-61fb-11ea-9869-65c55c62803d.png)

Pokud navíc znovu načtete stránku, pak na kartě *síť* byste měli vidět, že všechny prostředky potřebné k načtení stránky se načítají z *pracovníka služby* nebo *mezipaměti paměti*:

![image](https://user-images.githubusercontent.com/1101362/76210472-1d553e00-61fc-11ea-84c6-291644df709e.png)

To ukazuje, že prohlížeč není závislý na síťovém přístupu pro načtení vaší aplikace. To můžete ověřit buď tak, že vypnete webový server, nebo nastavíte, aby prohlížeč simuloval offline režim:

![image](https://user-images.githubusercontent.com/1101362/76210556-47a6fb80-61fc-11ea-9d12-20a8f6528744.png)

Teď, i když nemáte přístup k webovému serveru, byste měli být schopni stránku znovu načíst a zjistit, že se vaše aplikace pořád načítá a běží. Podobně i v případě, že simulaci velmi pomalé síťové připojení, bude stránka stále načtena ihned, protože je načítána nezávisle na síti.

### <a name="service-worker"></a>Pracovní proces služby

Podpora offline se dosahuje pomocí pracovního procesu služby. Toto je webový standard, který není specifický pro Blazor. Dokumentaci k pracovníkům služeb najdete v tématu https://developer.mozilla.org/docs/Web/API/Service_Worker_API. Další informace o běžných vzorech použití pro pracovní procesy najdete v skvělém článku životní [cyklus pracovního procesu služby](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).

Šablona aplikace BlazorPWA vytvoří dva pracovní pracovní soubory služby:

* *wwwroot/Service-Worker. js*, který se používá během vývoje
* *wwwroot/Service-Worker. Publikováno. js*, který se používá po publikování vaší aplikace

Pokud chcete sdílet logiku mezi těmito dvěma soubory, zvažte přidání třetího souboru JavaScriptu pro uložení běžné logiky a použití [`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) k načtení této logiky do obou souborů.

#### <a name="cache-first-fetch-strategy"></a>Strategie prvního načtení mezipaměti

Vestavěný pracovní proces služby *Service-Worker. Publish. js* řeší požadavky pomocí strategie *mezipaměti-First* . To znamená, že vždy raději vrátí obsah uložený v mezipaměti, pokud je k dispozici, bez ohledu na to, zda má uživatel přístup k síti nebo zda je na serveru k dispozici novější obsah.

Existují dva důvody, proč je to užitečné:

* **Zajišťuje spolehlivost.** Přístup k síti není logickým stavem. Uživatel není jednoduše online nebo offline. Ve skutečnosti se zařízení uživatele může domnívat, že je online, ale síť může být tak pomalá, aby bylo možné počkat na. Nebo může být u některých adres URL neplatných výsledků, například v případě, že je k dispozici nějaký portál Wi-Fi, který aktuálně blokuje nebo přesměrovává určité požadavky. Důvodem je to, proč `navigator.onLine` rozhraní API prohlížeče není spolehlivé a nemělo by být závislé na.
* **Zajišťuje správnost.** Při sestavování mezipaměti offline prostředků používá pracovník služby k zajištění toho, aby získal úplný a vysoce konzistentní snímek prostředků v jednom čase. Tato mezipaměť se pak použije jako atomická jednotka. V takovém případě neexistuje žádný bod, který žádá o síť pro novější prostředky, protože jediné požadované verze jsou ty, které jste už uložili do mezipaměti. Cokoli jiného rizika nekonzistence a nekompatibilita (například pokus o použití verzí sestavení .NET, která nebyla zkompilována dohromady).

#### <a name="background-updates"></a>Aktualizace na pozadí

V rámci duševního modelu si můžete představit jako v případě aplikace PWA offline, jako je například mobilní aplikace, kterou lze nainstalovat. Vždy se spustí okamžitě bez ohledu na připojení k síti, ale nainstalovaná logika aplikace pochází z snímku v čase, který nemusí být nejnovější verze.

Šablona Blazor PWA vytváří aplikace, které se automaticky pokusí o aktualizaci na pozadí, kdykoli uživatel navštíví a má fungující síťové připojení. Způsob funguje takto:

* Během kompilace projekt vygeneruje *manifest assetů pracovního procesu služby*. Ve výchozím nastavení se tento název nazývá *Service-Worker-assets. js*. Zobrazí se seznam všech statických prostředků, které vaše aplikace potřebuje pro funkci offline, jako jsou například sestavení .NET, soubory JavaScriptu, šablony stylů CSS atd., včetně jejich hodnot hash obsahu. Tento seznam je načten pracovním procesem služby, aby věděl, které prostředky se mají ukládat do mezipaměti.
* Pokaždé, když uživatel navštíví vaši aplikaci, prohlížeč znovu vyžádá *Service-Worker. js* a *Service-Worker-assets. js* na pozadí. Pokud server vrátí změněný obsah pro některý z těchto souborů (v porovnání s existujícím nainstalovaným pracovním procesem služby), pracovník služby se pokusí nainstalovat novou verzi.
* Při instalaci nové verze samotné pracovní proces služby vytvoří novou a samostatnou mezipaměť pro offline prostředky a začne ji naplnit prostředky uvedenými v *Service-Worker-assets. js*. Tato logika je implementována ve funkci `onInstall` v rámci *Service-Worker. Publish. js*.
* Pokud se proces úspěšně dokončí (tj. všechny prostředky se načtou bez chyb a všechny hodnoty hash obsahu se shodují), nový pracovní proces služby vstoupí do stavu "čekání na aktivaci". Jakmile uživatel aplikaci zavře (tj. neexistují žádné zbývající karty ani zobrazení oken vaší aplikace), bude nový pracovní proces služby "aktivní" a bude použit pro následné návštěvy aplikace. Původní pracovní proces služby a jeho mezipaměť se odstraní.
* Pokud se proces neúspěšně nedokončí, nová instance pracovního procesu služby se zahodí. V případě, že bude snad mít lepší síťové připojení, které může dokončit požadavky, se proces aktualizace znovu pokusí o další návštěvu uživatele.

Libovolný aspekt tohoto procesu můžete přizpůsobit úpravou logiky pracovního procesu služby. Žádná z výše uvedených možností není specifická pro Blazor, ale je pouze návrhem, který poskytuje možnost šablony PWA. Další informace najdete v [dokumentaci k pracovnímu pracovníkovi služby](https://developer.mozilla.org/docs/Web/API/Service_Worker_API.) .

#### <a name="how-requests-are-resolved"></a>Jak se řeší požadavky

Jak je popsáno výše, používá výchozí pracovní proces služby strategii *mezipaměti* , což znamená, že se při dostupnosti pokusí o poskytování obsahu v mezipaměti. Pokud není obsah uložený v mezipaměti pro určitou adresu URL, například při vyžádání dat z back-endu API, pracovní proces služby se vrátí na běžný požadavek sítě, který může být úspěšný jenom v případě, že je server dostupný. Tato logika je implementována uvnitř `onFetch` v rámci *Service-Worker. Publish. js*.

Pokud vaše Blazor komponenty spoléhají na požadavky na data z back-endové rozhraní API a chcete poskytnout uživatelsky přívětivé uživatelské prostředí v případě, kdy tyto požadavky selžou kvůli nedostupnosti sítě, je nutné implementovat logiku v rámci svých komponent. Můžete například použít `try/catch` kolem `HttpClient` požadavků.

#### <a name="support-server-rendered-pages"></a>Server podpory – vykreslené stránky

Zvažte, co se stane, když uživatel poprvé přejde na adresu URL, například `/counter` nebo jakýkoli přímý odkaz na aplikaci. V těchto případech nechcete vracet obsah uložený v mezipaměti jako `/counter`, ale místo toho musí prohlížeč načíst obsah uložený v mezipaměti jako `/index.html` pro spuštění aplikace Blazor WebAssembly. Tyto počáteční požadavky se označují jako požadavky na *navigaci* (na rozdíl od požadavků na *subprostředky* pro Image/CSS/atd.), nebo *načte/XHR* požadavky na data rozhraní API.

Výchozí pracovní proces služby obsahuje zvláštní případovou logiku pro požadavky na navigaci. Vyřeší je tak, že vrátí obsah uložený v mezipaměti pro `/index.html`bez ohledu na požadovanou adresu URL. Tato logika je implementována ve funkci `onFetch` v rámci *Service-Worker. Publish. js*.

Pokud má vaše aplikace určité adresy URL, které musí vracet Server HTML vykreslený serverem (a neobsluhuje `/index.html` z mezipaměti), pak je potřeba upravit logiku pracovního procesu služby. Například pokud všechny adresy URL obsahující `/Identity/` musí být zpracovány jako běžné požadavky pouze online na server a pak upravit logiku `onFetch` *Service-Worker. Publish. js* . Vyhledejte následující kód:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Změňte kód následujícím způsobem:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

Pokud to neuděláte, pracovní proces služby bude zachytávat požadavky na takové adresy URL a bude je řešit pomocí `/index.html`.

#### <a name="control-asset-caching"></a>Ukládání prostředků do mezipaměti ovládacích prvků

Pokud váš projekt definuje vlastnost MSBuild s názvem `ServiceWorkerAssetsManifest`, pak Nástroj pro sestavení Blazorvygeneruje manifest prostředků pracovního procesu služby se zadaným názvem. Výchozí šablona PWA vytvoří soubor projektu, který obsahuje následující:

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

Soubor je umístěn do výstupního adresáře *wwwroot* , takže prohlížeč může tento soubor načíst požadavkem `/service-worker-assets.js`. Chcete-li zobrazit obsah, otevřete *YourProject\bin\Debug\netstandard2.1\wwwroot\service-Worker-assets.js* v textovém editoru. Neupravujte ale soubor, protože se znovu vygeneruje u každého sestavení.

Ve výchozím nastavení tento manifest uvádí:

* Všechny prostředky spravované Blazor, jako jsou sestavení .NET, a soubory runtime .NET WebAssembly, které jsou potřeba pro funkci offline
* Všechny prostředky, které budou publikovány v adresáři *wwwroot* , například obrázky, soubory CSS a soubory JavaScriptu. To zahrnuje statické webové prostředky poskytované externími projekty a balíčky NuGet.

Úpravou logiky v `onInstall` v *Service-Worker. Publish. js*můžete řídit, které z těchto prostředků budou načteny a uloženy do mezipaměti pracovním procesem služby. Ve výchozím nastavení načte a zapíše soubory do mezipaměti, které odpovídají typickým příponám názvů webů, jako je například *. html*, *. CSS*, *. js*, *. wasm*a další, a navíc typy souborů specifické pro Blazor WebAssembly ( *. dll*, *. pdb*).

Pokud chcete zahrnout další prostředky, které nejsou k dispozici v adresáři *wwwroot* , můžete to provést tak, že definujete další položky položek MSBuild. Například v souboru projektu přidejte:

```xml
<ItemGroup>
    <ServiceWorkerAssetsManifestItem
        Include="MyDirectory\AnotherFile.json"
        RelativePath="MyDirectory\AnotherFile.json"
        AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

Metadata `AssetUrl` určují základní relativní adresu URL, kterou by měl prohlížeč použít při načítání prostředku do mezipaměti. To může být nezávislé na původním názvu zdrojového souboru na disku.

> [!IMPORTANT]
> Přidání `ServiceWorkerAssetsManifestItem` nezpůsobí publikování souboru v adresáři *wwwroot* . Můžete si také řídit výstup publikování samostatně. `ServiceWorkerAssetsManifestItem` způsobí, že se v manifestu Asset Service Worker zobrazí další položka.

## <a name="push-notifications"></a>Nabízená oznámení

Stejně jako jakékoli jiné PWA může Blazor WebAssembly PWA přijímat nabízená oznámení ze serveru back-end. Server je může kdykoli odeslat, i když uživatel aktivně nepoužívá vaši aplikaci (například když jiný uživatel provede akci, která může být relevantní).

Mechanismus pro odeslání nabízeného oznámení je zcela nezávislý na Blazor WebAssembly, protože je implementovaný back-end serverem, který může používat libovolnou technologii. Pokud chcete zasílat nabízená oznámení ze serveru ASP.NET Core, zvažte [použití techniky podobné tomu na neuvěřitelně Pizza Workshop](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications).

Mechanismus pro příjem a zobrazování nabízených oznámení na straně klienta je také nezávislý na Blazor WebAssembly, protože je implementována v pracovním procesu služby, což je soubor JavaScriptu. Jako příklad můžete znovu zobrazit [přístup k použití v neuvěřitelně Pizza Workshop](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).

## <a name="caveats-for-offline-pwas"></a>Upozornění pro offline PWAs

Ne všechny aplikace by se měly pokusit o podporu offline použití. Přináší značnou složitost, ale ne vždy důležité.

Podpora offline je obvykle relevantní pouze:

* Pokud je vaše primární úložiště dat místní pro prohlížeč. Například při sestavování uživatelského rozhraní pro zařízení [IoT](https://en.wikipedia.org/wiki/Internet_of_things) , které ukládá data v `localStorage` nebo [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).

* Pokud budete chtít načíst a uložit data rozhraní API back-endu relevantní pro každého uživatele, můžete k tomu přejít v režimu offline. Pokud podporujete úpravy, budete také muset vytvořit systém pro sledování změn a synchronizaci s back-endu.

* Pokud vaším cílem je zaručit, že se aplikace načte hned bez ohledu na stav sítě. Pak budete muset implementovat vhodné uživatelské prostředí kolem požadavků rozhraní API back-endu, aby se zobrazil průběh požadavků a plynulé chování, když dojde k selhání kvůli nedostupnosti sítě.

Kromě toho musí PWAs s podporou offline řešit řadu mimořádných komplikací. Vývojáři by se měli pečlivě seznámit s následujícími upozorněními.

### <a name="offline-support-only-when-published"></a>Podpora offline pouze v případě publikování

Šablona aplikace PWA Blazorpovoluje podporu offline pouze v případě, že byla publikována. Důvodem je, že během vývoje obvykle chcete zobrazit každou změnu, která se projeví okamžitě v prohlížeči, aniž by procházela proces aktualizace na pozadí.

Proto při sestavování aplikace podporující offline není dostačující testovat aplikaci v režimu vývoje. Chcete-li pochopit, jak bude reagovat na různé podmínky v síti, musíte aplikaci otestovat v jejím publikovaném stavu.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Dokončení aktualizace po navigaci uživatele z aplikace

Aktualizace se nedokončí, dokud uživatel nepřejde z vaší aplikace na všech kartách. Jak je vysvětleno v části [aktualizace na pozadí](#background-updates), po nasazení aktualizace do aplikace Prohlížeč načte aktualizované pracovní procesy služby a zahájí proces aktualizace.

Co překvapením mnoho vývojářů, i když se tato aktualizace dokončí, **neprojeví** se, dokud uživatel nepřejde na všechny karty. **Není dostačující aktualizovat** kartu zobrazující vaši aplikaci, a to i v případě, že se jedná o jedinou kartu, na které se aplikace zobrazuje. Dokud nebude vaše aplikace kompletně uzavřená, nový pracovní proces služby zůstane ve stavu "čekání na aktivaci". **Nejedná se o konkrétní Blazor, ale spíše je standardní chování webové platformy.**

To běžně vydává problémy vývojářům, kteří se pokoušejí testovat aktualizace pro pracovní pracovníky služby nebo offline prostředky v mezipaměti. Pokud se ve vývojovém nástroji v prohlížeči vraťte zpět, může se zobrazit například následující text:

![image](https://user-images.githubusercontent.com/1101362/76226394-b93f7380-6215-11ea-8572-7d52afee2dd8.png)

Pokud je seznam "klienti" (tj. karty nebo okna, ve kterém se aplikace zobrazuje) neprázdný, bude pracovní proces nadále čekat. Důvodem je zaručit konzistenci, tj., že všechny prostředky jsou načteny ze stejné atomické mezipaměti.

Při testování se může stát, že kliknete na odkaz "skipWaiting", jak je znázorněno na snímku obrazovky výše, a pak znovu načíst stránku. Pokud chcete, můžete to automatizovat pro všechny uživatele tak, že zakódujete pracovní proces služby, který [přeskočí fázi čekání a hned se aktivuje při aktualizaci](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase). Pokud to ale uděláte, budete mít jistotu, že se prostředky vždycky načítají konzistentně ze stejné instance mezipaměti.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Uživatelé můžou spouštět všechny historické verze aplikace.

Vývojáři webu obvykle očekávají, že uživatelé budou spouštět jenom nejnovější nasazené verze svých webových aplikací, protože to je obvyklé v tradičním modelu webové distribuce. Offline první aplikace PWA je ale více podobají nativní mobilní aplikaci, kde uživatelé nemusí nutně používat nejnovější verzi.

Jak je vysvětleno v části [aktualizace na pozadí](#background-updates), poté, co nasadíte aktualizaci do vaší aplikace, **bude mít každý existující uživatel i nadále k dispozici předchozí verzi pro alespoň jednu další návštěvu** (protože tato aktualizace probíhá na pozadí a není aktivována, dokud uživatel nepřejde). Předchozí používaná verze navíc nemusí nutně nasazovat předchozí verzi – může to být *jakákoli* historická verze v závislosti na tom, kdy uživatel poslední dokončení aktualizace dokončil.

To může být problém, pokud části front-end a back-end aplikace v aplikaci vyžadují smlouvu o schématu pro požadavky rozhraní API. Nekompatibilní změny schématu rozhraní API není nutné nasazovat, dokud nebudete mít jistotu, že všichni uživatelé provedli upgrade, nebo alespoň zablokovat uživatelům možnost používat nekompatibilní starší verze aplikace. To je stejně jako v případě nativní mobilní aplikace. Pokud nasadíte zásadní změnu v rozhraních API serveru, klientská aplikace se bude rušit pro uživatele, kteří se ještě neaktualizovali.

Pokud je to možné, nesaďte nenasazené změny rozhraní API back-endu. Pokud to ale musíte udělat, zvažte použití [rozhraní API pro standardní službu Service Worker, například `ServiceWorkerRegistration`](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) , k určení, jestli je aplikace aktuální, a pokud ne, abyste zabránili použití.

### <a name="interference-with-server-rendered-pages"></a>Rušení se stránkami vykreslenými serverem

[Jak je popsáno výše](#support-server-rendered-pages), pokud chcete obejít chování služby Service Worker při vracení `/index.html` obsahu pro všechny požadavky na navigaci, musíte upravit logiku pracovního procesu služby.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Ve výchozím nastavení se veškerý obsah manifestu Asset Service Worker ukládá do mezipaměti.

[Jak je popsáno výše](#control-asset-caching), soubor *Service-Worker-assets. js* se vygeneruje během sestavování a zobrazí všechny prostředky, které by pracovní proces služby měl načíst a Uložit do mezipaměti.

Vzhledem k tomu, že tento seznam ve výchozím nastavení zahrnuje všechno emitované do *wwwroot* (včetně obsahu dodaných externími balíčky a projekty), je nutné, abyste měli pozor, abyste do něj neumístili příliš mnoho obsahu. Pokud například adresář *wwwroot* obsahuje miliony imagí, pracovník služby by se pokusil načíst a uložit je do mezipaměti, což spotřebovává nadměrné šířky pásma a pravděpodobně se neúspěšně dokončuje.

Můžete implementovat libovolnou logiku pro řízení, které podmnožiny obsahu manifestu by měly být načteny a ukládány do mezipaměti úpravou funkce `onInstall` v *Service-Worker. Publish. js*.

### <a name="interaction-with-authentication"></a>Interakce s ověřováním

V kombinaci s možnostmi ověřování je možné použít šablonu PWA. Aplikace PWA podporující offline může také podporovat ověřování, když má uživatel připojení k síti.

Pokud však uživatel nemá připojení k síti, nebude moci ověřit ani získat přístupové tokeny. Při pokusu o návštěvě přihlašovací stránky se ve výchozím nastavení zobrazí zpráva oznamující "Chyba sítě".

V takovém případě je to vaše úloha navržení toku uživatelského rozhraní, který uživateli umožňuje pracovat v režimu offline bez nutnosti ověřit nebo získat přístupové tokeny nebo nejméně v těchto případech v případě bezproblémového fungování. Pokud to není u vaší aplikace možné, možná nebudete chtít povolit podporu offline.
