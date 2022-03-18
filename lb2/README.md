<h1>LB2 - Rubén Mena</h1>
<h2>Inhaltsverzeichnis</h2>
1. Wichtige Links <br>
2. Einführung <br>
  2.1 Infos zu de GIT Repos <br>
  2.2 Grafische Übersicht der Services <br>
4. Dokumentation <br>
5. Vagrantfile <br>
6. Erklärung des Codes <br>
7. Sicherheit <br>
8. Testen <br>
9. Quellenverzeichnis <br>

<h2>Wichtige Links</h2>

<a href="https://github.com/mena03/M300-Services">M300-Services GIT Repository</a><br>
<a href="https://github.com/mena03/vagrant-webserver">vagrant-webserver GIT Repository (für wget)</a><br>
<a href="http://127.0.0.1:8080">Webserver Main Page</a><br>
<a href="http://127.0.0.1:8080/logs/squid3/access"></a>access.log im Webserver<br>
<a href="http://127.0.0.1:8080/logs/squid3/cache"></a>cache.log im Webserver<br>

<h2>Einführung</h2>
Ich habe mit Hilfe von Vagrant eine VM gemacht die automatisch einen Proxy (Squid) und Webserver (Apache) installiert und konfiguriert. Der Proxy soll jeden Internettraffic erlauben bis auf eine bestimmte Website (z.B. www.google.com). Der Apache-Webserver soll uns Zugriff auf Log-Files geben ohne sich auf die VM verbinden zu müssen (z.B. per SSH, FTP, etc.). <br>
<h3>Infos zu den GIT Repos</h3>
Ich habe für alle restlichen Files, die nicht das Vagrantfile oder das README.md sind, ein seperates GIT Repository gemacht, dass öffentlich ist. Man findet es oben unter den "Wichtigen Links". Ich habe das so gemacht, dass 1. M300-Services sauber bleibt und 2. es nie Probleme hat im Vagrantfile mit meinen wget Zeilen. 

<h3>Grafische Übersicht der Services</h3>
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/service_graphic.png?raw=true"/>
<h2>Dokumentation</h2>
<h3>Vagrantfile</h3>
<p>Das Vagrantfile ist für die automatisierte Konfiguration der VM zuständig. Hier ist das Vagrantfile noch zu sehen:</p>
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/vagrantfile.png"/>

<h4>Erklärung des Codes</h4> 
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
<h2>Sicherheit</h2>
<p>Ich habe mit dem blocklist-File und der squid.conf Sicherheit für den Proxy User gewährleistet. Natürlich ist Google keine gefährliche Website, aber man könnte nun unzählige Websites in dieses blocklist-File zu schreiben, um unangebrachte, gefährliche oder sonstige Seiten zu blockieren.<br><br>
Da man Proxys auch mit GPOs von einem Active Directory oder einer Firewall erzwingen kann für bestimmte User und Gruppen ist das sehr effektiv, nicht nur um das Netzwerk zu schützen, sondern auch um den Traffic zu analysieren.</p>
<h2>Testen</h2>
<h3>Squid Proxy testen</h3>
<h4>blockierte und erlaubte Websites</h4>
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/squidconf.png?raw=true">
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/blocklist.png?raw=true">
<p>Wie man in den Bildern erkennen kann, ist diese Regel nur für den localhost definiert. Im blocklist-File habe ich die Seite .google.com drin. Mit "http_access allow !block" sage ich, dass alles durchgelassen werden soll bis auf die definierten Seiten im blockfile. Um nun zu testen, ob das die Konfiguration funktioniert empfehle und verwende ich <a href="https://www.mozilla.org/de/firefox/new/">Firefox</a> als Browser, weil man dort nur für den Browser selbst den Proxy einstellen kann und nicht für den ganzen Host.</p>

1. Firefox öffnen <br>
2. "about:preferences" in die Suchleiste eingeben <br>
3. Einstellungen suchen -> Proxy eingeben -> Einstellungen <br>
4. Am Schluss sollte das so aussehen:
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/Proxysettings_Firefox.png?raw=true"><br>
5. "OK" klicken <br>
6. "www.google.com" in der Suchleiste suchen <br>
7. Folgendes sollte kommen:
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/googleblocked.png?raw=true"><br>
8. Nun können Sie jede beliebige Website probieren und wenn es richtig konfiguriert ist, sollte alles funktionieren. <br>
<h3>Apache Webserver testen</h3>
1. 127.0.0.1:8080 in die Suchleiste: <br>
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/manipage.png?raw=true"> <br>
2. Wenn man nun vorher versucht hat auf www.google.com zu kommen, soll unter "access.log"  <b>TCP_DENIED_REPLY</b> für www.google.com:443 zu sehen sein:
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/accesslog.png?raw=true"> <br>
3. Unter cache.log sollte das in etwa so aussehen:
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/cachelog.png?raw=true">
