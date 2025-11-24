# JobLink

JobLINK is a web application designed to connect employers with job seekers. Employers can post available job vacancies, while job seekers can search, filter, and apply for jobs. The application incorporates the use of the JSearch API from RapidAPI to provide real-time job data and is deployed on a load-balanced infrastructure for high availability.

---

## Table of Contents

- [Project Overview](#project-overview)  
- [Features](#features)  
- [External APIs Used](#external-apis-used)  
- [Installation and Local Setup](#installation-and-local-setup)  
- [Deployment on Web Servers](#deployment-on-web-servers)  
- [Load Balancer Configuration](#load-balancer-configuration)  
- [Usage](#usage)  
- [Error Handling](#error-handling)  
- [Challenges and Solutions](#challenges-and-solutions)  
- [Credits](#credits)  
- [Demo Video](#demo-video)  
- [Submission Notes](#submission-notes)

---

## Project Overview

JobLINK addresses the need for a centralized, user-friendly platform for job posting and searching. Unlike entertainment-based applications, it provides real value by facilitating employment opportunities and simplifying the recruitment process.

The application includes:

- CRUD functionality for job postings  
- Search, filter, and sort features for job seekers  
- API integration for real-time data management  
- Deployment across two web servers with a load balancer for efficient traffic management

---

## Features

**For Employers:**

- Create, edit, and delete job postings  
- Add detailed job descriptions, requirements, salary, and deadlines  
- View applications submitted by job seekers  

**For Job Seekers:**

- Browse jobs by title, location, or company  
- Search, filter, and sort available jobs  
- Apply for jobs directly through the application  

**Security & Usability:**

- API keys and sensitive information are handled securely  
- Clean and intuitive user interface  
- Error messages and validation feedback for user interactions  

---

## External APIs Used

**JSearch API (RapidAPI Marketplace)**

- **Purpose:** Fetches real-time job listings, job details, and salary data for companies. Powers the job search functionality in JobLINK.  
- **Official Documentation:** [JSearch API Documentation](https://rapidapi.com)  

**Endpoints Used:**

- `GET /search` – Search jobs by keyword, location, and filters  
- `GET /job-details` – Retrieve detailed information for a specific job  
- `GET /job-salary` – Get salary information for a job  
- `GET /company-job-salary` – Retrieve salary stats for a company  

**Example Usage:**

```javascript
const options = {
  method: 'GET',
  headers: {
    'X-RapidAPI-Host': 'jsearch.p.rapidapi.com',
    'X-RapidAPI-Key': process.env.JSEARCH_API_KEY
  }
};

fetch('https://jsearch.p.rapidapi.com/search?query=developer%20jobs%20in%20chicago&page=1&num_pages=1&country=us&date_posted=all', options)
  .then(response => response.json())
  .then(data => {
      console.log(data);
      // Display jobs dynamically
  })
  .catch(err => console.error(err));
Security Notes:

API keys are stored in a .env file and excluded from GitHub via .gitignore.

Error handling is implemented for API downtime and invalid responses.

Installation and Local Setup
Prerequisites:

Node.js / Python / PHP environment (depending on backend)

NPM / pip for dependencies

Web browser

Steps:

Clone the repository:

```
git clone <your-repo-url>
cd joblink
Install dependencies:

```
npm install
# or
pip install -r requirements.txt
Create a .env file for API keys:

```
JSEARCH_API_KEY=<your-api-key>
Start the application locally:

```
npm start
# or
python app.py
Open http://localhost:3000 (or your configured port) in a browser.

Deployment on Web Servers
Copy project files to both web servers (web-01 and web-02) under /var/www/joblink:

```
scp -i ~/.ssh/<key> -r ./joblink ubuntu@<web01-private-ip>:/var/www/joblink
scp -i ~/.ssh/<key> -r ./joblink ubuntu@<web02-private-ip>:/var/www/joblink
Set ownership and permissions:

```
sudo chown -R www-data:www-data /var/www/joblink
sudo chmod -R 755 /var/www/joblink
Restart Nginx on both servers:

```
sudo systemctl restart nginx
Load Balancer Configuration
HAProxy is installed on the load balancer (lb-01) to distribute traffic between web-01 and web-02.
Load balancing method: round-robin

Backend Example:

haproxy

```
backend http_back
    balance roundrobin
    server web01 <web01-private-ip>:80 check
    server web02 <web02-private-ip>:80 check
Frontend Example:

haproxy

```
frontend http_front
    bind *:80
    bind *:443 ssl crt /etc/haproxy/certs/<certificate>.pem
    redirect scheme https code 301 if !{ ssl_fc }
    default_backend http_back
Test via the load balancer public IP; requests are automatically distributed between servers.

## Usage
**Employer:** Login → Post jobs → Manage listings → View applications
**Job Seeker:** Browse/search jobs → Filter/sort → Apply → Confirmation

**API Integration:** Job categories and data retrieved in real time from JSearch.

**Error Handling**
Handles API failures gracefully with messages to the user.

Input validation prevents invalid submissions.

Logs errors during deployment or API downtime.

## Challenges and Solutions
**Challenge	Solution**
Nginx conflicts on LB server	Disabled Nginx; HAProxy manages port 80
Using public IPs in HAProxy backend	Replaced with private IPs of web servers
Missing editors on Ubuntu	Installed nano and vim

**Credits**
**JSearch API:** RapidAPI Marketplace

**Libraries:** Node.js, Express, Nginx, HAProxy

**Resources:** ALX Web Infrastructure and API module guides

**Demo Video**
**Duration:** 2 minutes
Shows searching, filtering, and applying for jobs

Video URL: [https://youtu.be/kSePS6QEG5Y?si=EVX5NzLt4fTO8NdC]

## Submission Notes ##
**Repository URL:** [https://github.com/andrewthonriem/APIs_summative-Andrew.git]
**App Link:**[http://andrewonline.tech/]
