# Glossar

* [Git Book]: <http://book.git-scm.com/>

## working copy
- das Arbeitsverzeichnis mit den ausgecheckten Dateien

## repository
- das *.git* Verzeichnis

## stash
- siehe `git help stash`
- Zwischenablage für Änderungen, die nicht commited werden sollen
- sinnvoll bei Wechsel der Branch, oder beim Mergen von Branches

## index
- auch *staging area* genannt
- enthält Änderungen die im nächsten Commit enthalten sein sollen
- Stand der Datei im *index* ist exakt der Stand zum Zeitpunkt des Hinzufügens (Snapshot)

## tracking

Von tracked spricht man:
- bei Dateien wenn sie im repository erfasst sind
- bei einer lokalen branch, wenn sie eine remote branch referenziert

## referenzen
- Branches und Tags sind Referenzen
- Referenzen werden im Ordner `.git/refs` angelegt
- Referenzen sind Pointer auf Commits (Tags können auch auf Trees oder Blobs zeigen)
- im Ordner `.git/refs/heads` befinden sich die Referenzen auf die lokale branches
- im Ordner `.git/refs/remotes/<remote name>` befinden sich die Referenzen auf die remote branches

## blob
- enthält den Inhalt einer Datei zu dem Zeitpunkt zu dem sie commited wurde
- entspricht der version einer Datei

## treeish

Git unterstützt eine Vielzahl von Möglichkeiten einen Commit oder Tree zu referenzieren. 
Diese Möglichkeiten subsummiert man unter der Bezeichnung *treeish*:
-  [Git Book - Treeish]: <http://book.git-scm.com/4_git_treeishes.html>

## refspec
- Hilfe: `git help fetch`
- Beim Arbeiten mit Remotes über `fetch` und `pull` muss ein sogenannte 
sogenannte **refspec** angegeben werden.

Diese haben das folgende Format:

	<source>:<destination>

z.B. refspec für eine branch:
	
	<remote branch reference>:<local branch reference>
	refs/heads/master:refs/remotes/origin/master

Kurzschreibweise:

	master:origin/master
	

Über die folgende Konfiguration wird bei einem fetch `git pull origin`
auf der lokalen branch *master_dev* die branch *master_dev* vom
remote *origin* verwendet.

	[remote "origin"]
		url = /mnt/purgit/purpdm.git/
		fetch = +refs/heads/master_dev:refs/remotes/purgit/master_dev

## HEAD
- Pointer auf den letzten commit der aktuell ausgecheckten Branch
- liegt unter: `.git/HEAD`

## fast-forward

Ein merge oder push ist fast-forward, wenn die Zielbranch ein 
Vorgänger der Quellbranch ist. Sprich der Quellbranch enthält 
alle Commits der Zielbranch.


# Befehle zur Arbeit mit dem Repository

## Erstellung/Konfiguration

- Initialisierung eines repositories: `git init`
- Konfiguration: `git config [--global] <key> <value>`
- Konfiguration ausgeben: `git config --get <key>`

## Statistiken/Diagnose (Log, History, Diff, Blame ...)

Befehle: `branch, log, diff, difftool, show, ls-tree`

- lokale und remote branches mit Status auflisten: `git branch -avv`
- details einer remote branch: `git remote -v show <remote>`

- Snapshot einer Datei anzeigen bezogen auf eine Referenz (Tag, Branch) oder einen Commit: 

	`git show <ref|commit>:<path>`
	
- z.B.: 
	
	`git show master:WebContent/WEB-INF/com/foo/bar/Applet.java`

`git log` unterstützt eine Vielzahl von Formattierungen und Angaben der 
Revisionen. Hilfe siehe `git help log` und `git help rev-parse`

- geänderte Dateien anzeigen: `git log --stat`
- branches in graph visualisieren: `git log --graph`
- Änderungen im Log als diff anzeigen: `git log -p`
- Log einer Datei: `git log <treeish> <path>`
- tree eines commits: `git ls-tree <commit>`

### log inklusive file renames

Damit die history einer Datei vor der Umbenennung mit angezeigt wird,
muss `git log` das flag `--follow` übergeben werden.

	--follow
		Continue listing the history of a file beyond renames.

z. B.
	git log --follow WebContent/WEB-INF/com/foo/bar/Applet.java

