# Beschreibung

Das `git-cheat-sheet` ist eine Sammlung von Tipps und Tricks für die Arbeit mit 
dem Versionsverwaltungssystem [GIT](http://git-scm.com/).

# Markdown umwandeln

Folgend ein paar Hinweise um aus dem in [Markdown](http://de.wikipedia.org/wiki/Markdown) geschriebenen Cheat-Sheet 
ein HTML/PDF Dokument zu erstellen.

## in HTML

Für die Umwandlung in HTML verwende ich aktuell [bluecloth](http://deveiate.org/projects/BlueCloth).

## in PDF

Um ein PDF Dokument zu erzeugen verwende ich [wkhtmltopdf](http://code.google.com/p/wkhtmltopdf/).
Dieses verwendet die webkit Bibliothek um aus dem zuvor mittels bluecloth generiertem
HTML ein PDF zu erzeugen.

Das Tolle ist, das man mit `wkhtmltopdf` auch Stylesheets verwenden kann um das Dokument zu formattieren:

<code>
	--user-style-sheet <url>
			Specify a user style sheet, to load with every page
</code>

Folgendes CSS beispielsweise führt eine automatische Überschriftennummerierung für *H2 und H3* sowie 
einen Seitenumbruch vor einer überschrift ersten Grades *H1* ein.

<code>
	h1 {
		counter-reset: headings;
	}

	H2:before {
		      counter-increment: headings;  /* Add 1 to chapter */
		      content: counter(headings) ". ";
		      counter-reset: subheadings;
	}

	h2 {
		counter-reset: subheadings;
		page-break-before: always;
	}

	H3:before {
		      counter-increment: subheadings;  /* Add 1 to chapter */
		      content: counter(headings) "." counter(subheadings) ". ";
	}
</code>

