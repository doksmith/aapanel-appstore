# aaPanel / Pagoda Panel - Docker AppStore Repository

This repository is used for indexing, publishing, and maintaining applications for the aaPanel / Pagoda Panel Docker AppStore.

- **Feedback & Discussions**: Submit [Issues](https://github.com/dyzulk-lab/appstore/issues)
- **Contributions Welcome**: Submit Pull Requests (PR) to add or update Docker applications and help grow the ecosystem.

---

## Quick Start: How to Submit an Application

1. Create a directory under `apps/<your-app-name>/` containing:
   - `app.json` (Application metadata)
   - `ico-dkapp_{appname}.png` (100x100 PNG icon)
   - A version directory matching `app.json.appname`, containing:
     - `docker-compose.yml`
     - `.env`
2. Write `app.json` (See field rules below; for the complete template and specifications, see `template.md`).
3. Write `docker-compose.yml` and `.env`, ensuring all variables are consistent and the container launches properly.
4. Test locally, then submit a Pull Request.

> [!TIP]
> Recommended reading: [template.md](template.md) for full specification templates, field mappings, and best practices.

---

## Directory Structure Example

```
apps/
└── wordpress/
    ├── app.json
    ├── ico-dkapp_wordpress.png
    └── wordpress/
        ├── docker-compose.yml
        └── .env
```

---

## Icon & Naming Conventions

- **Icon**: `ico-dkapp_${appname}.png` (Exactly 100x100 pixels, PNG format).
- **App Name**: `appname` should use lowercase letters with underscores (`_`) or hyphens (`-`). Avoid uppercase letters and whitespace.
- Directory name and `appname` should remain identical.

---

## Quality Assurance Checklist (Before Submitting PR)

- [ ] Directory contains `app.json`, `ico-dkapp_${appname}.png`, and the version directory with all necessary files.
- [ ] `.env` includes at least: `HOST_IP`, `CPUS`, `MEMORY_LIMIT`, `APP_PATH`, and application-specific variables.
- [ ] `docker-compose.yml` references uppercase variables from `.env`, with ports bound using the `${HOST_IP}:${PORT}` convention.
- [ ] Label `labels.createdBy: "bt_apps"` is present under the main service.
- [ ] Dependency declarations (`depend`), if required, are accurate and verified.
- [ ] Category label `appTypeCN` preserves its standard internal category identifier (e.g., `建站`, `数据库`, `实用工具`, `中间件`, `存储/网盘`).
- [ ] External links (`home`, `help`) are valid and accessible.

---

## Submission Workflow (PR Requirements)

- Verify that containers can be pulled and run successfully in a standard Docker environment.
- Adhere strictly to the guidelines in this document and `template.md`.
- Include in your PR description: brief application summary, image versions, key environment variables, and any database/service dependencies.
- When modifying icons, documentation, or compose files, ensure `app.json` and the version directory are updated simultaneously.

For further details, examples, and best practices, please refer to [template.md](template.md).
