---
title: Hostitele ASP.NET Core ve službě Windows
author: guardrex
description: Zjistěte, jak hostovat aplikace ASP.NET Core ve službě Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/30/2018
uid: host-and-deploy/windows-service
ms.openlocfilehash: 11913019bfe5d06c259b806fce9cc580a8280ad5
ms.sourcegitcommit: fc2486ddbeb15ab4969168d99b3fe0fbe91e8661
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50758190"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="73626-103">Hostitele ASP.NET Core ve službě Windows</span><span class="sxs-lookup"><span data-stu-id="73626-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="73626-104">Podle [Luke Latham](https://github.com/guardrex) a [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="73626-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="73626-105">Aplikace ASP.NET Core je možné hostovat na Windows bez použití služby IIS jako [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications).</span><span class="sxs-lookup"><span data-stu-id="73626-105">An ASP.NET Core app can be hosted on Windows without using IIS as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications).</span></span> <span data-ttu-id="73626-106">Pokud hostovaný jako služba Windows, aplikace se automaticky spustí po restartování počítače.</span><span class="sxs-lookup"><span data-stu-id="73626-106">When hosted as a Windows Service, the app automatically starts after reboots.</span></span>

<span data-ttu-id="73626-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="73626-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="convert-a-project-into-a-windows-service"></a><span data-ttu-id="73626-108">Převést projekt do služby Windows</span><span class="sxs-lookup"><span data-stu-id="73626-108">Convert a project into a Windows Service</span></span>

<span data-ttu-id="73626-109">Následující minimální změny je potřeba nastavit stávající projekt ASP.NET Core pro spouštěn jako služba:</span><span class="sxs-lookup"><span data-stu-id="73626-109">The following minimum changes are required to set up an existing ASP.NET Core project to run as a service:</span></span>

1. <span data-ttu-id="73626-110">V souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="73626-110">In the project file:</span></span>

   * <span data-ttu-id="73626-111">Ověřte existenci Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) nebo ho přidat do `<PropertyGroup>` , který obsahuje Cílová architektura:</span><span class="sxs-lookup"><span data-stu-id="73626-111">Confirm the presence of a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) or add it to the `<PropertyGroup>` that contains the target framework:</span></span>

      ```xml
      <PropertyGroup>
        <TargetFramework>netcoreapp2.2</TargetFramework>
        <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
      </PropertyGroup>
      ```

      <span data-ttu-id="73626-112">Chcete-li publikovat pro více identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="73626-112">To publish for multiple RIDs:</span></span>

      * <span data-ttu-id="73626-113">Zadejte identifikátory RID v seznam oddělený středníkem.</span><span class="sxs-lookup"><span data-stu-id="73626-113">Provide the RIDs in a semicolon-delimited list.</span></span>
      * <span data-ttu-id="73626-114">Použijte název vlastnosti `<RuntimeIdentifiers>` (množné číslo).</span><span class="sxs-lookup"><span data-stu-id="73626-114">Use the property name `<RuntimeIdentifiers>` (plural).</span></span>

      <span data-ttu-id="73626-115">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="73626-115">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

   * <span data-ttu-id="73626-116">Přidat odkaz na balíček pro [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="73626-116">Add a package reference for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span></span>

1. <span data-ttu-id="73626-117">Proveďte následující změny v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="73626-117">Make the following changes in `Program.Main`:</span></span>

   * <span data-ttu-id="73626-118">Volání [hostitele. RunAsService](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostwindowsserviceextensions.runasservice) místo `host.Run`.</span><span class="sxs-lookup"><span data-stu-id="73626-118">Call [host.RunAsService](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostwindowsserviceextensions.runasservice) instead of `host.Run`.</span></span>

   * <span data-ttu-id="73626-119">Volání [UseContentRoot](xref:fundamentals/host/web-host#content-root) a cesta k aplikaci pro publikování umístění, namísto použití `Directory.GetCurrentDirectory()`.</span><span class="sxs-lookup"><span data-stu-id="73626-119">Call [UseContentRoot](xref:fundamentals/host/web-host#content-root) and use a path to the app's published location instead of `Directory.GetCurrentDirectory()`.</span></span>

     [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=ServiceOnly&highlight=8-9,16)]

1. <span data-ttu-id="73626-120">Publikování aplikace pomocí [dotnet publikovat](/dotnet/articles/core/tools/dotnet-publish), [profil publikování pro Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles), nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="73626-120">Publish the app using [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), a [Visual Studio publish profile](xref:host-and-deploy/visual-studio-publish-profiles), or Visual Studio Code.</span></span> <span data-ttu-id="73626-121">Když pomocí sady Visual Studio, vyberte **FolderProfile** a nakonfigurovat **cílové umístění** před výběrem **publikovat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="73626-121">When using Visual Studio, select the **FolderProfile** and configure the **Target Location** before selecting the **Publish** button.</span></span>

   <span data-ttu-id="73626-122">Chcete-li publikovat ukázkovou aplikaci pomocí nástrojů rozhraní příkazového řádku (CLI), spusťte [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkazu na příkazovém řádku ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="73626-122">To publish the sample app using command-line interface (CLI) tools, run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command at a command prompt from the project folder.</span></span> <span data-ttu-id="73626-123">V musí být zadán identifikátor RID `<RuntimeIdenfifier>` (nebo `<RuntimeIdentifiers>`) vlastnost souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="73626-123">The RID must be specified in the `<RuntimeIdenfifier>` (or `<RuntimeIdentifiers>`) property of the project file.</span></span> <span data-ttu-id="73626-124">V následujícím příkladu, publikování aplikace v rámci konfigurace verze pro `win7-x64` runtime do složky vytvořené v *c:\\svc*:</span><span class="sxs-lookup"><span data-stu-id="73626-124">In the following example, the app is published in Release configuration for the `win7-x64` runtime to a folder created at *c:\\svc*:</span></span>

   ```console
   dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
   ```

1. <span data-ttu-id="73626-125">Vytvořte účet uživatele pro používání služby `net user` příkaz:</span><span class="sxs-lookup"><span data-stu-id="73626-125">Create a user account for the service using the `net user` command:</span></span>

   ```console
   net user {USER ACCOUNT} {PASSWORD} /add
   ```

   <span data-ttu-id="73626-126">Pro ukázkovou aplikaci, vytvořte uživatelský účet s názvem `ServiceUser` a heslo.</span><span class="sxs-lookup"><span data-stu-id="73626-126">For the sample app, create a user account with the name `ServiceUser` and a password.</span></span> <span data-ttu-id="73626-127">V následujícím příkazu nahraďte `{PASSWORD}` s [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span><span class="sxs-lookup"><span data-stu-id="73626-127">In the following command, replace `{PASSWORD}` with a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span></span>

   ```console
   net user ServiceUser {PASSWORD} /add
   ```

   <span data-ttu-id="73626-128">Pokud potřebujete přidat uživatele do skupiny, použijte `net localgroup` příkaz, kde `{GROUP}` je název skupiny:</span><span class="sxs-lookup"><span data-stu-id="73626-128">If you need to add the user to a group, use the `net localgroup` command, where `{GROUP}` is the name of the group:</span></span>

   ```console
   net localgroup {GROUP} {USER ACCOUNT} /add
   ```

   <span data-ttu-id="73626-129">Další informace najdete v tématu [uživatelské účty služby](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="73626-129">For more information, see [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

1. <span data-ttu-id="73626-130">Udělit přístup, zápis a čtení a spouštění do složky aplikace pomocí [icacls](/windows-server/administration/windows-commands/icacls) příkaz:</span><span class="sxs-lookup"><span data-stu-id="73626-130">Grant write/read/execute access to the app's folder using the [icacls](/windows-server/administration/windows-commands/icacls) command:</span></span>

   ```console
   icacls "{PATH}" /grant {USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS} /t
   ```

   * <span data-ttu-id="73626-131">`{PATH}` &ndash; Cesta ke složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="73626-131">`{PATH}` &ndash; Path to the app's folder.</span></span>
   * <span data-ttu-id="73626-132">`{USER ACCOUNT}` &ndash; Uživatelský účet (SID).</span><span class="sxs-lookup"><span data-stu-id="73626-132">`{USER ACCOUNT}` &ndash; The user account (SID).</span></span>
   * <span data-ttu-id="73626-133">`(OI)` &ndash; Příznak objekt dědit šíří oprávnění na podřízené soubory.</span><span class="sxs-lookup"><span data-stu-id="73626-133">`(OI)` &ndash; The Object Inherit flag propagates permissions to subordinate files.</span></span>
   * <span data-ttu-id="73626-134">`(CI)` &ndash; Příznak kontejneru dědit šíří oprávnění na podřízené složky.</span><span class="sxs-lookup"><span data-stu-id="73626-134">`(CI)` &ndash; The Container Inherit flag propagates permissions to subordinate folders.</span></span>
   * <span data-ttu-id="73626-135">`{PERMISSION FLAGS}` &ndash; Nastaví její oprávnění přístupu.</span><span class="sxs-lookup"><span data-stu-id="73626-135">`{PERMISSION FLAGS}` &ndash; Sets the app's access permissions.</span></span>
     * <span data-ttu-id="73626-136">Zápis (`W`)</span><span class="sxs-lookup"><span data-stu-id="73626-136">Write (`W`)</span></span>
     * <span data-ttu-id="73626-137">Čtení (`R`)</span><span class="sxs-lookup"><span data-stu-id="73626-137">Read (`R`)</span></span>
     * <span data-ttu-id="73626-138">Spuštění (`X`)</span><span class="sxs-lookup"><span data-stu-id="73626-138">Execute (`X`)</span></span>
     * <span data-ttu-id="73626-139">Úplné (`F`)</span><span class="sxs-lookup"><span data-stu-id="73626-139">Full (`F`)</span></span>
     * <span data-ttu-id="73626-140">Upravit (`M`)</span><span class="sxs-lookup"><span data-stu-id="73626-140">Modify (`M`)</span></span>
   * <span data-ttu-id="73626-141">`/t` &ndash; Rekurzivně se vztahují na existující podřízené složky a soubory.</span><span class="sxs-lookup"><span data-stu-id="73626-141">`/t` &ndash; Apply recursively to existing subordinate folders and files.</span></span>

   <span data-ttu-id="73626-142">Pro publikování ukázkové aplikace *c:\\svc* složky a `ServiceUser` účet s oprávněními pro zápis a čtení a spouštění, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="73626-142">For the sample app published to the *c:\\svc* folder and the `ServiceUser` account with write/read/execute permissions, use the following command:</span></span>

   ```console
   icacls "c:\svc" /grant ServiceUser:(OI)(CI)WRX /t
   ```

   <span data-ttu-id="73626-143">Další informace najdete v tématu [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="73626-143">For more information, see [icacls](/windows-server/administration/windows-commands/icacls).</span></span>

1. <span data-ttu-id="73626-144">Použití [sc.exe](https://technet.microsoft.com/library/bb490995) nástroj příkazového řádku vytvořte službu.</span><span class="sxs-lookup"><span data-stu-id="73626-144">Use the [sc.exe](https://technet.microsoft.com/library/bb490995) command-line tool to create the service.</span></span> <span data-ttu-id="73626-145">`binPath` Hodnota je cesta ke spustitelnému souboru aplikace, která zahrnuje název spustitelného souboru.</span><span class="sxs-lookup"><span data-stu-id="73626-145">The `binPath` value is the path to the app's executable, which includes the executable file name.</span></span> <span data-ttu-id="73626-146">**Mezera mezi znaménko rovná se a znak pro uvození každého parametru a hodnota je povinný.**</span><span class="sxs-lookup"><span data-stu-id="73626-146">**The space between the equal sign and the quote character of each parameter and value is required.**</span></span>

   ```console
   sc create {SERVICE NAME} binPath= "{PATH}" obj= "{DOMAIN}\{USER ACCOUNT}" password= "{PASSWORD}"
   ```

   * <span data-ttu-id="73626-147">`{SERVICE NAME}` &ndash; Název, který chcete přiřadit ke službě v [správce řízení služeb](/windows/desktop/services/service-control-manager).</span><span class="sxs-lookup"><span data-stu-id="73626-147">`{SERVICE NAME}` &ndash; The name to assign to the service in [Service Control Manager](/windows/desktop/services/service-control-manager).</span></span>
   * <span data-ttu-id="73626-148">`{PATH}` &ndash; Cesta ke spustitelnému souboru služby.</span><span class="sxs-lookup"><span data-stu-id="73626-148">`{PATH}` &ndash; The path to the service executable.</span></span>
   * <span data-ttu-id="73626-149">`{DOMAIN}` (nebo pokud počítač není domény připojené, názvu místního počítače) a `{USER ACCOUNT}` &ndash; doménu (nebo názvu místního počítače) a uživatelský účet, pod kterým je služba spuštěna.</span><span class="sxs-lookup"><span data-stu-id="73626-149">`{DOMAIN}` (or if the machine isn't domain joined, the local machine name) and `{USER ACCOUNT}` &ndash; The domain (or local machine name) and user account under which the service runs.</span></span> <span data-ttu-id="73626-150">Proveďte **není** vynechat, nechte `obj` parametru.</span><span class="sxs-lookup"><span data-stu-id="73626-150">Do **not** omit the `obj` parameter.</span></span> <span data-ttu-id="73626-151">Výchozí hodnota pro `obj` je [účet LocalSystem](/windows/desktop/services/localsystem-account) účtu.</span><span class="sxs-lookup"><span data-stu-id="73626-151">The default value for `obj` is the [LocalSystem account](/windows/desktop/services/localsystem-account) account.</span></span> <span data-ttu-id="73626-152">Spuštěná služba v rámci `LocalSystem` účet představuje významné bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="73626-152">Running a service under the `LocalSystem` account presents a significant security risk.</span></span> <span data-ttu-id="73626-153">Vždy spustit službu pod uživatelským účtem s omezenými oprávněními na serveru.</span><span class="sxs-lookup"><span data-stu-id="73626-153">Always run a service under a user account with restricted privileges on the server.</span></span>
   * <span data-ttu-id="73626-154">`{PASSWORD}` &ndash; Heslo k uživatelskému účtu.</span><span class="sxs-lookup"><span data-stu-id="73626-154">`{PASSWORD}` &ndash; The user account password.</span></span>

   <span data-ttu-id="73626-155">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="73626-155">In the following example:</span></span>

   * <span data-ttu-id="73626-156">Služba má název **Moje_služba**.</span><span class="sxs-lookup"><span data-stu-id="73626-156">The service is named **MyService**.</span></span>
   * <span data-ttu-id="73626-157">Publikované služba se nachází v *c:\\svc* složky.</span><span class="sxs-lookup"><span data-stu-id="73626-157">The published service resides in the *c:\\svc* folder.</span></span> <span data-ttu-id="73626-158">Je název spustitelné aplikace *AspNetCoreService.exe*.</span><span class="sxs-lookup"><span data-stu-id="73626-158">The app executable is named *AspNetCoreService.exe*.</span></span> <span data-ttu-id="73626-159">`binPath` Je hodnota uzavřena do uvozovek (").</span><span class="sxs-lookup"><span data-stu-id="73626-159">The `binPath` value is enclosed in straight quotation marks (").</span></span>
   * <span data-ttu-id="73626-160">Je služba spuštěna pod `ServiceUser` účtu.</span><span class="sxs-lookup"><span data-stu-id="73626-160">The service runs under the `ServiceUser` account.</span></span> <span data-ttu-id="73626-161">Nahraďte `{DOMAIN}` s účtem uživatele domény nebo názvu místního počítače.</span><span class="sxs-lookup"><span data-stu-id="73626-161">Replace `{DOMAIN}` with the user account's domain or local machine name.</span></span> <span data-ttu-id="73626-162">Uzavřete `obj` hodnotu v rovné uvozovky (").</span><span class="sxs-lookup"><span data-stu-id="73626-162">Enclose the `obj` value in straight quotation marks (").</span></span> <span data-ttu-id="73626-163">Příklad: Pokud je hostující systém místní počítač s názvem `MairaPC`, nastavte `obj` k `"MairaPC\ServiceUser"`.</span><span class="sxs-lookup"><span data-stu-id="73626-163">Example: If the hosting system is a local machine named `MairaPC`, set `obj` to `"MairaPC\ServiceUser"`.</span></span>
   * <span data-ttu-id="73626-164">Nahraďte `{PASSWORD}` s heslem uživatelského účtu.</span><span class="sxs-lookup"><span data-stu-id="73626-164">Replace `{PASSWORD}` with the user account's password.</span></span> <span data-ttu-id="73626-165">`password` Je hodnota uzavřena do uvozovek (").</span><span class="sxs-lookup"><span data-stu-id="73626-165">The `password` value is enclosed in straight quotation marks (").</span></span>

   ```console
   sc create MyService binPath= "c:\svc\aspnetcoreservice.exe" obj= "{DOMAIN}\ServiceUser" password= "{PASSWORD}"
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="73626-166">Ujistěte se, že mezery mezi symboly rovná parametrů a hodnot parametrů jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="73626-166">Make sure that the spaces between the parameters' equal signs and the parameters' values are present.</span></span>

1. <span data-ttu-id="73626-167">Spusťte službu pomocí `sc start {SERVICE NAME}` příkazu.</span><span class="sxs-lookup"><span data-stu-id="73626-167">Start the service with the `sc start {SERVICE NAME}` command.</span></span>

   <span data-ttu-id="73626-168">Spustit službu ukázkové aplikace, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="73626-168">To start the sample app service, use the following command:</span></span>

   ```console
   sc start MyService
   ```

   <span data-ttu-id="73626-169">Příkaz trvá několik sekund se spustit službu.</span><span class="sxs-lookup"><span data-stu-id="73626-169">The command takes a few seconds to start the service.</span></span>

1. <span data-ttu-id="73626-170">Chcete-li zkontrolovat stav služby, použijte `sc query {SERVICE NAME}` příkazu.</span><span class="sxs-lookup"><span data-stu-id="73626-170">To check the status of the service, use the `sc query {SERVICE NAME}` command.</span></span> <span data-ttu-id="73626-171">Stav je uveden jako jeden z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="73626-171">The status is reported as one of the following values:</span></span>

   * `START_PENDING`
   * `RUNNING`
   * `STOP_PENDING`
   * `STOPPED`

   <span data-ttu-id="73626-172">Použijte následující příkaz a zkontrolujte stav služby app service vzorku:</span><span class="sxs-lookup"><span data-stu-id="73626-172">Use the following command to check the status of the sample app service:</span></span>

   ```console
   sc query MyService
   ```

1. <span data-ttu-id="73626-173">Pokud je služba v `RUNNING` stavu a pokud je služba webové aplikace, procházet aplikace, její cesta (ve výchozím nastavení, `http://localhost:5000`, který přesměruje `https://localhost:5001` při použití [HTTPS přesměrování Middleware](xref:security/enforcing-ssl)).</span><span class="sxs-lookup"><span data-stu-id="73626-173">When the service is in the `RUNNING` state and if the service is a web app, browse the app at its path (by default, `http://localhost:5000`, which redirects to `https://localhost:5001` when using [HTTPS Redirection Middleware](xref:security/enforcing-ssl)).</span></span>

   <span data-ttu-id="73626-174">Aplikační služba ukázkového procházet aplikace na adrese `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="73626-174">For the sample app service, browse the app at `http://localhost:5000`.</span></span>

1. <span data-ttu-id="73626-175">Zastavit službu s `sc stop {SERVICE NAME}` příkazu.</span><span class="sxs-lookup"><span data-stu-id="73626-175">Stop the service with the `sc stop {SERVICE NAME}` command.</span></span>

   <span data-ttu-id="73626-176">Následující příkaz zastaví aplikační služba ukázkového:</span><span class="sxs-lookup"><span data-stu-id="73626-176">The following command stops the sample app service:</span></span>

   ```console
   sc stop MyService
   ```

1. <span data-ttu-id="73626-177">Po krátké prodlevě zastavit službu, odinstalujte službu s `sc delete {SERVICE NAME}` příkazu.</span><span class="sxs-lookup"><span data-stu-id="73626-177">After a short delay to stop a service, uninstall the service with the `sc delete {SERVICE NAME}` command.</span></span>

   <span data-ttu-id="73626-178">Postup kontroly stavu aplikační služba ukázkového:</span><span class="sxs-lookup"><span data-stu-id="73626-178">Check the status of the sample app service:</span></span>

   ```console
   sc query MyService
   ```

   <span data-ttu-id="73626-179">Pokud je aplikační služba ukázkového v `STOPPED` stavu, použijte následující příkaz pro odinstalaci aplikační služba ukázkového:</span><span class="sxs-lookup"><span data-stu-id="73626-179">When the sample app service is in the `STOPPED` state, use the following command to uninstall the sample app service:</span></span>

   ```console
   sc delete MyService
   ```

## <a name="run-the-app-outside-of-a-service"></a><span data-ttu-id="73626-180">Spuštění aplikace mimo službu</span><span class="sxs-lookup"><span data-stu-id="73626-180">Run the app outside of a service</span></span>

<span data-ttu-id="73626-181">Usnadňuje testování a ladění, když se provozují mimo službu, takže je obvyklý přidáte kód, který volá `RunAsService` pouze za určitých podmínek.</span><span class="sxs-lookup"><span data-stu-id="73626-181">It's easier to test and debug when running outside of a service, so it's customary to add code that calls `RunAsService` only under certain conditions.</span></span> <span data-ttu-id="73626-182">Například aplikace může běžet jako aplikace konzoly v jazyce `--console` argument příkazového řádku nebo pokud je připojen ladicí program:</span><span class="sxs-lookup"><span data-stu-id="73626-182">For example, the app can run as a console app with a `--console` command-line argument or if the debugger is attached:</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=ServiceOrConsole)]

<span data-ttu-id="73626-183">Vzhledem k tomu, že konfigurace ASP.NET Core vyžaduje páry název hodnota pro argumenty příkazového řádku, `--console` přepínač byl předtím, než jsou předány argumenty [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder).</span><span class="sxs-lookup"><span data-stu-id="73626-183">Because ASP.NET Core configuration requires name-value pairs for command-line arguments, the `--console` switch is removed before the arguments are passed to [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder).</span></span>

> [!NOTE]
> <span data-ttu-id="73626-184">`isService` nebude zadán, využije z `Main` do `CreateWebHostBuilder` protože podpis `CreateWebHostBuilder` musí být `CreateWebHostBuilder(string[])` mohl [testování integrace](xref:test/integration-tests) fungovala správně.</span><span class="sxs-lookup"><span data-stu-id="73626-184">`isService` isn't passed from `Main` into `CreateWebHostBuilder` because the signature of `CreateWebHostBuilder` must be `CreateWebHostBuilder(string[])` in order for [integration testing](xref:test/integration-tests) to work properly.</span></span>

## <a name="handle-stopping-and-starting-events"></a><span data-ttu-id="73626-185">Zpracování, spouštění a zastavování události</span><span class="sxs-lookup"><span data-stu-id="73626-185">Handle stopping and starting events</span></span>

<span data-ttu-id="73626-186">Pro zpracování [OnStarting](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice.onstarting), [OnStarted](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice.onstarted), a [OnStopping](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice.onstopping) události, proveďte následující další změny:</span><span class="sxs-lookup"><span data-stu-id="73626-186">To handle [OnStarting](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice.onstarting), [OnStarted](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice.onstarted), and [OnStopping](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice.onstopping) events, make the following additional changes:</span></span>

1. <span data-ttu-id="73626-187">Vytvořte třídu, která je odvozena z [WebHostService](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice):</span><span class="sxs-lookup"><span data-stu-id="73626-187">Create a class that derives from [WebHostService](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice):</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=NoLogging)]

2. <span data-ttu-id="73626-188">Vytvořit metodu rozšíření pro [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost) , který předá vlastní `WebHostService` k [ServiceBase.Run](/dotnet/api/system.serviceprocess.servicebase.run):</span><span class="sxs-lookup"><span data-stu-id="73626-188">Create an extension method for [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost) that passes the custom `WebHostService` to [ServiceBase.Run](/dotnet/api/system.serviceprocess.servicebase.run):</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="73626-189">V `Program.Main`, zavolat novou metodu rozšíření `RunAsCustomService`, namísto [RunAsService](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostwindowsserviceextensions.runasservice):</span><span class="sxs-lookup"><span data-stu-id="73626-189">In `Program.Main`, call the new extension method, `RunAsCustomService`, instead of [RunAsService](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostwindowsserviceextensions.runasservice):</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=HandleStopStart&highlight=17)]

   > [!NOTE]
   > <span data-ttu-id="73626-190">`isService` nebude zadán, využije z `Main` do `CreateWebHostBuilder` protože podpis `CreateWebHostBuilder` musí být `CreateWebHostBuilder(string[])` mohl [testování integrace](xref:test/integration-tests) fungovala správně.</span><span class="sxs-lookup"><span data-stu-id="73626-190">`isService` isn't passed from `Main` into `CreateWebHostBuilder` because the signature of `CreateWebHostBuilder` must be `CreateWebHostBuilder(string[])` in order for [integration testing](xref:test/integration-tests) to work properly.</span></span>

<span data-ttu-id="73626-191">Pokud vlastní `WebHostService` kód vyžaduje službu z injektáž závislostí (jako je například protokolování), získat ze [IWebHost.Services](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost.services) vlastnost:</span><span class="sxs-lookup"><span data-stu-id="73626-191">If the custom `WebHostService` code requires a service from dependency injection (such as a logger), obtain it from the [IWebHost.Services](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost.services) property:</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=Logging&highlight=7-8)]

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="73626-192">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="73626-192">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="73626-193">Služby, které interakci s žádostí z Internetu nebo podnikové síti a jsou za proxy nebo nástroj pro vyrovnávání zatížení může vyžadovat dodatečnou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="73626-193">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="73626-194">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="73626-194">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="73626-195">Konfigurace HTTPS</span><span class="sxs-lookup"><span data-stu-id="73626-195">Configure HTTPS</span></span>

