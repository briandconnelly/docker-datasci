# BUILD: docker build --rm -t briandconnelly/datasci-base .

FROM python:3

LABEL maintainer="Brian Connelly <x@bconnelly.net>"
LABEL description="Base image for data science and analytics workflows using Python and R"

ARG user=datasci
ARG DEBIAN_PKGS="bash-completion git libmagick++-dev libsecret-1-dev locales lmodern neovim pandoc texlive-fonts-recommended texlive-latex-base texlive-latex-extra tmux"
ARG PYTHON_PKGS="black boto3 cryptography flake8 future numpy pandas psycopg2 pytz pyyaml rpy2 scipy six"
ARG R_PKGS="assertthat aws.s3 boot broom Cairo caret cli clipr config crayon DBI dbplot dbplyr devtools gganimate ggplot2 ggrepel ggridges glue here httr jsonlite knitr leaflet leaflet.extras lintr lubridate magick magrittr packrat purrr readr remotes reprex RMariaDB rmarkdown RPostgres scales stringr tidyverse viridis whisker xml2 yaml"

ENV LANGUAGE en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LC_ALL en_US.UTF-8
ENV LC_CTYPE en_US.UTF-8
ENV LC_MESSAGES en_US.UTF-8

RUN echo "deb http://cran.rstudio.com/bin/linux/debian stretch-cran35/" >> /etc/apt/sources.list \
  && apt-key adv --no-tty --keyserver ipv4.pool.sks-keyservers.net --recv-key "0xad5f960a256a04af" \
  && apt-get update -yqq \
  && apt-get upgrade -yqq \
  ## Install additional Debian packages ------------------------
  && apt-get install -yqq --no-install-recommends ca-certificates locales ${DEBIAN_PKGS} \
  && echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
  && locale-gen en_US.utf8 \
  && /usr/sbin/update-locale LANG=en_US.UTF-8 \
  ## Install R environment -------------------------------------
  && apt-get install -y --no-install-recommends -t stretch-cran35 r-base r-base-dev r-recommended \
  && mkdir -p /usr/local/lib/R/etc \
  && echo "options(repos = c(CRAN = 'https://cran.rstudio.com/'), download.file.method = 'libcurl')" >> /usr/local/lib/R/etc/Rprofile.site \
  && Rscript -e "install.packages(c('littler', 'docopt'), clean = TRUE)" \
  && ln -s /usr/local/lib/R/site-library/littler/examples/install2.r /usr/local/bin/install2.r \
  && ln -s /usr/local/lib/R/site-library/littler/examples/installGithub.r /usr/local/bin/installGithub.r \
  && ln -s /usr/local/lib/R/site-library/littler/bin/r /usr/local/bin/r \
  && if [ -n "${R_PKGS}" ]; then install2.r --error ${R_PKGS}; fi \
  ## Install Python packages -----------------------------------
  && pip install -U pip setuptools wheel \
  && if [ -n "${PYTHON_PKGS}" ]; then pip install --no-cache-dir ${PYTHON_PKGS}; fi \
  ## Add a non-root user and add to staff group ----------------
  && useradd -md /datasci ${user} \
  && adduser ${user} staff \
  ## Clean up --------------------------------------------------
  && apt-get autoremove -y \
  && apt-get autoclean -y \
  && rm -rf \
    /var/lib/apt/lists/* \
    /tmp/* \
    /var/tmp/*

USER ${user}
WORKDIR /datasci

CMD ["python"]
