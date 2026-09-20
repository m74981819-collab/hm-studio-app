name: Build Android APK

on:
  push:
    branches: [ main, master ]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Extract Android Source
        run: |
          echo "Finding source zip..."
          ZIP_FILE=$(find . -maxdepth 2 -name "*.zip" | head -n 1)
          if [ -n "$ZIP_FILE" ]; then
            echo "Unzipping $ZIP_FILE..."
            unzip -o "$ZIP_FILE"
          fi
          
          # Find gradlew location
          GRADLEW_PATH=$(find . -name "gradlew" | head -n 1)
          if [ -z "$GRADLEW_PATH" ]; then
            echo "Error: gradlew not found!"
            exit 1
          fi
          
          PROJECT_DIR=$(dirname "$GRADLEW_PATH")
          echo "PROJECT_DIR=$PROJECT_DIR" >> $GITHUB_ENV
          chmod +x "$GRADLEW_PATH"

      - name: Build Debug APK
        run: |
          cd "$PROJECT_DIR"
          ./gradlew assembleDebug --no-daemon --stacktrace

      - name: Upload APK Artifact
        uses: actions/upload-artifact@v4
        with:
          name: HM-Studio-App-APK
          path: '**/build/outputs/apk/debug/*.apk'
          if-no-files-found: error
