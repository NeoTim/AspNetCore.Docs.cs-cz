# <a name="contribute-to-the-aspnet-core-documentation"></a>Přispějte do ASP.NET základní dokumentace

Tento dokument se týká procesu přispívání do článků a ukázky kódu, které jsou hostovány na [webu dokumentace ASP.NET](https://docs.microsoft.com/aspnet/). Typo opravy a nové články jsou vítány příspěvky.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Jak provést jednoduchou opravu nebo návrh

Články jsou uloženy v úložišti jako markdown soubory. Jednoduché změny obsahu souboru Markdown se provádějí v prohlížeči výběrem odkazu **Upravit** v pravém horním rohu okna prohlížeče. (V úzkém okně prohlížeče rozbalte panel **voleb,** abyste viděli odkaz **Upravit.)** Podle pokynů vytvořte žádost o přijetí vzbuzování (PR). Pr přezkoumáme a přijmeme ho nebo navrhneme změny.

## <a name="how-to-make-a-more-complex-submission"></a>Jak provést složitější podání

Potřebujete základní znalostgitu [a GitHub.com](https://guides.github.com/activities/hello-world/).

* Otevřete [problém](https://github.com/dotnet/AspNetCore.Docs/issues/new) popisující, co chcete udělat, například změnu existujícího článku nebo vytvoření nového. Často požadujeme osnovu pro nový návrh tématu. Než investujete mnoho času, počkejte na schválení od týmu.
* Rozvětvení [aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/) repo a vytvořit větev pro vaše změny.
* Odeslat PR master s vašimi změnami.
* Pokud je vašemu PR přiřazen štítek "cla-required", [vyplňte smlouvu o příspěvku (CLA)](https://cla.dotnetfoundation.org/).
* Reagujte na zpětnou vazbu PR.

Příklad, kde tento proces vedl ke zveřejnění nového článku, naleznete [v tématu &num;Problém 67](https://github.com/dotnet/docs/issues/67) a žádost o přijetí [vyžádat &num;798](https://github.com/dotnet/docs/pull/798) v úložišti .NET Docs. Nový článek je [dokumentování kódu](https://docs.microsoft.com/dotnet/articles/csharp/codedoc).

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a>Rozšíření sady Docs Authoring Pack v kódu Sady Visual Studio

Pokud používáte Visual Studio Code k přispívání do dokumentace ASP.NET, můžete zvýšit svou produktivitu instalací rozšíření [Docs Authoring Pack.](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) Rozšíření poskytuje celou řadu nástrojů, které pomáhají s linting Markdown, kontrola pravopisu kódu a šablony článku.

## <a name="markdown-syntax"></a>Syntaxe značky

Články jsou napsány v [DocFx-ochucené Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), což je nadmnožina [GitHub-ochucené Markdown (GFM)](https://guides.github.com/features/mastering-markdown/). Příklady syntaxe DFM pro funkce uživatelského rozhraní běžně používané v dokumentaci ASP.NET naleznete v [tématu Metadata a Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) v průvodci stylem repo dokumentů .NET. 

## <a name="folder-structure-conventions"></a>Konvence struktury složek

Pro každý soubor Markdown může existovat složka pro obrázky a složka pro ukázkový kód. Pokud je článek [fundamentals/configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/fundamentals/configuration/index.md), obrázky jsou v [základech/konfigurace/index/\_statické](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static) a ukázkové soubory projektu aplikace jsou v [základech/konfigurace/index/ukázka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample). Obraz v souboru *fundamentals/configuration/index.md* je vykreslen následujícím Markdownem:

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

Všechny obrázky by měly mít [alternativní (alt) text](https://wikipedia.org/wiki/Alt_attribute). Informace o zadání alternativního textu naleznete v online zdrojích, například [WebAIM: Alternativní text](https://webaim.org/techniques/alttext/).

Pro názvy souborů Markdown a názvy obrazových souborů použijte malá písmena.

## <a name="internal-links"></a>Interní odkazy

Interní odkazy by `uid` měly používat cílový článek s odkazem externí reference (text odkazu je nastaven na název propojeného obsahu):

```md
<xref:uid_of_the_topic>
```

Pokud název článku není vhodný pro text odkazu (například slovo nebo fráze ve větě je text odkazu), zadejte odkaz externí reference a text propojení s následujícím:

```md
[link text](xref:uid_of_the_topic)
```

Další informace naleznete v [křížovém odkazu DocFX](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).

## <a name="images-and-screenshots"></a>Obrázky a snímky obrazovky

Nezahrnejte obrázky s články, s výjimkou:

* V základních onboarding (začátečník) tutoriály.
* Pokud je obraz potřebný pro přehlednost.

Tato omezení zmenšit velikost úložiště.

Jako volitelný krok zajistěte, aby byly komprimovány všechny obrázky a snímky obrazovky použité v dokumentaci, což pomáhá s velikostí souboru a výkonem načítání stránky. Několik populárních nástrojů patří TinyPNG (pomocí [tinypng webové stránky](https://tinypng.com/) nebo [TinyPNG API)](https://tinypng.com/developers)nebo [rozšíření Pro optimalizace obrazu](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) Visual Studio. 

## <a name="code-snippets"></a>Fragmenty kódu

Články často obsahují fragmenty kódu pro ilustraci bodů. DFM umožňuje kopírovat kód do souboru Markdown nebo odkazovat na samostatný soubor kódu. Dáváme přednost použití samostatných souborů kódu, kdykoli je to možné, abychom minimalizovali pravděpodobnost chyb v kódu. Soubory kódu jsou uloženy v repo pomocí struktury složek popsané dříve pro ukázkové projekty. 

Následující příklady ilustrují [syntaxi fragmentu kódu DFM](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) pro použití v souboru *configuration/index.md.*

Vykreslení celého souboru kódu jako fragmentu kódu:

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

Vykreslení části souboru jako fragmentu pomocí čísel řádků:

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

Pro fragmenty jazyka C# odkaz na [oblast Jazyka C#](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region). Kdykoli je to možné, použijte oblasti místo čísel řádků, protože čísla řádků v souboru kódu mají tendenci se měnit a jsou nesynchronizovaná s odkazy na čísla řádků v Markdownu. C# oblasti mohou být vnořeny. Pokud odkazuje na vnější oblasti, vnitřní `#region` a `#endregion` direktivy nejsou vykresleny ve výstřižku. 

Vykreslení oblasti jazyka C# s názvem "snippet_Example":

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

Zvýraznění vybraných čar v vykresleném úryvku (obvykle se vykresluje jako žlutá barva pozadí):

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a>Testování změn pomocí DocFX

Otestujte změny pomocí [nástroje příkazového řádku DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), který vytvoří místně hostovkou verzi webu. DocFX nevykresluje styl a rozšíření webu vytvořená pro docs.microsoft.com.

DocFX vyžaduje:

* Rozhraní .NET Framework v systému Windows.
* Mono pro Linux nebo macOS. 

### <a name="windows-instructions"></a>Pokyny pro Windows

* Stáhnout a rozbalit *docfx.zip* z [DocFX vydání](https://github.com/dotnet/docfx/releases).
* Přidejte docFX do cesty.
* V příkazovém prostředí přejděte do složky, která obsahuje soubor *docfx.json* (*aspnet* pro obsah ASP.NET nebo *aspnetcore* pro obsah ASP.NET Core) a spusťte následující příkaz:

  ```console
  docfx --serve
  ```

* V prohlížeči přejděte na . `http://localhost:8080/group1-dest/`

### <a name="mono-instructions"></a>Mono instrukce

* Nainstalujte Mono přes Homebrew:

  ```console
  brew install mono
  ```

* Stáhněte si [nejnovější verzi DocFX](https://github.com/dotnet/docfx/releases).
* Extrahovat archiv na *$HOME/bin/docfx*.
* Vytvořte pár aliasů pro **docfx** v bash shellu. První alias se používá k vytvoření dokumentace. Druhý alias se používá k sestavení a poskytování dokumentace.

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* V příkazovém prostředí přejděte do složky, která obsahuje soubor *docfx.json* (*aspnet* pro obsah ASP.NET nebo *aspnetcore* pro obsah ASP.NET Core) a spusťte následující příkaz, který vytvoří a bude sloužit dokumentům prostřednictvím jeho aliasu:

  ```console
  docfx-serve
  ```

* V prohlížeči přejděte na . `http://localhost:8080/group1-dest/`

## <a name="voice-and-tone"></a>Hlas a tón

Naším cílem je napsat dokumentaci, která je snadno srozumitelná co nejširšímu publiku. Za tímto účelem jsme stanovili pokyny pro styl psaní, které žádáme naše přispěvatele, aby se řídili. Další informace naleznete v [tématu Hlasové a tónpokyny](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) v .NET úložiště.

## <a name="microsoft-writing-style-guide"></a>Průvodce správným stylem psaní Microsoft

[Průvodce stylem psaní společnosti Microsoft](https://docs.microsoft.com/style-guide/welcome/) poskytuje styl psaní a terminologii pokyny pro všechny formy komunikace technologií, včetně dokumentace ASP.NET Core.

## <a name="redirects"></a>Přesměrování

Pokud odstraníte článek, změníte jeho název nebo ho přesunete do jiné složky, vytvořte přesměrování, aby se lidem, kteří článek se záložkou nevytvořili, nezoniciovala chyba *404 Not Found.* Přidejte přesměrování do [hlavního souboru přesměrování](https://github.com/dotnet/AspNetCore.Docs/blob/master/.openpublishing.redirection.json).
