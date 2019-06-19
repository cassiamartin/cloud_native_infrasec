### Build the environment using CloudFormation

1.  We will need an EC2 Key Pair to build this stack.
2.  Under **Services** go to **EC2**.
3.  Select **Key Pairs** on the left.
4.  **Create a Key Pair**. Name the Key Pair **FND203DemoKP** and save the file to your desktop.
    * We will not need the file, but a Key Pair must exist
5.  Now under **Services** open **CloudFormation**
6.  Click “**Create Stack**”
7.  In your Github tab, right-click to download the raw [InfraSecBuilderSessionEnvBuild.json](https://raw.githubusercontent.com/cassiamartin/cloud_native_infrasec/master/InfraSecBuilderSessionEnvBuild.json)
8.  In CloudFormation, choose **upload a template file**, and choose the json file you just downloaded: 
9.  Fill out the screen as follows:
    *   Stack Name: "**FND203-Demo-Stack**"
    *   Availability Zone 1: **Pick any availability zone**
    *   Availability Zone 2: **Pick any availability zone except the first one you picked**
    *  LatestLinuxAmiID: **Leave as default.**
    *  PassedKeyName: "**FND203DemoKP**"
10.  Click “Next”
11.  Click “Next” on the following screen.
12.  Acknowledge the CloudFormation Template creates a user by checking the box.
    ** _Note: People frequently miss this step_
13.  Click “Create Stack”
14.  Refresh the CloudFormation interface until the Status shows “Create Complete”
15.  Click on the Stack Name
16.  Go to the Outputs tab of the Stack
17.  Copy the following DNS names into a scratch pad. You can go to these in a web browser to validate that the Web servers are publicly accessible.
     *  The LoadBalancerFullDNS, PoCWebServer1PublicDNS, and PoCWebServer2PublicDNS should all work.

Now we’ve setup the environment I showed you on the slide. We can now move forward with [your hands on portion](../readme.md).

