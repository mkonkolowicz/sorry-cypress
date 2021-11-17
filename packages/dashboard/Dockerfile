FROM node:14-alpine AS build

WORKDIR /app
COPY packages/api/src/schema ./packages/api/src/schema
COPY packages/dashboard/ ./packages/dashboard
COPY packages/common/ ./packages/common
COPY package.json ./
COPY yarn.lock ./
COPY tsconfig.json ./
# COPY packages/dashboard/nginx/default.conf nginx/conf.d
# COPY packages/dashboard/nginx/nginx.conf nginx/nginx.conf
RUN yarn install --frozen-lockfile
RUN yarn workspace @sorry-cypress/common build
RUN yarn workspace @sorry-cypress/dashboard build

FROM nginx:1-alpine
WORKDIR /usr/share/nginx/html
COPY packages/dashboard/nginx/default.conf /etc/nginx/conf.d
COPY packages/dashboard/nginx/nginx.conf /etc/nginx
COPY packages/dashboard/nginx/default.conf.template /etc/nginx/templates/default.conf.template
COPY packages/dashboard/server/static .
COPY --from=build /app/packages/dashboard/dist .
COPY --from=build /app/packages/dashboard/dist/views/index.ejs index.html
ENV GRAPHQL_SCHEMA_URL "http://localhost:4000"
ENV CI_URL ""
ENV GRAPHQL_CLIENT_CREDENTIALS ""

RUN addgroup -g 2000 2000 && \
adduser -S -u 2000 -G 2000 2000
RUN chown -R 2000:2000 /usr/share/nginx/html
RUN chown -R 2000:2000 /etc/nginx
RUN chown -R 2000:2000 /etc/nginx/templates/default.conf.template
RUN chown -R 2000:2000 /var
RUN chown -R 2000:2000 /var/run

USER 2000

