# gitlab-ci-android
This Docker image contains the Android SDK, Android Debug Bridge (adb) and most common packages necessary for building Android apps in a CI tool like GitLab CI. Make sure your CI environment's caching works as expected, this greatly improves the build time, especially if you use multiple build jobs.

A `.gitlab-ci.yml` with caching of your project's dependencies would look like this:

```
image: alefaure/gitlab-ci-android

stages:
- build
- test

before_script:
- export GRADLE_USER_HOME=$(pwd)/.gradle
- chmod +x ./gradlew

cache:
  key: ${CI_PROJECT_ID}
  paths:
  - .gradle/

build:
  stage: build
  script:
  - ./gradlew assembleDebug
  artifacts:
    paths:
    - app/build/outputs/apk/app-debug.apk

instrumentedUnitTest:
  stage: test
  script:
  - adb connect {android_vm_ip}
  - ./gradlew connectedAndroidTest
  artifacts:
    paths:
    - app/build/reports/*
```