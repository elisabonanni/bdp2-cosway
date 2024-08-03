This repository contains Dockerfiles to build a Docker images derived from the ubuntu image which has the cowsay program installed in it that generates “ASCII art pictures” of a cow with a message.

### 1.1 Create a new local git repo on VM1

Create a new directory for the exercise:

```
mkdir cowsay
cd cowsay
```

Initialise git in the directory to create a local repo:

```
git init
```

### 1.2 Create the Dockerfile to build an image
Create a new directory called _Part1_:
```
mkdir Part1
cd Part1
```

Using ```vim Dockerfile``` create a Dockerfile with the following lines:

```
FROM ubuntu
RUN apt update
RUN apt install -y cowsay
```

### 1.3 Push the repo on VM1 to bdp2-cowsay GitHub repo
Add the Part1 directory with the Dockerfile to git:

```
git add Part1
```

Commit the changes:
```
git commit -m "Cowsay Dockerfile"
```

The status of git can be checked before and after the commit using ```git status``` to check the changes.

Create on GitHub a new repository called *bdp2-cowsay* and add it as origin.
```
git remote add origin https://github.com/elisabonanni/bdp2-cowsay.git
```

Push the changes commited to the "master branch" of the local repo to the "origin". It will be asked for Username and Password (personal token).
```
git push -u origin master
```

### 1.4 Build the image my-cowsay on VM1 out of the Dockerfile
```
docker build -t my-cowsay .
```

### 1.5 Run the image interactively
The command ```docker run``` is used to run the image, the ```-rm``` flag is used to remove the container while exiting and the ```-it``` flag is added to run it interactively:
```
docker run --rm -it my-cowsay
```

To check that cowsay works:
```
/usr/games/cowsay "Hello BDP2 Students"
```

If everything works fine the output would return:
```
_____________________
< Hello BDP2 Students >
---------------------
       \   ^__^
        \  (oo)\_______
           (__)\       )\/\
               ||----w |
               ||     ||
```

### 2.1 Message to be processed by cowsay from an environment variable
A new directory for this second part has been created:
```
mkdir Part2
```

Then each single point has been executed in a different subdirectory, creating a new Dockerfile and a new image for all of them.

Dockerfile reads the message to be processed by cowsay from an environment variable.

In the directory *~/cowsay/Part2/Point1*, the command ```docker run``` is executed with the -e flag to specify an environment variable MESSAGE.
```
docker run --rm -it -e MESSAGE="Message to be processed from an environment variable" my-cowsay

/usr/games/cowsay $MESSAGE
```

The output:
```
_________________________________
/ Message to be processed from an \
\ environment variable            /
---------------------------------
       \   ^__^
        \  (oo)\_______
           (__)\       )\/\
               ||----w |
               ||     ||
```

### 2.2 Run the image non-interactively
When the my-cowsay container is launched, it runs, prints the cowsay message and exits.

The cowsay command is run from the Dockerfile in the *~/cowsay/Part2/Point2* using the CMD instructions.

```
FROM ubuntu
RUN apt update
RUN apt install -y cowsay

CMD /usr/games/cowsay $MESSAGE
```

The MESSAGE environment variable is specified from the ```docker run``` command:
```
docker run --rm -e MESSAGE="Message which run non-interactively" my-cowsay
```

The output will be:
```
< Message which run non-interactively >
 -------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

### 2.3 Specify a set of messages to be printed by cowsay
These messages should be specified in a file, one line per message. The name and location of the file should be specified by an environment variable. The cowsay command will be executed for each of the contents of each line. The file name is specified with the COWFILE_NAME environment variable, the directory and the location of the file in the VM is specified with the COWFILE_DIR environment variable.

The Dockerfile in the *~/cowsay/Part2/Point3* is now modified as:
```
FROM ubuntu
RUN apt update
RUN apt install -y cowsay
COPY $COWFILE_DIR/$COWFILE_NAME /usr/games/

CMD while read -r line; do /usr/games/cowsay "$line"; done </usr/games/$COWFILE_NAME
```

With the command ```docker run```:
```
docker run --rm -e COWFILE_DIR="~/cowsay/Part2/Point3" -e COWFILE_NAME="set_messages.txt" my-cowsay
```

The output will be like:
```
 _____________
< Buongiorno! >
 -------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
 _______________
< Good Morning! >
 ---------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
 __________
< Bonjour! >
 ----------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
 __
< おはよう!  >
 --
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
 __
< Buen día!  >
 --
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

### 2.4 Specify flags to the cowsay command line through an environment variable.
Try the flag -t to display a tired cow (or -w for the opposite).

The ```FLAG``` environment variable is specified in the Dockerfile in the *~/cowsay/Part2/Point4* directory:
```
FROM ubuntu
RUN apt update
RUN apt install -y cowsay

CMD /usr/games/cowsay $FLAG $MESSAGE
```

The variable can be then specified in the ```docker run``` command:
```
docker run --rm -e FLAG='-t' -e MESSAGE="I'm tired!" my-cowsay
```

The output will be:
```
____________
< I'm tired! >
------------
       \   ^__^
        \  (--)\_______
           (__)\       )\/\
               ||----w |
               ||     ||
```

For the -w flag instead:
```
docker run --rm -e FLAG='-w' -e MESSAGE="I'm euphoric!" my-cowsay
```

And the output:
```
_______________
< I'm euphoric! >
---------------
       \   ^__^
        \  (OO)\_______
           (__)\       )\/\
               ||----w |
               ||     ||
```

### 2.5 Specify a set of messages to be printed by cowsay with a wait time.
There can be a wait time between the processing of each line of the file. This wait time should also be specified via an environment variable. The Dockerfile in the *~/cowsay/Part2/Point5* directory will be:

```
FROM ubuntu
RUN apt update
RUN apt install -y cowsay
COPY $COWFILE_DIR/$COWFILE_NAME /usr/games/

ENV WAIT_TIME=3

CMD while read -r line; do /usr/games/cowsay "$line"; sleep $WAIT_TIME; done </usr/games/$COWFILE_NAME
```

The ```docker run``` command is now:
```
docker run --rm -e COWFILE_DIR="~/cowsay/Part2/Point5" -e COWFILE_NAME="set_messages.txt" my-cowsay
```
