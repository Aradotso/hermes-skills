---
name: openclaw-pr-assets
description: Manage screenshot and video proof assets for OpenClaw pull requests using GitHub releases
triggers:
  - how do I add screenshots to an OpenClaw PR
  - store proof assets for OpenClaw pull request
  - attach videos to OpenClaw PR without bloating repo
  - manage OpenClaw PR screenshots and videos
  - create release for OpenClaw PR assets
  - link assets to OpenClaw pull request
  - upload proof files for OpenClaw PR
  - organize OpenClaw PR media files
---

# openclaw-pr-assets

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This repository provides a pattern for storing screenshot and video proof assets for OpenClaw pull requests without bloating the main product repository. Each GitHub release tag corresponds to one PR, with assets attached to that release.

## What It Does

`openclaw-pr-assets` demonstrates a best practice for managing large binary proof assets (screenshots, videos, GIFs) that need to be referenced in pull requests:

- **Keeps the main repo clean**: Binary assets never live in git branches
- **Preserves proof**: Assets remain accessible even after PR merge/close
- **Organized by PR**: Each release tag maps to exactly one PR number
- **GitHub-hosted**: Uses GitHub Releases as free CDN for assets

## Repository Structure

```
openclaw-pr-assets/
├── README.md           # Documentation
└── (releases)          # All assets stored as GitHub Release attachments
```

## Workflow Pattern

### 1. Create a Release for Your PR

When you have a PR with screenshots or videos to attach:

```bash
# Tag format: pr-<number> where <number> is the PR number
PR_NUMBER=123
TAG="pr-${PR_NUMBER}"

# Create and push the tag
git tag -a "${TAG}" -m "Assets for OpenClaw PR #${PR_NUMBER}"
git push origin "${TAG}"
```

### 2. Create GitHub Release and Upload Assets

Using GitHub CLI (`gh`):

```bash
# Create release and upload assets
gh release create "${TAG}" \
  --repo steipete/openclaw-pr-assets \
  --title "PR #${PR_NUMBER} - Brief Description" \
  --notes "Proof assets for https://github.com/OpenClaw/OpenClaw/pull/${PR_NUMBER}" \
  screenshot1.png \
  demo-video.mp4 \
  comparison.gif
```

Or manually:
1. Go to https://github.com/steipete/openclaw-pr-assets/releases/new
2. Choose tag: `pr-123`
3. Release title: `PR #123 - Feature Description`
4. Description: Link to the actual PR
5. Attach files by drag-and-drop
6. Publish release

### 3. Reference Assets in Your PR

```markdown
## Screenshots

Before:
![Before](https://github.com/steipete/openclaw-pr-assets/releases/download/pr-123/before.png)

After:
![After](https://github.com/steipete/openclaw-pr-assets/releases/download/pr-123/after.png)

## Video Demo

[Demo Video](https://github.com/steipete/openclaw-pr-assets/releases/download/pr-123/demo.mp4)
```

## Automation Script

Create a helper script `upload-pr-assets.sh`:

```bash
#!/bin/bash
set -e

if [ $# -lt 2 ]; then
  echo "Usage: $0 <PR_NUMBER> <file1> [file2] [...]"
  echo "Example: $0 123 screenshot.png demo.mp4"
  exit 1
fi

PR_NUMBER=$1
shift
FILES=("$@")

TAG="pr-${PR_NUMBER}"
REPO="steipete/openclaw-pr-assets"

echo "Creating release ${TAG} for PR #${PR_NUMBER}..."

# Check if tag exists
if git rev-parse "${TAG}" >/dev/null 2>&1; then
  echo "Tag ${TAG} already exists"
else
  git tag -a "${TAG}" -m "Assets for OpenClaw PR #${PR_NUMBER}"
  git push origin "${TAG}"
fi

# Create or update release
gh release create "${TAG}" \
  --repo "${REPO}" \
  --title "PR #${PR_NUMBER}" \
  --notes "Proof assets for OpenClaw PR #${PR_NUMBER}
  
See: https://github.com/OpenClaw/OpenClaw/pull/${PR_NUMBER}" \
  "${FILES[@]}" || \
gh release upload "${TAG}" \
  --repo "${REPO}" \
  "${FILES[@]}"

echo ""
echo "Assets uploaded! Use these URLs in your PR:"
for file in "${FILES[@]}"; do
  filename=$(basename "${file}")
  echo "https://github.com/${REPO}/releases/download/${TAG}/${filename}"
done
```

