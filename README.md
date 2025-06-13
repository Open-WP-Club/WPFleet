# WPFleet - Docker-based WordPress Multi-Site Hosting

WPFleet is a production-ready, scalable solution for hosting multiple WordPress sites on a single server using Docker, FrankenPHP, MariaDB, and Redis.

## Features

- **FrankenPHP** - Modern PHP application server with built-in Caddy
- **Automatic SSL** - Let's Encrypt certificates via Caddy
- **Shared MariaDB** - Single database server with isolated databases
- **Redis Caching** - Object cache for improved performance
- **WP-CLI** - Built-in WordPress command-line interface
- **Security First** - Isolated containers, security headers, and best practices
- **Resource Management** - CPU and memory limits per site
- **Easy Management** - Simple scripts for common tasks
- **Migration Support** - Import WordPress sites from archives and database dumps

## Requirements

- Docker Engine 20.10+
- Docker Compose 2.0+
- Linux server (Ubuntu 20.04+ recommended)
- Domain names pointing to your server
- Ports 80 and 443 available
- Minimum 2GB RAM (4GB+ recommended)
- SSH access for management

## Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/Open-WP-Club/wpfleet
cd wpfleet
```

### 2. Configure Environment

```bash
cp .env.example .env
nano .env  # Edit with your values
```

**Important**: Generate secure passwords for `MYSQL_ROOT_PASSWORD`, `MYSQL_PASSWORD`, and `WP_ADMIN_PASSWORD`.

### 3. Make Scripts Executable

```bash
chmod +x scripts/*.sh
```

### 4. Start Core Services

```bash
docker-compose up -d mariadb redis
```

### 5. Add Your First Site

Choose from three installation modes:

**Clean WordPress Installation (default)**

```bash
./scripts/site-manager.sh add example.com
```

**Skip Installation (infrastructure only)**

```bash
./scripts/site-manager.sh add example.com --skip-install
```

**Import Existing Site**

```bash
./scripts/site-manager.sh add example.com --import-from
```

## Site Installation Modes

WPFleet supports three different ways to add WordPress sites:

### Clean Installation (Default)

```bash
./scripts/site-manager.sh add example.com
```

**Best for:** New WordPress sites from scratch

**What it does:**

- Downloads latest WordPress core
- Creates fresh database
- Installs and configures Redis Object Cache
- Sets up optimized `wp-config.php`
- Creates admin user with generated password
- Applies security and performance settings

### Skip Installation

```bash
./scripts/site-manager.sh add example.com --skip-install
```

**Best for:** Custom installations, advanced users, or manual migrations

**What it does:**

- Creates database and file directories
- Sets up Caddy routing and SSL
- Shows database connection information
- **You handle:** WordPress installation, configuration, file uploads

**Output example:**

```
Database Information:
  Database Name: wp_example_com
  Database User: wpfleet
  Database Password: your_password
  Database Host: mariadb (or localhost:3306 from host)

Site Information:
  Files Directory: /path/to/wpfleet/data/wordpress/example.com
  Container Path: /var/www/html/example.com
  Site URL: https://example.com
```

### Import Existing Site

```bash
./scripts/site-manager.sh add example.com --import-from
```

**Best for:** Migrating existing WordPress sites to WPFleet

**What it does:**

- Creates infrastructure (database, directories, routing)
- Prompts for database backup file (`.sql` or `.sql.gz`)
- Prompts for files archive (`.tar.gz` or `.zip`)
- Imports database and extracts files
- Updates `wp-config.php` with new database settings
- Adds Redis configuration

**Migration Process:**

1. Export your existing site's database
2. Create archive of WordPress files
3. Run the import command
4. Provide paths when prompted
5. Site becomes immediately available

**Supported formats:**

- Database: `.sql`, `.sql.gz`
- Files: `.tar.gz`, `.zip`

## Migration Guide

### Migrating from Another Host

**Step 1: Prepare your existing site**

```bash
# On your old server
mysqldump -u username -p database_name > site_backup.sql
tar -czf site_files.tar.gz /path/to/wordpress/
```

**Step 2: Transfer files to WPFleet server**

```bash
scp site_backup.sql site_files.tar.gz user@wpfleet-server:/path/to/wpfleet/
```

**Step 3: Import to WPFleet**

```bash
./scripts/site-manager.sh add yourdomain.com --import-from
# When prompted:
# Database file: ./site_backup.sql
# Files archive: ./site_files.tar.gz
```

**Step 4: Update DNS**

- Point your domain to the new server
- WPFleet will automatically get SSL certificates

### Migrating Between WPFleet Sites

Use the built-in backup and restore functionality:

```bash
# Export from existing site
./scripts/backup.sh site olddomain.com

# Import to new site structure
./scripts/site-manager.sh add newdomain.com --import-from
# Use the backup files from previous step
```

### URL Search and Replace

If you need to change URLs after migration:

```bash
./scripts/db-manager.sh search-replace newdomain.com 'http://olddomain.com' 'https://newdomain.com'
```

## Usage

### Managing Sites

#### Add a new site (clean WordPress installation)

```bash
./scripts/site-manager.sh add example.com
```

This creates a fresh WordPress installation with:

- Latest WordPress core
- Clean database
- Redis Object Cache plugin
- Optimized configuration
- Admin user with generated password

#### Skip WordPress installation (infrastructure only)

```bash
./scripts/site-manager.sh add example.com --skip-install
```

This creates only the infrastructure:

- Database and directories
- Caddy routing configuration
- Shows database

## Scaling Considerations

### Vertical Scaling

- Increase memory/CPU limits in `.env`
- Tune MariaDB buffer pool size
- Increase Redis memory limit

### Horizontal Scaling

- Use external object storage for media files
- Implement CDN for static assets
- Consider separate database server
- Use load balancer for multiple instances

## Contributing

1. Fork the repository
2. Create your feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -am 'Add my feature'`
4. Push to the branch: `git push origin feature/my-feature`
5. Submit a pull request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

- Create an issue for bug reports or feature requests
- Check existing issues before creating new ones
- Provide detailed information for troubleshooting

## Acknowledgments

- [FrankenPHP](https://frankenphp.dev/) - Modern PHP application server
- [Caddy](https://caddyserver.com/) - Automatic HTTPS server
- [WordPress](https://wordpress.org/) - The world's most popular CMS
- [Docker](https://www.docker.com/) - Container platform
