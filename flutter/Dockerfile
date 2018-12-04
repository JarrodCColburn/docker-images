FROM debian:stretch
LABEL maintainer="jcourtlandcolburn@gmail.com"   

RUN apt-get update -y
RUN apt-get upgrade -y

# Install basics
RUN apt-get install -y --no-install-recommends \
  git \
  wget \
  curl \
  zip \
  unzip \
  ca-certificates \
  gnupg

# Install the rest of the dependencies.
RUN apt-get install -y --no-install-recommends \
  locales \
  golang \
  ruby \
  ruby-dev \
  nodejs \
  lib32stdc++6 \
  libstdc++6 \
  libglu1-mesa \
  build-essential \
  default-jdk-headless

ARG ANDROID_SDK_BUILD=4333796
ARG ANDROID_API=28
ARG ANDROID_SDK_VERSION=28.0.3
ARG GRADLE_VERSION=5.0
ARG FLUTTER_VERSION=0.11.13-beta

# Install the Android SDK Dependency.
ARG ANDROID_SDK_URL="https://dl.google.com/android/repository/sdk-tools-linux-${ANDROID_SDK_BUILD}.zip"
ENV ANDROID_TOOLS_ROOT=/opt/android_sdk
RUN mkdir -p ${ANDROID_TOOLS_ROOT}
RUN mkdir -p ~/.android
# Silence warning.
RUN touch ~/.android/repositories.cfg
ARG ANDROID_SDK_ARCHIVE=${ANDROID_TOOLS_ROOT}/sdk-tools-linux-${ANDROID_SDK_BUILD}.zip
ADD ${ANDROID_SDK_URL} ${ANDROID_SDK_ARCHIVE}
RUN unzip -q -d ${ANDROID_TOOLS_ROOT} ${ANDROID_SDK_ARCHIVE}

RUN rm ${ANDROID_SDK_ARCHIVE}
ENV PATH=${ANDROID_TOOLS_ROOT}/tools:${PATH}
ENV PATH=${ANDROID_TOOLS_ROOT}/tools/bin:${PATH}
RUN yes | sdkmanager --licenses > /dev/null
RUN sdkmanager "tools" "build-tools;${ANDROID_SDK_VERSION}" "platforms;android-${ANDROID_API}" "platform-tools" "extras;android;m2repository" "extras;google;m2repository" "patcher;v4"
ENV PATH=${PATH}:${ANDROID_TOOLS_ROOT}/build-tools/${ANDROID_SDK_VERSION}

# Setup gradle
ENV GRADLE_ROOT=/opt/gradle
RUN mkdir -p ${GRADLE_ROOT}
ARG GRADLE_ARCHIVE=${GRADLE_ROOT}/gradle-${GRADLE_VERSION}.zip
ARG GRADLE_URL="http://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip"
ADD ${GRADLE_URL} ${GRADLE_ARCHIVE}

RUN unzip -q -d ${GRADLE_ROOT} ${GRADLE_ARCHIVE}
ENV PATH=${GRADLE_ROOT}/bin:$PATH

# Setup flutter
ARG FLUTTER_URL="https://storage.googleapis.com/flutter_infra/releases/beta/linux/flutter_linux_v${FLUTTER_VERSION}.tar.xz"
ARG FLUTTER_ARCHIVE=/tmp/flutter_linux_v${FLUTTER_VERSION}.tar.gz

ADD ${FLUTTER_URL} ${FLUTTER_ARCHIVE}
RUN tar xf ${FLUTTER_ARCHIVE} -C /opt/ 

RUN rm ${FLUTTER_ARCHIVE}

ENV PATH=${PATH}:/opt/flutter/bin
RUN flutter doctor

# Set locale to en_US
RUN locale-gen en_US "en_US.UTF-8" && dpkg-reconfigure locales
ENV LANG en_US.UTF-8

RUN gem install bundler
# Install fastlane
RUN gem install fastlane -NV