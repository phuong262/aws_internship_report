---

title : "Creating an IAM User"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.2.3. </b> "
-------------------------

### 1. Search for IAM in the AWS Console Search Bar

![findIAM](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/findIAM.png)

### 2. Create an IAM User Group

* In the **IAM user groups** interface:

  * Select **Create group**.

![IAMUserGroup](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/IAMUserGroup.png)

* In the **Create user group** interface:

  * **User group name**: Enter `AdminGroup`.
  * **Attach permissions policies - Optional**: Search for `AdministratorAccess` and select it.

![createUserGroup](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/createUserGroup.png)

* Scroll to the bottom of the page and select **Create user group**.

![createUserGroupNext](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/createUserGroupNext.png)

* Verify that the IAM user group was created successfully.

![createUserGroupSuccess](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/createUserGroupSuccess.png)

### 3. Create IAM Users

* In the **IAM users** interface:

  * Select **Create user**.

![IAMUser](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/IAMUser.png)

* In the **Specify user details** interface:

  * **User name**: Enter `dev1`.
  * Select **Provide user access to the AWS Management Console - optional**.
  * Then, select **Next**.

![specify](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/specify.png)

* In the **Set permissions** interface:

  * Select **Add user to group**.
  * **User groups**: Select the newly created **AdminGroup**.
  * Then, select **Next**.

![setPermission](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/setPermission.png)

* In the **Review and create** interface:

  * Review the IAM user information.
  * Select **Create user**.

![reviewAndCreate](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/reviewAndCreate.png)

* In the **Retrieve password** interface:

  * **Note:** Select **Download .csv file** to save the login credentials.
  * Then, select **Return to user list**.

![success](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/success.png)

### 4. Continue Creating Accounts

* Repeat steps **2** and **3** to create additional IAM users with administrator permissions and provide the accounts to the team members for the project workshop.
