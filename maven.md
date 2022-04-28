https://www.javaguides.net/2018/06/maven-cheat-sheet.html

This page gives you all the useful maven commands used in maven applications.
I will daily use these maven commands in my project work so here I am sharing a maven cheat sheet with all maven commands. 
1. Create project
Create Java project with below maven command:
mvn archetype:generate
-DgroupId=org.yourcompany.project
-DartifactId=application
Create web project with below maven command:
mvn archetype:generate
-DgroupId=org.yourcompany.project
-DartifactId=application
-DarchetypeArtifactId=maven-archetype-webapp
Create archetype from existing project
mvn archetype:create-from-project
2. Main phases
clean — delete target directory
validate — validate, if the project is correct
compile — compile source code, classes stored in target/classes
test — run tests
package — take the compiled code and package it in its distributable format, e.g. JAR, WAR
verify — run any checks to verify the package is valid and meets quality criteria
install — install the package into the local repository
deploy — copies the final package to the remote repository
3. Maven phase commands(Project Build Commands)
clean project: This command will delete target directory
mvn clean
validate project: validate the project is correct and all necessary information is available
mvn validate
compile project: compile source code, classes stored in target/classes
mvn compile
test project: run tests using a suitable unit testing framework
mvn test
package project: take the compiled code and package it in its distributable format, such as a JAR /WAR
mvn package
verify project: run any checks to verify the package is valid and meets quality criteria
mvn verify
install project: install the package into the local repository, for use as a dependency in other projects locally
mvn install
deploy project: done in an integration or release environment, copies the final package to the remote repository for sharing with other developers and projects
mvn deploy
4. Skip running tests
Compiles the tests, but skips running them.
mvn install -DskipTests=true 
Skips compiling the tests and does not run them.
mvn install -Dmaven.test.skip=true 
5. Project Site Generation
Generate site without tests reports (tests are not executed):
mvn site:site
Generate site with unit tests reports:
mvn test site:site
Generate site with unit and integration tests reports:
mvn verify site:site
6. Code quality analysis
Analyse code quality with Sonar:
mvn clean install -DskipTests=true
mvn sonar:sonar
Read Sonar configuration guide.
7. Code coverage reporting
Notice:
It is much more feasible to generate code coverage reports directly from IDE than from Maven. Write test, write code, run coverage for separated test, and check that all important branches are covered.
Generate Clover reports for unit tests:
mvn clover2:setup test clover2:aggregate clover2:clover
Generate clover reports for unit and integration tests:
mvn clover2:setup verify clover2:aggregate clover2:clover
Read Clover configuration guide.
8. Dependency Management
Check dependencies for newer versions:
mvn versions:display-dependency-updates
Check plugins for newer versions:
mvn versions:display-plugin-updates
Check for newer versions defined as properties:
mvn versions:display-property-updates
Display project dependencies:
mvn dependency:tree
Analyze project dependencies:
mvn dependency:analyze
9. Getting Help
Display effective Maven settings:
mvn help:effective-settings
Display effective POM:
mvn help:effective-pom
Display all profiles (from settings.xml and POMs hierarchy):
mvn help:active-profiles
Display plugin goals (for m-compiler-p in the example below):
mvn compiler:help
Display plugin's goal description (for goal compile in m-compiler-p in the example below):
mvn compiler:help -Dgoal=compile -Ddetail
Help plugin — used to get relative information about a project or the system.
mvn help:describe describes the attributes of a plugin
mvn help:effective-pom displays the effective POM as an XML for the current build, with the active profiles factored in. Dependency plugin — provides the capability to manipulate artifacts.
mvn dependency:analyze analyzes the dependencies of this project
mvn dependency:tree prints a tree of dependencies Compiler plugin — compiles your java code. Set language level with the following configuration:
<plugin>
 <groupId>org.apache.maven.plugins</groupId>
     <artifactId>maven-compiler-plugin</artifactId>
      <version>3.6.1</version>
      <configuration>
          <source>1.8</source>
          <target>1.8</target>
      </configuration>
</plugin>


https://gist.github.com/michaellihs/b08c89581ec597fa198cf74e2239f4a6

Maven Cheat Sheet
My collection of useful hints and snippets for Apache Maven.

POM - Project Object Model
See http://maven.apache.org/pom.html

