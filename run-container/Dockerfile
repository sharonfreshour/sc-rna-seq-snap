FROM rocker/rstudio:4.4.0

LABEL maintainer="Walid Abu Al-Afia and Antonia Chroni"

# Set environment variables
ENV PATH=/opt/cellranger-8.0.1/bin:${PATH}
ENV LD_LIBRARY_PATH=/opt/cellranger-8.0.1/lib

# Install base dependencies and locales
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        locales \
        aptitude \
        gnupg \
        software-properties-common \
        dirmngr && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install dependencies and configure locale
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen en_US.UTF-8 && \
    update-locale LANG=en_US.UTF-8

# Set locale environment variables
ENV LANG=en_US.UTF-8 \
    LC_ALL=en_US.UTF-8

# Install R and dependencies
RUN apt-get update && \
    apt-get install -y \
        libcurl4-openssl-dev \
        libssl-dev \
        libxml2-dev \
        libcairo2-dev \
        libxt-dev \
        libpng-dev \
        libfreetype6-dev \
        libtiff5-dev \
        libjpeg-dev \
        libgsl-dev \
        librsvg2-dev \
        libnode-dev \
        libv8-dev \
        software-properties-common \
        libharfbuzz-dev \
        librsvg2-dev \
        libproj-dev \
        libbz2-dev \
        liblzma-dev \
        zlib1g-dev \
        libfribidi-dev \
        python3.9 \
        python3-dev \
        cmake \
        bedtools \
        openjdk-8-jre-headless \
        g++ \
        libopenblas-base \
        liblapack3 \
        libgeos-dev \
        pkg-config \
        jags \
        git-all \
        libudunits2-dev \
        libmagick++-dev \
        libgdal-dev \
        libhdf5-dev \
        libpoppler-cpp-dev \
        libfftw3-dev \
        libglpk-dev \
        libgmp-dev \
        libgit2-dev \
        curl \
        build-essential \
        autoconf \
        automake \
        flex \
        bison \
        fastqc \
        gdebi-core && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Remove any existing version of Pandoc (if installed)
RUN apt-get update && apt-get remove -y pandoc

# Install necessary tools for downloading and installing the .deb package
RUN apt-get install -y \
    wget \
    gdebi-core \
    curl \
    libssl-dev \
    libcurl4-openssl-dev \
    zlib1g-dev

# Install pandoc/1.19.2.1 to avoid conflixt with texlive 
# Set the Pandoc version and the URL for the release
ENV PANDOC_VERSION="1.19.2.1"
ENV PANDOC_URL="https://github.com/jgm/pandoc/releases/download/${PANDOC_VERSION}/pandoc-${PANDOC_VERSION}-1-amd64.deb"

# Download and install Pandoc .deb package
RUN wget ${PANDOC_URL} -O /tmp/pandoc.deb \
    && gdebi -n /tmp/pandoc.deb \
    && rm /tmp/pandoc.deb


# Install texlive full version
ENV TEXLIVE_INSTALL_NO_CONTEXT_CACHE=1 \
    NOPERLDOC=1

