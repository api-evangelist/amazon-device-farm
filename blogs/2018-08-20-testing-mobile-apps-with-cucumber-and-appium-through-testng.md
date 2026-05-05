---
title: "Testing mobile apps with Cucumber and Appium through TestNG on AWS Device Farm"
url: "https://aws.amazon.com/blogs/mobile/testing-mobile-apps-with-cucumber-and-appium-through-testng-on-aws-device-farm/"
date: "Mon, 20 Aug 2018 16:05:50 +0000"
author: "Adrian Hall"
feed_url: "https://aws.amazon.com/blogs/mobile/category/mobile-services/aws-device-farm/feed/"
---
<p>Mobile testing frameworks have matured in functionality to support the innumerable features of modern apps. In this blog post, we’re going to dive into how to set up a local development environment for Android app development, and then use <a href="http://appium.io/" rel="noopener" target="_blank">Appium</a> to perceive our sample app from a test perspective. Then, we’re going to show how you can use the device-interactive abilities of Appium and business-development utilities of <a href="https://cucumber.io/" rel="noopener" target="_blank">Cucumber</a> within a traditional Java <a href="https://testng.org/doc/index.html" rel="noopener" target="_blank">TestNG</a> testing environment. These tools will let you produce in-depth functional tests for mobile apps. Finally, we’ll demonstrate how you can use <a href="https://aws.amazon.com/device-farm/" rel="noopener" target="_blank">AWS Device Farm</a> to upload and run your tests across a variety of real mobile devices. This shows you how different hardware can affect the true end-user experience of your app.</p> 
<h3>Background</h3> 
<p>Functional testing on mobile devices has been a notoriously hard task to automate ever since smartphones first rose to popularity. Over time, Google and Apple, the two companies behind the Android and iOS mobile operating systems, each released their own low-level interfaces for working with elements on a device’s screen during a test. Using <a href="https://developer.android.com/training/testing/ui-automator" rel="noopener" target="_blank">Google’s UIAutomator</a> interface or <a href="https://developer.apple.com/documentation/xctest" rel="noopener" target="_blank">Apple’s XCTest</a> interface, you can now send commands to devices to ask about what the device is displaying, and also to tell the device to click a certain place on the display. Through these black-box, user-like interfaces, you can perform true functional testing on iOS and Android apps.</p> 
<p>Appium acts as a high-level layer of abstraction on top of these interfaces to enable the simplest test cases for developers. Appium has summed up all the complexities and nuances of UIAutomator and XCTest into a clean and simple API for querying and interacting with the UI elements of devices.</p> 
<p>A great advantage of this approach is that the Appium APIs for both Android and iOS are almost identical. So, if you’re testing the same app on Android and iOS, your tests for the two will share most of their code. This reduces development time and frustration.&nbsp;Appium doesn’t act as a&nbsp;“testing framework”&nbsp;per se, but rather as a framework for UI interaction. This is where a tried-and-true testing framework like Java’s TestNG comes into the picture.</p> 
<p>Using Java TestNG test suites, cases, and methods, it’s possible to interact with a device using Appium. You can perform standard TestNG testing techniques on the results of those interactions. If you want to see if a certain UI element exists on the screen properly, just query the element with Appium, and put the result of the query in a TestNG assertion! By combining the power of Appium and TestNG, you’re able to write clean and elegant functional tests for both Android and iOS apps.</p> 
<p>One additional testing innovation that we’ll be going over is the Cucumber framework. Cumbersome test cases and suites can overwhelm developers, and lose their relation to real business needs and behavioral specifications. Cucumber comes into the picture as a way to tie together natural language specifications to code test cases. This means that you can take the TestNG test that checks things about our app with Appium, and tie this test’s input and output to complete sentences of specifications. Testing output has never been cleaner and easier to parse!</p> 
<p>Additionally, these tests are ready for automation right out of the box. By using a platform like Device Farm, you can deploy your tests to automatically run on hundreds of different devices, with dozens of different operating system versions—to see exactly how your functional tests perform under different hardware conditions.</p> 
<h3>Getting started</h3> 
<p>For this workflow, you’ll be using Device Farm’s sample Android app and tests to explore the functionality of Appium, Cucumber, and TestNG. This app contains many of the standard UI elements for Android. You’ll develop your test locally by using an Android emulator, and Appium’s GUI-based app and Inspector. This is a pretty standard workflow for developing Android tests before deploying them to a cloud-based testing service like Device Farm.</p> 
<p>Here are the prerequisites for this test development workflow:</p> 
<ul> 
 <li><a href="https://developer.android.com/studio/#downloads" rel="noopener" target="_blank">Android Studio</a></li> 
 <li><a href="https://github.com/appium/appium-desktop/releases/tag/v1.5.0" rel="noopener" target="_blank">Appium</a></li> 
 <li><a href="https://www.baeldung.com/install-maven-on-windows-linux-mac" rel="noopener" target="_blank">Maven</a></li> 
 <li><a href="https://github.com/aws-samples/aws-device-farm-sample-app-for-android" rel="noopener" target="_blank">Device Farm’s sample Android app</a> and <a href="https://github.com/aws-samples/aws-device-farm-appium-cucumber-tests-for-sample-app" rel="noopener" target="_blank">sample Appium / Cucumber / TestNG tests</a></li> 