Minimal POM
<project>
     <modelVersion>4.0.0</modelVersion>
     <groupId>com.packt</groupId>
     <artifactId>sample-one</artifactId>
     <version>1.0.0</version>
</project>
Super POM
Bundled with Maven in MAVEN_HOME/lib/maven-model-builder- 3.2.3.jar - org/apache/maven/model/pom-4.0.0.xml - defines

Maven central repository
Maven central plugin repository
Required information to build a project in <build>
A <reporting> section
The default build profile
Overriding Values in POM
Define "blocks" with the same ID as in the Super POM. Show "effective" POM with

mvn help:effective-pom
Maven Coordinates
Identify a project, dependency or plugin
A combination of groupId, artifact and version
Plugins don't need a version, as a default, org.apache.maven.plugins or org. codehaus.mojo is used
Parent POM Files
Used to avoid redundancy
Version Handling
Set version in <project><version>...</version></project> from the command line with

mvn versions:set versions:commit -DnewVersion=1.0.0-SNAPSHOT
Maven Release Plugin
The Maven Release Plugin let's you upload artifacts to Maven repositories - e.g. Artifactory.

Jenkins Pipeline for Uploading Maven Artifacts
stage 'Clone from Git'
node {
    checkout scm
}

stage 'Build'
node {
    sh 'mvn clean install -DskipTests'
}

stage 'Test'
node {
    sh 'mvn test'
}


stage 'Upload to Artifactory'
timeout(time:5, unit:'MINUTES') {
    input message: 'Do you want to push artifacts to Artifactory?'

    node {
        def pom = readMavenPom file: 'pom.xml'
        def version = pom.version.replace("-SNAPSHOT", ".${currentBuild.number}")

        /**
         * Clean any locally modified files and ensure we are actually on origin/master
         * as a failed release could leave the local workspace ahead of origin/master
         */
        sh "git clean -f && git reset --hard origin/master"

        /**
         * Description of parameters (for further details, see http://maven.apache.org/maven-release/maven-release-plugin/)
         *
         *    -DreleaseVersion            Default version to use when preparing a release or a branch.
         *    -DdevelopmentVersion        Default version to use for new local working copy
         *    -DpushChanges               Implemented with git will or not push changes to the upstream repository
         *    -DlocalCheckout             Use a local checkout instead of doing a checkout from the upstream repository
         *    -DpreparationGoals          Goals to run as part of the preparation step, after transformation but before committing
         *    -Darguments="-DskipTests"   Skips the tests in the release goal - see http://stackoverflow.com/questions/8685100/how-can-i-get-maven-release-plugin-to-skip-my-tests
         *    -B                          Run in non-interactive (batch) mode
         *
         *  Preparation steps:
         *
         *    release:prepare             see http://maven.apache.org/maven-release/maven-release-plugin/prepare-mojo.html
         *    release:perform             see http://maven.apache.org/maven-release/maven-release-plugin/perform-mojo.html
         */
        sh """mvn \
            -DreleaseVersion=${version} \
            -DdevelopmentVersion=${pom.version} \
            -DpushChanges=false \
            -DlocalCheckout=true \
            -DpreparationGoals=initialize \
            -Darguments="-DskipTests" \
            release:prepare release:perform \
            -B"""

        sh "git push origin ${pom.artifactId}-${version}"
    }

}
Artifactory Release Management
see https://wiki.jenkins-ci.org/display/JENKINS/Jenkins+Artifactory+Plugin+-+Release+Management

Troubleshooting Maven
Problems with Maven Release Plugin
Update your Git version

Make sure to have the most recent version of the plugin set in your pom.xml

<plugin>
       <groupId>org.apache.maven.plugins</groupId>
       <artifactId>maven-release-plugin</artifactId>
       <version>2.5.3</version>
</plugin>
Maven does not upload RELEASE version but SNAPSHOT
See https://issues.apache.org/jira/browse/MRELEASE-812.

Further Resources
Book: Mastering Apache Maven 3
DZone: Why I never use the Maven release plugin
Maven Release Plugin: The Final Nail in the Coffin
Maven Cheat Sheet
About publishing Maven Artifacts
  
  
  https://github.com/gkhays/cheatsheets/blob/master/Maven-CheatSheet.md
  
  Maven Cheat Sheet
Get project version
mvn -q -Dexec.executable="echo" -Dexec.args='${project.version}' --non-recursive exec:exec
1.0.0-SNAPSHOT
Run a single test case
mvn -Dtest=<file> test
Run a single test method

