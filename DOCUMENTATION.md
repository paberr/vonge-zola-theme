# Vonge Zola Theme — Documentation

Full reference for configuring and using the Vonge Zola theme.

## Table of Contents

- [Content Structure](#content-structure)
- [Configuration Reference](#configuration-reference)
  - [Site Settings](#site-settings)
  - [Navigation](#navigation)
  - [Social Icons](#social-icons)
  - [Footer](#footer)
  - [Newsletter](#newsletter)
- [Content Blocks](#content-blocks)
  - [How Blocks Work](#how-blocks-work)
  - [Block Reference](#block-reference)
- [Page Extra Variables](#page-extra-variables)
  - [Blog Posts](#blog-posts)
  - [Projects](#projects)
  - [Testimonials](#testimonials)
- [Taxonomies](#taxonomies)

---

## Content Structure

The theme expects the following directory layout under `content/`:

```
content/
├── _index.md              # Homepage (uses config.toml content_blocks)
├── about.md               # About page (custom content blocks in front matter)
├── posts/
│   ├── _index.md          # Blog listing page
│   └── my-post.md         # Individual blog post
├── projects/
│   ├── _index.md          # Projects listing page
│   └── my-project.md      # Individual project page
└── testimonials/
    ├── _index.md          # Testimonials section config
    └── justin.md          # Individual testimonial
```

- **`posts/`** — Blog posts, sorted by date. The `_index.md` sets up pagination and the page layout via content blocks.
- **`projects/`** — Portfolio items, sorted by date. Uses the `project.html` page template.
- **`testimonials/`** — Client testimonials. Uses the `testimonial.html` page template. These are displayed by the `testimonials-section` block.
- **Custom pages** (e.g. `about.md`, `elements.md`) — Any standalone `.md` file in `content/` becomes a page. Use `extra.content_blocks` in front matter to control the layout.

---

## Configuration Reference

All theme settings live under `[extra]` in your `config.toml`.

### Site Settings

| Variable | Type | Description |
|---|---|---|
| `logo_image` | String | Path to the site logo displayed in the header (e.g. `"images/logo.svg"`) |
| `author_image` | String | Default author avatar shown on blog cards and post pages (e.g. `"/images/avatar.jpg"`) |
| `social_media_share_image` | String | Default Open Graph / Twitter Card image used when a page has no featured image |
| `date_format` | String | Strftime format for dates (e.g. `"%d %b %Y"`) |
| `language_direction` | String | Text direction on the `<html>` element — `"auto"`, `"ltr"`, or `"rtl"` |
| `keywords` | Array | Default meta keywords for SEO (e.g. `["keyword1", "keyword2"]`) |
| `twitter_handle` | String | Default Twitter `@handle` for Twitter Card meta tags |
| `google_analytics` | String | Google Analytics measurement ID (e.g. `"G-XXXXXXXXXX"`). Leave empty to disable. |
| `disqus_identifier` | String | Disqus shortname for comments on pages. Leave empty to disable. |

```toml
[extra]
logo_image = "images/logo.svg"
author_image = "/images/avatar.jpg"
social_media_share_image = "/images/01.jpg"
date_format = "%d %b %Y"
language_direction = "auto"
keywords = ["keyword1", "keyword2", "keyword3"]
twitter_handle = ""
google_analytics = ""
disqus_identifier = ""
```

### Navigation

The top navigation bar is defined as an array of link objects. Each entry has a `url` and `title`. An entry can optionally include a `submenu` array for dropdown menus.

Use `$BASE_URL` as a placeholder — it is replaced with your site's `base_url` at build time.

```toml
[extra]
navigation = [
    { url = "$BASE_URL", title = "Home" },
    { url = "$BASE_URL/projects", title = "Projects" },
    { url = "$BASE_URL/posts", title = "Blog" },
    { url = "$BASE_URL/tags", title = "Tags" },
    { url = "", title = "Pages", submenu = [
        { url = "$BASE_URL/about", title = "About" },
        { url = "$BASE_URL/elements", title = "Elements" }
    ] }
]
```

| Field | Type | Required | Description |
|---|---|---|---|
| `url` | String | Yes | Link target. Use `$BASE_URL` prefix for internal links. |
| `title` | String | Yes | Display text for the link. |
| `submenu` | Array | No | Nested array of `{url, title}` objects for a dropdown menu. |

### Social Icons

Social media links displayed as icons. The `icon` field maps to [Ionicons 4](https://ionicons.com/v4/) icon names.

```toml
[extra]
social_icons = [
    { url = "https://github.com/user", icon = "GitHub" },
    { url = "https://twitter.com/user", icon = "Twitter" },
    { url = "$BASE_URL/atom.xml", icon = "RSS" },
    { url = "https://pinterest.com", icon = "Pinterest" },
]
```

| Field | Type | Description |
|---|---|---|
| `url` | String | Link target. Supports `$BASE_URL` replacement. |
| `icon` | String | Ionicons 4 icon name (e.g. `"GitHub"`, `"Twitter"`, `"RSS"`, `"Pinterest"`). |

### Footer

The footer menu and optional copyright notice.

```toml
[extra.footer]
menu = [
    { url = "$BASE_URL", title = "Home" },
    { url = "$BASE_URL/projects/", title = "Projects" },
    { url = "$BASE_URL/about/", title = "About" },
    { url = "$BASE_URL/posts/", title = "Blog" },
]
copyright = "Copyright 2024 Your Name"
```

| Field | Type | Required | Description |
|---|---|---|---|
| `menu` | Array | Yes | Array of `{url, title}` link objects. Supports `$BASE_URL`. |
| `copyright` | String | No | Copyright text displayed at the bottom of the footer. |

### Newsletter

Global newsletter settings. These can be overridden per-page via the `newsletter` content block.

```toml
[extra.newsletter]
newsletter_title = "Join my mailing list"
newsletter_description = "Get inspiration, updates and, cool stuff!"
newsletter_identifier = ""
newsletter_button = "Subscribe"
```

| Field | Type | Description |
|---|---|---|
| `newsletter_title` | String | Heading text for the newsletter section. |
| `newsletter_description` | String | Description text below the heading. |
| `newsletter_identifier` | String | Mailchimp form action identifier. Leave empty to disable the form. |
| `newsletter_button` | String | Submit button text. |

---

## Content Blocks

### How Blocks Work

Vonge uses a **content block** system to build page layouts. Each page is composed of an ordered list of blocks, defined in the `extra.content_blocks` array.

- **Homepage:** Blocks are defined in `config.toml` under `[[extra.content_blocks]]`.
- **Sections and pages:** Blocks are defined in the file's front matter under `[extra]`.

Each block has a `block` field that selects the template, plus additional fields specific to that block type.

**Example** — a section `_index.md` with three blocks:

```toml
+++
title = "Blog"
sort_by = "date"
paginate_by = 6

[[extra.content_blocks]]
block = "page-heading"
title = "Blog"
description = "Latest articles and updates."

[[extra.content_blocks]]
block = "posts-list"
show_posts = true

[[extra.content_blocks]]
block = "newsletter"
newsletter_title = "Stay updated"
newsletter_description = "Subscribe for new posts."
newsletter_identifier = ""
newsletter_button = "Subscribe"
+++
```

### Block Reference

#### `hero`

Full-width hero section with image, text, and call-to-action buttons.

| Parameter | Type | Description |
|---|---|---|
| `title` | String | Main heading text. |
| `description_html` | String | Description rendered as HTML. Supports `<strong>`, `<em>`, etc. |
| `image` | String | Path to the hero image. |
| `image_alt` | String | Alt text for the hero image. |
| `cta_button` | String | Primary call-to-action button text. |
| `cta_button_link` | String | URL for the primary button. |
| `works_button` | String | Secondary button text. |
| `works_button_link` | String | URL for the secondary button. |

#### `projects-section`

Displays a preview of recent projects with a "view all" link.

| Parameter | Type | Description |
|---|---|---|
| `title` | String | Section heading. |
| `description_html` | String | Section description (HTML). |
| `link_url` | String | URL for the "view all projects" link. |
| `show_projects` | Boolean | Set to `true` to display projects. |

#### `testimonials-section`

Displays client testimonials from the `content/testimonials/` section.

| Parameter | Type | Description |
|---|---|---|
| `title` | String | Section heading. |
| `description_html` | String | Section description (HTML). |
| `show_testimonials` | Boolean | Set to `true` to display testimonials. |

#### `blog-section`

Displays a preview of recent blog posts with a "view all" link.

| Parameter | Type | Description |
|---|---|---|
| `title` | String | Section heading. |
| `description_html` | String | Section description (HTML). |
| `link_url` | String | URL for the "view all posts" link. |
| `show_posts` | Boolean | Set to `true` to display posts. |

#### `newsletter`

Email newsletter signup form (Mailchimp).

| Parameter | Type | Description |
|---|---|---|
| `newsletter_title` | String | Heading text. |
| `newsletter_description` | String | Description text. |
| `newsletter_identifier` | String | Mailchimp form action identifier. |
| `newsletter_button` | String | Submit button text. |

#### `contact-form`

Contact form that submits via email.

| Parameter | Type | Description |
|---|---|---|
| `form_title` | String | Form section heading. |
| `form_description` | String | Form section description. |
| `form_submission_email` | String | Email address that receives submissions. |
| `form_success_page` | String | URL to redirect to after successful submission. |
| `form_button_text` | String | Submit button text. |

#### `page-heading`

Page title and description header.

| Parameter | Type | Description |
|---|---|---|
| `title` | String | Page heading text. |
| `description` | String | Optional description below the heading. |

#### `page-image`

Full-width image block.

| Parameter | Type | Description |
|---|---|---|
| `image` | String | Path to the image. |
| `image_alt` | String | Alt text for the image. |

#### `content`

Raw HTML content block.

| Parameter | Type | Description |
|---|---|---|
| `content_html` | String | HTML content to render. |

#### `posts-list`

Paginated list of all blog posts. Use in `posts/_index.md`.

| Parameter | Type | Description |
|---|---|---|
| `show_posts` | Boolean | Set to `true` to display the post list. |

#### `projects-list`

Grid of all project cards. Use in `projects/_index.md`.

| Parameter | Type | Description |
|---|---|---|
| `show_projects` | Boolean | Set to `true` to display the project grid. |

> **Note:** Internal blocks like `blog-card`, `project-card`, `testimonial-card`, `pagination`, and `tags-list` are used by the theme internally and are not intended for direct use in content blocks.

---

## Page Extra Variables

Individual content pages use `[extra]` front matter variables for metadata.

### Blog Posts

| Variable | Type | Description |
|---|---|---|
| `image` | String | Featured image for the post. Shown on blog cards and the post page. |
| `author_image` | String | Author avatar override. Falls back to `config.extra.author_image`. |
| `seo.featured_image` | String | Open Graph / Twitter Card image override. Falls back to `extra.image`, then `config.extra.social_media_share_image`. |
| `seo.author_twitter_handle` | String | Twitter handle override. Falls back to `config.extra.twitter_handle`. |

```toml
+++
date = "2024-01-15"
title = "My Blog Post"
description = "A short summary for SEO and previews."

[taxonomies]
tags = ["travel", "photography"]

[extra]
image = "/images/post-1.jpg"
author_image = "/images/my-avatar.jpg"

[extra.seo]
featured_image = "/images/post-1-og.jpg"
author_twitter_handle = "@myhandle"
+++

Post content goes here...
```

### Projects

| Variable | Type | Description |
|---|---|---|
| `image` | String | Project card image. |

```toml
+++
date = "2024-01-10"
title = "Project Name"
description = "Photography"

[taxonomies]
tags = ["photography", "nature"]

[extra]
image = "/images/project-1.jpg"
+++

Project description goes here...
```

### Testimonials

| Variable | Type | Description |
|---|---|---|
| `name` | String | Client name. |
| `position` | String | Client title or role. |
| `image` | String | Client photo. |
| `blurb` | String | Testimonial text. |

```toml
+++
[extra]
name = "Jane Doe"
position = "Creative Director"
image = "/images/client-1.jpg"
blurb = "Working with this photographer was an absolute pleasure. The results exceeded all expectations."
+++
```

---

## Taxonomies

The theme uses a single `tags` taxonomy. Tags are displayed on blog cards and project pages.

Configure it in `config.toml`:

```toml
taxonomies = [
    { name = "tags", feed = true },
]
```

Assign tags in front matter:

```toml
[taxonomies]
tags = ["travel", "photography"]
```

The theme provides a tags listing page at `/tags/` and individual tag pages at `/tags/<tag-name>/`.
