FROM node:20-slim

WORKDIR /app

# ✅ Prisma يحتاج OpenSSL داخل Debian slim
RUN apt-get update -y && apt-get install -y openssl ca-certificates \
  && rm -rf /var/lib/apt/lists/*

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 4000

CMD ["sh", "-c", "npm run prisma:generate && npm run prisma:migrate:deploy && npm run start"]
