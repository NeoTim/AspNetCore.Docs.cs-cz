# <a name="how-to-buildrun-secure-user-data-sample"></a><span data-ttu-id="e44a1-101">Jak sestavení nebo spuštění ukázková data zabezpečení uživatele</span><span class="sxs-lookup"><span data-stu-id="e44a1-101">How to build/run Secure user data sample</span></span>

* <span data-ttu-id="e44a1-102">Nastavení hesla pomocí nástroje Správce tajný kód:</span><span class="sxs-lookup"><span data-stu-id="e44a1-102">Set password with the Secret Manager tool:</span></span>

  `dotnet user-secrets set SeedUserPW <pw>`

* <span data-ttu-id="e44a1-103">Aktualizace databáze:</span><span class="sxs-lookup"><span data-stu-id="e44a1-103">Update the database:</span></span>

    `dotnet ef database update`

* <span data-ttu-id="e44a1-104">Povolení HTTPS v projektu</span><span class="sxs-lookup"><span data-stu-id="e44a1-104">Enable HTTPS in the project</span></span>
