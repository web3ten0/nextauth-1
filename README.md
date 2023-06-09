# nextauth-1

## プロジェクトの作成・設定

Next.js の新規プロジェクトを作成し、各種インストールをします。

```shell
npx create-next-app@latest --typescript
...
cd nextauth-1
npm i prisma -D
npm i next-auth @prisma/client @next-auth/prisma-adapter
npx prisma init --datasource-provider sqlite
```

Prisma のモデルを作成します。

prisma/schema.prisma

```prisma
model Account {
  id                String  @id @default(uuid())
  userId            String
  type              String
  provider          String
  providerAccountId String
  refresh_token     String?
  access_token      String?
  expires_at        Int?
  token_type        String?
  scope             String?
  id_token          String?
  session_state     String?

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([provider, providerAccountId])
}

model Session {
  id           String   @id @default(uuid())
  sessionToken String   @unique
  userId       String
  expires      DateTime
  user         User     @relation(fields: [userId], references: [id], onDelete: Cascade)
}

model User {
  id            String    @id @default(uuid())
  name          String?
  email         String?   @unique
  emailVerified DateTime?
  image         String?
  accounts      Account[]
  sessions      Session[]
}

model VerificationToken {
  identifier String
  token      String   @unique
  expires    DateTime

  @@unique([identifier, token])
}
```

migrate します。

```shell
npx prisma migrate dev --name init
```
