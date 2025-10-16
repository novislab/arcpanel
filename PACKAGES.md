# ArcPanel - Recommended Packages

This document outlines the essential packages needed to build a high-performance, secure SSH web panel with ArcPanel.

---

## 🔐 SSH Connection Packages

### 1. **phpseclib/phpseclib** (Required - Core SSH)
**Purpose**: Pure PHP SSH2/SFTP library for SSH connections

**Why**:
- No native PHP SSH2 extension required
- Works with PHP 8.1+
- Actively maintained and secure
- Developer-friendly API
- Supports RSA, AES, SSH2, SFTP, X.509

**Installation**:
```bash
composer require phpseclib/phpseclib:~3.0
```

**Use Cases**:
- Connecting to remote servers via SSH
- Executing commands on remote servers
- File transfers (SFTP)
- SSH key management
- Terminal session management

**Example**:
```php
use phpseclib3\Net\SSH2;

$ssh = new SSH2('server.example.com');
if (!$ssh->login('username', 'password')) {
    exit('Login Failed');
}

echo $ssh->exec('pwd');
echo $ssh->exec('ls -la');
```

**Security Notes**:
- Always encrypt SSH passwords using Laravel's `Crypt` facade
- Store credentials securely in database
- Implement connection timeout limits
- Log all SSH connection attempts

---

## ⚡ Performance Packages

### 2. **laravel/octane** (Highly Recommended - Speed Boost)
**Purpose**: Supercharge application performance with persistent servers

**Why**:
- Boots Laravel once and keeps it in memory
- 10x-100x faster response times
- Handles thousands of concurrent connections
- Perfect for SSH panel's real-time needs

**Installation**:
```bash
composer require laravel/octane
php artisan octane:install
```

**Supported Servers** (Choose one):
- **FrankenPHP** (recommended for 2025) - Modern, supports HTTP/3
- **RoadRunner** - Easy to setup, good for most use cases
- **Swoole/Open Swoole** - Maximum performance

**Select FrankenPHP**:
```bash
php artisan octane:install
# Select: frankenphp

# Start the server
php artisan octane:start
```

**Performance Impact**:
- Traditional Laravel: ~50-100 requests/second
- With Octane: ~1,000-5,000+ requests/second

**Important Considerations**:
- Memory leaks can persist across requests
- Avoid storing state in static properties
- Clear singleton instances when needed
- Test thoroughly in development

---

### 3. **predis/predis** or **phpredis** (Recommended - Caching & Queues)
**Purpose**: Redis client for caching, sessions, and queues

**Why**:
- Lightning-fast in-memory data storage
- Essential for queue performance
- Session storage for concurrent SSH sessions
- Cache server information

**Installation**:
```bash
# Option 1: Pure PHP (easier)
composer require predis/predis

# Option 2: PHP Extension (faster, requires compilation)
# Install phpredis extension via pecl
```

**Configure** `.env`:
```env
CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
```

**Use Cases**:
- Cache server status checks
- Store active SSH sessions
- Queue SSH commands for processing
- Rate limiting SSH connection attempts

---

### 4. **laravel/horizon** (Recommended - Queue Management)
**Purpose**: Beautiful dashboard for Redis-powered queues

**Why**:
- Real-time monitoring of background jobs
- Perfect for managing SSH connection queues
- Auto-balancing workers
- Failed job management

**Installation**:
```bash
composer require laravel/horizon
php artisan horizon:install
php artisan migrate
```

**Usage**:
```bash
# Start Horizon
php artisan horizon

# Visit dashboard
http://localhost/horizon
```

**Use Cases for SSH Panel**:
- Queue SSH connection requests
- Background server health checks
- Batch command execution
- Log processing

**Configuration** (`config/horizon.php`):
```php
'defaults' => [
    'supervisor-1' => [
        'connection' => 'redis',
        'queue' => ['default', 'ssh'],
        'balance' => 'auto',
        'processes' => 3,
        'tries' => 3,
        'timeout' => 60,
    ],
],
```

