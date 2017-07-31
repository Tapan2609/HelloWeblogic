Hi Everyone,

This documents explains process of deployment of .war file into weblogic server using distelli software.

Prerequisites:
--------------------
1. Create Distelli account.
2. Install weblogic server into your machine (This project was created with ubuntu enivornment)
3. Keep weblogic server in running state.


Tools used:
-----------------
weblogic server - 10.3.6
Distelli
Maven 3


Steps:
-------------

Step 1 : Creating an application

* Migrate to source folder of your project in your machine.

[In this case, I have located my project in below mentioned path
/home/tapankumar/workspace_neon/<Project Name>]

eg : /home/tapankumar/workspace_neon/WeblogicIntegrationWithDistelli

$ cd  /home/tapankumar/workspace_neon/WeblogicIntegrationWithDistelli


Step 2: Creating distelli-manifest file
The Distelli Manifest file provides the Distelli Platform with the needed information to successfully deploy your application. This file must exist before pushing your application to Distelli.

* Create "distelli-manifest.yml" file in any text editor(eg : notepad) and save file with extension .yml
* Include below mentioned details(within dotted lines) in your distelli-manifest.yml file
----------------------------------------------------------------Start of file-----------------------------------------------
yourusername/HelloWeblogic<Distelli Application Name>:

    Env:
        # Set the JAVA_HOME below for the destination
        # deployment server JRE directory.
        #
        - MW_HOME : "/opt/Oracle/Middleware"
        - WLS_HOME: "$MW_HOME/wlserver_10.3"
        - JAVA_HOME: "/usr/lib/jvm/java-1.8.0-openjdk-amd64"
        #
        # JVM_ARGS: '"-Duser.timezone=UTC -Xmx128M -Xms128M"'
        - PORT: "7001"
        # ARGS: "$PORT"

    Build:
        - mvn package

    PkgInclude:
        - /opt/Oracle/Middleware/user_projects/domains/hw_domain/servers/AdminServer/deployments/*.war
        # lib/*.war


    PostInstall:
        - echo "PostStart"
        # publicip=$(curl -s ident.me)
        # echo "Public IP $publicip"
        # 'sudo cp -f ./target/*.war $CATALINA_HOME/webapps/'
        - sudo -s sh /opt/Oracle/Middleware/wlserver_10.3/server/bin/setWLSEnv.sh
        - sudo -s java -cp /opt/Oracle/Middleware/wlserver_10.3/server/lib/weblogic.jar weblogic.Deployer -adminurl t3://localhost:7001 -username weblogic -password weblogic123 -deploy -name WeblogicIntegrationWithDistelli -targets AdminServer -source /opt/Oracle/Middleware/user_projects/domains/hw_domain/servers/AdminServer/deployments/HelloWeblogic.war
        # By default tomcat detects if the war file has changed and
        # automatically extracts it. If restart is required you can
        # restart tomcat with the command below.
        # Restart Tomcat - You might have to use a different command
        # depending on how tomcat is installed
        # sudo service tomcat8 restart
        - 'echo "You can validate the install by pointing your browser at http://$publicip:$PORT/HelloWorldDistelli"'

-----------------------End of file-----------------------------------------------------------------------------------

* Replace the <username> placeholder with your Distelli username.
* Give any proper name for your distelli application ...In this case i have used name as "HelloWeblogic".

Note : In .yml (YAML format) files correct indentation is important.[Sample distelli-manifest.yml file attached with this code].

Step 3 : Login into Distelli account from cmd prompt

$ distelli login
Distelli Email : <Your registered email id>
password : <your distelli login password>

Step 4 : Creating a Version of the Application on Distelli

In this step, you’ll create a remote version of your application on Distelli using the distelli create command. You only need to create the application once, after that you can push the application, and subsequent versions, up to Distelli.

To create an application in Distelli, enter the following command replacing YourUserName with your Distelli username:

$ distelli create YourUserName/HelloWeblogic


Step 5 : Pushing your First Version

In this step, you’ll push your first application version to Distelli using the distelli push command. Enter the following command in the terminal:

$ distelli push -m


Step 6 : Creating an Environment

When you deploy an application with Distelli, you can deploy the application to an Environment which may contain 1 or more servers. You can imagine this in relation to the Software Development Life Cycle where you have several environments which may include; Dev, Test, Staging, and Production. And each of these environments may have 1 or more servers. In this step, you’ll create an environment and add the destination server. To do this:

* Login to the Distelli WebUI at [https://www.distelli.com/login](https://www.distelli.com/login).
* Click the HelloWeblogic application name.
* Click the Environments link.
* Click the Create Environment button.
* Enter the Name LocalWeblogic.
* Click Create Environment
You have just created an environment named Production-ruby-ubuntu.

* Click the Servers link.
* Click Add Servers link.
* Select the Add Server checkbox for the server you installed the agent on.
* Click the Add Selected Servers link.
You have added server(s) to the environment.


Step 7 : Deploying your Release

In this step, you’ll deploy your release to the Production-ruby-ubuntu environment which includes your destination server. On the development server where you installed the Distelli CLI and the sample application ensure you are in the RubyUbuntuSimpleApp root directory where the Distelli Manifest file is and use the distelli deploy command.

$ distelli deploy -m "Initial Release" -e LocalWeblogic



Step 8 : Validation

* Login into your weblogic server "http://localhost:7001/console"
* You must able to see your war file in deployments tab.
Home --> Deployments --> HelloWeblogic.war
