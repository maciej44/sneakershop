# 2 stage-build makes the size of the final image smaller.
# At the second stage, we copy only the final product of the first stage: static files.
# The node_modules (they can take a lot of space) with dependency files are not in the final image.

# The first stage
# Build React static files
FROM node:10 as builder
# 1. storefront
WORKDIR /app/storefront
COPY ./saleor-storefront-sneakershop/package*.json ./
RUN npm ci --silent
COPY ./saleor-storefront-sneakershop/ ./
ARG API_URI
ARG SENTRY_DSN
ARG SENTRY_APM
ARG DEMO_MODE
ARG GTM_ID
RUN API_URI=${API_URI} npm run build

# 2. dashboard
WORKDIR /app/dashboard
COPY ./saleor-dashboard/package*.json ./
RUN npm ci --silent
COPY ./saleor-dashboard/ ./
ARG APP_MOUNT_URI
ARG API_URI
ARG STATIC_URL
ENV APP_MOUNT_URI ${APP_MOUNT_URI:-/dashboard/}
ENV STATIC_URL ${STATIC_URL:-/dashboard/}
RUN STATIC_URL=${STATIC_URL} API_URI=${API_URI} APP_MOUNT_URI=${APP_MOUNT_URI} npm run build


# The second stage
# Copy React static files to container and start nginx
FROM nginx:stable
COPY --from=builder /app/storefront/dist/ /usr/share/nginx/html/storefront
COPY --from=builder /app/dashboard/build/dashboard/ /usr/share/nginx/html/dashboard
CMD ["nginx", "-g", "daemon off;"]