---

## 🔄 Real-Time Communication Packages

### 5. **laravel/reverb** (Highly Recommended - WebSockets)
**Purpose**: First-party WebSocket server for real-time features

**Why**:
- Perfect for real-time SSH terminal output
- No third-party services needed (bye Pusher!)
- Handles thousands of concurrent connections
- Built-in integration with Laravel Broadcasting

**Installation**:
```bash
php artisan install:broadcasting
```

This installs Reverb automatically and sets up broadcasting.

**Start Reverb**:
```bash
php artisan reverb:start --debug
```

**Use Cases**:
- Real-time terminal output streaming
- Live server status updates
- Instant connection notifications
- Multi-user collaboration on terminals

**Terminal Debug**:
```bash
# See WebSocket logs in terminal
php artisan reverb:start --debug
```

**Frontend Integration**:
```javascript
// Listen for terminal output
Echo.private(`terminal.${serverId}`)
    .listen('TerminalOutput', (e) => {
        console.log(e.output);
        appendToTerminal(e.output);
    });
```

**Important Notes**:
- Default port: 8080 (customize with `--port`)
- Use with Laravel Echo on frontend
- Supports horizontal scaling via Redis pub/sub
- SSL/TLS support for production

---

## 📊 Monitoring & Debugging Packages

### 6. **laravel/pulse** (Recommended - Production Monitoring)
**Purpose**: At-a-glance performance insights for production

**Why**:
- Lightweight production monitoring
- Track slow endpoints and jobs
- Monitor active SSH connections
- Identify bottlenecks

**Installation**:
```bash
composer require laravel/pulse
php artisan vendor:publish --provider="Laravel\Pulse\PulseServiceProvider"
php artisan migrate
```

**Dashboard**:
```bash
# Visit
http://localhost/pulse
```

**Use Cases**:
- Monitor SSH connection performance
- Track slow server commands
- Identify most active servers
- System resource usage

**When to Use**:
- ✅ Production environments
- ✅ Performance monitoring
- ✅ System health checks

---

### 7. **laravel/telescope** (Development Only - Debugging)
**Purpose**: Elegant debug assistant for local development

**Why**:
- Deep insight into requests, queries, jobs
- Debug SSH connection issues
- Track exceptions and logs
- Monitor database queries

**Installation**:
```bash
composer require laravel/telescope --dev
php artisan telescope:install
php artisan migrate
```

**Dashboard**:
```bash
http://localhost/telescope
```

**CRITICAL**: **NEVER** use in production!
- Heavy on resources
- Can leak sensitive data (SSH credentials!)
- Stores everything in database

**Use Cases (Development Only)**:
- Debug SSH connection failures
- Inspect executed commands
- Monitor query performance
- Track API requests

**When to Use**:
- ✅ Local development
- ✅ Debugging specific issues
- ❌ Production (use Pulse instead)

---

## 🛡️ Security Packages

### 8. **laravel/sanctum** (Already Included - API Security)
**Purpose**: Lightweight API authentication

**Why**:
- Already included with Laravel
- Perfect for SPA + API architecture
- Cookie-based SPA auth (CSRF protection)
- Token-based API auth
- Simpler than OAuth2

**Configuration**:
```bash
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate
```

**Use Cases**:
- Authenticate SSH panel users
- API tokens for mobile apps
- Session management
- Multi-device support

**Security Best Practices**:
- Use cookie-based auth for SPA (same domain)
- Set `HttpOnly` and `SameSite` cookie attributes
- Implement token abilities/scopes
- Rotate tokens regularly

---

### 9. **spatie/laravel-permission** (Optional - Role-Based Access)
**Purpose**: Role and permission management

**Why**:
- Control who can access which servers
- Fine-grained permission system
- Team-based server access
- Audit trail

**Installation**:
```bash
composer require spatie/laravel-permission
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
php artisan migrate
```

