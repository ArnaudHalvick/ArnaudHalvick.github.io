# 🎯 SQL Playground - Database Architecture for SEO

> **Interactive SQL learning platform demonstrating SEO-friendly database design principles for large-scale e-commerce catalogs.**

[![Next.js](https://img.shields.io/badge/Next.js-15+-black?style=flat&logo=next.js)](https://nextjs.org/)
[![Supabase](https://img.shields.io/badge/Supabase-Database-green?style=flat&logo=supabase)](https://supabase.com/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.2+-blue?style=flat&logo=typescript)](https://www.typescriptlang.org/)

**Video Presentation:** [Watch on YouTube](https://youtu.be/uPUUJFIIJhc)

## 🔍 SEO Relevance - Why This Matters for Technical SEO

While built as a learning platform, this project demonstrates **critical database design principles** essential for **SEO-friendly e-commerce catalogs** at scale.

### Technical SEO Applications

**🏗️ URL-Friendly Schema Design**
- Product categories, filters, and attributes structured to support clean URL paths
- Semantic routing that enables `/category/subcategory/product` instead of `/product?id=123`
- Database relationships that prevent parameter explosion in filter combinations

**⚡ Query Optimization for Core Web Vitals**
- Efficient database queries directly impact site performance
- Optimized queries reduce Time to First Byte (TTFB) - a Core Web Vitals signal
- Proper indexing strategies that scale to 10k+ product catalogs without performance degradation

**🔢 Scalable Architecture**
- Database patterns proven to work with large-scale e-commerce sites
- Table relationships designed to avoid N+1 query problems
- Data modeling that supports both search functionality and SEO requirements

**🎯 Faceted Navigation Support**
- Schema enables SEO-friendly filter combinations
- Prevents crawl traps from infinite URL variations
- Supports strategic parameter classification (stable vs. unstable filters)

## 🌟 Why I Built This

I wanted to learn SQL but couldn't find a platform that made it **fun, interactive, and practical**. Most resources were either too theoretical or lacked real-world scenarios.

As a **Full-Stack SEO Expert** with 7+ years of experience, I also wanted to demonstrate how database architecture decisions impact SEO performance at scale.

This playground shows:
- 🎓 **Interactive SQL learning** with realistic e-commerce data
- 🛠️ **SEO-relevant database design** for large catalogs
- 🔗 **Modern tech stack** (Next.js 15, TypeScript, Supabase)
- 🎮 **Practical scenarios** including data quality challenges

## ✨ Features

### 🎯 Interactive Learning

- **SQL Editor** with syntax highlighting and auto-completion
- **Real-time query execution** with instant results
- **20+ curated exercises** from beginner to advanced
- **Database schema explorer** to understand table relationships

### 🎮 Challenge Mode

- **Data quality challenges** with intentional errors to find and fix
- **AI-powered challenge generator** with custom difficulty levels
- **Realistic scenarios** like e-commerce data validation
- **Configurable error rates** (2-25%) for progressive difficulty

### 🛠️ Flexible Database Setup

- **One-click database setup** with multiple configurations
- **Realistic datasets** with 50-2000+ records
- **Challenge databases** with data quality issues
- **Custom configurations** for specific learning goals

### 🎨 Modern UI/UX

- **Clean, responsive design** that works on all devices
- **Dark/light mode** support
- **Intuitive navigation** with tabs and organized sections
- **Real-time feedback** and error handling

## 🚀 Quick Start

1. **Clone the repository**

   ```bash
   git clone https://github.com/ArnaudHalvick/sql-playground.git
   cd sql-playground
   ```

2. **Install dependencies**

   ```bash
   npm install
   ```

3. **Set up environment** ⚠️ **Service role key required!**

   ```bash
   # Copy the template
   cp .env.local.example .env.local

   # Fill in your Supabase credentials:
   NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
   NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
   SUPABASE_SERVICE_ROLE_KEY=your_service_role_key  # Essential for DB setup!
   ```

4. **Start the app**

   ```bash
   npm run dev
   ```

5. **Setup database** (click "Setup Database" in the app or use CLI)
   ```bash
   npm run db:setup-medium  # Clean dataset
   npm run db:challenge-light  # With data quality issues
   ```

> **💡 Why service role key?** The app needs admin privileges to create tables, insert sample data, and set up the query execution function. Without it, database setup won't work!

## 🎯 Learning Path

### 🟢 Beginner (Start Here!)

- Basic SELECT queries and filtering
- Understanding table relationships
- Simple JOINs and aggregations

### 🟡 Intermediate (Level Up!)

- Complex JOINs and subqueries
- GROUP BY and HAVING clauses
- Date functions and data analysis

### 🔴 Advanced (Master Level!)

- Window functions and CTEs
- Data quality validation
- Performance optimization
- Real-world problem solving

## 🎮 Challenge Examples

### Find Invalid Emails

```sql
SELECT first_name, last_name, email
FROM users
WHERE email NOT LIKE '%@%.com'
   OR email NOT LIKE '%@%.net'
   OR email NOT LIKE '%@%.org';
```

### Detect Pricing Anomalies

```sql
SELECT name, price,
  CASE
    WHEN price < 0 THEN 'Negative price'
    WHEN price = 0 THEN 'Zero price'
    WHEN price > 10000 THEN 'Suspiciously high'
  END as issue
FROM products
WHERE price <= 0 OR price > 10000;
```

### Delivery Logic Violations

```sql
SELECT id, status, delivery_date
FROM orders
WHERE status = 'delivered' AND delivery_date IS NULL;
```

## 🛠️ Tech Stack

- **Frontend**: Next.js 15, React, TypeScript
- **Database**: Supabase (PostgreSQL)
- **Styling**: Tailwind CSS, shadcn/ui
- **Editor**: CodeMirror with SQL syntax highlighting
- **Icons**: Lucide React

## 📊 Database Schema

Complete e-commerce database with:

- **Users** (customers with profiles and location data)
- **Products** (catalog with categories and pricing)
- **Orders** (purchase history with status tracking)
- **Order Items** (detailed line items and quantities)
- **Countries & Cities** (geographic location data)

Perfect for learning JOINs, aggregations, and **SEO-relevant query patterns**!

## 🎯 Use Cases

- **Technical SEO Specialists** understanding database impact on performance
- **E-commerce Developers** building SEO-friendly catalogs
- **Students & Developers** learning SQL fundamentals
- **Data Analysts** practicing query optimization
- **Job Seekers** preparing for technical interviews

## 🔗 Related Work

This project is part of my **Full-Stack SEO Expert** portfolio:

- **[SEO Workshop](https://github.com/ArnaudHalvick/SEO-Workshop)** - Complete technical + strategic SEO platform
- **[Portfolio](https://arnaudhalvick.github.io/)** - 12,000+ hours on Upwork, 100% Job Success, Top Rated

## 🤝 Contributing

Found a bug? Have an idea? Contributions are welcome!

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## 📝 License

MIT License - feel free to use this project for learning and teaching!

## 🙏 Acknowledgments

Built with love for the SQL learning community. Special thanks to:

- **Supabase** for the amazing database platform
- **shadcn/ui** for the beautiful component library
- **CodeMirror** for the powerful editor
- **Next.js** team for the incredible framework

---

**⭐ Star this repo if it helped you learn SQL!**

**Built by [Arnaud Halvick](https://arnaudhalvick.github.io/) - Full-Stack SEO Expert**

📧 halvick.arnaud@gmail.com | 💼 [Hire on Upwork](https://www.upwork.com/freelancers/~017740c356da4ab81f) (12k+ hours, 100% Job Success, Top Rated)
