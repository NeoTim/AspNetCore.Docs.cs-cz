* <span data-ttu-id="827e6-101">Vystavte certifikát pro vývoj HTTPS tak, že spustíte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="827e6-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="827e6-102">Předchozí příkaz nefunguje na Linux.</span><span class="sxs-lookup"><span data-stu-id="827e6-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="827e6-103">Informace o důvěřování certifikátu najdete v dokumentaci k distribuci Linux.</span><span class="sxs-lookup"><span data-stu-id="827e6-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="827e6-104">Předchozí příkaz zobrazí následující dialog:</span><span class="sxs-lookup"><span data-stu-id="827e6-104">The preceding command displays the following dialog:</span></span>

  ![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

* <span data-ttu-id="827e6-106">Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, vyberte **Ano** .</span><span class="sxs-lookup"><span data-stu-id="827e6-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="827e6-107">Další informace najdete v tématu [důvěryhodnost certifikátu pro vývoj ASP.NET Core https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .</span><span class="sxs-lookup"><span data-stu-id="827e6-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