**Use Cases**:
- Admin vs. User roles
- Server-specific access control
- Team permissions
- Action restrictions (view, connect, execute, delete)

**Example**:
```php
// Assign role
$user->assignRole('admin');

// Check permission
if ($user->can('connect-to-server')) {
    // Allow SSH connection
}

// In Livewire component
#[Can('connect-to-server')]
public function connect($serverId) { }
```

---

### 10. **spatie/laravel-activitylog** (Recommended - Audit Trail)
**Purpose**: Log all user activities and changes

**Why**:
- Track who accessed which servers
- Log all executed commands
- Security compliance
- Debugging user issues

**Installation**:
```bash
composer require spatie/laravel-activitylog
php artisan vendor:publish --provider="Spatie\Activitylog\ActivitylogServiceProvider"
php artisan migrate
```

**Use Cases**:
- Log SSH connection attempts
- Track command execution
- Server configuration changes
- User activity monitoring

**Example**:
```php
activity()
    ->performedOn($server)
    ->causedBy($user)
    ->withProperties(['ip' => request()->ip()])
    ->log('Connected to server');

activity()
    ->performedOn($server)
    ->causedBy($user)
    ->withProperties(['command' => 'ls -la'])
    ->log('Executed command');
```

---

## 🎨 UI Enhancement Packages

### 11. **livewire/flux-pro** (Already Installed ✅)
**Purpose**: Premium UI components

**Status**: Already installed from local source
**Version**: v2.2.5

**Available Components**:
- Data tables (perfect for server lists)
- Command palette (quick server access)
- Charts (server metrics)
- Rich text editor (server notes)
- Toast notifications

See [ARCPANEL.md](ARCPANEL.md) for complete component list.

---

## 📦 Additional Utility Packages

### 12. **spatie/laravel-backup** (Optional - Backup Management)
**Purpose**: Backup SSH configurations and data

**Installation**:
```bash
composer require spatie/laravel-backup
```

**Use Cases**:
- Backup server configurations
- Database backups
- Restore server settings

---

### 13. **spatie/laravel-query-builder** (Optional - API Filtering)
**Purpose**: Advanced API filtering and sorting

**Installation**:
```bash
composer require spatie/laravel-query-builder
```

**Use Cases**:
- Filter servers by status, region
- Sort by last connection time
- Search server names

---

## 🚀 Recommended Installation Order

### Phase 1: Core SSH Functionality
```bash
# SSH library (required)
composer require phpseclib/phpseclib:~3.0

# Test SSH connections work
```

### Phase 2: Performance Foundation
```bash
# Redis for caching & queues
composer require predis/predis

# Configure Redis in .env
# Update CACHE_DRIVER=redis, QUEUE_CONNECTION=redis
```

### Phase 3: Real-Time Features
```bash
# WebSocket for terminal
php artisan install:broadcasting

# Start Reverb server
php artisan reverb:start --debug
```

### Phase 4: Queue Management
```bash
# Queue dashboard
composer require laravel/horizon
php artisan horizon:install
php artisan migrate

# Start Horizon
php artisan horizon
```

### Phase 5: Monitoring (Production)
```bash
# Production monitoring
composer require laravel/pulse
php artisan vendor:publish --provider="Laravel\Pulse\PulseServiceProvider"
php artisan migrate
```

### Phase 6: Development Tools (Dev Only)
```bash
# Development debugging
composer require laravel/telescope --dev
php artisan telescope:install
php artisan migrate
```

### Phase 7: Security & Auditing
```bash
# Activity logging
composer require spatie/laravel-activitylog
php artisan vendor:publish --provider="Spatie\Activitylog\ActivitylogServiceProvider"
php artisan migrate

# Optional: Permissions
composer require spatie/laravel-permission
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
php artisan migrate
```

