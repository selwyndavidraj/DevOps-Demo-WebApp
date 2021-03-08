FROM tomcat:8-jre8  
MAINTAINER lpradel

RUN echo "export JAVA_OPTS=\"-Dapp.env=staging\"" > /usr/local/tomcat/bin/setenv.sh  
COPY ./ProdWebapp.war /usr/local/tomcat/webapps/ProdWebapp.war

CMD ["catalina.sh", "run"] 
