FROM docker.m.daocloud.io/library/node:20-alpine AS build

WORKDIR /app

# Install dependencies
COPY package.json  ./
RUN npm install --verbose

# Build the application
COPY . .
RUN npm run build --verbose

# ====================================
# Production stage
FROM docker.m.daocloud.io/library/node:20-alpine AS release

WORKDIR /app

# Copy built assets from the build stage
COPY --from=build /app/dist ./dist
COPY --from=build /app/node_modules ./node_modules
COPY package*.json ./

# Set environment to production
ENV NODE_ENV=production

# Expose the port the app runs on
EXPOSE 3000

# Start the application
CMD ["npm", "start"]