</ul> 
<h3>Working with projects and emulators in Android Studio</h3> 
<p>After you’ve downloaded and installed these on your local machine, start by opening up the Device Farm sample project in Android Studio. For this workflow, we assume that you’re familiar with Android Studio projects and emulators. The sample project requires a couple of dependencies that you can download through the SDK Manager. You want to have an Android Platform installed that matches your emulator’s platform, and you need the Android SDK Build Tools version 26.0.2. You’re going to use a Nexus 4 device emulator with platform version 22. You should be able to build and run the app on the emulator:</p> 
<p><img alt="" class="aligncenter wp-image-1833" height="358" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_1.png" width="300" /></p> 
<h3>Getting the Appium Inspector up and running</h3> 
<p>Open up the Appium desktop application and see if you can control the emulator. Opening the app for the first time brings up a screen like the following:</p> 
<p><img alt="" class="aligncenter size-full wp-image-1834" height="702" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_2.png" width="1169" /></p> 
<p>Choose <strong>Start Server v1.7.2</strong> to start the Appium listener service.</p> 
<p><img alt="" class="aligncenter size-full wp-image-1862" height="230" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_3.png" width="657" /></p> 
<p>This console shows any logs that Appium is generating. If you were to send some commands to Appium at its default server address&nbsp;<tt>http://0.0.0.0:4723</tt>, these commands would be logged to the console.</p> 
<p>Next, enter the Appium Inspector view to inspect the elements that are on the emulator’s screen right now. If you want to write Appium tests that interact with an Android device, you need to know how Appium sees these elements. The Inspector view is the best way to do this. It makes the test writing process more seamless by removing any guesswork.&nbsp;Choose the magnifying glass in the top-right corner to get started.</p> 
<p><img alt="" class="aligncenter wp-image-1836 size-full" height="627" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_4.png" width="983" /></p> 
<p>In this view, before you can inspect the device, you need to tell Appium a bit about the device you’re performing testing on, and the app you’re testing. You do this by sending a set of&nbsp;“desired capabilities” to the Appium server. This is good practice, because you’re going to need to set up the same desired capabilities later on in your test code. There’s a link at the bottom left that goes to the documentation about desired capabilities. For now, these are the desired capabilities that you’ll be using with your emulator:</p> 
<p><img alt="" class="aligncenter wp-image-1837 size-full" height="609" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_5.png" width="1232" /></p> 
<p>Set the&nbsp;<tt>platformName</tt> capability to&nbsp;<tt>Android</tt>,&nbsp;and set <tt>deviceName</tt> capability to <tt>Android Emulator</tt>. You need to do a bit of research for the last three values. You can get the first two values, <tt>appPackage</tt> and <tt>appActivity</tt>, from the app itself. It’s easy to find these values in the <tt>AndroidManifest.xml</tt> file of the source code of our app.</p> 
<p><img alt="" class="aligncenter size-full wp-image-1838" height="576" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_6.png" width="855" /></p> 
<p>The&nbsp;<tt>appPackage</tt> value is the root namespace for the application, and is located on line 2:</p> 
<div class="hide-language"> 
 <pre><code class="lang-text">package="com.amazonaws.devicefarm.android.referenceapp"</code></pre> 
