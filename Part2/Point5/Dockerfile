FROM ubuntu
RUN apt update
RUN apt install -y cowsay
COPY $COWFILE_DIR/$COWFILE_NAME /usr/games/

ENV WAIT_TIME=3

CMD while read -r line; do /usr/games/cowsay "$line"; sleep $WAIT_TIME; done </usr/games/$COWFILE_NAME

