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
├── shots.yml                 # shot-scraper configuration
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

# Run the scraper
shot-scraper multi shots.yml

# Or run individual commands:

# Take a screenshot only
shot-scraper https://www.flydenver.com/waittimesmonitorsview/ \
  --output screenshots/dia-wait-times.png \
  --width 1920 --height 1080 --wait 3000

# Scrape data only
shot-scraper javascript https://www.flydenver.com/waittimesmonitorsview/ \
  --javascript "your-javascript-here" \
  --output data/wait-times.json
```

## Configuration

The scraping behavior is configured in `shots.yml`. You can modify:

- **wait**: Time to wait before capturing (milliseconds)
- **width/height**: Screenshot dimensions
- **javascript**: Custom JavaScript for data extraction

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

The JavaScript extraction code in `shots.yml` attempts to parse the wait times page structure. If the page layout changes, the extraction logic may need to be updated to match the new structure.
