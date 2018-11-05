---
uid: web-pages/overview/ui-layouts-and-themes/validating-user-input-in-aspnet-web-pages-sites
title: Ověřování uživatelského vstupu v rozhraní ASP.NET Web Pages servery (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento článek popisuje, jak ověřit informace získáte od uživatelů &mdash; tedy zajistit, aby uživatelé zadali platné informace ve formátu HTML formulářů v jako...
ms.author: riande
ms.date: 02/20/2014
ms.assetid: 4eb060cc-cf14-41ae-bab1-14a2c15332d0
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/validating-user-input-in-aspnet-web-pages-sites
msc.type: authoredcontent
ms.openlocfilehash: 8f049adce33e452896b5e2a444635ff30d18e480
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021518"
---
<a name="validating-user-input-in-aspnet-web-pages-razor-sites"></a>Ověřování uživatelského vstupu v lokalitách rozhraní ASP.NET Web Pages (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek popisuje, jak ověřit informace získáte od uživatelů &mdash; to znamená, ujistěte se, aby uživatelé zadali platné informace ve formátu HTML formulářů na webu rozhraní ASP.NET Web Pages (Razor).
> 
> Co se dozvíte:
> 
> - Jak zkontrolovat, jestli se uživatelovo zadání uživatele odpovídá ověřovací kritéria, které definujete.
> - Jak určit, jestli jste předali všechny testy pro ověření.
> - Jak zobrazit chyby ověření (a způsob jejich formátování).
> - Jak ověřit data, která nepochází přímo od uživatele.
> 
> Toto jsou ASP.NET programování koncepty představenými v tomto článku:
> 
> - `Validation` Pomocné rutiny.
> - `Html.ValidationSummary` a `Html.ValidationMessage` metody.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Webové stránky ASP.NET (Razor) 3
>   
> 
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2.


Tento článek obsahuje následující části:

- [Přehled ověřování uživatelského vstupu](#Overview_of_User_Input_Validation)
- [Ověřování uživatelského vstupu](#Validating_User_Input)
- [Přidání ověřování na straně klienta](#Adding_Client-Side_Validation)
- [Formátování chyby ověření](#Formatting_Validation_Errors)
- [Ověřování dat, který nepochází přímo od uživatele](#Validating_Data_That_Doesnt_Come_Directly_from_Users)

<a id="Overview_of_User_Input_Validation"></a>
## <a name="overview-of-user-input-validation"></a>Přehled ověřování uživatelského vstupu

Pokud požadujete, aby uživatelé zadat informace na stránce – například do formátu, je důležité, abyste měli jistotu, že jsou hodnoty, které jsou zadány platné. Například nechcete zpracovat formuláře, který chybí důležitých informací.

Při zadávání hodnoty do formuláře HTML, jsou hodnoty, které se vstupem řetězce. V mnoha případech jsou hodnoty, které budete potřebovat některé jiné datové typy, jako jsou celá čísla nebo kalendářní data. Proto máte také aby se zajistilo, že hodnoty, které uživatelé zadají lze správně převést na odpovídající datové typy.

Můžete mít také určitá omezení na hodnotách. I v případě, že uživatelé správně zadat celé číslo, například můžete potřebovat abyste měli jistotu, že hodnota spadá do určitého rozsahu.

![Chyby ověření, které používají třídy CSS](validating-user-input-in-aspnet-web-pages-sites/_static/image1.png)

> [!NOTE] 
> 
> **Důležité** ověřování uživatelského vstupu je také důležité pro zabezpečení. Omezíte hodnoty, které mohou uživatelé zadat ve formulářích, snížíte pravděpodobnost, že někdo můžete zadat hodnotu, která může ohrozit zabezpečení vašeho webu.


<a id="Validating_User_Input"></a>
## <a name="validating-user-input"></a>Ověřování uživatelského vstupu

V ASP.NET Web Pages 2, můžete použít `Validator` pomocná rutina pro vstup uživatele. Základní přístup je provést následující kroky:

1. Určete, který vstupní prvky (pole), kterou chcete ověřit.

    Obvykle ověřte hodnoty v `<input>` prvky ve formuláři. Je však vhodné ověřte všechny vstupy, dokonce i vstup, který přichází z omezené prvky jako `<select>` seznamu. To pomáhá zajistit, že uživatelé není obejití ovládacích prvků na stránce a odeslání formuláře.
2. V kódu stránky přidat jednotlivé ověřovací kontroly pro každý vstupní element pomocí metod `Validation` pomocné rutiny.

    Chcete-li zkontrolovat u povinných polí, použijte `Validation.RequireField(field, [error message])` (pro jednotlivá pole) nebo `Validation.RequireFields(field1, field2, ...))` (pro seznam polí). Pro jiné typy ověřování pomocí `Validation.Add(field, ValidationType)`. Pro `ValidationType`, můžete použít tyto možnosti:

    `Validator.DateTime ([error message])`  
   `Validator.Decimal([error message])`  
   `Validator.EqualsTo(otherField [, error message])`  
   `Validator.Float([error message])`  
   `Validator.Integer([error message])`  
   `Validator.Range(min, max [, error message])`  
   `Validator.RegEx(pattern [, error message])`  
   `Validator.Required([error message])`  
   `Validator.StringLength(length)`  
   `Validator.Url([error message])`
3. Při odeslání stránky, zkontrolujte, zda má ověření proběhlo úspěšně, tak, že zkontrolujete `Validation.IsValid`:

    [!code-csharp[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample1.cs)]

    Pokud nejsou žádné chyby ověření, můžete přeskočit, zpracování normální stránky. Například pokud je účel stránky aktualizace databáze, neuděláte, dokud byly vyřešeny všechny chyby ověření.
4. Pokud jsou chyby ověření, zobrazit chybové zprávy v kódu stránky pomocí `Html.ValidationSummary` nebo `Html.ValidationMessage`, nebo obojí.

Následující příklad ukazuje stránka, která znázorňuje tyto kroky.

[!code-cshtml[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample2.cshtml)]

Pokud chcete zobrazit, jak funguje ověřování, spusťte na této stránce a záměrně dělat chyby. Například tady je co bude stránka vypadat, pokud zapomenete zadat název kurzu, pokud zadáte, a pokud zadáte neplatné datum:

![Chyby ověření na vykreslené stránce](validating-user-input-in-aspnet-web-pages-sites/_static/image2.png)

<a id="Adding_Client-Side_Validation"></a>
## <a name="adding-client-side-validation"></a>Přidání ověřování na straně klienta

Ve výchozím nastavení, uživatelský vstup je ověřen po odeslání stránky – to znamená, ověření se provede v kódu serveru. Nevýhody tohoto přístupu je, že uživatelé neví, že udělali chybu až po jejich odeslání stránky. Pokud formuláře je dlouhý nebo složitý, může být nepraktické uživatel hlášení chyb, až po odeslání stránky.

Můžete přidat podporu k provedení ověření v klientského skriptu. V takovém případě ověření se provede, jak uživatelé pracují v prohlížeči. Předpokládejme například, že určíte, že hodnota by měla být celé. Pokud uživatel zadá hodnotu jiných než celých čísel, je Chyba hlášená jako uživatel společnost opustí polem pro zadání. Uživatelé získají okamžitou zpětnou vazbu, která je vhodné pro ně. Ověřování na základě klienta může také omezit počet případů, kdy se uživatel má k odeslání formuláře, chcete-li opravit několik chyb.

> [!NOTE]
> I když používáte ověřování na straně klienta, provede se ověření vždycky také v serverovém kódu. Provedení ověření v serverovém kódu je v rámci bezpečnostních opatření v případě, že uživatelé obejít ověřování na základě klienta.


1. Zaregistrujte následující knihovny jazyka JavaScript na stránce:  

    [!code-html[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample3.html)]

   Jsou dva knihoven načíst ze sítě pro doručování obsahu (CDN), takže není nutné nemusí mít v počítači nebo serveru. Však musí mít místní kopii *jquery.validate.unobtrusive.js*. Pokud dosud nepracujete s šablonou služby WebMatrix (jako je **Starter Site** ), který obsahuje knihovnu, vytvoření webových stránek webu, který je založen na **Starter Site**. Zkopírujte *js* souboru k aktuální lokalitě.
2. Ve značkách pro každý prvek, který jste ověření, přidejte volání do `Validation.For(field)`. Tato metoda generuje atributy, které používají ověřování na straně klienta. (Místo generování skutečný kód jazyka JavaScript, generuje metodu atributů, jako je `data-val-...`. Tyto atributy podporují ověření nerušivého klienta, který používá jQuery proveďte práci).

Na následující stránce ukazuje, jak přidat funkce ověření klienta v příkladu je uvedeno výše.

[!code-cshtml[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample4.cshtml?highlight=35-39,51,61,71)]

Ne všechny ověřovací kontroly na klientech. Konkrétně se ověření datového typu (celé číslo, datum atd.) nejsou spuštěné na straně klienta. Následující kontroly pracovat na klienta a serveru:

- `Required`
- `Range(minValue, maxValue)`
- `StringLength(maxLength[, minLength])`
- `Regex(pattern)`
- `EqualsTo(otherField)`

V tomto příkladu nebudou fungovat test platné datum v kódu klienta. Však test se provede v kódu serveru.

<a id="Formatting_Validation_Errors"></a>
## <a name="formatting-validation-errors"></a>Formátování chyby ověření

Můžete řídit, jak se zobrazí chyby ověření definováním třídy CSS, které mají následující vyhrazené názvy:

- `field-validation-error`. Definuje výstup `Html.ValidationMessage` metoda při zobrazuje chybu.
- `field-validation-valid`. Definuje výstup `Html.ValidationMessage` metodu, pokud se nezobrazí žádná chyba.
- `input-validation-error`. Definuje způsob `<input>` prvky jsou generovány, když dojde k chybě. (Například může tato třída slouží k nastavení barvy pozadí &lt;vstupní&gt; element na jinou barvu, pokud její hodnota je neplatná.) Tato třída šablon stylů CSS se používá pouze během ověření klienta (v ASP.NET Web Pages 2).
- `input-validation-valid`. Definuje vzhled elementů `<input>` elementy, pokud se nezobrazí žádná chyba.
- `validation-summary-errors`. Definuje výstup `Html.ValidationSummary` metoda zobrazuje seznam chyb.
- `validation-summary-valid`. Definuje výstup `Html.ValidationSummary` metodu, pokud se nezobrazí žádná chyba.

Následující `<style>` bloku zobrazuje pravidla pro chybové podmínky.

[!code-css[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample5.css)]

Zadáte-li tento blok stylu na příkladu stránky z dříve v tomto článku, zobrazení chyb bude vypadat jako na následujícím obrázku:

![Chyby ověření, které používají třídy CSS](validating-user-input-in-aspnet-web-pages-sites/_static/image3.png)

> [!NOTE]
> Pokud nepoužíváte ověřování na straně klienta v ASP.NET Web Pages 2, šablon stylů CSS třídy pro `<input>` elementy (`input-validation-error` a `input-validation-valid` nemají žádný vliv.


### <a name="static-and-dynamic-error-display"></a>Chyby statické a dynamické zobrazení

Pravidla šablon stylů CSS pocházet uvedený jako dvojice, například `validation-summary-errors` a `validation-summary-valid`. Tyto páry umožňují definovat pravidla pro obě podmínky: chybovou podmínku a podmínku "normální" (bez chyb). Je důležité pochopit, že vždy vykreslení značky pro zobrazení chyb, i když nejsou žádné chyby. Například, když má stránka `Html.ValidationSummary` metody v kódu, zdroj stránky bude obsahovat následující kód, i když je zobrazení stránky vyžadováno poprvé:

`<div class="validation-summary-valid" data-valmsg-summary="true"><ul></ul></div>`

Jinými slovy `Html.ValidationSummary` vždy vykreslí metoda `<div>` elementu a seznam, i když v seznamu chyb je prázdný. Podobně `Html.ValidationMessage` vždy vykreslí metoda `<span>` prvek jako zástupný symbol pro jednotlivá pole chyb, i když se nezobrazí žádná chyba.

V některých situacích s chybovou zprávou můžou způsobit stránky a přeformátování a může způsobit elementy na stránce uspořádat jinak. Pravidla šablon stylů CSS, které končí `-valid` umožňují definovat rozložení, který pomůže tomuto problému zabránit. Například můžete definovat `field-validation-error` a `field-validation-valid` na obě stejné opravili velikost. Tímto způsobem zobrazované oblasti pro pole je statická a tok stránky se nezmění, pokud se zobrazí chybová zpráva.

<a id="Validating_Data_That_Doesnt_Come_Directly_from_Users"></a>
## <a name="validating-data-that-doesnt-come-directly-from-users"></a>Ověřování dat, který nepochází přímo od uživatele

Někdy je nutné ověřit informace, které nepřejde do stavu přímo z formuláře HTML. Typickým příkladem je stránka, kde je hodnota předaná v řetězci dotazu, jako v následujícím příkladu:

`http://server/myapp/EditClassInformation?classid=1022`

V tomto případě chcete Ujistěte se, že hodnota, která je předána na stránku (tady, 1022 pro hodnotu vlastnosti `classid`) je neplatný. Nelze použít přímo `Validation` pomocná provádět ověřování. Můžete však použít další funkce ověřování systému, jako je schopnost zobrazit chybové zprávy ověření.

> [!NOTE] 
> 
> **Důležité** vždy ověřte hodnoty, které jste získali z *jakékoli* zdroje, včetně hodnoty pole formuláře, hodnoty řetězce dotazu a hodnoty souboru cookie. Je snadné lidem, kteří tato místa změňte (třeba ke škodlivým účelům). Proto je nutné zkontrolovat tyto hodnoty z důvodu ochrany vašich aplikací.


Následující příklad ukazuje, jak může ověřit hodnotu, která je předána v řetězci dotazu. Kód se ověřuje, že hodnota není prázdná a že se jedná o celé číslo.

[!code-csharp[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample6.cs)]

Všimněte si, že test se provádí při požadavku není odeslání formuláře (`if(!IsPost)`). Tento test by úspěšně prošel zpracováním při prvním vyžádání stránky, ale ne žádosti při odeslání formuláře.

Chcete-li zobrazit tato chyba, můžete přidat chyby do seznamu chyb ověřování voláním `Validation.AddFormError("message")`. Pokud stránka obsahuje volání `Html.ValidationSummary` metoda, chyba je zobrazené v protokolu, stejně jako chyba ověření vstupu uživatele.

<a id="AdditionalResources"></a>
## <a name="additional-resources"></a>Další prostředky

[Práce s formuláři HTML ve webech stránky ASP.NET](https://go.microsoft.com/fwlink/?LinkID=202892)
