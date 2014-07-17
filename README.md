# JSPC issue with jetty 9.2
This is a sample project whos purpose is to demonstate an issue when trying to compile a jsp with a taglibs in jetty 9.2 series

I have a simple webapp with a single jsp index.jsp that use two taglibs
```java
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ taglib uri="http://displaytag.sf.net" prefix="display"%>
```
The jstl taglibs should be found in jetty's system classpath
The displaytag taglibs should be found in the webapp's classpath


When compiling the project with maven you can choose to compile your jsp at runtime, or precompile them with jetty-jspc-maven-plugin by activating the prod profile.


## Jetty 9.1.x
First, to demonstrate it is an issue with jetty 9.2 I have verified that everything work with 9.1

Without precompiling the jsp:
```bash
$ mvn -DjettyVersion=9.1.5.v20140505 clean compile jetty:run-war 
```
No error during compilation or when accessing http://localhost:8080/index.jsp


When precompiling the jsp:
```bash
$ mvn -DjettyVersion=9.1.5.v20140505 -Pprod clean compile jetty:run-war
```
No error during compilation or when accessing http://localhost:8080/index.jsp


## Jetty 9.2.x
With Jetty 9.2.x things get a little bit complicated. If I don't precompile my jsps, then when accessing my jsp for the first time, the compilation failed as jetty can't find the jstl's tld, but it can find the displaytag one.

If I try to precompile my jsp with jetty-jspc-maven-plugin then the jsp precompilation failed as jspc can't find the displaytag's tld in the webapp classpath.

Depending on how I compile the jsp either one or the other is not found.


Without precompiling the jsp:
```bash
$ mvn -DjettyVersion=9.2.1.v20140609 clean compile jetty:run-war 
```
No error during compilation but it failed to find jstl when accessing the jsp

  org.apache.jasper.JasperException: L'uri absolue: http://java.sun.com/jsp/jstl/core ne peut être résolu dans le fichier web.xml ou dans les fichiers jar déployés avec cette application


When precompiling the jsp:
```bash
$ mvn -DjettyVersion=9.2.1.v20140609 -Pprod clean compile jetty:run-war
```
The project failed the compile, this time jspc can't find displaytag.

  org.apache.jasper.JasperException: L'uri absolue: http://displaytag.sf.net ne peut être résolu dans le fichier web.xml ou dans les fichiers jar déployés avec cette application -> [Help 1]