<span data-ttu-id="73626-196">Jak nakonfigurovat službu s koncovým bodem zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="73626-196">To configure the service with a secure endpoint:</span></span>

1. <span data-ttu-id="73626-197">Vytvořte certifikát X.509, který pro hostování systému získání certifikátů vaší platformě a mechanismy nasazení.</span><span class="sxs-lookup"><span data-stu-id="73626-197">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>
1. <span data-ttu-id="73626-198">Zadejte [konfigurace koncového bodu HTTPS serveru Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) mohl certifikát používat.</span><span class="sxs-lookup"><span data-stu-id="73626-198">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="73626-199">Použití certifikátu vývoj pro ASP.NET Core HTTPS k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="73626-199">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="73626-200">Aktuální adresář a obsahu root</span><span class="sxs-lookup"><span data-stu-id="73626-200">Current directory and content root</span></span>

<span data-ttu-id="73626-201">Aktuální pracovní adresář vrátit voláním `Directory.GetCurrentDirectory()` pro službu Windows je *C:\\WINDOWS\\system32* složky.</span><span class="sxs-lookup"><span data-stu-id="73626-201">The current working directory returned by calling `Directory.GetCurrentDirectory()` for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="73626-202">*System32* složka není vhodné umístění pro ukládání souborů služby (například soubory nastavení).</span><span class="sxs-lookup"><span data-stu-id="73626-202">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="73626-203">Použijte jednu z následujících dvou přístupů k zajištění údržby a přístup k prostředky a nastavení souborů pomocí služby [FileConfigurationExtensions.SetBasePath](/dotnet/api/microsoft.extensions.configuration.fileconfigurationextensions.setbasepath) při použití [IConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.iconfigurationbuilder):</span><span class="sxs-lookup"><span data-stu-id="73626-203">Use one of the following approaches to maintain and access a service's assets and settings files with [FileConfigurationExtensions.SetBasePath](/dotnet/api/microsoft.extensions.configuration.fileconfigurationextensions.setbasepath) when using an [IConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.iconfigurationbuilder):</span></span>

