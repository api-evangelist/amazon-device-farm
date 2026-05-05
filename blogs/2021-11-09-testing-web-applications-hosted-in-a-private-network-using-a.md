---
title: "Testing web applications hosted in a private network using AWS Device Farm"
url: "https://aws.amazon.com/blogs/mobile/testing-web-applications-hosted-in-a-private-network-using-aws-device-farm/"
date: "Tue, 09 Nov 2021 21:37:22 +0000"
author: "Sindhoor Grandhi"
feed_url: "https://aws.amazon.com/blogs/mobile/category/mobile-services/aws-device-farm/feed/"
---
<p>AWS Device Farm offers an elastic Selenium Grid in the cloud that provides instant access to different versions of Chrome, Firefox, Internet Explorer, and Microsoft Edge browsers running on Windows servers. Today, thousands of web developers are leveraging Device Farm’s Grid to verify that their applications function and perform as intended on popular browsers. Recently, AWS Device Farm announced integration with Amazon Virtual Private Cloud (VPC), enabling developers to test web applications hosted in their private network (such as internal apps, or web apps hosted in a development or test environments). This blog post will describe how you can integrate Device Farm’s Desktop Browser Testing service with Amazon VPC to test your private web applications.</p> 
<h2>Solution overview</h2> 
<p>In this solution, we will be creating and hosting a sample static website on Amazon S3 and restricting access to it via an Amazon VPC in the us-west-2 Region. We will then create a new project in AWS Device Farm and configure the project to use our VPC. Once a Device Farm project is associated with a VPC, all future test sessions will be scheduled through that VPC. The following diagram illustrates our solution architecture:</p> 
<p><img alt="This is an architecture diagram showing how AWS Device Farm connects to a VPC in us-west-2 Region. When a user runs their Selenium tests, the browser requested by the test is launched in Device Farm. The Device Farm browser connects to user’s VPC by creating an Elastic Network Interface in the VPC’s Private Subnet." class="aligncenter size-full wp-image-6510" height="419" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2021/11/09/fig1fc.jpg" width="1159" /></p> 
<p>Note: AWS Device Farm is only available in the us-west-2 Region. Hence,&nbsp;Device Farm can only connect to VPCs within the <code>us-west-2</code>&nbsp;Region. To access resources in a VPC in another Region, you must create a VPC in the <code>us-west-2</code>&nbsp;Region and peer the VPCs. For information on peering VPCs, see <a href="https://docs.aws.amazon.com/vpc/latest/peering/">Amazon VPC Peering Guide</a>.</p> 
<p>For this guide, we will be creating all our resources in us-west-2. In the AWS Management Console, set your Region to us-west-2 before proceeding to the next steps.</p> 
<h2>Step 1: Create an Amazon VPC</h2> 
<ol> 
 <li>Create a public-private Amazon VPC by following Step 1 and Step 2 in the&nbsp;<a href="https://docs.aws.amazon.com/batch/latest/userguide/create-public-private-vpc.html">Creating a VPC with Public and Private Subnets</a> Once the VPC creation is successful, note the <a href="https://console.aws.amazon.com/vpc/home?#vpcs:sort=VpcId">VPC ID</a>, <a href="https://console.aws.amazon.com/vpc/home?#subnets:">private subnet ID</a>, and <a href="https://console.aws.amazon.com/vpc/home?#SecurityGroups:">security group IDs</a>. We will need this when configuring Device Farm to use this VPC.</li> 
 <li>Check if a NAT gateway was created for your public subnet. You can do this by choosing NAT Gateways in the left navigation pane of the VPC console.</li> 
 <li>Finally, create a VPC endpoint for Amazon S3 by following the steps <a href="https://aws.amazon.com/blogs/aws/new-vpc-endpoint-for-amazon-s3/">here</a>.<br /> <strong>Important:</strong> Make sure to select the “Gateway” option when creating the endpoint:</li> 