### diff
- analog zu `git diff` kann auch `git difftool` verwendet werden
- diff zum *index*: `git diff`
- diff zu anderem commit: `git diff <commit>`
- zwei Snapshots einer Datei vergleichen: `git diff <ref|commit> <ref|commit> -- <file>`


### merge commit diff/details

	1 commit d359156d4c13b65941a4468d7396b5136951f442
	2 Merge: 5a0899d 57ba4fd
	3 Author: Foo Bar <foo.bar@company.de>
	4 Date:   Wed Nov 16 16:47:37 2011 +0100
	5 
	6     Merge remote branch 'origin/somebranch' into myfeature_branch

Ein Merge-Commit hat immer zwei Parents. Der erste Parent ist die lokale Branch auf der gearbeitet wird (hier *myfeature_branch*).
Der zweite Parent ist die Branch welche in die lokale Branch gemerged wird (hier *origin/somebranch*).

Um sich die Commits anzuzeigen die durch den Merge hinzugekommen sind kann folgender Befehl verwendet werden:

	git log --stat -p 57ba4fd ^5a0899d

Die schreibweise hier bedeutet: Zeige alle Commits an die in *origin/somebranch* vorhanden sind, aber nicht in *myfeature_branch*.
Siehe auch *SPECIFYING REVISIONS in `git help rev-parse`*.


### blame
Zeige Revision und Autor der letzten Änderung für jede Zeile einer Datei:

	git blame WebContent/jsps/rbc/tpms/tpmsBOMs.jsp

Schränke die Anzeige von Revision und Autor auf einen bestimmten Zeilenbereich ein:

	git blame -L 1110,1160 WebContent/jsps/rbc/tpms/tpmsBOMs.jsp


## index

Änderungen die commited werden sollen, müssen in den *index* übernommen werden:

Befehle: `add, reset, rm, update-index`

- status des index anzeigen: `git status`
- aktuelle Änderungen samt entfernter Dateien in *index* übernehmen: `git add -A`
- aktuelle Änderungen ohne entfernte Dateien in *index* übernehmen: `git add -a`
- einzelne Dateien hinzufügen: `git add [files]`
- Dateien aus *index* entfernen: `git reset [files]`
- bereits getrackte Dateien untracken, lokale Version behalten: `git rm --cached <file>`

## commit 

Befehl: `commit`

Änderungen im *index* commiten: `git commit -m <commit message>`

Erklärung `commit`:

- ein commit mit den Änderungen im *index* wird erstellt
- der *index* wird geleert
- die Referenz des HEAD wird auf den neuen commit gesetzt

## branches

Befehle: `branch, merge`

- vom HEAD abbranchen und in die neue branch wechseln: `git checkout -b <new branch> [<ref|commit>]`
- eine branch von einem commit oder einer referenz anlegen: `git branch <new branch> [<ref|commit>]`
- eine branch umbenennen: `git branch -m <branch name> <new branch name>`
- eine branch löschen: `git branch -d <branch name>`

!!Verwirft nicht commitete Änderungen!!

- mittels `git reset --hard <commit>` kann der Pointer des aktuellen HEAD verschoben werden


### merging

- die Änderungen einer Branch bis zu dem angegebenen commit 
in den aktuellen HEAD integrieren: `git merge -v <reference|commit>`
- !! alle Änderungen müssen commited oder gestashed werden !!

- mergen ohne zu commiten: `git merge -v --no-commit <reference|commit>`

Erklärung `merge`:

1. sucht den gemeinsamen parent commit, genannt *merge-base*
2. vergleicht branches auf Basis der *merge-base* und fürt diese zusammen
3. nicht konfliktende Änderungen werden automatisch gemerged
4. ein merge commit wird erstellt.

### Konflikte aufösen
Beim mergen können Konflikte entstehen. Die Konflikte können
mit einem Tool der Wahl aufgelöst werden.

- Tool zur Auflösung von Konflikten aufrufen: `git mergetool`

Konfiguration des mergetools in `.gitconfig`:

	[merge]
		tool = kdiff3

mergetool Konfiguration für tortoise:

	C:\Programme\KDiff3\kdiff3.exe %base %theirs %mine -o %merged

#### auto-merge deaktiviern

Beim mergen werden beidseite Änderungen, die nicht zu einem Konflikt führen automatisch gemerged.
Dieses automatische mergen kann jedoch zu syntaktischen oder fachlichen Fehlern führen.
Für bestimmte Aktionen kann in Git ein eigener Befehl/Driver definiert werden. So auch für das mergen
von Dateien. Der Mechanismus wird unter *git help gitattributes* genau beschrieben.

