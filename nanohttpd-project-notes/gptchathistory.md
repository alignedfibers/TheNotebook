## NanoHTTPD Upgrade to Java 11+ and Gradle 7+ & Adding Android Gradle Plugin & More
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
- ***Pain in freaking butt all the places to set Gradle and JAVA Version***  
    - `gradle/wrapper/gradle-wrapper.properties` ***(distributionUrl=https\://services.gradle.org/distributions/gradle-7.0.2-bin.zip)***
    - `export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64` ***(Required in the terminal, you will likely want to make this in bashrc)***
    - `File>Settings>Build Execution Deployment>Build Tools>Gradle` 
    - ***(Easy to get confused, it is not the GRADLE SDK it is talking about here: Select Distribution:Wrapper and Gradle JDK 11.11.0.25/usr/lib/jvm/java-11)***
    - ***(The default always sets it to Java 21, you can also set this to a local installed SDK, I am confused though as it does not change the Gradle version)***
    - ***(Does not change the Gradle Version in gradle/wrapper/gradle-wrapper.properties)***
    - ***Your build also has to have AGP in root build.gradle under declaration `plugins {id 'com.android.library' version '7.0.1' apply false}`***
    - ***The Android Application plugin is used if you build application and the other if build library, you need to choose correct on and apply it***
    - ***The apply should look like: `apply plugin: 'com.android.application` and goes at top of the build file in root of the app directory / module directory***
    - ***If you choose you can create a common.gradle on the root of project like me to create quick use functions to apply your build to type configs***
    - ***Unfortunately, choosing the Java Version does not have a method in which it is allowed exactly at least not that I found for AGP, maybe a different plugin***
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