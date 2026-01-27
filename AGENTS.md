# Runtipi Appstore - Agent Context Guide

> [!IMPORTANT]
> This document provides comprehensive context for AI agents to understand the structure and patterns of this Runtipi appstore repository, enabling them to create new apps following established conventions.

## Repository Overview

This is a **private, opinionated Runtipi appstore** repository tailored for:
- **Hardware**: Orange Pi 3B v2.1
- **OS**: Armbian Linux v6.1 Minimal/IOT Debian 13 (Trixie)
- **Purpose**: Personal homelab application management

### Key Characteristics
- Uses Runtipi v2 schemas for app configuration
- Supports both JSON-based and YAML-based docker-compose configurations
- Includes automated testing for app validation
- Configured for ARM64 architecture (with some AMD64 support)

---

## Repository Structure

```
runtipi-appstore-orangepi-3b-2.1/
├── apps/                          # All application definitions
│   ├── brave/
│   ├── immich/
│   ├── linkding/
│   └── tailscale/
├── __tests__/                     # Automated test suite
│   └── apps.test.ts              # Validates app structure and schemas
├── scripts/                       # Utility scripts
│   └── update-config.ts          # Configuration update script
├── .github/                       # GitHub workflows and configurations
├── config.js                      # Repository configuration
├── package.json                   # Node.js dependencies
├── tsconfig.json                  # TypeScript configuration
├── renovate.json                  # Renovate bot configuration
└── README.md                      # Repository documentation
```

---

## App Structure

Each app in the `apps/` directory **MUST** follow this structure:

```
apps/<app-id>/
├── config.json                    # REQUIRED: App metadata and configuration
├── docker-compose.json            # REQUIRED: Service definitions (JSON format)
├── docker-compose.yml             # OPTIONAL: Traditional YAML format (alternative)
├── docker-compose.user_config.yml # OPTIONAL: User-specific overrides
└── metadata/                      # REQUIRED: App metadata assets
    ├── logo.jpg                   # REQUIRED: App logo (JPG format)
    └── description.md             # REQUIRED: App description and documentation
```

### Required Files

All apps **MUST** have these four files:
1. `config.json` - App metadata and form fields
2. `docker-compose.json` - Service definitions
3. `metadata/logo.jpg` - App logo image
4. `metadata/description.md` - App description

---

## Configuration Files

### 1. `config.json` - App Metadata

**Schema**: `https://schemas.runtipi.io/v2/app-info.json`

**Required Fields**:
```json
{
  "$schema": "https://schemas.runtipi.io/v2/app-info.json",
  "name": "App Display Name",
  "id": "app-id",
  "version": "v1.0.0",
  "tipi_version": 1,
  "available": true,
  "exposable": true,
  "dynamic_config": true,
  "https": false,
  "port": 8080,
  "short_desc": "Brief description",
  "description": "Longer description",
  "author": "Author Name",
  "categories": ["category1", "category2"],
  "source": "https://github.com/...",
  "website": "https://...",
  "supported_architectures": ["arm64", "amd64"],
  "created_at": 1234567890123,
  "updated_at": 1234567890123,
  "min_tipi_version": "4.5.0",
  "form_fields": []
}
```

**Key Properties**:
- `id`: Unique identifier (lowercase, matches directory name)
- `tipi_version`: App configuration version (use `1` or `2`)
- `available`: Whether app is available for installation
- `exposable`: Whether app can be exposed to external network
- `dynamic_config`: Use dynamic configuration (typically `true`)
- `https`: Whether to use HTTPS (typically `false` for internal apps)
- `port`: Internal port the app listens on
- `no_gui`: Set to `true` for CLI-only apps (e.g., Tailscale)
- `supported_architectures`: Array of supported architectures
- `min_tipi_version`: Minimum Runtipi version required

### Form Fields

Form fields define user-configurable environment variables:

**Field Types**:
- `text`: Text input
- `password`: Password input (masked)
- `boolean`: Checkbox
- `random`: Auto-generated random string

**Example Form Fields**:
```json
"form_fields": [
  {
    "type": "password",
    "label": "Password",
    "max": 50,
    "min": 3,
    "required": true,
    "env_variable": "APP_PASSWORD"
  },
  {
    "type": "random",
    "label": "Database Password",
    "min": 32,
    "env_variable": "DB_PASSWORD"
  },
  {
    "type": "text",
    "label": "Timezone",
    "required": true,
    "hint": "Timezone in TZ database format",
    "placeholder": "Asia/Makassar",
    "default": "Asia/Makassar",
    "env_variable": "TZ"
  },
  {
    "type": "boolean",
    "label": "Enable Feature",
    "env_variable": "ENABLE_FEATURE"
  }
]
```

