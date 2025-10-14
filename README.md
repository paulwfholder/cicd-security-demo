# CI/CD Security Scanning Pipeline Demo

**Author:** Paul Holder, CC  
**Purpose:** Demonstrating automated security integration in modern DevOps workflows  
**Technologies:** GitHub Actions, Python SAST tools, Dependency scanning

---

## 🎯 Project Overview

This repository demonstrates practical implementation of **Security as Code** by integrating automated security scanning directly into the CI/CD pipeline. Every code push triggers comprehensive security analysis, catching vulnerabilities before they reach production.

### Why This Matters

Modern application security requires **shifting left**—finding security issues during development rather than after deployment. This pipeline automates three critical security checks:

1. **Static Application Security Testing (SAST)** - Identifies code-level vulnerabilities
2. **Dependency Vulnerability Scanning** - Detects known CVEs in third-party libraries  
3. **Secret Detection** - Prevents credential leaks and API key exposure

---

## 🔧 Technical Implementation

### Pipeline Architecture

```
Code Push → GitHub Actions Trigger → Parallel Security Scans → Report Generation → Artifact Storage
```

### Security Tools Integrated

| Tool | Purpose | Industry Usage |
|------|---------|----------------|
| **Bandit** | Python SAST scanner detecting security issues in code | Used by Netflix, Uber, Lyft |
| **Safety** | Checks Python dependencies against vulnerability databases | 50K+ GitHub projects |
| **pip-audit** | PyPA-recommended tool for identifying insecure packages | Official Python ecosystem tool |
| **TruffleHog** | High-entropy secret detection preventing credential leaks | 10K+ stars, used by enterprises |
| **Dependency Review** | GitHub native tool analyzing dependency changes | Built-in GitHub security feature |

---

## 📋 What This Pipeline Does

### On Every Push/Pull Request:

1. **Code Quality Scan**
   - Runs Bandit against entire codebase
   - Identifies SQL injection risks, hardcoded secrets, insecure functions
   - Generates JSON report for parsing and tracking

2. **Dependency Vulnerability Check**  
   - Scans `requirements.txt` or `pyproject.toml` for known CVEs
   - Cross-references against Safety DB and OSV database
   - Reports severity levels (CRITICAL, HIGH, MEDIUM, LOW)

3. **Secret Scanning**
   - Uses entropy analysis to detect high-randomness strings (API keys, tokens)
   - Scans commit history for accidentally committed credentials
   - Prevents secrets from reaching repository

4. **Automated Reporting**
   - Uploads all scan results as workflow artifacts
   - Generates summary in GitHub Actions UI
   - Provides actionable remediation guidance

### Daily Scheduled Scan:

- Runs at 2 AM UTC even without code changes
- Catches **zero-day vulnerabilities** in dependencies
- Ensures continuous monitoring of security posture

---

## 🚀 Setup Instructions

### Prerequisites

- GitHub repository (public or private)
- Python project with dependencies listed in `requirements.txt`

### Installation

1. **Create workflow directory:**
   ```bash
   mkdir -p .github/workflows
   ```

2. **Add the workflow file:**
   - Copy `security-pipeline.yml` to `.github/workflows/`
   - Commit and push to trigger first scan

3. **Configure repository permissions:**
   - Go to: Settings → Actions → General
   - Enable: "Read and write permissions" for GITHUB_TOKEN

4. **View results:**
   - Navigate to: Actions tab → Latest workflow run
   - Download artifacts for detailed JSON reports

---

## 📊 Understanding the Results

### Bandit Output Example:

```
Issue: [B105:hardcoded_password_string] Possible hardcoded password
Severity: Medium   Confidence: Medium
Location: app.py:45
Code: password = "admin123"
```

**Remediation:** Use environment variables or secret management service

### Safety Output Example:

