---
title: Hosten und Bereitstellen von ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 8a5ac5c58e7ceab07e55da8b61ebb01f7ac984bc
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67153200"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hosten und Bereitstellen von ASP.NET Core Blazor

Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Veröffentlichen der App

Apps werden für die Bereitstellung in Releasekonfigurationen veröffentlicht.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Wählen Sie **Build** > **Publish {APPLICATION}** auf der Navigationsleiste aus.
1. Wählen Sie das *Veröffentlichungsziel* aus. Um lokal zu veröffentlichen, wählen Sie **Ordner** aus.
1. Übernehmen Sie den Standardspeicherort im Feld **Ordner auswählen**, oder geben Sie einen anderen Speicherort an. Wählen Sie die Schaltfläche **Veröffentlichen** aus.

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code / .NET Core-CLI](#tab/visual-studio-code+netcore-cli)

Verwenden Sie den Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) aus, um die App mit einer Releasekonfiguration zu veröffentlichen:

```console
dotnet publish -c Release
```

---

Das Veröffentlichen einer App löst eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts aus und [erstellt](/dotnet/core/tools/dotnet-build) das Projekt, bevor die Objekte für die Bereitstellung erstellt werden. Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren.

Eine clientseitige Blazor-App wird im Ordner */bin/Release/{ZIELFRAMEWORK}/publish/{ASSEMBLYNAME}/dist* veröffentlicht. Eine serverseitige Blazor-App wird im Ordner */bin/Release/{TARGET FRAMEWORK}/publish* veröffentlicht.

Die Objekte im Ordner werden auf dem Webserver bereitgestellt. Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.

## <a name="deployment"></a>Bereitstellung

Anleitungen für die Bereitstellung finden Sie in folgenden Themen:

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>

## <a name="blazor-serverless-hosting-with-azure-storage"></a>Serverloses Blazor-Hosting mit Azure Storage

Clientseitige Blazor-Apps können statische Inhalte aus [Azure Storage](https://azure.microsoft.com/services/storage/) direkt aus einem Speichercontainer erhalten.

Weitere Informationen finden Sie unter [Host and deploy ASP.NET Core Blazor client-side (Standalone deployment) (Hosten und Bereitstellen von clientseitigem ASP.NET Core Blazor (eigenständige Bereitstellung)): Azure Storage](xref:host-and-deploy/blazor/client-side#azure-storage).
