---
title : "Tạo IAM User"
date : 2024-01-01 
weight : 3
chapter : false
pre : " <b> 5.2.3. </b> "
---
### 1. Tìm kiếm IAM trong thanh công cụ tìm kiếm AWS Console
![findIAM](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/findIAM.png)
### 2. Tạo IAM user groups
- Trong giao diện IAM user groups:
  - Chọn **Create group**
![IAMUserGroup](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/IAMUserGroup.png)
- Trong giao diện Create user group:
  - User group name: Nhập ```AdminGroup```
  - Attach permissions policies - Optional: Tìm ```AdministratorAccess``` và click chọn
![createUserGroup](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/createUserGroup.png)
  - Cuộn đến cuối trang và nhấn **Create user group**
![createUserGroupNext](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/createUserGroupNext.png)
- Kiểm tra kết quả tạo thành công IAM user group
![createUserGroupSuccess](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/createUserGroupSuccess.png)
### 3. Tạo IAM users
- Trong giao diện IAM users:
  - Chọn **Create user**
![IAMUser](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/IAMUser.png)
- Trong giao diện Specify user details
  - User name: Nhập ```dev1```
  - Click chọn **Provide user access to the AWS Management Console - optional**
  - Sau đó, chọn **Next**
![specify](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/specify.png)
- Trong giao diện Set permissions:
  - Chọn **Add user to group**
  - User groups: Chọn AdminGroup vừa tạo
  - Sau đó, nhấn **Next**
![setPermission](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/setPermission.png)
- Trong giao diện Review and create:
  - Kiểm tra lại thông tin IAM user
  - Nhấn **create user**
![reviewAndCreate](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/reviewAndCreate.png)
- Trong giao diện Retrieve password
  - Lưu ý Nhấn **Download .csv file**
  - Sau đó nhấn **Return to user list**
![success](/images/5-Workshop/5.2-Prerequisite/5.2.3-creating-an-IAM-user/success.png)
### 4. Tiếp tục tạo tài khoản
- Thực hiện lại các bước 2, 3 để tạo thêm các tài khoản IAM user với quyền admin để cung cấp cho các thành viên nhóm thực hành project
