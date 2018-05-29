This repo exists to demonstrate that https://github.com/react-community/react-native-maps/pull/2096 breaks the react-native-maps build.

The project was created as follows:

The following steps were performed to test the build behavior:

(You can click on each line item to verify the changes in each step. I have not modified the .gitignore file to remove intermediate build files.)

1.  [Create the project.](commit/936d311)


```
create-react-native-app RNMapsGradleRepro
cd RNMapsGradleRepro
yarn eject
```

2.  [Attempt building - successful.](commit/62d60fa)


```
yarn android
```

3.  [Add react-native-maps dependency.](commit/bcb741f)


```
yarn add react-native-maps
```

4.  [Ran `react-native link`](commit/b08f546)


```
react-native link
git checkout -- ios/ # remove irrelevant iOS changes (also broken)
```

5.  [Attempt building - **unsuccessful**](commit/b08f546)


```
yarn android
```

This is the output from `./gradlew installDebug`:

```
yarn run v1.6.0
(node:44100) [DEP0005] DeprecationWarning: Buffer() is deprecated due to security and usability issues. Please use the Buffer.alloc(), Buffer.allocUnsafe(), or Buffer.from() methods instead.
$ react-native run-android
Scanning folders for symlinks in /Users/harry/Downloads/RNMapsGradleRepro/node_modules (11ms)
(node:44101) ExperimentalWarning: The fs.promises API is experimental
JS server already running.
Building and installing the app on the device (cd android && ./gradlew installDebug)...

FAILURE: Build failed with an exception.

* Where:
Build file '/Users/harry/Downloads/RNMapsGradleRepro/node_modules/react-native-maps/lib/android/build.gradle' line: 46

* What went wrong:
A problem occurred evaluating project ':react-native-maps'.
> Could not find method compileOnly() for arguments [com.facebook.react:react-native:+] on object of type org.gradle.api.internal.artifacts.dsl.dependencies.DefaultDependencyHandler.

* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output.

BUILD FAILED

Total time: 10.168 secs
Could not install the app on the device, read the error above for details.
Make sure you have an Android emulator running or a device connected and have
set up your Android development environment:
https://facebook.github.io/react-native/docs/getting-started.html
✨  Done in 13.20s.
```

6.  [Replace `react-native-maps` dependency with one that reverts #2096.](commit/5054d02)

I manually edited `package.json` to point to this commit: https://github.com/mlisik/react-native-maps/commit/0705ba1c5820f453bcec29c8227fd0bd1e93171f

This step could just as easily have been done using [patch-package](https://npm.im/patch-package), but this was quicker.

7.  [Attempt building - **successful**](commit/5054d02)

Reverting the pull request fixes the build.

**Note:** This project does not follow the rest of the installation instructions, such as adding a Google Maps API key, or actually rendering a map.
