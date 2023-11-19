FROM openjdk:8 AS BUILD_IMAGE
RUN apt update && apt install maven -y
RUN git clone -b vp-docker https://github.com/imranvisualpath/vprofile-repo.git
RUN cd vprofile-repo && mvn install

FROM tomcat:8-jre11

RUN rm -rf /usr/local/tomcat/webapps/*

COPY --from=BUILD_IMAGE vprofile-repo/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

EXPOSE 8080




# FROM openjdk:11 as BUILD_STAGE
# WORKDIR /myArtifact
# RUN apt update && apt install -y maven
# COPY . .
# RUN mvn clean install


# FROM tomcat:9-jre11
# RUN pwd
# RUN rm -rf /var/lib/tomcat/webapps/*
# COPY --from=BUILD_STAGE /myArtifact/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
# EXPOSE 8080
# CMD ["catalina.sh", "run"]
# CMD ["catalina.sh", "run"]