* <span data-ttu-id="73626-204">Pomocí obsahu kořenovou cestu.</span><span class="sxs-lookup"><span data-stu-id="73626-204">Use the content root path.</span></span> <span data-ttu-id="73626-205">`IHostingEnvironment.ContentRootPath` Se stejnou cestu k dispozici na `binPath` argument při vytvoření služby.</span><span class="sxs-lookup"><span data-stu-id="73626-205">The `IHostingEnvironment.ContentRootPath` is the same path provided to the `binPath` argument when the service is created.</span></span> <span data-ttu-id="73626-206">Namísto použití `Directory.GetCurrentDirectory()` k vytvoření cesty k souborům nastavení, použijte obsah kořenové cestě a udržovat soubory v kořenové obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="73626-206">Instead of using `Directory.GetCurrentDirectory()` to create paths to settings files, use the content root path and maintain the files in the app's content root.</span></span>
* <span data-ttu-id="73626-207">Store soubory na vhodné místo na disku.</span><span class="sxs-lookup"><span data-stu-id="73626-207">Store the files in a suitable location on disk.</span></span> <span data-ttu-id="73626-208">Zadejte absolutní cestu s `SetBasePath` do složky obsahující soubory.</span><span class="sxs-lookup"><span data-stu-id="73626-208">Specify an absolute path with `SetBasePath` to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73626-209">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="73626-209">Additional resources</span></span>

* <span data-ttu-id="73626-210">[Konfigurace koncového bodu kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (včetně konfigurace protokolu HTTPS a podporu SNI)</span><span class="sxs-lookup"><span data-stu-id="73626-210">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
