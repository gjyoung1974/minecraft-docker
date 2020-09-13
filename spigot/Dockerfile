# base layer
FROM debian:stable AS builder

# Get current & install Java, curl
RUN apt-get -y update && apt-get upgrade
RUN apt-get -y install git openjdk-11-jre-headless curl

# Run spigot as spigot user
RUN useradd spigot && mkdir -p /home/spigot
RUN usermod -a -G spigot spigot

# Add spigot files
RUN mkdir -p /server && mkdir -p /server/server/logs

# get spigot buildtools
RUN curl -L https://hub.spigotmc.org/jenkins/job/BuildTools/lastSuccessfulBuild/artifact/target/BuildTools.jar > /server/BuildTools.jar

# compile spigot server
# RUN git config --global --unset core.autocrlf
RUN java -jar /server/BuildTools.jar

# For multi-craft
RUN curl -L http://www.multicraft.org/download/conf?file=spigot.jar.conf > /server/spigot.jar.conf

# rename spigot
RUN mv /spigot-*.jar /spigot.jar

# App layer
FROM debian:stable AS minecraft-server

# Get current & install Java, curl
RUN apt-get -y update && apt-get upgrade
RUN apt-get -y install git openjdk-11-jre-headless curl

# Run spigot as spigot user
RUN useradd spigot && mkdir -p /home/spigot && mkdir -p /spigot/spigot
RUN usermod -a -G spigot spigot

# Add latest spigot jar
COPY --from=builder /spigot.jar /server/server/spigot.jar

# TODO add all of our fave plugins

# Accept the Minecraft EULA
RUN echo 'eula=true' > /server/server/eula.txt

# Add our configuration
ADD *.json /server/server/
ADD *.yml /server/server/
ADD *.properties /server/server/

# Take ownership of assets
RUN chown -R spigot:spigot /server

# Drop user permissions from root to spigot user
USER spigot

# TODO: decouple the config, plugins, world data, etc from the container
WORKDIR "/server/server"

EXPOSE 25565
EXPOSE 25575

ENTRYPOINT [ "java", "-Xms2G", "-Xmx2G", "-XX:+UseG1GC", "-jar", "/server/server/spigot.jar", "nogui" ]
