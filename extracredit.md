## Extra Credit

There’s a way to log all commands sent to the instance as well. But first you have to create S3 buckets and CloudWatch Logs

1.    Go to **S3**.
2.    **Create a Bucket** called **fdn203-ssmlogs-bucket-{myname}**. Before you move on, turn on **Default Encryption** using **AWS-KMS** and the **aws/s3**
3.    Then **Grant Amazon S3 Log Delivery group write access to the bucket**.
4.    Go to **CloudWatch**.
5.    In **Logs** you must **Create log group**, called **fnd203-ssmlogs-logs**.
6.    Go to **IAM**.
7.    We will modify the **Role** called
8.    Expand the Inline Policy and click **Edit Policy**
9.    **Add additional permissions** including
         > 1.  **S3**
         >    1.  **Write**
         >    2.  **Bucket: Any**
         >    3.  **Object**: **Any**
         > 2.  **CloudWatch Logs**
         >    1.  **Write**
         >    2.  **Log Group: Any**
         >    3.  **Log Stream: Any**

10.    **Review the policy** and **Save Changes**
11.    If there are any errors, go to **Previous** and keep adding **Any** to the resources the policy requires defined.

_This can be more restrictive in a production environment._

12.    Now go back to **Systems Manager**, **Session Manager**.
13.    Let’s use **Preferences** to set up logging.
14.    **Edit** the settings to **Write session output** and choose the bucket called “**fdn203-demo-bucket-{myname}**”. (Don’t forget, S3 buckets must have unique names, so make sure to add your name at the end. They can also only be lower case letters, numbers, “-“, and “.”)
15.    Let’s also send the output to **Cloudwatch logs**, we can deselect **Encrypt Log Data**, and create a log group name “**fdn203-demo-bucket-{myname}**”.
         1.  In production I would not recommend storing unencrypted logs.
16.    **Save** that configuration.
17.    Now back at **Sessions**, you can **Start a session** with any server with the SSM agent and access to the SSM Service.
        > 1.  curl http://169.254.169.254/latest/meta-data/instance-id
        > 2.  curl http://169.254.169.254/latest/meta-data/security-groups
        > 3.  curl http://169.254.169.254/latest/meta-data/iam/security-credentials/SharedServerConnectivityRole
18.    **Terminate** the connection.
19.    Checking **Session History** you will see the **Output Location** of your log.
20.    Look at the **CloudWatch Logs** of your session and see what commands you typed.

Now that you've completed the extra credit, you can return to [the main lab and continue with cleanup](./README.md).
