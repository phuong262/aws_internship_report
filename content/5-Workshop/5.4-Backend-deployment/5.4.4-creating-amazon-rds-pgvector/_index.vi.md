---
title: "RDS PostgreSQL và pgvector"
weight: 3
chapter: false
pre: "<b>5.4.4. </b>"
---

## 5.4.4.1. Khởi tạo RDS PostgreSQL

1. Mở Amazon RDS Console.
![RDS Available](/images/2.RDS/rds.png)
2. Chọn **Create database**.
3. Chọn PostgreSQL, Phương thức Full configration, mẫu Dev/Test.
![RDS Available](/images/2.RDS/rds2.png)
4. Chọn cấu hình phù hợp với môi trường.
![RDS Available](/images/2.RDS/3.png)
5. Cấu hình
```text
Engine version = PostgreSQL 16.14-R2
DB identifier = chatbot-postgres-dev
Master username = postgres
Select Managed in AWS Secrets Manager
Database authentication = Password authentication
Instance type = db.t4g.micro
Storage type = General Purpose SSD (gp3)
```
![RDS Available](/images/2.RDS/4.png)
![RDS Available](/images/2.RDS/5.png)
![RDS Available](/images/2.RDS/6.png)

6. Chọn VPC, DB Subnet Group và Security Group đã tạo.
![RDS Available](/images/2.RDS/7.png)
![RDS Available](/images/2.RDS/8.png)
7. Tạo database và chờ trạng thái **Available**.
![RDS Available](/images/2.RDS/9.png)

## 5.3.2. Lambda khởi tạo database, kích hoạt pgvector

1. Power shell chạy lệnh:
```text
New-Item -ItemType Directory -Force package

py -m pip install pg8000 -t .\package

Copy-Item .\lambda_function.py .\package\

Compress-Archive `
    -Path .\package\* `
    -DestinationPath .\rds-init-dev.zip `
    -Force
```
2. Tạo Lambda rds-init-dev

```text
def get_database_secret() -> dict[str, Any]:
    """Đọc username/password của RDS từ AWS Secrets Manager."""
    secret_arn = os.environ.get("DB_SECRET_ARN")

    if not secret_arn:
        raise ValueError(
            "Thiếu environment variable DB_SECRET_ARN"
        )

    response = secrets_client.get_secret_value(
        SecretId=secret_arn
    )

    secret_string = response.get("SecretString")

    if not secret_string:
        raise ValueError(
            "Secret không chứa SecretString"
        )

    secret = json.loads(secret_string)

    # Chỉ log tên khóa, tuyệt đối không log username/password.
    print(
        "Secret keys:",
        sorted(secret.keys()),
    )

    return secret

def create_database_objects(cursor: Any) -> None:
    """Bật pgvector và tạo bảng/index cho document chunks."""
    cursor.execute(
        "CREATE EXTENSION IF NOT EXISTS vector;"
    )

    cursor.execute(
        """
        CREATE TABLE IF NOT EXISTS document_chunks (
            id UUID PRIMARY KEY,

            document_id VARCHAR(100) NOT NULL,
            user_id VARCHAR(100) NOT NULL,
            session_id VARCHAR(100),

            file_name VARCHAR(255) NOT NULL,
            page_number INTEGER,
            chunk_index INTEGER NOT NULL,

            content TEXT NOT NULL,

            embedding VECTOR(1024) NOT NULL,

            embedding_model VARCHAR(150)
                NOT NULL
                DEFAULT 'amazon.titan-embed-text-v2:0',

            metadata JSONB
                NOT NULL
                DEFAULT '{}'::jsonb,

            created_at TIMESTAMPTZ
                NOT NULL
                DEFAULT CURRENT_TIMESTAMP,

            CONSTRAINT uq_document_chunk
                UNIQUE (document_id, chunk_index)
        );
        """
    )

    cursor.execute(
        """
        CREATE INDEX IF NOT EXISTS
            idx_document_chunks_document_id
        ON document_chunks(document_id);
        """
    )

    cursor.execute(
        """
        CREATE INDEX IF NOT EXISTS
            idx_document_chunks_user_id
        ON document_chunks(user_id);
        """
    )

    cursor.execute(
        """
        CREATE INDEX IF NOT EXISTS
            idx_document_chunks_embedding_hnsw
        ON document_chunks
        USING hnsw (
            embedding vector_cosine_ops
        );
        """
    )
```
3. Commit transaction results.
![pgvector enabled](/images/2.RDS/10.png)

## 5.3.3. Tạo bảng documents

| Table | Partition key | Sort key |
|---|---|---|
| ChatHistory-dev| sessionId (S)| messageKey (S)|
| Documents-dev | documentId (S)| -|
| Users |userId|- | 
1. Mở AWS DynamoDb
2. Chọn Create Table, thiết lập đúng các key của từng table.
![RDS tables](/images/2.RDS/12.png)
3. Chọn Create
![RDS tables](/images/2.RDS/11.png)
 
## 5.3.4. Tạo bảng document_chunks

```sql
CREATE TABLE IF NOT EXISTS document_chunks (
    id BIGSERIAL PRIMARY KEY,
    document_id VARCHAR(100) NOT NULL,
    user_id VARCHAR(100),
    file_name TEXT,
    page_number INTEGER,
    chunk_index INTEGER NOT NULL,
    content TEXT NOT NULL,
    embedding VECTOR(1024) NOT NULL,
    embedding_model VARCHAR(100),
    metadata JSONB,
    created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
    UNIQUE (document_id, chunk_index)
);
```

> **Lưu ý:** Điều chỉnh tên và kiểu cột theo đúng schema thực tế của dự án nếu có khác biệt.

<!--
![RDS tables](/images/workshop/09-rds-tables.png)
-->

## 5.3.5. Lambda khởi tạo database

Lambda khởi tạo thực hiện:

1. Đọc cấu hình kết nối.
2. Kết nối PostgreSQL.
3. Kích hoạt `pgvector`.
4. Tạo các bảng nếu chưa tồn tại.
5. Commit transaction.
6. Trả về kết quả JSON.

> **Ảnh minh chứng:** Chụp Test Event thành công và CloudWatch Logs. Không để lộ password.

<!--
![RDS init Lambda result](/images/workshop/10-rds-init-lambda.png)
-->

## 5.3.6. Kiểm tra dữ liệu

```sql
SELECT table_name
FROM information_schema.tables
WHERE table_schema = 'public';
```

```sql
SELECT COUNT(*) AS total_vectors
FROM document_chunks;
```

```sql
SELECT document_id, file_name, page_number, chunk_index, content
FROM document_chunks
LIMIT 5;
```

<!--
![RDS sample data](/images/workshop/11-rds-sample-data.png)
-->

