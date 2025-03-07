---
title: Kontobestätigung und kennwortwiederherstellung in ASP.NET Core
author: rick-anderson
description: Informationen Sie zum Erstellen einer ASP.NET Core-app mit e-Mail-Bestätigung und kennwortzurücksetzung.
ms.author: riande
ms.date: 3/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 59041bcf11f7deb351a2f0bb075ed80c8af5e12b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891677"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>Kontobestätigung und kennwortwiederherstellung in ASP.NET Core

::: moniker range="<= aspnetcore-2.0"

Finden Sie unter [diese PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) für die ASP.NET Core 1.1 und Version 2.1.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Durch [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), und [Joe Audette](https://twitter.com/joeaudette)

Dieses Tutorial veranschaulicht das Erstellen eine ASP.NET Core-app mit e-Mail-Bestätigung und kennwortzurücksetzung. Dieses Tutorial ist der **nicht** ein Thema ab. Sie sollten mit vertraut sein:

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [Authentifizierung](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

## <a name="prerequisites"></a>Vorraussetzungen

[.NET Core-2.2-SDK oder höher](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a>Erstellen einer Web-app und das Gerüst für Identity

Führen Sie die folgenden Befehle zum Erstellen einer Web-app mit Authentifizierung.

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a>Testen Sie die Registrierung neuer Benutzer

Die app auszuführen, wählen Sie die **registrieren** verknüpfen, und registrieren Sie einen Benutzer. Die ausschließliche Überprüfung der auf die e-Mail-Adresse an diesem Punkt ist, mit der [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) Attribut. Nach der Übermittlung der Registrierungs, werden Sie bei der app angemeldet. Weiter unten in diesem Tutorial wird der Code aktualisiert, damit neue Benutzer nicht anmelden können, bis ihre e-Mail-Adresse überprüft wird.

[!INCLUDE[](~/includes/view-identity-db.md)]

Beachten Sie die Tabelle `EmailConfirmed` Feld `False`.

Sie möchten möglicherweise verwenden Sie diese e-Mail erneut im nächsten Schritt, wenn die app eine Bestätigung per e-Mail sendet. Mit der rechten Maustaste auf die Zeile, und wählen **löschen**. Löschen den e-Mail-Alias erleichtert es in den folgenden Schritten.

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>E-Mail-Bestätigung erforderlich

Es ist eine bewährte Methode, die e-Mail-Adresse einer neuen benutzerregistrierung zu bestätigen. E-Mail-Bestätigung können, um zu überprüfen, ob sie sind eine andere Person keinen Identitätswechsel (d. h. sie noch nicht registriert mit einer anderen Person für den e-Mail-Adresse). Angenommen, Sie haben ein Diskussionsforum, und Sie möchten, um zu verhindern, dass "yli@example.com"aus der Registrierung als"nolivetto@contoso.com". Ohne e-Mail-Bestätigung "nolivetto@contoso.com" könnte unerwünschte e-Mail-Adresse aus Ihrer app erhalten. Angenommen, das der Benutzer versehentlich als registriert "ylo@example.com" und noch nicht bemerkt, dass die falsche Schreibweise von "Yli". Sie wäre nicht kennwortwiederherstellung zu verwenden, da die app die korrekte e-Mail-Adresse besitzt. E-Mail-Bestätigung enthält nur begrenzten Schutz von Bots. E-Mail-Bestätigung stellt keinen Schutz vor böswilligen Benutzern viele e-Mail-Konten bereit.

Im Allgemeinen möchten Sie verhindern, dass neue Benutzer keine Daten zu Ihrer Website veröffentlichen, bevor sie eine e-Mail bestätigte haben.

Update `Startup.ConfigureServices` eine bestätigte e-Mail-Adresse erforderlich ist:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;` verhindert, dass registrierte Benutzer anmelden, bis ihre-e-Mail bestätigt ist.

### <a name="configure-email-provider"></a>Konfigurieren von e-Mail-Anbieter

In diesem Tutorial [SendGrid](https://sendgrid.com) wird verwendet, um e-Mail zu senden. Sie benötigen eine SendGrid-Konto und einen Schlüssel zum Senden von e-Mails. Sie können andere e-Mail-Anbieter verwenden. ASP.NET Core 2.x enthält `System.Net.Mail`, wodurch Sie zum Senden von e-Mail-Adresse aus Ihrer app. Es wird empfohlen, dass Sie über SendGrid oder eine andere e-Mail-Dienst verwenden, um e-Mail zu senden. SMTP ist schwierig, sichere und ordnungsgemäß eingerichtet.

Erstellen Sie eine Klasse, um den Schlüssel für sichere e-Mails abrufen. In diesem Beispiel erstellen *Services/AuthMessageSenderOptions.cs*:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Konfigurieren von SendGrid benutzergeheimnisse

Legen Sie die `SendGridUser` und `SendGridKey` mit der [Secret-Manager-Tool](xref:security/app-secrets). Zum Beispiel:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Auf Windows, speichert Secret Manager-Schlüssel/Wert-Paare in einem *secrets.json* Datei die `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` Verzeichnis.

Den Inhalt der *secrets.json* Datei sind nicht verschlüsselt. Das folgende Markup zeigt die *secrets.json* Datei. Die `SendGridKey` Wert entfernt wurde.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Weitere Informationen finden Sie unter den [optionsmuster](xref:fundamentals/configuration/options) und [Konfiguration](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Installieren von SendGrid

In diesem Tutorial wird gezeigt, wie Hinzufügen von e-Mail-Benachrichtigungen über [SendGrid](https://sendgrid.com/), aber Sie können e-Mails über SMTP und andere Mechanismen senden.

Installieren Sie die `SendGrid` NuGet-Paket:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Geben Sie über die Paket-Manager-Konsole den folgenden Befehl aus:

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Geben Sie in der Konsole den folgenden Befehl aus:

```cli
dotnet add package SendGrid
```

---

Finden Sie unter [kostenlos erste Schritte mit SendGrid](https://sendgrid.com/free/) für ein kostenloses SendGrid-Konto zu registrieren.

### <a name="implement-iemailsender"></a>Implementieren von IEmailSender

Das implementieren `IEmailSender`, erstellen Sie *Services/EmailSender.cs* mit ähnlich dem folgenden Code:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Konfigurieren Sie beim Start zur Unterstützung von e-Mail-Adresse

Fügen Sie den folgenden Code der `ConfigureServices` -Methode in der die *"Startup.cs"* Datei:

* Hinzufügen `EmailSender` als vorübergehender Dienst.
* Registrieren der `AuthMessageSenderOptions` konfigurationsinstanz.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>Konto-Bestätigung und -Wiederherstellung aktivieren

Die Vorlage weist den Code für die Konto-Bestätigung und -Wiederherstellung. Suchen der `OnPostAsync` -Methode in der *Areas/Identity/Pages/Account/Register.cshtml.cs*.

Verhindern Sie, dass neu registrierte Benutzern automatisch durch die Anmeldung durch die Sie die folgende Zeile Kommentierung:

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

Die complete-Methode wird mit der geänderten Zeile hervorgehoben dargestellt:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>Registrieren und bestätigen Sie die e-Mail-Adresse und die kennwortzurücksetzung

Führen Sie die Web-app, und Testen Sie die kontobestätigung und das Kennwort eine Wiederherstellung durchführen.

* Die app ausführen und einen neuen Benutzer registrieren
* Überprüfen Sie Ihre e-Mail-Adresse für die der Link für die Bestätigung. Finden Sie unter [Debuggen-e-Mail](#debug) sollten Sie die e-Mail nicht erhalten.
* Klicken Sie auf den Link, um Ihre e-Mail-Adresse zu bestätigen.
* Melden Sie sich mit Ihren e-Mail-Adresse und Ihr Kennwort.
* Melden Sie sich ab.

### <a name="view-the-manage-page"></a>Anzeigen der Seite "verwalten"

Wählen Sie Ihren Benutzernamen im Browser: ![Browserfenster mit Benutzername](accconfirm/_static/un.png)

Seite "verwalten" wird angezeigt, mit der **Profil** Registerkarte ausgewählt. Die **-e-Mail** zeigt ein Kontrollkästchen, der angibt, der e-Mail-Adresse wurde bestätigt.

### <a name="test-password-reset"></a>Test Zurücksetzen des Kennworts

* Wenn Sie sich angemeldet haben, wählen Sie **Logout**.
* Wählen Sie die **melden Sie sich bei** verknüpfen, und wählen Sie die **haben Ihr Kennwort vergessen?** Link.
* Geben Sie die e-Mail-Adresse, die Sie verwendet, um das Konto zu registrieren.
* Es wird eine e-Mail mit einem Link zum Zurücksetzen Ihres Kennworts gesendet. Überprüfen Sie Ihre e-Mail-Adresse ein, und klicken Sie auf den Link zum Zurücksetzen Ihres Kennworts. Nachdem Sie Ihr Kennwort erfolgreich zurückgesetzt wurde, können Sie sich mit Ihrem e-Mail-Adresse und das neue Kennwort anmelden.

## <a name="change-email-and-activity-timeout"></a>Ändern Sie die e-Mail-Adresse und der Aktivität ein timeout

Das Standardtimeout für die Inaktivität beträgt 14 Tage. Im folgenden Code wird das Timeout bei Inaktivität bis 5 Tagen:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Ändern Sie alle Data Protection-token-Gültigkeitsdauer

Der folgende Code ändert alle Data Protection Token Zeitlimit bis drei Stunden:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

Die integrierten im Benutzertoken für die Identität (finden Sie unter [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) haben eine [Timeout für einen Tag](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Ändern Sie die Lebensdauer des Zugriffstoken-e-Mail

Die Standard-token-Lebensdauer von [das Benutzertoken für die Identität](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ist [eines Tages](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). In diesem Abschnitt veranschaulicht, wie die Lebensdauer des Zugriffstoken-e-Mail zu ändern.

Hinzufügen ein benutzerdefinierten [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) und <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Fügen Sie den benutzerdefinierten Anbieter zum Dienstcontainer hinzu:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>Erneutes Senden von e-Mail-Bestätigung

Finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore/issues/5410).

<a name="debug"></a>

### <a name="debug-email"></a>Debuggen von e-Mail-Adresse

Wenn Sie e-Mail-Adresse funktioniert nicht:

* Festlegen eines Haltepunkts im `EmailSender.Execute` überprüfen `SendGridClient.SendEmailAsync` aufgerufen wird.
* Erstellen Sie eine [Konsolen-app zum Senden von e-Mails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mit ähnlichen Code `EmailSender.Execute`.
* Überprüfen Sie die [-e-Mail-Aktivität](https://sendgrid.com/docs/User_Guide/email_activity.html) Seite.
* Überprüfen Sie Ihren Spam-Ordner.
* Versuchen Sie es einer anderen e-Mail-Alias für einen anderen e-Mail-Anbieter (Microsoft, Yahoo, Gmail usw.)
* Versuchen Sie es an andere e-Mail-Konten gesendet.

**Eine bewährte Sicherheitsmethode** besteht darin, **nicht** produktionsgeheimnisse in Test- und entwicklungsumgebungen verwendet. Wenn Sie die app in Azure veröffentlichen, können Sie die SendGrid-Geheimnisse Anwendungseinstellungen im Azure-Web-App-Portal festlegen. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.

## <a name="combine-social-and-local-login-accounts"></a>Kombinieren Sie Konten für soziale Netzwerke und lokale Anmeldung

Um diesen Abschnitt abgeschlossen haben, müssen Sie zuerst einen externer Authentifizierungsanbieter aktivieren. Finden Sie unter [Facebook, Google und externe Anbieter Authentifizierung](xref:security/authentication/social/index).

Sie können lokale als auch social kombinieren, durch Klicken auf Ihre e-Mail-Link. In der folgenden Reihenfolge "RickAndMSFT@gmail.com" wird zuerst als eine lokale Anmeldung; erstellt, Sie können jedoch zunächst erstellen Sie das Konto als Anmeldedaten eines sozialen Netzwerks, und fügen Sie eine lokale Anmeldung hinzu.

![Web-Anwendung: RickAndMSFT@gmail.com authentifizierte Benutzer](accconfirm/_static/rick.png)

Klicken Sie auf die **verwalten** Link. Beachten Sie die 0 externe (Anmeldungen per sozialem Netzwerk) mit diesem Konto verknüpft.

![Verwalten von anzeigen](accconfirm/_static/manage.png)

Klicken Sie auf den Link, um einen anderen Anmeldenamen-Dienst, und akzeptieren Sie die app-Anforderungen. In der folgenden Abbildung ist die Facebook dem externen Authentifizierungsanbieter:

![Verwalten Sie Ihre Facebook auflisten externer Anmeldungen-Ansicht](accconfirm/_static/fb.png)

Die beiden Konten wurden kombiniert. Sie können die Anmeldung der Konten. Sie sollten Ihre Benutzer lokale Konten hinzufügen, falls ihre sozialen Authentifizierungsdiensts ausgefallen ist oder eher sie den Zugriff auf ihre Konten sozialer Netzwerke verlieren.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Kontobestätigung zu aktivieren, nachdem Sie einen Standort der Benutzer enthält

Aktivierung kontobestätigung auf einer Website für Benutzer, alle vorhandenen Benutzer sperren. Vorhandene Benutzer werden gesperrt, da es sich bei ihrem Konto bestätigt werden nicht. Um vorhandene Benutzersperre zu umgehen, verwenden Sie einen der folgenden Ansätze:

* Ändern der Datenbank markieren Sie alle vorhandenen Benutzer als bestätigt wird.
* Vergewissern Sie sich vorhandene Benutzer. Z. B. Batch-Send-e-Mails mit Bestätigung Links.

::: moniker-end
