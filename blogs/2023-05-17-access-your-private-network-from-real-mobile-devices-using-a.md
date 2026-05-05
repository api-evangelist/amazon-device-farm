---
title: "Access your private network from real mobile devices using AWS Device Farm"
url: "https://aws.amazon.com/blogs/mobile/access-your-private-network-from-real-mobile-devices-using-aws-device-farm/"
date: "Wed, 17 May 2023 16:08:35 +0000"
author: "Nikhil Dabhade"
feed_url: "https://aws.amazon.com/blogs/mobile/category/mobile-services/aws-device-farm/feed/"
---
<p>Testing a mobile or web app on a real device often requires a secure connection to private endpoints. These endpoints are often hosted on AWS inside a VPC, on-premises, a cloud provider, or a mix of those configurations. In addition, you may want the host machines, to which your devices are connected, to be able to access private endpoints. For example, a common scenario is an unreleased version of your app is accessing the staging environment and the tests are accessing internal packages only accessible via your private network. With the launch of support for VPC-ENI based connectivity on AWS Device Farm, you can now easily connect your private devices and the test hosts on Device Farm to non-public endpoints.</p> 
<p>The new VPC connectivity supports multiple types of traffic such as TCP, UDP, web sockets, and streaming. This enables different types of apps installed on private devices to easily communicate with private endpoints without needing a separate configuration for each endpoint.</p> 
<p>Until recently, communication with private endpoints was achieved through the existing <a href="https://docs.aws.amazon.com/devicefarm/latest/developerguide/amazon-vpc-endpoints.html">Amazon VPC endpoint service</a> private link connection on Device Farm. In contrast, the ENI based approach significantly improves the setup and connection time, adds support for all traffic types, reduces the limitations on number of private endpoints you can connect to, eliminates the need for an explicit host proxy and device proxy settings for use cases that required it, and expands the IP subnets supported. You can now directly configure the VPC settings on a project level and have all the runs under that project use the same VPC settings.</p> 
<p>It is strongly suggested to use the <a href="https://docs.aws.amazon.com/devicefarm/latest/developerguide/vpc-eni.html">VPC-ENI method of VPC connectivity</a> on AWS Device Farm instead of the <a href="https://docs.aws.amazon.com/devicefarm/latest/developerguide/amazon-vpc-endpoints.html">Amazon VPC Endpoint service connectivity</a> option.</p> 
<p>In this blog, you will understand the architecture of this feature and walk through a hands-on example of connecting to a web application that is hosted in AWS VPC in the us-west-2 region.</p> 
<h2>Overview</h2> 
<p>The following diagram, shows the high-level architecture of the VPC-ENI feature:</p> 
<p><img alt="Figure 1: High-level architecture and traffic flow" class="alignnone size-full wp-image-10883" height="680" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2023/05/16/vpc-eni.jpg" width="880" /></p> 
<p style="text-align: center;">Figure 1: High-level architecture and traffic flow</p> 
<p>Your tests run in the fully managed service infrastructure inside the Device Farm service VPC in the AWS us-west-2 region. When you configure your Device Farm project with VPC settings and start a test session, it creates an Elastic Network Interface (ENI) in your VPC. The ENI is attached to one of the subnets that you provide as an input in your VPC settings. This network interface enables access from your Device Farm test runs to your private resources.</p> 
<p>The Device Farm VPC connectivity feature can only directly connect to a VPC in the us-west-2 region. However, if your final endpoint is not hosted in the us-west-2 region, you can use any of the cross-region VPC connectivity options such as <a href="https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html">VPC peering</a>, transit gateway, and VPN to reach the intended service. Device Farm does not impose any restrictions on what you can do outside of the VPC in us-west-2.</p> 
<p>Note, that if your VPC in us-west-2 needs to reach the internet then you would need a public facing NAT Gateway in that VPC. Deploying a NAT Gateway in your VPC may incur additional charges that are separate from the Device Farm pricing.</p> 
<h2>Prerequisites</h2> 
<p>Before you use this feature from your AWS account, ensure that you have following resources set up:</p> 
<ul> 
 <li>An AWS account.</li> 
 <li>An active subscription to <a href="https://docs.aws.amazon.com/devicefarm/latest/developerguide/working-with-private-devices.html">Private Devices</a> in Device Farm.</li> 
 <li>An <a href="https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html">Amazon VPC</a> with one or more private subnets in us-west-2 region of AWS.</li> 
 <li><a href="https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html">NAT Gateway</a> [Optional]: If your internal application requires access to the internet, these public resources must be accessible from within your VPC or through the NAT Gateway.</li> 
