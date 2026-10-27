# Application Template Specification

This guide outlines the specifications established across `apps/*` in this repository. It directs you on how to create a new application directory along with its corresponding `app.json`, version directory, `docker-compose.yml`, and `.env` files, ensuring that the aaPanel AppStore parses and renders them accurately.

---

## Directory Structure

```
apps/<appname>/                     # Application root directory (lowercase, matching appname in app.json)
├── app.json                        # Application metadata (core configuration)
├── ico-dkapp_${appname}.png        # Application icon (100x100 pixels, PNG format)
└── <appname>/                      # Application version directory (lowercase, matching appname in app.json)
    ├── docker-compose.yml
    └── .env
```

---

## app.json Template & Field Descriptions

Below is an annotated template. Optional fields can be included or omitted as needed, but all critical fields must be present.

```json
{
  "appid": 199,                            // Sequential ID based on the last entry in pkg/apps.json
  "appname": "alist",                      // Lowercase application identifier (matches directory name)
  "apptitle": "Alist",                     // Display title in AppStore
  "apptype": "Storage",                    // Application category type (see mapping table below)
  "appTypeCN": "存储/网盘",                 // Internal Chinese category (MUST remain Chinese for aaPanel tab filtering)
  "appversion": [                          // Version definitions (major and sub-versions)
    { "m_version": "latest", "s_version": [] },
    { "m_version": "3",      "s_version": ["42.0"] }
  ],
  "appdesc": "A file list program supporting multiple storage providers...", // Application description (English)
  "appstatus": 1,                           // 1 = Visible / Available, 0 = Hidden
  "home": "https://alist.nn.ci",            // Official website or GitHub repository
  "help": "https://alist.nn.ci/guide/",     // Documentation URL
  "updateat": 1752027587,                   // Unix timestamp in seconds
  "reuse": true,                            // Reserved setting (usually true)
  "cpu": 0,                                 // Default CPU limit (0 = unlimited)
  "mem": 0,                                 // Default memory limit in MB (0 = unlimited)
  "disk": 10240,                            // Reserved disk space setting in MB
  "depend": null,                           // Database dependency (can be null or an array as shown below)
  // "depend": [
  //   {
  //     "appname": ["mysql", "mariadb"],
  //     "apptype": "Database",
  //     "appTypeCN": "数据库服务",
  //     "appDesc": "Requires Docker MySQL or MariaDB to be installed first",
  //     "appversion": ["5", "8", "10", "11"]
  //   }
  // ],

  "field": [                                // Form fields displayed during panel installation (generates .env)
    { "attr": "domain",         "name": "Domain",                "type": "textarea", "default": "",     "suffix": "Domain name for browser access (optional)", "unit": "" },
    { "attr": "allow_access",   "name": "Allow External Access", "type": "checkbox", "default": true,   "suffix": "Allow direct access via host IP + port",      "unit": "" },
    { "attr": "cpus",           "name": "CPU Limit",             "type": "number",   "default": 0,      "suffix": "0 for unlimited, maximum available cores: ", "unit": "" },
    { "attr": "memory_limit",   "name": "Memory Limit",          "type": "number",   "default": 0,      "suffix": "0 for unlimited, maximum available memory: ", "unit": "" }
    // Additional custom fields (ports, passwords, URLs, GPU toggle, etc.):
    // { "attr": "web_http_port", "name": "Web Port",            "type": "number",   "default": 8080,   "suffix": "Service HTTP port", "unit": "" },
    // { "attr": "gpu",           "name": "Enable GPU",          "type": "checkbox", "default": false,  "suffix": "Requires aaPanel 9.5.0+", "unit": "" }
  ],

  "env": [                                  // Environment variable mappings (written in UPPERCASE to .env)
    { "key": "app_path",       "type": "path",    "default": null, "desc": "Application data directory" },
    { "key": "host_ip",        "type": "string",  "default": null, "desc": "Host IP" },
    { "key": "cpus",           "type": "number",  "default": null, "desc": "CPU core limit" },
    { "key": "memory_limit",   "type": "number",  "default": null, "desc": "Memory limit" }
    // Common types also include:
    // port, password, username, url, db_host, text, defaultUserName, defaultPassWord, etc.
    // Examples:
    // { "key": "web_http_port",    "type": "port",     "default": null, "desc": "Web HTTP port" },
    // { "key": "admin_password",   "type": "password", "default": null, "desc": "Administrator password" },
    // { "key": "api_base_url",     "type": "url",      "default": null, "desc": "API base URL" }
  ],

  "volumes": {                              // Volume declarations: Prompts host paths/files to be mounted
    "data": { "type": "path", "desc": "Data directory" },
    "conf": { "type": "file", "desc": "Configuration file" }
  }
}
```

