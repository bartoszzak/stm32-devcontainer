# Base image
ARG VARIANT=bookworm-slim
FROM debian:${VARIANT}
ENV DEBIAN_FRONTEND=noninteractive
ENV LC_ALL=C.UTF-8
ENV LANG=C.UTF-8


# Install dependencies
RUN apt-get update && \
    apt-get install -y git curl wget xz-utils unzip make openocd usbutils

# Set the version of the toolchain
ARG TOOLCHAIN_VERSION=13.2.rel1

# Set the host processor architecture
ARG TARGETARCH
RUN if [ "$TARGETARCH" = "amd64" ]; then export HOST_ARCH=x86_64; \
    else export HOST_ARCH=aarch64; \
    fi && \
# Download and extract the toolchain
    wget https://developer.arm.com/-/media/Files/downloads/gnu/$TOOLCHAIN_VERSION/binrel/arm-gnu-toolchain-$TOOLCHAIN_VERSION-$HOST_ARCH-arm-none-eabi.tar.xz && \
    mkdir /opt/gcc-arm-none-eabi && \
    tar xf arm-gnu-toolchain-$TOOLCHAIN_VERSION-$HOST_ARCH-arm-none-eabi.tar.xz --strip-components=1 -C /opt/gcc-arm-none-eabi

# Add /opt/gcc-arm-none-eabi/bin directory to the PATH environment variable
ENV PATH="/opt/gcc-arm-none-eabi/bin:${PATH}"

# Download and install Python 3.8 from source and libncursesw5 for GDB
ARG PYTHON_VERSION=3.8.18
RUN apt update && \
    apt install -y build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libsqlite3-dev libreadline-dev libffi-dev curl libbz2-dev
RUN cd /usr/src && \
    wget https://www.python.org/ftp/python/$PYTHON_VERSION/Python-$PYTHON_VERSION.tgz && \
    tar xzf Python-$PYTHON_VERSION.tgz && \
    cd Python-$PYTHON_VERSION && \
    ./configure --enable-optimizations && \
    make altinstall
RUN apt install -y libncursesw5

# Download the SVD file from the Keil DFP pack
# Change KEIL_PACK_NAME and SVD_NAME according to your needs
ARG KEIL_PACK_NAME=Keil.STM32F4xx_DFP.2.17.1
ARG SVD_NAME=STM32F4*
RUN wget https://www.keil.com/pack/$KEIL_PACK_NAME.pack
RUN mkdir /opt/keil-stm32-dfp
RUN unzip $KEIL_PACK_NAME.pack -d /opt/keil-stm32-dfp
RUN cp /opt/keil-stm32-dfp/CMSIS/SVD/$SVD_NAME.svd /usr/share/openocd/scripts/target

# Clean up the package lists to free up space
RUN apt-get clean && rm -rf /var/lib/apt/lists/*