# Install TexLive prerequisites
RUN apt-get update && \
    apt-get install -y wget unzip tar \
    make fontconfig perl openjdk-8-jre libgetopt-long-descriptive-perl \
    libdigest-perl-md5-perl libncurses5 \
    python3-pygments && \
    rm -rf /var/lib/apt/lists/*

# Install TexLive to the container
RUN wget http://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz && \
    tar xzf install-tl-unx.tar.gz && rm install-tl-unx.tar.gz && \
    cd install-tl* && \
    echo "selected_scheme scheme-full" > install.profile && \
    echo "tlpdbopt_install_docfiles 0" >> install.profile && \
    echo "tlpdbopt_install_srcfiles 0" >> install.profile && \
    echo "tlpdbopt_autobackup 0" >> install.profile && \
    echo "tlpdbopt_sys_bin /usr/bin" >> install.profile && \
    ./install-tl -profile install.profile && cd .. && rm -rf install-tl*

# Symlink TexLive binaries to PATH
RUN /usr/local/texlive/2024/bin/x86_64-linux/tlmgr path add

# Install rsession
RUN wget -P /tmp https://download2.rstudio.org/server/jammy/amd64/rstudio-server-2024.09.1-394-amd64.deb && \
    gdebi -n /tmp/rstudio-server-2024.09.1-394-amd64.deb

# Install CellRanger v8.0.1
# You will need to download the desired version from here: https://www.10xgenomics.com/support/software/cell-ranger/downloads/previous-versions
# Save on your `/tmp/` locally
# You should also replace the link with the one from the most recent download
RUN curl -o /tmp/cellranger-8.0.1.tar.gz "https://cf.10xgenomics.com/releases/cell-exp/cellranger-8.0.1.tar.gz?Expires=1733244151&Key-Pair-Id=APKAI7S6A5RYOXBWRPDA&Signature=Oev2Ml0L0ns0vQRE8ToqCFLGQIJjh~~2dVe1y6C7RvhnSsIViSBTyz1vlKgELLqgo1q8mvb-X1qsEZWXMwVmHzifnO6UseqQxDo2qKbfF9FMwCorTXtBcFiM~irstt2JqKEp9-LJ6zLLAwyXPDJPGvPHcnhp08Wt1S7HN-RWSdWGqbNeFXWjVSZeqG3OooUnB4S3Ykq5aJW9tSUBrCo6s2sR7mC-a0uwf-aawbXSSv3cvnTaAeyuMEFtH7G8QZnOmCCYlNG9~JtomynscwBvf3Hax1IVF8x4kEmCbdaKmBFrPqN8ifVz-FrsnXm3K9Smb2ArkKg6Ur-kIeJ-Q7FK4A__"
RUN tar -xvzf /tmp/cellranger-8.0.1.tar.gz -C /opt

# Install pip for Python 3.9
RUN curl -sS https://bootstrap.pypa.io/get-pip.py | python3

# Set default CRAN mirror
RUN echo "options(repos = c(CRAN = 'https://packagemanager.posit.co/cran/__linux__/jammy/latest'))" >> /usr/local/lib/R/etc/Rprofile.site

# Install Python packages
RUN pip3 install MACS2 leidenalg multiqc

# Enable RStudio Copilot
RUN echo "copilot-enabled=1" >> /etc/rstudio/rsession.conf

# Install necessary system libraries for HDF5 
# The hdf5r package depends on the underlying HDF5 C libraries. 
RUN apt-get update && \
    apt-get install -y libhdf5-dev

# Install R packages
RUN Rscript -e 'install.packages("usethis")' && \
    Rscript -e 'install.packages(c("BiocManager", "devtools", "remotes"))'

RUN Rscript -e 'BiocManager::install(c("miQC", "scater", "scDblFinder", "SingleCellExperiment", "celldex", "SingleR", "infercnv"))'

RUN Rscript -e 'devtools::install_github("igordot/scooter")'
RUN Rscript -e 'devtools::install_github("welch-lab/RcppPlanc")'
RUN Rscript -e 'devtools::install_github("kharchenkolab/numbat")'
RUN Rscript -e 'devtools::install_github("welch-lab/liger")'

#RUN Rscript -e 'install.packages("hdf5r")'
RUN Rscript -e 'install.packages("hdf5r", type = "source")'

#You can also add the following line to your Dockerfile to ensure the library path is set correctly:
ENV LD_LIBRARY_PATH=/path/to/hdf5/libs:$LD_LIBRARY_PATH

RUN Rscript -e 'install.packages(c("clustree", "cowplot", "data.table"))'
RUN Rscript -e 'install.packages(c("flexmix", "flextable", "forcats"))'
RUN Rscript -e 'install.packages(c("fs", "future", "GGally"))'
RUN Rscript -e 'install.packages(c("ggh4x", "ggplot2", "ggpmisc"))'
RUN Rscript -e 'install.packages(c("ggrepel", "ggthemes", "grid"))'
RUN Rscript -e 'install.packages(c("harmony", "igraph", "irlba"))'
RUN Rscript -e 'install.packages(c("knitr", "optparse"))'
RUN Rscript -e 'install.packages(c("patchwork", "purrr", "RColorBrewer"))'
RUN Rscript -e 'install.packages(c("remotes", "reshape2", "rliger"))'
RUN Rscript -e 'install.packages(c("rlist", "R.utils", "SeuratObject"))'
RUN Rscript -e 'install.packages(c("shiny", "SoupX", "stringr"))'
RUN Rscript -e 'install.packages(c("tidytext", "tidyverse", "tinytex", "yaml"))'

# Seurat
RUN Rscript -e 'remotes::install_version("Seurat", "4.4.0", repos = c("https://packagemanager.posit.co/cran/__linux__/jammy/latest"))'

# Set the entrypoint to rserver
ENTRYPOINT ["/init"]
