## <a name="share-interop-code-in-a-class-library"></a>Sdílení kódu spolupráce v knihovně tříd

Kód interoperability JS lze zahrnout do knihovny tříd, která umožňuje sdílení kódu v balíčku NuGet.

Knihovna tříd zpracovává vložení prostředků JavaScriptu do sestaveného sestavení. Soubory JavaScriptu jsou umístěny ve složce *wwwroot* . Nástroj při sestavování knihovny postará o vkládání prostředků.

Na sestavený balíček NuGet se odkazuje v souboru projektu aplikace stejným způsobem, jako na který se odkazuje na balíček NuGet. Po obnovení balíčku kód aplikace může zavolat do JavaScriptu, jako kdyby byl C#.

Další informace naleznete v tématu <xref:blazor/class-libraries>.