Make it executable:

```bash
chmod +x upload-pr-assets.sh
```

Use it:

```bash
./upload-pr-assets.sh 123 before.png after.png demo.mp4
```

## Python Helper Script

For Python-based automation:

```python
#!/usr/bin/env python3
import sys
import subprocess
from pathlib import Path

def upload_pr_assets(pr_number: int, files: list[str]):
    """Upload assets for an OpenClaw PR"""
    tag = f"pr-{pr_number}"
    repo = "steipete/openclaw-pr-assets"
    
    # Create tag if it doesn't exist
    try:
        subprocess.run(
            ["git", "rev-parse", tag],
            check=True,
            capture_output=True
        )
        print(f"Tag {tag} already exists")
    except subprocess.CalledProcessError:
        subprocess.run(
            ["git", "tag", "-a", tag, "-m", f"Assets for OpenClaw PR #{pr_number}"],
            check=True
        )
        subprocess.run(["git", "push", "origin", tag], check=True)
    
    # Create release and upload files
    notes = f"Proof assets for OpenClaw PR #{pr_number}\n\nSee: https://github.com/OpenClaw/OpenClaw/pull/{pr_number}"
    
    cmd = [
        "gh", "release", "create", tag,
        "--repo", repo,
        "--title", f"PR #{pr_number}",
        "--notes", notes
    ] + files
    
    try:
        subprocess.run(cmd, check=True)
    except subprocess.CalledProcessError:
        # Release exists, upload to existing
        cmd = ["gh", "release", "upload", tag, "--repo", repo] + files
        subprocess.run(cmd, check=True)
    
    # Print URLs
    print("\nAssets uploaded! Use these URLs in your PR:")
    for file in files:
        filename = Path(file).name
        print(f"https://github.com/{repo}/releases/download/{tag}/{filename}")

if __name__ == "__main__":
    if len(sys.argv) < 3:
        print("Usage: upload_pr_assets.py <PR_NUMBER> <file1> [file2] ...")
        sys.exit(1)
    
    pr_num = int(sys.argv[1])
    files = sys.argv[2:]
    upload_pr_assets(pr_num, files)
```

## Best Practices

1. **Naming Convention**: Use descriptive filenames
   - `before-main-menu.png`
   - `after-fixed-rendering.png`
   - `bug-reproduction.mp4`

2. **File Formats**:
   - Screenshots: PNG (lossless, good for UI)
   - Videos: MP4 (widely supported)
   - Comparisons: GIF (auto-play in GitHub)

3. **Optimize Before Upload**:
   ```bash
   # Optimize PNG
   optipng screenshot.png
   
   # Convert video to smaller MP4
   ffmpeg -i input.mov -vcodec h264 -acodec aac output.mp4
   ```

4. **Tag Immediately**: Create the tag as soon as you open the PR to avoid confusion

## Troubleshooting

### Tag Already Exists

```bash
# Delete local tag
git tag -d pr-123

# Delete remote tag
git push origin :refs/tags/pr-123

# Recreate
git tag -a pr-123 -m "Assets for PR #123"
git push origin pr-123
```

### Upload Failed

```bash
# Upload to existing release
gh release upload pr-123 --repo steipete/openclaw-pr-assets new-file.png
```

### Wrong Files Uploaded

```bash
# Delete asset from release
gh release delete-asset pr-123 wrong-file.png --repo steipete/openclaw-pr-assets
```

## Integration with OpenClaw

Reference this pattern in OpenClaw PR templates:

```markdown
## Visual Proof

Please upload screenshots/videos to the [openclaw-pr-assets](https://github.com/steipete/openclaw-pr-assets) repo and link them here.

Example:
![Screenshot](https://github.com/steipete/openclaw-pr-assets/releases/download/pr-XXX/screenshot.png)
```
