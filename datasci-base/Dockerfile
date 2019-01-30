# BUILD: docker build --rm -t briandconnelly/datasci-base .

FROM python:3

LABEL maintainer="Brian Connelly <x@bconnelly.net>"
LABEL description="Base image for data science and analytics workflows using Python and R"

ARG user=datasci

ENV LANGUAGE en_US.UTF-8 \
    LANG en_US.UTF-8 \
    LC_ALL en_US.UTF-8 \
    LC_CTYPE en_US.UTF-8 \
    LC_MESSAGES en_US.UTF-8

COPY packages-apt.txt packages-python.txt packages-r.txt jranke.asc /tmp/

RUN echo "deb http://cran.rstudio.com/bin/linux/debian stretch-cran35/" >> /etc/apt/sources.list \
  && apt-key add /tmp/jranke.asc \
  && apt-get update -yqq \
  && apt-get upgrade -yqq \
  ## Install additional Debian packages ------------------------
  && xargs -a /tmp/packages-apt.txt apt-get install -yqq --no-install-recommends \
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
  && xargs -a /tmp/packages-r.txt install2.r --deps TRUE --error \
  ## Install Python packages -----------------------------------
  && pip install -U pip setuptools wheel \
  && pip install --no-cache-dir -r /tmp/packages-python.txt \
  ## Add a non-root user and add to staff group ----------------
  && useradd -md /datasci ${user} \
  && adduser ${user} staff \
  ## Clean up --------------------------------------------------
  && rm -rf /tmp/* \
  && apt-get autoremove -y \
  && apt-get autoclean -y
  && rm -rf /var/lib/apt/lists/*

USER ${user}
WORKDIR /datasci

CMD ["python"]