mvn -Dtest=TestClass#testMethod test
Debug tests -- tests will pause and await remote debugger on port 5005

mvn -Dtest=<file> -Dmaven.surefire.debug test
See Maven - Debugging Tests
See also: How to run unit test with Maven

Property references
project.build.sourceDirectory
project.build.scriptSourceDirectory
project.build.testSourceDirectory
project.build.outputDirectory
project.build.testOutputDirectory
project.build.directory
See Maven project.build.directory

Remove platform encoding warning
<project ...>
 ...
 <properties>
   <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
   ...
 </properties>
 ..
</project>
Get a dependency tree
mvn dependency:tree
Purge local Maven repository
mvn dependency:purge-local-repository
Override local repository
mvn -Dmaven.repo.local=/build/.m2/repository
Custom User settings file
-s,--settings <arg> e.g.

mvn -s ~/custom/settings.xml
Use global settings file
-gs,--global-settings <arg> e.g.

mvn -gs /build/settings.xml
Logging Level
mvn clean package -Dorg.slf4j.simpleLogger.defaultLogLevel=DEBUG
Alternatively, you can edit it in ${MAVEN_HOME}/conf/logging/simplelogger.properties or by creating .mvn/jvm.config.

Force Update from Remote Repository
-U,--update-snapshots e.g.

mvn -U
Log File
Note: Suppresses output to the console.

-l,--log-file <arg> e.g.

mvn -l maven.log
Batch Mode
mvn -B
Parallel Builds
1 thread per available CPU core

mvn -T 1C
Your Maven build is slow. Speed it up!

List Modules and Directories
Reactor Build Order

mvn validate
List modules by Artifact ID

