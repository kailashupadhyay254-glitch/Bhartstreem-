name: Build Social Media App APK

on:
  push:
    branches: [ "main", "master" ]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Repository
      uses: actions/checkout@v4

    - name: Set up Node.js
      uses: actions/setup-node@v4
      with:
        node-version: 18
        cache: 'npm'

    - name: Install Dependencies
      run: |
        npm install --legacy-peer-deps || npm install

    - name: Set up Java Development Kit (JDK 17)
      uses: actions/setup-java@v4
      with:
        java-version: '17'
        distribution: 'temurin'

    - name: Setup Android SDK
      uses: android-actions/setup-android@v3

    - name: Make Gradlew Executable
      run: |
        if [ -f "android/gradlew" ]; then
          chmod +x android/gradlew
        elif [ -f "gradlew" ]; then
          chmod +x gradlew
        fi

    - name: Build Android APK
      run: |
        if [ -d "android" ]; then
          cd android && ./gradlew assembleRelease --no-daemon
        else
          ./gradlew assembleRelease --no-daemon
        fi

    - name: Upload Social Media App APK
      uses: actions/upload-artifact@v4
      with:
        name: Bharatstream-SocialMedia-App
        path: |
          **/build/outputs/apk/release/*.apk
          **/build/outputs/apk/debug/*.apk