### Phase 8: Performance Boost (When Ready for Production)
```bash
# Octane (advanced - test thoroughly first)
composer require laravel/octane
php artisan octane:install
# Select: frankenphp

# Start Octane
php artisan octane:start
```

---

## ⚙️ Environment Configuration

After installing packages, update your `.env`:

```env
# Application
APP_NAME=ArcPanel
APP_ENV=production
APP_DEBUG=false

# Database
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=arcpanel
DB_USERNAME=root
DB_PASSWORD=

# Redis (Required for queues, cache, sessions)
CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

# Broadcasting (Reverb)
BROADCAST_DRIVER=reverb

REVERB_APP_ID=your-app-id
REVERB_APP_KEY=your-app-key
REVERB_APP_SECRET=your-app-secret
REVERB_HOST=localhost
REVERB_PORT=8080
REVERB_SCHEME=http

# Sanctum
SANCTUM_STATEFUL_DOMAINS=localhost,127.0.0.1

# Horizon
HORIZON_ENABLED=true

# Telescope (Development only)
TELESCOPE_ENABLED=false
```

---

## 🎯 Production Checklist

Before deploying to production:

- [ ] Install Redis
- [ ] Configure queue workers (Horizon)
- [ ] Set up Reverb WebSocket server
- [ ] Enable Pulse monitoring
- [ ] **Disable Telescope** (dev only)
- [ ] Configure rate limiting
- [ ] Set up activity logging
- [ ] Enable HTTPS for Reverb
- [ ] Configure proper CORS settings
- [ ] Test Octane thoroughly (if using)

---

## 📊 Performance Impact Summary

| Package | Performance Impact | Resource Usage | When to Use |
|---------|-------------------|----------------|-------------|
| **phpseclib** | Moderate CPU | Low | Always (required) |
| **Laravel Octane** | 10x-100x faster | Higher memory | Production (after testing) |
| **Redis/Predis** | 10x-50x faster caching | Medium memory | Always (highly recommended) |
| **Horizon** | Minimal | Low | When using queues |
| **Reverb** | Real-time, minimal | Medium | Real-time features |
| **Pulse** | Minimal overhead | Low | Production monitoring |
| **Telescope** | Heavy (avoid in prod) | High | Development only |
| **Sanctum** | Minimal | Low | Always (included) |
| **Activity Log** | Low | Low-Medium DB | When auditing needed |

---

## 🔗 Useful Resources

- [phpseclib Documentation](https://phpseclib.com/)
- [Laravel Octane Docs](https://laravel.com/docs/12.x/octane)
- [Laravel Reverb Docs](https://laravel.com/docs/12.x/reverb)
- [Laravel Horizon Docs](https://laravel.com/docs/12.x/horizon)
- [Laravel Pulse Docs](https://laravel.com/docs/12.x/pulse)
- [Laravel Telescope Docs](https://laravel.com/docs/12.x/telescope)
- [Spatie Packages](https://spatie.be/open-source/packages)

---

## 💡 Pro Tips

1. **Start Simple**: Begin with phpseclib + Redis, add complexity as needed
2. **Monitor Early**: Install Pulse from day one to catch issues early
3. **Queue Everything**: Use Horizon to queue SSH operations for better UX
4. **Cache Aggressively**: Cache server status, user permissions, etc.
5. **Log Everything**: Activity logging is crucial for security
6. **Test Octane Thoroughly**: Don't rush into production with Octane
7. **WebSockets for Terminal**: Reverb makes real-time terminals smooth
8. **Separate Dev/Prod**: Never use Telescope in production

---

## 🚫 What NOT to Install

- ❌ **Pusher** - Use Reverb instead (free, better performance)
- ❌ **Laravel Passport** - Sanctum is simpler for SPA auth
- ❌ **Debugbar** - Use Telescope instead (cleaner)
- ❌ **Native SSH2 Extension** - phpseclib is more portable

---

**Next Steps**: Let me know which packages you'd like to install first, and I'll help you set them up with proper configuration for your SSH panel!
