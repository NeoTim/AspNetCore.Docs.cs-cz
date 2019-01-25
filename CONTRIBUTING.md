# <a name="contribute-to-the-aspnet-documentation"></a>Přispívání do dokumentace k ASP.NET

Tento dokument popisuje proces pro přispívání do článků a ukázky kódu, které jsou hostované na [webu Dokumentace k ASP.NET](https://docs.microsoft.com/aspnet/). Máte překlep opravy a nové články jsou Vítá příspěvky.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Jak vytvořit jednoduchý opravy nebo návrh

Články jsou uložené v úložišti jako soubory Markdown. Jednoduchý obsah souboru Markdownu změny v prohlížeči tak, že vyberete **upravit** odkaz v pravém horním rohu okna prohlížeče. (V okně prohlížeče úzký, rozbalte **možnosti** panelu zobrazíte **upravit** propojení.) Postupujte podle pokynů k vytvoření žádosti o přijetí změn (žádost o přijetí změn). Můžeme se zkontrolujte žádosti o přijetí změn a přijmout nebo navrhnout změny.

## <a name="how-to-make-a-more-complex-submission"></a>Jak provádět složitější odeslání

Potřebujete základní znalosti o [Git a webu GitHub.com](https://guides.github.com/activities/hello-world/).

* Otevřít [problém](https://github.com/aspnet/Docs/issues/new) popisující, co chcete udělat, jako je například změna existujících článků nebo vytvoří nový. Požadujeme často obrys pro návrh nové téma. Čekání na schválení od týmu před investovat mnoho času.
* Fork [aspnet/Docs](https://github.com/aspnet/Docs/) úložiště a vytvořte si větev pro vaše změny.
* Odešlete žádost o přijetí změn do hlavní větve se změnami.
* Pokud vaše žádost o přijetí změn má popisek "cla vyžaduje" přiřazeno, [dokončit licenční smlouvě (CLA)](https://cla.dotnetfoundation.org/).
* Reakce na žádosti o přijetí změn zpětnou vazbu.

Příklad, ve kterém tento proces vedl k publikování nových článků, naleznete v tématu [problém &num;67](https://github.com/dotnet/docs/issues/67) a [žádostí o přijetí změn &num;798](https://github.com/dotnet/docs/pull/798) v úložišti dokumentace rozhraní .NET. Je nový článek [dokumentace kódu](https://docs.microsoft.com/dotnet/articles/csharp/codedoc).

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a>Rozšíření Docs Authoring Pack ve Visual Studio Code 

Pokud používáte Visual Studio Code pro přispívání do dokumentace technologie ASP.NET, může zvýšit produktivitu pomocí instalace [Docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) rozšíření. Toto rozšíření poskytuje celou řadu nástrojů, která pomáhá s Markdownu linting, kontrolu pravopisu kódu a šablon článků.

## <a name="markdown-syntax"></a>Syntaxe markdownu

Články jsou psané [DocFx flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), který je nadstavbou jazyka [– GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/). Příklady syntaxe DFM pro funkce uživatelského rozhraní se běžně používají v dokumentaci k ASP.NET, naleznete v tématu [šablona metadat a Markdownu](https://github.com/dotnet/docs/blob/master/styleguide/template.md) v průvodce správným stylem úložiště dokumentů .NET. 

## <a name="folder-structure-conventions"></a>Konvence strukturu složky

Pro každý soubor Markdownu může existovat složka pro bitové kopie a složku pro ukázkový kód. Pokud je v článku [fundamentals/configuration/index.md](https://github.com/aspnet/Docs/blob/master/aspnetcore/fundamentals/configuration/index.md), jsou obrázky v [základy/configuration/indexu/\_statické](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static) a soubory projektu ukázkové aplikace jsou v [ Základy/configuration/indexu/ukázkové](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample). Obrázek v *fundamentals/configuration/index.md* souboru je vykreslen metodou následující Markdown:

```
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

Všechny bitové kopie by měl mít [alternativní text (alt)](https://wikipedia.org/wiki/Alt_attribute). Rady k zadávání alternativní text, najdete v online prostředků, jako například [WebAIM: Alternativní Text](https://webaim.org/techniques/alttext/).

Používat malá písmena pro názvy souborů Markdown a názvy obrázkových souborů.

## <a name="internal-links"></a>Vnitřní propojení

Vnitřní propojení používejte `uid` článku cíl s odkazem pro odkazy xref (text odkazu je nastavena na název propojeného obsahu):

```
<xref:uid_of_the_topic>
```

Pokud název článku je vhodná pro text odkazu (například slova nebo fráze ve větě je text odkazu), zadejte odkazy xref odkaz a text odkazu s následujícími možnostmi:

```
[link text](xref:uid_of_the_topic)
```

Další informace najdete v tématu [DocFX křížový odkaz](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).

## <a name="images-and-screenshots"></a>Obrázky a snímky obrazovky

Nezahrnovat obrázky v článcích, s výjimkou:

* V kurzech základní registrace (začátečníky).
* Když je potřeba bitovou kopii pro přehlednost.

Tato omezení zmenšit velikost úložiště.

Jako volitelný krok Ujistěte se, že jsou komprimované všechny Image a snímky obrazovky v dokumentaci, což pomáhá s výkonu načítání souboru velikost a stránky. Zahrnují několik oblíbených nástrojů TinyPNG (pomocí [TinyPNG webu](https://tinypng.com/) nebo [TinyPNG API](https://tinypng.com/developers)) nebo [Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) rozšíření sady Visual Studio. 

## <a name="code-snippets"></a>Fragmenty kódu

Články obsahují často fragmenty kódu pro ilustraci body. DFM můžete zkopírovat kód do souboru Markdown nebo odkazovat na samostatném souboru kódu. Budeme chtít používat soubory samostatného kódu pokaždé, když je to možné, chcete-li minimalizovat pravděpodobnost vzniku chyby v kódu. Soubory kódu jsou uloženy v úložišti pomocí výše uvedeného popisu pro ukázkové projekty strukturu složek. 

Následující příklady znázorňují [syntaxe fragment kódu DFM](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) pro použití v *configuration/index.md* souboru.

Soubor celý kód vykreslení jako fragment kódu:

```
[!code-csharp[](configuration/index/sample/Program.cs)]
```

Vykreslení část souboru jako fragment kódu s použitím čísla řádků:

```
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

Pro C# fragmenty kódu, odkaz [ C# oblasti](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region). Kdykoli je to možné, používejte oblastech než čísla řádků, protože změnit a bude synchronizován s odkazy na čísla řádků v Markdownu mají tendenci čísla řádků v souboru kódu. C#oblasti mohou být vnořené. Pokud odkazuje na vnější oblasti vnitřní `#region` a `#endregion` direktivy se nevykreslí v fragment kódu. 

K vykreslení C# oblast s názvem "snippet_Example":

```
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

Zvýraznění vybraných řádků v vykreslené fragmentu kódu (obvykle zobrazí jako barva žlutým pozadím):

```
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a>Testování změn s DocFX

Otestujte provedené změny se [nástroj příkazového řádku DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), která vytvoří místně hostované verzi lokality. DocFX nevykreslí rozšíření stylu a lokality vytvořené pro web docs.microsoft.com.

DocFX vyžaduje:

* Windows rozhraní .NET framework.
* Mono pro Linux nebo macOS. 

### <a name="windows-instructions"></a>Pokyny pro Windows

* Stáhněte a rozbalte *docfx.zip* z [uvolní DocFX](https://github.com/dotnet/docfx/releases).
* DocFX přidáte do cesty.
* V okně příkazového řádku, přejděte do odpovídající složky obsahující *docfx.json* souboru (*aspnet* obsahu ASP.NET nebo *aspnetcore* obsahu ASP.NET Core ) a spusťte následující příkaz:

  ```
  docfx --serve
  ```
    
* V prohlížeči přejděte na `http://localhost:8080`.

### <a name="mono-instructions"></a>Pokyny k mono

* Instalace součásti Mono pomocí Homebrew:

  ```
  brew install mono
  ```
* Stáhněte si [nejnovější verzi DocFX](https://github.com/dotnet/docfx/releases).
* Extrahovat archiv do *$HOME/bin/docfx*.
* Vytvoření páru aliasy pro **docfx** v prostředí bash. První alias sloužící k sestavení v dokumentaci. Druhý alias umožňuje sestavovat a dodávat v dokumentaci.

  ```
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```
* Spustit `docfx` z kořene úložiště k vytvoření webu. Spustit `docfx-serve` zobrazíte na webu `http://localhost:8080`.

## <a name="voice-and-tone"></a>Pro hlasové hovory a tón

Naším cílem je napsat dokumentace, která je snadno srozumitelný nejširší možné cílovou skupinou. Za tímto účelem jsme zavedli pokyny pro zápis styl, který jsme postupujte podle našich přispěvatele žádáme o. Další informace najdete v tématu [pokyny pro hlasové hovory a tón](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) v úložišti .NET.

## <a name="microsoft-writing-style-guide"></a>Průvodce správným stylem psaní Microsoft

[Průvodce správným stylem psaní Microsoft](https://docs.microsoft.com/style-guide/welcome/) poskytuje zápis styl a terminologie pokyny pro všechny formy komunikace technologie, včetně dokumentace k ASP.NET Core.

## <a name="redirects"></a>Přesměrování

Pokud budou moct odstranit článek, změňte její název souboru nebo přesunout do jiné složky, vytvořte přesměrování tak, aby nepřijímají lidí, kteří vytvořili záložku na článek *404 Nenalezeno* chyby. Přidat provede přesměrování [hlavní soubor přesměrování](https://github.com/aspnet/Docs/blob/master/.openpublishing.redirection.json).