</div> 
<p>The&nbsp;<tt>appActivity</tt> value is the activity that’s created when the app first starts. You can see this on lines 16–25.&nbsp; It’s identified with an <tt>intent-filter</tt> that includes <tt>android.intent.action.MAIN</tt>:</p> 
<div class="hide-language"> 
 <pre><code class="lang-xml">&lt;activity
            android:name="com.amazonaws.devicefarm.android.referenceapp.Activities.MainActivity"
            android:label="@string/app_name"
            android:launchMode="singleTop"
            android:screenOrientation="portrait"&gt;
            &lt;intent-filter&gt;
                &lt;action android:name="android.intent.action.MAIN"/&gt;
                &lt;category android:name="android.intent.category.LAUNCHER"/&gt;
            &lt;/intent-filter&gt;
        &lt;/activity&gt;</code></pre> 
</div> 
<p>If you don’t have access to the source code, you can get the values from a compiled app using the adb tool.</p> 
<p>Finally, you need the UUID for the device (in this case, an Android emulator). You can find this string by running&nbsp;<tt>adb devices</tt> in the terminal of Android Studio:</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">$ adb devices
List of devices attached
adb server is out of date.  killing...
* daemon started successfully *
emulator-5554   device</code></pre> 
</div> 
<p>The string <tt>emulator-5554</tt> is the UUID for the device.</p> 
<p>Enter all five values into the desired capabilities, and then save these capabilities with the&nbsp;<strong>Save as…</strong> button to make testing easier in the future. Choose&nbsp;<strong>Start Session</strong> to start the Appium Inspector.</p> 
<p><img alt="" class="aligncenter size-full wp-image-1839" height="682" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_7.png" width="1683" /></p> 
<p>If everything went ok, you should see a hierarchy of elements that make up the screen. When writing automation tests for devices, we typically refer to this type of data as the&nbsp;<i>source</i> or&nbsp;<i>dump</i> of the screen of the device. This isn’t literally the source code of the app, but rather this is analogous to the source of an HTML page when it finishes loading in the web browser.</p> 
<p>The element structure here is pretty legible. There are elements called&nbsp;<tt>android.widget.FrameLayout</tt> that represent the frame of an app,&nbsp;elements called <tt>android.widget.TextView</tt> that represent a block of text,&nbsp;elements called <tt>android.widget.ImageButton</tt> that represent a clickable image, and so on. If you’re working on an app with many custom UI elements defined in the app’s source code, you’ll see some elements that have a different class name than&nbsp;<tt>android.widget</tt>. By choosing these elements, you can see more diagnostic information in the <strong>Selected Element</strong> tab, on the right.</p> 
<p><img alt="" class="aligncenter size-full wp-image-1840" height="694" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_8.png" width="2060" /></p> 
<p>The data in the&nbsp;<strong>Find by Selector</strong> table and the <strong>Attribute Value</strong> table is the trick to writing effective Appium tests. These data points allow you to select our app’s element in the Java Appium test by&nbsp;<i>finding it by</i> a certain key-value pair, which the element matches. The two most common keys used by test writers are the&nbsp;“id” and “accessibility id” in the first table. The&nbsp;“text”,&nbsp;“class”, “resource-id”, and&nbsp;“content-desc” keys from the second table are also commonly used.</p> 
<p>Now that you’ve gone over the element hierarchy of Appium, you can look at what Appium code looks like that (1) references these elements and (2) interacts with them. The building blocks of your TestNG and Cucumber tests look like this:</p> 
<div class="hide-language"> 
 <pre><code class="lang-java">appiumDriver.findElementByAccessibilityId("ReferenceApp").click();</code></pre> 
</div> 
<h3>Writing TestNG tests that use Appium elements and Cucumber specifications</h3> 
<p>Now, let’s open up our sample tests in an integrated development environment (IDE)—preferably one with built-in support and highlighting for Maven projects, such as <a href="https://www.jetbrains.com/idea/" rel="noopener" target="_blank">JetBrains IntelliJ</a>.&nbsp;Maven is the Java build tool that you’ll use to install and bundle all of your dependencies. There are a few key settings in the Maven project that allow you to run tests in your desired environment. These settings are all preconfigured in our sample tests, but let’s go over them anyway, so that you can make sure that any tests you write are properly configured.</p> 
<p>The first thing to do is to make sure that the following line is contained towards the top of your pom.xml file:</p> 
<div class="hide-language"> 
 <pre><code class="lang-xml">&lt;packaging&gt;jar&lt;/packaging&gt;</code></pre> 