---

### 2. `docker-compose.json` - Service Definitions

**Schema**: `https://schemas.runtipi.io/v2/dynamic-compose.json`

**Structure**:
```json
{
  "$schema": "https://schemas.runtipi.io/v2/dynamic-compose.json",
  "schemaVersion": 2,
  "services": [
    {
      "name": "service-name",
      "image": "docker/image:tag",
      "isMain": true,
      "internalPort": 8080,
      "volumes": [],
      "environment": [],
      "dependsOn": [],
      "addToMainNetwork": true
    }
  ]
}
```

**Service Properties**:

- **`name`**: Service name (used in container naming)
- **`image`**: Docker image with tag
- **`isMain`**: Mark the primary service (one per app)
- **`internalPort`**: Port the service listens on internally
- **`volumes`**: Volume mappings
- **`environment`**: Environment variables
- **`dependsOn`**: Service dependencies
- **`addToMainNetwork`**: Add to Runtipi main network
- **`devices`**: Device mappings (for hardware access)
- **`securityOpt`**: Security options
- **`shmSize`**: Shared memory size
- **`healthCheck`**: Health check configuration
- **`capAdd`**: Linux capabilities to add

**Volume Mapping**:
```json
"volumes": [
  {
    "hostPath": "${APP_DATA_DIR}/data",
    "containerPath": "/data"
  },
  {
    "hostPath": "/etc/localtime",
    "containerPath": "/etc/localtime:ro"
  }
]
```

**Environment Variables**:
```json
"environment": [
  {
    "key": "ENV_VAR_NAME",
    "value": "${ENV_VAR_FROM_FORM}"
  },
  {
    "key": "STATIC_VAR",
    "value": "static-value"
  }
]
```

**Available Environment Variable Placeholders**:
- `${APP_DATA_DIR}`: App data directory
- `${DATA_FOLDER_HOST}`: Host data folder
- `${HOME_FOLDER_HOST}`: Host home folder
- `${<ENV_VARIABLE>}`: Any form field env_variable

**Device Mapping** (for hardware acceleration):
```json
"devices": [
  "/dev/dri:/dev/dri",
  "/dev/rga:/dev/rga",
  "/dev/dma_heap:/dev/dma_heap",
  "/dev/mpp_service:/dev/mpp_service"
]
```

**Security Options** (for privileged operations):
```json
"securityOpt": [
  "systempaths=unconfined",
  "apparmor=unconfined"
]
```

---

### 3. `docker-compose.yml` - Alternative YAML Format

Some apps use traditional Docker Compose YAML format instead of JSON:

```yaml
services:
  service-name:
    container_name: service-name
    image: docker/image:tag
    environment:
      - ENV_VAR=${ENV_VAR}
    volumes:
      - ${APP_DATA_DIR}/data:/data
    restart: unless-stopped
    labels:
      runtipi.managed: true
```

**When to use YAML**:
- Complex configurations that are easier to read in YAML
- Apps requiring advanced Docker Compose features
- Apps with many services

**Important**: Always include the label:
```yaml
labels:
  runtipi.managed: true
```

---

### 4. `docker-compose.user_config.yml` - User Overrides

Optional file for user-specific configuration that can be pasted into Runtipi's User Config page:

```yaml
# Copy paste this file into User Config page in runtipi

services:
  service-name:
    group_add:
      - video
```

---

### 5. `metadata/description.md` - App Description

Markdown file with app information, usage notes, and links:

```markdown
# Overview
Brief overview of what the app does.

## Important Notes
- Important configuration notes
- Default credentials
- Special requirements

## Configurable Environment Variables
- `ENV_VAR` - Description of the variable

## Links:
- [Project Website](https://example.com)
- [GitHub Repository](https://github.com/...)

## Preview
![Image URL](https://example.com/preview.png)
```

---

## Common Patterns

### Pattern 1: Simple Single-Service App

**Example**: Brave browser

```
apps/brave/
├── config.json          # Basic metadata, password form field
├── docker-compose.json  # Single service with volume and env vars
└── metadata/
    ├── logo.jpg
    └── description.md
```

**Characteristics**:
- Single container
- Simple configuration
- Few form fields
- No dependencies

---

### Pattern 2: Multi-Service App with Database

