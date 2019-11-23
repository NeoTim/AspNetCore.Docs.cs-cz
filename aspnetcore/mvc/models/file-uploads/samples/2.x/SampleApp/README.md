# <a name="upload-files-sample-app"></a>Ukázková aplikace pro nahrání souborů

Tato ukázková aplikace ukazuje koncepty popsané v tématu [nahrávání souborů v ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads) .

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní. Útočníci mohou provádět útoky [s cílem odepření služeb](/windows-hardware/drivers/ifs/denial-of-service) , pokus o nahrání virů nebo malwaru nebo pokus o ohrožení sítí a serverů jinými způsoby.

Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:

* Nahrajte soubory do vyhrazené oblasti pro nahrání souborů v systému, nejlépe pro nesystémovou jednotku. Použití vyhrazeného umístění usnadňuje ukládání bezpečnostních opatření do odesílaných souborů. Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru.&dagger;
* Neuchovávat nahrané soubory ve stejném adresářovém stromu jako aplikace.&dagger;
* Použijte název bezpečného souboru určený aplikací. Nepoužívejte název souboru poskytnutý uživatelským vstupem nebo nedůvěryhodného názvu odeslaného souboru.&dagger;
* Povoluje pouze konkrétní sadu schválených přípon souborů.&dagger;
* Ověřte podpis formátu souboru, abyste zabránili uživateli v nahrávání maskovaných souborů (například nahrání souboru *. exe* s příponou *. txt* ).&dagger;
* Ověřte, zda jsou na serveru provedeny také kontroly na straně klienta. Kontroly na straně klienta je snadné obejít.&dagger;
* Ověřte velikost nahrávání a zabraňte nahrávání, které jsou větší, než se očekávalo.&dagger;
* Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.
* **Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**

&dagger;ukázková aplikace ukazuje přístup, který splňuje kritéria.

> [!WARNING]
> Nahrává se škodlivý kód do systému je často prvním krokem při provádění kódu, který můžete:
>
> * Úplné převzetí systému.
> * Přetížit systém s výsledkem, že dojde k chybě systému.
> * Ohrozit data uživatele nebo systému.
> * Použijte graffiti pro veřejné uživatelské rozhraní.
>
> Informace o omezení možností útoku, při přijetí soubory od uživatelů najdete v následujících zdrojích:
>
> * [Nahrání souboru bez omezení](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [Zabezpečení Azure: Ujistěte se, že odpovídající ovládací prvky jsou na místě při přijetí soubory od uživatelů](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Další informace najdete v tématu [nahrání souborů v ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads).

## <a name="how-to-use-the-sample"></a>Jak používat ukázku

V souboru *appSettings. JSON* :

1. Nastavte cestu pro uložené soubory (`StoredFilesPath`).

   * Ukázková aplikace nastaví hodnotu na `c:\\files`, což předpokládá, že složka s názvem *Files* existuje v kořenu jednotky C:.
   * Cesta musí existovat. Vytvořte složku *soubory* na jednotce C: a nastavte cestu na vhodné umístění.
   * Proces aplikace vyžaduje pro cestu oprávnění ke čtení a zápisu.
   * **VÝZNAMNÁ!** Zakažte oprávnění EXECUTE pro všechny uživatele v cestě.

1. Nastavte limit velikosti souboru (`FileSizeLimit`) v bajtech. Výchozí hodnota `2097152` (2 097 152 bajtů) ukázkové aplikace povoluje nahrávání souborů až do velikosti 2 MB.