in *.git/config*:

	[<operation> "<driver name>"]
		name = <a descriptive name>
		driver = <command>

in *.gitattributes*:

	<pattern> <operation>=<driver name>

Möchte man z.B. für `.java` Dateien immer ein manuelles Mergetool ausführen, dann sieht dies wie folgt aus:

in `.git/config`:

	[merge "kdiff3"]
		name = use kdiff3 for manual merge
		driver = kdiff3 %A %B -o %A

in `.gitattributes`:

	*.java merge=kdiff3

## (untracked) Dateien ignorieren

- Hilfe: `git help gitignore`

- global (wird commited und gilt für alle): *.gitignore*
- lokal (wird nicht commit und gilt nur lokal): *$GIT_DIR/info/exclude*

### (tracked) Dateien ignorieren

Änderungen an den beiden Dateien `foo.txt` und `bar.txt` lassen sich wie folgt ignorieren:

`git update-index --assume-unchanged -- foo.txt bar.txt`

Zuruecksetzen mittels:

`git update-index --no-assume-unchanged -- foo.txt bar.txt`

oder

`git update-index --really-refresh`

## Arbeiten mit Remotes

### Remote Repository
Befehle: `clone, fetch, merge, pull, remote`

#### Konfiguration

Konfiguration für remote repository:

	[remote "origin"]
		url = /mnt/purgit/purpdm.git/
		fetch = +refs/heads/*:refs/remotes/purgit/*

- remote hinzufügen: `git remote add <repository> <url>`
- remote URL wird in .git/config configuriert
- bei `git clone` wird das Quellrepository automatisch als remote mit dem Namen 'origin' konfiguriert.

##### Remote Branch lokal anlegen/tracken

Mann muss zu einer Remote Branch immer eine lokale Branch/Referenz anlegen.
Eine Remote Branch nicht direkt ausgecheckt werden.
Versucht man eine Remote Branch direkt auszuchecken z.B.:

	git checkout origin/master_dev
 
dann wird man bei `git status` ein 

	(no branch)

angezeigt, sprich man befindet sich auf keiner Branch.
 
Mit folgendem Befehl wird eine lokale Branch/Referenz zu einer Remote Branch erzeug und diese ausgecheckt:
	
	`git checkout -b <new branch> <remote>/<branch>`
 

#### fetch und pull

- `git fetch` ohne *refspec* aktualisiert alle referenzen in *.git/refs/remotes/<repository>*,
legt neue remote referenzen an und holt alle neuen referenzierten Objekte. 
Beim fetchen eine Branch werden automatisch alle Tags die mit commits dieser branch assoziiert sind gefetched.
- `git pull` macht ein `git fetch` mit der angegebenen refspec und ein anschließendes `git merge`
- `git pull` merged immer mit dem HEAD (aktuelle branch)
- neue branch auf basis einer remote branch erstellen: `git fetch origin <remote branch>:<new branch>`
- gleich zur remote branch wechseln: `git checkout -b <new branch> <remote branch>`
- aktualisieren einer lokalen branch von einer remote branch: `git pull <repository> <refspec>`
- neue Objekte (Blobs,Commits,Trees) zu der angegebenen <refspec> holen: `fetch [<repository> [<refspec>...]]`: 

#### push

- *fast-forward* only: Das Remote muss ein Vorläufer der gepushten Reference sein
- Änderungen ins remote einspielen: `git push <repository> <refspec>`

!!!Löscht/Überschreibt remote branch!!!

- remote branch überschreiben: `git push <repository> +<ref>`
- eine remote Referenz (Branch, Tag) löschen: `git push <repository> :<ref>`


## Arbeiten mit Tags

- ein Tag kann jedes Objekt (Commit,Tree oder Blob) im repository referenzieren
- für gewöhnlich werden jedoch nur Commits getagged

Der Tag kann wie jede andere Referenz für die Befehle `diff, log, tag` 
verwendet werden.

Die folgenden Beispiele verwenden `foobar` als Tag Namen:

### Lokalen Tag überschreiben

Der Tag existiert lokal schon, soll aber auf ein anderes Objekt (Commit, Tree oder Blob) zeigen:

	git tag -f foobar [sha1/refspec]
	
### Remote tag überschreiben
Tag hat sich lokal geändert und soll auch im Remote Repository geändert werden:

	git push purgit +refs/tags/embedded_jetty_patch

### Lokale tags von remote tags aktualisieren

Tags haben sich im Remote Repository geändert und sollen lokal aktualisiert werden:

	git fetch --tags

Die Ausgabe sieht dann ungefähr so aus:

	ruben@laprjenster:~/projekte/someproject git fetch --tags
	From http://someserver/somerepo.git
	 - [tag update]      foobar -> foobar


### Liste von lokalen Tags mit SHA1 anzeigen

Dies ist leider nicht mittels `git tag` möglich, aber lässt sich mit `git show-ref` machen:
	
	git show-ref --tags
	
### Liste von Remote Tags mit SHA1 anzeigen

	git ls-remote --tags {remote}


# Aliases

https://git.wiki.kernel.org/index.php/Aliases

Mittels `git config` können Aliases können in 
`.git/config` oder `~/.gitconfig` eingefügt werden.

  git config [--global] alias.{name} {command}

Einem Alias der mehrere git Kommandos ausführen soll, muss ein Ausrufezeichen vorangestellt werden:

  [alias]
    jetty = !git cherry-pick --no-commit embedded_jetty_patch && git reset


## Authentication

- http://maymay.net/blog/2008/08/08/how-to-use-http-basic-authentication-with-git/


## Tipps und Tricks

###  Zeilennummern für Pager (less) in Diff Ansicht

	[core]
		pager = less -+$LESS -FRXN


### Alle Files (mit neuen Files) `stashen`

	git add -A 
	git stash save "your message"

### Alle lokalen Änderungen verwerfen (inklusive neuer Dateien)

  # alle Dateien zum index hinzufügen
	git add -A
	git reset --hard HEAD

### Nach merge Konflikte aufräumen

	rm $(find . -name \*.orig)

### Alle Branches anzeigen die bereits (hier in den master) gemerged sind
  
	git branch --merged master

### Alle Commits die in Branch `topic` aber nicht in Branch `master` sind
  
	git log topic ^master

### Alle Branches die Branch `feature` komplett enthalten
  
	git branch -a --contains feature

### Alle Branches die noch nicht gemerged sind

	git branch --no-merged

### Merge ohne commit

	git merge --dry-run
  
### Command History anzeigen (merge, checkout, pull, push)

	git revlog
  
## Fehlerbehebung und Wiederherstellung

Anleitungen wie bei korrupten Objekten vorzugehen ist,
wird in folgendem Abschnitt des git books beschrieben:

[Git Book - Recovering corrupted objects] : <http://book.git-scm.com/5_recovering_corrupted_objects.html>

### Korrupte Objekte 

#### Loose Objects

Loose Objects würden beim nächsten `git gc` automatisch entfernt werden.
Diese kann man ohne Probleme löschen.
Allerdings sollte man nochmal prüfen, welcher Entwickler dieses Objekt in seinem lokalen Repository hat - 
um hier eventuell die Ursache für das korrupte Objekt zu finden.

	remote: fatal: loose object 5b855cc89d48fa3e679ed54bb681520b4f7c426b (stored in
	./objects/5b/855cc89d48fa3e679ed54bb681520b4f7c426b) is corrupt
	error: git upload-pack: git-pack-objects died with error.
	fatal: git upload-pack: aborting due to possible repository corruption on the re
	mote side.
	remote: aborting due to possible repository corruption on the remote side.
	fatal: protocol error: bad pack header
  
### Commit finden der einen bestimmten Blob enthält

Log Ausgabe in dem der entsprechende SHA1 gesucht werden kann:

	git log --no-abbrev --raw master_dev
  
### Commit finden der einen bestimmten Tree enthält

Log Ausgabe in dem der entsprechende SHA1 gesucht werden kann:
  
	git log -t --no-abbrev --raw master_dev

### Wiederherstellen eines gelöschten Branches

1. Finde den letzten Sha des gelöschten Branches auf einem der folgenden Wege:
   * Zurückscrollen in der geöffneten Git Konsole (`git branch -d` gibt den Sha aus).
   * Wenn bereits ein Merge stattgefunden hat findet sich ein Sha in der Ausgabe von: `git reflog`
   * Wenn noch kein Merge stattgefunden hat suche den Sha über: `git fsck`
2. Anschließend kann der Branch wiederhergestellt werden:

   git branch <deleted branch name> <found-sha1-id>
