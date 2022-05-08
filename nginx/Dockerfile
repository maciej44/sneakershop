# 2 stage-build makes the size of the final image smaller.
# At the second stage, we copy only the final product of the first stage: static files.
# The node_modules (they can take a lot of space) with dependency files are not in the final image.

# The first stage
# Build React static files
FROM node:10 as builder
WORKDIR /app/storefront
COPY ./saleor-storefront-sneakershop/package*.json ./
RUN npm ci --silent
COPY ./saleor-storefront-sneakershop/ ./
ARG API_URI
ARG SENTRY_DSN
ARG SENTRY_APM
ARG DEMO_MODE
ARG GTM_ID
# ENV API_URI ${API_URI:-http://localhost:8000/graphql/}
RUN API_URI=${API_URI} npm run build


# The second stage
# Copy React static files and start nginx
FROM nginx:stable
# WORKDIR /app
COPY --from=builder /app/storefront/dist/ /usr/share/nginx/html
CMD ["nginx", "-g", "daemon off;"]
