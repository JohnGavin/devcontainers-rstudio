# R version: 4, 4.1, 4.0
ARG VARIANT="4.2"
FROM rocker/tidyverse:${VARIANT}
# Includes packages: devtools tidyverse tidymodels glmnet randomForest xgboost 

# RUN useradd -m -p rstudio rstudio
# RUN useradd -p rstudio rstudio
RUN echo 'rstudio:rstudio' | chpasswd
# RUN echo 'Docker!' | passwd --stdin root 

# Use the [Option] comment to specify true/false arguments that should appear in VS Code UX
#
# [Option] Install zsh
ARG INSTALL_ZSH="true"
# [Option] Upgrade OS packages to their latest versions
ARG UPGRADE_PACKAGES="false"

# Install needed packages and setup non-root user. Use a separate RUN statement to add your own dependencies.
ARG USERNAME=rstudio
ARG USER_UID=1001
ARG USER_GID=$USER_UID
USER $USER_UID:$USER_GID

# USER ${USERNAME}
# USER rstudio:staff

# RUN addgroup --system staff
# RUN adduser --system rstudio --ingroup staff
USER root
RUN usermod -aG sudo ${USERNAME} 
# USER $USER_UID:$USER_GID
# RUN sudo gpasswd -a ${USERNAME} sudo
# RUN sudo usermod -aG staff ${USERNAME} 
# sudo adduser <username> sudo
#RUN sudo usermod -u 1001 runner
#RUN sudo groupmod -g 121 runner
#RUN apt update -y \
#    && apt install -y --no-install-recommends \
#    sudo 
    # && adduser --disabled-password --gecos "" --uid 1000 ${USERNAME} \



COPY library-scripts/*.sh /tmp/library-scripts/
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive 
RUN /bin/bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" "true" "true" 
#RUN apt-get update && export DEBIAN_FRONTEND=noninteractive 
#RUN sudo /bin/bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" "true" "true" 
RUN apt-get -y install \
        python3-pip \
        libgit2-dev \
        libcurl4-openssl-dev \
        libssl-dev \
        libxml2-dev \
        libxt-dev \
    && apt-get autoremove -y && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/* /tmp/library-scripts \
    && python3 -m pip --no-cache-dir install radian \
    && pip --disable-pip-version-check --no-cache-dir install pybryt \
    && pip --disable-pip-version-check --no-cache-dir install pylint \
    && pip --disable-pip-version-check --no-cache-dir install jupyter \
    && pip --disable-pip-version-check --no-cache-dir install datascience \
    #&& pip --disable-pip-version-check --no-cache-dir install otter-grader \
    && pip --disable-pip-version-check --no-cache-dir install numpy \
    && pip --disable-pip-version-check --no-cache-dir install pandas \
    && pip --disable-pip-version-check --no-cache-dir install scipy \
    && pip --disable-pip-version-check --no-cache-dir install folium>=0.9.1 \
    && pip --disable-pip-version-check --no-cache-dir install matplotlib \
    && pip --disable-pip-version-check --no-cache-dir install ipywidgets>=7.0.0 \
    && pip --disable-pip-version-check --no-cache-dir install bqplot \
    && pip --disable-pip-version-check --no-cache-dir install nbinteract>=0.0.12 \
    #&& pip --disable-pip-version-check --no-cache-dir install otter-grader \
    #&& pip --disable-pip-version-check --no-cache-dir install okpy \
    && pip --disable-pip-version-check --no-cache-dir install scikit-learn \
    && install2.r --error --skipinstalled --ncpus -1 \
        languageserver \
        httpgd \
        statip \
        #patchwork \
        #paletteer \
        #here \
        #doParallel \
        #janitor \
        #vip \
        #ranger \
        #palmerpenguins \
        #skimr \
        #nnet \
        #kernlab \
        #plotly \
        #factoextra \
        #cluster \
        #tidymodels \
        #markdown \ 
        #ottr \
    && rm -rf /tmp/downloaded_packages

#RUN groupadd docker 
# RUN usermod -aG sudo ${USERNAME} 
#RUN usermod -aG docker ${USERNAME} 
RUN echo "%sudo   ALL=(ALL:ALL) NOPASSWD:ALL" > /etc/sudoers

USER rstudio

## Install summarytools and load some R package off the bat
RUN R -e "devtools::install_github('https://github.com/dcomtois/summarytools/tree/0-8-9')"
#RUN R -e "library(ottr)"
#RUN R -e "library(here)"
RUN R -e "library(languageserver)"
# RUN installGithub.r ucbds-infra/ottr@stable


# VSCode R Debugger dependency. Install the latest release version from GitHub without using GitHub API.
# See https://github.com/microsoft/vscode-dev-containers/issues/1032
RUN export TAG=$(git ls-remote --tags --refs --sort='version:refname' https://github.com/ManuelHentschel/vscDebugger v\* | tail -n 1 | cut --delimiter='/' --fields=3) \
    && Rscript -e "remotes::install_git('https://github.com/ManuelHentschel/vscDebugger.git', ref = '"${TAG}"', dependencies = FALSE)"

# R Session watcher settings.
# See more details: https://github.com/REditorSupport/vscode-R/wiki/R-Session-watcher
RUN echo 'source(file.path(Sys.getenv("HOME"), ".vscode-R", "init.R"))' >> ${HOME}/.Rprofile

# [Optional] Uncomment this section to install additional OS packages.
# RUN apt-get update \
#     && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>

# [Optional] Uncomment this section to install additional R packages.
# RUN install2.r --error --skipinstalled --ncpus -1 <your-package-list-here>


# [Optional] Uncomment this section to install vscode-jupyter dependencies.
USER root
RUN apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends libzmq3-dev \
    && install2.r --error --skipinstalled --ncpus -1 IRkernel \
    && python3 -m pip --no-cache-dir install jupyter \
    && R --vanilla -s -e 'IRkernel::installspec(user = FALSE)'
USER rstudio


# Expose Rstudio port. Log in with rstudio/rstudio
EXPOSE 8787
# ENV EDITOR_FOCUS_DIR "/workspaces/devcontainers-rstudio"
#        useradd -m $USER -u $USERID
#        && mkdir -p /home/$USER \
#        && chown -R $USER /home/$USER \
#        && chown -R ${USER}:staff /home/$USER

# RUN mkdir -p "$EDITOR_FOCUS_DIR"
# RUN docker run --rm -p 8787:8787 -w $EDITOR_FOCUS_DIR -e USER=vscode -e PASSWORD=vscode rocker/verse


# Expose Jupyter port
EXPOSE 8888

CMD ["/init"] 