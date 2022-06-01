FROM node:16-alpine3.14

# Install ssh client and git
RUN apk add --no-cache git openssh

# Set Time Zone GMT-3 (São Paulo)
RUN apk add tzdata
RUN cp /usr/share/zoneinfo/Brazil/East /etc/localtime

# add credentials on build
RUN mkdir /root/.ssh/ && chmod 700 /root/.ssh/ && ssh-keyscan bitbucket.org > /root/.ssh/known_hosts
ADD serverKey /root/.ssh/serverKey
ADD serverKey.pub /root/.ssh/serverKey.pub
RUN eval `ssh-agent -s`

# SSH Permisssions
RUN chmod 600 /root/.ssh/serverKey && chmod 600 /root/.ssh/serverKey.pub
RUN  echo "IdentityFile ~/.ssh/serverKey" >> /etc/ssh/ssh_config
RUN cat /root/.ssh/serverKey >> /root/.ssh/known_hosts

# Create app directory
WORKDIR /usr/src/app

# Copy app directory
RUN git clone  git@bitbucket.org:me/app-server.git

# Changing app directory
WORKDIR /usr/src/app/app-server

# Creating temp folder
RUN mkdir temp
RUN chmod 777 temp

# Install app dependencies
RUN npm install

# Run Build
RUN npm run compiler

EXPOSE 3000

# Bundle app source
CMD ["node", "dist/main.js"]
