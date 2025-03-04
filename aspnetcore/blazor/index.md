---
title: Einführung in Blazor in ASP.NET Core
author: guardrex
description: Lernen Sie ASP.NET Core Blazor kennen, eine Möglichkeit, interaktive clientseitige Webbenutzeroberflächen mit .NET in einer ASP.NET Core-App zu erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 07/01/2019
uid: blazor/index
ms.openlocfilehash: dbfadf9481cf16279e2a491bc04f51058e1d8a9c
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500427"
---
# <a name="introduction-to-blazor"></a>Einführung in Blazor

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

*Willkommen bei Blazor!*

Blazor ist ein Framework zum Erstellen von interaktiver clientseitiger Webbenutzeroberflächen mit .NET:

* Erstellen Sie umfassende interaktive Benutzeroberflächen mit C# anstatt mit JavaScript.
* Gemeinsames Verwenden von server- und clientseitiger App-Logik, die in .NET geschrieben wurde.
* Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).

Die Verwendung von .NET im für die clientseitige Webentwicklung bietet die folgenden Vorteile:

* Schreiben Sie Code in C# anstatt in JavaScript.
* Nutzen Sie das vorhandene .NET-Ökosystem von .NET-Bibliotheken.
* Geben Sie die App-Logik server- und clientübergreifend frei.
* Profitieren Sie von Leistung, Zuverlässigkeit und Sicherheit von .NET.
* Bleiben Sie mit Visual Studio unter Windows, Linux und macOS produktiv.
* Setzen Sie auf gebräuchliche Sprachen, Frameworks und Tools, die stabil, funktionsreich und einfach zu verwenden sind.

## <a name="components"></a>Komponenten

Blazor-Apps basieren auf *Komponenten*. Eine Komponente in Blazor ist ein Element der Benutzeroberfläche, beispielsweise eine Seite, ein Dialogfeld oder ein Formular für die Dateneingabe.

Komponenten sind in .NET-Assemblys integrierte .NET-Klassen, auf die Folgendes zutrifft:

* Sie definieren die Logik für ein flexibles Rendering der Benutzeroberfläche.
* Sie behandeln Benutzerereignisse.
* Sie können geschachtelt und wiederverwendet werden.
* Sie können als [Razor-Klassenbibliotheken](xref:razor-pages/ui-class) oder [NuGet-Pakete](/nuget/what-is-nuget) freigegeben und verteilt werden.

Die Komponentenklasse wird in der Regel in Form einer [Razor](xref:mvc/views/razor)-Markupseite mit der Dateierweiterung *.razor* geschrieben. Komponenten in Blazor werden formal als *Razor-Komponenten* bezeichnet. Razor ist eine Syntax, mit der HTML-Markup mit C#-Code kombiniert werden kann, ausgerichtet auf die Produktivität der Entwickler. Razor ermöglicht es Ihnen, mit [IntelliSense](/visualstudio/ide/using-intellisense)-Unterstützung zwischen HTML-Markup und C# in derselben Datei zu wechseln. Razor Pages und MVC verwenden ebenfalls Razor. Im Gegensatz zu Razor Pages und MVC, die auf einem Anforderung/Antwort-Modell aufbauen, werden Komponenten speziell für die clientseitige Benutzeroberflächenlogik und -gestaltung verwendet.

Das folgende Razor-Markup veranschaulicht eine Komponente (*Dialog.razor*), die in eine andere Komponente verschachtelt werden kann:

```cshtml
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="@OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    private string Title { get; set; }

    [Parameter]
    private RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

Der Textinhalt (`ChildContent`) und der Titel (`Title`) des Dialogfelds werden von der Komponente bereitgestellt, die diese Komponente in ihrer Benutzeroberfläche verwendet. `OnYes` ist eine C#-Methode, die vom Schaltflächenereignis `onclick` ausgelöst wird.

Blazor verwendet die natürlichen HTML-Tags für die Benutzeroberflächengestaltung. HTML-Elemente legen Komponenten fest, und die Attribute eines Tags übergeben Werte an die Eigenschaften einer Komponente.

Im folgenden Beispiel verwendet die `Index`-Komponente die `Dialog`-Komponente. `ChildContent` und `Title` werden durch die Attribute und den Inhalt des `<Dialog>`-Elements festgelegt.

*Index.razor*:

```cshtml
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

Das Dialogfeld wird gerendert, wenn die übergeordnete Komponente (*Index.razor*) in einem Browser aufgerufen wird:

![Im Browser gerenderte Dialogfeldkomponente](index/_static/dialog.png)

Wenn diese Komponente in der App verwendet wird, beschleunigt IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) und [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) die Entwicklung mithilfe von Syntax- und Parametervervollständigung.

Die Komponenten werden in einer In-Memory-Darstellung des Browser-DOM (Document Object Model) gerendert, die als *Renderbaum* bezeichnet und verwendet wird, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.

## <a name="blazor-client-side"></a>Clientseitiges Blazor

