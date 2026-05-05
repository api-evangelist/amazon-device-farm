---
title: "Build a CI/CD Pipeline for your Android App with AWS Services"
url: "https://aws.amazon.com/blogs/mobile/build-a-cicd-pipeline-for-your-android-app-with-aws-services/"
date: "Mon, 15 Jan 2018 16:59:03 +0000"
author: "Adrian Hall"
feed_url: "https://aws.amazon.com/blogs/mobile/category/mobile-services/aws-device-farm/feed/"
---
<p>Continuously building, testing, and delivering updates for your app to your users helps you release new features sooner and with fewer bugs. In an earlier blog post “<a href="https://aws.amazon.com/blogs/mobile/automatically-build-your-android-app-with-aws-codebuild/" rel="noopener noreferrer" target="_blank">Automatically build your Android app with AWS CodeBuild</a>,” Adrian Hall explains how you can use AWS CodeBuild to compile an Android app and publish the APK to Amazon S3.</p> 
<p>In this blog post, we’ll be adding a continuous integration and continuous delivery (CI/CD) pipeline by using <a href="https://aws.amazon.com/cloudformation" rel="noopener noreferrer" target="_blank">AWS CloudFormation</a>, <a href="https://aws.amazon.com/codepipeline" rel="noopener noreferrer" target="_blank">AWS CodePipeline</a>, <a href="https://aws.amazon.com/codebuild" rel="noopener noreferrer" target="_blank">AWS CodeBuild</a>, and <a href="https://aws.amazon.com/devicefarm" rel="noopener noreferrer" target="_blank">AWS Device Farm</a>. After you’ve completed the steps in this blog, you’ll have a working pipeline that will build your app, test it on real physical devices, and then deliver the packaged APK file to an S3 bucket each time a change is committed and pushed to the source code repository .</p> 
<p><img alt="" class="aligncenter size-full wp-image-1546" height="136" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/01/10/workflow_graphic.png" width="813" /></p> 
<h3>Create an Android project using Android Studio</h3> 
<p>Let’s start by creating a new project using Android Studio. If you already have a project you want to use, then you can skip this step. Otherwise, follow the steps on the <a href="https://developer.android.com/studio/projects/create-project.html" rel="noopener noreferrer" target="_blank">Android Studio website</a> to create a project. When you select the devices to target, choose <strong>Phone and Tablet</strong>. By default, a module named ‘app’ is created.</p> 
<h3>Create the CI/CD pipeline using AWS CloudFormation</h3> 
<p>Our pipeline uses multiple AWS services. We use an AWS CloudFormation template to provision all of those resources for us automatically. The pipeline consists of the following:</p> 
<ul> 
 <li>A CodeCommit repository to store the Android app’s code.</li> 
 <li>A CodeBuild project to compile, package, and deliver the Android app.</li> 
 <li>A Device Farm project to test the Android app on real physical devices.</li> 
 <li>An S3 bucket to store build artifacts.</li> 
 <li>A CodePipeline pipeline to monitor changes to the CodeCommit repository.&nbsp; Then it triggers a build using CodeBuild and tests using Device Darm.</li> 
 <li>AWS Lambda functions to invoke the Device Farm test from CodePipeline, and also to provision the Device Darm project from AWS CloudFormation</li> 
</ul> 
<p>Create the CloudFormation stack by following these steps:</p> 
<ol> 
 <li>Open the <a href="https://console.aws.amazon.com/cloudformation/home" rel="noopener noreferrer" target="_blank">AWS CloudFormation console</a>.</li> 
 <li>Click the <strong>Create Stack</strong> button.</li> 
 <li>Download the Cloudformation template from&nbsp;<a href="https://github.com/aws-samples/aws-code-snippets/blob/master/CloudFormation/DeviceFarm.yaml" rel="noopener noreferrer" target="_blank"><tt>https://github.com/aws-samples/aws-code-snippets/blob/master/CloudFormation/DeviceFarm.yaml</tt></a>.</li> 
 <li>Select <strong>Upload a template to Amazon S3</strong>, then click on <strong>Choose File</strong> and select the template you downloaded in the previous step</li> 
 <li>Type a stack name (for example, <tt>myapp-cicd-pipeline</tt>).</li> 
 <li>Review the default parameters, and then click <strong>Next</strong>.&nbsp; (Note that if you app’s module is not named ‘app’, you need to change the <strong>AppModuleName</strong> parameter).</li> 
 <li>(Optional) Set any options that you need in <strong>Options</strong>, and then click <strong>Next</strong>.</li> 
 <li>Select <strong>I acknowledge that AWS CloudFormation might create IAM resources.</strong>&nbsp; (The stack uses IAM roles for Lambda, CodeBuild, and CodePipeline.)</li> 
 <li>Click the <strong>Create</strong> button.</li> 
