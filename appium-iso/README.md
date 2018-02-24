# appium-ios

Appium uses Facebook's [Web Driver Agent](https://github.com/facebook/WebDriverAgent) for iOS automation.  
iOS automation requires Appium server running on a Mac machine because of XCode dependency and required command line tools.
 
### Install Appium on Mac 
1. Appium needs Node.js, to work with WebDriver JSON wire protocol. So we need to install Node first.  
   ``brew install node``  
   :exclamation: Do not installed node with `sudo`
2. Now install Appium via npm (package manager)
   ``npm install -g appium``   
   a. While installing appium , npm may ask you to use sudo for this kind error.  
   b. Instead of using `sudo` the *working directory* of npm can be [changed](https://docs.npmjs.com/getting-started/fixing-npm-permissions) such that appium gets installed to non-sudo location.
3. Install  *appium-doctor* to verify if we are ready with all the dependencies to automate iOS application.  
   ``npm install -g appium-doctor``  
    Use following commands to check with doctor.  
    ``appium-doctor --ios``

### Install Dependencies
1. Install *Carthage* dependency manager for working with Facebook’s WebDriver Agent.  
   ``brew install carthage``
2. Install *libimobiledevice*  
   ``brew install libimobiledevice --HEAD``
3. Install *ideviceinstaller*   
   ``brew install ideviceinstaller`` _only works for ios 9_
4. Install XCode (use the latest version). After installation XCode should be at */Applications/Xcode.app*
5. Install *ios-deploy*  
   ``npm install -g ios-deploy`` _to work with ios 10 and later_  
   
   :raised_hands: Check with the Doctor.

### Prepare Appium  
Diligently follow [steps](https://github.com/appium/appium-xcuitest-driver/blob/master/docs/real-device-config.md) for setting up Appium for automation on real devices.  
Some helpful notes complementing above "steps":  
* The *WebDriverAgent* XCode project can be found in *appium* installation path.  
   Use ``which appium`` to get appium installation location. If nothing is displayed then go to npm installation path and search for ``<npm_installation>/node_modules/appium/node_modules/appium-xcuitest-driver/WebDriverAgent``
* Inside *WebDriverAgent* directory run ``./Scripts/bootstrap.sh -d`` to download all the packages to build WebDriverAgent.
* Open *WebDriverAgent.xcodeproj* in XCode and create/use provisioning profile to build *WebDriverAgentLib* , *WebDriverAgentRunner* and *IntegrationApp* successfully.
* Once XCode builds are working fine.
  - Connect mobile device to Wi-Fi network same to which the Mac machine is connected. Try to ``ping`` the mobile IP from Mac machine.
  - Connect mobile device via USB to the Mac machine
  - Use ``instruments -s devices`` to know UDID of the connected mobile device. (iTunes can also be used for getting the UDID)
  - Below command is used to test if WebDriverAgent is successfully working on the connected mobile device.  
     ``xcodebuild -project WebDriverAgent.xcodeproj -scheme WebDriverAgentRunner -destination 'id=<udid>' test``
* Some errors that may be observed:
  - Inadequate entitlements or its profile has not been explicitly trusted by the user
    ```
    2018-02-24 13:26:31.290 xcodebuild[20982:4858431] Error Domain=com.apple.platform.iphoneos Code=-12 "Unable to launch com.apple.test.WebDriverAgentRunner-Runner" UserInfo={NSLocalizedDescription=Unable to launch com.apple.test.WebDriverAgentRunner-Runner, NSUnderlyingError=0x7fbbddc412c0 {Error Domain=DTXMessage Code=1 "(null)" UserInfo={DTXExceptionKey=The operation couldn’t be completed. Unable to launch com.apple.test.WebDriverAgentRunner-Runner because it has an invalid code signature, inadequate entitlements or its profile has not been explicitly trusted by the user. : Failed to launch process with bundle identifier 'com.apple.test.WebDriverAgentRunner-Runner'}}}
    2018-02-24 13:26:31.290 xcodebuild[20982:4858431] Error Domain=IDETestOperationsObserverErrorDomain Code=6 "Early unexpected exit, operation never finished bootstrapping - no restart will be attempted" UserInfo={NSLocalizedDescription=Early unexpected exit, operation never finished bootstrapping - no restart will be attempted}
    ```
    This has to be done explicitly on the mobile device.
    ``Settings -> General -> Profiles & Device Management -> <Developer App> -> Verify App``
    
  - Couldn’t be loaded because it is damaged or missing necessary resources
    ```
    Running tests...
    2018-02-24 13:29:20.364988+0530 WebDriverAgentRunner-Runner[6056:1962599] The bundle “<package_name>” couldn’t be loaded because it is damaged or missing necessary resources. Try reinstalling the bundle.

    ```
    1. Happens when code signing is not done properly. Please go through this [issue](https://github.com/appium/appium/issues/8058) .
    2. If signing is proper, then there may be a possibility of limited access permissions in the `appium-xcuitest-driver/WebDriverAgent` directory.  
       The only way to overcome this is to re-install Appium [without sudo][Install Appium on Mac] via npm
       ``npm uninstall -g appium``
       ``npm install -g appium``
     
   - Successful WebDriverAgentRunner `test` started but at wrong Port
     ```
     2018-02-24 18:21:02.551462+0530 WebDriverAgentRunner-Runner[6181:2013019] Running tests...
     2018-02-24 18:21:04.824303+0530 WebDriverAgentRunner-Runner[6181:2013019] Continuing to run tests in the background with task ID 1
     Test Suite 'All tests' started at 2018-02-24 18:21:05.634
     Test Suite 'WebDriverAgentRunner.xctest' started at 2018-02-24 18:21:05.637
     Test Suite 'UITestingUITests' started at 2018-02-24 18:21:05.637
     Test Case '-[UITestingUITests testRunner]' started.
        t =     0.01s Start Test at 2018-02-24 18:21:05.648
        t =     0.01s Set Up
     2018-02-24 18:21:05.711092+0530 WebDriverAgentRunner-Runner[6181:2013019] Built at Feb 24 2018 18:18:45
     2018-02-24 18:21:05.771647+0530 WebDriverAgentRunner-Runner[6181:2013019] ServerURLHere->http://<MobileIP>:0<-ServerURLHere
     ```
     
     The WebDriverAgentRunner should be not started at `http://<MobileIP>:0` but `http://<MobileIP>:8100`.  
     Set the port in env before starting the test. More in [docs](https://appium.io/docs/en/advanced-concepts/wda-custom-server/).  
     ``export USE_PORT=8100``
     
* Once WebDriverAgentRunner tests are working fine, finally check with curl command.  
   ``curl -X GET -H "Content-Type: application/json;charset=UTF-8, accept: application/json" http://<Mobile Device IP>:8100/status``
   
   Should return something like this:
   ```
    {
      "value" : {
        "state" : "success",
        "os" : {
          "name" : "iOS",
          "version" : "10.3.3",
          "sdkVersion" : "11.0"
        },
        "ios" : {
          "simulatorVersion" : "10.3.3",
          "ip" : "192.168.43.111"
        },
        "build" : {
          "time" : "Feb 24 2018 18:18:47"
        }
      },
      "sessionId" : "ABB239D1-FC06-4543-8E89-EB98841AC386",
      "status" : 0
    }
   ```
   
   Please bear in mind its better to do WebDriverAgentRunner tests first before using appium [clients](http://appium.io/docs/en/about-appium/appium-clients/index.html) for writing app functional tests.
       