Clientseitiges Blazor ist ein Single-Page-App-Framework zum Erstellen von interaktiven clientseitigen Web-Apps mit .NET. Blazor verwendet offene Webstandards ohne Plug-Ins oder Codetranspilation und funktioniert in allen modernen Webbrowsern, einschließlich mobiler Browser.

Das Ausführen von .NET-Code in einem Webbrowser wird durch [WebAssembly](http://webassembly.org) (Kurzform: *wasm*) ermöglicht. Es handelt sich um ein Bytecodeformat, das für schnelles Downloaden und die maximale Ausführungsgeschwindigkeit optimiert ist. WebAssembly ist ein offener Webstandard, der in Webbrowsern ohne Plug-Ins unterstützt wird.

WebAssembly-Code kann auf alle Funktionen des Browsers über JavaScript, auch als *JavaScript-Interoperabilität* (oder *JavaScript-Interop*) bezeichnet, zugreifen. Über WebAssembly im Browser ausgeführter .NET-Code wird in der JavaScript-Sandbox des Browsers ausgeführt. Hierbei greifen die Schutzmaßnahmen der Sandbox gegen schädliche Aktionen auf dem Clientcomputer.

![Clientseitiges Blazor führt .NET-Code unter Verwendung von WebAssembly im Browser aus.](index/_static/blazor-client-side.png)

Folgendes geschieht, wenn eine clientseitige Blazor-App in einem Browser erstellt und ausgeführt wird:

* C#-Codedateien und Razor-Dateien werden in .NET-Assemblys kompiliert.
* Die Assemblys und die .NET-Runtime werden im Browser heruntergeladen.
* Clientseitiges Blazor startet die .NET-Runtime und konfiguriert die Runtime zum Laden der Assemblys für die App. DOM-Änderungen und API-Aufrufe im Browser werden von der clientseitigen Blazor-Runtime über die JavaScript-Interop verarbeitet.

Die Größe der veröffentlichten App, ihre *Nutzlast*, ist ein wichtiger Leistungsfaktor für die Nutzbarkeit einer App. Das Herunterladen einer großen App in einen Browser dauert relativ lange, was die Benutzerfreundlichkeit beeinträchtigt. Clientseitiges Blazor optimiert die Nutzlastgröße, um die Downloadzeiten zu verkürzen:

* Nicht verwendeter Code wird aus der App entfernt, wenn sie vom [IL-Linker (Intermediate Language)](xref:host-and-deploy/blazor/configure-linker) veröffentlicht wird.
* HTTP-Antworten werden komprimiert.
* Die .NET-Runtime und die Assemblys werden im Browser zwischengespeichert.

## <a name="blazor-server-side"></a>Serverseitiges Blazor

Blazor entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche. Serverseitiges Blazor bietet Unterstützung zum Hosten von Razor-Komponenten in einer ASP.NET Core-App auf dem Server. Aktualisierungen der Benutzeroberfläche werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.

Die Runtime handhabt das Senden von Benutzeroberflächenereignissen vom Browser an den Server und wendet Benutzeroberflächenupdates an, die nach dem Ausführen der Komponenten vom Server zurück an den Browser gesendet werden.

Die Verbindung, die von serverseitigem Blazor für die Kommunikation mit dem Browser verwendet wird, wird auch für die Verarbeitung von JavaScript-Interopaufrufen verwendet.

![Serverseitiges Blazor führt .NET-Code auf dem Server aus und interagiert über eine SignalR-Verbindung mit dem Dokumentobjektmodell.](index/_static/blazor-server-side.png)

## <a name="javascript-interop"></a>JavaScript-Interoperabilität

Für Apps, die JavaScript-Bibliotheken und Zugriff auf Browser-APIs von Drittanbietern benötigen, sind die Komponenten für die Interoperabilität mit JavaScript konzipiert. Komponenten können jede Bibliothek oder API verwenden, die auch von JavaScript verwendet werden kann. C#-Code kann JavaScript-Code abfragen, und umgekehrt. Weitere Informationen finden Sie unter <xref:blazor/javascript-interop>.

## <a name="code-sharing-and-net-standard"></a>Codefreigabe und .NET Standard

Blazor implementiert [.NET Standard 2.0](/dotnet/standard/net-standard). .NET Standard ist eine formale Spezifikation von .NET-APIs, die häufig für .NET-Implementierungen verwendet werden. .NET-Standardklassenbibliotheken können auf verschiedenen .NET-Plattformen wie Blazor, .NET Framework, .NET Core, Xamarin, Mono und Unity freigegeben werden.

APIs, die nicht in einem Webbrowser angewendet werden können (z.B. zum Zugreifen auf ein Dateisystem, zum Öffnen eines Sockets und für das Threading), lösen die Ausnahme <xref:System.PlatformNotSupportedException> aus.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [WebAssembly](http://webassembly.org/)
* <xref:blazor/hosting-models>
* [Leitfaden für C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
