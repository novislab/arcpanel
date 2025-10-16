# ArcPanel Guidelines

## Application Overview
ArcPanel is a self-hosted SSH management panel built with Laravel 12, Livewire 3, and Flux UI. It provides web-based SSH access and server management capabilities through an intuitive interface.

---

## Color System & Theming

### Global Color Palette
All custom colors are defined in [resources/css/app.css](resources/css/app.css#L11-L37). **NEVER** add inline color values or arbitrary Tailwind colors. Use the defined system colors:

#### Zinc Palette (Primary UI Colors)
```css
--color-zinc-50: #fafafa    /* Lightest backgrounds */
--color-zinc-100: #f5f5f5   /* Light backgrounds */
--color-zinc-200: #e5e5e5   /* Borders, dividers */
--color-zinc-300: #d4d4d4   /* Subtle borders */
--color-zinc-400: #a3a3a3   /* Disabled states */
--color-zinc-500: #737373   /* Secondary text */
--color-zinc-600: #525252   /* Body text */
--color-zinc-700: #404040   /* Emphasized text */
--color-zinc-800: #262626   /* Dark backgrounds */
--color-zinc-900: #171717   /* Darker backgrounds */
--color-zinc-950: #0a0a0a   /* Darkest backgrounds */
```

#### Accent Colors (Interactive Elements)
```css
/* Light Mode */
--color-accent: var(--color-neutral-800)             /* Primary interactive color */
--color-accent-content: var(--color-neutral-800)     /* Content on accent backgrounds */
--color-accent-foreground: var(--color-white)        /* Text on accent backgrounds */

/* Dark Mode */
--color-accent: var(--color-white)                   /* Primary interactive color */
--color-accent-content: var(--color-white)           /* Content on accent backgrounds */
--color-accent-foreground: var(--color-neutral-800)  /* Text on accent backgrounds */
```

### Using Colors in Code

#### Tailwind Classes
```blade
<!-- Backgrounds -->
<div class="bg-zinc-50 dark:bg-zinc-900">...</div>

<!-- Text -->
<p class="text-zinc-600 dark:text-zinc-400">...</p>

<!-- Borders -->
<div class="border border-zinc-200 dark:border-zinc-700">...</div>

<!-- Accent Colors (for interactive elements) -->
<flux:button variant="primary">Uses accent colors automatically</flux:button>
```

#### Focus States
Focus styles are globally configured in [app.css](resources/css/app.css#L58-L62):
```css
input:focus[data-flux-control] {
    @apply outline-hidden ring-2 ring-accent ring-offset-2 ring-offset-accent-foreground;
}
```

**Rule**: Do NOT override focus styles unless absolutely necessary. All form inputs automatically use the accent color system.

---

## Flux UI Component Library

### Available Components (Pro Edition)
ArcPanel uses **Flux UI Pro v2.2.5** installed from local source. You have access to all Free + Pro components:

#### Free Components
- `flux:avatar` - User avatars and profile images
- `flux:badge` - Status indicators, labels, tags
- `flux:brand` - Logo and branding elements
- `flux:breadcrumbs` - Navigation breadcrumbs
- `flux:button` - Interactive buttons with variants
- `flux:callout` - Alert boxes, notifications
- `flux:checkbox` - Checkbox inputs with group variants (buttons, cards, pills)
- `flux:dropdown` - Dropdown menus
- `flux:field` - Form field wrapper with label/error
- `flux:heading` - Semantic headings
- `flux:icon` - SVG icon system
- `flux:input` - Text/email/password inputs
- `flux:modal` - Dialog modals
- `flux:navbar` - Top navigation bars
- `flux:profile` - User profile display
- `flux:radio` - Radio button inputs with group variants (buttons, cards, pills)
- `flux:select` - Select dropdowns with advanced variants
- `flux:separator` - Visual dividers
- `flux:switch` - Toggle switches
- `flux:text` - Styled text content
- `flux:textarea` - Multi-line text inputs
- `flux:tooltip` - Hover tooltips

#### Pro Components (Now Available!)
- `flux:accordion` - Collapsible content sections with animations
- `flux:autocomplete` - Auto-complete search inputs
- `flux:calendar` - Date selection calendar
- `flux:card` - Content card containers
- `flux:chart` - Advanced charting (area, line, axis, legends, tooltips)
- `flux:command` - Command palette (Cmd+K style interface)
- `flux:context` - Context menus and right-click menus
- `flux:date-picker` - Date picker with calendar popup
- `flux:editor` - Rich text WYSIWYG editor with toolbar
- `flux:pagination` - Pagination controls
- `flux:popover` - Popover floating elements
- `flux:select` (advanced) - Combobox, listbox, custom variants with search
- `flux:tab` / `flux:tabs` - Tab navigation components
- `flux:table` - Advanced data tables with sorting, filtering
- `flux:toast` - Toast notifications

### Flux Component Usage Rules

#### 1. Always Prefer Flux Components
When building UI, **ALWAYS** check if a Flux component exists before writing custom HTML:

```blade
<!-- ❌ WRONG: Custom HTML button -->
<button class="px-4 py-2 bg-accent text-white rounded">Click me</button>

<!-- ✅ CORRECT: Flux button -->
<flux:button variant="primary">Click me</flux:button>
```

#### 2. Button Variants
```blade
<!-- Primary actions (accent color) -->
<flux:button variant="primary">Save Changes</flux:button>

<!-- Secondary actions -->
<flux:button variant="secondary">Cancel</flux:button>

<!-- Tertiary/ghost actions -->
<flux:button variant="ghost">Learn More</flux:button>

<!-- Dangerous actions -->
<flux:button variant="danger">Delete Server</flux:button>
```

#### 3. Form Fields
All form fields should use `flux:input` and related components:

```blade
<!-- Text Input -->
<flux:input
    wire:model="serverName"
    label="Server Name"
    type="text"
    required
    placeholder="my-server"
/>

<!-- Password Input with viewable toggle -->
<flux:input
    wire:model="password"
    label="SSH Password"
    type="password"
    required
    viewable
/>

<!-- Textarea -->
<flux:textarea
    wire:model="description"
    label="Description"
    rows="4"
    placeholder="Enter server description..."
/>

<!-- Select Dropdown -->
<flux:select
    wire:model="region"
    label="Region"
    placeholder="Select region"
>
    <option value="us-east">US East</option>
    <option value="eu-west">EU West</option>
</flux:select>

<!-- Checkbox -->
<flux:checkbox
    wire:model="autoConnect"
    label="Auto-connect on page load"
/>

<!-- Switch/Toggle -->
<flux:switch
    wire:model="enableNotifications"
    label="Enable notifications"
/>
```

#### 4. Navigation Components
```blade
<!-- Sidebar Navigation (see app/sidebar.blade.php for reference) -->
<flux:navlist variant="outline">
    <flux:navlist.group heading="SSH Management">
        <flux:navlist.item
            icon="server"
            :href="route('servers.index')"
            :current="request()->routeIs('servers.*')"
            wire:navigate
        >
            Servers
        </flux:navlist.item>
    </flux:navlist.group>
</flux:navlist>

<!-- Breadcrumbs -->
<flux:breadcrumbs>
    <flux:breadcrumbs.item href="/">Home</flux:breadcrumbs.item>
    <flux:breadcrumbs.item href="/servers">Servers</flux:breadcrumbs.item>
    <flux:breadcrumbs.item>{{ $server->name }}</flux:breadcrumbs.item>
</flux:breadcrumbs>
```

#### 5. Modals
```blade
<!-- Modal trigger -->
<flux:button x-on:click="$flux.modal('delete-server').show()">
    Delete Server
</flux:button>

<!-- Modal definition -->
<flux:modal name="delete-server" class="space-y-6">
    <flux:heading size="lg">Delete Server</flux:heading>
    <flux:text>Are you sure you want to delete this server? This action cannot be undone.</flux:text>

    <div class="flex gap-2 justify-end">
        <flux:button variant="ghost" x-on:click="$flux.modal('delete-server').close()">
            Cancel
        </flux:button>
        <flux:button variant="danger" wire:click="deleteServer">
            Delete
        </flux:button>
    </div>
</flux:modal>
```

#### 6. Callouts & Alerts
```blade
<!-- Info callout -->
<flux:callout variant="info" icon="information-circle">
    SSH connection established successfully
</flux:callout>

<!-- Warning -->
<flux:callout variant="warning" icon="exclamation-triangle">
    This server has high CPU usage
</flux:callout>

<!-- Error -->
<flux:callout variant="danger" icon="x-circle">
    Failed to connect: Connection timeout
</flux:callout>

<!-- Success -->
<flux:callout variant="success" icon="check-circle">
    Server configuration saved
</flux:callout>
```

#### 7. Badges & Status Indicators
```blade
<!-- Status badges -->
<flux:badge variant="success">Online</flux:badge>
<flux:badge variant="danger">Offline</flux:badge>
<flux:badge variant="warning">Connecting</flux:badge>
<flux:badge variant="neutral">Idle</flux:badge>
```

#### 8. Icons
Custom icons are stored in [resources/views/flux/icon/](resources/views/flux/icon/). Use existing icons when possible:

```blade
<flux:icon name="folder-git-2" />
<flux:icon name="layout-grid" />
<flux:icon name="chevrons-up-down" />
<flux:icon name="book-open-text" />
```

---

### Flux Pro Component Examples

#### 9. Data Tables (Pro)
Perfect for displaying SSH servers, connection logs, and system information:

```blade
<flux:table>
    <flux:columns>
        <flux:column sortable="name">Server Name</flux:column>
        <flux:column sortable="host">Host</flux:column>
        <flux:column>Status</flux:column>
        <flux:column>Actions</flux:column>
    </flux:columns>

    <flux:rows>
        @foreach ($servers as $server)
            <flux:row :key="$server->id">
                <flux:cell>{{ $server->name }}</flux:cell>
                <flux:cell>{{ $server->host }}:{{ $server->port }}</flux:cell>
                <flux:cell>
                    <flux:badge :variant="$server->isOnline() ? 'success' : 'neutral'">
                        {{ $server->status }}
                    </flux:badge>
                </flux:cell>
                <flux:cell>
                    <flux:button size="sm" wire:click="connect({{ $server->id }})">
                        Connect
                    </flux:button>
                </flux:cell>
            </flux:row>
        @endforeach
    </flux:rows>
</flux:table>
```

#### 10. Command Palette (Pro)
Great for quick SSH server access and navigation:

```blade
<!-- Trigger with Cmd+K / Ctrl+K -->
<flux:command wire:model="search" placeholder="Search servers...">
    <flux:command.items>
        @foreach ($servers as $server)
            <flux:command.item wire:click="connect({{ $server->id }})">
                <flux:icon name="server" />
                {{ $server->name }} ({{ $server->host }})
            </flux:command.item>
        @endforeach
    </flux:command.items>
</flux:command>
```

#### 11. Toast Notifications (Pro)
For SSH connection status and system notifications:

```blade
<!-- Trigger from Livewire component -->
<flux:toast.group>
    <!-- Toast will appear automatically when dispatched -->
</flux:toast.group>

<!-- In your Livewire component -->
<?php
// Success notification
$this->dispatch('toast',
    message: 'Connected to server successfully',
    variant: 'success'
);

// Error notification
$this->dispatch('toast',
    message: 'Connection failed: ' . $error,
    variant: 'danger'
);
?>
```

#### 12. Accordion (Pro)
Perfect for organizing server details and SSH configuration:

```blade
<flux:accordion>
    <flux:accordion.item>
        <flux:accordion.heading>Connection Details</flux:accordion.heading>
        <flux:accordion.content>
            <div class="space-y-2">
                <p><strong>Host:</strong> {{ $server->host }}</p>
                <p><strong>Port:</strong> {{ $server->port }}</p>
                <p><strong>Username:</strong> {{ $server->username }}</p>
            </div>
        </flux:accordion.content>
    </flux:accordion.item>

    <flux:accordion.item>
        <flux:accordion.heading>Security Settings</flux:accordion.heading>
        <flux:accordion.content>
            <!-- Security configuration here -->
        </flux:accordion.content>
    </flux:accordion.item>
</flux:accordion>
```

#### 13. Rich Text Editor (Pro)
For SSH command notes and documentation:

```blade
<flux:editor wire:model="notes">
    <flux:editor.toolbar>
        <flux:editor.bold />
        <flux:editor.italic />
        <flux:editor.strike />
        <flux:editor.separator />
        <flux:editor.code />
        <flux:editor.link />
        <flux:editor.separator />
        <flux:editor.bullet />
        <flux:editor.ordered />
    </flux:editor.toolbar>

    <flux:editor.content placeholder="Add notes about this server..." />
</flux:editor>
```

#### 14. Cards (Pro)
Enhanced card component for server displays:

```blade
<div class="grid gap-4 md:grid-cols-2 lg:grid-cols-3">
    @foreach ($servers as $server)
        <flux:card>
            <div class="flex items-start justify-between">
                <div>
                    <flux:heading size="lg">{{ $server->name }}</flux:heading>
                    <flux:text>{{ $server->host }}:{{ $server->port }}</flux:text>
                </div>
                <flux:badge :variant="$server->isOnline() ? 'success' : 'neutral'">
                    {{ $server->status }}
                </flux:badge>
            </div>

            <flux:separator />

            <div class="flex gap-2">
                <flux:button variant="primary">Connect</flux:button>
                <flux:button variant="ghost">Edit</flux:button>
            </div>
        </flux:card>
    @endforeach
</div>
```

#### 15. Context Menus (Pro)
Right-click menus for server management:

```blade
<flux:context wire:key="server-{{ $server->id }}">
    <div class="p-4 bg-zinc-50 dark:bg-zinc-900 rounded-lg">
        {{ $server->name }}
    </div>

    <flux:menu slot="menu">
        <flux:menu.item icon="arrow-right" wire:click="connect({{ $server->id }})">
            Connect
        </flux:menu.item>
        <flux:menu.item icon="pencil" wire:click="edit({{ $server->id }})">
            Edit
        </flux:menu.item>
        <flux:menu.separator />
        <flux:menu.item icon="trash" variant="danger" wire:click="delete({{ $server->id }})">
            Delete
        </flux:menu.item>
    </flux:menu>
</flux:context>
```

#### 16. Tabs (Pro)
For organizing different SSH panel views:

```blade
<flux:tabs wire:model="activeTab">
    <flux:tab name="servers">Servers</flux:tab>
    <flux:tab name="logs">Connection Logs</flux:tab>
    <flux:tab name="settings">Settings</flux:tab>

    <flux:tab.panel name="servers">
        <!-- Server list content -->
    </flux:tab.panel>

    <flux:tab.panel name="logs">
        <!-- Logs content -->
    </flux:tab.panel>

    <flux:tab.panel name="settings">
        <!-- Settings content -->
    </flux:tab.panel>
</flux:tabs>
```

#### 17. Charts (Pro)
For visualizing server metrics and usage:

```blade
<flux:chart :data="$cpuUsageData" variant="area">
    <flux:chart.legend />

    <flux:chart.axis.y />
    <flux:chart.axis.x />

    <flux:chart.area
        :data="$cpuUsageData"
        :color="['#3b82f6']"
        label="CPU Usage"
    />

    <flux:chart.tooltip>
        <flux:chart.tooltip.heading />
        <flux:chart.tooltip.value />
    </flux:chart.tooltip>
</flux:chart>
```

---

## Dark Mode Requirements

**CRITICAL**: All new pages and components **MUST** support dark mode. ArcPanel defaults to dark mode (see [sidebar.blade.php:2](resources/views/components/layouts/app/sidebar.blade.php#L2)).

### Dark Mode Implementation Rules

#### 1. Always Use Dark Mode Classes
```blade
<!-- Background colors -->
<div class="bg-white dark:bg-zinc-800">...</div>

<!-- Text colors -->
<p class="text-zinc-900 dark:text-zinc-100">...</p>

<!-- Borders -->
<div class="border border-zinc-200 dark:border-zinc-700">...</div>
```

#### 2. Test Both Modes
Before finalizing any UI work:
1. Test in light mode
2. Test in dark mode (default)
3. Ensure readable contrast in both modes

#### 3. Common Dark Mode Patterns
```blade
<!-- Card/Panel -->
<div class="bg-zinc-50 dark:bg-zinc-900 border border-zinc-200 dark:border-zinc-700">
    <h3 class="text-zinc-900 dark:text-zinc-100">Title</h3>
    <p class="text-zinc-600 dark:text-zinc-400">Description</p>
</div>

<!-- Hover states -->
<button class="hover:bg-zinc-100 dark:hover:bg-zinc-800">...</button>

<!-- Placeholder patterns (see dashboard) -->
<x-placeholder-pattern class="stroke-gray-900/20 dark:stroke-neutral-100/20" />
```

---

## Architecture & File Structure

### Directory Organization

```
app/
├── Livewire/              # Livewire class-based components
│   └── Actions/           # Reusable actions (Logout, etc.)
├── Models/                # Eloquent models
└── ...

resources/
├── css/
│   └── app.css           # Global styles & theme variables
├── js/
│   └── app.js            # Frontend JavaScript
└── views/
    ├── components/       # Blade components
    │   └── layouts/      # Layout components
    ├── livewire/         # Volt components (single-file)
    │   ├── auth/         # Authentication pages
    │   └── settings/     # Settings pages
    └── flux/
        ├── icon/         # Custom Flux icons
        └── navlist/      # Custom navlist components

routes/
├── web.php              # Web routes
├── auth.php             # Authentication routes
└── console.php          # Artisan commands

tests/
├── Feature/             # Feature tests (most tests)
└── Unit/                # Unit tests
```

### Component Architecture

#### When to Use Livewire Volt
Use **Livewire Volt** for all interactive pages. Volt allows PHP logic and Blade templates in one file:

```php
<?php
// resources/views/livewire/servers/index.blade.php

use App\Models\Server;
use function Livewire\Volt\{state, computed};

state(['search' => '']);

$servers = computed(fn() => Server::query()
    ->when($this->search, fn($q) => $q->where('name', 'like', "%{$this->search}%"))
    ->latest()
    ->get()
);

?>

<x-layouts.app title="SSH Servers">
    <div class="space-y-6">
        <flux:heading size="xl">SSH Servers</flux:heading>

        <flux:input
            wire:model.live.debounce.300ms="search"
            placeholder="Search servers..."
            icon="magnifying-glass"
        />

        <div class="grid gap-4">
            @foreach ($this->servers as $server)
                <div class="p-4 bg-zinc-50 dark:bg-zinc-900 rounded-lg">
                    <h3>{{ $server->name }}</h3>
                </div>
            @endforeach
        </div>
    </div>
</x-layouts.app>
```

#### Layout Usage
Always use the appropriate layout:

```blade
<!-- For authenticated pages with sidebar -->
<x-layouts.app title="Page Title">
    <!-- Your content -->
</x-layouts.app>

<!-- For authentication pages -->
<x-layouts.auth>
    <!-- Login, register forms, etc. -->
</x-layouts.auth>
```

---

## SSH Panel Specific Guidelines

### Server Connection Management

#### Model Structure
When creating SSH-related models, include these common fields:

```php
// Server model example
class Server extends Model
{
    protected function casts(): array
    {
        return [
            'port' => 'integer',
            'last_connected_at' => 'datetime',
            'is_active' => 'boolean',
        ];
    }

    // Relationships
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }

    // Status helpers
    public function isOnline(): bool
    {
        // Implementation
    }
}
```

#### Security Considerations
1. **NEVER** store SSH passwords in plain text
2. Use Laravel's encryption: `encrypt($password)` / `decrypt($encryptedPassword)`
3. Validate all SSH connection parameters
4. Implement rate limiting for connection attempts
5. Log all SSH connection attempts for audit trails

```php
// Example: Secure password storage
use Illuminate\Support\Facades\Crypt;

// Storing
$server->password = Crypt::encryptString($password);

// Retrieving
$password = Crypt::decryptString($server->password);
```

#### Connection State Management
Use Livewire's reactive properties for connection states:

```php
state([
    'connectionStatus' => 'disconnected', // disconnected, connecting, connected, error
    'terminalOutput' => [],
]);

$connect = function() {
    $this->connectionStatus = 'connecting';

    try {
        // SSH connection logic
        $this->connectionStatus = 'connected';
    } catch (\Exception $e) {
        $this->connectionStatus = 'error';
        $this->dispatch('connection-failed', message: $e->getMessage());
    }
};
```

### UI Patterns for SSH Features

#### Terminal Display
```blade
<div class="relative h-full overflow-hidden rounded-xl border border-zinc-200 dark:border-zinc-700 bg-zinc-950 font-mono">
    <div class="p-4 space-y-2">
        @foreach ($terminalOutput as $line)
            <div class="text-zinc-100 text-sm">{{ $line }}</div>
        @endforeach

        <div class="flex items-center gap-2">
            <span class="text-green-400">$</span>
            <input
                type="text"
                wire:model="command"
                wire:keydown.enter="executeCommand"
                class="flex-1 bg-transparent text-zinc-100 outline-none"
                autofocus
            />
        </div>
    </div>
</div>
```

#### Server List Display
```blade
<div class="grid gap-4 md:grid-cols-2 lg:grid-cols-3">
    @foreach ($servers as $server)
        <div class="relative p-6 bg-zinc-50 dark:bg-zinc-900 border border-zinc-200 dark:border-zinc-700 rounded-xl">
            <div class="flex items-start justify-between">
                <div class="space-y-1">
                    <flux:heading size="lg">{{ $server->name }}</flux:heading>
                    <flux:text class="text-sm text-zinc-600 dark:text-zinc-400">
                        {{ $server->host }}:{{ $server->port }}
                    </flux:text>
                </div>

                @if ($server->isOnline())
                    <flux:badge variant="success">Online</flux:badge>
                @else
                    <flux:badge variant="neutral">Offline</flux:badge>
                @endif
            </div>

            <div class="mt-4 flex gap-2">
                <flux:button variant="primary" wire:click="connect({{ $server->id }})">
                    Connect
                </flux:button>
                <flux:button variant="ghost" wire:click="edit({{ $server->id }})">
                    Edit
                </flux:button>
            </div>
        </div>
    @endforeach
</div>
```

#### Connection Form
```blade
<form wire:submit="createServer" class="space-y-6">
    <flux:input
        wire:model="form.name"
        label="Server Name"
        placeholder="Production Server"
        required
    />

    <div class="grid gap-6 md:grid-cols-2">
        <flux:input
            wire:model="form.host"
            label="Hostname/IP"
            placeholder="192.168.1.100"
            required
        />

        <flux:input
            wire:model="form.port"
            label="Port"
            type="number"
            placeholder="22"
            required
        />
    </div>

    <flux:input
        wire:model="form.username"
        label="Username"
        placeholder="root"
        required
    />

    <flux:input
        wire:model="form.password"
        label="Password"
        type="password"
        viewable
    />

    <div class="flex justify-end gap-2">
        <flux:button variant="ghost" type="button" wire:click="$parent.closeModal()">
            Cancel
        </flux:button>
        <flux:button variant="primary" type="submit">
            Add Server
        </flux:button>
    </div>
</form>
```

---

## Testing Requirements

### Test Organization
```
tests/
├── Feature/
│   ├── Auth/              # Authentication tests
│   ├── Servers/           # Server management tests
│   └── SSH/               # SSH connection tests
└── Unit/
    └── Models/            # Model tests
```

### Test Patterns

#### Testing Volt Components
```php
use Livewire\Volt\Volt;

test('user can create server', function () {
    $user = User::factory()->create();

    Volt::test('servers.create')
        ->actingAs($user)
        ->set('form.name', 'Test Server')
        ->set('form.host', '192.168.1.100')
        ->set('form.port', 22)
        ->set('form.username', 'root')
        ->call('createServer')
        ->assertHasNoErrors();

    expect(Server::where('name', 'Test Server')->exists())->toBeTrue();
});
```

#### Testing SSH Connections
```php
test('server connection status is tracked', function () {
    $server = Server::factory()->create();

    expect($server->isOnline())->toBeFalse();

    // Mock successful connection
    $server->update(['last_connected_at' => now()]);

    expect($server->isOnline())->toBeTrue();
});
```

#### Always Run Tests After Changes
```bash
# Run specific test file
php artisan test tests/Feature/Servers/CreateServerTest.php

# Run tests matching a filter
php artisan test --filter=server

# Run all tests
php artisan test
```

---

## Development Workflow

### Creating New Features

#### 1. Plan the Feature
- Determine if you need models, migrations, or just UI components
- Check existing components for reusable patterns

#### 2. Create Models & Migrations
```bash
php artisan make:model Server --migration --factory --pest
```

#### 3. Create Volt Components
```bash
php artisan make:volt servers/index --test --pest
php artisan make:volt servers/create --test --pest
```

#### 4. Add Navigation
Update [resources/views/components/layouts/app/sidebar.blade.php](resources/views/components/layouts/app/sidebar.blade.php):

```blade
<flux:navlist.group heading="SSH Management">
    <flux:navlist.item
        icon="server"
        :href="route('servers.index')"
        :current="request()->routeIs('servers.*')"
        wire:navigate
    >
        Servers
    </flux:navlist.item>
</flux:navlist.group>
```

#### 5. Write Tests First
```php
test('user can view servers list', function () {
    $user = User::factory()->create();
    $servers = Server::factory()->count(3)->create(['user_id' => $user->id]);

    $this->actingAs($user)
        ->get(route('servers.index'))
        ->assertSuccessful()
        ->assertSeeLivewire('servers.index')
        ->assertSee($servers->first()->name);
});
```

#### 6. Run Tests
```bash
php artisan test --filter=server
```

#### 7. Format Code
```bash
vendor/bin/pint --dirty
```

### Code Quality Checklist
Before committing any code:

- [ ] All tests passing
- [ ] Dark mode implemented and tested
- [ ] Using Flux components (no custom buttons/inputs)
- [ ] Using defined color system (no arbitrary colors)
- [ ] Code formatted with Pint
- [ ] No sensitive data in code (passwords, keys)
- [ ] Loading states implemented for async operations
- [ ] Error handling in place
- [ ] Validation rules using Form Requests

---

## Common Pitfalls to Avoid

### ❌ Don't Do This

```blade
<!-- Custom styled button -->
<button class="px-4 py-2 bg-blue-500 text-white rounded">Submit</button>

<!-- Inline colors -->
<div style="background: #333; color: #fff">...</div>

<!-- No dark mode -->
<div class="bg-gray-100 text-gray-900">...</div>

<!-- Direct env() usage -->
$apiKey = env('API_KEY');

<!-- No loading states -->
<flux:button wire:click="connect">Connect</flux:button>
```

### ✅ Do This Instead

```blade
<!-- Flux button -->
<flux:button variant="primary" type="submit">Submit</flux:button>

<!-- Theme colors -->
<div class="bg-zinc-900 dark:bg-zinc-100 text-zinc-100 dark:text-zinc-900">...</div>

<!-- Dark mode support -->
<div class="bg-zinc-100 dark:bg-zinc-900 text-zinc-900 dark:text-zinc-100">...</div>

<!-- Config usage -->
$apiKey = config('services.api.key');

<!-- Loading states -->
<flux:button wire:click="connect" wire:loading.attr="disabled">
    <span wire:loading.remove>Connect</span>
    <span wire:loading>Connecting...</span>
</flux:button>
```

---

## Quick Reference

### Essential Commands
```bash
# Development server with hot reload
composer run dev

# Build assets
npm run build

# Run tests
php artisan test

# Format code
vendor/bin/pint --dirty

# Create Volt component
php artisan make:volt [name] --test --pest

# Create model with everything
php artisan make:model [Name] --all --pest
```

### Key Files
- [resources/css/app.css](resources/css/app.css) - Theme colors & global styles
- [resources/views/components/layouts/app/sidebar.blade.php](resources/views/components/layouts/app/sidebar.blade.php) - Main navigation
- [resources/views/components/layouts/app.blade.php](resources/views/components/layouts/app.blade.php) - App layout wrapper
- [CLAUDE.md](CLAUDE.md) - Laravel Boost guidelines (already configured)

### Color Reference
```blade
<!-- Backgrounds -->
bg-zinc-50 dark:bg-zinc-900      /* Cards, panels */
bg-white dark:bg-zinc-800         /* Page backgrounds */

<!-- Text -->
text-zinc-900 dark:text-zinc-100  /* Primary text */
text-zinc-600 dark:text-zinc-400  /* Secondary text */

<!-- Borders -->
border-zinc-200 dark:border-zinc-700
```

---

## Getting Help

When you need assistance:

1. **Search Documentation**: Use the `search-docs` tool for Laravel/Livewire/Flux documentation
2. **Check Existing Code**: Look at [resources/views/livewire/auth/](resources/views/livewire/auth/) for patterns
3. **Review Tests**: See [tests/Feature/](tests/Feature/) for testing examples
4. **Flux Components**: Refer to available components list above

Remember: **Consistency is key**. Always follow existing patterns in the codebase before creating new approaches.
