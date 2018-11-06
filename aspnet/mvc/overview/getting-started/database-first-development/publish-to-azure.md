---
uid: mvc/overview/getting-started/database-first-development/publish-to-azure
title: MVC Database First serveru publikovat do Azure | Dokumentace Microsoftu
author: Rick-Anderson
description: Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. Tento kurz seri...
ms.author: riande
ms.date: 12/22/2014
ms.assetid: 7131f1c1-cef3-4396-ab44-ed4519676546
msc.legacyurl: /mvc/overview/getting-started/database-first-development/publish-to-azure
msc.type: authoredcontent
ms.openlocfilehash: 1d2c26c211c5c8d97076327d01fe59d5ba4dc9ac
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021632"
---
<a name="publish-mvc-database-first-site-to-azure"></a><span data-ttu-id="05895-104">MVC Database First serveru publikovat do Azure</span><span class="sxs-lookup"><span data-stu-id="05895-104">Publish MVC Database First site to Azure</span></span>
====================
<span data-ttu-id="05895-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="05895-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="05895-106">Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi.</span><span class="sxs-lookup"><span data-stu-id="05895-106">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="05895-107">V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="05895-107">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="05895-108">Generovaný kód odpovídá sloupců v tabulce databáze.</span><span class="sxs-lookup"><span data-stu-id="05895-108">The generated code corresponds to the columns in the database table.</span></span>
> 
> <span data-ttu-id="05895-109">Tato části této série se zaměřuje na publikování webové aplikace a databáze do Azure.</span><span class="sxs-lookup"><span data-stu-id="05895-109">This part of the series focuses on publishing the web app and database to Azure.</span></span> <span data-ttu-id="05895-110">Si můžete přečíst v tomto tématu se dozvíte o publikování webové aplikace a databáze, ale ve skutečnosti provádět kroky, které je třeba spustit na začátku tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="05895-110">You can read this topic to learn about publishing a web app and database, but to actually perform the steps you must start at the beginning of the tutorial.</span></span> <span data-ttu-id="05895-111">Zobrazit [Začínáme](setting-up-database.md).</span><span class="sxs-lookup"><span data-stu-id="05895-111">See [Getting Started](setting-up-database.md).</span></span>


## <a name="deploy-your-web-app-on-azure"></a><span data-ttu-id="05895-112">Nasazení webové aplikace v Azure</span><span class="sxs-lookup"><span data-stu-id="05895-112">Deploy your web app on Azure</span></span>

<span data-ttu-id="05895-113">Účet Azure k dokončení tohoto kurzu potřebujete:</span><span class="sxs-lookup"><span data-stu-id="05895-113">You need an Azure account to complete this tutorial:</span></span>

