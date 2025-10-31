# Denver International Airport (DIA) Wait Times Scraper

This repository automatically scrapes and archives wait times from [Denver International Airport](https://www.flydenver.com/waittimesmonitorsview/) using [shot-scraper](https://shot-scraper.datasette.io/en/stable/).

## What it does

This scraper:
- 📸 Captures screenshots of the DIA wait times monitor page
- 📊 Extracts structured wait time data as JSON
- ⏰ Runs automatically every hour via GitHub Actions
- 📝 Commits changes to track wait times over time

## Repository Structure

```
├── .github/workflows/
│   └── scrape.yml           # GitHub Actions workflow for automated scraping
├── screenshots/              # PNG screenshots of the wait times page
│   └── dia-wait-times.png
├── data/                     # Structured JSON data
│   └── wait-times.json
├── shots.yml                 # shot-scraper screenshot configuration
├── scrape.js                 # JavaScript code for data extraction
├── requirements.txt          # Python dependencies
└── README.md                 # This file
```

## Data Format

The scraped data is saved in `data/wait-times.json` with the following structure:

```json
{
  "timestamp": "2024-01-01T12:00:00.000Z",
  "source": "Denver International Airport",
  "url": "https://www.flydenver.com/waittimesmonitorsview/",
  "checkpoints": [
    {
      "location": "Security Checkpoint Name",
      "waitTime": "10 minutes",
      "rawData": ["...", "..."]
    }
  ]
}
```

## How it works

1. **shot-scraper**: Uses Playwright to load the DIA wait times page
2. **JavaScript extraction**: Runs custom JavaScript to parse wait time data from the page
3. **GitHub Actions**: Automatically runs every hour on the schedule `15 * * * *`
4. **Version control**: All changes are committed, creating a historical record

## Running Locally

### Prerequisites

- Python 3.10 or higher
- pip

### Installation

```bash
# Install dependencies
pip install -r requirements.txt

# Install Playwright browsers
shot-scraper install
```

### Usage

```bash
# Create output directories
mkdir -p screenshots data

# Take screenshots (configured in shots.yml)
shot-scraper multi shots.yml

# Scrape wait times data as JSON
shot-scraper javascript https://www.flydenver.com/waittimesmonitorsview/ \
  --javascript "$(cat scrape.js)" \
  --output data/wait-times.json \
  --wait 3000

# Or run both with a single script
shot-scraper multi shots.yml && \
shot-scraper javascript https://www.flydenver.com/waittimesmonitorsview/ \
  --javascript "$(cat scrape.js)" \
  --output data/wait-times.json \
  --wait 3000
```

## Configuration

### shots.yml
Screenshot configuration. You can modify:
- **wait**: Time to wait before capturing (milliseconds)
- **width/height**: Screenshot dimensions

### scrape.js
JavaScript code for extracting wait times data. Modify this file to:
- Change data extraction logic
- Add new fields to capture
- Handle different page structures

## Automation

The GitHub Actions workflow (`.github/workflows/scrape.yml`) runs:

- ⏰ Every hour at minute 15
- 🔄 On every push to the repository
- 🖱️ Manually via workflow dispatch

## Credits

- **shot-scraper** by [Simon Willison](https://github.com/simonw/shot-scraper)
- Inspired by [eo-scraper](https://github.com/gtfierro/eo-scraper)

## License

See [LICENSE](LICENSE) file for details.

## Notes

The JavaScript extraction code in `scrape.js` attempts to parse the wait times page structure using multiple strategies (tables, div-based layouts, etc.). If the page layout changes, the extraction logic may need to be updated to match the new structure.
