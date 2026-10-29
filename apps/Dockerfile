FROM node:18.20.8-slim
WORKDIR /app 
COPY . .
RUN addgroup -S nonroot \
    && adduser -S nonroot -G nonroot

USER nonroot
RUN npm ci



RUN npm install
CMD ["npm", "start"]