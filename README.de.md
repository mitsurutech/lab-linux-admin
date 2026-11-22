# Lab zur Linux-Administration: Benutzer, Berechtigungen, cron und systemd in einer Ubuntu-VM

[English](README.md) | [Français](README.fr.md) | [Español](README.es.md) | [日本語](README.ja.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-linux-admin/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-linux-admin/actions/workflows/check.yml)

Ein fertiges **Lab zur Linux-Administration** für den Unterricht, von der [MitsuruTech Learning Platform](https://mitsurutech.co.uk/de/learning), auf der Schulen und Colleges allen Lernenden eine eigene Ubuntu-Maschine und ein Terminal im Browser geben. Die Lernenden sind Administratorin oder Administrator eines kleinen Servers und erledigen vier Aufgaben, die ein Sysadmin im ersten Jahr erledigt: **einen Benutzer und eine Gruppe anlegen**, **die Berechtigungen eines gemeinsamen Ordners setzen**, **einen Job mit cron planen** und **einen systemd-Dienst schreiben und aktivieren**. Auf dem Rechner der Lernenden wird nichts installiert, und das Lab wird von einem Skript bewertet, sodass eine Lehrkraft nicht jede Maschine einzeln durchsehen muss.

## Die vier Aufgaben

| Aufgabe | Was die Lernenden tun |
| --- | --- |
| 1. Ein Benutzer und eine Gruppe | Eine gewöhnliche Gruppe `devs` und einen gewöhnlichen Benutzer `maria` mit Home-Ordner und bash anlegen, in dieser Gruppe und ohne Administratorrechte |
| 2. Ein gemeinsamer Ordner | `/srv/projects` zum Ordner des Teams machen: Gruppenmitglieder können ihn lesen, beschreiben und betreten, darin angelegte Dateien gehören automatisch der Gruppe, und sonst hat niemand Zugriff |
| 3. Ein geplanter Job | Das `backup.sh` des Labs jeden Tag um 02:30 als root ausführen, aus einer Datei in `/etc/cron.d`, so geschrieben, wie cron es verlangt |
| 4. Ein Dienst | Eine systemd-Unit-Datei für das `heartbeat.sh` des Labs schreiben, die bei einem Fehler neu startet, und sie aktivieren, sodass sie bei jedem Systemstart startet |

Das Lab legt eine README im Home-Ordner der Lernenden ab, die sagt, was zu tun ist, und keine der Antworten verrät: keine Befehle, keine Modi, keine Unit-Datei. Es ist absichtlich klein, sodass es in eine erste Stunde zu **Benutzern und Berechtigungen, cron und systemd** passt und sich durch Kopieren der Datei um Festplatten, LVM oder eine Firewall erweitern lässt.

## Wie bewertet wird

Das Skript `check` läuft als root auf einer Kopie der gespeicherten Maschine nach einem frischen Start. Es sieht also nur, was **gespeichert** wurde, nie einen Befehl, der getippt wurde und beim Neustart verloren ging. Es geht die vier Aufgaben der Reihe nach durch und hält bei der ersten falschen an, und seine erste Zeile sagt, welche Aufgabe es ist und warum, zum Beispiel `NOT PASSED: task 3: the user field is /usr/local/bin/backup.sh, it must be root`. Es liest den echten Zustand: den Benutzer und die Gruppe in der Kontendatenbank, Eigentümer und Modus des Ordners und eine echte Datei, die vom neuen Benutzer und von jemandem außerhalb der Gruppe angelegt wird; Eigentümer, Berechtigungen und Zeilenende der cron-Datei und ihre eine Jobzeile; `ExecStart`, `Restart` und `WantedBy` der Unit-Datei und den Link, den `systemctl enable` anlegt. Eine Benutzer-Crontab, ein mit `chmod 777` weit geöffneter Ordner, ein Dienst, der gestartet, aber nie aktiviert wurde, und eine Kopie der Unit-Datei anstelle des Links fallen alle durch.

## Was in der Datei steht

`linux-admin.lab.yaml` ist eine vollständige **Definition eines VM-Labs**, genau das, was die Plattform ausführt:

| Schlüssel | Was er ist |
| --- | --- |
| `cpus`, `memory`, `disk` | die Größe der VM jeder lernenden Person: 1 CPU, 1024 MB und 10 GB |
| `open` | die Ports, die im Browser geöffnet werden: keine, das Lab ist das Terminal |
| `setup` | ein Skript, das einmal als root beim ersten Start der VM jeder lernenden Person läuft: Es installiert `cron` aus dem Ubuntu-Archiv, legt die beiden kleinen Skripte und den gemeinsamen Ordner an und schreibt die README des Labs |
| `check` | das oben beschriebene Bewertungsskript; sein Exit-Status ist das Ergebnis, und `0` bedeutet bestanden |

## Auf der Plattform verwenden

**Um es einer Klasse als Aufgabe zu stellen:**

1. Fügen Sie in Ihrem Modul eine Aufgabe hinzu und wählen Sie die Karte **Ubuntu-VM-Lab**.
2. Klicken Sie auf das Beispiel, oder fügen Sie den Inhalt von `linux-admin.lab.yaml` ein. Ein Beispiel ersetzt den Text, und „Rückgängig“ bringt Ihren zurück.
3. Veröffentlichen Sie und probieren Sie zuerst Ihre eigene Kopie aus. Alle Lernenden erhalten eine eigene VM, die zwischen den Sitzungen erhalten bleibt.
4. Weisen Sie sie zu wie jede Aufgabe. Wenn sie abgegeben ist, führen Sie die Prüfung in der Zeile einer lernenden Person in der Klassenliste aus: Sie startet eine Kopie der gespeicherten Maschine, die nicht zurückgespeichert wird, und zeigt, ob sie bestanden hat und was sie ausgegeben hat.

Die [Seite zu diesem Lab](https://mitsurutech.co.uk/de/help/lab-linux-admin) im Hilfehandbuch, sein [Leitfaden zu Ubuntu-VM-Labs](https://mitsurutech.co.uk/de/help/labs) und seine [Seite mit Beispiel-Labs](https://mitsurutech.co.uk/de/help/sample-labs) erklären den Rest. Noch kein Konto für Ihr College? [Ihr erstes Jahr ist kostenlos](https://mitsurutech.co.uk/de/pricing) für bis zu 50 Lernende, und für die [Registrierung](https://mitsurutech.co.uk/de/signup) genügt eine E-Mail-Adresse.

## Die Datei prüfen

Bei jedem Push prüft `.github/workflows/check.yml`, dass die Datei gültiges YAML mit den Schlüsseln oben ist und dass `setup` und `check` jeweils innerhalb der Grenze der Plattform von 16 KB liegen. Es startet keine VM: Das geschieht auf den eigenen Maschinen der Plattform.

## Was in einem Lab nicht funktioniert

Die VM erreicht das Paketarchiv von Ubuntu, PyPI, npm und GitHub und sonst nichts, ein Lab kann also nicht auf einen Host im Internet gerichtet werden. Es ist eine einzige VM, es gibt also keine zweite Maschine, die über ein Netzwerk zu verwalten wäre.

## Über uns

Erstellt von [Mitsuru Technologies](https://mitsurutech.co.uk/de) für die [MitsuruTech Learning Platform](https://mitsurutech.co.uk/de/learning): Python, Java, Go, Node, SQL und Linux im Browser für Schulen und Colleges, mit Container-Labs, VM-Labs, aus GitHub bereitgestellten Web-Apps und Quizzen, die sich selbst bewerten. Probieren Sie Code ohne Konto im [Playground](https://mitsurutech.co.uk/de/playground) aus.

Weitere Labs vom selben Ort: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) und [lab-sample](https://github.com/mitsurutech/lab-sample).

## Lizenz

Dieses Werk steht unter der Lizenz [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Sie dürfen es zum Lernen und Lehren weitergeben und bearbeiten, wenn Sie Mitsuru Technologies Ltd nennen, es nicht kommerziell nutzen und alles, was Sie daraus machen, unter derselben Lizenz weitergeben. Der vollständige Text steht in der Datei `LICENSE`.

`cron` wird beim Start einer VM aus dem Ubuntu-Archiv installiert; nichts davon wird hier verteilt.
