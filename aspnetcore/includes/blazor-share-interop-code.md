## <a name="share-interop-code-in-a-class-library"></a>Sdílení kódu interop v knihovně tříd

JS interop kód může být součástí knihovny tříd, který umožňuje sdílet kód v balíčku NuGet.

Knihovna tříd zpracovává vkládání prostředků JavaScriptu do sestaveného sestavení. Soubory JavaScriptu jsou umístěny ve složce *wwwroot.* Nástroj se postará o vkládání prostředků při budování knihovny.

Vytvořený balíček NuGet je odkazován v souboru projektu aplikace stejným způsobem, že je odkazováno na libovolný balíček NuGet. Po obnovení balíčku může kód aplikace volat do JavaScriptu, jako by to bylo C#.

Další informace naleznete v tématu <xref:blazor/class-libraries>.
