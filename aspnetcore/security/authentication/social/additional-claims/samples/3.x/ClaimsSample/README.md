# <a name="additional-claims-sample-app"></a>Ukázková aplikace s dalšími deklaracemi

Ukázková aplikace ukazuje, jak:

* Získá jméno a příjmení uživatele z Google a uloží deklarace identity s hodnotami poskytnutými Google.
* Uložte token Google Access do @no__t uživatele-0.

Použití ukázkové aplikace:

1. Zaregistrujte aplikaci a získejte platné ID klienta a tajný klíč klienta pro ověřování Google. Další informace najdete v tématu [Nastavení externího přihlášení Google](https://docs.microsoft.com/aspnet/core/security/authentication/social/google-logins).
1. Zadejte ID klienta a tajný klíč klienta k aplikaci v [GoogleOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) `Startup.ConfigureServices`.
1. Spusťte aplikaci a vyžádejte si stránku moje deklarace. Když uživatel není přihlášený, aplikace přesměruje na Google. Přihlaste se pomocí Google. Google přesměruje uživatele zpátky do aplikace (`/MyClaims`). Uživatel je ověřený a je načtena stránka moje deklarace. Dané deklarace identity a příjmení se nacházejí v části **deklarace identity uživatelů** s hodnotami poskytovanými Google. Přístupový token se zobrazí v části **vlastnosti ověřování**.