**Example**: Immich

```
apps/immich/
├── config.json                      # Complex form fields for paths
├── docker-compose.json              # Multiple services (app, ML, Redis, DB)
├── docker-compose.user_config.yml   # Optional user overrides
└── metadata/
    ├── logo.jpg
    └── description.md
```

**Characteristics**:
- Multiple services (main app, database, cache, ML)
- Service dependencies (`dependsOn`)
- Hardware acceleration (devices, security options)
- Complex volume mappings
- Multiple form fields for customization

**Service Dependencies**:
```json
{
  "name": "main-app",
  "dependsOn": ["db", "redis"]
}
```

---

### Pattern 3: Network Service (No GUI)

**Example**: Tailscale

```
apps/tailscale/
├── config.json          # no_gui: true
├── docker-compose.json  # Network capabilities
├── docker-compose.yml   # Alternative YAML format
└── metadata/
    ├── logo.jpg
    └── description.md
```

**Characteristics**:
- `no_gui: true` in config.json
- `exposable: false`
- Network capabilities (`cap_add`)
- Device mappings for network interfaces

**Network Capabilities**:
```yaml
cap_add:
  - net_admin
  - sys_module
```

---

## Hardware-Specific Configurations

### Orange Pi 3B RK3566 NPU Support

For apps leveraging the RK3566 NPU (e.g., Immich ML):

**Device Mappings**:
```json
"devices": [
  "/dev/rga:/dev/rga",
  "/dev/dri:/dev/dri",
  "/dev/dma_heap:/dev/dma_heap",
  "/dev/mpp_service:/dev/mpp_service"
]
```

**Volume Mappings for GPU Libraries**:
```json
"volumes": [
  {
    "hostPath": "/etc/OpenCL",
    "containerPath": "/etc/OpenCL:ro"
  },
  {
    "hostPath": "/usr/lib/aarch64-linux-gnu/libmali.so.1",
    "containerPath": "/usr/lib/aarch64-linux-gnu/libmali.so.1:ro"
  }
]
```

**Security Options**:
```json
"securityOpt": [
  "systempaths=unconfined",
  "apparmor=unconfined"
]
```

**Special Images**:
- Use RKNN-optimized images when available (e.g., `immich-machine-learning:v2.4.1-rknn`)

---

## Testing

### Automated Tests

Location: `__tests__/apps.test.ts`

**Test Coverage**:
1. **Required Files Test**: Validates each app has all required files
2. **Config Schema Test**: Validates `config.json` against Runtipi schema
3. **Docker Compose Schema Test**: Validates `docker-compose.json` against Runtipi schema

**Running Tests**:
```bash
bun install
bun run test
```

**Test Dependencies**:
- `@runtipi/common`: Provides schema validation
- `zod-validation-error`: Provides detailed validation errors

---

## Creating a New App

### Step-by-Step Guide

1. **Create App Directory**
   ```bash
   mkdir -p apps/<app-id>/metadata
   ```

2. **Create `config.json`**
   - Use schema: `https://schemas.runtipi.io/v2/app-info.json`
   - Set unique `id` matching directory name
   - Define `form_fields` for user configuration
   - Set appropriate `port`, `categories`, `supported_architectures`
   - Use timestamps: `Date.now()` for `created_at` and `updated_at`

3. **Create `docker-compose.json`**
   - Use schema: `https://schemas.runtipi.io/v2/dynamic-compose.json`
   - Define all services
   - Mark main service with `isMain: true`
   - Map volumes using `${APP_DATA_DIR}` and other placeholders
   - Reference form field variables with `${ENV_VARIABLE}`
   - Set service dependencies with `dependsOn`

4. **Add Metadata**
   - Add `metadata/logo.jpg` (app logo)
   - Create `metadata/description.md` with app information

5. **Optional Files**
   - Add `docker-compose.yml` if YAML format is preferred
   - Add `docker-compose.user_config.yml` for user overrides

6. **Validate**
   ```bash
   bun run test
   ```

---

## Best Practices

### Configuration
- ✅ Use `dynamic_config: true` for most apps
- ✅ Use `random` type for secrets and passwords
- ✅ Provide sensible defaults for form fields
- ✅ Use placeholders to guide users
- ✅ Include hints for complex configuration

### Docker Compose
- ✅ Use `${APP_DATA_DIR}` for app-specific data
- ✅ Mount `/etc/localtime:ro` for correct timezone
- ✅ Use `dependsOn` for service ordering
- ✅ Set `restart: unless-stopped` in YAML format
- ✅ Include `runtipi.managed: true` label in YAML

