---
title: Migrace moduly a obslužné rutiny HTTP do middlewaru ASP.NET Core
author: rick-anderson
description: ''
ms.author: tdykstra
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: 84381210910c66a7d121120b8c6b0f046cae8c4f
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207809"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>Migrace moduly a obslužné rutiny HTTP do middlewaru ASP.NET Core

Tento článek popisuje, jak migrovat existující ASP.NET [z modulů HTTP a obslužných rutin system.webserver](/iis/configuration/system.webserver/) k ASP.NET Core [middleware](xref:fundamentals/middleware/index).

## <a name="modules-and-handlers-revisited"></a>Moduly a obslužné rutiny kdykoli znovu spustit.

Než budete pokračovat s middlewarem ASP.NET Core, Pojďme nejdříve rekapitulace jak fungují moduly protokolu HTTP a obslužné rutiny:

![Obslužná rutina moduly](http-modules/_static/moduleshandlers.png)

**Obslužné rutiny jsou:**

* Třídy, které implementují [IHttpHandler](/dotnet/api/system.web.ihttphandler)

* Používá ke zpracování požadavků pomocí daného názvu souboru nebo příponu, například *.report*

* [Nakonfigurované](/iis/configuration/system.webserver/handlers/) v *Web.config*

**Moduly jsou:**

* Třídy, které implementují [IHttpModule](/dotnet/api/system.web.ihttpmodule)

* Vyvolat pro každý požadavek

* Možnost zkrácenou (zastaví další zpracování požadavku)

* Možnost přidat do odpovědi HTTP, nebo vytvořte svoje vlastní

* [Nakonfigurované](/iis/configuration/system.webserver/modules/) v *Web.config*

**Pořadí modulů zpracovat příchozí žádosti, ve které je určeno:**

