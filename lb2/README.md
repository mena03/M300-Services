<h1>LB2 - Rubén Mena</h1>
<h2>Inhaltsverzeichnis</h2>
1. Wichtige Links <br>
2. Einführung <br>
  2.1 Infos zu de GIT Repos <br>
  2.2 Grafische Übersicht der Services <br>
3. Dokumentation <br>
3.1 Vagrantfile <br>
3.2. Erklärung des Codes <br>
4. Sicherheit <br>
5. Testen <br>
6. Quellenverzeichnis <br>

<h2>1. Wichtige Links</h2>

<a href="https://github.com/mena03/M300-Services">M300-Services GIT Repository</a><br>
<a href="https://github.com/mena03/vagrant-webserver">vagrant-webserver GIT Repository (für wget)</a><br>
<a href="http://127.0.0.1:8080">Webserver Main Page</a><br>
<a href="http://127.0.0.1:8080/logs/squid3/access"></a>access.log im Webserver<br>
<a href="http://127.0.0.1:8080/logs/squid3/cache"></a>cache.log im Webserver<br>

<h2>2. Einführung</h2>
Ich habe mit Hilfe von Vagrant eine VM gemacht, die automatisch einen Proxy (Squid) und Webserver (Apache) installiert und konfiguriert. Der Proxy soll jeden Internettraffic erlauben bis auf eine bestimmte Website (www.google.com). Der Apache-Webserver soll uns Zugriff auf Log-Files geben ohne sich auf die VM verbinden zu müssen (z.B. per SSH, FTP, etc.). Der Webserver wird mit einem Passwort geschützt, so dass nicht jeder einfach die Log-Files des Proxy lesen kann. <br>
<h3>2.1 Infos zu den GIT Repos</h3>
Ich habe für alle restlichen Files, die nicht das Vagrantfile oder das README.md sind, ein seperates GIT Repository gemacht, dass öffentlich ist. Man findet es oben unter "1. Wichtige Links". Ich habe das so gemacht, dass 1. M300-Services sauber bleibt und 2. es nie Probleme hat im Vagrantfile mit meinen wget Zeilen. 

<h3>2.2 Grafische Übersicht der Services</h3>
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/service_graphic.png?raw=true"/>
<h2>3. Dokumentation</h2>
<h3>3.1 Vagrantfile</h3>
<p>Das Vagrantfile ist für die automatisierte Konfiguration der VM zuständig. Hier ist das Vagrantfile noch zu sehen:</p>
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/vagrantfile.png"/>

<h4>3.2 Erklärung des Codes</h4> 
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
    <td>37, 52, 58</td>
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
    <tr>
    <td>54</td>
    <td>Erstellt i File /etc/apache2/.htpasswd einen User namens apache mit Passwort apache</td>
  </tr>
    <tr>
    <td>55</td>
    <td>Ladet das Configfile für das Login Prompt herunter</td>
  </tr>
    <tr>
    <td>56</td>
    <td>Bennent das Standard Conf File für das Loginprompt um (mit .backup Endung)</td>
  </tr>
    <tr>
    <td>57</td>
    <td>In Zeile 55 heruntergeladene Datei in /etc/apache2/sites-available/ verschoben</td>
  </tr>
    <tr>
    <td>59</td>
    <td>beendet den Shell Progress (das Ende von den Bash Commands)</td>
  </tr>
    <tr>
    <td>60</td>
    <td>das Ende des Vagrantfiles</td>
  </tr>
</table>
<h2>4. Sicherheit</h2>
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/googleblocked.png?raw=true">
<p>Ich habe mit dem blocklist-File und der squid.conf Sicherheit für den Proxy User gewährleistet. Natürlich ist Google keine gefährliche Website, aber man könnte nun unzählige Websites in dieses blocklist-File zu schreiben, um unangebrachte, gefährliche oder sonstige Seiten zu blockieren.<br><br>
Da man Proxys auch mit GPOs von einem Active Directory oder einer Firewall erzwingen kann für bestimmte User und Gruppen ist das sehr effektiv, nicht nur um das Netzwerk zu schützen, sondern auch um den Traffic zu analysieren.<br><br>
Der Apache HTTP-Server wird noch mit einem Passwort geschützt, so dass nicht jeder die Logfiles vom Proxy lesen kann. 
</p> <img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/loginprompt.png?raw=true"/>
<h2>5. Testen</h2>
<h3>Squid Proxy testen</h3>
<h4>blockierte und erlaubte Websites</h4>
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/squidconf.png?raw=true">
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/blocklist.png?raw=true">
<p>Wie man in den Bildern erkennen kann, ist diese Regel nur für den localhost definiert. Im blocklist-File habe ich die Seite .google.com drin. Mit "http_access allow !block" sage ich, dass alles durchgelassen werden soll, bis auf die definierten Seiten im blockfile. Um nun zu testen, ob die Konfiguration funktioniert empfehle und verwende ich <a href="https://www.mozilla.org/de/firefox/new/">Firefox</a> als Browser, weil man dort nur für den Browser selbst den Proxy einstellen kann und nicht für den ganzen Host.</p>

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
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/loginprompt.png?raw=true"/><p>melden Sie sich mit dem User "apache" und dem Passwort "apache" an.</p> 
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/manipage.png?raw=true"> <br>
2. Wenn man nun vorher versucht hat auf www.google.com zu kommen, soll unter "access.log"  <b>TCP_DENIED_REPLY</b> für www.google.com:443 zu sehen sein:
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/accesslog.png?raw=true"> <br>
3. Unter cache.log sollte das in etwa so aussehen:
<img src="https://github.com/mena03/M300-Services/blob/main/lb2/images/cachelog.png?raw=true">

<h2>6. Quellenverzeichnis</h2>
<a href="https://wiki.squid-cache.org/SquidFaq/ConfiguringSquid">Squid.conf Hilfe</a><br>
<a href="https://httpd.apache.org/docs/2.4/mod/mod_auth_basic.html">Hilfe mit Apache Baisc Auth</a><br>
<a href="https://www.youtube.com/watch?v=00bwCjPp-FU">Basic Auth Configuration</a>
