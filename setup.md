### Build the environment using CloudFormation

1.  We will need an EC2 Key Pair to build this stack.
2.  Under **Services** go to **EC2**.
9.  Select **Key Pairs** on the left.
10.  **Create a Key Pair**. Name the Key Pair **FND203DemoKP** and save the file to your desktop.
    * We will not need the file, but a Key Pair must exist</u>
5.  Now under **Services** open **CloudFormation**
6.  Click “**Create Stack**”
7.  Choose an **existing file in S3**: [InfraSecBuilderSessionEnvBuild.json](InfraSecBuilderSessionEnvBuild.json)
8.  Fill out the screen as follows:
    *   Stack Name: **FND203-Demo-Stack**
    *  Availability Zone 1: **Pick any availability zone**
    *   Availability Zone 2: **Pick any availability zone except the first one you picked**
    *  LatestLinuxAmiID: **Leave as default.**
    *  PassedKeyName: **FND203DemoKP**
9.  Click “Next”
10.  Click “Next” on the following screen.
11.  Acknowledge the CloudFormation Template creates a user by checking the box.
12.  Click “Create Stack”
13.  Refresh the CloudFormation interface until the Status shows “Create Complete”
14.  Click on the Stack Name
15.  Go to the Outputs tab of the Stack
16.  Make note of the DNS names. You can use these to validate the Web servers that should be publicly accessible are.
    *  The LoadBalancerFullDNS, PoCWebServer1PublicDNS, and PoCWebServer2PublicDNS should all work.

Now we’ve setup the environment I showed you on the slide. We can now move forward with your hand-on portion.

