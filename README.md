# Antora Docker Build Action

A GitHub Action that builds an Antora documentation site using Docker. This action runs Antora inside a Docker container to generate your documentation site.

## Important Notes

### What This Action Does

This action:

- Builds a Docker image based on the official `antora/antora:latest` image
- Runs `antora site.yml` (or `antora antora.yml`) in your GitHub workspace
- Generates the Antora site output in the `build/site` directory (or as configured in your Antora playbook)

### What This Action Does NOT Do

This action does **NOT**:

- Directly generate a site compatible with GitHub Pages site generators
- Publish or deploy your site automatically
- Work as a "site generator" in GitHub Pages settings (it's a Docker-based action)

### GitHub Pages Compatibility

This is a **Docker-based action**, not a static site generator. If you're using GitHub Pages, you'll need to:

1. Use this action to build your site
2. Manually deploy the generated site files (typically from `build/site/`) to your GitHub Pages branch
3. Or use a separate deployment action after this one runs

This action is suitable for:

- Building Antora sites in CI/CD pipelines
- Generating documentation that will be deployed elsewhere
- Workflows where you need full control over the build and deployment process

## Prerequisites

- An Antora playbook file (`site.yml` or `antora.yml`) in your repository root
- Your Antora content sources properly configured in the playbook

## Usage

### Basic Example

```yaml
name: Build Documentation

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Build Antora Site
        uses: jsqu4re/antora_build@master

      - name: Upload artifacts
        uses: actions/upload-artifact@v3
        with:
          name: site
          path: build/site
```

### With Custom Playbook File

If your playbook is named `antora.yml` instead of `site.yml`, you'll need to modify the entrypoint or use a different approach, as this action currently runs `antora site.yml` by default.

### Complete Workflow with Deployment

```yaml
name: Build and Deploy Documentation

on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Build Antora Site
        uses: jsqu4re/antora_build@master

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./build/site
```

## Inputs

This action currently has no configurable inputs. It runs `antora site.yml` in the GitHub workspace directory.

## Outputs

This action currently has no outputs. The generated site files are created in the `build/site` directory (or as configured in your Antora playbook).

## How It Works

1. The action builds a Docker image from the included `Dockerfile`
2. The Docker image is based on `antora/antora:latest`
3. The container runs `antora site.yml` in your `$GITHUB_WORKSPACE` directory
4. Generated site files are created in the workspace and persist after the action completes

## Requirements

- Your repository must contain an Antora playbook file (`site.yml` by default)
- All content sources referenced in your playbook must be accessible
- Sufficient permissions for the GitHub Actions runner to access your content sources

## Limitations

- Currently hardcoded to run `antora site.yml` (cannot specify a different playbook file)
- No configurable inputs for customizing the Antora build
- Docker-based execution may be slower than native Antora installation
- Not directly compatible with GitHub Pages "site generator" workflow

## License

MIT License - see [LICENSE](LICENSE) file for details.
