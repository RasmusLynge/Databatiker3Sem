## Test, Maven and Travis Guidelines 
A set of guidelines, explaining how and what to do for a future project that must be able to do the following:  
  
**Unit Test all “relevant” public methods via Maven**
**Exclude integration tests from your unit test, and why this is necessary**

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

**Perform integration tests, and only integration tests, via maven’s verify phase**
**How and what to do, to test a Tomcat based WEB-API, via maven’s verify phase**
**How and what to do, to deploy your code via maven**
**How and what to do, to integrate your project with Travis**
