---
name: openclaw-master-skills
description: Curated collection of 1209+ best OpenClaw AI agent skills, weekly updated by MyClaw.ai
triggers:
  - browse openclaw skills
  - find openclaw agent capabilities
  - install openclaw skills
  - what skills are available for openclaw
  - show me openclaw skill collection
  - search openclaw master skills
  - add openclaw functionality
  - explore openclaw skill library
---

# OpenClaw Master Skills

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

The **OpenClaw Master Skills** repository is a curated, weekly-updated collection of 1209+ pre-built skills for OpenClaw AI agents. Maintained by [MyClaw.ai](https://myclaw.ai), this collection provides ready-to-use capabilities spanning AI/LLM tools, search, productivity, development, data processing, and more.

## What It Does

- **Skill Library**: 1209+ production-ready skills across 15+ categories
- **Weekly Updates**: Fresh skills and improvements every week
- **Organized Collection**: Skills grouped by category (AI Tools, Search, Productivity, Development, etc.)
- **Ready to Install**: Each skill is a self-contained module with instructions
- **Multi-Language Support**: Documentation in 8 languages (EN, CN, FR, DE, RU, JA, IT, ES)

## Installation

### Install Entire Collection

```bash
# Via ClawHub (recommended)
clawhub install openclaw-master-skills

# Or clone repository
git clone https://github.com/LeoYeAI/openclaw-master-skills.git
cd openclaw-master-skills
```

### Install Individual Skills

```bash
# Copy a single skill to your OpenClaw workspace
cp -r openclaw-master-skills/skills/<skill-name> ~/.openclaw/workspace/skills/

# Example: Install the browser-use skill
cp -r openclaw-master-skills/skills/browser-use ~/.openclaw/workspace/skills/
```

### Browse Skills

```bash
# Navigate to skills directory
cd openclaw-master-skills/skills/

# List all available skills
ls -la

# View a specific skill's README
cat browser-use/README.md
```

## Repository Structure

```
openclaw-master-skills/
├── skills/
│   ├── academic-deep-research/
│   ├── agent-browser/
│   ├── ai-humanizer/
│   ├── browser-use/
│   ├── playwright/
│   ├── web-search-plus/
│   └── ... (1209+ skills)
├── README.md
├── README.zh-CN.md
├── README.fr.md
└── ... (multilingual docs)
```

Each skill directory contains:
- `SKILL.md` or `README.md` — Skill documentation
- Source code (Python, JavaScript, etc.)
- Configuration files
- Dependencies list

## Key Skill Categories

### 🤖 AI & LLM Tools (50+ skills)
- `academic-deep-research` — Transparent research with full methodology
- `agent-browser` — Rust-based headless browser automation
- `ai-humanizer` — Transform AI text to human-like writing
- `deep-research-pro` — Multi-source research with citations
- `gemini` — Gemini CLI for Q&A and generation
- `openai-whisper` — Local speech-to-text
- `prompt-engineering-expert` — Advanced prompt optimization

### 🔍 Search & Web (21+ skills)
- `brave-search` — Web search via Brave API
- `tavily` — AI-optimized web search
- `web-search-plus` — Unified search with auto-routing
- `firecrawl` — Web scraping with anti-bot bypass
- `playwright` — Browser automation and extraction

### 📋 Productivity & Office (43+ skills)
- `1password` — Password management via CLI
- `apple-notes` — Manage Apple Notes
- `bear-notes` — Bear notes integration
- `agent-memory` — Persistent memory for agents

### 💻 Development & Code (85+ skills)
- `code-review` — Automated code review
- `git-workflow` — Git automation
- `docker-compose` — Container orchestration
- `postgres` — Database management

### 📊 Data & Analytics (30+ skills)
- `data-analysis` — Statistical analysis
- `csv-processor` — CSV manipulation
- `sql-query` — Database querying

## Usage Examples

### Finding Skills by Category

```python
import os
import json

def find_skills_by_category(category_keyword):
    """Find skills matching a category keyword."""
    skills_dir = "openclaw-master-skills/skills"
    matching_skills = []
    
    for skill_name in os.listdir(skills_dir):
        skill_path = os.path.join(skills_dir, skill_name)
        if os.path.isdir(skill_path):
            readme_path = os.path.join(skill_path, "README.md")
            if os.path.exists(readme_path):
                with open(readme_path, 'r') as f:
                    content = f.read().lower()
                    if category_keyword.lower() in content:
                        matching_skills.append(skill_name)
    
    return matching_skills

# Example: Find all browser-related skills
browser_skills = find_skills_by_category("browser")
print(f"Found {len(browser_skills)} browser skills:")
for skill in browser_skills:
    print(f"  - {skill}")
```

### Installing Multiple Skills

```python
import shutil
import os

def install_skills(skill_names, openclaw_workspace="~/.openclaw/workspace/skills"):
    """Install multiple skills to OpenClaw workspace."""
    workspace = os.path.expanduser(openclaw_workspace)
    os.makedirs(workspace, exist_ok=True)
    
    source_base = "openclaw-master-skills/skills"
    
    for skill_name in skill_names:
        source = os.path.join(source_base, skill_name)
        destination = os.path.join(workspace, skill_name)
        
        if os.path.exists(source):
            shutil.copytree(source, destination, dirs_exist_ok=True)
            print(f"✓ Installed {skill_name}")
        else:
            print(f"✗ Skill not found: {skill_name}")

# Example: Install essential research skills
research_skills = [
    "academic-deep-research",
    "web-search-plus",
    "tavily",
    "deep-research-pro"
]

install_skills(research_skills)
```

### Reading Skill Metadata

```python
import os
import re

def parse_skill_metadata(skill_name):
    """Extract metadata from a skill's README."""
    readme_path = f"openclaw-master-skills/skills/{skill_name}/README.md"
    
    if not os.path.exists(readme_path):
        return None
    
    with open(readme_path, 'r') as f:
        content = f.read()
    
    # Extract description (first table cell or paragraph)
    desc_match = re.search(r'\|\s*`[^`]+`\s*\|\s*([^|]+)\s*\|', content)
    description = desc_match.group(1).strip() if desc_match else ""
    
    return {
        "name": skill_name,
        "description": description,
        "path": readme_path
    }

# Example: Get metadata for browser-use skill
metadata = parse_skill_metadata("browser-use")
print(f"Skill: {metadata['name']}")
print(f"Description: {metadata['description']}")
```

### Creating a Custom Skill Index

```python
import os
import json

def build_skill_index():
    """Build a searchable index of all skills."""
    skills_dir = "openclaw-master-skills/skills"
    index = {}
    
    for skill_name in os.listdir(skills_dir):
        skill_path = os.path.join(skills_dir, skill_name)
        
        if not os.path.isdir(skill_path):
            continue
        
        # Check for Python files
        has_python = any(f.endswith('.py') for f in os.listdir(skill_path))
        
        # Check for Node.js files
        has_nodejs = any(f.endswith('.js') or f == 'package.json' 
                         for f in os.listdir(skill_path))
        
        # Read README for description
        readme_path = os.path.join(skill_path, "README.md")
        description = ""
        if os.path.exists(readme_path):
            with open(readme_path, 'r') as f:
                lines = f.readlines()
                if len(lines) > 1:
                    description = lines[1].strip()
        
        index[skill_name] = {
            "path": skill_path,
            "languages": {
                "python": has_python,
                "nodejs": has_nodejs
            },
            "description": description
        }
    
    return index

# Build and save index
index = build_skill_index()
with open("skill_index.json", 'w') as f:
    json.dump(index, f, indent=2)

print(f"Indexed {len(index)} skills")
```

## Configuration

### Environment Variables

Skills may require various API keys and credentials:

```bash
# Search APIs
export BRAVE_API_KEY="your_brave_api_key"
export TAVILY_API_KEY="your_tavily_api_key"
export GOOGLE_API_KEY="your_google_api_key"
export GOOGLE_CSE_ID="your_custom_search_engine_id"

# AI/LLM APIs
export OPENAI_API_KEY="your_openai_api_key"
export ANTHROPIC_API_KEY="your_anthropic_api_key"
export GEMINI_API_KEY="your_gemini_api_key"

# Other services
export FIRECRAWL_API_KEY="your_firecrawl_api_key"
export PERPLEXITY_API_KEY="your_perplexity_api_key"
```

### OpenClaw Workspace Structure

```bash
~/.openclaw/
├── workspace/
│   ├── skills/          # Installed skills
│   ├── config/          # Configuration files
│   └── data/            # Persistent data
└── settings.json        # Global settings
```

## Common Patterns

### Pattern 1: Multi-Skill Workflow

```python
# Combine search, analysis, and reporting skills
def research_workflow(topic):
    """Complete research workflow using multiple skills."""
    
    # 1. Search the web
    from skills.web_search_plus import search
    search_results = search(topic)
    
    # 2. Deep research
    from skills.deep_research_pro import research
    analysis = research(search_results, depth="comprehensive")
    
    # 3. Summarize findings
    from skills.summarize import summarize_text
    summary = summarize_text(analysis)
    
    return {
        "raw_results": search_results,
        "analysis": analysis,
        "summary": summary
    }
```

### Pattern 2: Skill Discovery

```python
def discover_skills_for_task(task_description):
    """Find relevant skills based on task description."""
    relevant_skills = []
    keywords = task_description.lower().split()
    
    skills_dir = "openclaw-master-skills/skills"
    
    for skill_name in os.listdir(skills_dir):
        # Check if any keyword matches skill name
        if any(keyword in skill_name.lower() for keyword in keywords):
            relevant_skills.append(skill_name)
    
    return relevant_skills

# Example
task = "browse websites and extract data"
skills = discover_skills_for_task(task)
# Returns: ['agent-browser', 'browser-use', 'playwright', 'firecrawl', ...]
```

### Pattern 3: Skill Dependencies

```python
def check_skill_dependencies(skill_name):
    """Check if a skill's dependencies are met."""
    skill_path = f"openclaw-master-skills/skills/{skill_name}"
    
    # Check for requirements.txt (Python)
    requirements_path = os.path.join(skill_path, "requirements.txt")
    if os.path.exists(requirements_path):
        with open(requirements_path) as f:
            print(f"Python dependencies for {skill_name}:")
            print(f.read())
    
    # Check for package.json (Node.js)
    package_path = os.path.join(skill_path, "package.json")
    if os.path.exists(package_path):
        with open(package_path) as f:
            package_data = json.load(f)
            if "dependencies" in package_data:
                print(f"Node.js dependencies for {skill_name}:")
                print(json.dumps(package_data["dependencies"], indent=2))

# Example
check_skill_dependencies("playwright")
```

## Troubleshooting

### Skill Not Found

```python
# Verify skill exists
skill_name = "browser-use"
skill_path = f"openclaw-master-skills/skills/{skill_name}"

if not os.path.exists(skill_path):
    print(f"Error: Skill '{skill_name}' not found")
    print("Available skills:")
    print("\n".join(os.listdir("openclaw-master-skills/skills")))
```

### Permission Issues

```bash
# Fix permissions on skills directory
chmod -R 755 openclaw-master-skills/skills/

# Fix OpenClaw workspace permissions
chmod -R 755 ~/.openclaw/workspace/skills/
```

### Missing Dependencies

```bash
# Install Python dependencies for a skill
cd openclaw-master-skills/skills/browser-use
pip install -r requirements.txt

# Install Node.js dependencies
cd openclaw-master-skills/skills/playwright
npm install
```

### API Key Issues

```python
import os

def verify_api_keys(skill_name):
    """Check if required API keys are set."""
    
    # Common API key requirements by skill type
    api_key_map = {
        "brave-search": ["BRAVE_API_KEY"],
        "tavily": ["TAVILY_API_KEY"],
        "openai-whisper-api": ["OPENAI_API_KEY"],
        "gemini": ["GEMINI_API_KEY"],
        "firecrawl": ["FIRECRAWL_API_KEY"]
    }
    
    required_keys = api_key_map.get(skill_name, [])
    missing_keys = []
    
    for key in required_keys:
        if not os.getenv(key):
            missing_keys.append(key)
    
    if missing_keys:
        print(f"Missing API keys for {skill_name}:")
        for key in missing_keys:
            print(f"  - {key}")
        return False
    
    return True

# Example
if not verify_api_keys("brave-search"):
    print("Set missing keys in your environment")
```

### Outdated Skills

```bash
# Update to latest version
cd openclaw-master-skills
git pull origin main

# Re-install updated skills
cp -r skills/browser-use ~/.openclaw/workspace/skills/
```

## Best Practices

1. **Review Before Installing**: Read each skill's documentation before installation
2. **Environment Variables**: Store API keys in environment variables, never in code
3. **Regular Updates**: Pull latest changes weekly to get new skills and improvements
4. **Skill Isolation**: Test new skills in a separate workspace before production use
5. **Documentation**: Keep notes on which skills work well together
6. **Version Control**: Track your installed skills configuration

## Integration with MyClaw.ai

The skills in this collection are curated by [MyClaw.ai](https://myclaw.ai), a platform that provides:

- Dedicated AI agent servers for each user
- Pre-configured skill environments
- Automatic skill updates
- Cloud-based execution
- Multi-agent orchestration

Visit [https://myclaw.ai](https://myclaw.ai) to try the hosted platform.

## Resources

- **Repository**: https://github.com/LeoYeAI/openclaw-master-skills
- **Homepage**: https://myclaw.ai
- **License**: MIT
- **Stars**: 1990+ (26 stars/day growth)
- **Forks**: 301
- **Issues**: https://github.com/LeoYeAI/openclaw-master-skills/issues
