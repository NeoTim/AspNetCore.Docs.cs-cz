# <a name="key-vault-configuration-provider-sample-app"></a>Ukázková aplikace pro poskytovatele konfigurace Key Vault

Tato ukázka znázorňuje použití poskytovatele konfigurace Azure Key Vault.

Ukázka se spustí v jednom ze dvou režimů určených `#define` příkazem v horní části souboru *program.cs* . Pokyny naleznete v tématu [direktivy preprocesoru v ukázkovém kódu](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code):

* `Certificate`: Demonstruje použití Azure Key Vault ID klienta a certifikátu X. 509 pro přístup k tajným klíčům uloženým v Azure Key Vault. Tuto verzi ukázky můžete spustit z libovolného umístění, nasadit do Azure App Service nebo libovolného hostitele, který podporuje aplikaci ASP.NET Core.
* `Managed`: Ukazuje, jak používat [Identita spravované služby](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Azure k ověření aplikace pro Azure Key Vault s ověřováním Azure AD bez přihlašovacích údajů v kódu nebo konfiguraci aplikace. Aby se aplikace mohla ověřit pomocí Azure Key Vault, ID a tajný kód klienta Azure AD se nevyžadují. Tato ukázka musí být nasazená, Azure App Service aby bylo možné prozkoumat spravovanou scearnio identity.

Další informace najdete v tématu [Poskytovatel konfigurace Azure Key Vault](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration).
