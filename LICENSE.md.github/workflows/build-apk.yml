name: Build Android APK

on:
  push:
    branches: [ main, master ]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: '17'

      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: 8.11.1

      - name: Build Debug APK
        working-directory: ./android-app
        run: gradle assembleDebug --no-daemon

      - name: Upload Debug APK
        uses: actions/upload-artifact@v4
        with:
          name: HMStudio-Debug-APK
          path: android-app/app/build/outputs/apk/debug/*.apk
          retention-days: 14
