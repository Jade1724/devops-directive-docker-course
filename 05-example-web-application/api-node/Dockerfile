# Better practice to specify exact version rather than 'latest' or not specifying tag which defaults to 'latest'
FROM node:19.6-alpine

# Make the Docker file system more organized
WORKDIR /usr/src/app

# Set node environment to production
ENV NODE_ENV production

# Separate the layer to copy dependency list to and install to utilize caching
COPY package*.json ./

# npm clean install, only install exact version specified in lock file. 
# only install production dependencies, not dev dependencies.
# specify cache space to store node modules to avoid download from the public registry/
RUN --mount=type=cache,target=/usr/src/app/.npm \
  npm set cache /usr/src/app/.npm && \
  npm ci --only=production

# Run as a non root user to give less permissions. node image has a node user.
USER node

# Only copy source file into Docker working directory.
# Assign owner and group to the source directory to node user. 
COPY --chown=node:node ./src .

# This does not do anything to container config. 
# This is a documentation for users to expose this port when running the container
EXPOSE 3000
# Production server run command
CMD ["node", "index.js"]