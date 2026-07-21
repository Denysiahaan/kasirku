# 🏪 KasirKu - Sistem Kasir Digital untuk UMKM Indonesia

![KasirKu Banner](public/KasirKu.svg)

**KasirKu** adalah aplikasi Point of Sale (POS) modern berbasis cloud yang dirancang khusus untuk membantu UMKM Indonesia mengelola bisnis mereka dengan lebih efisien. Dengan arsitektur multi-tenant, KasirKu memastikan data bisnis Anda aman dan terisolasi.

## ✨ Fitur Utama

### 🛒 Manajemen Transaksi
- **Sistem POS Interaktif** - Interface yang intuitif untuk mencatat penjualan dengan cepat
- **Metode Pembayaran Lengkap** - Support tunai dan QRIS
- **Nomor Antrian Otomatis** - Queue management untuk order pelanggan
- **Perhitungan Kembalian Otomatis** - Untuk transaksi tunai

### 📊 Manajemen Menu & Produk
- **CRUD Menu Items** - Tambah, edit, dan hapus produk dengan mudah
- **Katalog Produk** - Kelola daftar menu dan harga
- **Real-time Updates** - Perubahan langsung tersinkronisasi

### 📈 Laporan & Analitik
- **Dashboard Interaktif** - Visualisasi data penjualan real-time
- **Laporan Keuangan** - Tracking pendapatan harian dan bulanan
- **Best-Selling Analysis** - Identifikasi produk terlaris
- **Transaction History** - Riwayat lengkap semua transaksi

### 🔐 Keamanan & Multi-Tenant
- **Row-Level Security (RLS)** - Isolasi data antar pengguna
- **Tenant Isolation** - Setiap bisnis memiliki data terpisah
- **Google OAuth** - Login cepat dan aman
- **Session Management** - Keamanan authenticated sessions

### 🎨 User Experience
- **Modern UI/UX** - Design dengan glassmorphism dan gradient effects
- **Responsive Design** - Mobile-friendly untuk semua perangkat
- **Interactive Demo** - Demo POS terminal langsung di landing page
- **Dark/Light Theme** - Support untuk berbagai preferensi visual

## 🛠️ Tech Stack

