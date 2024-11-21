## What is difference of API and Implementation in Gradle build files and how to use it?
*[Notes on making build compatible with modern gradle and fix deprecations] 
- ***If you’re using Gradle 6.0 or later, some of these configurations have been deprecated and replaced by the implementation/api and testImplementation/testApi configurations.***  
    ### Summary of Replacements:
    - `compile` ➔ `api` ***(Same as compile making dependency available to consumers. Recommended to use implementation instead)***
    - `compile` ➔ `implementation`***(Preferred over api - Used for dependencies that are only needed by the current module)***
    - `compileOnly` ➔ `compileOnly` ***(No change, this is used for Compile Time Only Scope)***
    - `testCompile` ➔ `testImplementation` ***(Used for scoping tests dependencies just to the tests)***
    - `testCompile` ➔ `testApi` ***(Also works but is not common, use testImplimentation instead)***
 
- ***Original source for this project is primarily setup with a Maven build. Maven Surefire Plugin forking max memory needs to be increased to run.***  
    - In the root POM update the surefire plugin xml to the below and it corrects it.  
        ```xml
            <plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-surefire-plugin</artifactId>
				<!--<version>2.18.1</version>-->
				<version>3.0.0-M7</version>
				<configuration>
					<forkCount>1</forkCount>
					<reuseForks>false</reuseForks>
					<argLine>-Xms512m -Xmx2048m</argLine>
				</configuration>
			</plugin>
        ```
- [] 
- [] 
- [] 
- [] 
- [] 
- [] 
- [] 
- [] 
- [] 
- [] 

*[ ]*  
*[ ]*  
*[ ]*  