</div> 
<p>This line means that you’ll be building your Maven project into a single compressed JAR output file. This is necessary for Device Farm, but also makes running Appium tests simpler as well. For the same reason, make sure the following <tt>maven-jar</tt> plugin is in the plugins list:</p> 
<div class="hide-language"> 
 <pre><code class="lang-xml">&lt;plugin&gt;
  &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
  &lt;artifactId&gt;maven-jar-plugin&lt;/artifactId&gt;
  &lt;version&gt;2.6&lt;/version&gt;
  &lt;executions&gt;
    &lt;execution&gt;
      &lt;goals&gt;
        &lt;goal&gt;test-jar&lt;/goal&gt;
      &lt;/goals&gt;
    &lt;/execution&gt;
  &lt;/executions&gt;
&lt;/plugin&gt;</code></pre> 
</div> 
<p>Also, make sure that these dependencies are in the <tt>pom.xml</tt>:</p> 
<div class="hide-language"> 
 <pre><code class="lang-xml">&lt;dependencies&gt;
            &lt;dependency&gt;
                &lt;groupId&gt;org.testng&lt;/groupId&gt;
                &lt;artifactId&gt;testng&lt;/artifactId&gt;
                &lt;version&gt;6.11&lt;/version&gt;
                &lt;scope&gt;test&lt;/scope&gt;
            &lt;/dependency&gt;
            &lt;dependency&gt;
                &lt;groupId&gt;io.appium&lt;/groupId&gt;
                &lt;artifactId&gt;java-client&lt;/artifactId&gt;
                &lt;version&gt;4.1.2&lt;/version&gt;
            &lt;/dependency&gt;
        &lt;/dependencies&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;info.cukes&lt;/groupId&gt;
            &lt;artifactId&gt;cucumber-java&lt;/artifactId&gt;
            &lt;version&gt;1.2.5&lt;/version&gt;
            &lt;scope&gt;test&lt;/scope&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;info.cukes&lt;/groupId&gt;
            &lt;artifactId&gt;cucumber-testng&lt;/artifactId&gt;
            &lt;version&gt;1.2.4&lt;/version&gt;
        &lt;/dependency&gt;</code></pre> 
</div> 
<p>The first dependency makes sure that Appium’s client software is included with your build, the second is for TestNG, the third is for Cucumber, and the last is for Cucumber’s TestNG plugin. Next, make sure that the following plugin is also in the plugins list:</p> 
<div class="hide-language"> 
 <pre><code class="lang-xml">&lt;plugin&gt;
  &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
  &lt;artifactId&gt;maven-dependency-plugin&lt;/artifactId&gt;
  &lt;version&gt;2.10&lt;/version&gt;
  &lt;executions&gt;
    &lt;execution&gt;
      &lt;id&gt;copy-dependencies&lt;/id&gt;
      &lt;phase&gt;package&lt;/phase&gt;
      &lt;goals&gt;
        &lt;goal&gt;copy-dependencies&lt;/goal&gt;
      &lt;/goals&gt;
      &lt;configuration&gt;
        &lt;outputDirectory&gt;${project.build.directory}/dependency-jars/&lt;/outputDirectory&gt;
      &lt;/configuration&gt;
    &lt;/execution&gt;
  &lt;/executions&gt;
&lt;/plugin&gt;</code></pre> 
</div> 
<p>This plugin makes sure that all of the dependencies for your project get included with the resultant JAR file.&nbsp;Check that the following plugin is included as well. This plugin is the assembly tool to parse your instruction file:</p> 
<div class="hide-language"> 
 <pre><code class="lang-xml">&lt;plugin&gt;
  &lt;artifactId&gt;maven-assembly-plugin&lt;/artifactId&gt;
  &lt;version&gt;2.5.4&lt;/version&gt;
  &lt;executions&gt;
    &lt;execution&gt;
      &lt;phase&gt;package&lt;/phase&gt;
      &lt;goals&gt;
        &lt;goal&gt;single&lt;/goal&gt;
      &lt;/goals&gt;
      &lt;configuration&gt;
        &lt;finalName&gt;zip-with-dependencies&lt;/finalName&gt;
        &lt;appendAssemblyId&gt;false&lt;/appendAssemblyId&gt;
        &lt;descriptors&gt;
          &lt;descriptor&gt;src/main/assembly/zip.xml&lt;/descriptor&gt;
        &lt;/descriptors&gt;
      &lt;/configuration&gt;
    &lt;/execution&gt;
  &lt;/executions&gt;