</ol> 
<p>AWS CloudFormation creates your stack. After it’s finished, the stack is in the ‘CREATE_COMPLETE’ status. Click the stack in the list of CloudFormation stacks, and then click the <strong>Outputs</strong> tab on the bottom half of the screen. The stack outputs two values, which we use in the upcoming steps. They are <tt>CodeRepoCloneUrlHttp</tt>, which contains the Git URL that we push code to, and <tt>OutputApkUrl</tt>, which contains the URL to the latest built and tested APK file.</p> 
<p><img alt="" class="aligncenter size-large wp-image-1540" height="721" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/01/10/stack_output-1024x721.png" width="1024" />Configure the project to use CodeCommit</p> 
<p>If your development environment isn’t already configured for AWS CodeCommit, you need to follow <a href="http://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-https-unixes.html" rel="noopener noreferrer" target="_blank">the setup steps</a> in the <em>AWS CodeCommit User Guide</em> to install the Git client, grant CodeCommit permissions to your IAM user, and set up the credential helper. You can skip step 4 because we’re configuring an existing project to use the Git repository.</p> 
<p>To configure your Android project to use CodeCommit, you need to add the CodeCommit repository that was created by the CloudFormation stack as a Git remote. Use the <tt>CodeRepoCloneUrlHttp</tt> output value from the previous step, and run the following command from your project’s workspace:</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">git remote add codecommit &lt;CodeRepoCloneUrlHttp from the stack output&gt;</code></pre> 
</div> 
<h3>Push the code to CodeCommit</h3> 
<p>We’re now ready to push the source code for your Android app to the CodeCommit repository. Doing this triggers an execution on the CodePipeline pipeline. A couple minutes after you’ve pushed the code, you’ll have a fully tested APK file in the output S3 bucket.</p> 
<p>From your Android app’s workspace, you need to make sure that all of the files have been checked into the Git repository.</p> 
<p>Add all of the files in your app’s workspace:</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">git add -A</code></pre> 
</div> 
<p>Commit the changes to your local Git repository:</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">git commit -a -m "Initial checkin of the code"</code></pre> 
</div> 
<p>Push the changes to the CodeCommit repository:</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">git push codecommit</code></pre> 
</div> 
<h3>View the build</h3> 
<p>To view the progress of the CodePipeline execution, you need to open <a href="https://console.aws.amazon.com/codepipeline/home" rel="noopener noreferrer" target="_blank">the CodePipeline console</a>. Find the pipeline that has the stack name of the CloudFormation stack that you created as the prefix and click the name. From here, you can see the progress of the pipeline execution.</p> 
<p>The execution goes through four stages (Source, Build, Test, and Deliver).</p> 
<ul> 
 <li><strong>Source</strong>: The latest code is taken from the CodeCommit repository and packaged as a ZIP file for use by other stages.</li> 
 <li><strong>Build</strong>: CodePipeline triggers a CodeBuild build to compile and package the app as an APK file.</li> 
 <li><strong>Test</strong>: CodePipeline invokes a Lambda function that uploads the APK file to Device Farm and runs a test on real devices.</li> 
 <li><strong>Deliver</strong>: The built and tested APK file is delivered to the specified S3 bucket</li> 
</ul> 
<h3><img alt="" class="aligncenter size-large wp-image-1549" height="1024" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/01/10/edited-output-833x1024.png" width="833" /></h3> 
<p>You can view the Device Farm test results from <a href="https://us-west-2.console.aws.amazon.com/devicefarm/home" rel="noopener noreferrer" target="_blank">the Device Farm console</a>. Click the project named <strong>demo-app-devicefarm</strong> if you used the default settings when you created the stack. Otherwise, click the name of the project that you specified in the CloudFormation stack parameters. Under the <strong>Automated tests</strong> tab, you see a test if it’s currently running or has finished. If you don’t see a test, the Test stage might not yet have been executed by CodePipeline. After the test has finished, you can view the results and also screenshots from the test.</p> 
<p><img alt="" class="aligncenter size-large wp-image-1543" height="1024" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/01/10/004-943x1024.png" width="943" /></p> 
<p>The final stage of the pipeline is to deliver the artifact to Amazon S3. Open <a href="https://s3.console.aws.amazon.com/s3/home" rel="noopener noreferrer" target="_blank">the Amazon S3 console&nbsp;</a> and find the artifact bucket. If your stack’s prefix is ‘<tt>myapp-cicd-pipeline</tt>‘, then the artifact bucket has a prefix of ‘<tt>myapp-cicd-pipeline-artifactbucket-</tt>‘. After the pipeline completes the execution, if the build and test phases completed successfully, an APK file is delivered in this S3 bucket.</p> 
<p>The CloudFormation stack output has an output with a key named <tt>OutputApkUrl</tt>. The value contains a URL that you can use to download the APK file directly. You can also use the URL to install the APK file on an Android device.</p> 
<p><img alt="" class="aligncenter size-large wp-image-1544" height="852" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/01/10/app-1024x852.png" width="1024" /></p> 
<h3>Next Steps</h3> 
<p>We’ve automated the building, testing, and delivery for an Android app by using AWS services. With a little work, you can modify the AWS CloudFormation template to also sign the app using your certificate, and deliver it directly to the Google Play store. To automate the delivery to the Google Play store, you can use tools such as fastlane. You can invoke these tools from CodeBuild.</p> 
<h5>About the Author</h5> 
<p><em>This post was contributed by Vinay Selvaraj. Vinay is a Consultant with AWS Professional Services who specializes in helping customers migrate to the cloud. You can contact him on Twitter at <a href="https://twitter.com/vinayselvaraj" rel="noopener noreferrer" target="_blank">@vinayselvaraj</a>.</em></p>
