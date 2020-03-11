# <a name="contribute-to-the-aspnet-core-documentation"></a>Přispívání do dokumentace k ASP.NET Core

Tento dokument popisuje proces pro přispívání do článků a ukázek kódu, které jsou hostovány na [webu dokumentace ASP.NET](https://docs.microsoft.com/aspnet/). Překlepné opravy a nové články jsou příspěvky v uvítání.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Jak provést jednoduchou opravu nebo návrh

Články jsou uloženy v úložišti jako soubory Markdownu. Jednoduché změny obsahu souboru Markdownu se provedou v prohlížeči výběrem odkazu **Upravit** v pravém horním rohu okna prohlížeče. (V úzkém okně prohlížeče rozbalte panel **Možnosti** , aby se zobrazil odkaz **Upravit** .) Podle pokynů vytvořte žádost o přijetí změn (PR). Žádost o přijetí změn zkontrolujeme a přijmeme nebo navrhneme.

## <a name="how-to-make-a-more-complex-submission"></a>Jak vytvořit komplexnější odesílání

Budete potřebovat základní porozumění [Gitu a GitHub.com](https://guides.github.com/activities/hello-world/).

* Otevřete [problém](https://github.com/dotnet/AspNetCore.Docs/issues/new) , který popisuje, co chcete udělat, jako je například Změna existujícího článku nebo vytvoření nového. Často si vyžádáme osnovu pro nový návrh tématu. Než budete investovat spoustu času, počkejte na schválení od týmu.
* Rozvětvení úložiště [ASPNET/docs](https://github.com/dotnet/AspNetCore.Docs/) a vytvoření větve pro změny.
* Odešlete žádost o přijetí změn do hlavní větve se změnami.
* Pokud má vaše žádost o přijetí změn přiřazený popisek cla – požadováno, [dokončete licenční smlouvu s příspěvkem (cla)](https://cla.dotnetfoundation.org/).
* Reakce na zpětnou vazbu od PR

Příklad, kdy tento proces vedl k publikování nového článku, najdete v tématu [problém &num;67](https://github.com/dotnet/docs/issues/67) a [žádost o přijetí změn &num;798](https://github.com/dotnet/docs/pull/798) v úložišti docs .NET. Nový článek [dokumentuje kód](https://docs.microsoft.com/dotnet/articles/csharp/codedoc).

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a>Rozšíření docs Authoring Pack v Visual Studio Code

Pokud Visual Studio Code přispějete k dokumentaci k ASP.NET, můžete zvýšit svou produktivitu tím, že nainstalujete rozšíření pro [vytváření obsahu pro Docs](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) . Rozšíření poskytuje celou řadu nástrojů, které pomáhají Markdownu linting, kontrolu pravopisu kódu a šablony článků.

## <a name="markdown-syntax"></a>Syntaxe Markdownu

Články jsou napsané v [DocFx Markdownu](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), což je nadmnožina Markdownu s [charakterem (GFM)](https://guides.github.com/features/mastering-markdown/). Příklady syntaxe DFM pro funkce uživatelského rozhraní, které se běžně používají v dokumentaci k ASP.NET, najdete v tématu [metadata a šablony Markdownu](https://github.com/dotnet/docs/blob/master/styleguide/template.md) v příručce ke stylům úložiště dokumentů .NET. 

## <a name="folder-structure-conventions"></a>Konvence struktury složek

Pro každý soubor Markdownu může existovat složka pro obrázky a složka pro vzorový kód. Pokud je článek [základy/konfigurace/index. MD](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/fundamentals/configuration/index.md), obrázky jsou v části [základy/konfigurace/index/\_statické](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static) a ukázkové soubory projektu aplikace jsou v podstatě [základy/konfigurace/index/ukázka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample). Obrázek v souboru *základů/konfigurace/index. MD* je vykreslený pomocí následujících Markdownu:

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

Všechny obrázky by měly mít [alternativní text (ALT)](https://wikipedia.org/wiki/Alt_attribute). Rady týkající se zadání alternativního textu najdete v tématu online zdroje, jako je například [WebAIM: alternativní text](https://webaim.org/techniques/alttext/).

Pro názvy souborů Markdownu a názvy souborů obrázků používejte malá písmena.

## <a name="internal-links"></a>Interní odkazy

Interní odkazy by měly používat `uid` cílového článku s odkazem na odkazy XREF (text odkazu je nastaven na název propojeného obsahu):

```md
<xref:uid_of_the_topic>
```

Pokud název článku není vhodný pro text odkazu (například slovo nebo fráze ve větě je text odkazu), zadejte odkaz odkazy XREF a text odkazu následujícím způsobem:

```md
[link text](xref:uid_of_the_topic)
```

Další informace najdete v tématu [DocFX-reference](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).

## <a name="images-and-screenshots"></a>Obrázky a snímky obrazovky

Nezahrnovat obrázky s články, s výjimkou:

* V kurzech pro základní připojování (začátečník).
* Když je obrázek potřebný k přehlednost.

Tato omezení omezují velikost úložiště.

V případě volitelného kroku zajistěte, aby všechny image a snímky obrazovky používané v dokumentaci byly komprimované, což pomáhá s velikostí souborů a výkonem zatížení stránky. Mezi několik oblíbených nástrojů patří TinyPNG (pomocí [webu TinyPNG](https://tinypng.com/) nebo [rozhraní TinyPNG API](https://tinypng.com/developers)) nebo rozšíření pro [optimalizaci image](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) sady Visual Studio. 

## <a name="code-snippets"></a>Fragmenty kódu

Články často obsahují fragmenty kódu pro ilustraci bodů. DFM umožňuje zkopírovat kód do souboru Markdownu nebo použít odkaz na samostatný soubor kódu. Doporučujeme používat samostatné soubory kódu, kdykoli je to možné, k minimalizaci pravděpodobnosti chyb v kódu. Soubory kódu jsou uloženy v úložišti pomocí struktury složky popsané dříve pro ukázkové projekty. 

Následující příklady ilustrují [syntaxi fragmentu kódu DFM](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) pro použití v souboru *Configuration/index. MD* .

Vykreslení celého souboru kódu jako fragmentu:

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

Vykreslení části souboru jako fragmentu pomocí čísel řádků:

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

Pro C# fragmenty kódu odkaz na [ C# oblast](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region). Kdykoli je to možné, používejte oblasti místo čísel řádků, protože čísla řádků v souboru kódu se obvykle mění a jsou nesynchronizovaná s odkazy na řádky v Markdownu. C#oblasti můžou být vnořené. Pokud se odkazuje na vnější oblast, vnitřní `#region` a direktivy `#endregion` nejsou vykresleny ve fragmentu. 

Vykreslení C# oblasti s názvem "snippet_Example":

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

Zvýraznění vybraných řádků ve vykresleném fragmentu (obvykle vykresleno jako žlutá barva pozadí):

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a>Testování změn pomocí DocFX

Otestujte změny pomocí [nástroje příkazového řádku DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), který vytvoří místně hostovanou verzi lokality. DocFX nevykresluje styl a rozšíření webu vytvořená pro docs.microsoft.com.

DocFX vyžaduje:

* .NET Framework ve Windows.
* Mono pro Linux nebo macOS. 

### <a name="windows-instructions"></a>Pokyny pro Windows

* Stáhněte a rozbalte *docfx. zip* ze [docfx verzí](https://github.com/dotnet/docfx/releases).
* Přidejte k cestě DocFX.
* V příkazovém prostředí přejděte do složky, která obsahuje soubor *docfx. JSON* (*ASPNET* pro ASP.NET obsah nebo *aspnetcore* pro obsah ASP.NET Core), a spusťte následující příkaz:

  ```console
  docfx --serve
  ```

* V prohlížeči přejděte na `http://localhost:8080/group1-dest/`.

### <a name="mono-instructions"></a>Pokyny mono

* Instalace mono přes homebrew:

  ```console
  brew install mono
  ```

* Stáhněte si [nejnovější verzi DocFX](https://github.com/dotnet/docfx/releases).
* Extrahujte archiv a *$Home/bin/docfx*.
* Vytvořte dvojici aliasů pro **docfx** v prostředí bash. První alias se používá k sestavení dokumentace. Druhý alias se používá k sestavení a obsluze dokumentace.

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* V příkazovém prostředí přejděte do složky, která obsahuje soubor *docfx. JSON* (*ASPNET* pro ASP.NET obsah nebo *aspnetcore* pro obsah ASP.NET Core), a spuštěním následujícího příkazu Sestavte a doručovat dokumentaci pomocí jejího aliasu:

  ```console
  docfx-serve
  ```

* V prohlížeči přejděte na `http://localhost:8080/group1-dest/`.

## <a name="voice-and-tone"></a>Hlas a tón

Naším cílem je napsat dokumentaci, kterou snadno pochopíte nejširší možnou cílovou skupinu. Za tímto účelem jsme zřídili pokyny pro psaní stylu, který požádáme, aby naši přispěvatelé sledovali. Další informace najdete v tématu [pokyny pro hlasové a tónové](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) úložiště v rozhraní .NET.

## <a name="microsoft-writing-style-guide"></a>Průvodce stylem psaní pro Microsoft

[Příručka pro psaní stylu společnosti Microsoft](https://docs.microsoft.com/style-guide/welcome/) obsahuje pokyny pro psaní stylu a terminologie pro všechny formy komunikace technologie, včetně dokumentace ASP.NET Core.

## <a name="redirects"></a>Přesměruje

Pokud odstraníte článek, změníte jeho název nebo ho přesunete do jiné složky, vytvoříte přesměrování tak, aby lidé, kteří si tento článek přesunuli, neobdrželi chybu *404, která nebyla nalezena* . Přidejte přesměrování do [hlavního souboru přesměrování](https://github.com/dotnet/AspNetCore.Docs/blob/master/.openpublishing.redirection.json).
