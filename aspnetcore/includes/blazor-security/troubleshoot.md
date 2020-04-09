## <a name="troubleshoot"></a>Řešení potíží

### <a name="cookies-and-site-data"></a>Soubory cookie a údaje o webu

Soubory cookie a data webu mohou přetrvávat v rámci aktualizací aplikací a zasahovat do testování a odstraňování problémů. Zrušte následující položky při provádění změn kódu aplikace, změn uživatelského účtu u poskytovatele nebo změn konfigurace aplikace zprostředkovatele:

* Soubory cookie pro přihlášení uživatelů
* Soubory cookie aplikací
* Uložená a uložená data webu uložená v mezipaměti

Jedním z přístupů, jak zabránit tomu, aby přetrvávající soubory cookie a data webu zasahovaly do testování a odstraňování problémů, je:

* Pro testování, které můžete nakonfigurovat tak, aby při každém zavření prohlížeče odstranili všechna data souborů cookie a webu, použijte prohlížeč, který můžete nakonfigurovat tak, aby odstranil všechna data souborů cookie a webu.
* Zavřete prohlížeč mezi jakoukoli změnou konfigurace aplikace, testovacího uživatele nebo zprostředkovatele.

### <a name="run-the-server-app"></a>Spuštění aplikace Server

Při testování a řešení potíží s hostovkou aplikace Blazor se ujistěte, že aplikaci spouštěte z projektu **Server.** Například v sadě Visual Studio před spuštěním aplikace s některou z následujících přístupů zkontrolujte, že projekt serveru je zvýrazněn v **Průzkumníkovi řešení:**

* Vyberte tlačítko **Spustit**.
* Použijte **ladění** > **start ladění** z nabídky.
* Stiskněte <kbd>klávesu F5</kbd>.
