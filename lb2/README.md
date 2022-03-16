<h1>LB2 - Rubén Mena</h1>
<h2>Wichtige Links</h2>

<a href="https://github.com/mena03/M300-Services">M300-Services GIT Repository</a><br>
<a href="https://github.com/mena03/vagrant-webserver">vagrant-webserver GIT Repository (für wget)</a><br>

<h2>Meine Aufgabe bei der LB2</h2>
Ich habe mit Hilfe von Vagrant eine VM gemacht die automatisch einen Proxy (Squid) und Webserver (Apache) installiert und konfiguriert. Der Proxy soll jeden Internettraffic erlauben bis auf eine bestimmte Website (z.B. www.google.com). Der Apache-Webserver soll uns Zugriff auf Log-Files geben ohne sich auf die VM verbinden zu müssen (z.B. per SSH, FTP, etc.). <br>
<h3>Infos zu den GIT Repos</h3>
Ich habe für alle restlichen Files, die nicht das Vagrantfile oder das README.md sind, ein seperates GIT Repository gemacht, dass öffentlich ist. Man findet es oben unter den "Wichtigen Links". Ich habe das so gemacht, dass 1. M300-Services sauber bleibt und 2. es nie Probleme hat im Vagrantfile mit meinen wget Zeilen. 

<h2>Dokumentation</h2>
<h3>Vagrantfile</h3>
<h4>Das Vagrantfile ist für die automatisierte Konfiguration der VM zuständig. Hier ist das Vagrantfile noch zu sehen:</h4>
<img src="https://raw.githubusercontent.com/mena03/M300-Services/main/lb2/images/vagrantfile.png?token=GHSAT0AAAAAABSMD6CLU3WLYBKZ5NHW3W3OYRR2PTQ"/>

<h4>Erklärung des Codes</h4>
<p>INFO: Alles hinter einem "#" ist ein Kommentar und wird somit nicht erklärt. 
<table>
  <tr>
    <th>Zeile</td>
    <th>Funktion</td>
  </tr>
  <tr>
    <td>15</td>
    <td>Definiert welche Vagrant Box installiert wird (in diesem Fall eine Ubuntu Maschine)</td>
  </tr>
  <tr>
    <td>16, 17</td>
    <td>Port Forwarding zwischen physischem Gerät (Host) und VM (guest)</td>
  </tr>
    <tr>
    <td>18</td>
    <td>Erlaubt uns unten Commands vom Terminal der Maschine automatisch auszuführen</td>
  </tr>
    <tr>
    <td>20</td>
    <td>So meldet man sich als root an</td>
  </tr>
    <tr>
    <td>21</td>
    <td>Sucht nach Updates von Paketen</td>
  </tr>
    <tr>
    <td>22</td>
    <td>Installiert die von der Zeile 21 gefundenen Updates</td>
  </tr>
    <tr>
    <td>23, 24, 25</td>
    <td>Installiert die Pakete die nach install stehen. Der Parameter "-y" erlaubt uns dies ohne User Interaktion durchzuführen</td>
  </tr>
    <tr>
    <td>27</td>
    <td>Startet den Squid Proxy</td>
  </tr>
    <tr>
    <td>28</td>
    <td>Startet den Squid Proxy neu</td>
  </tr>
    <tr>
    <td>29</td>
    <td>Ladet meine squid.conf von dem angegebenen Pfad herunter und speichert sie im Ordner, in dem man sich gerade befindet</td>
  </tr>
    <tr>
    <td>30</td>
    <td>Benennt das Standard squid.conf file um mit einem .backup hinten, so dass sie nicht benutzt wird vom Squid Proxy</td>
  </tr>
    <tr>
    <td>31</td>
    <td>Verschiebt die heruntergeladene squid.conf in den Ordner /etc/squid3/</td>
  </tr>
    <tr>
    <td>32</td>
    <td>Ladet das file blocklist von dem angegebenen Pfad herunter und speichert sie im Ordner, in dem man sich gerade befindet</td>
  </tr>
    <tr>
    <td>33</td>
    <td>Verschiebt die eben heruntergeladene Datei blocklist in den Ordner /etc/squid3/</td>
  </tr>
    <tr>
    <td>34</td>
    <td>Startet squid neu</td>
  </tr>
    <tr>
    <td>36</td>
    <td>Startet den apache2 Dienst</td>
  </tr>
    <tr>
    <td>37, 52</td>
    <td>Startet den apache2 Dienst neu</td>
  </tr>
    <tr>
    <td>38</td>
    <td>Ladet meine index.html vom angegebenen Pfad herunter und speichert sie im Ordner, in dem man sich gerade befindet</td>
      <tr>
    <td>39</td>
    <td>Benennt Standard index.html File in /var/www/html in index.html.backup um </td>
  </tr>
  </tr>
    <tr>
    <td>40</td>
    <td>Verschiebt das in Zeile 38 heruntergeladene index.html in /var/www/html</td>
  </tr>
  <tr>
    <td>42, 43, 44, 47, 49, 51</td>
    <td>Gibt jedem die ganzen Berechtigungen zu den angegebenen Pfaden/Dateien</td>
  </tr>
  <tr>
    <td>45, 46</td>
    <td>Erstellt einen Folder Namens "logs"/"squid3" in /var/www/html</td>
  </tr>
    <tr>
    <td>48, 50</td>
    <td>Erstellt eine Verknüpfung der Datei im ersten angegebenen Pfad im Ordner /var/www/html/logs/squid3/</td>
  </tr>
</table>
<h2>Test</h2>
<h3>Squid Proxy</h3>
<h4>blockierte und erlaubte Websites</h4>
<img src="https://raw.githubusercontent.com/mena03/M300-Services/main/lb2/images/squidconf.png?token=GHSAT0AAAAAABSMD6CK77XCO6STVMJOLPAWYRR2U2Q">
<p>Wie man im Bild erkennen kann, ist diese Regel nur für den localhost definiert, mann könnte dies jedoch erweitern auf ein Netzwerk natürlich. Im blocklist-File habe ich die Seite .google.com drin. Um nun zu testen, ob das die Konfiguration funktioniert empfehlen ich <a href="https://www.mozilla.org/de/firefox/new/">Firefox</a> als Browser, weil man dort nur für den Browser selbst den Proxy einstellt. Dies kann aber auch in den Betriebssystemeinstellungen gemacht werden. </p>
<h5>Firefox</h5>
1. Firefox öffnen <br>
2. "about:preferences" in die Suchleiste eingeben <br>
3. Einstellungen suchen -> Proxy eingeben -> Einstellungen <br>
4. Am Schluss sollte das so aussehen:
<img src="https://raw.githubusercontent.com/mena03/M300-Services/main/lb2/images/Proxysettings_Firefox.png?token=GHSAT0AAAAAABSMD6CKUCUYGXQEVAVRRJ7OYRR27CQ"><br>
5. "OK" klicken <br>
6. "www.google.com" in der Suchleiste suchen <br>
7. Folgendes sollte kommen:
<img src=""><br>
8. Nun können Sie jede beliebige Website probieren und wenn es richtig konfiguriert ist, sollte alles funktionieren. <br>
<h3>Apache Webserver</h3>
1. 127.0.0.1:8080 in die Suchleiste:
<img src=""> <br>
2. Wenn man nun vorher versucht hat auf www.google.com zu kommen, soll man wenn man nun auf "access.log" klickt sollte man die TCP_DENIED_REPLY für www.google.com:443 sehen:
<img src=""> <br>