</ul> 
<h2>Implementation</h2> 
<p>Here are the implementation steps:</p> 
<ol> 
 <li>Launch a non-public web application running on an EC2 instance inside a VPC in AWS us-west-2 region.</li> 
 <li>Create a Device Farm project with VPC Settings.</li> 
 <li>Update Security group of your EC2 instance for ingress from Device Farm ENI.</li> 
 <li>Start a remote access session on a private device.</li> 
 <li>Access your internal web application from remote access session.<strong>Note</strong>, all the infrastructure of AWS Device Farm is hosted only in <strong>us-west-2</strong> region. Thus, you should create the required resources in the AWS us-west-2 region.</li> 
</ol> 
<h2>Launch a non-public web application inside a VPC in AWS us-west-2 region:</h2> 
<p>For this post, you are using an <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html">Amazon EC2</a> instance operating inside a private subnet of an Amazon VPC to host a sample web application.</p> 
<ol> 
 <li>&nbsp;To create a VPC and other resources such as subnets, route tables, internet gateway(optional),&nbsp; and NAT gateway (optional) follow the instructions <a href="https://docs.aws.amazon.com/vpc/latest/userguide/create-vpc.html#create-vpc-and-other-resources">here</a>. &nbsp;Ensure that the subnet is associated with a <a href="https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html">route table</a> that doesn’t have a route to an internet gateway.<br /> <strong>Note:</strong> It is strongly suggested to have more than one subnets in your VPC to ensure high availability. This is true for use cases outside of the example in this post.</li> 
 <li>&nbsp;To launch an EC2 instance, follow the steps below. A detailed documentation for launching an EC2 instance can also be found <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-instance-wizard.html#liw-launch-instance-with-defined-parameters">here</a>.</li> 
</ol> 
<p>a.&nbsp; &nbsp; In the <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-instance-wizard.html#liw-ami">Application and OS Images (Amazon Machine Image)</a>, choose a free tier Amazon Linux AMI and instance type as t2.micro.</p> 
<p>b.&nbsp; &nbsp; Select or create the <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-instance-wizard.html#liw-key-pair">key pair</a> to securely connect to your instance.</p> 
<p>c.&nbsp; &nbsp; Edit the <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-instance-wizard.html#liw-network-settings">network settings</a> section and select the VPC which you have created as a part of prerequisites. In the Subnet dropdown, select the private subnet.</p> 
<p>d.&nbsp; &nbsp; <strong>Disable</strong> the auto-assign public IP option.</p> 
<p>e.&nbsp; &nbsp; Next edit the Firewall (security groups) section. Choose <strong>Create Security Group</strong> option and provide a suitable name.</p> 
<p>f.&nbsp; &nbsp; Under <strong>Inbound security groups rules</strong>, remove the <strong>SSH traffic rule</strong> which is selected by default. You will update these rules in the later section to allow flow of HTTP traffic over port 80 from Device Farm private device to your EC2 instance that goes through the service managed network interface.</p> 
<p>g.&nbsp; &nbsp; Expand the <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-instance-wizard.html#liw-advanced-details">Advanced details</a> section and scroll down to <strong>User data</strong> option. Now, enter the following commands to install a web server:</p> 
<pre><code class="lang-bash">#!/bin/bash
yum -y update
yum -y install httpd
chkconfig httpd on
service httpd start
echo "&lt;html&gt;&lt;h1&gt;Hello! I am an Ec2 instance speaking from a private subnet of your VPC &lt;/h1&gt;&lt;/html&gt;" &gt; /var/www/html/index.html
</code></pre> 
<p>h.&nbsp; &nbsp; Launch the instance.</p> 
<p>i.&nbsp; &nbsp; Note the private IP address of this instance as you will use it later to access the web application from a private device.</p> 
<h2>Create a Device Farm project with VPC Settings</h2> 
<p>Next, you create a new project in Device Farm console and assign VPC Settings using the following steps:</p> 
<ol> 
 <li>On the <a href="https://us-west-2.console.aws.amazon.com/devicefarm/home#/mobile/projects">Device Farm console</a>, choose <strong>New project</strong>.<br /> <strong>Note:</strong> It is strongly suggested to not use an existing project when using the VPC-ENI feature. Specifically, if you have used the <a href="https://docs.aws.amazon.com/devicefarm/latest/developerguide/amazon-vpc-endpoints.html">VPCE feature</a> on the same project. Using both the connectivity methods in the same project results in unintended errors.</li> 
 <li>Enter a suitable name for your project.</li> 
 <li>Under the <strong>Virtual Private Cloud (VPC) Settings</strong> section, select the <strong>VPC</strong> in which your internal web applications is hosted.</li> 
 <li>Choose the private <strong>Subnets</strong> that Device Farm will use to create the network interface in your VPC.<br /> <strong>Note:</strong> It is strongly suggested to have select more than one subnet in this setting to ensure high availability. This also is true for use cases outside of the example in this post.</li> 
 <li>Next, select the <strong>Security groups</strong> for Device Farm to set up your VPC configuration. Make a note of this security group as you will allow-list this in on your EC2 instance security group configuration.</li> 
 <li>Choose <strong>Create</strong>.</li> 