### Frontend
- **[Next.js 15](https://nextjs.org/)** - React framework dengan App Router
- **[React 19](https://react.dev/)** - UI library
- **[Tailwind CSS](https://tailwindcss.com/)** - Utility-first CSS framework
- **[Lucide Icons](https://lucide.dev/)** - Beautiful icon set

### Backend & Database
- **[Supabase](https://supabase.com/)** - Backend-as-a-Service
  - PostgreSQL Database
  - Authentication & Authorization
  - Row-Level Security (RLS)
  - Real-time subscriptions
  
### Deployment & Hosting
- **[Vercel](https://vercel.com/)** - Deployment platform
- **Environment Variables** - Secure configuration management

## 🚀 Getting Started

### Prerequisites

Pastikan Anda telah menginstall:
- [Node.js](https://nodejs.org/) (v18 atau lebih baru)
- [npm](https://www.npmjs.com/) atau [yarn](https://yarnpkg.com/)
- [Git](https://git-scm.com/)

### Installation

1. **Clone repository**
   ```bash
   git clone https://github.com/yourusername/kasirku.git
   cd kasirku/kasirku
   ```

2. **Install dependencies**
   ```bash
   npm install
   # atau
   yarn install
   ```

3. **Setup environment variables**
   
   Buat file `.env.local` di root folder:
   ```env
   NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
   NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
   ```

4. **Run database migrations**
   ```bash
   node scripts/run-migrations.js
   # atau di Unix/Mac
   ./scripts/run-migrations.sh
   ```

5. **Start development server**
   ```bash
   npm run dev
   # atau
   yarn dev
   ```

6. **Open browser**
   
   Buka [http://localhost:3000](http://localhost:3000) untuk melihat aplikasi

## 📁 Project Structure

```
kasirku/
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── auth/              # Authentication pages
│   │   ├── dashboard/         # Dashboard & main app
│   │   │   ├── menu/         # Menu management
│   │   │   ├── order/        # Order management
│   │   │   └── laporan/      # Reports
│   │   ├── globals.css       # Global styles
│   │   ├── layout.js         # Root layout
│   │   └── page.tsx          # Landing page
│   ├── components/            # React components
│   │   ├── auth/             # Auth-related components
│   │   ├── home/             # Landing page sections
│   │   └── layout/           # Layout components
│   ├── lib/                   # Utilities & helpers
│   │   ├── supabase/         # Supabase client configs
│   │   └── tenant.ts         # Tenant management
│   └── types/                 # TypeScript type definitions
├── public/                    # Static assets
│   └── KasirKu.svg           # Logo
├── migrations/                # Database migrations
│   ├── 001_initial_schema.sql
│   └── 002_rls_policies.sql
├── docs/                      # Documentation
│   └── database-schema.md
├── .env.local                 # Environment variables (gitignored)
├── next.config.mjs           # Next.js configuration
├── tailwind.config.ts        # Tailwind CSS configuration
└── package.json              # Dependencies & scripts
```

## 🗄️ Database Schema

KasirKu menggunakan PostgreSQL dengan Supabase. Schema utama meliputi:

### Tables
- **`menu_items`** - Daftar produk/menu
- **`orders`** - Data pesanan pelanggan
- **`order_items`** - Detail item dalam pesanan
- **Tenant Isolation** - Setiap tabel memiliki `tenant_id` untuk isolasi data

### Security
- **Row-Level Security (RLS)** - Aktif di semua tabel
- **Tenant-based Policies** - User hanya bisa akses data mereka sendiri
- **Automatic Timestamps** - `created_at`, `updated_at`

Lihat [Database Schema Documentation](docs/database-schema.md) untuk detail lengkap.

## 🎨 Design System

### Color Palette
```css
--color-brand-600: #1a6df5;  /* Primary brand color */
--color-accent-500: #0ea5e9; /* Accent color */
```

### Effects
- **Glassmorphism** - `backdrop-blur-xl` dengan background semi-transparan
- **Gradients** - Smooth color transitions
- **Aura Effects** - Silver rotating border animations
- **Shadows** - Layered shadows untuk depth

## 📱 Features Demo

### Landing Page
- Hero section dengan demo POS interaktif
- Features showcase dengan glassmorphism cards
- About section dengan financial performance preview
- FAQ accordion
- Pricing/CTA section dengan aura effect
- Responsive navbar dengan hamburger menu

### Authentication
- Email/password login & register
- Google OAuth integration
- Password strength indicator
- Show/hide password toggle
- Form validation

### Dashboard
- Welcome header dengan user info
- Stats overview cards
- Quick action buttons
- Account information panel
- Getting started guide

### Menu Management
- Add/Edit/Delete menu items
- Product catalog display
- Real-time updates
- Form validation

### Order Management
- Interactive POS interface
- Cart management
- Payment method selection (Cash/QRIS)
- Change calculation
- Queue number generation
- Order confirmation

### Reports
- Daily/Monthly revenue charts
- Transaction statistics
- Best-selling products
- Revenue trends

## 🔒 Security Features

- ✅ Row-Level Security (RLS) policies
- ✅ Tenant isolation per user
- ✅ Secure authentication with Supabase Auth
- ✅ Environment variable protection
- ✅ CSRF protection
- ✅ Input validation & sanitization
- ✅ Secure session management

## 📦 Scripts

```json
{
  "dev": "next dev",           // Start development server
  "build": "next build",       // Build for production
  "start": "next start",       // Start production server
  "lint": "next lint"          // Run ESLint
}
```

## 🌐 Deployment

### Deploy to Vercel

1. Push code ke GitHub
2. Import project di [Vercel](https://vercel.com)
3. Set environment variables
4. Deploy!

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/yourusername/kasirku)

### Environment Variables untuk Production

Pastikan set di Vercel dashboard:
- `NEXT_PUBLIC_SUPABASE_URL`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`

## 🤝 Contributing

Contributions are welcome! Silakan fork repository ini dan submit pull request.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📝 License

Distributed under the MIT License. See `LICENSE` for more information.

## 📧 Contact

**KasirKu Team** - [WhatsApp Support](https://wa.me/6283815396398)

Project Link: [https://github.com/yourusername/kasirku](https://github.com/yourusername/kasirku)

## 🙏 Acknowledgments

- [Next.js Documentation](https://nextjs.org/docs)
- [Supabase Documentation](https://supabase.com/docs)
- [Tailwind CSS](https://tailwindcss.com/)
- [Lucide Icons](https://lucide.dev/)
- [Vercel](https://vercel.com/)

---

<div align="center">
  <strong>Made with ❤️ for Indonesian UMKM</strong>
  <br>
  <sub>© 2026 KasirKu Indonesia. Dedicated to digitalizing Indonesian micro-businesses.</sub>
</div>

