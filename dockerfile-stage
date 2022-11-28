FROM node as builder 

WORKDIR /home/node/app

COPY package*.json ./

RUN npm install

COPY . .

RUN npx tsc

CMD node build/index.js

USER node