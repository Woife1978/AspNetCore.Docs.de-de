---
title: Erstellen Ihrer ersten Blazor-App
author: guardrex
description: Erstellen Sie Schritt-für-Schritt eine Blazor-App.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: df27dad17133f287b1c73dc308b4cc69426e0a63
ms.sourcegitcommit: 739a3d7ca4fd2908ea0984940eca589a96359482
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67040714"
---
# <a name="build-your-first-blazor-app"></a>Erstellen Ihrer ersten Blazor-App

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

In diesem Tutorial erfahren Sie, wie Sie eine Blazor-App erstellen und ändern.

Befolgen Sie die Anweisungen im Artikel <xref:blazor/get-started>, um ein Blazor-Projekt für dieses Tutorial zu erstellen.

## <a name="build-components"></a>Erstellen von Komponenten

1. Navigieren Sie zu jeder der drei Seiten der App im Ordner *Pages*: „Home“, „Counter“ und „Fetch data“. Diese Seiten werden durch die Razor-Komponentendateien *Index.razor*, *Counter.razor* und *FetchData.razor* implementiert.

1. Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen. Das Heraufsetzen eines Zählers auf einer Webseite erfordert normalerweise das Schreiben in JavaScript, aber Blazor bietet einen besseren Ansatz mit C#.

1. Sehen Sie sich die Implementierung der Counter-Komponente in der *Counter.razor*-Datei an.

   *Pages/Counter.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   Die Benutzeroberfläche der Counter-Komponente wird mithilfe von HTML definiert. Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt. Das HTML-Markup und die C#-Renderinglogik werden zur Erstellungszeit in eine Komponentenklasse konvertiert. Der Name der generierten .NET-Klasse stimmt mit dem Namen der Datei überein.

   Member der Komponentenklasse werden in einem `@code`-Block definiert. Im `@code`-Block werden der Komponentenstatus (Eigenschaften, Felder) und Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben. Diese Member werden dann als Teil der Renderinglogik der Komponente und zur Behandlung von Ereignissen verwendet.

   Wenn die **Hier klicken**-Schaltfläche ausgewählt wird:

   * Der registrierte `onclick`-Handler der Counter-Komponente wird aufgerufen (die `IncrementCount`-Methode).
   * Die Counter-Komponente generiert ihre Renderstruktur neu.
   * Die neue Renderstruktur wird mit der vorherigen verglichen.
   * Es werden nur Änderungen des Dokumentobjektmodells (Document Object Model, DOM) angewendet. Der angezeigte Zähler wird aktualisiert.

1. Ändern Sie die C#-Logik der Counter-Komponente, um das Zählerinkrement von eins auf zwei heraufzusetzen.

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Erstellen Sie die App neu, und führen Sie sie aus, um die Änderungen anzuzeigen. Wählen Sie die Schaltfläche **Hier klicken** aus. Der Leistungsindikator wird um zwei erhöht.

## <a name="use-components"></a>Verwenden von Komponenten

Beziehen Sie eine Komponente mithilfe einer HTML-Syntax in eine andere Komponente ein.

1. Fügen Sie die Counter-Komponente der Index-Komponente der App durch Hinzufügen eines `<Counter />`-Elements zur Index-Komponente (*Index.razor*) hinzu.

   Wenn Sie für diese Oberfläche die Blazor-Clientseite verwenden, enthält die Index-Komponente eine Survey Prompt-Komponente (`<SurveyPrompt>`-Element). Ersetzen Sie das `<SurveyPrompt>`-Element durch das `<Counter>`-Element. Wenn Sie für diese Oberfläche eine serverseitige Blazor-App verwenden, fügen Sie das `<Counter>`-Element zur Index-Komponente hinzu:

   *Pages/Index.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Erstellen Sie die App neu, und führen Sie sie aus. Die Index-Komponente verfügt über einen eigenen Leistungsindikator.

## <a name="component-parameters"></a>Komponentenparameter

Komponenten können auch Parameter aufweisen. Komponentenparameter werden mithilfe nicht öffentlicher Eigenschaften für die Komponentenklasse definiert, die mit `[Parameter]` versehen ist. Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.

1. Aktualisieren Sie den `@code`-C#-Code der Komponente:

   * Fügen Sie eine `IncrementAmount`-Eigenschaft hinzu, die mit dem `[Parameter]`-Attribut ausgestattet ist.
   * Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.

   *Pages/Counter.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. Geben Sie unter Verwendung eines Attributs einen `IncrementAmount`-Parameter in das `<Counter>`-Element der Index-Komponente ein. Legen Sie den Wert fest, um den Zähler um zehn heraufzusetzen.

   *Pages/Index.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Laden Sie die Index-Komponente neu. Der Zähler wird jedes Mal, wenn die Schaltfläche **Hier klicken** ausgewählt wird, um zehn heraufgesetzt. Der Zähler in der Counter-Komponente wird weiter um eins erhöht.

## <a name="route-to-components"></a>Weiterleiten an Komponenten

Die `@page`-Anweisung im oberen Teil der *Counter.razor*-Datei gibt an, dass die Counter-Komponente ein Routingendpunkt ist. Die Counter-Komponente behandelt Anforderungen, die an `/counter` gesendet werden. Ohne die `@page`-Anweisung behandelt eine Komponente keine weitergeleiteten Anforderungen, aber die Komponente kann immer noch von anderen Komponenten verwendet werden.

## <a name="dependency-injection"></a>Dependency Injection

Im Dienstcontainer der App registrierte Dienste stehen für Komponenten über [Abhängigkeitsinjektion (DI, Dependency Injection)](xref:fundamentals/dependency-injection) zur Verfügung. Fügen Sie Dienste mit der `@inject`-Anweisung in eine Komponente ein.

