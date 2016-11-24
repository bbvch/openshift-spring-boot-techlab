# Openshift Origin Spring Boot Techlab

# Openshift Client Tools 'oc' installieren.
Von der Webseite
 
 https://github.com/openshift/origin/releases/tag/v1.2.2

lade das die richte openshift-origin-client-tools-v1.2.2 Version für deine Umgebung runter und lege die Datei in ein Verzeichnis das im Pfad ist.
 
starte ein Terminal und überprüfe die Version
 
```sh
$ oc version
```
erwartete Ausgabe ist

```sh
oc v1.2.2
kubernetes v1.2.0-36-g4a3f9c5  
```

# Bei Openshift anmelden
 
Um Openshift zu bedienen kann entweder die Web Console verwendet werden oder die Client Tools 'oc' console.

Das SLL Zertifikat ist self-sign daher muss es separat akzeptiert werden.

## Web Console

Die Url für die Web Console ist 

https://openshift-rg01-master.northeurope.cloudapp.azure.com:8443/console

## Client Tool 'oc'

Mit dem folgenden Befehl anmelden.

```sh
$ oc login https://openshift-rg01-master.northeurope.cloudapp.azure.com:8443
```

# Project erstellen

```sh
# ein neues Projekt erstellen.
$ oc new-project <name>

# ein Beispiel Spring Boot Project anlegen.
$ oc new-app codecentric/springboot-maven3-centos~https://github.com/codecentric/springboot-sample-app.git


```


 Ohne den Artikel wäre dieser Beitrag nicht möglich gewesen:

https://blog.codecentric.de/en/2016/03/deploy-spring-boot-applications-openshift/
