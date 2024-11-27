# Use a specific OpenJDK version with Alpine Linux
FROM openjdk:8u151-jdk-alpine3.7

# Expose the application port (ensure it matches your Spring Boot app's server port)
EXPOSE 8070

# Set environment variables for the application directory
ENV APP_HOME /usr/src/app

# Create the application directory (optional but recommended for clarity)
RUN mkdir -p $APP_HOME

# Copy the built JAR file into the container (ensure this matches your actual JAR location)
COPY target/shopping-cart-0.0.1-SNAPSHOT.jar $APP_HOME/app.jar

# Set the working directory
WORKDIR $APP_HOME

# Define the command to run the application
ENTRYPOINT ["java", "-jar", "app.jar"]