Sehen Sie sich die Anweisungen der FetchData-Komponente an.

Wenn Sie eine serverseitige Blazor-App verwenden, ist der `WeatherForecastService`-Dienst als [Singleton](xref:fundamentals/dependency-injection#service-lifetimes) registriert, sodass eine Instanz des Diensts in der gesamten App verfügbar ist. Mit der `@inject`-Anweisung wird die Instanz des `WeatherForecastService`-Diensts in die Komponente eingefügt.

*Pages/FetchData.razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Die FetchData-Komponente verwendet den eingefügten Dienst als `ForecastService`, um ein Array von `WeatherForecast`-Objekten abzurufen:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

Wenn Sie eine clientseitige Blazor-App verwenden, wird `HttpClient` eingefügt, um Wettervorhersagedaten aus der Datei *weather.json* in den Ordner *wwwroot/sample-data* zu abzurufen:

*Pages/FetchData.razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

Eine [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife rendert jede Vorhersageinstanz als eine Zeile in der Wetterdatentabelle:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]


## <a name="build-a-todo-list"></a>Erstellen einer Aufgabenliste

Fügen Sie der App eine neue Komponente hinzu, die eine einfache Aufgabenliste implementiert.

1. Fügen Sie der App im Ordner *Pages* eine leere Datei mit dem Namen *Todo.razor* hinzu:

1. Geben Sie das ursprüngliche Markup für die Komponente an:

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. Fügen Sie der Navigationsleiste die Todo-Komponente hinzu.

   Die NavMenu-Komponente (*Shared/NavMenu.razor*) wird im Layout der App verwendet. Layouts sind Komponenten, mit denen Sie die Duplizierung des Inhalts in der App vermeiden können. Weitere Informationen finden Sie unter <xref:blazor/layouts>.

   Fügen Sie einen `<NavLink>` für die Todo-Komponente hinzu, indem Sie das folgende Listenelementmarkup unterhalb der vorhandenen Listenelemente der Datei *Shared/NavMenu.razor* hinzufügen:

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Erstellen Sie die App neu, und führen Sie sie aus. Besuchen Sie die neue Todo-Seite, um sicherzustellen, dass der Link zur Todo-Komponente funktioniert.

1. Fügen Sie eine *TodoItem.cs*-Datei dem Stamm des Projekts hinzu, die eine Klasse zum Darstellen eines Aufgabenelements enthalten soll. Verwenden Sie den folgenden C#-Code für die `TodoItem`-Klasse:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Kehren Sie zur Todo-Komponente (*Pages/Todo.razor*) zurück:

   * Fügen Sie ein Feld für die Aufgabenelemente in einem `@code`-Block hinzu. Die Todo-Komponente verwaltet mit diesem Feld den Status der Aufgabenliste.
   * Fügen Sie ein Markup für eine unsortierte Liste und eine `foreach`-Schleife hinzu, um jedes Aufgabenelement als Listenelement zu rendern.

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Die App benötigt Benutzeroberflächenelemente, damit der Liste Aufgabenelemente hinzugefügt werden können. Fügen Sie eine Texteingabe und eine Schaltfläche unterhalb der Liste hinzu:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Erstellen Sie die App neu, und führen Sie sie aus. Bei Auswahl der Schaltfläche **Todo-Element hinzufügen** geschieht nichts, da kein Ereignishandler mit der Schaltfläche verknüpft ist.

1. Fügen Sie eine `AddTodo`-Methode der Todo-Komponente hinzu, und registrieren Sie sie mit dem `@onclick`-Attribut für Schaltflächenklicks:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   Die `AddTodo`-C#-Methode wird aufgerufen, wenn die Schaltfläche ausgewählt wird.

1. Um den Titel des neuen Aufgabenelements abzurufen, fügen Sie ein `newTodo`-Zeichenfolgenfeld hinzu, und binden Sie es mithilfe des `bind`-Attributs an den Wert der Texteingabe:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="@newTodo" />
   ```

1. Aktualisieren Sie die `AddTodo`-Methode, um das `TodoItem` mit dem angegebenen Titel der Liste hinzuzufügen. Löschen Sie den Wert der Texteingabe, indem Sie für `newTodo` eine leere Zeichenfolge festlegen:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Erstellen Sie die App neu, und führen Sie sie aus. Fügen Sie der Aufgabenliste einige Aufgabenelemente hinzu, um den neuen Code zu testen.

1. Der Titeltext für die einzelnen Aufgabenelemente kann bearbeitet werden, und ein Kontrollkästchen kann dem Benutzer helfen, die erledigten Elemente nachzuverfolgen. Fügen Sie für jedes Aufgabenelement eine Kontrollkästcheneingabe hinzu, und binden Sie ihren Wert an die `IsDone`-Eigenschaft. Ändern Sie `@todo.Title` in ein `<input>`-Element, das an `@todo.Title` gebunden ist:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Um sicherzustellen, dass diese Werte gebunden sind, aktualisieren Sie den `<h1>`-Header, um die Anzahl der noch nicht erledigten Aufgabenelemente anzuzeigen (`IsDone` ist `false`).

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. Die erledigte Todo-Komponente (*Pages/Todo.razor*):

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Erstellen Sie die App neu, und führen Sie sie aus. Fügen Sie Aufgabenelemente hinzu, um den neuen Code zu testen.

## <a name="publish-and-deploy-the-app"></a>Veröffentlichen und Bereitstellen der App

Wie Sie die App veröffentlichen, erfahren Sie unter <xref:host-and-deploy/blazor/index>.
