## Set Up

```sh
brew install hugo
```

## Development

```sh
hugo server          # local dev server at http://localhost:1313
hugo server -D       # include draft posts
```

## Build

```sh
hugo --minify        # build to ./public/
```

## Content

```sh
hugo new posts/my-post-title.md     # new blog post (draft by default)
hugo new work/my-project.md         # new work item
```

To publish a post, set `draft: false` in its frontmatter.

To hide a page without deleting it, set `draft: true`.

To delete a page, just delete the file.

## Structure

```
content/
  about/index.md       # profile page
  work/*.md            # work/project items
  posts/*.md           # blog posts
assets/css/main.css    # all styles
layouts/               # Hugo templates
hugo.toml              # site config, social links, bio
```
