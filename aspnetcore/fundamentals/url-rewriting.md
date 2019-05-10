---
title: Middleware v ASP.NET Core přepisování adres URL
author: guardrex
description: Další informace o adrese URL přepsání a přesměrování s Middleware pro přepis adres URL v aplikacích ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: fundamentals/url-rewriting
ms.openlocfilehash: 72d5b2e902a95442ccffb7a149b917c50373775b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902202"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>Middleware v ASP.NET Core přepisování adres URL

Podle [Luke Latham](https://github.com/guardrex) a [Mikael Mengistu](https://github.com/mikaelm12)

::: moniker range="<= aspnetcore-1.1"

1.1 verzi tohoto tématu, stáhněte si [Middleware pro přepis adres URL v ASP.NET Core (verze 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/URL_Rewriting_1.1.pdf).

::: moniker-end

Toto téma představuje přepisování adres URL s pokyny, jak používat Middleware pro přepis adres URL v aplikacích ASP.NET Core.

Přepisování adres URL je úprava žádosti o adresy URL v závislosti na jeden nebo více předdefinovaných pravidel. Přepisování adres URL vytvoří abstrakci mezi umístění prostředků a jejich adresy tak, aby umístění a adresy nejsou připojeny úzce. Přepisování adres URL je užitečné v několika scénářích pro:

* Přesuňte nebo nahradit server zdroje dočasně nebo trvale a udržovat stabilní lokátory pro tyto prostředky.
* Rozdělte zpracování požadavků v různých aplikací nebo víc oblastech jednu aplikaci.
* Odebrat, přidat nebo změnit uspořádání segmenty adres URL na příchozí požadavky.
* Optimalizujte veřejné adresy URL pro hledání modulu optimalizace (SEO).
* Povolit používání popisný veřejné adresy URL usnadnit návštěvníkům předpovědět, můžete si vyžádat prostředek vrácený obsah.
* Přesměrujte nezabezpečené požadavky do zabezpečené koncové body.
* Zabraňte hotlinking, kde externí web používá prostředí statické prostředku v jiné lokalitě propojením prostředku do jeho vlastní obsah.

> [!NOTE]
> Přepisování adres URL, může snížit výkon aplikace. Tam, kde je to možné, omezte počet a složitost pravidel.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([stažení](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>Adresa URL pro přesměrování a URL revize

Rozdíl mezi formulace *adresy URL přesměrování* a *přepsání adresy URL* je jednoduchý, ale má vliv na důležité pro poskytování prostředků pro klienty. Middleware pro přepis adres URL ASP.NET Core je schopen splnit potřebu obojí.

A *adresy URL přesměrování* zahrnuje operace na straně klienta, kde klientovi je odeslán pokyn k přístupu k prostředku na jinou adresu než klient původně požadována. To vyžaduje odezvy serveru. Adresa URL přesměrování, který je vrácen do klienta se zobrazí v adresním řádku prohlížeče, pokud klient odešle novou žádost o prostředku.

Pokud `/resource` je *přesměrováno* k `/different-resource`, tento server odpoví, že klient by měl získat prostředek na `/different-resource` s kód stavu označující, že přesměrování je dočasný nebo trvalý.

![Koncový bod služby WebAPI dočasně změnila z verze 1 (v1) verze 2 (v2) na serveru. Klient odešle požadavek na službu na cestu /v1/api verze 1. Server odesílá zpět 302 odpověď (Found) se nové, dočasné cesty pro službu v /v2/api verze 2. Klient provádí druhou žádost ve službě na adrese URL přesměrování. Server jako odpověď vrátí stavový kód 200 (OK).](url-rewriting/_static/url_redirect.png)

Při přesměrování požadavků na jinou adresu URL, označuje, zda přesměrování trvalé nebo dočasné zadáním stavový kód s odpovědí:

* *301 - trvale přesunuto* stavový kód se používá ve kterém prostředek má novou, trvalé adresy URL a chcete, aby dáte pokyn, aby klient, všechny budoucí žádosti o prostředku by měl použít novou adresu URL. *Klient může ukládat do mezipaměti a opakovaně používat odpovědi, když je obdržena 301 stavový kód.*

* *302 - nalezen* stavový kód se používá v případě přesměrování je dočasný nebo obecně změnit. 302 stavový kód označuje klientovi nechcete uložit adresu URL a budoucí použití.

Další informace o stavových kódů, najdete v části [dokumentu RFC 2616: Definice stavových kódů](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

A *přepsání adresy URL* je operace na straně serveru, který poskytuje prostředek z adres různých prostředků než vyžádáno klientem. Přepisování adres URL nevyžaduje odezvy serveru. Přepsaný adresa URL není vrácen do klienta a nebude zobrazovat v adresním řádku prohlížeče.

Pokud `/resource` je *přepsán* k `/different-resource`, server *interně* načte a vrátí prostředek na `/different-resource`.

I když se klient může být schopný načíst prostředek na přepsaný adresu URL, není klient informován, že prostředky existují na přepsaný adrese URL, když vytvoří jeho žádost a obdrží odpověď.

![Koncový bod služby WebAPI se změnil z verze 1 (v1) verze 2 (v2) na serveru. Klient odešle požadavek na službu na cestu /v1/api verze 1. Přístup k této službě na cestu /v2/api verze 2 je přepsán adresu URL požadavku. Služby jsou reaguje na klienta se stavovým kódem 200 (OK).](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>Ukázková aplikace přepis adres URL

Můžete prozkoumat funkce Middleware přepisování adres URL se [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/). Aplikace použije pro přesměrování a přepsání pravidel a zobrazuje přesměrované nebo přepsaný adresu URL pro několik scénářů.

## <a name="when-to-use-url-rewriting-middleware"></a>Kdy použít Middleware pro přepis adres URL

Middleware pro přepis adres URL používejte, až budete moci pomocí následujících postupů:

* [Modul přepisování adres URL se službou IIS v systému Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Apache mod_rewrite modulu na serveru Apache](https://httpd.apache.org/docs/2.4/rewrite/)
* [Na serveru Nginx přepisování adres URL](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Navíc používat middleware, když je aplikace hostovaná na [serveru HTTP.sys](xref:fundamentals/servers/httpsys) (dříve se označovaly jako WebListener).

Hlavní důvody pro použití serverem přepisování adres URL technologií ve službě IIS, Apache a Nginx jsou:

* Middleware nepodporuje úplné funkce aplikace tyto moduly.

  Některé funkce serveru moduly nechcete pracovat s projekty ASP.NET Core, jako `IsFile` a `IsDirectory` omezení modulu IIS Rewrite. V těchto scénářích platí místo toho použijte middleware.
* Výkon middleware pravděpodobně neodpovídá počtu moduly.

  Srovnávací testy je jediný způsob, jak zjistit poprvé jaký přístup nejvíce snižuje výkon, nebo pokud snížení výkonu zanedbatelné.

## <a name="package"></a>Balíček

Aby byly middleware ve vašem projektu, přidejte odkaz na balíček pro [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) v souboru projektu, který obsahuje [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) balíčku.

Pokud nepoužíváte `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all, přidejte odkaz na projekt `Microsoft.AspNetCore.Rewrite` balíčku.

## <a name="extension-and-options"></a>Rozšíření a možnosti

Vytvořit přepsání adresy URL a přesměrovat pravidla tak, že vytvoříte instanci [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) třída s atributem rozšiřující metody pro každý z vašich pravidlech přepisování. Zřetězit více pravidel v pořadí, které chcete je zpracována. `RewriteOptions` Jsou předány do Middleware pro přepis adres URL, protože se přidá do kanálu požadavku s <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Přesměrovat na www webové služby.

Povolení aplikace pro přesměrování jinou hodnotu než tři možnosti`www` vyžádá, aby `www`:

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Požadavek na trvalé přesměrování `www` subdomény, pokud je požadavek jinou hodnotu než`www`. Přesměruje se [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) stavový kód.

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Požadavek na přesměrování `www` subdomény, pokud je příchozí požadavek jinou hodnotu než`www`. Přesměruje se [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) stavový kód. Přetížení umožňuje stanovit stavový kód odpovědi. Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídu pro přiřazení kódu stavu.

### <a name="url-redirect"></a>Adresa URL pro přesměrování

Použití <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> pro přesměrování požadavků. První parametr obsahuje vaše regulárního výrazu pro porovnávání v cestě adresy URL příchozích. Druhý parametr je řetězci pro nahrazení. Třetí parametr, pokud jsou k dispozici, určuje kód stavu. Pokud nezadáte kód stavu, stavový kód výchozí *302 - nalezen*, což znamená, že prostředek je dočasně nepřesunul ani nahradit.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

V prohlížeči pomocí nástrojů pro vývojáře, vytvořte žádost na ukázkovou aplikaci s cestou `/redirect-rule/1234/5678`. Odpovídá regulárnímu cestu požadavku na `redirect-rule/(.*)`, a cesta se nahradí `/redirected/1234/5678`. Přesměrování URL budou odeslána zpět do klienta se *302 - nalezen* stavový kód. Prohlížeč odešle nový požadavek na adrese URL přesměrování, které se zobrazí v adresním řádku prohlížeče. Protože žádná pravidla v aplikaci vzorek se vyhledala shoda podle adresy URL pro přesměrování:

* Druhý požadavek přijme *200 – OK* odezvu aplikace.
* Text odpovědi zobrazí adresu URL přesměrování.

A odezvy se provádí na server, když je adresa URL *přesměrováno*.

> [!WARNING]
> Buďte opatrní při vytváření pravidla pro přesměrování. U každého požadavku do aplikace, včetně po přesměrování se vyhodnocují pravidla pro přesměrování. Je snadné vytvořit náhodou *smyčky nekonečné přesměrování*.

Původní žádosti: `/redirect-rule/1234/5678`

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí](url-rewriting/_static/add_redirect.png)

Část výraz v závorkách se nazývá *skupina zachycení*. Tečka (`.`) výrazu znamená, že *odpovídá jakémukoli znaku*. Hvězdička (`*`) označuje *odpovídá předchozímu znaku nula nebo vícekrát*. Proto segmenty poslední dvě cesty adresy URL, `1234/5678`, jsou zachyceny na základě skupina zachycení `(.*)`. Libovolná hodnota je zadat v adrese URL požadavku po `redirect-rule/` zachycena této skupiny jediné zachycení.

V řetězci pro nahrazení zachycené skupiny jsou vloženy do řetězce bez znak dolaru (`$`) následované pořadové číslo pro zachytávání. Hodnota první skupiny zachycení se získá pomocí `$1`, druhý s `$2`, a budou pokračovat v práci v pořadí pro zachycení skupiny ve vaší regulární výraz. Je pouze jeden zachycené skupině regex pravidlo přesměrování v ukázkovou aplikaci, takže pouze jednu skupinu vložený v řetězci pro nahrazení, který je `$1`. Když se pravidlo použije, adresa URL stane `/redirected/1234/5678`.

### <a name="url-redirect-to-a-secure-endpoint"></a>Adresa URL přesměrování do zabezpečeného koncového bodu

Použití <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> pro přesměrování požadavků protokolu HTTP na stejného hostitele a cestu pomocí protokolu HTTPS. Pokud není zadán kód stavu, middleware použije se výchozí *302 - nalezen*. Pokud není zadán port:

* Výchozí hodnota middleware `null`.
* Schéma se změní na `https` (protokol HTTPS), a klient přistupuje k prostředku na portu 443.

Následující příklad ukazuje, jak nastavit stavový kód na *301 - trvale přesunuto* a změňte port 5001.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Použití <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> přesměrovat nezabezpečené požadavky na stejného hostitele a cestu s zabezpečený protokol HTTPS na portu 443. Middleware nastaví stavový kód na *301 - trvale přesunuto*.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Při přesměrování do zabezpečeného koncového bodu bez nutnosti další přesměrování pravidla, doporučujeme používat Middleware přesměrování protokolu HTTPS. Další informace najdete v tématu [vynucení HTTPS](xref:security/enforcing-ssl#require-https) tématu.

Ukázková aplikace je schopná představením toho, jak používat `AddRedirectToHttps` nebo `AddRedirectToHttpsPermanent`. Přidat metodu rozšíření k `RewriteOptions`. Ujistěte se, nezabezpečený požadavek na aplikaci na libovolnou adresu URL. Zavřít upozornění, že nedůvěryhodný certifikát podepsaný svým držitelem zabezpečení v prohlížeči nebo vytvořte výjimku důvěřovat certifikátům.

Původní žádost o prostřednictvím `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https.png)

Původní žádost o prostřednictvím `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Přepsání adresy URL

Použití <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepis adres URL. První parametr obsahuje regulárního výrazu pro porovnávání na příchozí cestě adresy URL. Druhý parametr je řetězci pro nahrazení. Třetí parametr `skipRemainingRules: {true|false}`, pozná middleware, jestli se mají přeskočit další přepsání pravidla, pokud aktuální pravidlo použije.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Původní žádosti: `/rewrite-rule/1234/5678`

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí](url-rewriting/_static/add_rewrite.png)

Stříšky (`^`) na začátku výrazu znamená že odpovídající začne na začátku cesty URL.

V předchozím příkladu se pravidlo přesměrování `redirect-rule/(.*)`, neexistuje žádný stříšky (`^`) na začátku regulární výraz. Proto může předcházet žádné znaky `redirect-rule/` v cestě k porovnání.

| Cesta                               | Shoda |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Ano   |
| `/my-cool-redirect-rule/1234/5678` | Ano   |
| `/anotherredirect-rule/1234/5678`  | Ano   |

Pravidlo pro přepis adres `^rewrite-rule/(\d+)/(\d+)`, pouze odpovídá cesty, pokud jsou při spuštění systému `rewrite-rule/`. V následující tabulce Všimněte si rozdílů v porovnávání.

| Cesta                              | Shoda |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Ano   |
| `/my-cool-rewrite-rule/1234/5678` | Ne    |
| `/anotherrewrite-rule/1234/5678`  | Ne    |

Následující `^rewrite-rule/` část výrazu, jsou dvě skupiny zachycení `(\d+)/(\d+)`. `\d` Označuje, že *odpovídá číslici (číslo)*. Znaménko plus (`+`) znamená, že *odpovídat nejméně jeden z předchozí znak*. Proto se adresa URL musí obsahovat číslo za nímž následuje lomítko následované jiné číslo. Tyto zachytávání skupiny jsou vloženy do přepsaný adresy URL jako `$1` a `$2`. Přepište řetězci pro nahrazení pravidlo umístí zachycené skupiny do řetězce dotazu. Požadovaná cesta `/rewrite-rule/1234/5678` je přepsán získat prostředek na `/rewritten?var1=1234&var2=5678`. Pokud řetězec dotazu je k dispozici na původní požadavek, to se zachová, i když je přepsán adresu URL.

Neexistuje žádné odezvy na server se získat prostředek. Pokud existuje prostředek se načíst a vrácen do klienta se *200 – OK* stavový kód. Protože klient není přesměrován, adresu URL v adresním řádku prohlížeče se nezmění. Klienty nelze rozpoznat, že na serveru došlo k operaci přepisování adres URL.

> [!NOTE]
> Použití `skipRemainingRules: true` vždy, když možné protože pravidel je výpočetně náročná a zvyšuje doba odezvy aplikace. Nejrychlejší odezvu aplikace:
>
> * Pravidla z nejčastěji odpovídající pravidlo pro nejméně často odpovídající pravidlo pro přepis adres pořadí.
> * Zpracování zbývající pravidla přeskočte, pokud je nalezena shoda, a je potřeba žádná další pravidla zpracování.

### <a name="apache-modrewrite"></a>Apache mod_rewrite

Použití Apache mod_rewrite pravidel s <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>. Ujistěte se, že soubor pravidel je nasazen s aplikací. Další informace a příklady pravidel mod_rewrite najdete v tématu [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).

A <xref:System.IO.StreamReader> slouží k načtení pravidla z *ApacheModRewrite.txt* souboru pravidel:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

Ukázková aplikace přesměruje požadavky z `/apache-mod-rules-redirect/(.\*)` k `/redirected?id=$1`. Stavový kód odpovědi je *302 - nalezen*.

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

Původní žádosti: `/apache-mod-rules-redirect/1234`

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí](url-rewriting/_static/add_apache_mod_redirect.png)

Middleware podporuje následující proměnné na serveru Apache mod_rewrite:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* IPV6
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* ČAS
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* MODULU KPI

### <a name="iis-url-rewrite-module-rules"></a>Modul přepisování adres URL služby IIS pravidla

Pokud chcete použít stejnou sadu pravidel, která se vztahuje na modul přepisování adres URL služby IIS, použijte <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>. Ujistěte se, že soubor pravidel je nasazen s aplikací. Není přímá middlewaru, který má být použit aplikace *web.config* souboru při spuštění ve Windows serveru služby IIS. Se službou IIS, tato pravidla by měly být uloženy mimo aplikaci prvku *web.config* souboru, aby nedocházelo ke konfliktům s modulem IIS Rewrite. Další informace a příklady pravidel modul přepisování adres URL služby IIS najdete v tématu [pomocí přepisování adres Url modul 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [URL přepsání konfigurace odkazu na modul](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).

A <xref:System.IO.StreamReader> slouží k načtení pravidla z *IISUrlRewrite.xml* souboru pravidel:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

Ukázková aplikace přepíše žádosti od `/iis-rules-rewrite/(.*)` k `/rewritten?id=$1`. Odpověď je odeslána na klienta se *200 – OK* stavový kód.

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

Původní žádosti: `/iis-rules-rewrite/1234`

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí](url-rewriting/_static/add_iis_url_rewrite.png)

Pokud máte aktivní revize modul IIS s nakonfigurovaná pravidla úrovni serveru, které by ovlivnily aplikace nežádoucí způsoby, můžete zakázat modul IIS revize pro danou aplikaci. Další informace najdete v tématu [moduly IIS zakázání](xref:host-and-deploy/iis/modules#disabling-iis-modules).

#### <a name="unsupported-features"></a>Nepodporované funkce

Middleware vydané s ASP.NET Core 2.x nepodporuje následující funkce modul přepisování adres URL služby IIS:

* Odchozí pravidla
* Vlastní serverové proměnné
* Zástupné znaky
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>Podporované serverové proměnné

Middleware podporuje následující proměnné serveru modul přepisování adres URL služby IIS:

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> prostřednictvím <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>. Tento přístup může poskytnout větší flexibilitu pro umístění vaší revize souborů pravidel. Ujistěte se, že soubory přepsání pravidel jsou nasazeny na server v cestě, které zadáte.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Pravidlo na základě – metoda

Použití <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> implementovat vlastní logiku pravidla v metodě. `Add` Zpřístupňuje <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, který je k dispozici <xref:Microsoft.AspNetCore.Http.HttpContext> pro použití v metodě. [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) Určuje, jak další kanálu probíhá zpracování. Nastavte hodnotu na jednu z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> polí popsaných v následující tabulce.

| `RewriteContext.Result`              | Akce                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules` (výchozí) | Pokračujte v použití pravidel.                                         |
| `RuleResult.EndResponse`             | Zastavit použití pravidel a odeslání odpovědi.                       |
| `RuleResult.SkipRemainingRules`      | Zastavit použití pravidel a odeslat kontextu do dalšího middlewaru. |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

Ukázková aplikace předvádí metodu, který přesměruje požadavky pro cesty, která končit *.xml*. Pokud se požadavek `/file.xml`, je požadavek přesměrován na `/xmlfiles/file.xml`. Kód stavu je nastaven na *301 - trvale přesunuto*. Při prohlížeč provede novou žádost */xmlfiles/file.xml*, Middleware statické soubory slouží soubor do klienta z *wwwroot/xmlfiles* složky. Pro přesměrování explicitně nastavte stavový kód odpovědi. V opačném případě *200 – OK* se vrátí stavový kód a přesměrování nedojde na straně klienta.

*RewriteRules.cs*:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Tento přístup může také přepsání požadavků. Ukázková aplikace předvádí přepsání cesty u všech žádostí textový soubor k poskytování *soubor.txt* textový soubor z *wwwroot* složky. Middleware statické soubory slouží souboru na základě cesty aktualizované požadavku:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs*:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>Pravidlo na základě IRule

Použít <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> používat logice pravidla v třídě, která implementuje <xref:Microsoft.AspNetCore.Rewrite.IRule> rozhraní. `IRule` nabízí větší flexibilitu v porovnání s využitím pravidel založených na volání metody přístupu. Vaše implementace třídy může obsahovat konstruktor, který umožňuje, můžete předat parametry <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> metody.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Hodnoty parametrů v ukázkové aplikaci pro `extension` a `newPath` jsou kontrolovány pro splnění určitých podmínek. `extension` Musí obsahovat hodnotu, a hodnota musí být *.png*, *.jpg*, nebo *.gif*. Pokud `newPath` není platný, <xref:System.ArgumentException> je vyvolána výjimka. Pokud se požadavek *image.png*, je požadavek přesměrován na `/png-images/image.png`. Pokud se požadavek *image.jpg*, je požadavek přesměrován na `/jpg-images/image.jpg`. Kód stavu je nastaven na *301 - trvale přesunuto*a `context.Result` je nastavena na zastavení zpracování pravidel a odeslání odpovědi.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Původní žádosti: `/image.png`

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí pro image.png](url-rewriting/_static/add_redirect_png_requests.png)

Původní žádosti: `/image.jpg`

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí pro image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Příklady regulární výraz

| Cíl | Řetězec regulárního výrazu &<br>Příklad shody | Náhradní řetězec &<br>Příklad výstupu |
| ---- | ------------------------------- | -------------------------------------- |
| Přepsat cestu do řetězce dotazu | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Odstranit koncové lomítko | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Vynucení adresy koncové lomítko | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Vyhněte se přepsání určité žádosti | `^(.*)(?<!\.axd)$` Nebo `^(?!.*\.axd$)(.*)$`<br>Ano: `/resource.htm`<br>Ne: `/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Změna uspořádání segmenty adres URL | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Nahraďte segment adresy URL | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

## <a name="additional-resources"></a>Další zdroje

* [Spuštění aplikace](startup.md)
* [Middleware](xref:fundamentals/middleware/index)
* [Regulární výrazy v rozhraní .NET](/dotnet/articles/standard/base-types/regular-expressions)
* [Jazyk regulárních výrazů – Stručná referenční příručka](/dotnet/articles/standard/base-types/quick-ref)
* [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)
* [Pomocí modulu přepisování adres Url 2.0 (pro IIS)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [Odkaz na modul konfigurace adresy URL revize](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [Fórum modulu přepsání adresy URL služby IIS](https://forums.iis.net/1152.aspx)
* [Zachovat jednoduchá struktura adresy URL](https://support.google.com/webmasters/answer/76329?hl=en)
* [10 přepisování adres URL tipy a triky](http://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [Lomítko nebo lomítko](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