&lt;/plugin&gt;</code></pre> 
</div> 
<p>Then, make sure that the following instruction file is saved to&nbsp;<tt>src/main/assembly/zip.xml</tt>:</p> 
<div class="hide-language"> 
 <pre><code class="lang-xml">&lt;assembly
    xmlns="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.0 http://maven.apache.org/xsd/assembly-1.1.0.xsd"&gt;
  &lt;id&gt;zip&lt;/id&gt;
  &lt;formats&gt;
    &lt;format&gt;zip&lt;/format&gt;
  &lt;/formats&gt;
  &lt;includeBaseDirectory&gt;false&lt;/includeBaseDirectory&gt;
  &lt;fileSets&gt;
    &lt;fileSet&gt;
      &lt;directory&gt;${project.build.directory}&lt;/directory&gt;
      &lt;outputDirectory&gt;./&lt;/outputDirectory&gt;
      &lt;includes&gt;
        &lt;include&gt;*.jar&lt;/include&gt;
      &lt;/includes&gt;
    &lt;/fileSet&gt;
    &lt;fileSet&gt;
      &lt;directory&gt;${project.build.directory}&lt;/directory&gt;
      &lt;outputDirectory&gt;./&lt;/outputDirectory&gt;
      &lt;includes&gt;
        &lt;include&gt;/dependency-jars/&lt;/include&gt;
      &lt;/includes&gt;
    &lt;/fileSet&gt;
  &lt;/fileSets&gt;
