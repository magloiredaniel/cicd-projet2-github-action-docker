FROM node:lts-bullseye
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install --omit=dev
COPY . .
ENV PORT=81
EXPOSE 81
CMD [ "node", "index.js" ]