1. [Životního cyklu aplikace](https://msdn.microsoft.com/library/ms227673.aspx), což je řada události vyvolané ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)atd. Každý modul vytvořit obslužnou rutinu pro jeden nebo více událostí.

2. Pro stejnou událost, pořadí, ve kterém jsou nakonfigurované v *Web.config*.

Kromě modulů, můžete přidat obslužné rutiny pro události životního cyklu, které mají vaše *Global.asax.cs* souboru. Tyto rutiny spustit po obslužných rutin v nakonfigurovaných moduly.

## <a name="from-handlers-and-modules-to-middleware"></a>Z obslužné rutiny a moduly, které middleware

**Middleware je jednodušší než HTTP moduly a obslužné rutiny:**

* Moduly, obslužné rutiny, *Global.asax.cs*, *Web.config* (s výjimkou konfigurace služby IIS) a životního cyklu aplikace jsou pryč

* Role moduly a obslužné rutiny byly převzaty middlewaru

* Middleware budou nakonfigurováni s použitím kódu spíše než v *Web.config*

* [Větvení v kanálu](xref:fundamentals/middleware/index#use-run-and-map) umožňuje odesílat požadavky pro konkrétní middleware, na základě nejenom adresy URL ale také na hlavičky žádosti, řetězce dotazů, atd.

**Middleware jsou velmi podobné moduly:**

* Vyvolá se v zásadě pro každý požadavek

* Možnost zkrácenou žádost, pomocí [není předání požadavku do dalšího middlewaru](#http-modules-shortcircuiting-middleware)

* Možnost vytvářet své vlastní odpovědi HTTP

**Middleware a moduly jsou zpracovány v jiném pořadí:**

* Pořadí middlewaru je založena na pořadí, ve kterém jste vložili do kanálu požadavku, zatímco je především podle pořadí modulů [životního cyklu aplikace](https://msdn.microsoft.com/library/ms227673.aspx) události

* Middlewaru odpovědi probíhá v pořadí reverzní od požadavkům, přestože pořadí modulů je stejný pro požadavky a odpovědi

* Zobrazit [vytvoření kanálu middlewaru s IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

![Middleware](http-modules/_static/middleware.png)

Všimněte si, jak na obrázku výše, že ověřovací middleware short-circuited požadavku.

## <a name="migrating-module-code-to-middleware"></a>Migrace kódu modulu do middlewaru

Existující modul HTTP bude vypadat nějak takto:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

Jak je znázorněno v [Middleware](xref:fundamentals/middleware/index) stránky, middleware ASP.NET Core je třída, která poskytuje `Invoke` pořízení – metoda `HttpContext` a vrácení `Task`. Nové middleware bude vypadat nějak takto:

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

Předchozí šablonu postupem middleware byla získána z část [zápis middlewaru](xref:fundamentals/middleware/write).

*MyMiddlewareExtensions* pomocná třída usnadňuje konfiguraci middlewaru v vaše `Startup` třídy. `UseMyMiddleware` Metoda přidá do kanálu požadavku vaše třída middlewaru. Získání služby nezbytné middlewarem vložený v konstruktoru middlewaru.

<a name="http-modules-shortcircuiting-middleware"></a>

Modul může ukončit požadavek, například pokud uživatel nemá oprávnění:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

Tento middleware zpracovává není voláním `Invoke` na další middleware v kanálu. Mějte na paměti, že to nebude ukončit plně požadavek, protože předchozí middlewares bude stále vyvolán při odpovědi díky cestě zpět prostřednictvím kanálu.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

Při migraci vašeho modulu funkce do nové middleware možná zjistíte, že váš kód nebude kompilovat, protože `HttpContext` třídy významně změnil v ASP.NET Core. [Později](#migrating-to-the-new-httpcontext), uvidíte, jak migrovat do nového objektu HttpContext ASP.NET Core.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>Migrace modulu vložení do kanálu požadavku

Z modulů HTTP jsou obvykle přidány do požadavku kanálu pomocí *Web.config*:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

Tím, že převod [přidání nové middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) požadavku kanálu v vaše `Startup` třídy:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

Přesné místě v kanálu, ve kterém vložíte nový middleware závisí na událost, která je zpracována jako modul (`BeginRequest`, `EndRequest`atd) a jeho pořadí v seznamu modulů v *Web.config*.

Jak už jsme uvedli, neexistuje žádný životního cyklu aplikací v ASP.NET Core a pořadí, ve kterém jsou zpracovány odpovědi middlewarem se liší od pořadí, použít moduly. To může se rozhodnout pořadí ještě náročnější.

Pokud řazení stane problém, můžete modul rozdělit do několika middlewarových komponent, které lze provést řazení nezávisle na sobě.

## <a name="migrating-handler-code-to-middleware"></a>Migrace kódu obslužné rutiny pro middleware

Obslužné rutiny HTTP vypadá přibližně takto:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

V projektu ASP.NET Core převedla to pro middleware nějak takto:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

Tento middleware je velmi podobný middleware odpovídající moduly. Pouze skutečné rozdílem je, že zde není žádná volání `_next.Invoke(context)`. To dává smysl, protože rutina je na konci požadavku kanálu, takže neexistují žádné další middleware, který má být vyvolán.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>Migrace obslužná rutina vložení do kanálu požadavku

Konfiguraci obslužné rutiny HTTP se provádí *Web.config* a vypadá přibližně takto:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

To může převést tak, že přidáte nový middlewaru obslužná rutina požadavku kanálu v vaše `Startup` třídy, podobně jako s middlewarem převést z modulů. Problém s tohoto přístupu je, že ho bude posílat všechny žádosti o novou obslužnou rutinu middlewaru. Ale chcete jenom s danou příponou přenosu žádostí do vlastního middlewaru. Který by vám poskytlo stejné funkce, kterou jste používali s obslužnou rutinu HTTP.

Jedním z řešení je větvení kanálu pro požadavky s danou příponou pomocí `MapWhen` – metoda rozšíření. Můžete to provést ve stejném `Configure` metody, kde můžete přidávat další middleware:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen` mají tyto parametry:

1. Výraz lambda, který přijímá `HttpContext` a vrátí `true` Pokud požadavek by měl přejděte dolů větve. To znamená, že můžete rozvětvit požadavky nejen založené na jejich rozšíření, ale také na hlavičky žádosti, parametry řetězce dotazu, atd.

2. Výraz lambda, který přebírá `IApplicationBuilder` a přidá veškerý middleware pro větev. To znamená, že přidáte další middleware do větve před middlewaru obslužné rutiny.

Middleware přidané do kanálu než větev se vyvolá u všech požadavků. větev nebude mít vliv na ně.

## <a name="loading-middleware-options-using-the-options-pattern"></a>Možnosti middlewaru pomocí vzoru možnosti načítání

Některé moduly a obslužné rutiny mají možnosti konfigurace, které jsou uloženy v *Web.config*. V ASP.NET Core, nový model konfigurace se používá místo *Web.config*.

Nové [konfigurační systém](xref:fundamentals/configuration/index) vám tento problém vyřešit tyto možnosti:

* Přímo vložit možnosti do middleware, jak je znázorněno [další části](#loading-middleware-options-through-direct-injection).

* Použití [možnosti vzor](xref:fundamentals/configuration/options):

1. Vytvoření třídy k umístění možnosti middlewaru, například:

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. Hodnoty možností Store

   Konfigurace systému umožňuje ukládat hodnoty možností kamkoli chcete. Ale nejvíce lokality použijte *appsettings.json*, takže provedeme tento přístup:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   *MyMiddlewareOptionsSection* následuje název oddílu. Nemusí být stejný jako název třídy možnosti.

3. Třída options přidružit hodnot možností

    Možnosti vzor používá framework injektáž závislostí ASP.NET Core pro přiřazení možností typu (například `MyMiddlewareOptions`) s `MyMiddlewareOptions` objekt, který má skutečné možnosti.

    Aktualizace vašeho `Startup` třídy:

   1. Pokud používáte *appsettings.json*, přidejte do konfigurace tvůrce v `Startup` konstruktor:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. Nakonfigurujte možnosti služby:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. Vaše možnosti přidružte vaše třída možnosti:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. Vložit možnosti do vaší konstruktor middlewaru. To se podobá vkládá možnosti do kontroleru.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   [UseMiddleware](#http-modules-usemiddleware) rozšiřující metoda, která přidá vaši middlewaru, který má `IApplicationBuilder` se postará o vkládání závislostí.

   To se neomezuje na `IOptions` objekty. Tímto způsobem můžete vloženy jakýkoli jiný objekt, který vyžaduje middlewaru.

## <a name="loading-middleware-options-through-direct-injection"></a>Načítají se možnosti middlewaru prostřednictvím přímé vkládání

Možnosti vzor má výhodu, že vytvoří volné párování mezi hodnotami možnosti a jejich příjemce. Po třídy jste přidružili k možnosti skutečné hodnoty, jiné třídy lze získat přístup k možnostem prostřednictvím rozhraní vkládání závislostí. Není třeba předávat hodnoty možnosti.

Tím je prolomen ale pokud chcete použít stejné middleware dvakrát, s použitím různých možností. Například povolení middleware použitý v jiné větve, které umožňuje různé role. Dva objekty různé možnosti nelze přidružit jeden možnosti třídy.

Řešením je získat možnosti objekty s hodnotami skutečné možnosti ve vaší `Startup` třídy a předat tyto přímo ke každé instanci middlewaru.

1. Přidejte druhý klíč do *appsettings.json*

   Chcete-li přidat druhou sadu možností *appsettings.json* souboru, aby byl jednoznačně identifikovaný použijte nový klíč:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. Načíst hodnoty pro možnosti a předat je do middlewaru. `Use...` – Metoda rozšíření (která middlewaru přidá do kanálu) je logické místo a zajistěte tak předání hodnot možností: 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. Povolí middleware má provést parametr možnosti. Poskytněte přetížení `Use...` – metoda rozšíření (, která přijímá parametr možnosti a předává jej do `UseMiddleware`). Když `UseMiddleware` se volá s parametry, předá parametry do konstruktoru middleware při vytvoření instance objektu middlewaru.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   Všimněte si, jak to zabalí objekt možnosti v `OptionsWrapper` objektu. To implementuje `IOptions`, jak očekává konstruktor middlewaru.

## <a name="migrating-to-the-new-httpcontext"></a>Migrace do nového objektu HttpContext

Už dříve jste viděli, která `Invoke` metoda v middlewaru přebírá parametr typu `HttpContext`:

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext` má se významně změnil v ASP.NET Core. Tato část ukazuje, jak nejčastěji používané vlastnosti přeložit [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) k novému `Microsoft.AspNetCore.Http.HttpContext`.

### <a name="httpcontext"></a>HttpContext

**HttpContext.Items** se přeloží na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**ID požadavku jedinečné (nevyskytují System.Web.HttpContext)**

Poskytuje jedinečný id pro každý požadavek. Velmi užitečné zahrnout do protokolů.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext.Request

**HttpContext.Request.HttpMethod** se přeloží na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext.Request.QueryString** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext.Request.Url** a **HttpContext.Request.RawUrl** přeložit na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext.Request.IsSecureConnection** se přeloží na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext.Request.UserHostAddress** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext.Request.Cookies** se přeloží na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext.Request.RequestContext.RouteData** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext.Request.Headers** se přeloží na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext.Request.UserAgent** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext.Request.UrlReferrer** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext.Request.ContentType** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext.Request.Form** se přeloží na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> Čtení hodnot formuláře pouze v případě, že je typem obsahu sub *x--www-form-urlencoded* nebo *data formuláře*.

**HttpContext.Request.InputStream** se přeloží na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> Tento kód používejte pouze ve obslužné rutiny typu middleware, na konci kanálu.
>
>Nezpracované tělo si můžete přečíst, jak je znázorněno výše jen jednou pro každý požadavek. Middleware pokusu o čtení textu po první čtení budou číst prázdným textem zprávy.
>
>To neplatí pro čtení formuláře, jak je uvedeno výše, protože, který se provádí z vyrovnávací paměti.

### <a name="httpcontextresponse"></a>HttpContext.Response

**HttpContext.Response.Status** a **HttpContext.Response.StatusDescription** přeložit na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**HttpContext.Response.ContentEncoding** a **HttpContext.Response.ContentType** přeložit na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**HttpContext.Response.ContentType** na svůj vlastní také převádí na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext.Response.Output** se přeloží na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext.Response.TransmitFile**

Poskytovat souboru je popsána [tady](../fundamentals/request-features.md#middleware-and-request-features).

**HttpContext.Response.Headers**

Odesílání hlaviček odpovědí je složité skutečnost, že pokud jste nastavili, je poté, co všechno se zapsala do těla odpovědi, že nebude odeslána.

Řešením je nastavení, která bude volána vpravo před zápisem do odpovědi spustí metodu zpětného volání. To se nejlépe provádí na začátku `Invoke` metoda v middlewaru. Je tato metoda zpětného volání, který nastavuje vaše hlavičky odpovědi.

Následující kód nastaví volá metodu zpětného volání `SetHeaders`:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetHeaders` Metoda zpětného volání bude vypadat takto:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext.Response.Cookies**

Soubory cookie cestovat do prohlížeče v *Set-Cookie* hlavičky odpovědi. V důsledku toho odesílání souborů cookie vyžaduje stejné zpětného volání jako používané pro odesílání hlaviček odpovědí:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetCookies` Metoda zpětného volání by vypadat nějak takto:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>Další zdroje

* [Přehled protokolu HTTP moduly a obslužné rutiny HTTP](/iis/configuration/system.webserver/)
* [Konfigurace](xref:fundamentals/configuration/index)
* [Spuštění aplikace](xref:fundamentals/startup)
* [Middleware](xref:fundamentals/middleware/index)
