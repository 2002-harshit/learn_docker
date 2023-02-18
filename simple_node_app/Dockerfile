FROM node

# when you "from" a pre existing image, all the contents(like the whole docker file) of that image is inherited, so when we "from" node, we get underlying OS also which is alpine, which can be seen from node's official docker file.

WORKDIR /app

COPY . /app
# copy requires a source and a destination, COPY src and dest, COPY . /app  means copy everything from my current working directory(in which dockerfile is there) to container's /app.(app inside root/), here COPY . . will also work since we have already specified the current working directory of the container.

RUN npm install
# these commands are executed in the current workdir so /app.

EXPOSE 80
#* because our node app listens on port 80.

#* now we want our app to start
# RUN node server.js
#! but this is wrong because the run command is only executed when the image is built, but we want node server.js to execute every time when our container starts.
CMD ["node","server.js"]
#! if you dont specify the cmd, the cmd of the base image is executed.

