# GitHub Repository Setup Instructions

Follow these steps to create and configure the GitHub repository for VSM documentation.

## 1. Create the Repository

1. Go to https://github.com/new
2. Repository settings:
   - **Owner**: `viable-systems` (create organization first if needed)
   - **Repository name**: `vsm-docs`
   - **Description**: "Comprehensive documentation for the Viable Systems Model implementation in Elixir"
   - **Public** repository
   - **DO NOT** initialize with README, .gitignore, or license (we already have them)

## 2. Push Local Repository

After creating the empty repository on GitHub, run these commands:

```bash
# Add the remote repository
git remote add origin https://github.com/viable-systems/vsm-docs.git

# Push the main branch
git push -u origin main
```

## 3. Configure GitHub Pages

1. Go to Settings → Pages
2. Source: Deploy from a branch
3. Branch: `main` → `/site` folder
4. Save

Note: The first deployment will happen automatically when the GitHub Actions workflow runs.

## 4. Configure Repository Settings

### General Settings
- Features:
  - ✅ Issues
  - ✅ Projects
  - ✅ Wiki (optional)
  - ❌ Sponsorships (unless desired)
  - ✅ Discussions
  - ❌ Preserve this repository

### Branch Protection Rules
1. Go to Settings → Branches
2. Add rule for `main` branch:
   - ✅ Require a pull request before merging
   - ✅ Require status checks to pass before merging
   - ✅ Require branches to be up to date before merging
   - Required status checks: `build`

### Actions Settings
1. Go to Settings → Actions → General
2. Actions permissions: Allow all actions and reusable workflows
3. Workflow permissions: Read and write permissions

## 5. Add Repository Topics

Add these topics to help with discoverability:
- `vsm`
- `viable-systems-model`
- `elixir`
- `documentation`
- `mkdocs`
- `cybernetics`
- `systems-thinking`

## 6. Create Initial Issues

Create these issues to track remaining work:

### Issue 1: Complete Subsystem Documentation
```markdown
**Title**: Complete documentation for Algedonic and Temporal Variety channels

**Description**:
- [ ] Document Algedonic Channel concepts and implementation
- [ ] Document Temporal Variety Channel (our novel contribution)
- [ ] Add code examples for both channels
- [ ] Include API references
```

### Issue 2: Add API Reference Structure
```markdown
**Title**: Create comprehensive API reference documentation

**Description**:
- [ ] Set up auto-generation from ExDoc
- [ ] Create API overview pages
- [ ] Document all public modules
- [ ] Add usage examples
```

### Issue 3: Create Tutorial Series
```markdown
**Title**: Develop step-by-step tutorials

**Description**:
- [ ] "Building Your First VSM System" tutorial
- [ ] "Integrating with Phoenix" guide
- [ ] "Scaling VSM Systems" advanced tutorial
- [ ] Video companion content (optional)
```

## 7. Set Up Team Access

If working with a team:
1. Go to Settings → Manage access
2. Add team members with appropriate roles:
   - **Maintain**: For documentation contributors
   - **Write**: For regular contributors
   - **Admin**: For core team members

## 8. Enable GitHub Environments

1. Go to Settings → Environments
2. Create `github-pages` environment
3. Add protection rules if desired

## 9. First Deployment

After pushing the code:
1. Check Actions tab for workflow status
2. Once successful, documentation will be available at:
   https://viable-systems.github.io/vsm-docs/

## 10. Add Badges to README

After setup, update the README.md with status badges:

```markdown
[![Documentation](https://github.com/viable-systems/vsm-docs/actions/workflows/deploy.yml/badge.svg)](https://github.com/viable-systems/vsm-docs/actions/workflows/deploy.yml)
[![GitHub Pages](https://img.shields.io/badge/docs-live-brightgreen)](https://viable-systems.github.io/vsm-docs/)
```

## Troubleshooting

### Pages not deploying
- Check Actions tab for errors
- Ensure GitHub Pages is enabled in settings
- Verify the `site/` directory exists after build

### Workflow failing
- Check Python version compatibility
- Ensure all dependencies in requirements.txt are available
- Review workflow logs for specific errors

### Access issues
- Verify organization permissions
- Check repository visibility settings
- Ensure team members have correct roles

---

Once these steps are complete, the VSM documentation will be live and automatically updated with each push to the main branch!