---
title: Schreiben von benutzerdefinierter ASP.NET Core-Middleware
author: rick-anderson
description: Erfahren Sie, wie benutzerdefinierte ASP.NET Core-Middleware geschrieben wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/17/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: 352db93dd7061070c76e34f6c03883f68e2041ee
ms.sourcegitcommit: 28a2874765cefe9eaa068dceb989a978ba2096aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67167108"
---
# <a name="write-custom-aspnet-core-middleware"></a>Schreiben von benutzerdefinierter ASP.NET Core-Middleware

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://ardalis.com/)

Middleware ist Software, die zu einer Anwendungspipeline zusammengesetzt wird, um Anforderungen und Antworten zu verarbeiten. ASP.NET Core stellt in umfangreichem Maß integrierte Middlewarekomponenten zur Verfügung. In manchen Szenarios möchten Sie aber vielleicht selbst eine benutzerdefinierte Middleware schreiben.

## <a name="middleware-class"></a>Middlewareklasse

Für gewöhnlich ist Middleware in einer Klasse gekapselt und wird mit einer Erweiterungsmethode verfügbar gemacht. Sehen Sie sich folgende Middleware an, die die Kultur der aktuellen Anforderung über eine Abfragezeichenfolge festlegt:

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

Im vorhergehenden Beispielcode wird die Erstellung einer Middlewarekomponente veranschaulicht. Informationen zur Unterstützung der integrierten Lokalisierung für ASP.NET Core finden Sie unter <xref:fundamentals/localization>.

Testen Sie die Middleware, indem Sie die Kultur übergeben. Fordern Sie beispielsweise `https://localhost:5001/?culture=no` an.

Im folgenden Code wird der Middlewaredelegat in eine Klasse verschoben:

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

Die Middlewareklasse muss Folgendes enthalten:

* Einen öffentlichen Konstruktor mit einem Parameter des Typs <xref:Microsoft.AspNetCore.Http.RequestDelegate>.
* Eine öffentliche Methode mit dem Namen `Invoke` oder `InvokeAsync`. Diese Methode muss Folgendes:
  * Eine `Task` zurückgeben.
  * Einen ersten Parameter des Typs <xref:Microsoft.AspNetCore.Http.HttpContext> akzeptieren.
  
Zusätzliche Parameter für den Konstruktor und `Invoke`/`InvokeAsync` werden mittels [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) aufgefüllt.

## <a name="middleware-dependencies"></a>Middlewareabhängigkeiten

Middleware sollte das [Prinzip der expliziten Abhängigkeiten](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) befolgen, indem sie ihre Abhängigkeiten in ihrem Konstruktor verfügbar macht. Middleware wird einmal während der *Anwendungslebensdauer* erstellt. Lesen Sie den Abschnitt [Voranforderungsbasierte Middlewareabhängigkeiten](#per-request-middleware-dependencies), wenn Sie Dienste für Middleware innerhalb einer Anforderung gemeinsam verwenden müssen.

Middlewarekomponenten können Ihre Abhängigkeiten über [Dependency Injection (DI)](xref:fundamentals/dependency-injection) mit Konstruktorparametern auflösen. [UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) kann auch direkt zusätzliche Parameter annehmen.

## <a name="per-request-middleware-dependencies"></a>Voranforderungsbasierte Middlewareabhängigkeiten

Weil Middleware zum Zeitpunkt des Anwendungsstarts erstellt wird (und nicht voranforderungsbasiert), werden *bereichsbezogene* Lebensdauerdienste von Middlewarekonstruktoren in den einzelnen Anforderungen nicht gemeinsam mit anderen Typen mit Dependency Injection verwendet. Wenn Sie einen *bereichsbezogenen* Dienst sowohl in Ihrer Middleware als auch in anderen Typen verwenden müssen, fügen Sie diese Dienste zur Signatur der `Invoke`-Methode hinzu. Die `Invoke`-Methode kann zusätzliche Parameter akzeptieren, die durch DI aufgefüllt werden:

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a>Erweiterungsmethode für die Middleware

Die folgende Erweiterungsmethode stellt die Middleware über <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> zur Verfügung:

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

Der folgende Code ruft die Methode von `Startup.Configure` auf:

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
