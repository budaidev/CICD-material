# Jenkins

* Egyik legrégebbi és legelterjedtebb CI/CD eszköz
* Ingyenes, nyílt forráskódú
* Nagyon sok plugin
* Skálázható (master és agents) 
* Pipelines "as code", DSL: `Jenkinsfile`
* Stage-ek, melyek step-ekből állnak
* Kétféleképp adható meg
  * Scripted: régebbi, Groovy nyelven adható meg
  * Declarative: szigorúbb struktúra, limitáltabb, egyszerűbb elkezdeni
* Felkészítve konténerizált könyezetre
  * Docker konténerben futtatható
  * Buildek külön Docker konténerben futtathatóak

## Jenkins start - practice

```shell
xcopy /e /i javax-cip-public\jenkins jenkins
cd jenkins
code .
```

* Saját image:
  * Docker client
  * Pluginek
  * Varázsló kikapcsolása



## Run 

```bash
docker-compose up -d
```

## Docker setup


