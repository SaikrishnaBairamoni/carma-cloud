# CARMAcloud

## Documentation
CARMAcloud provides some of the infrastructure components for CARMA. It enables users to define geofences, rules of practice, replay storms to test weather-related rules of practice, as well as monitor CARMA-enabled vehicles and the messages and controls exchanged with them.

## Deployment
CARMAcloud can be deployed on a Linux server. Ensure you have a properly configured git client and Java Development Kit before executing the following commands:
```
cd /tmp
git clone https://github.com/usdot-fhwa-stol/carma-cloud.git
wget http://apache.mirrors.lucidnetworks.net/tomcat/tomcat-9/v9.0.34/bin/apache-tomcat-9.0.34.tar.gz && tar -xzf apache-tomcat-9.0.34.tar.gz && mv apache-tomcat-9.0.34 tomcat && rm -rf apache-tomcat-9.0.34.tar.gz
mkdir -p tomcat/webapps/carmacloud/ROOT && mv CARMACloud/web/* tomcat/webapps/carmacloud/ROOT/
find ./CARMACloud/src -name "*.java" > sources.txt && mkdir -p tomcat/webapps/carmacloud/ROOT/WEB-INF/classes
javac -cp tomcat/lib/servlet-api.jar:CARMACloud/lib/commons-compress-1.18.jar:CARMACloud/lib/javax.json.jar -d tomcat/webapps/carmacloud/ROOT/WEB-INF/classes @sources.txt
sed -i '/<\/Engine>/ i \ \ \ \ \  <Host name="carmacloud" appBase="webapps/carmacloud" unpackWARs="false" autoDeploy="false">\n      </Host>' tomcat/conf/server.xml
echo -e '127.0.0.1\tcarmacloud' | sudo -u root tee -a /etc/hosts
mv CARMACloud/lib tomcat/webapps/carmacloud/ROOT/WEB-INF/
touch tomcat/webapps/carmacloud/event.csv
mv CARMACloud/osmbin/rop.csv tomcat/webapps/carmacloud/
mv CARMACloud/osmbin/storm.csv tomcat/webapps/carmacloud/
java -cp tomcat/webapps/carmacloud/ROOT/WEB-INF/classes/:tomcat/lib/servlet-api.jar cc.ws.UserMgr ccadmin admin_testpw > tomcat/webapps/carmacloud/user.csv
gunzip CARMACloud/osmbin/*.gz
mv CARMACloud/osmbin tomcat/webapps/carmacloud/
rm -f sources.txt && rm -rf CARMACloud
sudo -u root mv tomcat /opt/
```
These commands will download the CARMAcloud source code from github, necessary dependencies, and the tomcat webserver. Changes to the tomcat version might be necessary if version 9.0.34 is no longer available on the Apache mirror. Next the java code will be compiled and the .class files will be placed in the correct directory. Tomcat's server.xml file will have the carmacloud host entry inserted in the correct location. Carmacloud will be added to the /etc/hosts file. The java command that runs cc.ws.UserMgr will create the ccadmin user for the system. It is suggested to change to password to something more secure by replacing "admin_testpw" with the desired password in the command.
## Configuration
The Tomcat webserver must be configured to run on the deployment server. Click [here](https://tomcat.apache.org/tomcat-9.0-doc/index.html) to find the documentation for Tomcat. There are many configuration items to considered but two that must be addressed for any deployment are adding the domain name and SSL Certificate to the server.xml file. Proper security measures dealing with file permissions should be taken as well. According to Tomcat's [documentation](https://tomcat.apache.org/tomcat-9.0-doc/security-howto.html#System_Properties) a tomcat user and group should be created in the operating system. The standard configuration for file permissions is to have all Tomcat files owned by root with group tomcat. Owner should have read/write permissions, group should have read permission, and world has no permissions. The exceptions are the logs, temp and work directory that are owned by the tomcat user rather than root. Additional users can be added to CARMAcloud by run the following command and replacing <username> and <password> with the desired user name and password respectively:
```
java -cp tomcat/webapps/carmacloud/ROOT/WEB-INF/classes/:tomcat/lib/servlet-api.jar cc.ws.UserMgr <username> <password> >> /opt/tomcat/webapps/carmacloud/user.csv
```
Additionally, you will need to generate an [access token](https://account.mapbox.com/access-tokens/) from Mapbox, and replace the text \<your access token goes here\> with your access token in the /opt/tomcat/webapps/carmacloud/ROOT/script/map.js file. Once everything is configured for the deployment, run the following command to start the application:
```
sudo -u tomcat /opt/tomcat/bin/catalina.sh start
```

## Contribution
Welcome to the CARMA contributing guide. Please read this guide to learn about our development process, how to propose pull requests and improvements, and how to build and test your changes to this project. [CARMA Contributing Guide](Contributing.md) 

## Code of Conduct 
Please read our [CARMA Code of Conduct](Code_of_Conduct.md) which outlines our expectations for participants within the CARMA community, as well as steps to reporting unacceptable behavior. We are committed to providing a welcoming and inspiring community for all and expect our code of conduct to be honored. Anyone who violates this code of conduct may be banned from the community.

## Attribution
The development team would like to acknowledge the people who have made direct contributions to the design and code in this repository. [CARMA Attribution](ATTRIBUTION.md) 

## License
By contributing to the Federal Highway Administration (FHWA) Connected Automated Research Mobility Applications (CARMA), you agree that your contributions will be licensed under its Apache License 2.0 license. [CARMA License](<docs/License.md>) 

## Contact
Please click on the CARMA logo below to visit the Federal Highway Adminstration(FHWA) CARMA website. For more information, contact CARMA@dot.gov.

[![CARMA Image](docs/image/CARMA_icon2.png)](https://highways.dot.gov/research/research-programs/operations/CARMA)
