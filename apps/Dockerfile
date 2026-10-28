FROM node:18.20.8-slim
WORKDIR /app 

RUN addgroup -S nonroot \
    && adduser -S nonroot -G nonroot

USER nonroot

RUN npm ci

COPY . .

RUN npm install
CMD ["npm", "start"]