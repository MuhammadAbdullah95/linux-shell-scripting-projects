# Weather Forecast Accuracy Tracking System

An automated weather monitoring and forecast validation system that tracks daily weather data for Casablanca, Morocco and analyzes forecast accuracy over time.

## 📋 Overview

This is a two-part system that:
1. **Collects** live weather data and forecasts from wttr.in API
2. **Validates** forecast accuracy by comparing predictions with actual temperatures
3. **Categorizes** accuracy into performance tiers (Excellent, Good, Fair, Poor)
4. **Maintains** historical records for trend analysis

## 🌤️ System Components

### 1. rx_poc.sh - Weather Data Collector

**Purpose**: Daily weather observation and forecast extraction

**Functionality**:
- Fetches live weather report from wttr.in API for Casablanca
- Extracts current temperature with regex pattern matching
- Extracts tomorrow's noon forecast temperature
- Records data with timezone-aware timestamps
- Logs all information to `rx_poc.log`

**Key Operations**:
```bash
# Fetch weather data
curl -s wttr.in/Casablanca?T --output weather_report

# Extract current temperature
obs_temp=$(curl -s wttr.in/Casablanca?T | grep -m 1 '°.' | grep -Eo -e '-?[[:digit:]].*')

# Extract forecast for tomorrow noon
fc_temp=$(curl -s wttr.in/Casablanca?T | head -23 | tail -1 | grep '°.' | cut -d 'C' -f2 | grep -Eo -e '-?[[:digit:]].*')
```

### 2. fc_accuracy.sh - Forecast Accuracy Analyzer

**Purpose**: Validates weather forecast accuracy

**Functionality**:
- Reads yesterday's forecast from log file
- Compares with today's actual temperature
- Calculates accuracy (forecast error)
- Categorizes accuracy into performance tiers
- Appends analysis to historical TSV file

**Accuracy Categories**:
| Difference | Category | Description |
|------------|----------|-------------|
| ±1°C | Excellent | Highly accurate forecast |
| ±2°C | Good | Reliable forecast |
| ±3°C | Fair | Acceptable forecast |
| >3°C | Poor | Inaccurate forecast |

## 🔄 System Workflow

```
Day 1 (12:00 PM)
├── rx_poc.sh executes
├── Fetches: Current temp = 20°C, Tomorrow's forecast = 22°C
└── Logs: "2025 11 01 20°C 22°C" → rx_poc.log

Day 2 (12:00 PM)
├── rx_poc.sh executes
├── Fetches: Current temp = 21°C, Tomorrow's forecast = 23°C
├── Logs: "2025 11 02 21°C 23°C" → rx_poc.log
│
├── fc_accuracy.sh executes (12:05 PM)
├── Reads: Yesterday's forecast = 22°C
├── Compares: Today's actual = 21°C
├── Calculates: Accuracy = 22 - 21 = 1°C
├── Category: "Excellent" (within ±1°C)
└── Logs: "2025 11 02 21 22 1 excellent" → historical_fc_accuracy.tsv
```

## 🛠️ Technologies

- **Shell**: Bash scripting
- **API**: wttr.in weather service
- **Tools**: curl, grep, cut, head, tail
- **Data Format**: TSV (Tab-Separated Values)
- **Timezone**: Morocco/Casablanca

## 📥 Installation

```bash
# Make scripts executable
chmod +x rx_poc.sh fc_accuracy.sh
```

## 💻 Usage

### Manual Execution

**Collect today's weather data:**
```bash
./rx_poc.sh
```

Output:
```
The current Temperature of Casablanca: 23(25) °C
The forecasted temperature for noon tomorrow for Casablanca : 22 C
```

**Analyze forecast accuracy:**
```bash
./fc_accuracy.sh
```

Output:
```
accuracy is 1
Forecast accuracy is excellent
```

### Automated Execution with Cron

```bash
# Edit crontab
crontab -e

# Add these lines:
# Collect weather data daily at 12:00 PM
0 12 * * * /usr/local/bin/rx_poc.sh

# Calculate accuracy daily at 12:05 PM
5 12 * * * /usr/local/bin/fc_accuracy.sh
```

## 📊 Data Files

### rx_poc.log
Daily weather observations and forecasts
```
year    month   day     obs_temp        fc_temp
2025    11      01      23(25) °C       22(25) ° C
2025    11      02      21 °C           23 °C
```

### historical_fc_accuracy.tsv
Forecast accuracy analysis results
```
year    month   day     obs_temp    fc_temp     accuracy    accuracy_range
2025    11      01      23          22          -1          excellent
2025    11      02      21          23          2           good
```

### weather_report
Raw weather data from wttr.in API (ASCII art format)