</ol> 
<p><img alt="In the Create Endpoint page in VPC console, the option of AWS Services is selected as Service Category. After that, Amazon S3 and Gateway are selected as the Service Name and Type respectively." class="aligncenter size-full wp-image-6511" height="353" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2021/11/09/fig2fc.jpg" width="1233" /></p> 
<p>After completing the preceding three steps, the VPC setup should look like the following :</p> 
<p><strong>VPC:</strong></p> 
<p><img alt="The VPC details page in the AWS Management Console should show the VPC state as Available, and DNS hostname and resolution as Enabled" class="aligncenter size-full wp-image-6512" height="496" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2021/11/09/fig3fc.jpg" width="1613" /></p> 
<p><strong>Private and Public Subnets:</strong></p> 
<p><img alt="The Subnets page of the VPC console should show as private and public subnet connected to the VPC we just created" class="aligncenter size-full wp-image-6513" height="283" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2021/11/09/fig4fc.jpg" width="1638" /></p> 
<p><strong>NAT gateway:</strong></p> 
<p><img alt="The NAT gateway page in the VPC console should show a new gateway that is automatically created and connected to the public subnet" class="aligncenter size-full wp-image-6514" height="199" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2021/11/09/fig5fc.jpg" width="1649" /></p> 
<h2>Step 2: Create a sample static website using Amazon S3</h2> 
<p>We will now create a sample static website using Amazon Simple Storage Service (Amazon S3). To create the website using <a href="https://console.aws.amazon.com/console/home">AWS Management Console</a>, follow <a href="https://docs.aws.amazon.com/AmazonS3/latest/dev/HowDoIWebsiteConfiguration.html">Configure a bucket as Static website</a> guide.</p> 
<p>After we have created a static website by following the steps in the guide, we will have an Amazon S3 website in one of the following formats:</p> 
<p><a href="http://bucket-name.s3-website.us-west-2.amazonaws.com/">http://bucket-name.s3-website.us-west-2.amazonaws.com</a></p> 
<p><a href="http://bucket-name.s3-website-region.amazonaws.com/">http://bucket-name.s3-website-us-west-2.amazonaws.com</a></p> 
<p>For this blog, we can&nbsp;create a website using the sample index.html file provided in the preceding guide. Here is the file again for reference:</p> 
<div class="hide-language"> 
 <pre><code class="lang-html">&lt;html xmlns="http://www.w3.org/1999/xhtml" &gt;
&lt;head&gt;
    &lt;title&gt;My Website Home Page&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
  &lt;h1&gt;Welcome to my website&lt;/h1&gt;
  &lt;p&gt;Now hosted on AWS!&lt;/p&gt;
