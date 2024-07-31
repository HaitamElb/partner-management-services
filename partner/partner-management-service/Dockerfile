FROM openjdk:11

# Build arguments
ARG spring_config_label
ARG active_profile
ARG spring_config_url
ARG iam_adapter_url

# Environment variables
ENV active_profile_env=${active_profile}
ENV spring_config_label_env=${spring_config_label}
ENV spring_config_url_env=${spring_config_url}
ENV iam_adapter_url_env=${iam_adapter_url}

# User creation arguments
ARG container_user=mosip
ARG container_user_group=mosip
ARG container_user_uid=1001
ARG container_user_gid=1001

# Install packages and create user
RUN apt-get -y update \
    && apt-get install -y unzip wget \
    && groupadd -g ${container_user_gid} ${container_user_group} \
    && useradd -u ${container_user_uid} -g ${container_user_group} -s /bin/sh -m ${container_user}

# Set working directory
WORKDIR /home/${container_user}

# Create a directory for additional JARs
RUN mkdir -p /home/${container_user}/additional_jars/
ENV loader_path=/home/${container_user}/additional_jars/

# Copy application JAR
COPY ./target/partner-management-service-*.jar partner-management-service.jar

# Change permissions
RUN chown -R ${container_user}:${container_user} /home/${container_user}

# Switch to container user
USER ${container_user_uid}:${container_user_gid}

EXPOSE 8109

# Updated CMD to download auth adapter and use it
# CMD wget -q --show-progress "${iam_adapter_url_env}" -O ${loader_path}/kernel-auth-adapter.jar; \
CMD   java -jar -Dloader.path="${loader_path}" -Dspring.cloud.config.label="${spring_config_label_env}" -Dspring.profiles.active="${active_profile_env}" -Dspring.cloud.config.uri="${spring_config_url_env}" partner-management-service.jar