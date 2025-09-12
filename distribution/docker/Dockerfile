# Use a minimal base image with Java
FROM eclipse-temurin:21-jre

# Set Keycloak version and user/group
ARG KEYCLOAK_VERSION=26.3.0-custom
ENV KEYCLOAK_USER=keycloak
ENV KEYCLOAK_GROUP=keycloak
ENV KEYCLOAK_HOME=/opt/keycloak

# Create user, group, and directories
RUN groupadd -r ${KEYCLOAK_GROUP} && \
    useradd -r -g ${KEYCLOAK_GROUP} ${KEYCLOAK_USER} && \
    mkdir -p ${KEYCLOAK_HOME}

# Copy the server distribution TAR.GZ created by Maven
COPY ./keycloak-${KEYCLOAK_VERSION}.tar.gz /tmp/

# Unpack the archive using tar and set ownership
# The --strip-components=1 flag removes the top-level directory from the archive
RUN tar -xzf /tmp/keycloak-${KEYCLOAK_VERSION}.tar.gz -C ${KEYCLOAK_HOME} --strip-components=1 && \
    rm /tmp/keycloak-${KEYCLOAK_VERSION}.tar.gz && \
    chown -R ${KEYCLOAK_USER}:${KEYCLOAK_GROUP} ${KEYCLOAK_HOME}

# Copy your custom provider (JDBC driver) and set ownership
#COPY postgresql-42.7.7.jar ${KEYCLOAK_HOME}/providers/
#RUN chown ${KEYCLOAK_USER}:${KEYCLOAK_GROUP} ${KEYCLOAK_HOME}/providers/postgresql-42.7.7.jar

# Switch to the keycloak user
USER ${KEYCLOAK_USER}

# --- Build Optimization Step ---
# Run the Keycloak build command.
# This creates an optimized server configuration.
# By not specifying a dialect, you allow Keycloak to auto-detect it at runtime,
# which is the most flexible and robust approach.
RUN ${KEYCLOAK_HOME}/bin/kc.sh build --db=postgres

# Set the default command to start the server
ENTRYPOINT ["/opt/keycloak/bin/kc.sh"]
