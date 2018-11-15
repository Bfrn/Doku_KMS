# Proposal
## Welches Projekt?
Die [Godot Engine](https://github.com/godotengine/godot) ist eine simple platformunabhängige Game-Engine.
## Welche Issues?
Wir beschäftigen uns mit dem folgenden [Issue](https://github.com/godotengine/godot/issues/23250), bei dem eine unerwartete Invertierung von Kacheln-Koordinaten einer Welt-Matrix auftritt.

## Komplexitätsübersicht
Die Komplexität des Issues ist in Gänze noch nicht abzusehen, da zunächst eine Einarbeitung in die Code-Basis und der mathematischen Grundlagen nötig ist.

## Geplantes Vorgehen
Zunächst beschäftigen wir uns mit der [contributing guidelines](https://github.com/godotengine/godot/blob/master/CONTRIBUTING.md) und wir installieren uns die Software, die wir benötigen um an dem Issue zu arbeiten. Danach ist geplant durch Testen die Fehlerquelle zu identifizieren und einen Lösungsvorschlag zu erarbeiten.

# Fortschritt 

## 26.10.18 

Wir haben uns zunächst über den Irc-Chat mit den Entwicklern des Projekts in  Verbindung gesetzt. Dabei haben wir von einem anderen Entwickler erfahren, dass das Projekt sehr einsteigerfreundlich ist und das man über den Tag Hacktoberfest gute Issues für Neulinge im Projekt finden kann. Diesen Rat haben wir auch befolgt und haben dann unser Issue ausgewählt.
Außerdem haben wir die Entwicklungs- und Testumgebung über diesen [Guide](http://docs.godotengine.org/en/latest/development/compiling/) eingerichtet. 
Wir hatten zunächst die Idee dass die Fehlerquelle in der Funktion void Transform2D::affine_invert() der Klasse transform_2d.cpp liegt, jedoch wurde dies nach weitere Kommunikation mit den Entwicklern verworfen, da uns geraten wurde die tile_map.cpp weiter zu untersuchen.

![screenshot_Issue_1](/Bilder/Issue_1.png)

Dabei ist uns besonders der Aufruf floor() aufgefallen in der Funktion world_to_map(), da bereits  im Issue darauf hingewiesen worden ist. Daher haben wir uns die Beispielprojekte von dem Issue Ersteller heruntergeladen und selbst den Ablauf nachvollzogen. Dabei ist uns aufgefallen, dass die Fehler bei den Beispielprojekten durch einen Rundungsfehler bei dem Aufruf von floor() auftreten. Anschließend haben wir floor() zu round() abgeändert, da bei floor() immer eine Abrundung statt findet. 

![screenshot_Issue_2](/Bilder/Issue_2.PNG)

Danach haben wir diese erste Korrektur in unser Repo eingepflegt und einen Pull-Request erstellt, der vom automatischen Testsystem des Repos auch akzeptiert worden ist. Dieser enthielt eine Beschreibung der Fehlerursache und ein Script zum Testen des Mappings von Weltkoordinaten auf Zellkoordinaten.
![screenshot_pull-request](/Bilder/pullRequest2.png)

## 27.10.18 
Nachdem unser Pull-Request einige Stunden online gewesen ist, wurden wir von einem Entwickler auf Anwendungsfälle aufmerksam gemacht worden, die wir bis dahin noch nicht in Betracht gezogen hatten, bei denen unsere Änderungen neue Fehler hervorgerufen hätten. 

![screenshot_Problem](/Bilder/Problem_1.PNG)

Daher haben wir uns weiter und tiefgehender mit den Funktionsweisen der Klassen und der Funktionen beschäftigt, indem wir den Ablauf logisch nachvollzogen haben. Dabei haben wir Stück für Stück die einzelnen Codeteile der Funktion untersucht und konnten sie als Fehlerquelle ausschließen. Jedoch waren viele Codeteile schwierig zu verstehen, da diese viel mit nicht trivialer Mathematik zutun hatten. Daher haben wir uns weiter durch die Dokumentation von Godot gelesen um eine Möglichkeit zu finden das Programm zu debuggen. Diese haben wir auch gefunden, denn Godot bietet eine [Anleitung](http://docs.godotengine.org/en/3.0/development/cpp/configuring_an_ide.html) dazu wie eine C++ Entwicklungsumgebung aufzusetzen ist, die einen Debugger enthält.
Aufgrund unseres Problems, welches an eine sog. Scene geknüpft war, welches ein eigener Prozess des eigentlichen Programms ist, mussten spezielle Debugger-Einstellungen vorgenommen werden. Diese Information haben wir nicht in der Dokumentation gefunden. Deswegen sind wir wieder auf den IRC zurückgekommen und haben entsprechen hilfreiche  (sogar ziemlich schnell) Antworten erhalten. Durch das Inspizieren der Variablen innerhalb der Funktionsaufrufe die durch den Aufruf unserer untersuchten Funktion aufgetreten sind haben wir die Fehlerquelle ermitteln können. Denn wie wir bereits zu Anfang vermutet hatten lag die Fehlerquelle wirklich in der Funktion affine_invert(), denn dort entstand ein Rundungsfehler, den es zu behoben galt. 
![screenshot_Commit](/Bilder/commit_1.PNG)

Danach haben wir den Pull-Request überarbeitet, jedoch wurde dieser zunächst nicht angenommen vom automatischen Testsystem.

![screenshot_Kommunikation](/Bilder/pullRequest6.PNG)


![screenshot_Kommunikation](/Bilder/pullRequest4.PNG)

## 28.10.18 

Daher haben wir die Klammerung in unserer geänderten Codezeile so angepasst, dass unsere Änderung vom Testsystem angenommen worden wurde. Einen Tag später haben wir dann die Benachrichtigung erhalten, dass unser Bug-Fix in die nächste Version mit aufgenommen wird.

![screenshot_Milestone](/Bilder/milestone_1.PNG)
  
# Erfahrungsberichte
## Simon
### Opensource-Einstieg
Beschäftigt hat sich mein Team mit dem Projekt Godot, einer Game-engine geschrieben in C++.
Durch die nähe zu der Community durch z.B. IRC und der Issue-Diskussion, gelang es uns schnell
einen Einstieg in das Projekt zu erzielen. Durch die gute Dokumentation und Guides ist es möglich
gewesen die Entwicklungsumgebung erfolgreich einzurichten. Dennoch war die Komplexität dieses
Ungetüms einer Software, in einem Ausmaß, der uns vorher noch nicht begegnet ist, überwältigend.
Wir haben versucht das Problem, eine Präzisionsungenauigkeit bei der Transformation von
Weltpixeln zu Kachelkoordinaten, zu analysieren und die Fehlerquelle ausfindig zu machen. Die
Problemstelle wurde lokalisiert und es wurde ein naiver fix implementiert. Der ungewohnte
Umgang mit Git in Bezug auf ein opensource Projekt hat neue Einblicke gewährt, wie z.B. git
commit –amend oder git revert. Im Endeffekt wurde unsere Lösung von der Community aus
nachvollziehbaren Gründen, die mit uns kommuniziert worden sind, abgelehnt. Dennoch sind wir
um Erfahrungen reicher geworden.

### Scrum an einem Vormittag
Die Teamfindung hat sich durch die schnelle Aufteilung erübrigt, was dennoch keine Probleme
dargestellt hat. Alle Teammitglieder waren engagiert und haben schnell produktive Ideen in der
Sprintplanung dargebracht. Aufgrund meiner etwas eingerosteten Kenntnisse in der
Webentwicklung konnte ich sehr von den Erfahrungen einzelner Teammitglieder profitieren und
meinen Perspektive ergänzend dazu darbieten und Lösungsansätze mitgestalten. Die
Entwicklungssprints wurden genutzt um die Ideen umzusetzen. Die anschließende Besprechung mit
dem Kunden war kurz und unsere Ergebnisse mussten schnell auf den Punkt gebracht werden.
Schlussendlich wurde in der Retrospektive Probleme angesprochen und in der nächsten Iteration
erfolgreich ausgebessert. Im großen und ganzen war der gesamte Prozess spannend und es war
immer etwas zu tun, was mir gefallen hat.

### Testing Einstieg
Hauptsächlich hat mich Jest interessiert, weshalb dieses Testing-Framework meinen Hauptfokus bekommen hat. Die Essence ist wohl, das Testing-Frameworks eine Sammlung von Funktionen bieten, die Fehler werfen und sie schön präsentiert. Dieses Video wie z.B https://www.youtube.com/watch?v=VQZx1Z3sW0E haben mir sehr geholfen zu verstehen, was diese Frameworks eigentlich machen. 
Ein Test wurde geschrieben, der testen soll, ob ein DOM objekt eingefügt worden ist.

```javascript
function createNewTaskElement (taskString){
    var listItem=document.createElement("li");

    //input (checkbox)
    var checkBox=document.createElement("input");//checkbx
    //label
    var label=document.createElement("label");//label
    //input (text)
    var editInput=document.createElement("input");//text
    //button.edit
    var editButton=document.createElement("button");//edit button
    //button.delete
    var deleteButton=document.createElement("button");//delete button

    label.innerText=taskString;

    //Each elements, needs appending
    checkBox.type="checkbox";
    editInput.type="text";

    editButton.innerText="Edit";//innerText encodes special characters, HTML does not.
    editButton.className="edit";
    deleteButton.innerText="Delete";
    deleteButton.className="delete";
    
    //and appending.
    listItem.appendChild(checkBox);
    listItem.appendChild(label);
    listItem.appendChild(editInput);
    listItem.appendChild(editButton);
    listItem.appendChild(deleteButton);
    return listItem;
}

addButton.onclick= function(){
    console.log("Add Task...");
    //Create a new list item with the text from the #new-task:
    var listItem=createNewTaskElement(taskInput.value);

    //Append listItem to incompleteTaskHolder
    incompleteTaskHolder.appendChild(listItem);
    bindTaskEvents(listItem, taskCompleted);

    taskInput.value="";
};
```
Beim klicken des Buttons wird die Funktion createNewTaskElement mit dem Namen der Task als Parameter angestoßen wird. Diese Funktion erstellt die nötigen Elemente und klebt sie zusammen. Interpretiert habe ich diesen Vorgang als Integrationstest, weniger als Unit-Test, da mehr als eine Einheit (klicken den Buttons und zusammenführen im DOM) behandelt wird.

Der Test legt an sich sieht nicht gut aus. Mit etwas ausgeklügelterer Herangehensweise würde ein etwas kompakterer Test entstehen. Dennoch erledigt er das was ich mir vorgestellt habe.

```javascript
'use strict';
test("Add Task Test", () =>{
    document.body.innerHTML =
        '<p>' +
        '   <label for="new-task">Add Item</label><input id="new-task" type="text"><button id="addButton">Add</button>' +
        '</p>' +
        '<ul id="incomplete-tasks">' +
        '</ul>' +
        '<h3>Completed</h3>\n' +
        '<ul id="completed-tasks">\n' +
        '</ul>\n';
    const { addButton } = require("../todo.js")
    const $ = require('jquery');
    jest.mock('../todo')
    var taskInput=document.getElementById("new-task");//Add a new task.
    taskInput.value = "Test";
    $('#addButton').click();
    expect(taskInput.value).toBe('');
    expect($('#incomplete-tasks').children().length).toBe(1);
    expect($('#incomplete-tasks').children().children()[1].innerText).toBe("Test");
    taskInput.value = "Test_2";
    $('#addButton').click();
    expect(taskInput.value).toBe('');
    expect($('#incomplete-tasks').children().length).toBe(2);
    expect($('#incomplete-tasks').children().children()[6].innerText).toBe("Test_2");
});
```
Es wird eine abgespeckte version des HTML deklariert, die sich auf diesen Anwendungsfall beschränkt, dies ist evt. unklug gewählt, da eig. mit dem Original gearbeitet werden sollte. 
Anschließend werden die benötigten Funktionen und Objecte von der zu testenden Datei als "required" deklariert. Um Syntaktischen Zucker hinzuzufügen, wurde jquery eingebunden.
Anschließen wird ein String im Inputfeld eingetragen, der beim nächsten Schritt als Taskname dienen soll. Das anklicken eines Button wird mit jquery simuliert. Anschließen werden die Test durchgeführt. In Jest wird dies z.B.  mit expect(Prüfwert).toBe(Erwartungswert) gemacht.

```javascript
test('Delete Task', () => {
  const $ = require('jquery')
  document.documentElement.innerHTML = html
  jest.mock('../todo')
  const { addButton } = require('../todo.js')
  $('#addButton').val('complete_test')
  $('#addButton').click()
  $('#addButton').val('incomplete_test')
  $('#addButton').click()
  $('#delete').each(() => {
    this.click()
  })
  expect($('#incomplete-tasks').children().length).toBe(0)
  expect($('#complete-tasks').children().length).toBe(0)

})
```
Der zweite Test erstellt zuerst "incomplete-task" Listenelemente, anschließend werden alle "#delet"-Buttons betätigt. Erwartungsmäßig sind alle Task entsprechend gelöscht. Wie man auch festellen kann, ist das Styling in diesem Test anders. Hier wurde ESLint und JavaScript Standard Style angewandt. Ausserdem habe ich mir das Einlesen der HTML bei Björn abgeschaut.

## Björn
### Opensource-Einstieg
Wir haben uns im Team für das Godot-Engine Projekt entschieden, da es uns als gutes Einstiegsprojekt empfohlen wurde. Der Umgang mit den anderen Entwicklern des Projekts war während der Gruppenarbeit immer sehr freundlich und professionell. Außerdem war es sehr spannend zu sehen, wie unsere Lösungsvorschläge für den Issue innerhalb der Projektcommunity diskutiert wurden. 
Die eigentliche Programmierarbeit um einen Lösungsvorschlag für den im Issue beschriebenen Bug zu lösen, war sehr zeitaufwendig und hat viele Stunden an Codeanalyse und Debugging benötigt. Zudem war die Codebasis an vielen Stellen unverständlich für mich, da ich bisher nur mit C und nicht C++ gearbeitet hatte, und somit waren manche Softwarekonzepte innerhalb des Codes für mich nur schwer erschließbar. Jedoch war die Dokumentation des Projekts sehr gut und man konnte dadurch viele größere Probleme gut lösen, z.B. das Aufsetzen des Debuggers oder die Installation der eigentlichen Software.
Weiterhin war es zudem sehr interessant, wie sich der Einsatz von git innerhalb der Gruppenarbeit zum zum Unialltag unterschieden hat, denn wir haben z.B. darauf geachtet unsere Commits informativ zu gestalten, damit die anderen Entwickler unsere Änderungen gut nachvollziehen konnten. Denn die meisten bisherigen von uns verfassten Commits in Uni-Projekten waren sehr oft mehr Mittel zum Zweck um den Code zu pushen.
Insgesamt war die Projektarbeit sehr interessant und die investierte Arbeitszeit hat sich gelohnt, da man einen guten Einblick in die reale Softwareentwicklungswelt bekommen hat.

### Scrum an einem Vormittag
Das mini Scrum-Projekt war von der Programmierarbeit nicht besonders aufwändig, da schnell eine Code-Basis geschaffen werden konnte. Weiterhin konnte während der Gruppenarbeit jedes Teammitglied sich gut einbringen und produktiv mitarbeiten.
Die einzelnen Scrum-Phasen waren zwar bereits schon bekannt aus anderen Projektarbeiten, jedoch war die Umsetzung innerhalb der kurzen Zeit eine kleine Herausforderung, da man schnell und zielgerichtet vorgehen musste. Zudem war die Verwendung von Jira komplett neu für mich, und mir war zunächst nicht klar wie die Arbeit mit den Timern gedacht war und wie diese zu verwenden sind. Diese Problematiken haben die erste Scrum Iteration sehr stressig gestaltet und die von uns erzielten Ergebnisse waren noch ausbaufähig. 
Durch die Hilfe von meinen Gruppenmitglieder gelang es mir aber in der ersten Iteration einen guten Einstieg in die Entwicklungsarbeit mit Jira zu bekommen. Zudem konnten wir durch die Scrum-Retroperspektive und das Kundenfeedback schnell unsere Planungsfehler identifizieren. Dadurch war es uns möglich die zweite Scrum-Iteration wesentlich zielgerichteter und effizienter umzusetzen.
Insgesamt war die Gruppenarbeit sehr lehrreich, da man schnell einen neuen Workflow kennengelernt hat und diesen stetig verbessern konnte in einer relativ kurzen Zeit, durch die sehr kurzen Iterationen.

## Testing, Codestyle & Linting

Zunächst habe ich mich mit ESLint und JavaScript Standard Style beschäftigt. Dazu habe ich mir die Tools über die Konsole installiert und habe anschließend mir darüber die Fehler im Quelltext anzeigen lassen. Dabei fiel mir auf, dass ESLint auch die Anmerkungen bzw. Fehler  von JavaScript Standard Style ausgibt. Da ESLint für unser relativ kleines Programm schon über 300 Fehler angezeigt hat, habe ich mich aus Bequemlichkeitsgründen dazu entschieden die Tools in der Webstorm-Ide zu installieren. Dies hat auch sehr gut funktioniert, da ich nun mit zwei Tastendrücke das codestyling und das linting für eine java script Datei durchführen kann.

Außerdem habe ich auch JSHint ausprobiert, jedoch habe ich mich für ESLint entschieden, da es mir auch die Fehler von JavaScript Standard Style  angezeigt hat.

Danach habe ich mich mit QUnit und JEST befasst und habe ich nach einigen Testläufen dazu entschieden JEST zu verwenden, da es für mich zugänglicher gewesen ist und mir die IDE-Integration leichter fiel.

Jedoch stand ich zunächst vor dem Problem, dass ich nicht wusste wie ich mit JEST Funktionen testen sollte die den Dom manipulieren.
Jedoch konnte ich durch großen Zeitaufwand das Problem lösen und folgende Testfälle erstellen:

```javascript
var $ = require('jquery')
var html = require('fs').readFileSync('./todo.html').toString()
const testInput = 'Task_1'

test('Adding Task', () => {
  document.documentElement.innerHTML = html
  const { addButton } = require('../todo.js')

  // check that incomplete-tasks is empty
  expect($('#incomplete-tasks').children().length).toBe(0)

  // submit test.input
  $('#new-task').val(testInput)
  $('#btn').click()

  // check that the test-input is removed from input-field
  expect($('#new-task').val()).not.toMatch(testInput)

  // check if the new-task was added to  incomplete-tasks
  expect($('#incomplete-tasks').children().length).toBe(1)

  // check if the new task has the right content
  expect(document.getElementById('incomplete-tasks').children[0].children[1].innerText).toMatch(testInput)
})

test('Deleting Task', () => {
  document.documentElement.innerHTML = html
  const { addButton } = require('../todo.js')

  // check that incomplete-tasks is empty
  expect($('#incomplete-tasks').children().length).toBe(0)

  // submit test.input
  $('#new-task').val(testInput)
  $('#btn').click()

  // check if the new task was added correctly
  expect($('#new-task').val()).not.toMatch(testInput)
  expect($('#incomplete-tasks').children().length).toBe(1)
  expect(document.getElementById('incomplete-tasks').children[0].children[1].innerText).toMatch(testInput)

  // delete new-task
  $('#incomplete-tasks').children('li:nth-child(1)').children('.delete').click()
  // check if the new task was deleted correctly
  expect($('#incomplete-tasks').children().length).toBe(0)
  expect($('#completed-tasks').children().length).toBe(0)
})

test('Edit Task', () => {
  document.documentElement.innerHTML = html
  const { addButton } = require('../todo.js')

  // check that incomplete-tasks is empty
  expect($('#incomplete-tasks').children().length).toBe(0)

  // submit test.input
  $('#new-task').val(testInput)
  $('#btn').click()

  // check if the new task was added correctly
  expect($('#new-task').val()).not.toMatch(testInput)
  expect($('#incomplete-tasks').children().length).toBe(1)
  expect(document.getElementById('incomplete-tasks').children[0].children[1].innerText).toMatch(testInput)

  // edit new-task
  const editedInput = 'Edited_Task'
  document.getElementById('incomplete-tasks').children[0].children[1].innerText = editedInput
  // check if the new task was edited correctly
  expect($('#incomplete-tasks').children().length).toBe(1)
  expect(document.getElementById('incomplete-tasks').children[0].children[1].innerText).toMatch(editedInput)
})

```

Diese Tests prüfen ob ein Task korrekt erstellt, gelöscht oder bearbeitet worden ist durch eine quasi "Simulation" einer User-Interaktion. Somit handelt es sich hierbei eher um einen Funktionstest als um einen Unit-Test. Um diese Problematik zu umgehen wäre es vlt. sinnvoller gewesen die Funktionalitäten zum Erstellen bzw. Bearbeiten der eigentlichen Daten von den Funktionen zum Darstellen der Daten zu trennen. Dies hätte es ermöglicht sinnvolle Unit-Tests zu schreiben.
Jedoch gelang es mir aus Zeitknappheit nicht dies zusätzlich umzusetzen.
Weiterhin verwenden die Tests JQuery zur besseren Lesbarkeit, jedoch werden an manchen Stellen noch die klassischen Selektoren verwendet, da ich  längere Zeit nicht mehr mit JQuery gearbeitet hatte.

Das Hauptproblem beim Erstellen der Test war die Einbindung des html-codes in die Tests und die richtig Import-Methode für die Funktionalität des addButton. Jedoch konnte ich diese Probleme mit Hilfe meiner Kommilitonen  und folgender Guides lösen:
* http://www.phpied.com/jest-jquery-testing-vanilla-app/
* https://jestjs.io/docs/en/tutorial-jquery

Insgesamt denke ich das die von mir geschriebenen Tests noch ausbaufähig sind, da in allen Testfällen noch doppelter Code auftritt und noch nicht alle Dom-Interaktionen über JQuery stattfinden.

Meine Änderungen am Code sind unter folgendem [Link](https://github.com/priiish/KMS_Team1/tree/Bjoerns_Version) einsehbar.
