Linux, MacOS, Windows: [![Build Status](https://travis-ci.org/yannrichet/rsession.png)](https://travis-ci.org/yannrichet/rsession)
Windows: [![AppVeyor Build Status](https://ci.appveyor.com/api/projects/status/github/yannrichet/rsession?branch=master&svg=true)](https://ci.appveyor.com/project/yannrichet/rsession)

[![codecov](https://codecov.io/gh/yannrichet/rsession/branch/master/graph/badge.svg)](https://codecov.io/gh/yannrichet/rsession)
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.github.yannrichet/Rsession/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.github.yannrichet/Rsession)

# Rsession: R (3.5) sessions wrapping for Java (8+) #

Rsession provides an easy to use java class giving access to remote or local R sessions.
The back-end engine should be:

 * "true" R (3.5 & 3.6), through Rserve (locally spawned automatically if necessary, fully compatible with legacy R),
 * Renjin 3.5 (lower compatibility, but still very good),
 * and R2js, which is an on-the-fly translation to math.js, with lowest compatibility and hack-style coding, but full BSD licence.

Rsession differs from R2js, Rserve or Renjin as it is a higher level API, and it includes server side startup of Rserve. It is also easier to use as it provides a multi session R engine for all these wrappers.

JRI is another alternative, but it does not provide multi-sessions feature.

## Example Java code ##
```java
import static org.math.R.*;
...
 
    public static void main(String args[]) {
        Rsession r = RserveSession.newInstanceTry(System.out, null);

        double[] rand = (double[]) r.eval("rnorm(10)"); //create java variable from R command

        //...
        r.set("c", Math.random()); //create R variable from java one

        r.save(new File("save.Rdata"), "c"); //save variables in .Rdata
        r.rm("c"); //delete variable in R environment
        r.load(new File("save.Rdata")); //load R variable from .Rdata

        //...
        r.set("df", new double[][]{{1, 2, 3}, {4, 5, 6}, {7, 8, 9}, {10, 11, 12}}, "x1", "x2", "x3"); //create data frame from given vectors
        double value = (double) (r.eval("df$x1[3]")); //access one value in data frame

        //...
        r.toJPEG(new File("plot.jpg"), 400, 400, "plot(rnorm(10))"); //create jpeg file from R graphical command (like plot)

        String html = r.asHTML("summary(rnorm(100))"); //format in html using R2HTML
        System.out.println(html);

        String txt = r.asString("summary(rnorm(100))"); //format in text
        System.out.println(txt);

        //...
        System.out.println(r.installPackage("sensitivity", true)); //install and load R package
        System.out.println(r.installPackage("DiceKriging", true));

        r.end();
    }
```
## Use it ##

### Using R2js backend: ###

No dependency required. Only based on Nashorn engine bundled in Java >8, so just add `rsession.jar` in your classpath:

  * https://github.com/yannrichet/rsession/blob/master/Rsession/dist/rsession.jar

Then instanciate R session using:
```java
        Rsession r = new R2jsSession(System.out,null);
```


### Using Renjin backend: ###

Add `rsession.jar:renjin-jar-with-dependencies.jar` in your classpath: 

  * https://github.com/yannrichet/rsession/blob/master/Rsession/dist/rsession.jar
  * https://github.com/yannrichet/rsession/blob/master/Rsession/lib/renjin-jar-with-dependencies.jar


Then instanciate R session using:
```java
      Rsession r = new RenjinSession(System.out,null);
      ...
```


### Using Rserve backend: ###

Install R 3.5 or 3.6 from http://cran.r-project.org, then add `rsession.jar:Rserve*.jar:REngine*.jar` in your project classpath:

  * https://github.com/yannrichet/rsession/blob/master/Rsession/dist/rsession.jar
  * https://github.com/yannrichet/rsession/blob/master/Rsession/lib/REngine-2.1.0.jar
  * https://github.com/yannrichet/rsession/blob/master/Rsession/lib/Rserve-1.8.1.jar
  

Then:
  * start Rserve on localhost `/usr/bin/R CMD /usr/lib/R/library/Rserve/libs/Rserve --vanilla --RS-enable-control --RS-port 6311`, and instanciate R session using:
      ```java
      Rsession r = RserveSession.newLocalInstance(System.out,null); 
      ```
  * or use the auto-spawned Rserve (may fail for exotic configuration):
      ```java
      Rsession r = RserveSession.newInstanceTry(System.out,null);
      ```
  * connect to remote Rserve (eg. previously started on 192.168.1.1 with `/usr/bin/R CMD /usr/lib/R/library/Rserve/libs/Rserve --vanilla --RS-enable-control --RS-port 6311`:
      ```java
      Rsession r = RserveSession.newRemoteInstance(System.out,RserverConf.parse("R://192.168.1.1"));
      ```


### Through maven dependency: ###


Alternatively to setup classpath manually, just use maven:

```xml
<dependencies>
...
    <dependency>
      <groupId>com.github.yannrichet</groupId>
      <artifactId>Rsession</artifactId>
      <version>3.0.8</version>
    </dependency>
...
</dependencies>
```

![Analytics](https://ga-beacon.appspot.com/UA-109580-20/rsession)
