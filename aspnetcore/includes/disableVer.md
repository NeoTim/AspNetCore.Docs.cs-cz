<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>Zakázat ověřování výchozího účtu

S výchozími šablonami se uživatel přesměruje na `Account.RegisterConfirmation` místo, kde může vybrat odkaz pro potvrzení účtu. Ve výchozím nastavení `Account.RegisterConfirmation` se používá ***jenom*** pro testování. v produkční aplikaci by se mělo zakázat automatické ověření účtu.

Pokud chcete vyžadovat potvrzený účet a zabránit okamžitému přihlášení při registraci, nastavte `DisplayConfirmAccountLink = false` v */areas/identity/Pages/Account/RegisterConfirmation.cshtml.cs*:

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]