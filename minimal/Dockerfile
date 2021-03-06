#
# GitLab CI Android Runner
#
#
FROM ubuntu:16.10

ENV ANDROID_BUILD_TOOLS "25.0.1"
ENV ANDROID_SDK_TOOLS "25.2.4"
ENV ANDROID_HOME "/android-sdk"
ENV PATH=$PATH:${ANDROID_HOME}/tools:${ANDROID_HOME}/platform-tools

# Prepare dependencies
RUN mkdir $ANDROID_HOME \
  && apt-get update \
  && apt-get install -y openjdk-8-jdk wget expect \
  && apt-get install --yes wget tar unzip lib32stdc++6 lib32z1 libqt5widgets5 \
  && apt-get clean \
  && rm -fr /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install sdk tools
RUN wget -O android-sdk.zip https://dl.google.com/android/repository/tools_r${ANDROID_SDK_TOOLS}-linux.zip \
  && unzip -q android-sdk.zip -d $ANDROID_HOME \
  && rm android-sdk.zip

# Add tools from travis
ADD https://raw.githubusercontent.com/travis-ci/travis-cookbooks/ca800a93071a603745a724531c425a41493e70ff/community-cookbooks/android-sdk/files/default/android-wait-for-emulator /usr/local/bin/android-wait-for-emulator
RUN chmod +x /usr/local/bin/android-wait-for-emulator

# Add own tools
COPY update_sdk /usr/local/bin/update_sdk
COPY assure_emulator_awake.sh /usr/local/bin/assure_emulator_awake.sh
RUN chmod +x /usr/local/bin/update_sdk \
  && chmod +x /usr/local/bin/assure_emulator_awake.sh

# Update sdks
RUN update_sdk platform-tools \
  && update_sdk build-tools-${ANDROID_BUILD_TOOLS} \
  && update_sdk android-23 \
  && update_sdk sys-img-armeabi-v7a-google_apis-23 \
  && update_sdk extra-android-m2repository \
  && update_sdk extra-google-m2repository \
  && update_sdk extra-google-google_play_services
