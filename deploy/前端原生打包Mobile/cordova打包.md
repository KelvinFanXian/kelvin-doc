## cordova

- https://segmentfault.com/a/1190000022407419
- https://cordova.apache.org/docs/en/11.x/guide/cli/index.html





## sdkmanager

- https://stackoverflow.com/questions/53151859/cordova-install-android-targets-not-installed-using-sdkmanager

```bash
sdkmanager --list
sdkmanager --install "platforms;android-27"
sdkmanager 'build-tools;30.0.3'
```



----

## android

```bash
cordova create hello com.example.hello HelloWorld
cd hello
cordova platform add android --save
cordova platform ls
cordova requirements
cordova build android

# test
cordova emulate android
cordova run android
```



## ios

```bash
cd cordova-project
cp -r /Users/xianfan/pisen/pisen-scm/PinShengMobileShop/dist/* .
cordova build ios

# xcode to open
open ./platforms/ios/HelloWorld.xcworkspace/
```

