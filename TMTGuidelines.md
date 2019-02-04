## Test, Maven and Travis Guidelines 
A set of guidelines, explaining how and what to do for a future project that must be able to do the following:  
  
###### Unit Test all “relevant” public methods via Maven
Brug TDD til at teste grænseværdier mm.
```mvn test```

###### Exclude integration tests from your unit test, and why this is necessary
Det er vigtigt at ekskludere integration tests fra sine unit tests, da integrations tests kræver at ens program kører på en server. (f.eks. når Web API skal testes).

Tilføj maven surefire og failsafe plugin:
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.12.1</version>
    <configuration>
       <excludes>
          <exclude>**/integrationtests/*</exclude>
       </excludes>
    </configuration>
</plugin>

<plugin>
   <groupId>org.apache.maven.plugins</groupId>
   <artifactId>maven-failsafe-plugin</artifactId>
   <version>2.12.4</version>
   <configuration>
     <includes>
       <include>**/integrationtests/*</include>
     </includes>
   </configuration>
   <executions>
     <execution>
       <goals>
         <goal>integration-test</goal>
         <goal>verify</goal>
       </goals>
     </execution>
   </executions>
</plugin>
```

Opret herefter en ny package med navnet ```"integrationtests”``` under test packages til at lave dine integration tests.

###### Perform integration tests, and only integration tests, via maven’s verify phase
```mvn verify``` 

###### How and what to do, to test a Tomcat based WEB-API, via maven’s verify phase
```
<profiles>
<profile>
  <id>test</id>
  <build>
     <plugins> 
  	<plugin>  
   	<groupId>org.apache.tomcat.maven</groupId>  
   	<artifactId>tomcat7-maven-plugin</artifactId>  
   	<version>2.2</version>  
   	<configuration>  
     	  <path>/</path>
     	  <port>7777</port>
   	</configuration>
   	<executions>
     	 <execution>
       	  <id>start-tomcat</id>
       	  <phase>pre-integration-test</phase>
       	  <goals>
         	    <goal>run</goal>
       	  </goals>
       	  <configuration>
         	    <fork>true</fork>
       	  </configuration>
     	 </execution>
     	<execution>
       	<id>stop-tomcat</id>
       	<phase>post-integration-test</phase>
       	<goals>
         <goal>shutdown</goal>
       </goals>
     </execution>
    </executions>
   </plugin>
  </plugins>
  </build>
 </profile>
</profiles>
```
Vi kan nu teste ved at skrive “ ```mvn verify -Ptest``` ” 
###### How and what to do, to deploy your code via maven
```
<profile>
  <id>deploylocal</id>
  <build>
    <plugins>
      <plugin>  
        <groupId>org.apache.tomcat.maven</groupId>  
        <artifactId>tomcat7-maven-plugin</artifactId>  
        <version>2.2</version>  
        <configuration>
         <url>http://localhost:8084/manager/text</url>  
         <server>TomcatServer</server>
         <path>/deploy</path>
         <username>XXX_USER</username>
         <password>XXX_PASSWORD</password>
         <update>true</update>
        </configuration>  
      </plugin>
    </plugins>
  </build>
</profile>
```

Vi kan deploye ved at skrive ”```mvn tomcat7:deploy -Pdeploy```”

Hvis vi også skal køre vores integration test: 
”```mvn verify -Ptest tomcat7:deploy -Pdeploy```”


###### How and what to do, to integrate your project with Travis
1.	Host your project on GitHub
2.	Sign in to Travis CI
3.	Flip the Switch
4.	Add a .travis.yml file, commit, and push this file to Github.

```
language: java
jdk: oraclejdk8

cache:
  directories:
    - $HOME/.m2

script:
  mvn verify -Ptest

after_success:

  	- mvn tomcat7:deploy -Pdeploy
```
