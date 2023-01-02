FROM node:16-alpine AS deps
WORKDIR /app

COPY package.json yarn.lock* package-lock.json* pnpm-lock.yaml* ./

# Install dependencies
RUN apk add --no-cache --virtual .gyp \
  libc6-compat make python3 g++ \
  && yarn \
  && apk del .gyp

###############################
### NOT COMPATILBLE WITH NX ###
###############################
# Left commented out but can be used on non-nx project.

# # Rebuild the source code only when needed
# FROM node:16-alpine AS builder
# WORKDIR /app
# COPY --from=deps /app/node_modules ./node_modules
# # .next folder contains everything necessary to start
# COPY ./dist/apps/demo/* ./

# ENV NEXT_TELEMETRY_DISABLED 1
# RUN yarn build

# Production image, copy all the files and run next
FROM node:16-alpine AS runner
WORKDIR /app

ENV NODE_ENV production
ENV NEXT_TELEMETRY_DISABLED 1

COPY --from=deps /app/node_modules ./node_modules
# Automatically leverage output traces to reduce image size
# https://nextjs.org/docs/advanced-features/output-file-tracing
# COPY --from=builder  /app/.next/standalone ./
# COPY --from=builder  /app/.next/static ./.next/static
COPY ./dist/apps/demo/.next ./.next
COPY ./dist/apps/demo/public ./public
COPY ./dist/apps/demo/next.config.js ./
COPY ./dist/apps/demo/package.json ./
COPY ./dist/apps/demo/with-nx.js ./

# RUN yarn
CMD yarn next start
