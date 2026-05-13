# DMX Lab Website

This repository contains the DMX Lab website, based on the al-folio Jekyll theme.

## Deployment

For an organization homepage under the current `ProjectDMX` organization, the repository should be named:

```text
ProjectDMX.github.io
```

The site is configured for:

```text
https://projectdmx.github.io
```

If the GitHub organization is later renamed to `DMXLab`, rename the repository to `DMXLab.github.io` and update `_config.yml`:

```yml
url: https://dmxlab.github.io
baseurl: ""
```

## Local Development

```bash
bundle install
bundle exec jekyll serve
```
