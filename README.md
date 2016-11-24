# Openshift Origin Spring Boot Techlab

## Openshift Client Tools 'oc' installieren.
Von der Webseite
 
 https://github.com/openshift/origin/releases/tag/v1.2.2

lade das die richte openshift-origin-client-tools-v1.2.2 Version für deine Umgebung runter und lege die Datei in ein Verzeichnis das im Pfad ist.
 
starte ein Terminal und überprüfe die Version
 
```sh
$ oc version
oc v1.2.2
kubernetes v1.2.0-36-g4a3f9c5  
```

## Bei Openshift anmelden
 
Um Openshift zu bedienen kann entweder die Web Console verwendet werden oder die Client Tools 'oc' console.

Das SLL Zertifikat ist self-sign daher muss es separat akzeptiert werden.

### Web Console

Die Url für die Web Console ist 

https://openshift-rg01-master.northeurope.cloudapp.azure.com:8443/console

### Client Tool 'oc'

Mit dem folgenden Befehl anmelden.

```sh
$ oc login https://openshift-rg01-master.northeurope.cloudapp.azure.com:8443
```

## Projekt Setup

Leider ist es nicht möglich direkt ein Spring Boot Projekt in Openshift zu deployen. 

```sh
# ein neues Projekt erstellen.
$ oc new-project <name>

# ein Beispiel Spring Boot Project anlegen.
$ oc new-app codecentric/springboot-maven3-centos~https://github.com/codecentric/springboot-sample-app.git

# den Status vom Deplyoment überwachen. Entweder in der Web Console oder mit dem Befehl
oc status

# wenn das Deployment abgeschlossen ist sollte ein neuer pod und ein service laufen
oc get pods
oc get services

# wir überprüfen ob der Service bereits von Aussen sichtbar ist.
oc get routes

# dieser Serivce ist noch nicht von Aussen sichtbar es gibt keine Route. Um dies zu ändern erstellen wir eine.
oc expose service <service name>

```

jetzt ist der Spring Boot Service von Aussen erreichbar.

Wenn wir oc status ausführen bekommen wir eine Warnung wie folgt

```sh
dc/springboot-sample-app has no readiness probe to verify pods are ready to accept traffic or ensure deployment is successful
```

## Rediness Probe

Um eine rediness probe hinzuzufügen forken wir das Projekt von github.

https://github.com/codecentric/springboot-sample-app.git

checken es lokal aus fügen dem pom.xml die folgende dependency hinzu

```xml
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
```

diese Änderung einchecken und pushen.

Dann ein neues Projekt anlegen wie oben und euer eigenes Repository (eben forked) deployen

```sh
oc new-app codecentric/springboot-maven3-centos~https://github.com/<dein github user>/springboot-sample-app.git
```

auch diesen Build und Deployment kann man mit oc status oder über die Web Console überwachen.
Wenn das Deplyoment erfolgreich wollen wir auch diesen Service nach Aussen sichtbar machen.

```sh
# erst den serivce namen ermitteln
oc get services

# dann den serivce nach Aussen sichtbar machen
oc expose service <service name>
```

jetzt können wir auf diesem Service /health aufrufen.

Wir nutzen /health um damit einen rediness Check in Openshift zu definieren. Erst /health erfolgreich aufgerufen werden kann ist unser Service bereit für Traffic.

```sh
# erst den namen der deployment configuration ermitteln
oc get deploymentconfig

# dann diese deployment configuration ändern
oc edit dc <deployment config name>

```
unter diesem Pfad 
```xml
spec:
  [......]
  template:
    [......]
    spec:
        [......]
        resources: {}
```

die rediness probe hinzufügen:

```xml
spec:
  [......]
  template:
    [......]
    spec:
        [......]
        resources: {}
        readinessProbe:
          httpGet:
            path: /health/
            port: 8080
            scheme: HTTP
          initialDelaySeconds: 10
          timeoutSeconds: 1
```














siehe https://spring.io/guides/gs/spring-boot/ für mehr Details.



```


 Ohne den Artikel wäre dieser Beitrag nicht möglich gewesen:

https://blog.codecentric.de/en/2016/03/deploy-spring-boot-applications-openshift/
