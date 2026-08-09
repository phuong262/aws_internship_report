---
title : "Kiểm thử hệ thống"
date : 2024-01-01
weight : 5
chapter : false
pre : " <b> 5.5 </b> "
---

Sau khi hoàn tất triển khai Frontend và Backend, nhóm tiến hành kiểm thử các chức năng chính của **Smart Docs AI** trên môi trường thực tế. Phạm vi kiểm thử tập trung vào hai luồng quan trọng nhất: xác thực người dùng và hỏi đáp trên tài liệu bằng RAG.

Các trường hợp kiểm thử được thực hiện trực tiếp trên giao diện web tại `smart-docs-jet.vercel.app`. Mỗi kết quả được đối chiếu với dữ liệu đầu vào để đánh giá tính chính xác và khả năng xử lý lỗi của hệ thống.

> **Dữ liệu demo:** sử dụng tệp `ke_hoach_gia_dinh_demo.pdf`, gồm kế hoạch chi tiêu, mua sắm, lịch gia đình và chuyến đi Vũng Tàu. Không sử dụng tài liệu chứa thông tin cá nhân thật.

### Nội dung thực hiện

1. [Kiểm thử xác thực](5.5.1-Authentication/)
2. [Kiểm thử tải lên tài liệu & RAG](5.5.2-Document-RAG/)