&lt;/assembly&gt;</code></pre> 
</div> 
<p>Now, you’re done setting up the project.</p> 
<p>Finally, we’re going to set one more setting to make sure that the test connects to your emulator properly. Remember those&nbsp;“desired capabilities” that you entered earlier to view elements in the Appium Inspector? You need to set those same capabilities in your test. This is a step that’s only needed for local development. You don’t need these settings when you run your tests on Device Farm.&nbsp; Make sure you return here to comment out the relevant lines before you upload the tests to Device Farm.</p> 
<p>Let’s look at the source code for the test to edit its capabilities. Go to <tt>src/test/java/Tests/AbstractBaseTest/TestBase.java</tt>, and look at lines 85–89:</p> 
<p><img alt="" class="aligncenter size-full wp-image-1841" height="546" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_9.png" width="1097" /></p> 
<p>Do the capabilities look right in your sample tests? If not, modify them to look just like they did in the Appium Inspector so that you can connect to your emulator or device properly.</p> 
<p>Now, let’s look at some real Appium code—in this case, for testing the login page.&nbsp; This is located in <tt>src/test/java/Pages/LoginPage.java</tt>:</p> 
<p><img alt="" class="aligncenter size-full wp-image-1842" height="1109" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_10.png" width="789" /></p> 
<p>Our example test structure adheres to <a href="https://github.com/SeleniumHQ/selenium/wiki/PageObjects" rel="noopener" target="_blank">Appium’s pagination model for testing</a>. In this model, you separate the code for querying a page in an app with Appium from testing the results of that query with Cucumber/TestNG. In <tt>src/test/java/Tests/AbstractBaseTest/TestBase.java</tt>, there’s an example of a login page that uses Appium to find elements on the page and perform the login process.</p> 
<p>See lines 58–68 of the login function for the best look at how Appium does its magic. You can see that the code goes through the process of choosing a user name field, filling it in with the text of the user name, choosing a password field, filling it in with the text of the password, and then choosing a login button. Each field and button corresponds to a real element on the screen of the app, just like you saw in the Appium Inspector.</p> 
<p>In the login button definition from lines 31–32, you can see that the login button is found by using its Appium <tt>id</tt> selector. The following screenshot shows how to get that selector using Appium’s Inspector:</p> 
<p><img alt="" class="aligncenter size-full wp-image-1843" height="867" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_11.png" width="1711" /></p> 
<p>Well look at that—it shows the exact same id from the test in the Appium Inspector! So, if you wanted to write your own test on your own app, it’s not too hard to just get the id from the Appium Inspector and plug it into your Java code.</p> 
<p>Now, let’s look at how to perform the testing itself by using TestNG and Cucumber on these Appium elements. Open up <tt>src/test/java/Tests/LoginTest.java</tt>:</p> 
<p><img alt="" class="aligncenter size-full wp-image-1844" height="1064" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_12.png" width="757" /></p> 
<p>You’ll see a few familiar methods if you’ve written TestNG or Cucumber tests before. On the TestNG side, you see standard TestNG assertions. These perform the core of the testing functionality: asserting that certain things about the Appium output must be a certain way, or else the test will fail. If you check out the TestBase.java file from before, you see even more TestNG functions, with some annotated as&nbsp;<tt>@BeforeSuite</tt> and&nbsp;<tt>@AfterClass</tt>. These sorts of functions allow a sequence of events to occur when testing. This allows you to force TestNG to perform some methods before or after various points in your test.</p> 
<p>Within a TestNG package, you structure each test method within suites and classes. Therefore, a method you annotate as @BeforeSuite would run before every new suite, @AfterClass would run after every new class, and so on. For more information about how to structure your TestNG test and annotate methods around it, see <a href="https://testng.org/doc/index.html" rel="noopener" target="_blank">the TestNG documentation</a>.</p> 
<p>On the Cucumber side, you see some annotations like&nbsp;<tt>Given</tt> or&nbsp;<tt>Then</tt>, as well as the all-important annotation <tt>CucumberOptions</tt>. <tt>Given</tt> and <tt>Then</tt> annotations come from Cucumber’s model of how to convert natural language test specifications to code. These annotations are given a string as their parameter. That string is a regular expression that contains most of a sentence or phrase. Check out the <tt>src/test/resources/LoginTest/LoginTest.feature</tt> file to see what specifications these tests are referring to. This feature file is specified in the&nbsp;<tt>CucumberOptions</tt> directory as the value to the <tt>features</tt> key:</p> 
<p><img alt="" class="aligncenter size-full wp-image-1845" height="229" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_13.png" width="748" /></p> 
<p>Check that out—a business specification for how the behavior of your login page should work, complete with phrases that would match the regular expressions in the annotations of your test!</p> 
<p>This is the biggest advantage of Cucumber and why it’s such a great testing framework. Even though correlating your test code to explicit specifications adds short-term overhead to your test writing, this practice can save you from unnoticed mistakes in the long run, as specs change over time. By using the TestNG plugins for Cucumber that you added to <tt>pom.xml</tt> earlier, it’s easy for you to use Cucumber specifications and features within a TestNG suite of Appium tests.</p> 
<p>Now that we’ve gone over the requirements for running tests and the code for how your tests work, you’re ready for a test run. Run the following from the root of the project directory (either in a terminal window or within your IDE’s terminal) to watch the tests run on your emulator:</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">mvn clean package</code></pre> 
</div> 
<p>And voila!</p> 
<p><img alt="" class="aligncenter size-full wp-image-1864" height="721" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/gif1_scaled_down.gif" width="677" /></p> 
<h3>Deploying Appium/TestNG tests to Device Farm</h3> 
<p>Next, let’s run these tests on some real devices rather than just on an Android emulator. In just a few steps, you’ll be watching your sample test run automatically on devices in the cloud:</p> 
<p><img alt="" class="aligncenter size-full wp-image-1849" height="1066" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_14.png" width="2560" /></p> 
<ol> 
 <li>Open the <a href="https://console.aws.amazon.com/devicefarm/home" rel="noopener" target="_blank">AWS Device Farm Console</a>.</li> 
 <li>Choose <strong>Start testing today</strong> if the screen is present.</li> 
 <li>Choose <strong>Create a new project</strong> to set up a project. A project is a logical grouping of test runs that are usually organized around a single app or Android project.</li> 
 <li>Choose <strong>Create a new run</strong> to set up your automation test run.&nbsp;<img alt="" class="aligncenter wp-image-1850" height="232" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_15-1024x395.png" width="600" /></li> 
 <li>Choose the button with the Android and iOS icons in it. This just means that you’re testing a native mobile app rather than a mobile web app.</li> 
 <li>Upload the Device Farm sample app’s APK file. You can find this file inside the directory where you initially built it with Android Studio at&nbsp;<tt>app/build/outputs/apk/app-debug.apk</tt>.</li> 
 <li>Choose&nbsp;<strong>Next</strong> to go to the <strong>Configure</strong> menu.</li> 
 <li>Choose&nbsp;<strong>Appium Java TestNG</strong> as your test type.</li> 
 <li>Upload a file called&nbsp;<tt>zip-with-dependencies.zip</tt>. You can find this in the&nbsp;<tt>target</tt> folder of your TestNG tests after you run the Maven command. This file is built and packaged from the instructional text file you saved earlier in your Maven project. It contains all of your dependencies for your project as Java class and JAR files—so everything running in Device Farm is just as it was on your local machine.</li> 
 <li>Finally, choose&nbsp;<strong>Customize your test environment</strong> to see a glimpse into the inner workings of Device Farm:&nbsp;<img alt="" class="aligncenter wp-image-1851" height="640" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_16.png" width="600" /></li> 
