#Layer 1: Telling Docker to use the node:17-alpine image as the base image for the container.
#FROM node:17-alpine

# Layer 2: Telling Docker to create a directory called `client` in the container and set it as the working directory.
#WORKDIR /client

# Layer 3: Copying the package.json file from the root of the project to the `client` directory in the container.
#COPY package.json package-lock.json ./

# Layer 4: Installing the dependencies listed in the package.json file.
#RUN npm install

# Layer 5: Copying all the files from the root of the project to the `client` directory in the container.
#COPY . .

# Layer 6: Telling Docker that the container will listen on port 3000.
#EXPOSE 3000

# Layer 7: Telling Docker to run the `npm start` command when the container is started.
#CMD ["npm", "start"]

# ---- Stage 1: Build the React app ----
FROM node:20-alpine AS build

WORKDIR /client

# Copy only package files first (better layer caching)
COPY package.json package-lock.json ./

# Install exact, reproducible dependencies
RUN npm ci

# Copy the rest of the source code
COPY . .
# Accept the API URL as a build argument (CRA bakes REACT_APP_* vars in at build time)
ARG REACT_APP_API_URL=http://localhost:5000
ENV REACT_APP_API_URL=$REACT_APP_API_URL


# Build the production-optimized static files
RUN npm run build

# ---- Stage 2: Serve the static files with nginx ----
FROM nginx:1.27-alpine

# Copy the built static files from the previous stage into nginx's public folder
COPY --from=build /client/build /usr/share/nginx/html

# nginx listens on port 80 by default
EXPOSE 80

# nginx image already has the correct CMD, no need to override it