### Security
- ✅ Use `random` type with `min: 32` for database passwords
- ✅ Minimize use of `securityOpt` and `devices`
- ✅ Use read-only mounts (`:ro`) where possible
- ✅ Avoid `privileged: true` unless absolutely necessary

### Architecture Support
- ✅ Specify `supported_architectures` accurately
- ✅ Test on target architecture (arm64 for Orange Pi)
- ✅ Use multi-arch images when available

### Documentation
- ✅ Provide clear descriptions in `description.md`
- ✅ Document default credentials
- ✅ List all configurable environment variables
- ✅ Include links to official documentation
- ✅ Add preview images when available

---

## Common Environment Variables

### Runtipi-Provided
- `${APP_DATA_DIR}`: App-specific data directory
- `${DATA_FOLDER_HOST}`: Host data folder path
- `${HOME_FOLDER_HOST}`: Host home folder path

### User-Defined (via form_fields)
- Any `env_variable` defined in form fields
- Reference with `${ENV_VARIABLE_NAME}`

### Standard Conventions
- `TZ`: Timezone (e.g., `Asia/Makassar`)
- `DB_PASSWORD`: Database password
- `DB_USERNAME`: Database username (often `tipi`)
- `DB_DATABASE_NAME`: Database name
- `JWT_SECRET`: JWT secret for authentication

---

## Categories

Common categories used in this repository:
- `utilities`: General utility apps
- `data`: Data management and storage
- `photography`: Photo and media apps
- `network`: Network services
- `security`: Security and VPN apps

---

## Version Management

### App Versioning
- Use semantic versioning: `v1.2.3`
- Match upstream project versions when possible
- Include build tags if needed: `v1.18.0-rolling-weekly`

### Runtipi Version
- `tipi_version`: App config version (1 or 2)
- `min_tipi_version`: Minimum Runtipi version (e.g., `4.5.0`)

---

## Troubleshooting

### Common Issues

**Test Failures**:
- Ensure all required files exist
- Validate JSON syntax
- Check schema compliance with `@runtipi/common`

**Container Issues**:
- Verify image availability for arm64
- Check device mappings for hardware access
- Ensure volume paths are correct
- Verify environment variables are set

**Network Issues**:
- Check `addToMainNetwork` setting
- Verify port mappings
- Ensure `exposable` is set correctly

---

## Examples

### Minimal App (Single Service)

**config.json**:
```json
{
  "$schema": "https://schemas.runtipi.io/v2/app-info.json",
  "name": "My App",
  "id": "myapp",
  "version": "v1.0.0",
  "tipi_version": 1,
  "available": true,
  "exposable": true,
  "dynamic_config": true,
  "https": false,
  "port": 8080,
  "short_desc": "A simple app",
  "description": "A simple application",
  "author": "Author",
  "categories": ["utilities"],
  "source": "https://github.com/...",
  "website": "https://...",
  "supported_architectures": ["arm64"],
  "created_at": 1234567890123,
  "updated_at": 1234567890123,
  "min_tipi_version": "4.5.0",
  "form_fields": []
}
```

**docker-compose.json**:
```json
{
  "$schema": "https://schemas.runtipi.io/v2/dynamic-compose.json",
  "schemaVersion": 2,
  "services": [
    {
      "name": "myapp",
      "image": "myapp/myapp:latest",
      "isMain": true,
      "internalPort": 8080,
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data",
          "containerPath": "/data"
        }
      ],
      "addToMainNetwork": true
    }
  ]
}
```

---

## Additional Resources

- **Runtipi Documentation**: https://runtipi.io/docs
- **Schema Definitions**: 
  - App Info: https://schemas.runtipi.io/v2/app-info.json
  - Dynamic Compose: https://schemas.runtipi.io/v2/dynamic-compose.json
- **Docker Compose Reference**: https://docs.docker.com/compose/

---

## Summary

This repository follows a structured pattern for defining Runtipi apps:

1. Each app has its own directory under `apps/`
2. Required files: `config.json`, `docker-compose.json`, `metadata/logo.jpg`, `metadata/description.md`
3. Configuration uses Runtipi v2 schemas
4. Apps are validated with automated tests
5. Hardware-specific optimizations for Orange Pi 3B RK3566

When creating new apps, follow the patterns established by existing apps (brave, immich, linkding, tailscale) and ensure all tests pass before deployment.