## 🎯 Skills Demonstrated

### 1. API Integration
- RESTful API consumption with curl
- Handling HTTP responses
- Parsing text-based API output

### 2. Text Processing
- **grep** with regex patterns (`-Eo`, `-m 1`)
- **cut** for field extraction
- **head/tail** for line selection
- Command chaining with pipes

### 3. Data Validation
```bash
if [ -1 -le $accuracy ] && [ $accuracy -le 1 ]
then
    accuracy_range=excellent
elif [ -2 -le $accuracy ] && [ $accuracy -le 2 ]
then
    accuracy_range=good
```

### 4. Date/Time Handling
```bash
TZ='Morocco/Casablanca'
day=$(TZ='Morocco/Casablanca' date -u +%d)
month=$(TZ='Morocco/Casablanca' date +%m)
year=$(TZ='Morocco/Casablanca' date +%Y)
```

### 5. Arithmetic Operations
```bash
accuracy=$(($yesterday_fc - $today_temp))
```

### 6. File I/O
- Reading from log files with `tail` and `head`
- Writing TSV data with tab delimiters
- Appending to files with `>>`

## 📈 Real-World Applications

This system demonstrates concepts used in:

1. **Meteorological Services**: Validating forecast model accuracy
2. **Data Engineering**: ETL (Extract, Transform, Load) pipelines
3. **Quality Assurance**: Measuring prediction performance
4. **Time Series Analysis**: Building historical datasets
5. **API Integration**: Working with third-party data sources
6. **Automated Monitoring**: Scheduled data collection

## 🔧 Technical Insights

### Why Casablanca?
- City parameter: `city=Casablanca`
- Timezone: `Morocco/Casablanca`
- API endpoint: `wttr.in/Casablanca?T`

### Regex Pattern for Temperature Extraction
```bash
grep -Eo -e '-?[[:digit:]].*'
```
- `-E`: Extended regex
- `-o`: Only matching part
- `-?`: Optional minus sign (for negative temps)
- `[[:digit:]].*`: Digits followed by any characters

### TSV Format Benefits
- Easy to parse with `cut`
- Human-readable
- Importable into spreadsheets
- Compatible with data analysis tools

## 🔮 Future Enhancements

1. **Multi-City Support**: Track multiple cities simultaneously
2. **Database Integration**: Store data in SQLite or PostgreSQL
3. **Data Visualization**: Generate graphs with gnuplot or Python
4. **Statistical Analysis**: Calculate mean accuracy, standard deviation
5. **Alerting**: Email notifications for poor accuracy
6. **Web Dashboard**: Real-time display with HTML/JavaScript
7. **Machine Learning**: Predict accuracy based on weather patterns
8. **API Fallback**: Multiple weather API sources for reliability
9. **Historical Comparison**: Compare accuracy across seasons
10. **Export Features**: Generate CSV/JSON reports

## 📊 Sample Analysis

Based on collected data:
- **Location**: Casablanca, Morocco
- **Current Conditions**: Partly cloudy, 23°C (feels like 25°C)
- **Wind**: 5 km/h Southeast
- **Visibility**: 6 km
- **Tomorrow's Forecast**: 22°C at noon
- **Accuracy Tracking**: Operational since Nov 1, 2025

## 🧪 Testing

```bash
# Test weather data collection
./rx_poc.sh

# Verify log file
cat rx_poc.log

# Test accuracy calculation
./fc_accuracy.sh

# Check historical data
cat historical_fc_accuracy.tsv
```

## 🐛 Troubleshooting

**Issue**: No temperature extracted
- **Solution**: Check internet connection, verify wttr.in is accessible

**Issue**: Accuracy calculation errors
- **Solution**: Ensure rx_poc.log has at least 2 entries

**Issue**: Timezone incorrect
- **Solution**: Verify TZ variable is set correctly

## 📚 Learning Outcomes

- ✅ API integration and data fetching
- ✅ Advanced text processing with grep/cut/head/tail
- ✅ Regular expressions for pattern matching
- ✅ File parsing and data extraction
- ✅ Conditional logic and range checking
- ✅ Timezone-aware date handling
- ✅ TSV data format management
- ✅ Automation with cron jobs
- ✅ Building data pipelines
- ✅ Quality metrics and validation

## 👤 Author

**Muhammad Abdullah**
- Course: IBM's "Hands-on Introduction to Linux Commands and Shell Scripting"
- Project: Weather Data Analysis System

## 📄 License

MIT License - Open source and free to use.

## 🙏 Acknowledgments

- **wttr.in** - Free weather API service by Igor Chubin
- **IBM Skills Network** - Course provider

---

*Automated Weather Intelligence System - Built with Bash*
