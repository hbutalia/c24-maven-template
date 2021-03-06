C24 iO Maven Template
=====================

This sample provides a starting template for users wishing to use Maven to generate
Java classes from their models (.dod) and transforms (.tfd) as opposed to using the
iO Studio.

The link to the latest reference documentation on the C24 iO Maven plugin can be found 
here: http://www.c24.biz/financial-messaging-technical-resources.html

The C24 iO Maven plugin requires the C24 iO Studio to be installed on the same machine and
if you are using licensed libraries, you will also need your licence to be installed. While
the location of the iO installation can be set in the pom.xml, for portability this example
requires that the IO_HOME environment variable is set.

Instructions for usage
----------------------

1. In the pom.xml update the groupId, artifactId and version as applicable for your project.

2. Replace the sample .dods and .tfd in the src/main/C24 directory with your 

3. Run mvn package

The resultant jar will just contain your generated classes. See the [Maven Shade plugin](http://maven.apache.org/plugins/maven-shade-plugin/) 
if you wish to build a jar file which also contains their dependencies.

This example explicitly sets some deployment options in the pom.xml file. You may wish
to instead set these inside your project file and to use the <projectFile> element within
the deploy task.

Details
-------

The key part of the pom is the use of the C24 iO Maven plugin:

    <plugin>
      <groupId>biz.c24.io.maven</groupId>
      <artifactId>c24-maven-plugin</artifactId>
      <version>${c24.io.maven.version}</version>
      <executions>
        <execution>
          <id>c24-model-deploy</id>
          <phase>generate-sources</phase>
          <configuration>
            <session>
              <deploy>
                <generateMainMethods>false</generateMainMethods>
                <toDir>target/generated-sources/src/main/java</toDir>
                <deployEnvironment>false</deployEnvironment>

                <!-- By default CDO's will be generated but you can optionally turn this off if you wanted SDO classes only-->
                <deployAsCDO>true</deployAsCDO>

                <!-- Deploy as SDO option-->
                <!--<deployAsSDO>true</deployAsSDO>-->

                <!-- Genrate code with java 8 options in which case the extra dependency on io-api-java8 will be needed-->
                <!--<javaVersion>JDK_8</javaVersion>-->
                
                <fileSet>
                  <directory>${basedir}/src/main/C24</directory>
                  <includes>
                    <name>**/*.dod</name>
                    <name>**/*.tfd</name>
                  </includes>
                </fileSet>
              </deploy>
            </session>
          </configuration>
          <goals>
            <goal>run</goal>
          </goals>
        </execution>
      </executions>
      <dependencies>
        <dependency>
          <groupId>biz.c24.io</groupId>
          <artifactId>c24-io-api</artifactId>
          <!-- you can choose your own desired version here -->
          <version>${c24.io.api.version}</version>
        </dependency>
        <dependency>
          <groupId>biz.c24.io</groupId>
          <artifactId>c24-io-studio</artifactId>
          <!-- you can choose your own desired version here -->
          <version>${c24.io.studio.version}</version>
        </dependency>

        <!-- if deploying with Java 8 option this will be needed-->
        <!--<dependency>-->
        <!--  <groupId>biz.c24.io</groupId>-->
        <!--  <artifactId>c24-io-api-java8</artifactId>-->
        <!--  <version>${c24.io.api.version}</version>-->
        <!--</dependency>-->
      </dependencies>
    </plugin>


This binds the plugins execution to the 'generate-sources' phase of the Maven build.
The session section controls what the plug-in does when it runs. In the example above, 
it will generate Java classes for all files ending in .dod or .tfd that it finds in the 
src/main/C24 directory. 

Plugins use separate repositories to find their dependencies than the main build so in
addition to requiring:

    <repositories>
        <repository>
            <id>c24-nexus</id>
            <name>C24 Nexus</name>
            <url>http://repo.c24io.net/nexus/content/groups/public</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
    
so that the Maven can find the c24-io-api jar for the compilation step, we also need:

    <pluginRepositories>
        <pluginRepository>
            <id>c24-nexus</id>
            <name>C24 Nexus</name>
            <url>http://repo.c24io.net/nexus/content/groups/public</url>
        </pluginRepository>
    </pluginRepositories>
    
so that Maven can find the c24-maven-plugin and its dependencies.

