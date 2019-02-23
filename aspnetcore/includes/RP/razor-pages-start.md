Výchozí šablona vytvoří **RazorPagesMovie**, **Domů**, **o** a **kontakt** odkazy a stránky. V závislosti na velikosti okna prohlížeče můžete potřebovat klikněte na navigačním ikonu zobrazíte odkazy.

![Index nebo Domovská stránka](../../tutorials/razor-pages/razor-pages-start/_static/home2.png)

Otestujte odkazy. **RazorPagesMovie** a **Domů** odkazů přejít na indexovou stránku. **o** a **kontakt** odkazů přejít na `About` a `Contact` stránky v uvedeném pořadí.

## <a name="project-files-and-folders"></a>Projektových souborů a složek

Následující tabulka uvádí soubory a složky v projektu. Pro účely tohoto kurzu *Startup.cs* soubor je nejdůležitější pochopit. Není nutné zkontrolovat každý odkaz níže uvedené. Odkazy jsou uvedeny jako odkaz, pokud potřebujete další informace o souboru nebo složky v projektu.

| Soubor nebo složku              | Účel |
| ----------------- | ------------ |
| wwwroot | Obsahuje statické soubory. Zobrazit [statické soubory](xref:fundamentals/static-files). |
| Stránky | Složka pro [stránky Razor](xref:razor-pages/index). |
| *appsettings.json* | [Konfigurace](xref:fundamentals/configuration/index) |
| *Program.cs* | [Hostitelé](xref:fundamentals/index#host) aplikace ASP.NET Core.|
| *Startup.cs* | Nakonfiguruje služby a kanál žádosti. Zobrazit [spuštění](xref:fundamentals/startup).|

### <a name="the-pages-folder"></a>Složka stránek

*_Layout.cshtml* soubor obsahuje společné elementy HTML (skripty a šablony stylů) a nastaví rozložení pro aplikaci. Například když kliknete na **RazorPagesMovie**, **Domů**, **o** nebo **kontakt**, se zobrazí stejné prvky. Společné prvky patří navigační nabídce na horní a záhlaví v dolní části okna. Zobrazit [rozložení](xref:mvc/views/layout) Další informace.

*_ViewImports.cshtml* soubor obsahuje direktivy Razor, které jsou importovány do každé stránky Razor. Zobrazit [direktivy import sdílených](xref:mvc/views/layout#importing-shared-directives) Další informace.

*Soubor _ViewStart.cshtml* nastaví stránky Razor `Layout` použít vlastnost *_Layout.cshtml* souboru. Zobrazit [rozložení](xref:mvc/views/layout) Další informace.

*_ValidationScriptsPartial.cshtml* soubor obsahuje odkaz na [jQuery](https://jquery.com/) skripty pro ověření. Pokud přidáme `Create` a `Edit` stránky později v tomto kurzu *_ValidationScriptsPartial.cshtml* soubor se použije.

`About`, `Contact` a `Index` stránky jsou basic můžete použít ke spuštění aplikace. `Error` Stránky se používá k zobrazení informací o chybách. `Privacy` Stránce můžete zadat podrobnosti o zásadách ochrany osobních údajů vašeho webu.