```
╒══════════════════════════════════════════════════════════════════════════════╕
│                                                                              │
│                               /$$$$$$            /$$                         │
│                              /$$__  $$          | $$                         │
│           /$$$$$$$  /$$$$$$ | $$  \__//$$$$$$  /$$$$$$   /$$   /$$          │
│          /$$_____/ |____  $$| $$$$   /$$__  $$|_  $$_/  | $$  | $$          │
│         |  $$$$$$   /$$$$$$$| $$_/  | $$$$$$$$  | $$    | $$  | $$          │
│          \____  $$ /$$__  $$| $$    | $$_____/  | $$ /$$| $$  | $$          │
│          /$$$$$$$/|  $$$$$$$| $$    |  $$$$$$$  |  $$$$/|  $$$$$$$          │
│         |_______/  \_______/|__/     \_______/   \___/   \____  $$          │
│                                                            /$$  | $$          │
│                                                           |  $$$$$$/          │
│  by pyup.io                                                \______/           │
│                                                                              │
╘══════════════════════════════════════════════════════════════════════════════╛

 REPORT 

  Safety is using PyUp's free open-source vulnerability database.

  Timestamp: 2025-10-14 08:00:00
  
  -> requests version 2.25.0 has known security vulnerability:
     CVE-2023-32681 - Unintended leak of Proxy-Authorization header
     CVSS: 6.1 (MEDIUM)
     Affected: <2.31.0
     Fix: Upgrade to requests>=2.31.0
```

---

## 🛡️ Security Best Practices Demonstrated

### 1. **Defense in Depth**
Multiple scanning tools provide overlapping coverage—if one tool misses a vulnerability, another may catch it.

### 2. **Fail-Safe Design**  
Pipeline uses `continue-on-error: true` to ensure scans complete even if issues are found, providing full visibility rather than blocking deployments prematurely.

### 3. **Continuous Monitoring**
Daily scheduled scans catch newly disclosed vulnerabilities in dependencies between code changes.

### 4. **Artifact Preservation**
JSON reports stored as artifacts enable:
- Historical tracking of security posture
- Integration with SIEM/SOAR platforms
- Compliance audit trails

### 5. **Developer-Friendly Output**
Plain text summaries in workflow UI make results accessible to non-security team members.

---

## 🎓 Learning Outcomes

This project demonstrates proficiency in:

- ✅ **DevSecOps Principles** - Integrating security into development workflows
- ✅ **CI/CD Pipeline Design** - GitHub Actions YAML configuration  
- ✅ **SAST Tool Implementation** - Bandit, Safety, pip-audit practical usage
- ✅ **Vulnerability Management** - CVE identification and tracking
- ✅ **Secret Management** - Preventing credential exposure
- ✅ **Automation Architecture** - Event-driven and scheduled scanning
- ✅ **Security Reporting** - Artifact generation and result interpretation

---

## 🔄 Integration with Larger Security Programs

This pipeline serves as a **foundational layer** for comprehensive Application Security:

### Next Steps for Production Use:

1. **DAST Integration**  
   - Add OWASP ZAP or Burp Suite for runtime testing
   - Scan deployed applications for vulnerabilities missed by SAST

2. **Container Security**
   - Integrate Trivy or Grype for Docker image scanning
   - Check for vulnerabilities in base images and OS packages

3. **Infrastructure as Code Security**
   - Add Checkov or tfsec for Terraform/CloudFormation scanning
   - Prevent misconfigured cloud resources

4. **Security Gates**
   - Configure severity thresholds that block deployments
   - Require security team approval for high-severity findings

5. **SIEM Integration**
   - Forward scan results to Splunk, Elastic, or AWS Security Hub
   - Correlate with runtime security events

---

## 📈 Metrics & Continuous Improvement

Track these KPIs to demonstrate security program maturity:

- **Mean Time to Remediation (MTTR)** - Days from vulnerability detection to fix
- **Vulnerability Escape Rate** - Issues found in production vs pre-production  
- **Scan Coverage** - Percentage of repositories with automated scanning
- **False Positive Rate** - Invalid findings requiring tool tuning

---

## 🤝 Contributing

This is a demonstration project, but suggestions for improvements are welcome:

- Additional security tools to integrate
- Enhanced reporting formats
- Multi-language support (Java, JavaScript, Go)

---

## 📞 Contact

**Paul Holder, CC**  
Application Security Engineer  
[LinkedIn](https://linkedin.com/in/paulwfholder) | [GitHub](https://github.com/paulwfholder)

---

## 📚 Additional Resources

- [OWASP DevSecOps Guideline](https://owasp.org/www-project-devsecops-guideline/)
- [GitHub Actions Security Hardening](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)
- [NIST Secure Software Development Framework](https://csrc.nist.gov/projects/ssdf)
- [CIS Software Supply Chain Security Guide](https://www.cisecurity.org/insights/white-papers/cis-software-supply-chain-security-guide)

---

**Last Updated:** October 2025  
**License:** MIT  
**Status:** Active Development / Portfolio Demonstration
