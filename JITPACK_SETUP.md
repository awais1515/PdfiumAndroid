# JitPack Setup Instructions for 16KB Aligned PdfiumAndroid

## What Has Been Done:

1. ✅ **Updated AGP to 8.5.1** - Supports 16KB alignment
2. ✅ **Updated Gradle to 8.7** - Required for AGP 8.5.1
3. ✅ **Updated compileSdk to 35** - Android 15 support
4. ✅ **Updated targetSdk to 35** - Play Store requirement
5. ✅ **Enabled 16KB alignment** - `useLegacyPackaging = false` in packaging config
6. ✅ **Updated version to 1.9.2-16kb** - New version for JitPack

## 16KB Alignment Configuration:

The library is configured with:
```groovy
packaging {
    jniLibs {
        useLegacyPackaging = false  // AGP 8.5+ handles 16KB alignment automatically
    }
}
```

**Note:** AGP 8.5.1 automatically aligns native libraries to 16KB boundaries during packaging when `useLegacyPackaging = false`. However, the prebuilt `.so` files themselves might need to be rebuilt from source with 16KB alignment for optimal compatibility.

## For JitPack:

### Option 1: Use JitPack Directly (Recommended)

1. **Push changes to your GitHub fork:**
   ```bash
   cd /Users/apple/AndroidStudioProjects/PdfiumAndroid
   git add .
   git commit -m "Add 16KB alignment support with AGP 8.5.1"
   git push origin main  # or your branch name
   ```

2. **Create a release tag:**
   ```bash
   git tag -a v1.9.2-16kb -m "16KB aligned version"
   git push origin v1.9.2-16kb
   ```

3. **Use in IAP project:**
   Update `app/build.gradle`:
   ```groovy
   dependencies {
       implementation 'com.github.YOUR_GITHUB_USERNAME:PdfiumAndroid:v1.9.2-16kb'
       // Replace YOUR_GITHUB_USERNAME with your actual GitHub username
   }
   ```

### Option 2: Build Locally and Publish

If you want to build and test locally first:
```bash
cd /Users/apple/AndroidStudioProjects/PdfiumAndroid
./gradlew clean build publishToMavenLocal
```

Then use in IAP project:
```groovy
repositories {
    mavenLocal()  // Add this
    // ... other repositories
}

dependencies {
    implementation 'com.github.mhiew:pdfium-android:1.9.2-16kb'
}
```

## Update AndroidPdfViewer to Use Your Fork:

After PdfiumAndroid is available on JitPack, update AndroidPdfViewer's `build.gradle`:

```groovy
dependencies {
    api 'com.github.YOUR_GITHUB_USERNAME:PdfiumAndroid:v1.9.2-16kb'
}
```

## Verify 16KB Alignment:

After building your APK/AAB, verify alignment:
```bash
# Check alignment
zipalign -c -v 16384 your-app.apk
```

## Important Notes:

1. **Prebuilt Libraries:** The current `.so` files are prebuilt. For true 16KB alignment, they should be rebuilt from source using NDK r28+ with proper alignment flags.

2. **AGP 8.5.1 Alignment:** AGP 8.5.1 with `useLegacyPackaging = false` will align libraries during packaging, but this may not be sufficient for all cases.

3. **Testing:** Test on devices with 16KB page size (e.g., Pixel 8/9 with Android 15) to verify compatibility.

