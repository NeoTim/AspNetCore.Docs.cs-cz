## <a name="troubleshoot"></a>Řešení potíží

### <a name="cookies-and-site-data"></a>Soubory cookie a data webu

Soubory cookie a data lokality můžou trvat mezi aktualizacemi aplikací a rušit testování a řešit potíže. Při provádění změn kódu aplikace zrušte zaškrtnutí těchto políček, změní se uživatelský účet se zprostředkovatelem nebo se změní konfigurace aplikace poskytovatele:

* Soubory cookie pro přihlašování uživatelů
* Soubory cookie aplikace
* Data uložených v mezipaměti a uložená data webu

Jedním z způsobů, jak zabránit kolizím souborů cookie a dat lokality při testování a řešení potíží, je:

* Konfigurace prohlížeče
  * Použijte prohlížeč pro testování, který můžete nakonfigurovat tak, aby při každém zavření prohlížeče odstranil všechna data cookie a data lokality.
  * Ujistěte se, že je prohlížeč uzavřený ručně nebo pomocí integrovaného vývojového prostředí (IDE) mezi jakoukoli změnou aplikace, testovacího uživatele nebo zprostředkovatele.
* Použití vlastního příkazu k otevření prohlížeče v anonymním nebo privátním režimu v aplikaci Visual Studio:
  * Otevřete dialogové okno **Procházet s pomocí** tlačítka **Spustit** v aplikaci Visual Studio.
  * Vyberte tlačítko **Přidat** .
  * Zadejte cestu k prohlížeči do pole **program** . Následující cesty ke spustitelným složkám jsou typická umístění instalace pro Windows 10. Pokud je váš prohlížeč nainstalovaný v jiném umístění nebo nepoužíváte Windows 10, zadejte cestu ke spustitelnému souboru prohlížeče.
    * Microsoft Edge:`C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
    * Google Chrome:`C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`
    * Mozilla Firefox:`C:\Program Files\Mozilla Firefox\firefox.exe`
  * Do pole **argumenty** zadejte parametr příkazového řádku, který prohlížeč používá pro otevření v anonymním nebo privátním režimu. Některé prohlížeče vyžadují adresu URL aplikace.
    * Microsoft Edge:`-inprivate`
    * Google Chrome:`--incognito --new-window https://localhost:5001`
    * Mozilla Firefox:`-private -url https://localhost:5001`
  * Do pole **popisný název** zadejte název. Například, `Firefox Auth Testing`.
  * Vyberte tlačítko **OK**.
  * Chcete-li se vyhnout nutnosti vybrat profil prohlížeče pro každou iteraci testování s aplikací, nastavte profil jako výchozí pomocí tlačítka **nastavit jako výchozí** .
  * Ujistěte se, že je prohlížeč uzavřený pomocí integrovaného vývojového prostředí (IDE) mezi každou změnou aplikace, testovacího uživatele nebo konfigurace poskytovatele.

### <a name="run-the-server-app"></a>Spuštění serverové aplikace

Při testování a řešení potíží s hostovanou aplikací Blazor se ujistěte, že jste spustili aplikaci z **`Server`** projektu. Například v aplikaci Visual Studio ověřte, že je serverový projekt zvýrazněný v **Průzkumník řešení** před spuštěním aplikace s některým z následujících přístupů:

* Vyberte tlačítko **Spustit**.
* Použijte **ladění**  >  **Spustit ladění** z nabídky.
* Stiskněte klávesu <kbd>F5</kbd>.

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>Kontrola obsahu JSON Web Token (JWT)

K dekódování JSON Web Token (JWT) použijte nástroj Microsoft 's [JWT.MS](https://jwt.ms/) Tool. Hodnoty v uživatelském rozhraní nikdy nenechávají prohlížeč.
