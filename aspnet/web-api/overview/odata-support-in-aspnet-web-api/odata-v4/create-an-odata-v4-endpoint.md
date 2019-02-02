---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-endpoint
title: Vytvoření koncového bodu OData v4 pomocí rozhraní ASP.NET Web API 2.2 | Dokumentace Microsoftu
author: MikeWasson
description: Open Data Protocol (OData) je protokol data access pro web. OData nabízí jednotným způsobem pro dotazování a manipulaci s datovými sadami prostřednictvím operace CRUD...
ms.author: riande
ms.date: 01/23/2019
ms.assetid: 1e1927c0-ded1-4752-80fd-a146628d2f09
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-endpoint
msc.type: authoredcontent
ms.openlocfilehash: c6a4aa4eb563fd77d5afd9248175d5f5b7984d19
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667567"
---
# <a name="create-an-odata-v4-endpoint-using-aspnet-web-api"></a>Vytvoření koncového bodu OData v4 pomocí rozhraní ASP.NET Web API 

> Open Data Protocol (OData) je protokol data access pro web. OData nabízí jednotným způsobem pro dotazování a manipulaci s datovými sadami prostřednictvím operace CRUD (vytváření, čtení, aktualizace a odstranění).
>
> Rozhraní ASP.NET Web API podporuje v3 a v4 protokolu. Může probíhat dokonce na koncový bod v4, na kterém běží vedle sebe s koncovým bodem v3.
>
> Tento kurz ukazuje, jak vytvořit koncový bod OData v4, který podporuje operace CRUD.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
>
> - Web API 5.2
> - OData v4
> - Visual Studio 2017 (stáhněte si Visual Studio 2017 [tady](https://visualstudio.microsoft.com/downloads/))
> - Entity Framework 6
> - .NET 4.7.2
>
> ## <a name="tutorial-versions"></a>Kurz verze
>
> OData verze 3, naleznete v tématu [vytváření koncového bodu OData v3](../odata-v3/creating-an-odata-endpoint.md).

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

V sadě Visual Studio z **souboru** nabídce vyberte možnost **nový** &gt; **projektu**.

Rozbalte **nainstalováno** &gt; **Visual C#**  &gt; **webové**a vyberte **webová aplikace ASP.NET (.NET Framework)**  šablony. Pojmenujte projekt &quot;ProductService&quot;.

[![](create-an-odata-v4-endpoint/_static/image7.png)](create-an-odata-v4-endpoint/_static/image7.png)

Vyberte **OK**.



[![](create-an-odata-v4-endpoint/_static/image8.png)](create-an-odata-v4-endpoint/_static/image8.png)

Vyberte **prázdný** šablony. V části **přidat složky a základní odkazy pro:** vyberte **webového rozhraní API**. Vyberte **OK**.

## <a name="install-the-odata-packages"></a>Instalace balíčků OData

Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet** &gt; **Konzola správce balíčků**. V okně konzoly Správce balíčků zadejte příkaz:

[!code-console[Main](create-an-odata-v4-endpoint/samples/sample1.cmd)]

Tento příkaz nainstaluje nejnovější balíčky OData NuGet.

## <a name="add-a-model-class"></a>Přidejte třídu modelu

A *modelu* je objekt, který představuje entitu dat ve vaší aplikaci.

V Průzkumníku řešení klikněte pravým tlačítkem na složku modely. V místní nabídce vyberte **přidat** &gt; **třídy**.

[![](create-an-odata-v4-endpoint/_static/image6.png)](create-an-odata-v4-endpoint/_static/image5.png)

> [!NOTE]
> Podle konvence tříd modelu jsou umístěny ve složce modely, ale není nutné postupovat podle tohoto vytváření názvů pro svoje vlastní projekty.


Název třídy `Product`. V souboru Product.cs nahraďte často používaný kód následujícím kódem:

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample2.cs)]

`Id` Vlastnost je klíč entity. Klienti mohou odesílat dotazy entit podle klíče. Například pokud chcete získat produkt s ID 5, identifikátor URI je `/Products(5)`. `Id` Vlastnosti také bude primární klíč v back-end databáze.

## <a name="enable-entity-framework"></a>Povolení rozhraní Entity Framework

V tomto kurzu použijeme Entity Framework (EF) Code First k vytvoření back-end databáze.

> [!NOTE]
> Web API OData EF nevyžaduje. Použijte všechny vrstvy přístup k datům, která jsou dobře převeditelné entity databáze do modelů.


Nejdřív nainstalujte balíček NuGet pro EF. Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet** &gt; **Konzola správce balíčků**. V okně konzoly Správce balíčků zadejte příkaz:

[!code-console[Main](create-an-odata-v4-endpoint/samples/sample3.cmd)]