### Key Rules & Guidelines:
- `field[].attr` maps 1:1 with `env[].key` (automatically converted to UPPERCASE in `.env`).
- `env[].type` determines how aaPanel validates and renders inputs:
  - `port`: Port availability check and auto-assignment.
  - `path`: Host directory path selector.
  - `password`: Masked secret input field.
  - `string` / `number`: Standard inputs.
- `depend`: Declares service dependencies (such as MySQL or MariaDB), prompting users to install dependencies first. See `nextcloud` or `wordpress` for examples.

---

### apptype and appTypeCN Mapping Table

> [!IMPORTANT]
> `appTypeCN` is an internal identifier used by the aaPanel UI category navigation and dependency resolution system. **It MUST be preserved in its original Chinese value**.

| `apptype` | `appTypeCN` (Must Keep Chinese) | English Meaning / Scope |
|---|---|---|
| **BuildWebsite** | `建站` | Website, CMS, Blog |
| **Database** | `数据库` | Relational / NoSQL Database |
| **Storage** | `存储/网盘` | Cloud Storage, WebDAV, Object Store |
| **Tools** | `实用工具` | Utility Tools, Admin Interfaces |
| **Middleware** | `中间件` | In-Memory Cache, Message Broker |
| **AI** | `AI/大模型` | LLM, AI Infrastructure, OCR |
| **Media** | `多媒体` | Streaming, Audiobooks, Music |
| **Email** | `邮件/邮局` | Mail Server, Webmail |
| **DevOps** | `DevOps` | CI/CD, Git, Development Tools |
| **System** | `系统` | OS Containers, System Utilities |

---

## .env File Specification

The following four variables are **mandatory** in all `.env` files:
- `HOST_IP`
- `CPUS`
- `MEMORY_LIMIT`
- `APP_PATH`

In addition, each `env[].key` declared in `app.json` is converted to uppercase in `.env` (e.g., `web_http_port` $\rightarrow$ `WEB_HTTP_PORT`).

```ini
# Application Custom Variables (Example)
ALIST_WEB_PORT=5244
S3_SERVER_PORT=5426

# Mandatory Universal Items
HOST_IP=127.0.0.1      # 0.0.0.0 if allow_access is checked; 127.0.0.1 for local host only
CPUS=0                 # 0 = unlimited
MEMORY_LIMIT=0         # 0 = unlimited
APP_PATH=              # Injected automatically by aaPanel
```

---

## docker-compose.yml Specification & Example

- Avoid using `container_name` to prevent collisions and ensure reliable panel container lifecycle tracking. Use the service name directly.
- When running multiple connected services (>5), define an internal custom bridge network.
- **Always include the label**: `labels.createdBy: "bt_apps"`.

```yaml
services:
  alist:
    image: xhofe/alist:v3.42.0
    deploy:
      resources:
        limits:
          cpus: ${CPUS}                      # CPU limit
          memory: ${MEMORY_LIMIT}            # Memory limit
    environment:
      - PUID=0
      - PGID=0
      - UMASK=022
    ports:
      - ${HOST_IP}:${ALIST_WEB_PORT}:5244    # Port binding format: ${HOST_IP}:${PORT}:container_port
      - ${HOST_IP}:${S3_SERVER_PORT}:5426
    restart: always
    volumes:
      - ${APP_PATH}/data:/opt/alist/data     # Recommend mounting under ${APP_PATH}/ for seamless backup
      - ${APP_PATH}/mnt:/mnt/data
    labels:
      createdBy: "bt_apps"                  # Mandatory label for aaPanel tracking
```

### Best Practices:
1. **Port Bindings**: Always format as `${HOST_IP}:${PORT}:<container_port>` to respect the `allow_access` toggle.
2. **Mount Paths**: Mount persistent data and configurations under `${APP_PATH}/` to support one-click backups and migrations.
3. **Labels**: The `createdBy: "bt_apps"` label is strictly required under the primary service.

---

## Quality Assurance Checklist (Self-Check)

- [ ] Directory structure contains `app.json`, `ico-dkapp_${appname}.png`, and `<appname>/` version folder with compose and `.env`.
- [ ] `.env` contains at least `HOST_IP`, `CPUS`, `MEMORY_LIMIT`, `APP_PATH`, and custom variables.
- [ ] `docker-compose.yml` uses uppercase variables from `.env` and port bindings follow `${HOST_IP}:${PORT}`.
- [ ] Label `labels.createdBy: "bt_apps"` is configured.
- [ ] If dependencies are declared, `depend` structure is valid and version ranges are accurate.
- [ ] Icon is exactly 100x100 pixels in PNG format; links for `home` and `help` are active and accessible.
