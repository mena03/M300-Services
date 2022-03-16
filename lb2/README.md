<h1>LB2 - Rubén Mena</h1>
<h2>Planung</h2>
Ich habe mit Hilfe von Vagrant eine VM gemacht die automatisch einen Proxy (Squid) und Webserver (Apache) installiert und konfiguriert. Der Proxy soll jeden Internettraffic erlauben bis auf eine bestimmte Website (z.B. www.google.com). Der Apache-Webserver soll uns Zugriff auf Log-Files geben ohne sich auf die VM verbinden zu müssen (z.B. per SSH, FTP, etc.). 

<h2>Dokumentation</h2>
<h3>Vagrantfile</h3>
<img src="/images/vagrantfile.png"/>

<h2>Test</h2>
