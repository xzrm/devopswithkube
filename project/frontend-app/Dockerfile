FROM node:14-alpine AS builder

RUN apk add --update python3 make g++ \
    && rm -rf /var/cache/apk/*

WORKDIR /app

ENV PATH /app/node_modules/.bin:$PATH

COPY package.json ./

RUN yarn install

COPY . .

RUN yarn build

##############

FROM nginx:1.20.2-alpine

RUN apk add --no-cache bash

RUN rm -rf /etc/nginx/conf.d
COPY conf /etc/nginx

COPY --from=builder /app/build /usr/share/nginx/html/

EXPOSE 3000

WORKDIR /usr/share/nginx/html

#this is relevanet for when there is .env file used
# COPY ./env.sh .
# COPY .env .

#replaces env vars in minified js file
COPY ./env_sub.sh .


RUN chmod +x env_sub.sh 
# RUN chmod +x env.sh

ENV JSFOLDER=/usr/share/nginx/html/static/js/main*.js

CMD ["/bin/bash", "-c", "/usr/share/nginx/html/env_sub.sh && nginx -g \"daemon off;\""]