# You can pick any Debian/Ubuntu-based image. 😊
FROM mcr.microsoft.com/dotnet/sdk:6.0

# [Optional] Install zsh
ARG INSTALL_ZSH="true"
# [Optional] Upgrade OS packages to their latest versions
ARG UPGRADE_PACKAGES="false"

# Install needed packages and setup non-root user. Use a separate RUN statement to add your own dependencies.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
COPY library-scripts/*.sh /tmp/library-scripts/
RUN bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" "true" "true"

# cd into the user directory, download and unzip the Azure DevOps agent
RUN cd /home/vscode && mkdir azure-pipelines && cd azure-pipelines

# input Azure DevOps agent arguments
ARG ARCH="x64"
ARG AGENT_VERSION="2.214.1"

RUN cd /home/vscode/azure-pipelines \
    && curl -O -L https://vstsagentpackage.azureedge.net/agent/${AGENT_VERSION}/vsts-agent-linux-${ARCH}-${AGENT_VERSION}.tar.gz \
    && tar xzf /home/vscode/azure-pipelines/vsts-agent-linux-${ARCH}-${AGENT_VERSION}.tar.gz \
    && /home/vscode/azure-pipelines/bin/installdependencies.sh

# copy over the start.sh script
COPY library-scripts/start.sh /home/vscode/azure-pipelines/start.sh

# Apply ownership of home folder
RUN chown -R vscode ~vscode

# make the script executable
RUN chmod +x /home/vscode/azure-pipelines/start.sh

# install mono
RUN apt-get update && \
    apt-get install -y apt-transport-https dirmngr gnupg ca-certificates

RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF

RUN echo 'deb https://download.mono-project.com/repo/debian stable-buster main' | tee /etc/apt/sources.list.d/mono-official-stable.list

RUN apt-get update && \
    apt-get install -y mono-devel

# Clean up
RUN rm -rf /var/lib/apt/lists/* /tmp/library-scripts