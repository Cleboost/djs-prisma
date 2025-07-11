# @djs-core/prisma-plugin

Official plugin to integrate Prisma ORM with djs-core, providing simplified database management in your Discord.js applications.

## 🚀 Features

- **Automatic integration** : Automatic Prisma connection when the bot starts
- **Auto-generation** : Automatic Prisma client generation after build
- **TypeScript support** : Complete TypeScript types for `client.db`
- **Dependency management** : Automatic installation of Prisma dependencies
- **Clean shutdown** : Automatic database disconnection on shutdown

## 📦 Installation

```bash
npm install @djs-core/prisma-plugin
```

## 🔧 Configuration

### 1. Create your Prisma schema

Create a `prisma/schema.prisma` file in your project:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql" // or "mysql", "sqlite", etc.
  url      = env("DATABASE_URL")
}

// Your models here
model User {
  id    Int     @id @default(autoincrement())
  email String  @unique
  name  String?
}
```

### 2. Configure djs-core

In your djs-core configuration file:

```typescript
import { DjsCore } from "djs-core";
import { DjsPrismaPlugin } from "@djs-core/prisma-plugin";

const core = new DjsCore({
  plugins: [
    DjsPrismaPlugin({
      schemaPath: "./prisma/schema.prisma" // optional, default
    })
  ]
});
```

### 3. Environment variables

Add your database URL to your `.env` file:

```env
DATABASE_URL="postgresql://user:password@localhost:5432/mydb"
```

## 💻 Usage examples

#### Find users

```typescript
client.on(Events.InteractionCreate, async (interaction) => {
  if (!interaction.isChatInputCommand()) return;
  
  if (interaction.commandName === "users") {
    const users = await client.db.user.findMany({
      select: {
        id: true,
        name: true,
        email: true
      }
    });
    
    const userList = users.map(u => `${u.id}: ${u.name} (${u.email})`).join('\n');
    await interaction.reply(`Users:\n${userList}`);
  }
});
```

## 🔧 Configuration options

```typescript
DjsPrismaPlugin({
  schemaPath: "./prisma/schema.prisma" // Path to your Prisma schema
})
```

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `schemaPath` | `string` | `"./prisma/schema.prisma"` | Path to the Prisma schema file |

## 🛠️ Development

### Prisma client generation

The plugin automatically generates the Prisma client after each build. If you need to do it manually:

```bash
npx prisma generate
```

### Database migrations

```bash
# Create a migration
npx prisma migrate dev --name init

# Apply migrations in production
npx prisma migrate deploy
```

### Prisma Studio (optional)

To visualize and edit your data:

```bash
npx prisma studio
```

## 📋 Prerequisites

- Node.js 18+
- djs-core
- A database supported by Prisma (PostgreSQL, MySQL, SQLite, etc.)

## 🤝 Contributing

Contributions are welcome! Feel free to:

1. Fork the project
2. Create a branch for your feature
3. Commit your changes
4. Push to the branch
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License. See the `LICENSE` file for details.

## 🆘 Support

If you encounter issues:

1. Verify that your Prisma schema is valid
2. Make sure your database URL is correct
3. Check that all dependencies are installed
4. Consult the [Prisma documentation](https://www.prisma.io/docs)

---

**Note**: This plugin requires your project to use djs-core as the build framework. 