# Stage 0, "build-stage", based on Node.js, to build and compile the frontend
FROM node:18.17.0 as build-stage

WORKDIR /app

COPY package*.json /app/

RUN npm install

COPY ./ /app/

RUN npm run build

# Stage 1, based on Nginx, to have /assets/bundles only the compiled app, ready for production with Nginx
FROM nginx:1.24.0

COPY --from=build-stage /assets/bundles /usr/share/nginx/html



# Copy the default nginx.conf
COPY --from=build-stage /app/nginx.conf /etc/nginx/conf.d/default.conf