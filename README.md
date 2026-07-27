# Secure File Share MVP

Encrypted file sharing with local storage, MySQL metadata, and Ethereum transaction records.

## Architecture

```
React (Vite)
   │
   ▼
Node.js + Express
   │
   ├── MySQL          Users, Files
   ├── uploads/       AES-encrypted files
   └── AES Encryption
          │
          ▼
Ethereum Smart Contract (Hardhat)
   Owner, Receiver, Filename, Timestamp, Hash
```

## Pages

1. **Login** — JWT authentication
2. **Register** — Account + MetaMask wallet
3. **Dashboard** — Stats and recent uploads
4. **Upload** — Encrypt → store → DB → blockchain
5. **Shared Files** — Download and decrypt

## Prerequisites

- Node.js 18+
- MySQL 8+
- MetaMask browser extension

## Setup

### 1. Database

```bash
mysql -u root -p < database/setup.sql
```

### 2. Backend

```bash
cd backend
cp .env.example .env
# Edit .env with your MySQL credentials and secrets
npm install
npm start
```

Server runs at `http://localhost:5000`

### 3. Smart Contract

```bash
# From project root
npm install
npx hardhat node
```

In a new terminal:

```bash
npx hardhat run scripts/deploy.js --network localhost
```

Copy the printed `CONTRACT_ADDRESS` into `backend/.env`.

### 4. Frontend

```bash
cd frontend
npm install
npm run dev
```

App runs at `http://localhost:3000`

### 5. MetaMask

1. Add Hardhat network: `http://127.0.0.1:8545`, Chain ID `31337`
2. Import a test account from Hardhat node output (private key)
3. Connect wallet on Register and Upload pages

## MVP Workflow

**User 1 (Sender)**

1. Register with MetaMask wallet
2. Login
3. Upload file → AES encrypted → stored in `backend/uploads/`
4. Metadata saved to MySQL
5. MetaMask calls `shareFile(receiver, filename, hash)` on contract

**User 2 (Receiver)**

1. Register with a different MetaMask account
2. Login
3. Open Shared Files
4. Download → server decrypts → original file returned

## API Endpoints

| Method | Route | Description |
|--------|-------|-------------|
| POST | `/api/auth/register` | Register user |
| POST | `/api/auth/login` | Login |
| GET | `/api/auth/users` | List users (for receiver dropdown) |
| POST | `/api/files/upload` | Upload & encrypt file |
| GET | `/api/files/my-uploads` | Sender's uploads |
| GET | `/api/files/shared` | Files shared with current user |
| GET | `/api/files/download/:id` | Download decrypted file |
| GET | `/api/files/stats` | Dashboard stats |

## Smart Contract

```solidity
function shareFile(address receiver, string filename, string hash)
function getAllTransactions() returns (Transaction[])
```

Each transaction stores: owner, receiver, filename, hash, timestamp.

## Project Structure

```
MVP/
├── backend/           Express API + AES encryption
├── frontend/          React UI (5 pages)
├── contracts/         FileShare.sol
├── scripts/           Hardhat deploy script
├── database/          MySQL setup SQL
└── hardhat.config.js
```

## Screenshots

_Add screenshots of Login, Dashboard, Upload, and Shared Files pages here._

## Tech Stack

**Backend:** express, bcrypt, jsonwebtoken, multer, crypto, mysql2, ethers, cors

**Frontend:** react, axios, ethers, react-router-dom, vite

**Blockchain:** hardhat, @openzeppelin/contracts
