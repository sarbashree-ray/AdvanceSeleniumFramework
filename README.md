Selenium Automation Framework Architecture

selenium-automation-framework/
│
├── **src/main/java/com/qa/**
│   ├── annotations/                  # Custom annotations
│   │   └── Marker.java               # Marker interface for test categorization
│   │
│   ├── base/                         # Core framework classes
│   │   ├── BaseClass.java            # Test lifecycle management
│   │   ├── CustomListener.java       # TestNG listener
│   │   ├── DriverManager.java        # WebDriver management
│   │   ├── TestNGSuiteManager.java   # Dynamic test suite generator
│   │   └── WaitExecuter.java         # Custom wait utilities
│   │
│   ├── constants/                    # Constants
│   │   ├── ConfigConstants.java      # Configuration keys
│   │   ├── DirectoryConstants.java   # Path constants
│   │   └── FileConstants.java        # File names
│   │
│   ├── pageobjects/                  # Page Object Model
│   │   └── *.java                    # Page classes (LoginPage, DashboardPage, etc.)
│   │
│   ├── scripts/                      # Test scripts
│   │   ├── LoginTest.java            # Test classes
│   │   └── *.java                    
│   │
│   └── utils/                        # Utilities
│       ├── FileUtils.java            # File operations
│       ├── LoggingUtils.java         # Enhanced logging
│       ├── RetryExecuter.java        # Test retry logic
│       └── ScreenshotHelper.java     # Screenshot capture
│
├── **src/test/resources/**
│   ├── config/                       # Configuration files
│   │   ├── config.properties         # Environment config
│   │   └── extent_config.xml         # ExtentReports config
│   │
│   └── test-data/                    # Test data files
│       └── *.json/csv                # Data-driven test inputs
│
├── **target/**
│   ├── extent-reports/               # Generated HTML reports
│   │   └── ExtentReport.html         # Primary report file
│   │
│   ├── screenshots/                  # Failure screenshots
│   └── archive/                      # Archived test runs
│
├── **dashboard/                      # Streamlit Monitoring System**
│   ├── app.py                        # Main Streamlit application
│   ├── components/                   # UI components
│   │   ├── charts.py                 # Plotly visualization
│   │   └── watcher.py                # File system watcher
│   │
│   ├── models/                       # Data models
│   │   └── TestResult.py             # Pydantic model for test data
│   │
│   └── services/                     # Business logic
│       ├── ReportParser.py           # ExtentReports HTML parser
│       └── ArchiveService.py         # Results archiving
│
├── **pom.xml**                       # Maven build config
├── **requirements.txt**              # Python dependencies
└── **README.md**                     # Project documentation


Key Architectural Components
1. Core Test Framework (Java)
BaseClass: Handles test lifecycle (before/after methods)

DriverManager: Implements browser-specific WebDriver configurations

CustomListener: Captures test events for ExtentReports

Marker Annotations: @Smoke, @Regression for test categorization

2. Page Object Model
Separates UI locators and actions from test scripts

Example: LoginPage.java contains all login-related selectors and methods

3. Reporting System
ExtentReports: Generates interactive HTML reports

Screenshot Integration: Auto-captures on test failures

Archive Service: Stores historical runs with timestamps

4. Streamlit Dashboard (Python)
python
class RealTimeDashboard:
    def __init__(self):
        self.report_dir = "target/extent-reports"
        self.setup_data_pipeline()  # Kafka/RabbitMQ for scale
        
    def setup_data_pipeline(self):
        # Watches for:
        # 1. New test executions
        # 2. Report file changes
        # 3. Screenshot updates
        pass
5. CI/CD Integration
yaml
# Sample GitHub Actions workflow
jobs:
  test-execution:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: mvn test -Dmarkers="smoke"
      
      # Parallel dashboard deployment
      - run: |
          pip install -r requirements.txt
          nohup streamlit run dashboard/app.py &


Real-time Updates:

File system watcher detects report changes

Dashboard parses new test data using BeautifulSoup

Plotly charts update without page refresh

Scaling Considerations
For Large Test Suites:

Replace file watcher with message queue (Kafka/RabbitMQ)

Use Redis for real-time data caching

Distributed Execution:

java
// In BaseClass.java
@BeforeSuite
public void setupGrid() {
    if(config.get("remote")) {
        driver = new RemoteWebDriver(new URL(gridHub), capabilities);
    }
}
Dashboard Enhancements:

Add Test History Comparison

Integrate JIRA defect logging

Machine Learning for flaky test detection