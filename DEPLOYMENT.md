# PyPI Deployment Guide

This repository uses modern PyPI deployment with GitHub Actions and trusted publishing (no API tokens needed).

## One-Time Setup

### 1. Configure Trusted Publishing on PyPI

1. Go to https://pypi.org/manage/account/publishing/
2. Add a new pending publisher:
   - **PyPI Project Name**: `weather-au`
   - **Owner**: `tonyallan` (or repository owner)
   - **Repository name**: `weather-au`
   - **Workflow name**: `publish.yml`
   - **Environment name**: `pypi`

That's it! No API tokens or passwords needed.

## Deploying a New Release

1. **Update version** in `pyproject.toml`:
   ```toml
   version = "0.0.8"
   ```

2. **Commit and push**:
   ```bash
   git add pyproject.toml
   git commit -m "Bump version to 0.0.8"
   git push
   ```

3. **Create a GitHub Release**:
   - Go to repository → **Releases** → **Draft a new release**
   - Create tag: `v0.0.8`
   - Title: `Release 0.0.8`
   - Describe the changes
   - Click **Publish release**

4. **Done!** The GitHub Action automatically builds and publishes to PyPI.

## Local Development

### Install for development:
```bash
pip install -e .[dev]
```

### Build locally:
```bash
pip install build
python -m build
```

### Run tests:
```bash
pytest
```

## Testing Before Release

To test on TestPyPI before releasing:

1. Go to **Actions** → **Publish to PyPI** → **Run workflow**
2. This publishes to TestPyPI only
3. Test install: `pip install --index-url https://test.pypi.org/simple/ weather-au`

## Resources

- [PyPI Trusted Publishing](https://docs.pypi.org/trusted-publishers/)
- [Python Packaging Guide](https://packaging.python.org/)
