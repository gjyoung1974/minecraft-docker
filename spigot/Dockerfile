FROM debian:stable

# Get current & install Java, curl
RUN apt-get -y update && apt-get upgrade
RUN apt-get -y install git openjdk-11-jre-headless curl

# Run spigot as spigot user
RUN useradd spigot && mkdir -p /home/spigot
RUN usermod -a -G spigot spigot

# Add spigot files
RUN mkdir -p /home/spigot/Spigot_Server && mkdir -p /logs

# get spigot buildtools
RUN curl -L https://hub.spigotmc.org/jenkins/job/BuildTools/lastSuccessfulBuild/artifact/target/BuildTools.jar > /home/spigot/Spigot_Server/BuildTools.jar

# compile spigot server
# RUN git config --global --unset core.autocrlf
RUN java -jar /home/spigot/Spigot_Server/BuildTools.jar

# For multi-craft
RUN curl -L http://www.multicraft.org/download/conf?file=spigot.jar.conf > /home/spigot/Spigot_Server/spigot.jar.conf

# Accept the Minecraft EULA
RUN echo 'eula=true' > /home/spigot/Spigot_Server/eula.txt

# Add our configuration
ADD *.json /home/spigot/Spigot_Server/
ADD *.yml /home/spigot/Spigot_Server/
ADD *.properties /home/spigot/Spigot_Server/
RUN mv /spigot-*.jar /spigot.jar

# Clean up from the build of spigot.jar
RUN rm -r -f /BuildData && rm -r -f Spigot && rm BuildTools.log.txt && rm -r -f apache-maven-3.6.0
RUN apt-get --purge -y remove curl git && apt-get -y autoremove

# Take ownership of assets
RUN chown -R spigot:spigot /home/spigot/Spigot_Server && chown -R spigot:spigot /logs

# Drop user permissions from root to spigot user
USER spigot

# TODO: decouple the config, plugins, world data, etc from the container
WORKDIR "/home/spigot/Spigot_Server"

EXPOSE 25565

ENTRYPOINT [ "java", "-Xms2G", "-Xmx2G", "-XX:+UseG1GC", "-jar", "/spigot.jar", "nogui" ]
