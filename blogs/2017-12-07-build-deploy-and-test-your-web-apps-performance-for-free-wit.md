---
title: "Build, deploy, and test your web app’s performance for free with AWS Mobile Hub"
url: "https://aws.amazon.com/blogs/mobile/build-deploy-and-test-your-web-apps-performance-for-free-with-aws-mobile-hub/"
date: "Thu, 07 Dec 2017 18:56:20 +0000"
author: "Adrian Hall"
feed_url: "https://aws.amazon.com/blogs/mobile/category/mobile-services/aws-device-farm/feed/"
---
<p>According to a recent study, two-thirds of users access web applications on a mobile device, yet very few developers test their web applications on mobile devices. Today, we are adding web performance testing on mobile devices to AWS Mobile Hub. Combined with the recently announced <code class="lang-bash">awsmobile</code> CLI, web developers can run a simple performance test on our fleet of iOS and Android devices in the AWS Device Farm.</p> 
<p>Recently, we introduced <a href="https://github.com/aws-samples?utf8=%E2%9C%93&amp;q=aws-mobile&amp;type=&amp;language=" rel="noopener noreferrer" target="_blank">a number of downloadable starter kits</a> that provide you with a fully functional cloud-enabled app out of the box. In this blog post, we walk through the deployment and testing of a web application React Starter Kit. It should take about 10 minutes.</p> 
<p>1.&nbsp;&nbsp;&nbsp;&nbsp; Sign in to the <a href="https://console.aws.amazon.com/mobilehub/home#/" rel="noopener noreferrer" target="_blank">Mobile Hub console</a>. Choose the Restaurant Ordering Starter Kit and choose <strong>Start</strong>.</p> 
<p><img alt="" class="aligncenter size-full wp-image-1512" height="629" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2017/12/07/1.-Hub-Console.png" width="877" /></p> 
<p>2.&nbsp;&nbsp;&nbsp;&nbsp; Create a project: A project is a where you create and manage your app’s cloud resources.</p> 
<p><img alt="" class="aligncenter size-full wp-image-1513" height="396" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2017/12/07/2.-Create-Project.png" width="877" /></p> 
<p>3.&nbsp;&nbsp;&nbsp;&nbsp; Set up your starter kit: When you create the project, the starter kit automatically configures a number of backend features for you. While the cloud backend is configuring, set up your local environment. First, clone the starter kit. All of our starter kits are hosted on GitHub:</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">git clone https://github.com/awslabs/aws-mobile-react-sample.git&nbsp;</code></pre> 
</div> 
<p>4.&nbsp;&nbsp;&nbsp;&nbsp; Next, set up the AWS Mobile CLI.&nbsp;The AWS Mobile CLI enables Web and React Native developers to configure and manage their cloud environments right from their desktop. The CLI creates a Mobile Hub project in the backend, allowing users to integrate features like user sign-in, analytics, and serverless APIs for their apps directly from the terminal window.</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">npm install -g awsmobile-cli</code></pre> 
</div> 
<p>To use the CLI with AWS, make sure <a href="http://docs.aws.amazon.com/aws-mobile/latest/developerguide/web-getting-started.html#web-getting-started-prerequisites" rel="noopener noreferrer" target="_blank">your credentials are set up</a>. By this time, the cloud backend should be set up and you can access your project. Choose <strong>Next</strong>.</p> 
<p><img alt="" class="aligncenter size-full wp-image-1514" height="569" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2017/12/07/3.-Setup-starterkit.png" width="877" /></p> 
<p>5.&nbsp;&nbsp;&nbsp;&nbsp; Your project has automatically configured the backend you set up in the cloud. You need to link this cloud backend to the React starter app on your desktop. The <code class="lang-bash">awsmobile init</code> command links your project to the app you cloned on your desktop and pulls a cloud configuration file into your app. The configuration file contains a ledger of all endpoints for the configured backend features.</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">awsmobile init project-id</code></pre> 
</div> 
<p><img alt="" class="aligncenter size-full wp-image-1515" height="532" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2017/12/07/4.-Setup-backend.png" width="877" /></p> 
<p>6.&nbsp;&nbsp;&nbsp;&nbsp; Usually, at this point&nbsp;you would write code in your app to connect your app to the Amplify library to use the features you’ve enabled in the backend. For the starter kit, we’ve already <a href="https://github.com/awslabs/aws-mobile-react-sample/tree/master/client/src" rel="noopener noreferrer" target="_blank">written that code</a>, so you can proceed to the last step of deploying your app to the cloud.</p> 
<p><img alt="" class="aligncenter size-full wp-image-1516" height="519" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2017/12/07/5.-Deploy-App.png" width="877" /></p> 
<p>7.&nbsp;&nbsp;&nbsp;&nbsp; Test your code first to make sure the app is functioning. Use <code class="lang-bash">awsmobile run</code> instead of <code class="lang-bash">npm start</code>. This pushes any updated code to the cloud backend and launches the web application in your browser.&nbsp;Your React application has user registration and sign-in, which allows you to perform CRUD operations against an Amazon DynamoDB table. The app uses an Express application running in AWS Lambda. Lambda is invoked by Amazon API Gateway using proxy integration with greedy paths that only authenticated users can access.</p> 
<p><img alt="" class="aligncenter size-full wp-image-1517" height="437" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2017/12/07/6.-WebApp.png" width="877" /></p> 
<p>AWS Mobile Hub lets you host your web application by pushing your frontend code to Amazon Simple Storage Service (Amazon S3). The code is then distributed over an Amazon Cloudfront URL. You can publish your web application and start a free performance test with one command:</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">awsmobile publish --test</code></pre> 
</div> 
<p>When you run this command, your browser automatically opens two tabs.&nbsp; One&nbsp;shows the tests being run and the other shows your web application as it is deployed on the CloudFront CDN.</p> 
<p><img alt="" class="aligncenter size-full wp-image-1518" height="220" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2017/12/07/7.-Start-Webtest.png" width="877" /></p> 
<p>The web application is run on a number of devices, such as the Apple iPhone 8 and Google Pixel 2. This helps you gain real insights on app performance, with metrics such as time to first meaningful paint and time to first byte. Additionally, we provide screenshots of what the app looks like on different screen sizes and OS versions so developers can identify layout issues before release. To run more comprehensive tests, choose from our <a href="http://awsdevicefarm.info/" rel="noopener noreferrer" target="_blank">large library of cloud devices at AWS Device Farm</a>. After a few minutes, you should see your test results:</p> 
<p><img alt="" class="aligncenter size-full wp-image-1519" height="988" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2017/12/07/8.-CompleteWebtest.png" width="900" /></p> 
<p>We’re done! In 10 minutes you were able to start from scratch to build, deploy, and test a cloud-enabled app.</p> 
<p>&nbsp;</p>
