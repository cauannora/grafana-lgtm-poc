FROM node:latest

WORKDIR /app

COPY . /app

RUN npm cache clean --force

RUN npm install

EXPOSE 8082

CMD ["npm" ,"run", "start"]