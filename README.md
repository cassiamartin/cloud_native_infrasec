## re:Inforce 2019 FND203 - Mitigate Risks Using Cloud-Native Infrastructure Security

Here are the steps you’ll perform as part of this Builders Session:

1.  [Enable detailed, holistic logging and network-based security monitoring](#Enable granular logging to see everything in your AWS environment)
2.  [Review and improve upon granular control of communication between workloads in the cloud](#Granular, Provable Control of Communications)
3.  [Improve upon granular network-based controls protection side-to-side movement](#When Security includes explicitly denying network access)
4.  [Evaluate detailed logging capabilities](#Logging actions in your environment and making it easy to see what’s changed)
5.  [Evaluate network-based protections](### Logging and monitoring of the network for bad behavior is important too)
6.  [Further reduce administrative risks by reducing access and improving logging](#Reducing the risk of Admin access and administrative ports)

For ease of experience, here are the general steps you will take. I want you to learn the interface though, so these instructions are more of a guide than a tutorial. Please don’t hesitate to ask questions.

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

## Enable granular logging to see everything in your AWS environment

1.  Go to the **CloudTrail** service in the console
2.  Click on Getting Started if you haven’t seen this before
3.  We want to **Create trail** in order to make sure we are capturing what exactly?
4.  Let’s set a **Trail Name** of “**All API Commands across all Regions**”. Then let’s **Apply trail to all regions** with a single click.
5.  Seeing **All Read/Write events** would show us every API call made to our AWS environment moving forward.
6.  We should save these for further evaluation, so you would want to **Create a new S3 bucket** and call it “**fdn203-demo-bucket-{myname}**”. (Don’t forget, S3 buckets must have unique names, so make sure to add your name at the end. They can also only be lower case letters, numbers, “-“, and “.”)
7.  Let’s **Create** that trail. We can come back to look at it later.

    Monitoring what API calls are made is great, but it’s difficult to convert that into something like Change Management for all infrastructure in the cloud. Is there a service to help there?

1.  **Services** called **Config** would be worth looking into.
2.  After **Getting Started** we can start tracking All resources, Including Global Resources
3.  We would want to store this data in a central bucket as well. Let’s **Create a new bucket** and use the default to ensure its unique (Note: We could use the bucket we just created, but you would have to add permissions, which would take more time).
4.  AWS does a good job of clearly defining roles, so let’s allow AWS to **Create AWS Config service-linked role**
5.  **Next**, we can choose rules we want to test against, but we can do that later too if we **Skip** it for now.
6.  **Confirm**ing these choices will enable Config to monitor all changes to our environment. And we’ll see that in a bit.

  Now that we’ve got good logging of the Control Plane (API commands and Changes to the environment), let’s turn on logging of the Data Plane.

1.  Using a **Service** like **GuardDuty** you can monitor logs in near-real-time for security anomalies.
2.  After **Getting Started** we can quickly enable this service with just one click. It’s that easy. What is GuardDuty monitoring, we’ll check that out later.

When we looked at our on-premises environment we identified that disjointed security tooling, lack of insight into what’s going on in the environment, and difficulty managing change control and permissions in the environment all led to risks becoming problems pretty fast. With the services we just enabled, we’ll see how we now have complete insight into who’s doing what in the environment, what changes are being made, and if and when problems start to arise.

## Granular, Provable Control of Communications

1.  Looking at the granular control of system-to-system communication used to be difficult. Now, looking at your **EC2** Service **Security Groups** allows you to quickly see who can talk to whom.
2.  Picking a Security Group like the **Services Server Security Group** we can see the more traditional way of doing things.
3.  Checking the **Outbound** rules, we see the servers can talk to a range of IP’s, 65,536 to be precise. But there are only maybe 6-8 servers that they actually need to talk to.
4.  Well, if we copy the **GroupID** of the **PoC Web Server Security Group** we start to reduce that number
5.  **Edit** the **Outbound** **Services Server Security Group** rules and replace the 10.0.0.0/16 with the Security Group name you copied.
6.  **Save** this and you’ll see the **Destination** of the rule now shows the Security Group you listed.
7.  You can **repeat** this by **Edit**ing and **Adding Rule**s for each security group you want to allow access to.

In doing this, you’ve reduce the scope of internal traffic communication from 65,636 host down to 8\. Additionally, if you ever need to stand up more servers in these groups, they would be automatically accessible without intervention, as long as you put them in the same Security Group. On premise, you would either need to have Firewalls between all internal VLAN’s, Routers, and sites or complex Network ACL’s on every switch in your environment. This reduces the risk of threats, the risk of misconfiguration, and the operational burden all at once.

## When Security includes explicitly denying network access

1.  Security Groups are awesome at allowing access, but in **VPC** Services, **Network ACLs** are great at explicitly blocking them.
2.  For instance, if you wanted to make sure you explicitly blocked the Load Balancer in my WebApp from talking to my Database servers, you could **Create a network ACL**.
3.  I would Name it “**LoadBalancerIsolation**” and put it in the **Web Application VPC**.
4.  I would add an **Outbound Rule** by **Editing Outbound Rules**
5.  **Adding Rules** like
    * Rule #: **50**
    *  Of type **All Traffic**
    3.  To the Destination **10.0.2.0/24**
    4.  And a **Deny** Behavior

   And

    *  Rule #: **60**
    2.  Of type **All Traffic**
    3.  To the Destination **10.0.130.0/24**
    4.  And a **Deny** Behavior

   And
   *   Rule #: **100**
   2.  Of type **All Traffic**
   3.  To the Destination **10.0.0.0/8**
   4.  And an **Allow** Behavior

   Would block whatever Subnet you apply this to from talking to the Database Subnets but still allow access to the rest of the network, including the Web and Services VPC.

1.  After **Saving** you need to allow access to that subnet from the internet, so recreating the **All Traffic Allow** rule is necessary. **Add a Rule**
    *  Rule #: **100**
    2.  Of type **All Traffic**
    3.  To the Destination **0.0.0.0/0**
    4.  And a **Allow** Behavior
2.  After **Saving** you would then use **Subnet Associations** to **Edit Subnet Associations**.
3.  Here you would Associate with the **Web App Public Subnet in AZ1** and **Web App Public Subnet in AZ2** subnets by clicking **Edit**.

  Now, you’ve effectively ensured that if the Load Balancers in your environment misbehave, they can’t communicate with or compromise the Database servers directly. But there was no additional hardware firewall or complex routing required to make this simple change in the simple network topology.

  But let’s say you want to go further. The Proof of Concept servers you built aren’t exchanging state so they don’t need to communicate to each other. This way, if one gets compromised it can’t hurt the other. Let’s build that protection.

1.  You would **Create a network ACL**.
2.  Name it “**PoCProtectionAZ1**” and put it in the **Proof of Concept VPC**.
3.  Add an **Outbound Rule** by **Editing Outbound Rules**
4.  **Add Rules**
    *  Rule #: **50**
    2.  Of type **All Traffic**
    3.  To the Destination **10.250.128.0/24**
    4.  And a **Deny** Behavior

   And
   *  Rule #: **100**
   2.  Of type **All Traffic**
   3.  To the Destination **0.0.0.0/0**
   4.  And an **Allow** Behavior

1.  **Save** the rules and allow access to that subnet from the internet. **Edit inbound Rules** and **Add Rule**
    *  Rule #: **100**
    2.  Of type **All Traffic**
    3.  To the Destination **0.0.0.0/0**
    4.  And a **Allow** Behavior

1.  After **Saving** you would then use **Subnet Associations** to **Edit Subnet Associations**.
2.  Here you would Associate with the **Proof of Concept Public Subnet in AZ1** subnets by clicking **Edit**.
3.  You can choose to duplicate those steps to block the other direction by setting
    *  Name to “**PoCProtectionAZ2**”
    2.  Blocking traffic to the Destination **10.250.0.0/24**
    3.  And Associating with **Proof of Concept Public Subnet in AZ2**

Now, even within the same workload or application you are protecting servers from each other where putting Firewalls in the past would have been impossible.

## Logging actions in your environment and making it easy to see what’s changed

1.  We just made a bunch of changes, and on-premises it may be difficult to track them. Let’s check the **CloudTrail** in **Services** to see what it noticed.
2.  The Dashboard shows us some recent events, but we want to see the complete **Event History**.
3.  Here we can see your **User Name** performing actions tracked by **Event name** against different **Resource Names**. API commands not related to Data (because we chose that earlier) are being captured by CloudTrail.
4.  You can remove the system calls by using a **Filter** on **User name** and putting your **Username** in the text box and hitting enter. Now **Scroll down**. Do you see all the ACL’s you changed?

  But again, seeing these API calls doesn’t give you a good visual of the changes occurring.

1.  Lets go to **Config**.
2.  I can see all of my resources, including some called **EC2 NetworkAcl**.
3.  Clicking there gives you a list of ACL’s, and you can **click** on the first one.
4.  Seeing details on that ACL, you can also see a visual **Configuration Timeline**
5.  In the configuration timeline, you can see the changes that occurred over the past few minutes.
    *  If you don’t see any changes go back and choose a different ACL
6.  If you expand **Changes** you can see exactly what changes you made to the resource, including what you applied it to as a **Relationship Change**.

How would you do this on-premises?

## Logging and monitoring of the network for bad behavior is important too

1.  Let’s go back to **GuardDuty** and see what findings we may have.

  If this is a new or infrequently used account, you may have no Findings. If you do have Findings and this is not a new account, we can walk through those separately.

1.  Since this is a good design and relatively new, let’s create some demonstration findings in **Settings**
2.  After we **Generate sample findings** we can go back to the **Findings**
3.  9 High Severity Findings, 31 Medium Severity Findings, and 5 Informational Findings (where can you see those numbers quickly) show up. Let’s investigate the first High Severity, **[SAMPLE] Backdoor:EC2/C&CActivity.B!DNS**.
4.  You can see the (fake) instance that caused this Finding, what the instance did wrong, when it occurred, and more information. The “!DNS” at the end means something, do you know what? Does the **Action Type** help?
    1.  Ask me about the 3 data sources GuardDuty uses if you don’t already know.
5.  **Scrolling down** the Findings list again you see another high severity **[SAMPLE] Backdoor:EC2/XORDDOS**.
6.  Here you see a lot of the same type of information. But why is this **Action Type** different?
    1.  If we didn’t turn on those logs how did it see the traffic?
7.  **Scrolling down** the Findings list a bit more you find **[SAMPLE] UnauthorizedAccess:IAMUser/UnusualASNCaller**.
8.  This one not only has a different **Action Type** but also starts with **IAMUser** instead of **EC2**. Why does that matter?
    1.  Is this a different data source?

Now you’ve seen that GuardDuty is monitoring logs on your behalf, and without you having to pay for storage, the AI/ML or Threat feeds, and the man hours to do the analysis. This is all happening at Cloud scale too, no longer do you need to have terabytes of logs that are never touched.

## Reducing the risk of Admin access and administrative ports

There’s still a risk of open administrative ports though right? Whether open to the internet or open internally for malware to find it’s a risk. Except it doesn’t have to be.

1.  Let’s go back to **VPC** and **Security Groups**.
2.  Open the **Services Server Security Group** and the **Inbound Rules**.
3.  Now, despite the fact that those are made up IP’s, you are going to **Edit Rules** and delete all the rules (Click the x on the right). Then **Save** and **Close**.
4.  But with no access, how can we monitor or log into the box if we need to? Our **Service** called **Systems Manager** can help there.
5.  Systems Manager has a feature called **Session Manager** worth checking out.
6.  At **Sessions**, you can **Start a session** with any server with the SSM agent and access to the SSM Service.
7.  We disabled all access to the **Services Server for AZ1**, yet there it is. Let’s select it and **Start session**.
8.  Is this a console? For the AWS server? Let’s find out.
    *  Type: curl http://169.254.169.254/latest/meta-data/instance-id
        *   Does that instance ID look familiar?
    2.  Try: curl http://169.254.169.254/latest/meta-data/security-groups
        *   That looks like the Security Group we modified doesn’t it?
    3.  Let’s try: Ping 8.8.8.8
        *  Should it work?
    4.  Last time: curl http://169.254.169.254/latest/meta-data/iam/security-credentials/SharedServerConnectivityRole
        *  Sure looks like an AWS server.

## Extra Credit

There’s a way to log all commands sent to the instance as well. But first you have to create S3 buckets and CloudWatch Logs

1.  Go to **S3**.
2.  **Create a Bucket** called **fdn203-ssmlogs-bucket-{myname}**. Before you move on, turn on **Default Encryption** using **AWS-KMS** and the **aws/s3**
3.  Then **Grant Amazon S3 Log Delivery group write access to the bucket**.
4.  Go to **CloudWatch**.
5.  In **Logs** you must **Create log group**, called **fnd203-ssmlogs-logs**.
6.  Go to **IAM**.
7.  We will modify the **Role** called
8.  Expand the Inline Policy and click **Edit Policy**
9.  **Add additional permissions** including
    1.  **S3**
        1.  **Write**
        2.  **Bucket: Any**
        3.  **Object**: **Any**
    2.  **CloudWatch Logs**
        1.  **Write**
        2.  **Log Group: Any**
        3.  **Log Stream: Any**

1.  **Review the policy** and **Save Changes**
2.  If there are any errors, go to **Previous** and keep adding **Any** to the resources the policy requires defined.

_<u>This can be more restrictive in a production environment.</u>_

1.  Now go back to **Systems Manager**, **Session Manager**.
2.  Let’s use **Preferences** to set up logging.
3.  **Edit** the settings to **Write session output** and choose the bucket called “**fdn203-demo-bucket-{myname}**”. (Don’t forget, S3 buckets must have unique names, so make sure to add your name at the end. They can also only be lower case letters, numbers, “-“, and “.”)
4.  Let’s also send the output to **Cloudwatch logs**, we can deselect **Encrypt Log Data**, and create a log group name “**fdn203-demo-bucket-{myname}**”.
    1.  In production I would not recommend storing unencrypted logs.
5.  **Save** that configuration.
6.  Now back at **Sessions**, you can **Start a session** with any server with the SSM agent and access to the SSM Service.
    1.  curl http://169.254.169.254/latest/meta-data/instance-id
    2.  curl http://169.254.169.254/latest/meta-data/security-groups
    3.  curl http://169.254.169.254/latest/meta-data/iam/security-credentials/SharedServerConnectivityRole
7.  **Terminate** the connection.
8.  Checking **Session History** you will see the **Output Location** of your log.
9.  Look at the **CloudWatch Logs** of your session and see what commands you typed.

Let’s go back to the presentation.

## Clean up:

1.  **Empty** and **Delete** your **fdn203-demo-bucket-{myname}** bucket
2.  **Empty** and **Delete** your **fdn203-ssmlogs-bucket-{myname}** bucket
    *  If you got this far
3.  Turn off **Config**
4.  Empty and Delete your **Config Bucket**
5.  Delete your **CloudTrail Trail**
6.  Disable **GuardDuty**
7.  In **VPC**, go to the **NACLs** you created and **disassociate** them from any subnets.
8.  Now **Delete** the **NACLs**.
9.  In CloudFormation **Delete your Stack**
    *  Wait until this is complete
