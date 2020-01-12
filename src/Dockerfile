FROM node:12-alpine

ADD package.json /tmp/package.json
RUN cd /tmp && npm ci
RUN mkdir -p /opt/app && cp -a /tmp/node_modules /opt/app/

EXPOSE 3001

CMD ["node", "server.js"]
