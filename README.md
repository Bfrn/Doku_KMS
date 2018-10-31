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

Wir haben uns auf den Issue geeinigt, den wir bearbeiten möchten. Außerdem haben wir die Entwicklungs- und Testumgebung über diesen [Guide](http://docs.godotengine.org/en/latest/development/compiling/) eingerichtet. 
Wir hatten zunächst die Idee dass die Fehlerquelle in der Funktion void Transform2D::affine_invert() der Klasse transform_2d.cpp liegt, jedoch wurde dies nach weitere Kommunikation mit den Entwicklern verworfen, da uns geraten wurde die tile_map.cpp weiter zu untersuchen.
![screenshot_Kommunikation](/Bilder/Issue_1%20-%20Kopie.png)
Dabei ist uns besonders der Aufruf floor() aufgefallen in der Funktion world_to_map(), da bereits  im Issue darauf hingewiesen worden ist. Daher haben wir uns die Beispielprojekte von dem Issue Ersteller heruntergeladen und selbst den Ablauf nachvollzogen. Dabei ist uns aufgefallen, dass die Fehler bei den Beispielprojekten durch einen Rundungsfehler bei dem Aufruf von floor() auftreten. Anschließend haben wir floor() zu round() abgeändert, da bei floor() immer eine Abrundung statt findet. Danach haben wir diese erste Korrektur in unser Repo eingepflegt und einen Pull-Request erstellt, der vom automatischen Testsystem des Repos auch akzeptiert worden ist. Dieser enthielt eine Beschreibung der Fehlerursache und ein Script zum Testen des Mappings von Weltkoordinaten auf Zellkoordinaten.
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

![screenshot_Milestone](/Bilder/mileston_1.PNG)
