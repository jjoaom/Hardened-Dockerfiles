# Docker – Hardened Dockerfile Comparison



![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)



## Overview



Containerization improves isolation compared to running software directly on the host, but container images can still contain a significant number of vulnerabilities depending on how they are built.



This repository compares:



- Dockerfile.Vulnerable

- Dockerfile.Hardened



Both images run OWASP Juice Shop to ensure functional equivalence, while differing only in hardening approach.



The comparison is performed using Trivy v0.69.0, focusing on:



- Total vulnerabilities  

- Fixable vulnerabilities  

- Severity breakdown (CRITICAL, HIGH, MEDIUM, LOW, UNKNOWN)



The goal is to demonstrate the security impact of Dockerfile hardening practices.



---



## Tooling



Vulnerability scanning is performed with Trivy v0.69.0.



For consistency:

- Same Trivy version

- Same scan date

- Same flags for both images



---



## How to Run



### 1. Build both images



``docker build -f Dockerfile.Vulnerable -t juice-vulnerable .``

``docker build -f Dockerfile.Hardened -t juice-hardened .``



### 2. Run Trivy using Docker



``docker run --rm -v /var/run/docker.sock:/var/run/docker.sock 

&nbsp; aquasec/trivy:0.69.0 image 

&nbsp; --format json 

&nbsp; -o vulnerable.json 

&nbsp; juice-vulnerable``



``docker run --rm -v /var/run/docker.sock:/var/run/docker.sock 

&nbsp; aquasec/trivy:0.69.0 image 

&nbsp; --format json 

&nbsp; -o hardened.json 

&nbsp; juice-hardened``



Optional flags:



``--ignore-unfixed``   → exclude vulnerabilities without available fixes  

``--severity CRITICAL,HIGH ``  → filter by severity  



Example (fixable only):



``docker run --rm -v /var/run/docker.sock:/var/run/docker.sock 

&nbsp; aquasec/trivy:0.69.0 image 

&nbsp; --ignore-unfixed 

&nbsp; juice-vulnerable``



---



# Results



## Vulnerable Image



| Total | Fixable | Critical | High | Medium | Low | Unknown |

|--------|----------|----------|--------|---------|------|----------|

| 3301 | 236 | 134 | 610 | 1426 | 1129 | 2 |



## Hardened Image



| Total | Fixable | Critical | High | Medium | Low | Unknown |

|--------|----------|----------|--------|---------|------|----------|

| 119 | 113 | 11 | 58 | 45 | 5 | 0 |



---



## Observations



- The hardened image reduces total vulnerabilities by approximately 96%.

- Critical vulnerabilities are reduced from 134 to 11.

- The reduction is primarily driven by:

&nbsp; - Minimizing base image footprint

&nbsp; - Reducing installed packages

&nbsp; - Removing unnecessary layers

&nbsp; - Using updated package sources

&nbsp; - Removing Root user



---



## Notes on Interpretation



- "Fixable" refers to vulnerabilities with a FixedVersion available at scan time.

- Results depend on:

&nbsp; - Base image

&nbsp; - Scan date (CVE database updates daily)

&nbsp; - Trivy version

&nbsp; - Scan flags used



Reproducibility requires maintaining these variables constant.



---



## Conclusion



Hardening a Dockerfile significantly reduces the attack surface and the number of exploitable vulnerabilities, even when running the same application.



This repository provides a practical demonstration of that impact.

---



## Acknowledgment



Special thanks to Dbsystel for the Trivy Vulnerability Explorer:



https://github.com/dbsystel/trivy-vulnerability-explorer



The tool was helpful for visualizing and exploring Trivy JSON reports in a structured way.





