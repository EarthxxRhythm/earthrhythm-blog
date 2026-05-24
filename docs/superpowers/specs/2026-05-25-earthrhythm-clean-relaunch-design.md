# EarthRhythm Clean Relaunch Design

Date: 2026-05-25

## Goal

Relaunch the existing EarthRhythm blog as a clean, maintainable Hugo site deployed to a Vercel preview domain. The work keeps the current Hugo stack and theme, keeps existing article content unchanged, and focuses on making the project stable, coherent, and easier to maintain.

## Scope

In scope:

- Fix Hugo configuration issues that prevent reliable local builds or Vercel deployments.
- Keep `baseURL` aligned with the Vercel temporary domain.
- Refresh non-article site copy: homepage, about page, title, subtitle, description, menus, and metadata.
- Preserve the existing EarthRhythm brand feeling.
- Clean repository structure so generated files and local build artifacts are not treated as source.
- Update deployment and project documentation so the next launch path is clear.

Out of scope:

- Editing article body content.
- Replacing Hugo or the current theme.
- Adding major new features.
- Configuring a custom domain.

## Current Project Context

The repository is a Hugo static blog using `hugo-theme-reimu`. It contains source content under `content/`, static assets under `static/`, theme files under `themes/`, configuration in `hugo.toml` and `config/_default/params.yml`, Vercel deployment settings, and generated output under `public/` and `resources/_gen/`.

Some displayed Chinese text appears as mojibake in terminal output, so configuration and markdown files need to be reviewed and rewritten with clean UTF-8 text where they are part of the site shell. Existing article files are left untouched unless a build failure requires a minimal metadata fix.

## Engineering Design

The source of truth will be the Hugo source tree, not generated output. The relaunch should keep these maintained:

- `hugo.toml`
- `config/_default/params.yml`
- `content/_index.md`
- `content/about/_index.md`
- `content/archives/_index.md`
- `content/friend/_index.md`
- `static/`
- `themes/`
- `vercel.json`
- project documentation

Generated files such as `public/`, `resources/_gen/`, and `.hugo_build.lock` should be ignored going forward. If they are already tracked, remove them from tracking without deleting the working source files needed by Hugo.

## Brand And Content Design

The blog should keep the EarthRhythm identity while sounding more finished:

- Site title: use `EarthRhythm` as the main brand.
- Subtitle and description: short, reflective, and suitable for a personal knowledge blog.
- Homepage: a concise welcome page that explains what the blog is for without touching the post list behavior supplied by the theme.
- About page: replace deployment-checklist style content with a clear personal/site introduction.
- Menu labels: keep simple Chinese navigation for home, archives, tags, categories, and about.
- Social links: keep GitHub and email if currently configured; avoid enabling unused integrations.

## Deployment Design

Vercel is the first deployment target. The site should build from source with Hugo and publish Hugo's generated output. The design assumes the Vercel temporary domain is the public launch target for now, not a custom domain.

The deployment documentation should describe:

- Local build command.
- Vercel import settings.
- Expected output directory.
- How to verify homepage, article pages, CSS, and navigation.

## Verification

Before calling the relaunch complete, run:

- A Hugo build command.
- A local preview if Hugo is available.
- A repository status check showing intentional source changes.

If Hugo is not installed locally, document that limitation and rely on configuration review plus Vercel build settings.

## Acceptance Criteria

- Hugo configuration is valid and readable.
- The site brand reads as EarthRhythm, not a temporary test site.
- Homepage and about page contain clean, intentional copy.
- Article content remains unchanged.
- Vercel deployment settings target the temporary Vercel domain.
- Generated output is ignored or removed from version tracking.
- Documentation explains how to build and deploy the site.