&lt;/body&gt;
&lt;/html&gt;
</code></pre> 
</div> 
<p>Once the bucket is created, we can&nbsp;see the URL of the website in the Amazon S3 console under the Properties section:</p> 
<p><img alt="The URL of the website is displayed under the Static website hosting section of the S3 bucket properties page" class="aligncenter size-full wp-image-6515" height="318" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2021/11/09/fig6fc.jpg" width="1548" /></p> 
<p>Using the URL, we can navigate to the website.</p> 
<p><strong>Note:</strong> If you are unable to view the website at this stage, make sure you have given <a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html#step3-edit-block-public-access">public access</a> to your S3 bucket.</p> 
<p>Now, let us update our S3 bucket’s policy to allow access only from the VPC we created in Step 1 of this guide. We can do this by updating our S3 bucket policy to the following (make sure to replace the bucket name and VPC ID with your information):</p> 
<div class="hide-language"> 
 <pre><code class="lang-json">{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::xxxx-mywebapp2/*",
            "Condition": {
                "StringEquals": {
                    "aws:sourceVpc": "vpc-048x1234567x123x1"
                }
            }
        }
    ]
}
</code></pre> 
</div> 
<p><strong>Note:</strong> If you open your website after applying the preceding bucket policy, you should not be able to access it from the public internet.</p> 
<h2>Step 3: Create an AWS Device Farm project and configure your VPC</h2> 
<p>Now that we have a static website hosted in our private Amazon VPC, let’s understand how we can test that website using AWS Device Farm.</p> 
<p>First, create a Device Farm project with your VPC details by following these steps:</p> 
<ol> 
 <li>Sign in to the Device Farm console at <a href="https://console.aws.amazon.com/devicefarm">https://console.aws.amazon.com/devicefarm.</a></li> 
 <li>On the left side navigation panel, choose Desktop Browser Testing, then choose Projects.</li> 
 <li>Click “New Project” on the top right side of the console.</li> 
 <li>Enter a name for your project.</li> 
 <li>In the Virtual Private Cloud (VPC) Settings section, select the VPC we created in the previous section, and the Private Subnets and Security Group attached to that VPC.</li> 
 <li>Then click Create to create the project. From the project page, make a note of the project ARN. It looks like arn:aws:devicefarm:us-west-2:111122223333:testgrid-project:123e4567-e89b-12d3-a456-426655440000. We will need the project ARN in the next step.</li> 
</ol> 
<p><img alt="In the AWS Device Farm project settings page, a VPC, a private subnet, and a security group are selected from the dropdown." class="aligncenter size-full wp-image-6516" height="831" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2021/11/09/fig7fc.jpg" width="1612" /></p> 
<h2>Step 4: Create a Selenium test script and run it on AWS Device Farm</h2> 
<p>To test the static web app we created, we need to write an automated test script using the Selenium framework. We can use the following sample Selenium Python script to test the website we created. The script first launches a Selenium Session in our Device Farm account. It then launches a Firefox browser instance hosted in AWS Device Farm and navigates to the web URL. It then asserts if the words “Welcome to my website” appear on the webpage.</p> 
<p>test.py:</p> 
<div class="hide-language"> 
 <pre><code class="lang-python"># Import the AWS SDK and Selenium libraries for Python
import boto3
from selenium.webdriver import FirefoxOptions
from selenium.webdriver import Remote
from selenium.webdriver.common.by import By

# Initialize a DeviceFarmClient in us-west-2
devicefarm_client = boto3.client("devicefarm", region_name="us-west-2")

# Create a pre-signed web driver hub URL:
testgrid_url_response = devicefarm_client.create_test_grid_url(
    projectArn="arn:aws:devicefarm:us-west-2:111122223333:testgrid-project:123e4567-e89b-12d3-a456-426655440000",
    expiresInSeconds=300)

# Use the FirefoxOptions class to request a browser instance
driver = Remote(testgrid_url_response['url'], options=FirefoxOptions())

try:
    driver.implicitly_wait(30)
    driver.maximize_window()
    driver.get("http://xxxx-mywebapp.s3-website-us-west-2.amazonaws.com/")
    element = driver.find_element(By.TAG_NAME, "h1")
    assert element.text == 'Welcome to my website'
finally:
    # Deletes the session once the test execution is complete
    driver.quit()
</code></pre> 
</div> 
<p>Make the following changes to the preceding script:</p> 
<ol> 
 <li>Replace the projectArn with the ARN of the project you have created in Step 3.</li> 
 <li>Update the URL with the Amazon S3 URL of your website that you have created in Step 1.</li> 
</ol> 
<p>We can now run the script from our local machine by navigating to the folder where we have stored the script and running the following:</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">$ python3 test.py</code></pre> 
</div> 
<p>Once the script starts running, navigate to the Device Farm console, choose Projects under “Desktop Browser Testing”, and select the relevant Project to see the session artifacts:</p> 
<p><img alt="Video from the session is seen under the video tab in the session detail page of the AWS Device Farm console" class="aligncenter size-full wp-image-6517" height="384" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2021/11/09/fig8fc.jpg" width="1601" /></p> 
<p><img alt="Action logs from the session are seen under the Actions tab in the session detail page of the AWS Device Farm console" class="aligncenter size-full wp-image-6518" height="667" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2021/11/09/fig9fc.jpg" width="1608" /></p> 
<p><img alt="Console and webdriver logs from the session are seen under the Files tab in the session detail page of the AWS Device Farm console" class="aligncenter size-full wp-image-6519" height="418" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2021/11/09/fig10fc.jpg" width="1594" /></p> 
<p>You can see from the video recording that Device Farm was able to launch our website from within our VPC and validate the assertion in our test.</p> 
<h2>Conclusion</h2> 
<p>Testing your web applications on different browser environments helps ensure your application delivers a delightful user experience, independent of the browser used to access it. AWS Device Farm offers a one-stop solution for testing your web applications on desktop and mobile browsers. <a href="https://aws.amazon.com/device-farm/">Start using AWS Device Farm today</a>.</p> 
<h3>About the author</h3> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter size-full wp-image-6522" height="160" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2021/11/09/Sindhoor-Grandhi-Profile.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Sindhoor Grandhi</h3> 
  <p>Sindhoor Grandhi is a Senior Product Manager for AWS Device Farm. He is passionate about helping customers ship high quality applications.</p> 
 </div> 
</footer>
