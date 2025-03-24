FROM node:18-slim

# add curl for healthcheck
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl tini && \
    # What is tini??-----> tini is used to Properly Handle Signals like SIGTERM
    # How do you know curl is required?-----> You should have communication with the DEV team.
    
    rm -rf /var/lib/apt/lists/*
    # This step above is to reduce the image size.
    #It is commonly used in Dockerfiles to reduce the image size by removing package lists 
    # that are no longer needed after installing software.

WORKDIR /usr/local/app

# have nodemon available for local dev use (file watching)
RUN npm install -g nodemon

COPY package*.json ./

RUN npm ci && \
 npm cache clean --force && \
 mv /usr/local/app/node_modules /node_modules

COPY . .

ENV PORT=80
EXPOSE 80

ENTRYPOINT ["/usr/bin/tini", "--"]
CMD ["node", "server.js"]
