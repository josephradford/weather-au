# PyPI Deployment Guide

This repository is configured for modern PyPI deployment using GitHub Actions and trusted publishing (OIDC).

## Prerequisites

### 1. PyPI Account Setup
- Create an account on [PyPI](https://pypi.org) (for production)
- Create an account on [TestPyPI](https://test.pypi.org) (for testing)

### 2. Configure Trusted Publishing on PyPI

Trusted publishing eliminates the need for API tokens by using GitHub's OIDC provider.

#### For Production PyPI:
1. Go to https://pypi.org/manage/account/publishing/
2. Add a new pending publisher with these details:
   - **PyPI Project Name**: `weather-au`
   - **Owner**: `tonyallan` (or your GitHub username)
   - **Repository name**: `weather-au`
   - **Workflow name**: `publish.yml`
   - **Environment name**: `pypi`

#### For TestPyPI:
1. Go to https://test.pypi.org/manage/account/publishing/
2. Add a new pending publisher with the same details:
   - **PyPI Project Name**: `weather-au`
   - **Owner**: `tonyallan`
   - **Repository name**: `weather-au`
   - **Workflow name**: `publish.yml`
   - **Environment name**: `testpypi`

### 3. Configure GitHub Environments (Optional but Recommended)

For additional security, configure deployment environments in GitHub:

1. Go to your repository **Settings** → **Environments**
2. Create two environments:
   - `pypi` (for production releases)
   - `testpypi` (for testing)
3. For the `pypi` environment, consider adding protection rules:
   - Required reviewers
   - Deployment branches (only main/master)

## Deployment Methods

### Method 1: Automatic Deployment on Release (Recommended)

1. **Update the version** in `pyproject.toml`:
   ```toml
   version = "0.0.8"  # Increment as needed
   ```

2. **Commit and push** your changes:
   ```bash
   git add pyproject.toml
   git commit -m "Bump version to 0.0.8"
   git push
   ```

3. **Create a GitHub Release**:
   - Go to your repository → **Releases** → **Draft a new release**
   - Create a new tag (e.g., `v0.0.8`)
   - Title: `Release 0.0.8`
   - Description: List changes and updates
   - Click **Publish release**

4. The GitHub Action will automatically:
   - Build the package
   - Publish to PyPI
   - Create a release on PyPI

### Method 2: Manual Deployment via Workflow Dispatch

For testing or manual deployments:

1. Go to **Actions** → **Publish to PyPI**
2. Click **Run workflow**
3. Select the branch
4. Click **Run workflow**

This will:
- Build the package
- Publish to **TestPyPI** only (for testing)

## Local Testing

### Build the Package Locally
```bash
# Install build tools
pip install build

# Build the package
python -m build

# Output will be in dist/
# - weather_au-X.Y.Z.tar.gz (source distribution)
# - weather_au-X.Y.Z-py3-none-any.whl (wheel)
```

### Test Installation Locally
```bash
# Install from local build
pip install dist/weather_au-0.0.7-py3-none-any.whl

# Or install in editable mode for development
pip install -e .
```

### Test Upload to TestPyPI (Manual)
```bash
# Install twine
pip install twine

# Upload to TestPyPI
python -m twine upload --repository testpypi dist/*

# Test installation from TestPyPI
pip install --index-url https://test.pypi.org/simple/ weather-au
```

## Version Management

The version is currently managed in `pyproject.toml`. Before each release:

1. Update the version number in `pyproject.toml`
2. Commit the change
3. Create a git tag matching the version
4. Push both the commit and tag

Future enhancement: Consider using dynamic versioning tools like:
- `setuptools_scm` (version from git tags)
- `versioneer`
- `bump2version` / `bumpver`

## Continuous Integration

The repository has two GitHub Actions workflows:

### 1. Build and Test (`build.yml`)
- Runs on: Push and Pull Requests
- Tests on: Python 3.8, 3.9, 3.10, 3.11, 3.12
- Runs: Linting (flake8) and tests (pytest)

### 2. Publish to PyPI (`publish.yml`)
- Triggers:
  - On GitHub Release (publishes to PyPI)
  - Manual workflow dispatch (publishes to TestPyPI)
- Uses: Trusted publishing (OIDC) - no API tokens needed
- Builds both: Source distribution and wheel

## Best Practices

1. **Always test on TestPyPI first**
   - Use manual workflow dispatch to test
   - Verify the package installs correctly
   - Check the package metadata

2. **Use semantic versioning**
   - MAJOR.MINOR.PATCH (e.g., 1.2.3)
   - Increment appropriately based on changes

3. **Write good release notes**
   - Document breaking changes
   - List new features
   - Note bug fixes

4. **Keep dependencies updated**
   - Review and update in `pyproject.toml`
   - Test with different Python versions

5. **Tag releases in git**
   - Use annotated tags: `git tag -a v0.0.8 -m "Release 0.0.8"`
   - Push tags: `git push --tags`

## Troubleshooting

### Build Fails
- Check `pyproject.toml` syntax
- Ensure all dependencies are listed
- Verify Python version compatibility

### PyPI Upload Fails
- Verify trusted publishing is configured correctly
- Check that the version doesn't already exist on PyPI
- Ensure the workflow has necessary permissions

### Tests Fail
- Run tests locally: `pytest`
- Check Python version compatibility
- Review test dependencies in `pyproject.toml`

## Resources

- [PyPI Trusted Publishing Guide](https://docs.pypi.org/trusted-publishers/)
- [Python Packaging User Guide](https://packaging.python.org/)
- [setuptools Documentation](https://setuptools.pypa.io/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