</ol> 
<p><img alt="Figure 2: Create a project in Device Farm console." class="alignnone size-full wp-image-10884" height="602" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2023/05/16/create-project.png" width="958" /></p> 
<p style="text-align: center;">Figure 2: Create a project in Device Farm console.</p> 
<h2>Update Security group of your EC2 instance for ingress from Device Farm ENI.</h2> 
<p>In order to further improve the security posture and only allow traffic from Device Farm ENI over port 80 to your EC2 instance, allow-list the security group used in Device Farm VPC settings using the following steps:</p> 
<ol> 
 <li>Navigate to the security group of the EC2 instance on which web application is hosted.</li> 
 <li>Follow the instructions on <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/working-with-security-groups.html#updating-security-group-rules">updating security group rules</a>.</li> 
 <li>Allow-list the security group ID that you have noted while configuring the VPC settings of Device Farm. This will allow HTTP traffic from Device Farm to EC2 instance through the service managed network interface.</li> 
</ol> 
<h2>Starting a remote access session on a private device</h2> 
<p>Start a remote access session on your private device using the following steps:</p> 
<ol> 
 <li>First, navigate to the Device Farm project created in previous section.</li> 
 <li>On the <strong>Remote access</strong>&nbsp;tab, choose&nbsp;<strong>Start a new session</strong>.</li> 
 <li>Choose any <strong>private device</strong> for your session.</li> 
 <li>In <strong>Session name</strong>, enter a name for the session.</li> 
 <li>Choose <strong>Confirm and start session</strong>.</li> 
</ol> 
<h2>Access your internal web application from remote access session</h2> 
<p>You can access the web application in a remote access session on a private device using the following steps:</p> 
<ol> 
 <li>Ensure that the Device Farm console shows the device screen and you can interact with device.</li> 
 <li>Open the mobile web browser on the device.</li> 
 <li>Enter the private IP address of EC2 instance that you noted in Step 2 of the launching an EC2 instance section using the following format:<br /> <code>http://&lt;private_ip_of_instance&gt;</code></li> 
 <li>Once the page loads, you can see the response from the instance on which our non-public web application is running.<img alt="Figure 3: Webpage hosted in your VPC displayed on the private device browser." class="alignnone size-full wp-image-10885" height="772" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2023/05/16/remote-access.png" width="960" /></li> 
</ol> 
<p style="text-align: center;">Figure 3: Webpage hosted in your VPC displayed on the private device browser.</p> 
<h2>Conclusion</h2> 
<p>In this post, we demonstrated how you can access an internal web applications through the VPC-ENI based connectivity feature in AWS Device Farm. The feature operates at scale, allowing you to securely connect multiple private devices to multiple private endpoints that are inside a VPC or are reachable from that VPC. It supports multiple traffic types and works through a simple project level configuration. You can learn more about this feature in our <a href="https://docs.aws.amazon.com/devicefarm/latest/developerguide/vpc-eni.html">documentation</a>.</p> 
<h3>About the authors:</h3> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter size-full wp-image-11636" height="160" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2023/05/03/nikhil-150x150.jpg" width="120" />
  </div> 
  <h4 class="lb-h4">Nikhil Dabhade</h4> 
  <p>Nikhil is a Principal Solutions Engineer at AWS Mobile. He is passionate about simplified developer experience and helping customers become successful with AWS. Outside of work, he can be found building prototypes, rowing or playing badminton.</p> 
 </div> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter size-full wp-image-11636" height="160" src="https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2023/05/16/Nishant-dhaman.png" width="120" />
  </div> 
  <h4 class="lb-h4">Nishant Dhiman</h4> 
  <p>Nishant Dhiman is a Solutions Architect at AWS with an extensive background in Serverless, Security and Mobile platform offerings. He is a voracious reader and a passionate technologist. He loves to interact with customers and always relishes giving talks or presenting on public forums. Outside of work, he likes to keep himself engaged with podcasts, calligraphy and music</p> 
 </div> 
</footer>
