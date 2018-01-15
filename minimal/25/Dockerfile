#
# GitLab CI Android Runner
#
#
FROM openjdk:8-slim

ENV ANDROID_BUILD_TOOLS "27.0.3"
ENV API_LEVEL "25"
ENV ANDROID_HOME "/android-sdk"
ENV PATH=$PATH:${ANDROID_HOME}/emulator:${ANDROID_HOME}/tools:${ANDROID_HOME}/tools/bin:${ANDROID_HOME}/platform-tools

# Prepare dependencies
RUN mkdir $ANDROID_HOME \
  && apt-get update --yes \
  && apt-get install --yes wget tar unzip lib32stdc++6 lib32z1 libqt5widgets5 \
  && apt-get clean \
  && rm -fr /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install sdk tools
RUN wget -nv -O android-sdk.zip https://dl.google.com/android/repository/sdk-tools-linux-3859397.zip \
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

# Update platform and build tools
RUN update_sdk  'tools' 'platform-tools' "build-tools;${ANDROID_BUILD_TOOLS}"

# Update SDKs
RUN update_sdk "platforms;android-${API_LEVEL}"

# Update emulators
RUN update_sdk "system-images;android-${API_LEVEL};google_apis;x86"

# Update extra and support
RUN update_sdk "extras;android;m2repository" "extras;google;m2repository" \
  "extras;google;google_play_services" \
  "extras;m2repository;com;android;support;constraint;constraint-layout;1.0.2" \
  "extras;m2repository;com;android;support;constraint;constraint-layout-solver;1.0.2"
  
RUN sdkmanager --update
