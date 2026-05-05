---
title: "AWS CodePipeline adds support for AWS Device Farm as a test provider"
url: "https://aws.amazon.com/blogs/mobile/aws-codepipeline-adds-support-for-aws-device-farm-as-test-provider/"
date: "Thu, 19 Jul 2018 23:42:47 +0000"
author: "Dennis Hills"
feed_url: "https://aws.amazon.com/blogs/mobile/category/mobile-services/aws-device-farm/feed/"
---
<p>AWS CodePipeline now supports AWS Device Farm as a test provider to deliver truly automated testing in your continuous integration and continuous delivery (CI/CD) pipeline.</p> 
<p>In a previous blog post “<a href="https://aws.amazon.com/blogs/mobile/build-a-cicd-pipeline-for-your-android-app-with-aws-services/">Build a CI/CD Pipeline for Your Android App with AWS Services</a>,” Vinay Selvaraj walked you through building a complete CI/CD pipeline using <a href="https://aws.amazon.com/codecommit/">AWS CodeCommit</a>, <a href="https://aws.amazon.com/codebuild/">AWS CodeBuild</a>, <a href="https://aws.amazon.com/lambda/">AWS Lambda</a>, <a href="https://aws.amazon.com/device-farm/">AWS Device Farm</a>, and <a href="https://aws.amazon.com/s3/">Amazon S3</a> (all triggered by a single code repository push).</p> 
<p>In that solution, two Lambda functions were deployed-one to provision a Device Farm test project, and the other to kick off a Device Farm test on real devices. Now that Device Farm is fully integrated as a test provider for AWS CodePipeline, we can greatly improve on that solution.</p> 
<p>In this article, we first walk you through adding Device Farm as a test provider directly into your build, test, and deployment pipeline using the CodePipeline console. We then run through building an entire CI/CD pipeline solution by using a single CloudFormation template. The template automates resource provisioning with Device Farm as a native test provider in the test stage of a CodePipeline pipeline.</p> 
<p>Let’s go over the new addition to CodePipeline.</p> 
<h2>Add Device Farm as a test provider using the AWS CodePipeline console</h2> 
<p>Start by signing into the <a href="https://console.aws.amazon.com/codepipeline/home?region=us-east-1#/intro">CodePipeline console</a>.</p> 
<p>If you don’t already have an existing CodePipeline pipeline created, follow these <a href="https://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines-create.html#pipelines-create-console">instructions</a>&nbsp;to create one. Then follow the steps below to add Device Farm as a test provider.</p> 
<p style="padding-left: 30px;">1.&nbsp;&nbsp;&nbsp;&nbsp; Select an existing pipeline from the CodePipeline Console.</p> 
<p style="padding-left: 30px;">2.&nbsp;&nbsp;&nbsp;&nbsp; <strong>Choose Edit</strong> at the top of your existing pipeline</p> 
<p style="padding-left: 30px;"><img alt="" class="alignnone wp-image-1752" height="164" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/07/19/edit-pipeline-1024x205.png" width="819" /></p> 
<p style="padding-left: 30px;">3.&nbsp;&nbsp;&nbsp;&nbsp; Add a new stage in your pipeline and name your stage</p> 
<p style="padding-left: 30px;">4.&nbsp;&nbsp;&nbsp;&nbsp; <strong>Choose Action</strong>. This opens a new window on the right half of the console.</p> 
<p style="padding-left: 30px;">5.&nbsp;&nbsp;&nbsp;&nbsp; In the new action window, for <strong>Action Category, choose</strong> <strong>Test</strong>.</p> 
<p style="padding-left: 30px;">6.&nbsp;&nbsp;&nbsp;&nbsp; Enter an Action name: (for example, <em>TestOnRealDevices</em>).</p> 
<p style="padding-left: 30px;">7.&nbsp;&nbsp;&nbsp;&nbsp; For Test Provider, choose <strong>AWS Device Farm</strong>.</p> 
<p style="padding-left: 30px;">8.&nbsp;&nbsp;&nbsp;&nbsp; For <strong>Project name</strong>, choose if you want to use an <strong>existing Device Farm test project</strong> or <strong>create a new one</strong></p> 
<p style="padding-left: 60px;"><span style="text-decoration: underline;"><strong>Existing</strong> Device Farm test project</span></p> 
<p style="padding-left: 60px;">If you have an existing Device Farm test project, choose the project name from the dropdown and skip to step 10.</p> 
<p style="padding-left: 60px;"><span style="text-decoration: underline;"><strong>New</strong> Device Farm test project</span></p> 
<p style="padding-left: 60px;">You can create a new Device Farm project by choosing Create a new project. This directs you to the Device Farm console to provision a new project. After you create a new project via the Device Farm console, return here, hit the refresh button (located to the right of the Project name field), and then choose the newly created project name from the dropdown.</p> 
<p style="padding-left: 60px;"><img alt="" class="alignnone wp-image-1753" height="210" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/07/19/create-new-project-1024x273.png" width="788" /></p> 
<p style="padding-left: 30px;">9.&nbsp;&nbsp;&nbsp;&nbsp; Configure the remaining required and optional properties.</p> 
<p style="padding-left: 60px;"><img alt="" class="alignnone size-large wp-image-1751" height="1024" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2018/07/19/df-project-requirements-714x1024.png" width="714" /></p> 
<p style="padding-left: 30px;">10.&nbsp; Choose <strong>Add action</strong> to save this test stage.</p> 
<p>Congratulations! You’ve successfully added Device Farm as a test provider to your CodePipeline stage using the console. You can now trigger the pipeline with a code push, and CodePipeline will integrate directly with Device Farm to test the complied app on real devices.</p> 
<h2>Resources</h2> 
<p><a href="https://docs.aws.amazon.com/devicefarm/latest/developerguide/welcome.html">AWS Device Farm Developer Guide</a></p> 
<p><a href="https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome.html">AWS CodePipeline User Guide</a></p> 
<p>&nbsp;</p>
