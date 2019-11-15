::: moniker range=">= aspnetcore-3.0"
Visual Studio zobrazí následující dialog:

![Tento projekt je nakonfigurován pro použití protokolu SSL. Aby nedocházelo k upozorněním SSL v prohlížeči, můžete se rozhodnout důvěřovat certifikátu podepsanému svým držitelem, který ASP.NET Core vygeneroval. Chcete důvěřovat ASP.NET Core certifikátu SSL?](~/getting-started/_static/trustCert-3x.png)

Vyberte **Ano** , pokud důvěřujete ASP.NET Core certifikát SSL.

Zobrazí se následující dialogové okno:

![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, vyberte **Ano** .

Další informace najdete v tématu [důvěryhodnost certifikátu pro vývoj ASP.NET Core https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .
::: moniker-end

::: moniker range="< aspnetcore-3.0"
Visual Studio zobrazí následující dialog:

![Tento projekt je nakonfigurován pro použití protokolu SSL. Aby nedocházelo k upozorněním SSL v prohlížeči, můžete se rozhodnout důvěřovat certifikátu podepsanému svým držitelem, který IIS Express vygeneroval. Chcete důvěřovat IIS Express certifikátu SSL?](~/getting-started/_static/trustCert.png)

Vyberte **Ano** , pokud důvěřujete IIS Express certifikát SSL.

Zobrazí se následující dialogové okno:

![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, vyberte **Ano** .

Další informace najdete v tématu [důvěryhodnost certifikátu pro vývoj ASP.NET Core https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .
::: moniker-end