</ol> 
<p>Through this interface on the bottom half of the screen, you can see how the tests run on Device Farm servers, and then edit the run commands to best suit your needs!</p> 
<p>This is awesome for a lot of reasons, but let’s demonstrate with a simple use case for now. Cucumber solves a big problem that test frameworks encounter by simplifying test output to complete natural language sentences. By using Device Farm’s custom execution environment settings, you can stop TestNG from polluting the beautiful Cucumber output by removing the “verbose output”.</p> 
<p><img alt="" class="aligncenter size-full wp-image-1852" height="590" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_17.png" width="1535" /></p> 
<ol start="11"> 
 <li>Choose <strong>Edit</strong> to edit the YAML configuration of commands that Device Farm runs.</li> 
 <li>Scroll down to line 43, and remove the&nbsp;<tt>-verbose 10</tt> option at the end of the main test runner command.</li> 
 <li>Save this as a new YAML file configuration.</li> 
 <li>Choose the <strong>Next step</strong> button to proceed to the <strong>Select devices</strong> page.&nbsp;<img alt="" class="aligncenter wp-image-1853" height="403" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_18.png" width="601" /></li> 
 <li>Select which devices to run the tests against. In this case, let’s watch it run on all the recommended devices in Device Farm, so you don’t need to change any default settings. Choose <strong>Next</strong> to go to the <strong>Specify device state</strong> page.&nbsp;<img alt="" class="aligncenter wp-image-1854" height="559" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_19.png" width="600" /></li> 
 <li>On the <strong>Specify device state</strong> page, you can change a lot of cool things about the device state, like the network profile and GPS location. You don’t need to change these settings for now, and can leave the default settings. Choose <strong>Next</strong> to go to the final review page.&nbsp;<img alt="" class="aligncenter wp-image-1855" height="458" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_20.png" width="600" /></li> 
 <li>Finally, on the review page, choose <strong>Confirm</strong> and start the run!</li> 
</ol> 
<p>You can choose any of the devices on the main Run screen to watch the test in real time. Choosing a device brings up the device’s video and the corresponding logs produced from its test. This is what it looks like when the test first begins to set up:</p> 
<p><img alt="" class="aligncenter size-full wp-image-1856" height="769" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_21.png" width="1255" /></p> 
<p>And this is a screenshot of when the test begins to run:</p> 
<p><img alt="" class="aligncenter size-full wp-image-1865" height="547" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/gif2_scaled_down.gif" width="1200" /></p> 
<p>And after the test is done, you can check out a detailed report of how the test went. You can also download the logs and video files that were produced during the run:</p> 
<p><img alt="" class="aligncenter size-full wp-image-1858" height="1292" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/08/17/screenshot_23.png" width="2514" /></p> 
<h3>Next steps</h3> 
<p>That’s all for now! Stay tuned for more blog posts in the future about cool new ways to test mobile apps. To learn about ways you can meet your testing needs with Device Farm, see <a href="https://docs.aws.amazon.com/devicefarm/latest/developerguide/welcome.html" rel="noopener" target="_blank">the Device Farm documentation</a>. If you run into problems, find us on <a href="https://stackoverflow.com/questions/tagged/aws-device-farm" rel="noopener" target="_blank">Stack Overflow</a> and <a href="https://forums.aws.amazon.com/forum.jspa?forumID=193&amp;start=0" rel="noopener" target="_blank">AWS Forums</a>!</p> 
<p><em>This post was written by Jon Sims.&nbsp; Jon is a Solutions Engineer on the AWS Device Farm team.</em></p>
