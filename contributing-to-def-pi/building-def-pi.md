# Building dEF-Pi

Maven is used to integrate all projects, and as such, the following command can be used to compile all the projects:

```bash
mvn -f master/pom.xml install
```

The project is separated in the following sub projects, that are build via the maven command. These sub-projects are:

* master
* api
* commons
* orchestrator
* service
* maven-plugin

The `pom.xml` file used to integrate all the projects can be found in the _master_ folder in the repository.