FROM debian:stable

# Get current & install Java, curl
RUN apt-get -y update && apt-get upgrade
RUN apt-get -y install openjdk-11-jdk curl

# Run bungeecord as bungee user
RUN useradd bungee && mkdir -p /home/bungee
RUN usermod -a -G bungee bungee

# add some configuration files
ADD locations.yml /home/bungee/locations.yml
ADD config.yml  /home/bungee/config.yml

# add the bungeecord server
RUN curl -L https://ci.md-5.net/job/BungeeCord/lastSuccessfulBuild/artifact/bootstrap/target/BungeeCord.jar > /home/bungee/BungeeCord.jar
ADD bungeecord.jar.conf /home/bungee/bungeecord.jar.conf

# Add bungeecord modules
RUN mkdir -p /home/bungee/modules
RUN curl -L https://ci.md-5.net/job/BungeeCord/lastSuccessfulBuild/artifact/module/cmd-server/target/cmd_server.jar > /home/bungee/modules/cmd_server.jar 
RUN curl -L https://ci.md-5.net/job/BungeeCord/lastSuccessfulBuild/artifact/module/cmd-alert/target/cmd_alert.jar > /home//bungee/modules/cmd_alert.jar
RUN curl -L https://ci.md-5.net/job/BungeeCord/lastSuccessfulBuild/artifact/module/cmd-find/target/cmd_find.jar > /home/bungee/modules/cmd_find.jar
RUN curl -L https://ci.md-5.net/job/BungeeCord/lastSuccessfulBuild/artifact/module/cmd-list/target/cmd_list.jar > /home/bungee/modules/cmd_list.jar
RUN curl -L https://ci.md-5.net/job/BungeeCord/lastSuccessfulBuild/artifact/module/cmd-send/target/cmd_send.jar > /home/bungee/modules/cmd_send.jar
RUN curl -L https://ci.md-5.net/job/BungeeCord/lastSuccessfulBuild/artifact/module/reconnect-yaml/target/reconnect_yaml.jar > /home/bungee/modules/reconnect_yaml.jar

RUN chown -R bungee:bungee /home/bungee

# Clean up
RUN apt-get --purge -y remove curl && apt-get -y autoremove

USER bungee
WORKDIR "/home/bungee"

EXPOSE 25577

ENTRYPOINT [ "java", "-Xms512M", "-Xmx512M", "-jar", "/home/bungee/BungeeCord.jar" ]