- <span data-ttu-id="05895-114">Je možné [zdarma otevřít účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) – budete dostávat kredity můžete použít k vyzkoušení placených služeb Azure a dokonce i po jejich použití až můžete účet ponechat a dál používat bezplatné služby Azure.</span><span class="sxs-lookup"><span data-stu-id="05895-114">You can [open an Azure account for free](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) - You get credits you can use to try out paid Azure services, and even after they're used up you can keep the account and use free Azure services.</span></span>
- <span data-ttu-id="05895-115">Je možné [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) – vaše předplatné MSDN vám dává kredity každý měsíc, můžete použít k placení za služby Azure.</span><span class="sxs-lookup"><span data-stu-id="05895-115">You can [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - Your MSDN subscription gives you credits every month that you can use for paid Azure services.</span></span>

<span data-ttu-id="05895-116">Chcete-li publikovat webovou aplikaci, klikněte pravým tlačítkem na projekt a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="05895-116">To publish your web app, right-click the project and select **Publish**.</span></span>

![publikování webu](publish-to-azure/_static/image1.png)

<span data-ttu-id="05895-118">Vyberte možnost Microsoft Azure Websites.</span><span class="sxs-lookup"><span data-stu-id="05895-118">Select Microsoft Azure Websites.</span></span>

![Vyberte Azure](publish-to-azure/_static/image2.png)

<span data-ttu-id="05895-120">Pokud nejste přihlášení k Azure, zadejte svoje přihlašovací údaje účtu Azure.</span><span class="sxs-lookup"><span data-stu-id="05895-120">If you are not signed in to Azure, provide your Azure account credentials.</span></span> <span data-ttu-id="05895-121">Vyberte nová vytvořit novou webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="05895-121">Then, select New to create a new web app.</span></span>

![Nový web](publish-to-azure/_static/image3.png)

<span data-ttu-id="05895-123">Vytvořte jedinečný název pro vaši webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="05895-123">Create a unique name for your web app.</span></span> <span data-ttu-id="05895-124">Budete vědět, že název je jedinečný Pokud se zobrazí zelená značka zaškrtnutí vpravo od názvu pole.</span><span class="sxs-lookup"><span data-stu-id="05895-124">You will know the name is unique if you see a green check mark to the right of the name field.</span></span> <span data-ttu-id="05895-125">Vyberte oblast pro vaši webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="05895-125">Select a region for your web app.</span></span> <span data-ttu-id="05895-126">Vyberte **vytvořit nový server** pro databázi a zadejte uživatelské jméno a heslo pro tento nový server databáze.</span><span class="sxs-lookup"><span data-stu-id="05895-126">Select **Create new server** for the database, and provide a user name and password for this new database server.</span></span> <span data-ttu-id="05895-127">Až budete hotovi, klikněte na tlačítko **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="05895-127">When finished, click **Create**.</span></span>

![Vytvoření webu](publish-to-azure/_static/image4.png)

<span data-ttu-id="05895-129">Hodnoty připojení jsou nyní Hotovo.</span><span class="sxs-lookup"><span data-stu-id="05895-129">Your connection values are now all set.</span></span> <span data-ttu-id="05895-130">Můžete ponechat tyto hodnoty ponechá beze změn.</span><span class="sxs-lookup"><span data-stu-id="05895-130">You can leave these values unchanged.</span></span>

![připojení](publish-to-azure/_static/image5.png)

<span data-ttu-id="05895-132">Klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="05895-132">Click **Next**.</span></span>

<span data-ttu-id="05895-133">Pro nastavení, Všimněte si, že dvě připojení k databázi zadávají - ApplicationDBContext a ContosoUniversityDataEntities.</span><span class="sxs-lookup"><span data-stu-id="05895-133">For Settings, notice that two database connections are specified - ApplicationDBContext and ContosoUniversityDataEntities.</span></span> <span data-ttu-id="05895-134">ApplicationDBContext je připojení k účtu tabulky uživatelů.</span><span class="sxs-lookup"><span data-stu-id="05895-134">ApplicationDBContext is the connection for user account tables.</span></span> <span data-ttu-id="05895-135">Tyto hodnoty zobrazit pouze připojovací řetězce databáze.</span><span class="sxs-lookup"><span data-stu-id="05895-135">These values only show the connection strings for the databases.</span></span> <span data-ttu-id="05895-136">Neznamená to, že tyto databáze bude publikován při publikování webu.</span><span class="sxs-lookup"><span data-stu-id="05895-136">It does not mean that these databases will be published when you publish your site.</span></span> <span data-ttu-id="05895-137">Po dokončení publikování webové aplikace, publikuje se váš projekt databáze.</span><span class="sxs-lookup"><span data-stu-id="05895-137">You will publish your database project after you have finished publishing the web app.</span></span>

![](publish-to-azure/_static/image6.png)

<span data-ttu-id="05895-138">Tři tečky (...) vedle připojení k databázi zobrazuje podrobnosti o připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="05895-138">The ellipsis (...) next to the database connection shows you the details of the connection string.</span></span> <span data-ttu-id="05895-139">Klikněte na tlačítko se třemi tečkami vedle ContosoUniversityDataEntities.</span><span class="sxs-lookup"><span data-stu-id="05895-139">Click the ellipsis next to ContosoUniversityDataEntities.</span></span>

![](publish-to-azure/_static/image7.png)

<span data-ttu-id="05895-140">Poznamenejte si název databázového serveru a databáze.</span><span class="sxs-lookup"><span data-stu-id="05895-140">Note the name of the database server and the database.</span></span> <span data-ttu-id="05895-141">Název serveru je náhodně generované.</span><span class="sxs-lookup"><span data-stu-id="05895-141">The server name is randomly generated.</span></span> <span data-ttu-id="05895-142">Název databáze je jednoduchý název vašeho webu pomocí  **\_db** připojí.</span><span class="sxs-lookup"><span data-stu-id="05895-142">The database name is simple the name of your site with **\_db** appended.</span></span> <span data-ttu-id="05895-143">Oba názvy budete potřebovat později při publikování databáze.</span><span class="sxs-lookup"><span data-stu-id="05895-143">You will need both names later when you publish your database.</span></span>

<span data-ttu-id="05895-144">Klikněte na tlačítko **OK** zavřete okno připojovací řetězec databáze.</span><span class="sxs-lookup"><span data-stu-id="05895-144">Click **OK** to close the database connection string window.</span></span>

<span data-ttu-id="05895-145">V okně Publikovat Web, klikněte na tlačítko **Další** zobrazíte náhled.</span><span class="sxs-lookup"><span data-stu-id="05895-145">In the Publish Web window, click **Next** to see the preview.</span></span>

![](publish-to-azure/_static/image8.png)

<span data-ttu-id="05895-146">Klikněte na tlačítko Spustit Náhled zobrazíte seznam souborů k publikování.</span><span class="sxs-lookup"><span data-stu-id="05895-146">You can click Start Preview to see a list of the files to publish.</span></span> <span data-ttu-id="05895-147">Protože při prvním publikování tohoto webu, v seznamu je každý odpovídající soubor v projektu.</span><span class="sxs-lookup"><span data-stu-id="05895-147">Since this is the first time you have published this site, the list is every relevant file in the project.</span></span>

<span data-ttu-id="05895-148">Klikněte na tlačítko **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="05895-148">Click **Publish**.</span></span>

<span data-ttu-id="05895-149">V podokně výstupu se zobrazí výsledek publikace.</span><span class="sxs-lookup"><span data-stu-id="05895-149">The Output pane will display the result of your publication.</span></span>

![](publish-to-azure/_static/image9.png)

<span data-ttu-id="05895-150">Po publikování web je immedialely spuštění ve webovém prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="05895-150">After publication, the site is immedialely launched in a web browser.</span></span> <span data-ttu-id="05895-151">Nasazení webu a můžete zaregistrovat nový uživatel k webu; ale tabulek v projektu ContosoUniversityData dosud nebyly publikovány.</span><span class="sxs-lookup"><span data-stu-id="05895-151">Your site has been deployed and you can register a new user to the site; however, your tables in the ContosoUniversityData project have not yet been published.</span></span> <span data-ttu-id="05895-152">Pokud kliknete na seznamu students odkaz dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="05895-152">If you click on the List of students link you will receive an error.</span></span>

## <a name="publish-database-to-sql-azure"></a><span data-ttu-id="05895-153">Publikování databáze SQL Azure</span><span class="sxs-lookup"><span data-stu-id="05895-153">Publish database to SQL Azure</span></span>

<span data-ttu-id="05895-154">Před publikováním databáze, musí se ujistěte, že místního počítače může připojit k serveru databáze.</span><span class="sxs-lookup"><span data-stu-id="05895-154">Before publishing your database, you must make sure your local computer can connect to the database server.</span></span> <span data-ttu-id="05895-155">Omezuje brány firewall pro databázový server, které počítače může připojit k databázi.</span><span class="sxs-lookup"><span data-stu-id="05895-155">The firewall for your database server restricts which machines can connect to the database.</span></span> <span data-ttu-id="05895-156">Budete muset přidat IP adresu počítače do povolených IP adres pro bránu firewall.</span><span class="sxs-lookup"><span data-stu-id="05895-156">You need to add the IP address of your computer to the allowed IP addresses for the firewall.</span></span>

<span data-ttu-id="05895-157">Přihlaste se ke svému účtu Azure na webu Azure portal.</span><span class="sxs-lookup"><span data-stu-id="05895-157">Login to your Azure account through the Azure portal.</span></span>

<span data-ttu-id="05895-158">Vyberte vaši novou databázi a vyberte **spravovat**.</span><span class="sxs-lookup"><span data-stu-id="05895-158">Select your new database and select **Manage**.</span></span>

![Správa](publish-to-azure/_static/image10.png)

<span data-ttu-id="05895-160">Musíte nakonfigurovat váš databázový server umožňuje připojení z vašeho počítače.</span><span class="sxs-lookup"><span data-stu-id="05895-160">You must configure your database server to allow connections from your computer.</span></span> <span data-ttu-id="05895-161">Vyberete-li spravovat, zobrazí se výzva přidat aktuální IP adresu, protože k databázovému serveru povolené.</span><span class="sxs-lookup"><span data-stu-id="05895-161">When you select Manage, you are asked to add the current IP address as permitted to the database server.</span></span> <span data-ttu-id="05895-162">Vyberte Ano.</span><span class="sxs-lookup"><span data-stu-id="05895-162">Select Yes.</span></span>

![Přidat ip adresu](publish-to-azure/_static/image11.png)

<span data-ttu-id="05895-164">Je pravděpodobné, že IP adresa, kterou jste přidali v předchozím kroku není jedinou IP adresu, kterou je potřeba nakonfigurovat pro připojení.</span><span class="sxs-lookup"><span data-stu-id="05895-164">There is a chance that the IP address you added in the previous step is not the only IP address you need to configure for connections.</span></span> <span data-ttu-id="05895-165">Pokuste se přihlásit k databázi a zjistěte, jestli připojení máte správně nastavený.</span><span class="sxs-lookup"><span data-stu-id="05895-165">You can attempt to login to the database to see if the connections have been properly set up.</span></span> <span data-ttu-id="05895-166">Zadejte uživatele a heslo, které jste vytvořili dříve.</span><span class="sxs-lookup"><span data-stu-id="05895-166">Provide the user and password you created earlier.</span></span>

![přihlášení](publish-to-azure/_static/image12.png)

<span data-ttu-id="05895-168">Pokud se zobrazí chybová zpráva, budete muset přidat další IP adresu.</span><span class="sxs-lookup"><span data-stu-id="05895-168">If you receive an error message, you need to add another IP address.</span></span> <span data-ttu-id="05895-169">Chyba na zprávu klikněte a zobrazit podrobnosti o chybě.</span><span class="sxs-lookup"><span data-stu-id="05895-169">Click the error message to see more details about error.</span></span> <span data-ttu-id="05895-170">V podrobnostech se zobrazí, které je potřeba přidat IP adresu.</span><span class="sxs-lookup"><span data-stu-id="05895-170">In the details you will see the IP address that you need to add.</span></span> <span data-ttu-id="05895-171">Poznamenejte si tuto IP adresu.</span><span class="sxs-lookup"><span data-stu-id="05895-171">Note this IP address.</span></span>

![není povoleno](publish-to-azure/_static/image13.png)

<span data-ttu-id="05895-173">Zavřít toto okno přihlášení a vraťte se do portálu Azure portal.</span><span class="sxs-lookup"><span data-stu-id="05895-173">Close this login window, and return to the Azure portal.</span></span>

<span data-ttu-id="05895-174">Přejděte na řídicí panel pro vaši databázi.</span><span class="sxs-lookup"><span data-stu-id="05895-174">Navigate to the Dashboard for your database.</span></span> <span data-ttu-id="05895-175">Klikněte na tlačítko **Spravovat povolené IP adresy**.</span><span class="sxs-lookup"><span data-stu-id="05895-175">Click **Manage allowed IP addresses**.</span></span>

![Správa ip adres](publish-to-azure/_static/image14.png)

<span data-ttu-id="05895-177">Teď musíte přidat IP adresu z chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="05895-177">You must now add the IP address from the error message.</span></span> <span data-ttu-id="05895-178">Změňte oblasti povolené IP adresy, které zahrnují jeden z chybové zprávy, nebo přidejte tuto IP adresu jako samostatný záznam.</span><span class="sxs-lookup"><span data-stu-id="05895-178">Either change the range of allowed IP addresses to include the one from the error message, or add that IP address as a separate entry.</span></span>

![Přidejte novou adresu](publish-to-azure/_static/image15.png)

<span data-ttu-id="05895-180">Uložte změny do povolených IP adres.</span><span class="sxs-lookup"><span data-stu-id="05895-180">Save the change to allowed IP addresses.</span></span>

<span data-ttu-id="05895-181">Kliknutím na spravovat a zkuste se znovu přihlásit k databázi.</span><span class="sxs-lookup"><span data-stu-id="05895-181">Click Manage, and try logging in again to the database.</span></span> <span data-ttu-id="05895-182">Budete muset počkat několik minut, než je povolené IP adresy pro bránu firewall správně nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="05895-182">You may need to wait a few minutes before the allowed IP addresses are correctly configured for the firewall.</span></span> <span data-ttu-id="05895-183">Když se můžete úspěšně přihlásit databáze, dokončení nastavení připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="05895-183">When you can successfully log in the database, you have finished setting up your connection to the database.</span></span>

<span data-ttu-id="05895-184">Toto okno správy můžete nechat otevřené, protože zkontroluje výsledek nasazení databáze za chvíli.</span><span class="sxs-lookup"><span data-stu-id="05895-184">You can leave this management window open because you will check the result of your database deployment shortly.</span></span>

<span data-ttu-id="05895-185">Vraťte se do projektu databáze.</span><span class="sxs-lookup"><span data-stu-id="05895-185">Return to your database project.</span></span> <span data-ttu-id="05895-186">Klikněte pravým tlačítkem na projekt a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="05895-186">Right-click the project and select **Publish**.</span></span>

![publikování databáze](publish-to-azure/_static/image16.png)

<span data-ttu-id="05895-188">V okně publikování databáze vyberte **upravit**.</span><span class="sxs-lookup"><span data-stu-id="05895-188">In the Publish Database window, select **Edit**.</span></span>

![Upravit](publish-to-azure/_static/image17.png)

<span data-ttu-id="05895-190">Zadejte název databázového serveru a ověřování pověření pro server.</span><span class="sxs-lookup"><span data-stu-id="05895-190">Provide the name of the database server and your authentication credentials for the server.</span></span> <span data-ttu-id="05895-191">Po zadání přihlašovacích údajů, vyberte databázi, kterou jste vytvořili ze seznamu dostupných databází.</span><span class="sxs-lookup"><span data-stu-id="05895-191">After providing the credentials, select the database you created from the list of available databases.</span></span> <span data-ttu-id="05895-192">Ve výchozím nastavení Visual Studio nastaví název pole databáze na název vašeho projektu, který nemusí být stejné jako databáze, kterou jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="05895-192">By default, Visual Studio sets the name of the database field to the name of your project which might not be the same as the database you created.</span></span>

![](publish-to-azure/_static/image18.png)

<span data-ttu-id="05895-193">Klikněte na tlačítko OK.</span><span class="sxs-lookup"><span data-stu-id="05895-193">Click OK.</span></span>

<span data-ttu-id="05895-194">Bude pravděpodobně chcete uložit tento profil, abyste mohli publikovat aktualizace budoucí bez nutnosti opětovného zadání všechny informace o připojení.</span><span class="sxs-lookup"><span data-stu-id="05895-194">You will probably want to save this profile so you can publish updates in the future without re-entering all of the connection information.</span></span> <span data-ttu-id="05895-195">Vyberte **vytvořit profil**.</span><span class="sxs-lookup"><span data-stu-id="05895-195">Select **Create Profile**.</span></span>

![Uložit profil](publish-to-azure/_static/image19.png)

<span data-ttu-id="05895-197">Vytvoří soubor v projektu s názvem **ContosoUniversityData.publish.xml**.</span><span class="sxs-lookup"><span data-stu-id="05895-197">It will create a file in your project named **ContosoUniversityData.publish.xml**.</span></span> <span data-ttu-id="05895-198">Při příštím publikování databáze do Azure, jednoduše načtěte tento profil.</span><span class="sxs-lookup"><span data-stu-id="05895-198">The next time you want to publish the database to Azure, simply load that profile.</span></span>

<span data-ttu-id="05895-199">Nyní, klikněte na tlačítko **publikovat** k vytvoření databáze v Azure.</span><span class="sxs-lookup"><span data-stu-id="05895-199">Now, click **Publish** to create the database on Azure.</span></span>

![Publikování](publish-to-azure/_static/image20.png)

<span data-ttu-id="05895-201">Publikování výsledky se zobrazí po spuštění nějakou dobu.</span><span class="sxs-lookup"><span data-stu-id="05895-201">After running for a while, the publishing results are displayed.</span></span>

![výsledky](publish-to-azure/_static/image21.png)

<span data-ttu-id="05895-203">Teď můžete přejít zpět na portál pro správu pro vaši databázi.</span><span class="sxs-lookup"><span data-stu-id="05895-203">Now, you can go back to the management portal for your database.</span></span> <span data-ttu-id="05895-204">Aktualizovat návrhové zobrazení a Všimněte si, že byly nasazeny 3 tabulek předem vyplněný daty.</span><span class="sxs-lookup"><span data-stu-id="05895-204">Refresh the design view, and notice the 3 tables with pre-filled data have been deployed.</span></span>

![nové tabulky](publish-to-azure/_static/image22.png)

<span data-ttu-id="05895-206">Nyní jste připraveni k otestování webové aplikace, který je nasazený do Azure.</span><span class="sxs-lookup"><span data-stu-id="05895-206">Now you are ready to test the web app that is deployed to Azure.</span></span> <span data-ttu-id="05895-207">Přejděte do webové aplikace v Azure (například http://contosositeexample.azurewebsites.net/).</span><span class="sxs-lookup"><span data-stu-id="05895-207">Navigate to the web app on Azure (such as http://contosositeexample.azurewebsites.net/).</span></span> <span data-ttu-id="05895-208">Klikněte na odkaz pro seznam studentů a měli byste vidět zobrazení indexu pro studenty.</span><span class="sxs-lookup"><span data-stu-id="05895-208">Click the link for List of students and you should see the index view for students.</span></span>

![Zobrazení](publish-to-azure/_static/image23.png)

<span data-ttu-id="05895-210">V některých případech nutné databáze a připojení nějakou dobu správně nakonfigurovat.</span><span class="sxs-lookup"><span data-stu-id="05895-210">Occasionally, the database and connection need a little time to be properly configured.</span></span> <span data-ttu-id="05895-211">Pokud obdržíte chybu, počkejte několik minut a pak to zkuste znovu.</span><span class="sxs-lookup"><span data-stu-id="05895-211">If you receive an error, wait a few minutes and then try again.</span></span>

## <a name="conclusion"></a><span data-ttu-id="05895-212">Závěr</span><span class="sxs-lookup"><span data-stu-id="05895-212">Conclusion</span></span>

<span data-ttu-id="05895-213">Tato série poskytuje jednoduchý příklad toho, jak generovat kód z existující databáze, která umožňuje uživatelům upravovat, aktualizovat, vytvářet a odstraňovat data.</span><span class="sxs-lookup"><span data-stu-id="05895-213">This series provided a simple example of how to generate code from an existing database that enables users to edit, update, create and delete data.</span></span> <span data-ttu-id="05895-214">Používá ASP.NET MVC 5, Entity Framework a ASP.NET generování uživatelského rozhraní pro vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="05895-214">It used ASP.NET MVC 5, Entity Framework and ASP.NET Scaffolding to create the project.</span></span>

<span data-ttu-id="05895-215">Úvodní příklad vývoje Code First najdete v tématu [Začínáme s ASP.NET MVC 5](../introduction/getting-started.md).</span><span class="sxs-lookup"><span data-stu-id="05895-215">For an introductory example of Code First development, see [Getting Started with ASP.NET MVC 5](../introduction/getting-started.md).</span></span>

<span data-ttu-id="05895-216">Složitější příklad naleznete v tématu [vytváření datového modelu Entity Framework pro aplikaci ASP.NET MVC 4](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span><span class="sxs-lookup"><span data-stu-id="05895-216">For a more advanced example, see [Creating an Entity Framework Data Model for an ASP.NET MVC 4 App](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span></span> <span data-ttu-id="05895-217">Všimněte si, že rozhraní API pro DbContext, který používáte pro práci s daty v databázi první je stejný jako rozhraní API můžete použít pro práci s daty v Code First.</span><span class="sxs-lookup"><span data-stu-id="05895-217">Note that the DbContext API that you use for working with data in Database First is the same as the API you use for working with data in Code First.</span></span> <span data-ttu-id="05895-218">I v případě, že máte v úmyslu použít první databáze, můžete zjistíte, jak zpracovat složitější scénáře, jako je čtení a aktualizace souvisejících dat, zpracování konfliktů souběžnosti, a tak dále z Code First kurzu.</span><span class="sxs-lookup"><span data-stu-id="05895-218">Even if you intend to use Database First, you can learn how to handle more complex scenarios such as reading and updating related data, handling concurrency conflicts, and so forth from a Code First tutorial.</span></span> <span data-ttu-id="05895-219">Jediný rozdíl spočívá ve vytvoření databáze, třída kontextu a tříd entit.</span><span class="sxs-lookup"><span data-stu-id="05895-219">The only difference is in how the database, context class, and entity classes are created.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="05895-220">Předchozí</span><span class="sxs-lookup"><span data-stu-id="05895-220">Previous</span></span>](enhancing-data-validation.md)
