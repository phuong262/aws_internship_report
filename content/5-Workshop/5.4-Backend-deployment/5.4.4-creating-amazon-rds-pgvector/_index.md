---
title: "RDS PostgreSQL and pgvector"
weight: 4
chapter: false
pre: "<b> 5.4.4 </b>"
---

## Initialize RDS PostgreSQL

1. Open the Amazon RDS Console.
![RDS Available](/images/5-Workshop/5.4-Backend-Deployment/rds.png)
2. Select **Create database**.
3. Select PostgreSQL, Full configuration method, Dev/Test template.
![RDS Available](/images/5-Workshop/5.4-Backend-Deployment/rds2.png)
4. Choose the appropriate configuration for the environment.
![RDS Available](/images/5-Workshop/5.4-Backend-Deployment/3.png)
5. Configuration
```text
Engine version = PostgreSQL 16.14-R2
DB identifier = chatbot-postgres-dev
Master username = postgres
Select Managed in AWS Secrets Manager
Database authentication = Password authentication
Instance type = db.t4g.micro
Storage type = General Purpose SSD (gp3)
```
![RDS Available](/images/5-Workshop/5.4-Backend-Deployment/4.png)
![RDS Available](/images/5-Workshop/5.4-Backend-Deployment/5.png)
![RDS Available](/images/5-Workshop/5.4-Backend-Deployment/6.png)

6. Select the created VPC, DB Subnet Group and Security Group.
![RDS Available](/images/5-Workshop/5.4-Backend-Deployment/7.png)
![RDS Available](/images/5-Workshop/5.4-Backend-Deployment/8.png)
7. Create database and wait for **Available** status.
![RDS Available](/images/5-Workshop/5.4-Backend-Deployment/9.png)

## Lambda initializes database, activates pgvector

1. Power shell run command:
```text
New-Item -ItemType Directory -Force package

py -m pip install pg8000 -t .\package

Copy-Item .\lambda_function.py .\package\

Compress-Archive `
    -Path .\package\* `
    -DestinationPath .\rds-init-dev.zip `
    -Force
```
2. Create Lambda rds-init-dev

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
![pgvector enabled](/images/5-Workshop/5.4-Backend-Deployment/10.png)