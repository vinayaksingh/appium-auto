# appium-auto

Repo to collect and share learnings about mobile app automation using [Appium](http://appium.io/).

## Why Appium
There are many reasons why a functional test framework can be designed around Appium and some are listed [here](https://stackoverflow.com/questions/28363221/advantages-and-disadvantages-of-appium).

Important ones:
* There are many robust ,neat and easy-to-use automation tools present in the market but with most them there is a requirement to somehow add "foreign" code in the application under test, such that the automation tools can "access" app's properties and perform actions on them. 
Example in MonkeyTalk an "agent" is added while compiling the app code and in case of [TestComplete](https://smartbear.com/product/testcomplete/pricing/) the app needs to be instrumented. So application code and/or developer signing keys/certificates is required to add agents in the application under test.  

   Appium does not need Application code or developer keys/certificates thus no foreign agent is added in the application's code. That means the same ipa(iOS) and apk(Android) can be tested that have been hosted in the App Stores.

* Appium is open-source, thus can be extended, modified and used as it suits your business. Easy to plug-in other popular tools for continuous integration, advance reporting etc.

* Appium supports a lot of programming languages. Appium is designed on [JSONWireProtocol](https://github.com/SeleniumHQ/selenium/wiki/JsonWireProtocol) thus supporting a language just means creating [libraries](http://appium.io/docs/en/about-appium/appium-clients/index.html) that wraps HTTP REST calls to Appium server. 

* Appium supports executing tests in-parallel. With mobile app automation the major pain point is to test the app features/functions across array of devices with different form-factors like screen-sizes and resolutions, operating system versions and flavours, ever evolving mobile device hardwares and so-on. So regression across multiple devices becomes mandatory before app is shipped to end-users. Its very time-efficient if tests can be run in parallel on multiple devices.

* Appium's awesome support for iOS automation. With integration of Facebook's [Web Driver Agent](https://github.com/facebook/WebDriverAgent) Appium can automate iOS sand-boxed functions.
