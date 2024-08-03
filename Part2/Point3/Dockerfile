FROM ubuntu
RUN apt update
RUN apt install -y cowsay
COPY $COWFILE_DIR/$COWFILE_NAME /usr/games/
CMD while read -r line; do /usr/games/cowsay "$line"; done </usr/games/$COWFILE_NAME

