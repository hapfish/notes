
# Configure Maven to Download from Nexus

http://www.sonatype.org/nexus/go/  
http://www.sonatype.org/nexus/downloads/  

http://www.sonatype.org/nexus/archived/  


https://support.sonatype.com/entries/20943003-Configure-Maven-to-Download-from-Nexus  

https://support.sonatype.com/entries/21283268-Configure-Maven-to-Deploy-to-Nexus  



If you are adopting Nexus for internal development you should configure a single Nexus group which contains both releases and

snapshots. To do this, add snapshot repositories to your public group, and add the following mirror configuration to your Maven

settings in ~/.m2/settings.xml

Configuring Maven to Use a Single Nexus Group.
<settings>
  <mirrors>
    <mirror>
      <!--This sends everything else to /public -->
      <id>nexus</id>
      <mirrorOf>*</mirrorOf>
      <url>http://localhost:8081/nexus/content/groups/public</url>
    </mirror>
  </mirrors>
  <profiles>
    <profile>
      <id>nexus</id>
      <!--Enable snapshots for the built in central repo to direct -->
      <!--all requests to nexus via the mirror -->
      <repositories>
        <repository>
          <id>central</id>
          <url>http://central</url>
          <releases><enabled>true</enabled></releases>
          <snapshots><enabled>true</enabled></snapshots>
        </repository>
      </repositories>
     <pluginRepositories>
        <pluginRepository>
          <id>central</id>
          <url>http://central</url>
          <releases><enabled>true</enabled></releases>
          <snapshots><enabled>true</enabled></snapshots>
        </pluginRepository>
      </pluginRepositories>
    </profile>
  </profiles>
  <activeProfiles>
    <!--make the profile active all the time -->
    <activeProfile>nexus</activeProfile>
  </activeProfiles>
</settings>


We have defined a single profile - "nexus".   This profile is configured to download from a repository with the id "central" that has

been configured with a bogus URL. This URL is overridden by the mirror setting in the same settings.xml file to point to the URL of

your single Nexus group. The nexus group is then listed as an active profile in the activeProfiles element.

 


Configure Maven to Deploy to Nexus

To configure a Maven project to publish artifacts to Nexus, you'll need to add a distributionManagement element to your project's

pom.xml.    Here's an example of a distributionManagement element that contains the appropriate configuration to deploy snapshots and

releases to a server running on your.server.com port 8081:

```  
<distributionManagement>
  <repository>
     <id>deployment</id>
     <name>Internal Releases</name>
     <url>http://your.server.com:8081/nexus/content/repositories/releases/</url>
  </repository>
  <snapshotRepository>
     <id>deployment</id>
     <name>Internal Releases</name>
     <url>http://your.server.com:8081/nexus/content/repositories/snapshots/</url>
  </snapshotRepository>
</distributionManagement>

```  


If your Nexus server requires authentication, you will also need to place your credentials in ~/.m2/settings.xml in a servers element. 

Here's a sample servers element using the default username and password for the deployment user:

```  
<servers>
  <server>
    <id>deployment</id>
    <username>deployment</username>
    <password>deployment123</password>
  </server>
</servers>

```  



Once you've configured your project's pom.xml and your Maven Settings, you can deploy your project with "mvn deploy" which will execute

the build up to the deploy phase.   When you run a deploy with a project that has a snapshot version, Maven will deploy to the

repository defined in snapshotRepository, and when you run a build with a project that has a release version it will deploy to the

repository defined in the repository element.

 
