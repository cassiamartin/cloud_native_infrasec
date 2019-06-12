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