Otevřete soubor Web.config a přidat následující části uvnitř **konfigurace** element, za **configSections** elementu.

[!code-xml[Main](create-an-odata-v4-endpoint/samples/sample4.xml?highlight=6)]

Toto nastavení přidá připojovacího řetězce pro databázi LocalDB. Tato databáze se použije při spuštění aplikace místně.

V dalším kroku přidejte třídu pojmenovanou `ProductsContext` ke složce modely:

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample5.cs)]

V konstruktoru `"name=ProductsContext"` poskytuje název připojovacího řetězce.

## <a name="configure-the-odata-endpoint"></a>Konfigurace koncového bodu OData

Otevřete soubor aplikace\_Start/WebApiConfig.cs. Přidejte následující **pomocí** příkazy:

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample6.cs)]

Pak přidejte následující kód, který **zaregistrovat** metody:

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample7.cs)]

Tento kód provede dvě věci:

- Vytvoří Entity Data Model (EDM).
- Přidá trasu.

EDM je abstraktní modelem data. EDM slouží k vytvoření dokumentu metadat služby. **ODataConventionModelBuilder** třídy pomocí výchozí konvence pojmenování vytvoří modelu EDM. Tento přístup vyžaduje nejméně kódu. Pokud chcete mít větší kontrolu nad EDM, můžete použít **Tvůrce ODataModelBuilder** třídy za účelem vytvoření tak, že explicitně přidáte vlastností, klíčů a navigačních vlastností EDM.

A *trasy* informuje webového rozhraní API, jak směrovat požadavky HTTP na koncový bod. Chcete-li vytvořit trasy OData v4, zavolejte **MapODataServiceRoute** – metoda rozšíření.

Pokud aplikace obsahuje více koncových bodů protokolu OData, vytvořte pro každou samostatnou trasu. Zadejte všechny trasy, trasy jedinečný název a předpony.

## <a name="add-the-odata-controller"></a>Přidat kontroler OData

A *řadič* je třída, která zpracovává požadavky HTTP. Můžete vytvořit samostatné kontrolerem pro každou sadu entit v služby OData. V tomto kurzu vytvoříte jeden řadič pro `Product` entity.

V Průzkumníku řešení klikněte pravým tlačítkem na složku řadiče a vyberte **přidat** &gt; **třídy**. Název třídy `ProductsController`.

> [!NOTE]
> Verzi tohoto kurzu pro prostředí OData v3 používá **přidat kontroler** generování uživatelského rozhraní. V současné době neexistuje žádný generování uživatelského rozhraní pro OData v4.


Často používaný kód v ProductsController.cs nahraďte následujícím kódem.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample8.cs)]

Využívá kontroler `ProductsContext` pro přístup k databázi pomocí EF. Všimněte si, že přepíše kontroleru **Dispose** metoda a neprovede **ProductsContext**.

Toto je výchozí bod pro kontroler. V dalším kroku přidáme metody pro všechny operace CRUD.

## <a name="query-the-entity-set"></a>Dotazování sady entit

Přidejte následující metody, které `ProductsController`.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample9.cs)]

Konstruktor bez parametrů verzi `Get` metoda vrátí celou kolekci produktů. `Get` Metody *klíč* parametr vyhledá produktu podle jeho klíče (v tomto případě `Id` vlastnost).

**[EnableQuery]** atribut umožňuje upravit dotaz, pomocí možnosti dotazu, jako je například $filter, $sort a $page klientům. Další informace najdete v tématu [podporuje možnosti dotazu OData](../supporting-odata-query-options.md).

## <a name="add-an-entity-to-the-entity-set"></a>Přidání entity do sady entit

Pokud chcete povolit klientům přidání nového produktu do databáze, přidejte následující metodu do `ProductsController`.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample10.cs)]

## <a name="update-an-entity"></a>Aktualizace entity

OData podporuje dvě různé sémantiky pro aktualizaci entity, opravy a PUT.

- PATCH provede částečnou aktualizaci. Klient určuje vlastnosti k aktualizaci.
- PUT nahradí celý entity.

Nevýhodou PUT je, že klient musí odesílat hodnoty pro všechny vlastnosti v entitě, včetně hodnoty, které se mění. [Specifikace prostředí OData](http://docs.oasis-open.org/odata/odata/v4.0/os/part1-protocol/odata-v4.0-os-part1-protocol.html#_Toc372793719) uvádí, že je upřednostňovaný opravy.

V každém případě zde je kód pro opravu a PUT metody:

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample11.cs)]

V případě opravy, využívá kontroler **Delta&lt;T&gt;**  typ sledovat změny.

## <a name="delete-an-entity"></a>Odstranění entity

Pokud chcete povolit klientům z databáze odstranit produkt, přidejte následující metodu do `ProductsController`.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample12.cs)]
