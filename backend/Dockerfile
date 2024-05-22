# Use the official Node.js image.
FROM node:14

# Create and set the working directory.
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies.
RUN npm install

# Copy the rest of the application files.
COPY . .

# Specify the command to run the application.
CMD ["node", "backend/index.js"]
