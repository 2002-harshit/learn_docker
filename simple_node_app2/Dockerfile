FROM node:latest
#* we definitely need node for the app

WORKDIR /app
#* setting  up the workdir in the container  as /app

COPY package.json .
#! why did we copy separately, since we know docker caches different layers, so this layer might be used in some other docker image

RUN npm install

COPY . .

EXPOSE 80

VOLUME [ "/app/feedback" ]
#* we dont want a volume for temp, beacuse it is temporary!!, so no persistence needed. 

CMD ["node","server.js"]


  