Note: On Windows, use double quotes (") in the awk statement

mvn --also-make dependency:tree | grep maven-dependency-plugin | awk '{ print $(NF-1) }'
This variant does not require awk. As above, use double quotes for Windows.

mvn -Dexec.executable='echo' -Dexec.args='${project.artifactId}' exec:exec -q
List Module Directories

mvn -q --also-make exec:exec -Dexec.executable="pwd"
How to list active sub-modules in a Maven project?
How to list the Maven build/compilation sequence based on dependencies?
Maven Tips and Tricks: Advanced Reactor Options
Local Dependencies
<dependency>
    <groupId>com.sample</groupId>
    <artifactId>sample</artifactId>
    <version>1.0</version>
    <scope>system</scope>
    <systemPath>${project.basedir}/src/main/resources/yourJar.jar</systemPath>
</dependency>
See How to add local jar files to a Maven project?.

Alternatively

mvn install:install-file
   -Dfile=<path-to-file>
   -DgroupId=<group-id>
   -DartifactId=<artifact-id>
   -Dversion=<version>
   -Dpackaging=<packaging>
   -DgeneratePom=true

Where: <path-to-file>  the path to the file to load
   <group-id>      the group that the file should be registered under
   <artifact-id>   the artifact name for the file
   <version>       the version of the file
   <packaging>     the packaging of the file e.g. jar
Effective POM
mvn help:effective-pom
What are the difference between pom.xml and effective pom in Apache Maven?
Apache Maven Help Plugin

Generate Maven Project
mvn -B archetype:generate
    -DarchetypeGroupId=org.apache.maven.archetypes
    -DgroupId=com.log4j.maven
    -DartifactId=dependency-example
Fix broken builds with this log4j Maven dependency example

Quick Start

mvn archetype:generate \
    -DarchetypeGroupId=org.apache.maven.archetypes \
    -DarchetypeArtifactId=maven-archetype-quickstart
Plugins
Execute a specific plugin goal by ID
mvn sql:execute@create-db
See Maven plugin execution ID
See also: Run a single Maven plugin execution?z
See also: SQL Maven Plugin#Executions

Shade or Uber JAR
Automatically remove classes not being used.

<project>
  ...
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.1.0</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <minimizeJar>true</minimizeJar>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  ...
</project>
See Selecting Contents for Uber JAR.

Release Plugin
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-release-plugin</artifactId>
  <version>2.5.3</version>
  <configuration>
    <tagNameFormat>v@{project.version}</tagNameFormat>
  </configuration>
</plugin>
Maven Release Plugin > Prepare a Release

Maven Lifecycle
Lifecycle default -> [validate, initialize, generate-sources, process-sources,
generate-resources, process-resources, compile, process-classes, generate-test-sources,
process-test-sources, generate-test-resources, process-test-resources, test-compile,
process-test-classes, test, prepare-package, package, pre-integration-test, integration-test,
post-integration-test, verify, install, deploy]
[DEBUG] Lifecycle clean -> [pre-clean, clean, post-clean]
[DEBUG] Lifecycle site -> [pre-site, site, post-site, site-deploy]
Command Line Options
(Frequently used by me)

Options	Description
-B,--batch-mode	Run in non-interactive (batch) mode (disables output color)
-D,--define	Define a system property
-e,--errors	Produce execution error messages
-f,--file	Force the use of an alternate POM file (or directory with pom.xml)
-N,--non-recursive	Do not recurse into sub-projects
-o,--offline	Work offline
-P,--activate-profiles	Comma-delimited list of profiles to activate
-pl,--projects	Comma-delimited list of specified reactor projects to build instead of all projects. A project can be specified by [groupId]:artifactId or by its relative path
-rf,--resume-from	Resume reactor from specified project
-s,--settings	Alternate path for the user settings file
-U,--update-snapshots	Forces a check for missing releases and updated snapshots on remote repositories
-v,--version	Display version information
-X,--debug	Produce execution debug output
See all: Maven Command Line Options

References
Maven - alternative .m2 directory
How to Override the Maven Local Repository setting
Maven logging: Maven logging and the command line and How to change maven logging level....
Maven CLI Options Reference
Purging local repository dependencies
     
     
     https://lzone.de/cheat-sheet/Maven
     
     Maven Cheat SheetEdit Cheat Sheet
Maven Configuration
pom.xml
settings.xml
Sonatype Nexus: self-hosting a Maven repository
Maven CLI Commands
General command structure

mvn -P<profile> <command> <scope>
Simple stuff

mvn help
mvn compile
mvn validate
mvn verify
mvn test
mvn clean 
mvn clean package
mvn clean install
mvn clean deploy
ARTIFACTS
mvn archetype:create                    # Create pom.xml

mvn archetype:create -DgroupId=<group> \     # Create JAR
                     -DartifactId=<new id>

mvn archetype:create -DgroupId=<group> \     # Create WAR
                     -DartifactId=<new id> \
                     -DarchetypeArtifactId=maven-archetype-webapp

mvn install:install-file <params>            # Install dependencies
RELEASING
mvn deploy:deploy-file <params ...>

# Useful release options:
#
#    -P <profile>
#    -Dusername=<user>
#    -Dpassword=<password>
#
mvn release:prepare
mvn release:clean
mvn release:perform
TOMCAT PLUGIN
mvn tomcat:deploy
mvn tomcat:redeploy
mvn tomcat:undeploy
mvn tomcat:stop
mvn tomcat:start
IDE INTEGRATION
mvn -Declipse.workspace=<path> eclipse:add-maven-repo
mvn eclipse:eclipse
     
     

     
     

     
https://onecompiler.com/cheatsheets/maven

     Simple clean, compile, install, build commands
mvn compile                 #Compiles source code and stores classes to target/classes
mvn validate                #Validates project 
mvn test                    #Runs tests
mvn clean                   #Deletes target directory
mvn clean package           #Compiled code is packaged to WAR/JAR/deb etc
mvn clean install           #Install the artifact in local repository(.m2 Repo)
mvn clean deploy            #Copies the package(WAR/JAR/deb etc) to the remote repository
mvn verify
mvn clean verify
Options
mvn clean install -Dmaven.test.skip=true              #Skips compiling and running tests
mvn clean install -DskipTests=true                    #Compiles but skips running tests
mvn clean install -Dmaven.test.failure.ignore=true    #Compiles and executes tests but ignores if any tests failed
mvn verify Dit.test=TestName                          #Executes specified test
mvn clean install -T 4                                # -T is used to specify number of threads used, default 2 i.e., 2 threads per CPU.
mvn clean install -X/--debug                          #Enables debug mode
mvn clean package -U/--update-snapshots               #Force check on dependency updates 
mvn dependency:purge-local-repository                 #Removes local repository
Dependency Plugin
mvn dependency:analyze                                 #Analyzes dependencies of the project
mvn dependency:tree                                    #Prints dependency tree
mvn versions:display-dependency-updates                #Displays dependency updates 
mvn dependency:analyze -DignoreNonCompile=true         #Shows unused dependencies
Creates pom.xml:

mvn archetype:create
Create java project (JAR):
mvn archetype:create -DgroupId=org.onecompiler.project -DartifactId=one-compiler -DarchetypeArtifactId=maven-archetype-quickstart
Create Web project (WAR):
mvn archetype:create -DgroupId=org.onecompiler.project -DartifactId=one-compiler -DarchetypeArtifactId=maven-archetype-webapp
Eclipse sources:
mvn eclipse:eclipse                             #Eclipse creates .project, .settings, .classpath files based on pom.xml    
mvn eclipse:eclipse -DdownloadSources=true      #Also downloads dependencies
Create archetype from project:

mvn archetype:create-from-project
Lists all archetypes:

mvn archetype:generate
Useful commands for Automatic deployment with tomcat plugin in your pom.xml
mvn tomcat:deploy                   #Deploys package
mvn tomcat:stop                     #Stops tomcat server
mvn tomcat:start                    #Starts tomcat server
Gives description of attributes plugin:

mvn help:describe -Dplugin=help
Describes single goal of plugin:

mvn help:describe -Dplugin=help -Dmojo=help
Displays the effective pom.xml for the current build, with the active profiles in it:

mvn help:effective-pom 
Displays active profiles:

mvn help:active-profiles
     
     
 https://chenweixiang.github.io/docs/All_Cheat_Sheets_v2.pdf
     
     https://spsarolkar.github.io/maven/cheatsheet/2018/09/01/Maven-cheatsheet.html
     
     https://cheatsheetfactory.geekyhacker.com/java/maven
     
     https://blogs.sap.com/2016/09/12/mvn/
     Maven “cheatsheet”
0
2
1,375
Overview
Maven is a very helpful tool when it comes to developing Java applications.  Maven can be run from within an IDE but I sometimes prefer the command line.  Unfortunately for some reason the Maven commands never stick in my long term memory and I find myself constantly looking up how to do things.  Therefor I have a cheat sheet that I use it for quick reference which saved me a lot of time in the past. 

The topics covered touch the most frequent commands I use in Maven and the general flow is something like

Create the project structure
Do development
Deploy to a local server
Perform a release
Minor edit – updated release flow.

Overview
Project creation
Multi project structure
Simple java project
Web project
Olingo project
IDE setup and build projects
Eclipse project
Create
Clean
Build
Deploy to local server
Prepare the pom.xml
Create a local server
Deploy to a local server
Start/Stop local server
Maven release
Prepare the pom.xml
Perform release
Clean release
Task	Maven cmd
List archtypes	
mvn archetype:generate

Multi project	mvn archetype:generate -DarchetypeGroupId=org.codehaus.mojo.archetypes -DarchetypeArtifactId=pom-root -DarchetypeVersion=RELEASE
Simple Java project	mvn archetype:generate -DgroupId=[groupId] -DartifactId=[artifactId] -DarchetypeArtifactId=maven-archetype-quickstart -e
Web project	mvn archetype:generate -DgroupId=[groupId] -DartifactId=[artifactId] -DarchetypeArtifactId=maven-archetype-webapp
Olingo project	mvn archetype:generate -DinteractiveMode=false -DgroupId=au.project -DartifactId=odata -DarchetypeGroupId=org.apache.olingo  -DarchetypeArtifactId=olingo-odata2-sample-cars-annotation-archetype -DarchetypeVersion=2.0.0
Required Eclipse files	mvn eclipse:eclipse
Remove Eclipse files	mvn eclipse:clean
Local build and install	mvn clean install
Create local HCP server	mvn neo-java-web:install-local
Deploy to local HCP server	mvn neo-java-web:deploy-local
Start local HCP server	mvn neo-java-web:start-local
Stop local HCP server	mvn neo-java-web:stop-local
Maven release	mvn release:prepare
Maven release cleanup	mvn release:clean
Project creation
Maven does have a few very useful archetypes that will help to quickly create the structure for your project.

To list them run


mvn archetype:generate

Multi project structure
One design choice could be to break up the project structure along the lines of the application layers.  For this you would need a root project.


mvn archetype:generate -DarchetypeGroupId=org.codehaus.mojo.archetypes -DarchetypeArtifactId=pom-root -DarchetypeVersion=RELEASE

Running the above creates a project containing the root pom file.


C:.
└───project-root
        pom.xml

Simple java project
To create a basic Java project structure.  If you use this in a multi project structure go to the project root directory.


mvn archetype:generate -DgroupId=[groupId] -DartifactId=[artifactId] -DarchetypeArtifactId=maven-archetype-quickstart -e

Running above gives you the following project structure assuming you chose “model” as the artifactId.


C:\
└───project-root
    │   pom.xml
    │
    └───model
        │   pom.xml
        │
        └───src
            ├───main
            │   └───java
            │       └───au
            │           └───project
            │                   App.java
            │
            └───test
                └───java
                    └───au
                        └───project
                                AppTest.java

Web project
To create a simple web project.


mvn archetype:generate -DgroupId=[groupId] -DartifactId=[artifactId] -DarchetypeArtifactId=maven-archetype-webapp

Running above gives you the following project structure assuming you chose “web” as the artifactId.


C:\
└───project-root
    │   pom.xml
    │
    └───web
        │   pom.xml
        │
        └───src
            └───main
                ├───resources
                └───webapp
                    │   index.jsp
                    │
                    └───WEB-INF
                            web.xml

Olingo project
There are also a lot of archetypes to get you going for specific scenarios. Example to create an odata web project using Apache Olingo


mvn archetype:generate -DinteractiveMode=false -DgroupId=au.project -DartifactId=odata -DarchetypeGroupId=org.apache.olingo  -DarchetypeArtifactId=olingo-odata2-sample-cars-annotation-archetype -DarchetypeVersion=2.0.0

Running the above gives you the following web project structure.


C:\
└───project-root
    │   pom.xml
    │
    └───odata
        │   pom.xml
        │
        └───src
            └───main
                ├───java
                │   └───org
                │       └───apache
                │           └───olingo
                │               └───sample
                │                   └───annotation
                │                       ├───model
                │                       │       Address.java
                │                       │       Car.java
                │                       │       Driver.java
                │                       │       Manufacturer.java
                │                       │
                │                       ├───processor
                │                       │       AnnotationSampleServiceFactory.java
                │                       │
                │                       └───util
                │                               AnnotationSampleDataGenerator.java
                │
                ├───resources
                │       log4j.xml
                │
                └───webapp
                    │   index.jsp
                    │
                    └───WEB-INF
                            web.xml


IDE setup and build projects
Now that you have created your project structure you can start development.

Eclipse project
Usually you can just import the Maven project into Eclipse but let’s say you want to generate the Eclipse project manually or you want to delete the Eclipse project files (.settings, .project, .classpath).

Create

mvn eclipse:eclipse

Clean

mvn eclipse:clean

Build
It might be worthwhile to read through the and understand the Maven build lifecycle.  On your development machine you will usually do the following to build and install locally.  I usually run the ‘clean’ phase to delete the target folder.


mvn clean install

Deploy to local server
After you have downloaded and extracted the SAP server you will have the Neo cmd line application available to perform various tasks related to your HCP environment.  You can make use of Maven for some of the tasks related to creating server, deploy to local server, start-stop local server. For more detail read Building Java Web Applications with Maven and Working with the “Neo” Maven Plugin.

Prepare the pom.xml
You need to configure the pom file by specifying and configuring the neo maven plugin.  The following is an example I have added to the root pom.  I have left my local settings as an example but you need to make the required changes. For more information about the Neo Maven plugin.


<build>
  <plugins>
  <plugin>
  <groupId>com.sap.cloud</groupId>
  <artifactId>neo-java-web-maven-plugin</artifactId>
  <version>3.12.4.3</version>
  <executions>
  </executions>
  <configuration>
  <!-- Location of the SAP HANA Cloud Platform SDK -->
  <sdkInstallPath>C:/apps/neo-java-web-sdk-3.12.4.3</sdkInstallPath>
  <!-- WAR of the application under test -->
  <source>${project.basedir}/odata/target/odata.war</source>
  <!-- Configuration for local server -->
  <location>C:/apps/neo-java-web-sdk-3.12.4.3/server</location>
  </configuration>
  </plugin>
  </plugins>
  </build>

Create a local server
You only need to do this once.


mvn neo-java-web:install-local

After running the above the local server is created.

Deploy to a local server
After a successful build you will want to deploy the application to the local server.


mvn neo-java-web:deploy-local

Start/Stop local server
To start or stop the local server make use of the following

Start


mvn neo-java-web:start-local

Stop


mvn neo-java-web:stop-local

Maven release
Up until now we have covered the basics.  Lets have a look at something more interesting, releasing your project with Maven.  The idea is to create a release tag with a release version (maybe for production), and then to increment the trunc version.  E.g. currently our trunc (development) version is 1.0-SNAPSHOT.  When we release we create the 1.0 tag that indicates an incremental release.  The trunc version is then incremented to 2.0-SNAPSHOT.  This allows an easy way to fix bugs on the production code line. Below diagram illustrates what a very basic release flow might look like.

Release.png

Currently we are on trunc version 1.0-SNAPSHOT, below is a screen shot of a Git repo.  At this stage it is version 1.0-SNAPSHOT.

1.0-SNAPSHOT.png

Prepare the pom.xml
First specify your git repository.  Add the following to the root pom.


<scm>
  <connection>scm:git:https://git.hanatrial.ondemand.com/sometrial/myproject</connection>
  <developerConnection>scm:git:https://git.hanatrial.ondemand.com/sometrial/myproject</developerConnection>
  <url>https://git.hanatrial.ondemand.com/plugins/gitiles/sometrial%2Fmyproject</url>
  <tag>HEAD</tag>
  </scm>


Now specify and configure the release plugin.  Add the following to the root pom under build>plugins.


<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-release-plugin</artifactId>
  <version>2.5.1</version>
  <configuration>
          <username>[username]</username>
          <password>[password]</password>
        </configuration>
  <dependencies>
  <dependency>
  <groupId>org.apache.maven.shared</groupId>
  <artifactId>maven-invoker</artifactId>
  <version>2.2</version>
  </dependency>
  </dependencies>
  </plugin>


Perform release
Now you will perform the maven release.  You might want to perform this on a test repository to ensure all is working and that you understand the configuration required.

Run the following from the project root directory.

Note: Make sure you do not have any local changes before starting the release.


mvn release:prepare

After this you will notice a new Tag in your Git repo and that the pom.xml version has incremented to 1.1-SNAPSHOT

/wp-content/uploads/2016/09/release_prepare_1033788.png

Clean release
All that remains to do now is to clean up after the release.  You can run the following.


mvn release:clean

Conclusion

As you have seen Maven is a powerful tool that can be used for many development tasks.

Thank you for reading.
     
     
     
 
     https://docs.jivesoftware.com/8.0_on_prem_int/sys_admin/jive.help.core/developer/MavenCommandLineCheatsheet.html
     
     
     Maven Command Line Cheatsheet
A quick reference to Maven commands.

mvn -Dtest=<unqualified-classname> test
Tests only the specified class

mvn -Dmaven.surefire.debug test
Enables remote debugging of tests on port 5005. Surefire will block on the port until you connect with your debugger.

mvn -Dcargo.wait=true -P int integration-test
Runs CS in Tomcat via cargo

mvn help:effective-pom
Shows the logical contents of a pom.xml, including contents inherited from the parent pom.xml, up to and including the Maven super POM.

mvn dependency:tree
Shows all dependencies (including transitive dependencies) of your project. This is very helpful for debugging dependency version issues.

mvn -X <package-name>
Shows all explicit and transitive dependencies for a package, helping to identify conflicts

mvn dependency:sources

Downloads all project sources separate from IDE project creation.  Execute from root of parent project, then have your IDE synch up sources.

Not Currently Available to Customers:

mvn -Dupgrade.version=<version you're upgrading to> com.jivesoftware.maven:maven-upgrade-plugin:upgrade

Will generate a list of diff files based on product changes that correspond to your overlays. Also generates a mirrored directory structure with product files that enables you to diff against your project's web directory

Note: requires SVN access; a version is in development to use Maven dependencies instead.

mvn deploy:deploy-file -DgroupId=<package> -DartifactId=<artifact-name> -Dversion=<version-no> -Dpackaging=jar -Dfile=/path/to/file.jar -Durl=https://maven-secure.jiveland.com/archiva/repository/jive.snapshots -DrepositoryId=secure.jive.snapshots

In this example, the command deploys a jar artifact to our repository.  This command working depends on your having adequate permissions.

Powered by Jive Software
© 1999-2013 Jive Software. All rights reserved. Follow us on Twitter @JiveSoftware
     
     
  
