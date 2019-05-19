# <a name="additional-claims-sample-app"></a>Další deklarace identity ukázkové aplikace

Ukázková aplikace předvádí, jak:

* Získat křestní jméno a příjmení uživatele z Googlu a ukládat deklarace názvu s hodnotami poskytované Google.
* Přístupový token Google Store v uživatele `AuthenticationProperties`.

Použití ukázkové aplikace:

1. Aplikace registrovaly a získaly platné ID klienta a tajný kód klienta pro ověřování Google. Další informace najdete v tématu [nastavení externí přihlášení Google](https://docs.microsoft.com/aspnet/core/security/authentication/social/google-logins).
1. Zadejte ID klienta a tajný kód klienta aplikace v [GoogleOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) z `Startup.ConfigureServices`.
1. Spusťte aplikaci a požadavku na stránku Moje deklarací identity. Když uživatel není přihlášen, aplikace přesměruje do Googlu. Přihlásit se přes Google. Google přesměruje uživatele zpět do aplikace (`/MyClaims`). Ověření uživatele a načtení stránky Moje deklarací identity. Křestní jméno a příjmení deklarace identity se nacházejí v části **deklarace identity uživatelů** Google zadanými hodnotami. Přístupový token se zobrazí v části **vlastnosti ověřování**.
