## React App

This is basic React application display "Hello World" on the screen. This application is used in the Docker Blog to demonstrate to improve the security of Docker images of a Node.js application.

#### Multi-Stage Build

Multistage builds offer a great way to streamline Docker images, making them smaller and more secure. We have taken our [Dockerfile](/Dockerfile) and improved it by using a multi-stage build. This allows us increase the security of our image by removing the build dependencies from the final image. The final image is now much smaller and only contains the files needed to run the application. The image size has been reduced from 1.4GB to 142MB.

## Deploy React app
Step-by-step guide to deploy React app from a GitHub repository using NGINX in a Docker container on an Ubuntu server.
### Step 1: Clone the GitHub Repository
```
git clone https://github.com/abrahimcse/react-app.git
cd react-app
```
### Step 2: Create a Multistage Dockerfile
In the root of project, create a `Dockerfile`

```
vim Dockerfile
```
Here is the new Dockerfile with the multi-stage build:

```Dockerfile
# Stage 1: Build the application
FROM node:21.5-alpine3.18 AS builder

# Set the working directory for the build stage
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the application source code into the container
COPY . .

# Build the application
RUN npm run build

# Stage 2: Create the final image
FROM nginx:1.20

# Set the working directory within the container
WORKDIR /app

# Copy the built application files from the builder stage to the nginx html directory
COPY --from=builder /app/build /usr/share/nginx/html

# Expose port 80 for the web server
EXPOSE 80

# Start nginx in the foreground
CMD ["nginx", "-g", "daemon off;"]
```
Save the file `esc` , `:wq` then `Enter`.

### Step 3: Build and Run the Docker Container

Run the following commands:

- Build the Docker Image
    ```
    docker build -t react-app .
    ```
- Run the Container
    ```
    docker run -d -p 80:80 react-app
    ```
**Result:**

Firstly we build an image with the default Dockerfile with the tag `react-app:` and then we build the image with the multi-stage Dockerfile with the tag `react-app-multi-stage:`. We can see that the image without the multi-stage build is 1.4GB and the image with the multi-stage build is 142MB. 

![](https://github.com/abrahimcse/react-app/blob/main/public/image.png)


