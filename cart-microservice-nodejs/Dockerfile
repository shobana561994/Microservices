# Use a specific version of the Node.js image
FROM node:14

# Set the working directory in the container
WORKDIR /var/www

# Copy package.json and package-lock.json for installing dependencies
COPY package*.json ./

# Install application dependencies
RUN npm install

# Copy the rest of your application code
COPY . .

# Expose the port the application will run on
EXPOSE 1004

# Command to run the application
CMD ["node", "index.js"]
