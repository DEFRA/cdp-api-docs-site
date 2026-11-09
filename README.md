# cdp-api-docs-site

The CDP API catalogue, at the root of the CDP API docs site: the landing page, a list of advertised
APIs and a list of delivery groups.

Nothing is listed automatically. Teams add their API or group with a pull request, saying what it
does and who it is for. Tenant service docs are not here: each service publishes its own under
`api/<service>/<version>/`.

```
docs/guides/
├── index.md                 landing page, links to the two lists (platform)
├── apis/
│   └── index.md             list of advertised APIs (platform)
└── groups/
    ├── index.md             list of delivery groups (platform)
    └── <group>.md           one page per delivery group (delivery group)
```

## Advertise an API

Publish your API docs first (see `cdp-build-action/api-docs-build`). Then add one entry to
`docs/guides/apis/index.md` that links to them, says who owns the API (linking to your group page
if you have one), what it does and who it is for. This list is the only place the description
lives:

```markdown
- [CDP Uploader](/api/cdp-uploader/latest/) ([Platform](../groups/platform.md)): upload files and
  have them virus scanned before they reach your S3 bucket. For any service that accepts files from
  users.
```

## Add a delivery group

1. Add `docs/guides/groups/<group>.md`. Copy [`platform.md`](docs/guides/groups/platform.md): what
   your group does, the names of the APIs you own with links to their docs, and how to
   contact you. Describe each API in the APIs list, not here.
2. Add it to the list in `docs/guides/groups/index.md`:

   ```markdown
   - [Platform](./platform.md): builds and runs the Core Delivery Platform.
   ```

3. Add your page to `.github/CODEOWNERS`, so your group reviews its own changes.

The platform team reviews the landing page and both lists. The dry-run can't check links to
`/api/...`, because tenant docs are built elsewhere, so reviewers open each new one before approving.

## Publishing

Pull requests run a dry-run build. Merges to `main` build the site with base URL `/` and sync it to
the root of `s3://cdp-management-api-docs`, excluding `api/*` so tenant docs are never changed. The
sync runs in three steps (assets, then pages, then deletes), so `cdp-api-docs` never copies pages
whose assets aren't uploaded yet.

## Build locally

```bash
cd ~/dev/DEFRA/cdp-api-docs-builder
DOCS_BASE_URL=/ ./build.sh ~/dev/DEFRA/cdp-api-docs-site check
DOCS_BASE_URL=/ ./build.sh ~/dev/DEFRA/cdp-api-docs-site site
```

The site is written to `build-site/`.
